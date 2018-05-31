开始使用Apollo客户端最简单的方法是使用我们的入门套件Apollo Boost，该套件使用我们的推荐设置为您配置客户端。 Apollo Boost包含了我们认为对构建Apollo应用程序非常重要的软件包，比如我们在内存缓存，本地状态管理和错误处理方面。 它也足够灵活来处理认证等功能。

如果您是一位想从头开始配置Apollo客户端的高级用户，请前往我们的网站 [Apollo Boost migration guide](https://www.apollographql.com/docs/react/essentials/get-started.html/../advanced/boost-migration.html). 对于大多数用户来说，Apollo Boost应该满足您的需求，所以我们不建议您切换，除非您需要更多定制。

## 安装

首先，我们安装一些包

```bash
npm install apollo-boost react-apollo graphql-tag graphql --save

```

*   apollo-boost: 包含您设置Apollo客户端所需的所有内容
*   react-apollo: 查看React的图层集成
*   graphql-tag: 解析你的GraphQL查询是必要的
*   graphql: 也是解析您的GraphQL查询

> 如果您想自己浏览本教程，我们建议使用本地运行新的React项目 [create-react-app](https://reactjs.org/docs/add-react-to-a-new-app.html) 或者创建一个新的React沙盒 [CodeSandbox](https://codesandbox.io/). 作为参考，我们将使用[this Launchpad](https://launchpad.graphql.com/w5xlvm3vzz)作为我们的示例应用程序的GraphQL服务器，它从Coinbase API中提取汇率数据。 如果您想跳过并查看我们即将构建的应用，则可以查看它 [CodeSandbox](https://codesandbox.io/s/nn9y2wzyw4).

## 创建一个客户端

太棒了，现在你拥有了所有你需要的依赖关系，让我们来创建你的Apollo客户端。 您需要开始的唯一事情就是您的 [GraphQL server](https://launchpad.graphql.com/w5xlvm3vzz) 终端. 如果您没有直接通过uri传递，它将默认为应用程序所在的同一主机上的/ graphql端点。

在我们的index.js文件中，让我们从apollo-boost导入ApolloClient，并将GraphQL服务器的端点添加到客户端配置对象的uri属性中。

```js
import ApolloClient from "apollo-boost";

const client = new ApolloClient({
  uri: "https://w5xlvm3vzz.lp.gql.zone/graphql"
});

```

就是这样！ 现在您的客户端已准备好开始提取数据。 在我们将Apollo Client连接到React之前，我们先尝试用普通的JavaScript发送一个查询。 在同一个index.js文件中，尝试调用client.query（）。 请记住导入gql函数以将查询字符串解析为查询文档。

```js
import gql from "graphql-tag";

client
  .query({
    query: gql
      {
        rates(currency: "USD") {
          currency
        }
      }
    
  })
  .then(result => console.log(result));

```

打开你的控制台并检查结果对象。 您应该看到一个附带费率的数据属性，以及一些其他属性，如加载和networkStatus。 虽然您不需要React或其他前端框架仅仅通过Apollo客户端获取数据，但我们的视图层集成使您可以更轻松地将查询绑定到用户界面，并用数据来响应地更新组件。 让我们学习如何将Apollo Client连接到React，以便我们可以使用react-apollo开始构建查询组件。

## 将您的客户端连接到React

要将Apollo Client连接到React，您需要使用从react-apollo导出的ApolloProvider组件。 ApolloProvider与React类似 [context provider](https://github.com/reactjs/rfcs/blob/master/text/0002-new-version-of-context.md).它包装了您的React应用程序并将客户端放置在上下文中，这允许您从组件树中的任何位置访问它。

在index.js中，让我们用ApolloProvider包装我们的React应用程序。 我们建议将ApolloProvider放置在您的应用程序的某处，并放置在您需要访问GraphQL数据的任何地方。 例如，如果您使用React Router，它可能位于根路由组件之外。

```jsx
import React from "react";
import { render } from "react-dom";

import { ApolloProvider } from "react-apollo";

const App = () => (
  <ApolloProvider client={client}>
    <div>
      <h2>My first Apollo app 🚀</h2>
    </div>
  </ApolloProvider>
);

render(<App />, document.getElementById("root"));

```

## 请求数据

一旦你的ApolloProvider被连接起来，你就可以开始使用查询组件来请求数据了！ Query是一个从react-apollo导出的React组件，它使用[render prop pattern]（https://reactjs.org/docs/render-props.html） 与您的UI共享GraphQL数据。

首先，将包裹在gql函数中的GraphQL查询传递给Query组件上的查询prop。 然后，您将为Query组件的children prop提供一个函数，以确定要呈现的内容，哪个Query将使用包含加载，错误和数据属性的对象进行调用。 Apollo客户端为您追踪错误和加载状态，这将在加载和错误属性中反映出来。 一旦查询结果返回，它将被附加到数据属性。

让我们在index.js中创建一个ExchangeRates组件，以查看正在使用的查询组件！

```jsx
import { Query } from "react-apollo";
import gql from "graphql-tag";

const ExchangeRates = () => (
  <Query
    query={gql
      {
        rates(currency: "USD") {
          currency
          rate
        }
      }
    }
  >
    {({ loading, error, data }) => {
      if (loading) return <p>Loading...</p>;
      if (error) return <p>Error :(</p>;

      return data.rates.map(({ currency, rate }) => (
        <div>
          <p>{${currency}: ${rate}}</p>
        </div>
      ));
    }}
  </Query>
);

```

恭喜，您刚刚创建了您的第一个查询组件！ 🎉如果您在上一个示例中的应用程序组件中呈现ExchangeRates组件，您将首先看到一个加载指示符，然后在页面准备就绪时看到该页面上的数据。 Apollo客户端在从服务器返回时会自动缓存这些数据，因此如果您运行相同的查询两次，则不会看到加载指示符。

如果您想要使用我们刚刚构建的应用程序，可以在[CodeSandbox]（https://codesandbox.io/s/nn9y2wzyw4） 上查看它。 不要停在这里！ 尝试构建更多的查询组件并尝试使用刚刚学到的概念。

如果您想进一步探索，下面是以不同前端库为特色的示例应用的更多版本：

*   React Native Web: [https://codesandbox.io/s/xk7zw3n4](https://codesandbox.io/s/xk7zw3n4)
*   Vue: [https://codesandbox.io/s/3vm8vq6kwq](https://codesandbox.io/s/3vm8vq6kwq)
*   Angular (Ionic): [https://github.com/aaronksaunders/ionicLaunchpadApp](https://github.com/aaronksaunders/ionicLaunchpadApp)

## Apollo Boost

在我们的示例应用程序中，我们使用Apollo Boost来快速设置Apollo客户端。 尽管您的GraphQL服务器端点是您需要开始的唯一配置选项，但我们还包含一些其他选项，以便您可以快速实现本地状态管理，身份验证和错误处理等功能。

### 它包含什么

Apollo Boost包含一些我们认为对Apollo客户端开发非常重要的软件包。 以下是包含的内容：

*   apollo-client: 所有魔法都发生的地方
*   apollo-cache-inmemory: 我们推荐的缓存
*   apollo-link-http: 用于远程数据获取的Apollo链接
*   apollo-link-error: 用于错误处理的Apollo链接
*   apollo-link-state: 本地状态管理的Apollo链接

关于Apollo Boost的令人敬畏的事情是，你不必自己设置任何一个！ 只需指定几个选项，如果您想使用这些功能，我们会照顾其余的。

### 配置选项

以下是您可以传递给从apollo-boost导出的ApolloClient的选项。 所有这些都是可选的。

uri: string

代表您的GraphQL服务器端点的字符串。 默认为/ graphql

fetchOptions: Object

您想要传递的任何选项（凭证，标题等）。 这些选项是静态的，因此它们不会在每个请求中更改。

request: (operation: Operation) => Promise

每个请求都会调用该函数。 它需要GraphQL操作并可以返回一个承诺。 要动态设置fetchOptions，可以使用operation.setContext（{headers}）将它们添加到操作的上下文中。 这里设置的任何选项将优先于fetchOptions。 用于身份验证。

onError: (errorObj: { graphQLErrors: GraphQLError\[\], networkError: Error, response?: ExecutionResult, operation: Operation }) => void

我们包含一个默认的错误处理程序，将错误注销到控制台。 如果您想要以不同的方式处理您的错误，请指定此功能。

clientState: { resolvers?: Object, defaults?: Object, typeDefs?: string | Array }

表示您的apollo-link-state配置的对象。 如果您想使用Apollo缓存进行本地状态管理，这非常有用。 了解更多信息[quick start](https://www.apollographql.com/docs/link/links/state.html#start).

cacheRedirects: Object

在请求发生之前将查询重定向到高速缓存中的另一个条目的函数映射。 如果您有一个项目列表，并且希望在查询单个项目的详细信息页面上使用列表查询中的数据，这非常有用。 更多关于这一点 [here](https://www.apollographql.com/docs/react/essentials/get-started.html/../features/performance.html#cache-redirects).

## 下一步

现在您已经学会了如何使用Apollo Client获取数据，您已准备好深入探索创建更复杂的查询和突变。 在本节之后，我们建议您继续：

*   [Queries](https://www.apollographql.com/docs/react/essentials/get-started.html/./queries.html): 了解如何使用参数提取查询并深入了解配置选项。 有关选项的完整列表，请查看Query的API参考。
*   [Mutations](https://www.apollographql.com/docs/react/essentials/get-started.html/./mutations.html): 了解如何使用突变更新数据，以及何时需要更新Apollo缓存。 有关选项的完整列表，请查看Mutation组件的API参考。
*   [Using Apollo Client directly](https://www.apollographql.com/docs/react/essentials/get-started.html/./direct-access.html): 有时，您需要直接访问客户端，就像我们在上面的普通JavaScript示例中所做的那样。 了解我们的指南的时间和方式，或访问API参考以获取完整的选项列表。

## 原文链接

* https://www.apollographql.com/docs/react/essentials/get-started.html
