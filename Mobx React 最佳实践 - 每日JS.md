![](https://p0.ssl.qhimg.com/t011ff6737b8c8ad266.png)

# Mobx React — 最佳实践

在本文中，我想向您展示将React与mobx一起使用的常见最佳实践。 我会把它们作为规则来呈现。 因此，无论何时遇到特定问题，请在遵守这些规则的同时尝试解决问题。

本文要求您对mobx中的store有基本的了解。 如果没有，请先阅读 [这个](https://mobx.js.org/best/store.html) 

> 需要快速入门吗？ 我创建了一个启动项目，它实现了推荐的实践. [https://github.com/danielbischoff/react-mobx-starter](https://github.com/danielbischoff/react-mobx-starter)

#### 这些Store代表UI状态

请时刻记住，store代表你的应用程序的ui状态。 这意味着，当您将store的状态保存到文件中时，关闭程序并使用加载状态重新启动它，您将拥有相同的程序，并会看到相同的内容，就像您在关闭程序之前所看到的那样。 store不是“本地数据库”。 它们还包含有关哪些按钮可见，禁用，输入文件的当前文本等信息。

#### 将你的rest请求与store分开

不要在store内调用你的rest接口。 这使他们很难测试。 而是将这些rest调用放入额外的类中，并使用store的构造函数将这些实例传递给每个store。 当您编写测试时，您可以轻松伪造这些api调用并将伪造的api实例传递给每个store。

#### 将您的业务逻辑保留在store中

不要在组件中编写业务逻辑。 当您在组件中编写业务逻辑时，您没有机会重用它，您的业务逻辑会分布在许多组件上，这使得重构或重用代码变得困难。 使用store中的方法编写业务逻辑，并从组件中调用这些方法。

#### 不要创建全局store实例。 

不要创建全局store实例。 您无法为组件编写任何合理且可靠的测试。 而是使用Provider将您的store注入您的组件props中。 然后在您的测试中，您可以轻松地模拟这些store。

#### 只允许store更改其属性

切勿直接在组件中更改store的属性。 只允许store更改自己的属性。 始终从store调用方法来更改store的属性。 否则，您的应用程序状态（stores=应用程序状态）将从任何地方更新，您正在慢慢失去控制。 这使得调试非常困难。

#### 始终使用@observer注释每个组件

注解为@observer的每个组件允许每个组件在store prop的变化更新。 否则，使用@ component注释的父组件需要重新呈现，以更新其子组件。 因此需要重新渲染更少的组件。

#### 使用 @computed

假设您希望在用户没有管理员角色且应用程序未处于“管理员模式”时禁用您的按钮。 像一个store中的isAdmin这样的单一属性是不够的。 您将需要store中的计算属性。

#### 你可能不需要react router

你可能不需要react router。 正如我之前所说，您希望您的store代表您的应用程序的状态。 当使用react router处理部分应用程序状态时，您不要让您的store代表应用程序状态。 因此，请将当前显示的视图保存在您的某个store的属性中。 然后你有一个组件只是呈现属性所说的内容。

#### 尝试支持受控组件而不是不受控制的组件

始终尝试构建受控组件。 这使得测试组件和组件的整体复杂性易于处理。

#### 原文链接

https://medium.com/dailyjs/mobx-react-best-practices-17e01cec4140
