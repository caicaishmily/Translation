这是我们全栈React+GraphQL系列教程的第四部分，每一部分都是独立的且都会介绍一个新的关键的知识点。所以你可以单独的阅读任何一部分或者一整个系列教程，这完全取决于你。

以下使我们到目前为止所涉及的部分：

*   [Part 1: 前端](https://dev-blog.apollodata.com/full-stack-react-graphql-tutorial-582ac8d24e3b)
*   [Part 2: 服务器端](https://dev-blog.apollodata.com/react-graphql-tutorial-part-2-server-99d0528c7928)
*   [Part 3: 基本的 GraphQL Mutations](https://dev-blog.apollodata.com/react-graphql-tutorial-mutations-764d7ec23c15)
*   Part 4: 良好的UI (你在此)
*   [Part 5: 输入类型和自定义解析器](https://medium.com/p/tutorial-graphql-input-types-and-client-caching-f11fa0421cfd)
*   [Part 6: 服务器订阅](https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-server-side-e51c32dc2951)
*   [Part 7: 在客户端使用GraphQL订阅](https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-client-side-40e185e4be76)
*   [Part 8: 分页](https://dev-blog.apollodata.com/tutorial-pagination-d1c3b3ee2823)


在这一部分，我们将继续深入学习GraphQL mutation在客户端是如何工作的，我们从[part 3](https://dev-blog.apollodata.com/react-graphql-tutorial-mutations-764d7ec23c15) 拿到一个列表视图和mutation。模拟500毫秒的网络延迟，然后从用户的角度使用存储更新用户界面使延迟几乎消失。

到本教程结束时，您将知道良好的用户界面是如何使用存储更新来处理网络延迟。

> 提示: 如果某些事情不能像预期的那样工作，您可以随时通过运行 _git checkout t4-end_ 解决该问题


开始之前，让我们克隆教程git repo并安装依赖关系：

```
git clone https://github.com/apollographql/graphql-tutorial.gitcd graphql-tutorialgit checkout t4-startcd server && npm install && cd ../client && npm install && cd ..
```

为确保一切按预期工作，让我们分别在不同的终端中启动服务器和客户端：

```
cd servernpm start
```

在另一个终端：

```
cd clientnpm start
```

如果它正常工作，您现在应该在浏览器中看到第3部分末尾的ChannelsView：

![](https://p0.ssl.qhimg.com/t01134f4c61f4ef1016.gif)

#### Mutations和延迟

正如您在上图中看到的那样，您添加的任何新channel都会立即显示在列表中。 这很好，因为反应性有助于实现更好的用户体验。

然而，如果你在第3部分中跟着教程学习的话，你可能还记得，这个更新实际上是**两次往返** 现在！ 一个用于mutation请求，另一个用于在mutation完成后重新获取列表。 第二次往返当前是必需的，因为客户无法知道您刚刚添加的新项目应该在列表中。 GraphQL根本没有任何方式编码该信息，因为它将后端视为黑盒子：在数据输出中查询。 在我们解决这个问题之前，让我们看看为什么这是一个问题。

为了了解如果网络速度较慢UX会是什么样子，我们将通过添加中间件来模拟Apollo网络接口中500毫秒的网络延迟。

为此，请在声明networkInterface常量之后立即将以下行添加到client/src/App.js中。

```
// const networkInterface = ...networkInterface.use([{  applyMiddleware(req, next) {    setTimeout(next, 500);
},}]);
```

---

现在我们已经为每个请求添加了500毫秒的延迟，添加一个新channel感觉有点不同：

![](https://p0.ssl.qhimg.com/t012fc7466e2c3a53fa.gif)

从“tennis”这个词变暗到消失的时间，您看到的延迟是mutation完成其服务器往返所花费的时间。 从输入框中单词“tennis”消失直到它出现的时间的延迟是列表重新读取查询的延迟。

尽管重新初始化让我们能够轻松快捷地建立此功能，但我们需要一个更好的解决方案，让用户体验到延迟的制作应用程序。 目前，这些用户的体验并不是很好：首先，他们在等待，没有任何反应; 然后，他们看到他们的输入完全消失，然后它终于出现在他们预期的地方。 

特别是在移动网络上，几百毫秒的延迟并不罕见，我们需要一种方法来处理它。 我们可以做的第一件事是避免第二次往返重新获取列表。

#### mutation完成后存储更新

对我们来说幸运的是，mutation已经返回了我们需要将新项目加入到列表中的所有信息。 我们只需告诉Apollo Client 如何为我们做这些！ 

对于基于客户端操作需要更新存储的mutation和其他情况，Apollo提供了一组功能强大的工具来执行必要的存储更新：readQuery，writeQuery，readFragment和writeFragment。 你可以阅读所有关于它们[点这里](http://dev.apollodata.com/core/read-and-write.html).

因为在mutation后更新存储是一种常见的用例，所以Apollo Client通过mutate中暴露的update属性可以非常容易地使用这些函数。 

要使用它，我们用下面的调用来替换AddChannel.js中的refetchQueries选项来更新：

```
const handleKeyUp = (evt) => {    if (evt.keyCode === 13) {      evt.persist();
mutate({         variables: { name: evt.target.value },        update: (store, { data: { addChannel } }) => {            // Read the data from the cache for this query.            const data = store.readQuery({query: channelsListQuery });
// Add our channel from the mutation to the end.            data.channels.push(addChannel);
// Write the data back to the cache.            store.writeQuery({ query: channelsListQuery, data });
},      })      .then( res => {        evt.target.value = '';
});
}  };
```

现在，只要mutation完成，我们就从存储中读取channelsListQuery的当前结果，将新channel追加到它里面，并告诉Apollo Client将其写回存储。 这就是我们必须做的！ 我们的ChannelsListWithData组件会自动更新新数据。

如果您运行另一个mutation，您会注意到在输入消失并重新出现在列表末尾之间不再有任何延迟。 它几乎是瞬间的，这太棒了！

![](https://p0.ssl.qhimg.com/t015df62ef46b8ed7d3.gif)

初始mutation的潜伏期仍然存在，但幸运的是，还有一种方法可以解决这个问题！

#### 良好的 UI

Apollo Client可以利用一般称为乐观用户界面的技巧来模拟零延迟的服务器响应。 乐观的用户界面非常难以手动设置，但是由于Apollo同时管理存储和网络请求，使用乐观用户界面非常简单。

您只需模拟零延迟服务器响应即可将optimisticResponse属性添加到mutate调用中。 optimisticResponse应该是您期望从服务器获得的响应。 我们已经知道我们期望的channel名称。 ID并不重要，所以我们只是采取一个随机值来确保没有冲突。 最后，我们还必须指定__typename以确保Apollo Client知道它是什么类型的对象：

```
mutate({   variables: { name: evt.target.value },  optimisticResponse: {     addChannel: {       name: evt.target.value,       id: Math.round(Math.random() * -1000000),       __typename: 'Channel',     },   },   update: ...})
```

如果将optimisticResponse添加到您的代码中，您会注意到在返回时间和项目出现在列表底部之间没有更多延迟。

事实上，optimisticResponse速度非常快，即使在输入字段中删除项目之前，该项目也会出现在列表中。这是因为我们有一段代码在成功返回mutation时清除输入字段。现在我们拥有乐观的用户界面，这已经不再适合了。相反，让我们给用户一些关于哪些列表项已被服务器确认，哪些列表项没有被确认的视觉反馈。

为了让用户知道channel尚未被服务器确认，我们需要以某种方式将该信息添加到该项目。与其修改服务器模式以跟踪某些客户端状态，我们将在此处使用一些小技巧来跟踪哪些项目是乐观的。

我们知道所有服务器生成的ID都是正整数，但乐观的“生成”ID都是负数 - 多么幸运！ ;-)

```
id: Math.round(Math.random() * -1000000),
```

> Note: 有更清晰的方法来跟踪您的用户界面中的乐观项目，但他们需要更多的设置，所以我们将在未来的教程中讲述这些。

为了使乐观的channel在视觉上截然不同，我们在ChannelsListWithData.js中给他们一个额外的CSS类：

```
return (                { channels.map( ch =>         ({ch.name})      )}      );
```

最后，我们只需要定义App.css中的那个类。 出于我们的目的，我们只是让文本变得更加灰色：

```
div.optimistic {  color: rgba(255, 255, 255, 0.5);}
```

就是这样！ 通过这些更改，addChannel mutation现在应该看起来好多了！

![](https://p0.ssl.qhimg.com/t01b62dab10c3e19d94.gif)

---

#### 结论

因此，本教程系列中的第4部分已经完成了！ 您已经学会了如何使用存储更新和乐观的用户界面来隐藏用户的网络延迟。 结合本教程系列的第1,2和3部分，您现在已经熟悉使用Apollo编写具有卓越用户体验的简单React + GraphQL应用程序的所有基础知识。

在本系列的[下一节](https://medium.com/p/tutorial-graphql-input-types-and-client-caching-f11fa0421cfd) 我们将向应用添加第二个视图，并学习如何使用缓存数据和预取以更快地显示预览和加载页面。 如果你不想错过未来的部分，一定要订阅！
> 如果您喜欢本教程并希望继续学习Apollo和GraphQL，请务必点击下面的“关注”按钮，然后在Twitter上关注我们[@apollographql](https://twitter.com/apollographql) 和 [@helferjs](https://twitter.com/helferjs).
