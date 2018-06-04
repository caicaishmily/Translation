这是我们全栈GraphQL + React教程的第7部分，它指导您创建消息传递应用程序。 这个系列的每个部分都是独立的，并着重于一些新的主题，所以您可以直接跳到您感兴趣的部分或整个系列。 以下是我们迄今为止所涉及的内容：

*   [Part 1: 创建一个简单的客户端](https://dev-blog.apollodata.com/full-stack-react-graphql-tutorial-582ac8d24e3b)
*   [Part 2: 创建一个简单的服务器](https://dev-blog.apollodata.com/react-graphql-tutorial-part-2-server-99d0528c7928)
*   [Part 3: 编写Mutation并保持客户端同步](https://dev-blog.apollodata.com/react-graphql-tutorial-mutations-764d7ec23c15)
*   [Part 4: 友好的用户界面和客户端存储更新](https://dev-blog.apollodata.com/tutorial-graphql-mutations-optimistic-ui-and-store-updates-f7b6b66bf0e2)
*   [Part 5: 输入类型和自定义解析器](https://dev-blog.apollodata.com/tutorial-graphql-input-types-and-client-caching-f11fa0421cfd)
*   [Part 6: 服务器上的GraphQL订阅](https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-server-side-e51c32dc2951)
*   Part 7: 客户端订阅 (这一节!)
*   [Part 8: 分页](https://dev-blog.apollodata.com/tutorial-pagination-d1c3b3ee2823)

---

在第6部分中，我们为message channels实现了GraphQL订阅服务器端的部分。 客户端可以使用这些订阅在特定事件发生时收到通知 - 在这种情况下，可以在指定channel中创建消息。 在本教程中，我们将向客户端添加GraphQL订阅，以便客户端实例可以看到信道中消息的实时更新。

---

让我们开始克隆Git仓库并安装依赖关系：

```
git clone https://github.com/apollographql/graphql-tutorial.gitcd graphql-tutorialgit checkout t7-startcd server && npm installcd ../client && npm install
```

首先，通过启动服务器和客户端来确保一切运行正常。 

在一个终端会话中，我们启动将在端口4000上运行的服务器：

```
cd servernpm start
```

在另一个会话中，我们启动将在端口3000上运行的客户端：

```
cd clientnpm start
```

![](https://p0.ssl.qhimg.com/t01339d810107e8272f.gif)

当你在你的浏览器中浏览 [http://localhost:3000](http://localhost:3000) 的时候, 你应该进入我们的消息应用程序的主页，其中有一个用户创建的频道列表。 点击其中一个频道，你会看到我们创建的详细视图 [Part 5](https://dev-blog.apollodata.com/tutorial-graphql-input-types-and-client-caching-f11fa0421cfd), 您可以在该频道中添加新消息。 您会注意到，如果您在多个窗口中打开相同频道，则在一个窗口中添加的消息不会显示在另一个窗口中。 到本教程结束时，客户端同步将允许多个用户查看彼此的更改！

#### GraphQL订阅传输

向客户端添加订阅的第一步是设置客户端和服务器将通过的WebSocket连接。 形成和维护WebSocket连接将是client / src / App.js中定义的Apollo网络接口的工作。 为了将WebSocket支持添加到我们现有的接口中，我们将构建一个GraphQL订阅客户端并将其与我们现有的网络接口合并，以创建一个新的接口，该接口通过HTTP执行正常的GraphQL查询并通过WebSockets执行订阅查询。

首先，我们在client / src / App.js的顶部添加必要的导入

```
import { SubscriptionClient, addGraphQLSubscriptions } from 'subscriptions-transport-ws';
```

接下来，我们构建基于WebSocket的订阅客户端并将其与我们现有的网络接口合并

```
const networkInterface = createNetworkInterface({ uri:'http://localhost:4000/graphql' });
```

```
networkInterface.use([{  applyMiddleware(req, next) {    setTimeout(next, 500);
},}]);
```

```
const wsClient = new SubscriptionClient(ws://localhost:4000/subscriptions, {  reconnect: true,});
```

```
const networkInterfaceWithSubscriptions = addGraphQLSubscriptions(  networkInterface,  wsClient,);
```

现在我们需要做的只是在我们的应用程序中启用订阅，就是使用networkInterfaceWithSubscriptions作为Apollo客户端的网络接口

```
const client = new ApolloClient({  networkInterface: networkInterfaceWithSubscriptions,  ...});
```

如果加载客户端并查看开发人员工具的“网络”选项卡（右键单击和“检查元素”），您应该看到客户端已建立与服务器的WebSocket连接。

![](https://p0.ssl.qhimg.com/t0122490deaca06e620.png)

#### 监听消息

现在我们可以在客户端中使用GraphQL订阅，下一步就是使用订阅来检测消息的创建。 我们的目标是使用订阅来更新我们的React视图，以便在添加频道时查看频道中的新消息。

在我们开始之前，我们必须将客户端/ src / components / ChannelDetails.js组件重构为完整的ES6类组件，而不仅仅是一个函数，以便我们可以使用React生命周期事件来设置订阅。

首先，我们更新我们的import语句以包含Component类。

```
import React, { Component } from 'react';
```

然后，我们将我们的功能组件重构为ES6类

```
class ChannelDetails extends Component {  render() {    const { data: {loading, error, channel }, match } = this.props;
```

```
    if (loading) {      return ;
}    if (error) {      return {error.message};
}    if(channel === null){      return     }
```

```
    return (                        {channel.name}                      );
}}
```

现在我们的组件已准备好处理订阅，我们可以写出订阅查询：

```
const messagesSubscription = gql  subscription messageAdded($channelId: ID!) {    messageAdded(channelId: $channelId) {      id      text    }  }
```

为了提出订阅请求，我们将使用Apollo Client的subscribeToMore功能，当我们收到新数据时，我们可以让我们更新商店。 首先，我们在组件中定义一个componentWillMount，这是我们开始订阅的地方。

```
class ChannelDetails extends Component {  componentWillMount() {  }
```

```
  render() {    ...  }}
```

在这个React生命周期函数中，我们设置了我们的订阅来收听新消息，并在他们出现时将它们添加到我们的本地存储。 因为updateQuery函数应该根据prev（先前的存储状态）生成一个新的存储状态实例，所以我们使用Object.assign方法创建存储的副本并添加新消息进行修改。 

另外，因为我们手动管理我们的消息存储，所以可能会有重复的消息。 一条消息可以在执行mutation时添加一次，并在收到订阅通知时再次添加。 为了防止重复，我们添加了一个额外的检查来验证我们是否已将消息添加到具有先前mutation的存储中。

```
componentWillMount() {  this.props.data.subscribeToMore({    document: messagesSubscription,    variables: {      channelId: this.props.match.params.channelId,    },    updateQuery: (prev, {subscriptionData}) => {      if (!subscriptionData.data) {        return prev;
}
```

```
      const newMessage = subscriptionData.data.messageAdded;
// don't double add the message      if (!prev.channel.messages.find((msg) => msg.id === newMessage.id)) {        return Object.assign({}, prev, {          channel: Object.assign({}, prev.channel, {            messages: [...prev.channel.messages, newMessage],          })        });
} else {        return prev;
}    }  });}
```

我们现在快完成了！ 我们所要做的就是在AddMessage组件中执行相同的重复数据删除检查，因为当我们创建新的消息时，我们可能会在查询返回数据之前通过WebSocket通知创建。 在client/src/components/AddMessage.js中，替换data.channel.messages.push（addMessage）; 用相同的语句包装在检查重复的条件下

```
if (!data.channel.messages.find((msg) => msg.id === addMessage.id)){  // Add our Message from the mutation to the end.  data.channel.messages.push(addMessage);}
```

现在我们准备测试我们的基于订阅的实时更新消息视图！ 打开客户端的两个窗口，并在两个窗口中选择相同的通道。 当您在一个客户端添加消息时，您应该看到在另一个客户端显示相同的消息！

![](https://p0.ssl.qhimg.com/t0101c68c44cab4fc50.gif)

#### 结论

恭喜！ 现在，您已通过Apollo将GraphQL订阅的服务器端实现连接到客户端，以便用户可以看到来自其他客户端的消息添加的实时更新。 在下一个[教程]（https://dev-blog.apollodata.com/tutorial-pagination-d1c3b3ee2823）
中有更多更改（如分页），并授权您的应用程序将准备好实际使用！

> 如果您喜欢本教程并希望继续学习Apollo和GraphQL，请确保点击下面的“关注”按钮，并在Twitter上关注我们[@apollographql]（https://twitter.com/apollographql） 和作者 在[@ShadajL]（http://twitter.com/shadajl）。

感谢我的导师, [Jonas Helfer](https://medium.com/@helfer), 多谢他的支持！

![](https://p0.ssl.qhimg.com/t014fc6606574d991f5.jpg)
