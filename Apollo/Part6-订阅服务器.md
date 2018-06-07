# Tutorial: GraphQL 订阅服务器消息

## Full-stack GraphQL + React Tutorial — Part 6
这是全栈Graph+React手册里面的第六部分，该系列将指导你创建一个消息应用。并且每一部分都是独立的，并且聚焦于一些新的话题，所以你可以跳跃到吸引你的某一部分开始阅读，或者你也可以从头到尾阅读，下面是该手册的其他部分：
*   [Part 1: 创建一个简单的客户端](https://dev-blog.apollodata.com/full-stack-react-graphql-tutorial-582ac8d24e3b)
*   [Part 2: 创建一个简单的服务器](https://dev-blog.apollodata.com/react-graphql-tutorial-part-2-server-99d0528c7928)
*   [Part 3: 编写Mutation并保持客户端同步](https://dev-blog.apollodata.com/react-graphql-tutorial-mutations-764d7ec23c15)
*   [Part 4: 良好的用户界面和客户端存储更新](https://dev-blog.apollodata.com/tutorial-graphql-mutations-optimistic-ui-and-store-updates-f7b6b66bf0e2)
*   [Part 5: 输入类型和自定义解析器](https://dev-blog.apollodata.com/tutorial-graphql-input-types-and-client-caching-f11fa0421cfd)
*   Part 6: 服务器端订阅 (当前)
*   [Part 7: 客户端上的GraphQL订阅](https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-client-side-40e185e4be76)
*   [Part 8: 分页](https://dev-blog.apollodata.com/tutorial-pagination-d1c3b3ee2823)

---
在本教程中，我们将介绍如何将GraphQL订阅添加到我们的服务器的过程。 在第5部分中，我们添加了消息的概念，并在客户端实现了channel Detail的视图，以在每个channel中显示消息。 但现在，消息不会跨客户端同步，因为服务器无法通知客户端已添加新消息。

与许多其他应用程序一样，我们的消息应用程序需要对某些功能进行实时更新，因此在本教程中，我们将构建服务器端逻辑，以使我们的客户端能够实时显示新消息，这归功于GraphQL订阅。 如果您想深入了解订阅如何工作，请查看[本博客文章]（https://dev-blog.apollodata.com/graphql-subscriptions-in-apollo-client-9a2457f015fb），介绍Apollo客户支持 订阅和[请求评论](https://github.com/facebook/graphql/blob/master/rfcs/Subscriptions.md) 来添加对GraphQL规范的订阅。

---
我们先开始克隆git仓库并且安装依赖包
```
git clone https://github.com/apollographql/graphql-tutorial.gitcd graphql-tutorialgit checkout t6-startcd server && npm installcd ../client && npm install
```

首先我们得确认客户端和服务端都正常运行

在一个命令行终端我们开启一个运行在4000端口的服务端服务

```
cd servernpm start
```
在另一个终端我们开启一个运行在3000端口的客户端

```
cd clientnpm start
```
当你在你的浏览器地址栏输入[http://localhost:3000](http://localhost:3000)，其中有一个用户创建的channel列表。 点击其中一个频道，您将看到我们在最后一部分创建的detail视图，您可以在该频道中添加新消息。

![](https://p0.ssl.qhimg.com/t019758a4f4a761f61f.gif)

我们不会在本教程中编写任何客户端代码，但我们将使用客户端UI来插入消息以测试我们的订阅实现。

#### GraphQL 订阅

要将消息添加到channel时通知客户端，我们将使用GraphQL订阅，这使得客户端可以进行查询，并在特定的服务器端事件的情况下通知新的结果。 在我们的服务器实现中，我们将使用带有WebSockets的Express服务器将更新推送到客户端。

在本教程中，我们将首先添加一个订阅，通知客户端有新消息。 接下来，我们将向我们的GraphQL模式添加一个字段并为订阅实现一个解析器。 最后，我们将使用内存中的pub-sub对象来处理传递有关添加消息的通知。 总之，消息创建和订阅通知的流程如下所示：

![](https://p0.ssl.qhimg.com/t0125463a2210df4ca8.png)

现在让我们通过添加一个订阅来监听消息添加到我们的服务器端模式！ 在server / src / schema.js中的GraphQL模式的最后，我们添加了一个新的根类型的Subscription，它与Query和Mutation处于同一级别。 该根类型包含messageAdded（channelId：ID！），该字段表示客户端可以侦听的主题，以通知添加到特定channel的消息。 总之，我们补充道：

```
// server/src/schema.jstype Subscription {  messageAdded(channelId: ID!): Message}
```

随着我们继续实现订阅，我们的模式现在为我们提供了坚实的参考。

#### 添加订阅解析器

现在我们有一个模式来定义客户端可以请求哪些订阅，下一步是允许事件发送到订阅。 为了简单起见，我们将使用graphql-subscriptions包中的内存PubSub系统实现我们的订阅模型。 我们还需要使用来自同一个包的withFilter帮助程序来分解他们用于哪个channel的事件。 在server / src / resolvers.js中，我们有必要开始添加一些导入语句

```
// server/src/resolvers.jsimport { PubSub, withFilter } from ‘graphql-subscriptions’;
```

接下来，我们将构建一个PubSub实例来处理我们应用程序的订阅主题。 在我们定义解析器之前，我们将添加此实例，在创建消息时我们需要使用该实例生成事件。

```
const pubsub = new PubSub();
```

```
export const resolvers = {
```

一旦我们设置了订阅管理器，我们需要用它发布消息！ 使用PubSub类，这与调用pubsub.publish（topic，data）一样简单。 对于我们的应用程序，我们将每条新消息发布到messageAdded主题以及一个携带channel ID的附加属性（本教程稍后将更加重要）。 有一点需要注意：主题名称不必与订阅名称匹配; 我们在这里使用了相同的名称来保持简单。

```
addMessage: (root, { message }) => {  const channel = channels.find(channel => channel.id ===message.channelId);
if(!channel)    throw new Error(“Channel does not exist”);
```

```
  const newMessage = { id: String(nextMessageId++), text: message.text };
channel.messages.push(newMessage);
```

```
  pubsub.publish(‘messageAdded’, { messageAdded: newMessage, channelId: message.channelId });
```

```
  return newMessage;}
```

接下来，让我们使用发布的事件来解析订阅查询！ 通过graphql-subscriptions包，这非常容易。 我们在解析器中设置嵌套对象，就像我们通常会那样，而不是在最后返回一个对象，我们返回一个异步迭代器，它将发送消息发送到客户端。 由于messageAdded主题包含_all_ channels的事件，因此我们还使用前面导入的withFilter函数来节省资源。 这将过滤事件以仅选择查询中指定的通道的事件。 当使用withFilter时，第一个参数是一个返回我们正在过滤的异步迭代器的函数。 第二个参数是一个条件，指定事件是否应通过给定事件数据和查询变量的过滤器。

```
Subscription: {  messageAdded: {    subscribe: withFilter(      () => pubsub.asyncIterator(‘messageAdded’),      (payload, variables) => {        return payload.channelId === variables.channelId;
}    )  }}
```

这就是解决GraphQL订阅查询所需的全部内容！

#### 用于订阅的WebSocket传输

本教程的最后一步是通过WebSockets为我们的GraphQL服务器添加订阅支持，因为我们无法通过HTTP将频繁的更新从服务器推送到客户端。 感谢subscriptions-transport-ws包，这非常简单！ 首先，让我们开始在server / server.js中添加必要的导入语句

```
import { execute, subscribe } from ‘graphql’;import { createServer } from ‘http’;import { SubscriptionServer } from ‘subscriptions-transport-ws’;
```

接下来，我们可以在我们的GraphQL服务器中打开WebSocket。 我们分两步执行此操作：首先用createServer包装Express服务器，然后使用包装的服务器设置WebSocket来侦听GraphQL订阅。

```
// Wrap the Express serverconst ws = createServer(server);
```

```
ws.listen(PORT, () => {  console.log(GraphQL Server is now running on http://localhost:${PORT});
```

```
  // Set up the WebSocket for handling GraphQL subscriptions  new SubscriptionServer({    execute,    subscribe,    schema  }, {    server: ws,    path: '/subscriptions',  });});
```

接下来，我们将Graph_i_QL配置为使用我们刚刚设置的订阅WebSocket。

```
server.use('/graphiql', graphiqlExpress({  endpointURL: '/graphql',  subscriptionsEndpoint: ws://localhost:4000/subscriptions}));
```

与此同时，我们的服务器已准备就绪！ 要试用它，我们可以在[http://localhost:4000/graphiql](http://localhost:4000/graphiql) 上打开GraphiQL并运行以下查询

```
subscription {  messageAdded(channelId: 1) {    id    text  }}
```

当您运行查询时，您应该看到类似下面的这样一条消息

```
"Your subscription data will appear here after server publication!"
```

Graph_i_QL现在正在监听新消息的创建，我们可以通过在客户端创建消息来触发该消息。 因为我们只在收听channel1，所以请务必导航到客户端的第一个channel（或直接将浏览器指向[http://localhost:3000/channel/1](http://localhost:3000/channel/1)).当你创建一条新消息时，你应该看到它立即显示在你的Graph_i_QL窗口中！

![](https://p0.ssl.qhimg.com/t011a43227d4cdb94e9.gif)

#### 总结

万岁！ 您现在已经实现了GraphQL订阅的服务器端部分，方法是向模式添加订阅类型并通过WebSockets实现订阅传输！ 通过对客户端进行一些更改（将在[下一个教程]（https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-client-side-40e185e4be76）
中进行解释）我们的客户端将能够几乎实时查看消息添加。

>如果您喜欢本教程并希望继续学习Apollo和GraphQL，请务必点击下面的“关注”按钮，然后在Twitter上关注我们[@apollographql](https://twitter.com/apollographql) 和作者 [@ShadajL](http://twitter.com/shadajl).

感谢我的导师, [Jonas Helfer](https://medium.com/@helfer), 感谢他在我写这篇文章时的所做的支持!

#### 原文链接

https://dev-blog.apollodata.com/tutorial-graphql-subscriptions-server-side-e51c32dc2951
