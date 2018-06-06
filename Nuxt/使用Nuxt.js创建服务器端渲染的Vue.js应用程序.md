JavaScript框架/库（如Vue）可以在浏览您的网站时提供出色的用户体验。 大多数提供了一种动态更改页面内容的方式，而不必每次都向服务器发送请求。

但是，这种方法存在问题。 最初加载您的网站时，您的浏览器没有收到完整的页面显示。 相反，它会发送一堆文件来构建页面（HTML，CSS和其他文件）以及如何将它们放在一起的说明（JavaScript框架/库）需要花费相当多的时间将所有这些信息放在一起 在浏览器实际上显示某些内容之前。 这就像被送了一堆书以及一个扁平的书柜一样。 你必须先建立书架，然后用书填充它。

这个解决方案很聪明：在_server_上有一个框架/库的版本，可以构建一个准备好显示的页面。 然后将这个完整的页面发送到浏览器，并具有进一步修改的能力，并且仍然具有动态页面内容（框架/库），就像发送一个现成的书架和一些书一样。 当然，你还是必须把书放在书柜里，但是你可以立刻拿到东西。

![Visual comparison of client-side and server-side rendering](https://p0.ssl.qhimg.com/t01d19e31e9df6d5089.png)

除了愚蠢的比喻之外，还有其他一些优点。 例如，很少更改的页面（例如关于我们页面）不需要在用户每次请求它时重新创建。 因此，服务器可以创建一次，然后将其缓存或存储在某处以供将来使用。 这些类型的速度改进可能看起来很小，但在响应速度以毫秒（或更少）为单位进行测量的时间内，每一点都很重要。

如果您想了解更多有关Vue环境中SSR优势的信息，您应该查看[Vue’s own article on SSR](https://ssr.vuejs.org/en/). 实现这些结果有多种选择，但最受欢迎的选项是Nuxt [由Vue团队推荐](https://vuejs.org/v2/guide/ssr.html#Nuxt-js).

## 为什么是 Nuxt.js

Nuxt.js基于名为Next的热门React库的SSR实现。 在看到这个设计的优点之后，为Vue设计了一个名为Nuxt的类似实现。 熟悉React + Next组合的人会在应用程序的设计和布局中发现一些相似之处。 但是，Nuxt提供Vue特有的功能来为Vue创建强大且灵活的SSR解决方案。

Nuxt已经升级到1.0版本的产品版本 [January 2018](https://medium.com/@nuxt_js/nuxt-js-1-0-is-out-bab1af459972) 并且是一个积极和支持良好的社区的一部分。 其中一件伟大的事情是，使用Nuxt构建项目与构建任何其他Vue项目没有什么不同。 实际上，它提供了许多功能，可以让您在更短的时间内创建结构良好的代码库。

另外需要注意的是**Nuxt不能用于SSR**。 它被推广为创建通用Vue.js应用程序的框架，并包含一个用于使用相同代码库创建静态生成的Vue应用程序的命令（nuxt generate）。 所以如果你对深入SSR感到担心，不要惊慌。 您可以始终创建一个静态站点，同时仍然利用Nuxt的功能。

为了掌握Nuxt的潜力，我们来创建一个简单的项目。 这个项目的最终源代码在这里[hosted on GitHub](https://github.com/BenShelton/nuxt-example) 如果你想查看它，或者你可以在线查看使用nuxt生成并在Netlify上托管的例子[view a live version](https://nuxt-example.netlify.com/) 
## 创建一个Nuxt项目

首先，让我们使用名为vue-cli的Vue项目生成器来快速创建示例项目：

```bash
# install vue-cli globally
npm install -g vue-cli

# create a project using a nuxt template
vue init nuxt-community/starter-template my-nuxt-project

```

经过几个选项后，这将在文件夹my-nuxt-project或您指定的任何内容中创建一个项目。 然后我们只需要安装依赖关系并运行服务器：

```bash
cd my-nuxt-project
npm install # Or yarn
npm run dev

```

到这一步就可以打开你的浏览器到localhost:3000，你的项目应该正在运行。 与创建Vue Webpack项目没有多大区别。 但是，当我们查看应用程序的实际结构时，那里并不多，特别是与Vue Webpack模板相比时。

![Diagram of project directories and their relation to the Nuxt config file](https://p0.ssl.qhimg.com/t01fce09cedc83cedc2.png)

查看package.json也显示我们只有一个依赖项，Nuxt本身。 这是因为每个版本的Nuxt都适合与Vue，Vue-router和Vuex的特定版本一起工作，并将它们捆绑在一起。

项目根目录中还有一个nuxt.config.js文件。 这使您可以自定义Nuxt提供的一组功能。 默认情况下，它会为您设置标题标记，加载栏颜色和ESLint规则。 如果你渴望看到你可以配置什么, [这里是相关文档](https://nuxtjs.org/guide/configuration/); 我们将在本文中介绍一些选项。

那么这些目录有什么特别之处呢？

## 项目布局

如果您浏览创建的目录，则它们都有一个随附的自述文件，说明目录中的内容以及通常与文档的链接。

这是使用Nuxt的一个好处：应用程序的默认结构. 任何 [优秀的前端开发人员](https://www.toptal.com/front-end) 将构建类似于此的应用程序，但是关于结构有许多不同的想法，并且在团队中工作时，一段时间将不可避免地讨论或选择这种结构。 Nuxt为您提供一个。

Nuxt将查找某些目录并根据它发现的内容为您构建应用程序。 让我们逐个检查这些目录。

### Pages

这是唯一**必需的**目录。 此目录中的任何Vue组件都会根据它们的文件名和目录结构自动添加到vue-router中。 这非常方便。 通常我会有一个单独的Pages目录，并且必须在另一个路由器文件中手动注册每个组件。 此路由器文件对于较大的项目可能会变得复杂，并且可能需要拆分才能保持可读性。 相反，Nuxt将为你处理所有这些逻辑。

为了演示，我们可以在Pages目录中创建一个名为about.vue的Vue组件。 我们只需添加一个简单的模板，例如：

```html
<template>
 <h1>About Page</h1>
</template>

```

当你保存时，Nuxt会为你重新生成路由。 看到我们称为我们的组件about.vue，如果您导航到/about，您应该看到该组件。 简单吧。

有一个特殊的文件名。 命名文件index.vue将为该目录创建一个根路由。 项目生成时，pages目录中已经有一个index.vue组件，它与您网站的主页或登录页面相关。 （在开发示例中，这只是localhost:3000。）

![Nuxt scans the Vue files in the pages directory and outputs the appropriate pages.](https://p0.ssl.qhimg.com/t01e1723dd2eca256eb.png)

深层次路由怎么样呢？ Pages目录中的子目录有助于构建您的路线。 所以如果我们想要一个View Product页面，我们可以像这样构造我们的Pages目录：

```shell
/pages
--| /products
----| index.vue
----| view.vue

```

现在，如果我们导航到/products/view，我们将在products目录中看到view.vue组件。 如果我们导航到/products，我们将在products目录中看到index.vue组件。

您可能会问，为什么我们不只是在pages目录中创建products.vue组件，而是像我们为/about页面所做的那样。 你可能会认为结果是一样的，但这两种结构之间是有区别的。 让我们通过添加另一个新页面来演示这一点。

假设我们想为每个员工分配一个关于页面。 例如，让我们为我创建一个关于页面。 它应该位于/about/ben-jones。 最初，我们可以尝试像这样构建Pages目录：

```shell
/pages
--| about.vue
--| /about
----| ben-jones.vue

```

当我们尝试访问/about/ben-jones时，我们改为获取about.vue组件，与/about相同。 这里发生了什么？

有趣的是，Nuxt在这里做的是生成一个**嵌套路由**。 这种结构表明你想要一个永久性的/about路由，并且该路由内的任何东西都应该嵌套在它自己的视图区域中。 在vue-router中，这将通过在about.vue组件中指定一个<router-view />组件来表示。 在Nuxt中，这是相同的概念，除了<router-view />之外，我们只需使用<nuxt />。 所以让我们更新我们的about.vue组件以允许嵌套路由：

```html
<template>
 <div>
   <h1>About Page</h1>
   <nuxt />
 </div>
</template>

```

现在，当我们导航到/ about时，我们得到了我们之前拥有的about.vue组件，只有一个标题。 但是，当我们导航到/ about/ben-jones时，我们改为使用_and_ ben-jones.vue组件呈现<nuxt />占位符所在的位置。

这不是我们最初想要的，但是有一个带有一个人列表的关about页面的想法，当点击它时，用他们的信息填充页面上的一个部分是一个有趣的概念，所以让我们暂时将它放在一边。 如果您确实需要其他选项，那么我们所要做的就是重构我们的目录。 我们只需要移动/about目录中的about.vue组件，并将其重命名为index.vue，因此生成的结构将是：

```shell
/pages
--| /about
----| index.vue
----| ben-jones.vue

```

最后，假设我们想要使用路由参数来检索特定的产品。 例如，我们希望能够通过导航到/products/edit/64来编辑产品，其中64是product_id。 我们可以通过以下方式做到这一点：

```shell
/pages
--| /products
----| /edit
------| _product_id.vue

```

请注意_product_id.vue组件开头的下划线 - 这表示可以通过$route.params对象或Nuxt的Context中的params对象（稍后会介绍）访问一个路由参数。 请注意，param的键将是没有初始下划线的组件名 _在这种情况下，product_id_ 因此尽量使它们在项目中保持唯一。 因此，在_product_id.vue中，我们可能会有这样的内容：

```html
<template>
 <h1>Editing Product {{ $route.params.product_id }}</h1>
</template>

```

你可以开始想象更复杂的布局，这将是一个使用vue-router设置的痛苦。 例如，我们可以将所有上述内容组合到一个路由中，例如：

```shell
/pages
--| /categories
----| /_category_id
------| products.vue
------| /products
--------| _product_id.vue

```

这并不难去解释像/categories/2/products/3这样的路由会显示什么。我们将products.vue组件与_nested_ _product_id.vue组件一起使用，其中包含两个路径参数：category_id和product_id。 这比起等价的路由器配置来说要简单得多。

当我们谈论这个话题时，我倾向于在路由器配置中做的一件事是设置路由器防护。 由于Nuxt正在为我们构建路由器，因此可以使用beforeRouteEnter在组件本身上完成此操作。 如果你想验证路由参数，Nuxt提供了一个名为validate的组件方法。 因此，如果您想在渲染组件之前检查product_id是否为数字，则可以将以下内容添加到_product_id.vue的脚本标记中：

```js
export default {
 validate ({ params }) {
   // Must be a number
   return /^\d+$/.test(params.product_id)
 }
}

```

现在，导航到 /categories/2/products/someproduct会是404的结果，因为someproduct是不是一个有效的数字。

这就是Pages目录。 学习如何在这个目录中正确地组织你的路由是必不可少的，所以最初花费一点时间对于充分利用Nuxt非常重要。 如果你正在寻找一个简要的概述，那么参考它是很有帮助的 [路由文档](https://nuxtjs.org/guide/routing).

如果你担心无法控制路由器，大可不必。 这种默认设置适用于各种各样的项目，只要它们结构良好。 但是，在某些情况下，您可能需要添加更多路由到路由器，而不是Nuxt为您自动生成或重新构建它们。 Nuxt提供了一种在配置中自定义路由器实例的方法，允许您添加新路由并自定义生成的路由。 您还可以编辑路由器实例的核心功能，包括由Nuxt添加的额外选项。 所以如果你确实遇到了一个边缘案例，你仍然可以灵活地找到合适的解决方案。

### Store

Nuxt可以根据store目录的结构构建您的Vuex store，类似于Pages目录。 如果你不需要store，只需删除目录。 store有两种模式，Classic和Modules。

Classic**需要**你在store目录中有一个index.js文件。 在那里你需要导出一个返回一个Vuex实例的函数：

```js
import Vuex from 'vuex'

const createStore = () => {
 return new Vuex.Store({
   state: ...,
   mutations: ...,
   actions: ...
 })
}

export default createStore

```

这样可以让您根据自己的需要创建store，就像在正常的Vue项目中使用Vuex一样。

模块模式也**要求**您在store目录中创建一个index.js文件。 但是，该文件只需要导出Vuex store的根状态/突变/操作。 下面的例子指定了一个空白的根状态：

```js
export const state = () => ({})

```

然后，store目录中的每个文件将被添加到它自己的名称空间或模块中的store。 例如，让我们创建一个存储当前产品的地方。 如果我们在store目录中创建一个名为product.js的文件，那么store的命名空间部分将在$ store.product中可用。 以下是该文件的一个简单示例：

```js
export const state = () => ({
 _id: 0,
 title: 'Unknown',
 price: 0
})

export const actions = {
 load ({ commit }) {
   setTimeout(
     commit,
     1000,
     'update',
     { _id: 1, title: 'Product', price: 99.99 }
   )
 }
}

export const mutations = {
 update (state, product) {
   Object.assign(state, product)
 }
}

```

加载动作中的setTimeout模拟某种API调用，它将用响应更新存储; 在这种情况下，它需要一秒钟。 现在，让我们在 products/view页面中使用它：

```html
<template>
 <div>
   <h1>View Product {{ product._id }}</h1>
   <p>{{ product.title }}</p>
   <p>Price: {{ product.price }}</p>
 </div>
</template>

<script>
import { mapState } from 'vuex'
export default {
 created () {
   this.$store.dispatch('product/load')
 },
 computed: {
   ...mapState(['product'])
 }
}
</script>

```

有几点需要注意：在这里，我们在创建组件时调用我们的假API。 您可以看到我们正在调度的product/load操作是在Product下的命名空间。 这就明确了我们正在处理的store的哪一部分。 然后，通过将状态映射到本地计算属性，我们可以在我们的模板中轻松使用它。

有一个问题：我们在API运行时看到原始状态。 之后，我们将使用由Nuxt提供的解决方案来修复此问题（称为提取）。

再次强调一下，我们从来不需要npm install vuex，因为它已经包含在Nuxt包中。 当您将一个index.js文件添加到store目录时，所有这些方法都将随后自动打开。

这是主要解释的两个目录; 其余的更简单。

### Components

组件目录包含可重用组件，如导航栏，图像库，分页，数据表等。在Pages目录中看到组件被转换为路由，您需要在其他位置存储这些类型的组件。 这些组件可通过导入页面或其他组件访问：

```js
import ComponentName from ~/components/ComponentName.vue

```

### Assets

这包含未编译的assets，并且更多与Webpack如何加载和处理文件有关，而不是Nuxt如何工作。 如果你有兴趣，我建议阅读 [guide in the Readme](https://nuxtjs.org/guide/assets).

### Static

这包含映射到您网站根目录的静态文件。 例如，在这个目录中放置一个名为logo.png的图像可以使它在/logo.png中可用。 这对robots.txt，favicon.ico等元文件以及其他需要的文件很有用。

### Layouts

通常，在Vue项目中，你有某种根组件，通常称为App.vue。 您可以在这里设置您的（通常为静态的）应用布局，其中可能包括导航栏，页脚，然后是vue路由器的内容区域。 默认布局完全是这样做的，并且在布局文件夹中为您提供。 最初，它只有一个带有<nuxt />组件（相当于<router-view />）的div，但是可以根据需要设置样式。 例如，我在示例项目中添加了一个简单的导航栏，用于在各种演示页面中进行导航。

![A layout can be applied to multiple pages.](https://p0.ssl.qhimg.com/t0150c4b3d25eeffca7.png)

您可能希望为应用的某个部分设置不同的布局。 也许你有一些看起来不同的CMS或管理面板。 要解决这个问题，请在布局目录中创建一个新布局。 作为一个例子，我们来创建一个admin-layout.vue布局，它只有一个额外的标题标签并且没有导航栏：

```html
<template>
 <div>
   <h1>Admin Layout</h1>
   <nuxt />
 </div>
</template>

```

然后，我们可以在Pages目录中创建一个admin.vue页面，并使用由Nuxt提供的名为layout的属性来指定我们要用于该组件的布局的名称（作为字符串）：

```html
<template>
 <h1>Admin Page</h1>
</template>

<script>
export default {
 layout: 'admin-layout'
}
</script>

```

以上就是Nuxt的所有。 除非指定，否则页面组件将使用默认布局，但当您导航到/ admin时，它现在使用admin-layout.vue布局。 当然，如果你愿意，这个布局可以在多个管理屏幕上共享。 要记住的一件重要事情是**布局必须包含<nuxt />元素**。

最后要注意的是布局。 您可能在尝试时注意到，如果您键入无效的URL，则会显示一个错误页面。 事实上，这个错误页面是另一种布局。 Nuxt有它自己的错误布局([源码在此](https://github.com/nuxt/nuxt.js/blob/master/lib/app/components/nuxt-error.vue)), 但是如果你想编辑它，只需创建一个error.vue布局，然后用它来代替。 这里需要注意的是错误布局不能包含<nuxt /> element_。 您还可以访问组件上的错误对象，并显示一些基本信息。 （如果你想检查它，它会在运行Nuxt的终端中打印出来。）

### Middleware

中间件是可以在呈现页面或布局之前执行的功能。 您可能想要这么做的原因有很多。 路由防护是一种流行的用法，您可以在Vuex存储中检查有效的登录或验证某些参数（而不是在组件本身上使用验证方法）。 我最近使用的一个项目使用中间件来生成基于路线和参数的动态面包屑。

这些功能可以是异步的; 只是要小心，因为在中间件解决之前什么都不会显示给用户。 他们也可以访问Nuxt的上下文，我将在后面解释。

### Plugins

该目录允许您在创建应用程序之前注册Vue插件。 这允许插件在Vue实例的整个应用程序中共享，并且可以在任何组件中访问。

大多数主要的插件都有一个Nuxt版本，可以通过遵循他们的文档轻松注册到Vue实例。 但是，在某些情况下，您将开发插件或需要修改现有的插件。 我从文档中借用的示例显示了如何为vue通知执行此操作。 首先，我们需要安装软件包：

```bash
npm install vue-notifications --save

```

然后在plugins目录中创建一个名为vue-notifications.js的文件，并包含以下内容：

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)

```

类似于您在正常Vue环境中注册插件的方式。 然后编辑项目根目录下的nuxt.config.js文件，并将以下条目添加到module.exports对象中：

```js
plugins: ['~/plugins/vue-notifications']

```

就是这样。 现在，您可以在整个应用程序中使用vue通知。 示例项目中的/插件就是一个例子。

这样就完成了目录结构的概要。 这可能看起来很多，但如果你正在开发一个Vue应用程序，你已经设置了相同的逻辑。 Nuxt有助于抽象化设置并帮助您专注于构建。

Nuxt不仅仅是帮助开发。 它通过提供额外的功能来超越您的组件。


### Nuxt的增压组件

当我第一次开始研究Nuxt时，我不断阅读有关页面组件是如何被超载的。 它听起来很棒，但它并不是很明显，究竟是什么意思，它带来了什么好处。

这意味着所有的页面组件都有附加的附加方法，Nuxt可以使用它来提供附加功能。 事实上，我们已经在前面看到过其中一种，当我们使用验证方法检查参数并在用户无效时重定向用户。

Nuxt项目中使用的两个主要是asyncData和fetch方法。 两者在概念上非常相似，它们在生成组件之前以_asynchronously_方式运行，并且可以用它们来填充组件和存储的数据。 它们还可以使页面在发送到客户端之前在服务器上完全呈现，即使我们必须等待某些数据库或API调用。

asyncData和fetch之间有什么区别？

*   asyncData用于填充页面组件的数据。 当您返回一个对象时，它将在渲染前与数据输出合并。
*   fetch用于填充Vuex Store。 如果你返回一个promise，Nuxt将等待，直到它在渲染前解决。

所以让我们把它们变得更好用。 较早的时候不知你是否记得在/products/view页面，我们遇到了一个问题，即在我们假的API调用正在进行时，store的初始状态会短暂显示？ 解决这个问题的一种方法是在组件或应用store中存储布尔值，例如load = true，然后在API调用完成时显示加载组件。 之后，我们将设置loading= false并显示数据。

相反，让我们在呈现之前使用抓取来填充store。 在一个名为/products/view-async的新页面中，让我们更改创建的方法以获取; 这应该工作，对吗？

```js
export default {
 fetch () {
   // Unfortunately the below line throws an error
   // because 'this.$store' is undefined...
   this.$store.dispatch('product/load')
 },
 computed: {...}
}

```

这里有一个问题：这些“增压”方法在创建组件之前运行_before_，所以这不会指向组件，也不会访问组件。 那么我们如何在这里访问store？

### 上下文API

当然，有一个解决方案。 在Nuxt的所有方法中，都提供了一个参数（通常是第一个参数），其中包含一个非常有用的名为Context的对象。 这就是你需要在整个应用程序中引用的所有内容，这意味着我们不需要等待Vue首先在组件上创建这些引用。

我强烈建议查看[Context docs](https://nuxtjs.org/api/context/) 里面有什么可用的，一些便利的应用程序，您可以在其中访问所有插件，重定向，可用于更改路由，显示错误页面的错误以及路由，查询和存储等一些不言自明的插件。

因此，要访问store，我们可以解构上下文并从中提取store。 我们还需要确保我们返回一个promise，以便Nuxt可以在渲染组件之前等待它解决，因此我们还需要对Store操作进行一些小调整。

```js
// Component
export default {
 fetch ({ store }) {
   return store.dispatch('product/load')
 },
 computed: {...}
}

// Store Action
load ({ commit }) {
 return new Promise(resolve => {
   setTimeout(() => {
     commit('update', { _id: 1, title: 'Product', price: 99.99 })
     resolve()
   }, 1000)
 })
}

```

您可以根据您的编码风格使用async/await或其他方法，但概念是相同的 - 我们告诉Nuxt确保API调用完成，并且Store更新结果_before_尝试渲染组件。 如果您尝试导航到/products/view-async，则无法看到产品处于初始状态的内容的闪光灯。

您可以想象，即使没有SSR，这在任何Vue应用程序中都可能有用。 上下文也可用于所有中间件以及NuxtServerInit等其他Nuxt方法，这是一个在Store初始化之前运行的特殊存储操作（下一部分将给出一个示例）

## 使用SSR时的注意事项

我相信很多（包括我自己）在开始使用像Nuxt这样的技术的同时，像处理任何其他Vue项目一样，最终遇到了一个我们知道通常工作的墙，在Nuxt中似乎是不可能的。 由于更多的这些注意事项被记录在案，它会更容易克服，但开始调试时要考虑的主要问题是客户端和服务器是两个独立的实体。

当您最初访问页面时，会向Nuxt发送请求，服务器将尽可能多地构建该页面和应用程序的其余部分，然后服务器将其发送给您。 然后，客户的责任是在需要时继续导航和加载块。

我们希望服务器尽可能多地做，但有时它不能访问它需要的信息，这会导致客户端完成工作。 或者更糟的是，当客户端呈现的最终内容与服务器预期的内容不同时，客户端会被告知从头开始重新构建它。 这是一个很大的迹象表明应用程序逻辑有问题。 值得庆幸的是，如果开始发生错误，您的浏览器控制台（在开发模式）会生成一个错误。

我们举一个例子来说明如何解决一个常见问题，会话管理。 想象一下，您有一个Vue应用程序，您可以在其中登录帐户，并使用您决定保留在localStorage中的令牌（例如JWT）存储会话。 当您最初访问站点时，您需要根据API对该令牌进行身份验证，该API会返回一些基本的用户信息（如果有效）并将该信息放入应用store中。

阅读Nuxt的文档后，您会看到有一个称为NuxtServerInit的方便方法，它允许您在初始加载时异步填充Store。 这听起来很完美！ 因此，您可以在Store中创建用户模块，并在Store目录的index.js文件中添加适当的操作：

```js
export const actions = {
 nuxtServerInit ({ dispatch }) {
   // localStorage should work, right?
   const token = localStorage.getItem('token')
   if (token) return dispatch('user/load', token)
 }
}

```

刷新页面时，出现错误，localStorage未定义。 想想这是怎么回事，这是有道理的。 此方法在服务器上运行，它不知道客户端上存储在localStorage中的内容; 实际上，它甚至不知道“localStorage”是什么！ 所以这不是一个选项。

![The server tries to execute localStorage.getItem('token') but throws an error, then a caption below explaining the problem.](https://p0.ssl.qhimg.com/t0186255de2c95385cb.png)

那么解决方案是什么？ 其实有几个。 您可以让客户初始化store，但最终失去了SSR的好处，因为客户最终完成了所有的工作。 您可以在服务器上设置会话，然后使用它来验证用户，但这是另一个要设置的层。 最类似于localStorage方法的是使用cookie。

Nuxt可以访问cookie，因为它们是通过客户端向服务器发送的请求发送的。 与其他Nuxt方法一样，nuxtServerInit可以访问Context，这次是第二个参数，因为第一个参数是为store保留的。 在上下文中，我们可以访问req对象，该对象存储来自客户端请求的所有标题和其他信息。 （如果您使用过Node.js，这将特别熟悉）

因此，在将令牌存储在cookie中（在本例中称为“令牌”）之后，让我们在服务器上访问它。

```js
import Cookie from 'cookie'

export const actions = {
 nuxtServerInit ({ dispatch }, { req }) {
   const cookies = Cookie.parse(req.headers.cookie || '')
   const token = cookies['token'] || ''
   if (token) return dispatch('user/load', token)
 }
}

```

一个简单的解决方案，但可能并不明显。 学习思考某些行为发生在何处（客户端，服务器或两者）以及他们可以访问的内容需要一些时间，但好处是值得的。

## 部署

使用Nuxt进行部署非常简单。 使用相同的代码库，您可以创建一个SSR应用程序，单页面应用程序或静态页面。

### 服务器端渲染的应用程序（SSR App）

这可能是您在使用Nuxt时瞄准的目标。 这里部署的基本概念是在您选择的任何平台上运行构建过程并设置一些配置。 我将使用[docs](https://nuxtjs.org/faq/heroku-deployment)中的Heroku示例

首先，在package.json中为Heroku设置脚本：

```js
"scripts": {
 "dev": "nuxt",
 "build": "nuxt build",
 "start": "nuxt start",
 "heroku-postbuild": "npm run build"
}

```

然后使用heroku-cli设置Heroku环境([setup instructions here](https://devcenter.heroku.com/articles/heroku-cli):

```bash
# set Heroku variables
heroku config:set NPM_CONFIG_PRODUCTION=false
heroku config:set HOST=0.0.0.0
heroku config:set NODE_ENV=production

# deploy
git push heroku master

```

就是这样。 现在您的SSR Vue应用程序已准备好供全世界观看。 其他平台有不同的设置，但过程相似。 目前列出的官方部署方法是：

*   [Now](https://nuxtjs.org/faq/now-deployment)
*   [Dokku (Digital Ocean)](https://nuxtjs.org/faq/dokku-deployment)
*   [Nginx](https://nuxtjs.org/faq/nginx-proxy)

### 单页面应用(SPA)

如果您想利用Nuxt提供的一些额外功能，但要避免服务器试图渲染页面，则可以将其部署为SPA。

首先，最好在不使用SSR的情况下测试您的应用程序，因为默认情况下npm run dev在SSR上运行。 要改变它，请编辑nuxt.config.js文件并添加以下选项：

```js
mode: 'spa',

```

现在，当你运行npm run dev时，SSR将被关闭，应用程序将作为SPA来运行，供你测试。 这个设置也确保了未来的版本将包含SSR。

如果一切看起来都很好，那么部署与SSR应用程序完全相同。 请记住，您需要先设置模式：'spa'，让构建过程知道您需要SPA。

### 静态页面

如果您根本不想处理服务器，而是想生成可用于静态托管服务（例如Surge或Netlify）的页面，则可以选择此选项。 请记住，如果没有服务器，您将无法访问上下文中的req和res，因此如果您的代码依赖于此，请确保符合它。 例如，在生成示例项目时，nuxtServerInit函数会引发错误，因为它试图从请求头中的cookie中获取令牌。 在这个项目中，这并不重要，因为这些数据并没有在任何地方使用，但在实际的应用程序中，需要有另一种方式来访问这些数据。

一旦排序，部署就很容易。 您可能需要首先更改的一件事是添加一个选项，以便nuxt generate命令也会创建一个后备文件。 这个文件会提示主机服务让Nuxt处理路由而不是主机服务，并抛出404错误。 为此，请将以下行添加到nuxt.config.js中：

```js
generate: { fallback: true },

```

这里有一个使用Netlify的例子，它目前不在Nuxt文档中。 请记住，如果这是您第一次使用netlify-cli，系统会提示您进行身份验证：

```bash
# install netlify-cli globally
npm install netlify-cli -g

# generate the application (outputs to dist/ folder)
npm run generate

# deploy
netlify deploy dist

```

就这么简单！ 正如本文开头提到的那样，这个项目有一个版本[here](https://nuxt-example.netlify.com/). 以下服务还有官方部署文档：

*   [Surge](https://nuxtjs.org/faq/surge-deployment)
*   [GitHub Pages](https://nuxtjs.org/faq/github-pages)

## 了解更多

Nuxt正在迅速更新，而这只是其提供的一小部分功能。 我希望这篇文章鼓励您尝试一下，看看它是否有助于改进Vue应用程序的功能，使您能够更快地开发并利用其强大的功能。

如果你正在寻找更多的信息，那么看看Nuxt的官方链接：

*   [Documentation](https://nuxtjs.org)
*   [Playground](https://glitch.com/edit/#!/nuxt-hello-world)
*   [GitHub](https://github.com/nuxt/nuxt.js)
*   [FAQ](https://nuxtjs.org/faq)

寻找你的JavaScript游戏？ 尝试阅读Toptaler MarkoMišura的[JavaScript设计模式综合指南](https://www.toptal.com/javascript/comprehensive-guide-javascript-design-patterns)

## 理解基础

### 什么是Vue.js?

Vue.js（或简称Vue）是一个JavaScript框架，其设计轻巧易学，而且功能强大，足以处理大型应用程序。 它允许您开发丰富的交互式Web应用程序，以提供卓越的用户体验。

### 什么是Nuxt?

Nuxt是创建通用Vue应用程序的框架。 这意味着它为您的项目提供了一个结构，为您处理更复杂的服务器配置，并允许在各种环境中部署相同的代码库。

### Vue和Nuxt开源吗?

是的，他们的两个代码库都可以在GitHub上公开查看，并且正在持续开发中。 两个都有核心团队，并得到社区的支持。 在那里跟踪问题，欢迎任何人提出改进建议或提交拉取请求。

### 客户端和服务器端意味着什么?

客户端是指用于请求并最终显示网页的设备，例如，当您浏览网站时，您的计算机/平板电脑/手机上的浏览器。 服务器端是指接收网页请求并发送相应文件以显示该网页的服务器。

### 服务器端渲染有什么好处?

SSR提供更好的SEO，更快的初始负载和缓存页面的能力，但通常意味着更多的服务器请求和整页重新加载。 将SSR与客户端框架（如Vue）的优点相结合，可以提供丰富的用户交互和就地DOM更新，使Nuxt成为一个出色的解决方案。

### 什么是虚拟DOM?

像Vue.js这样的框架允许部分更新网页的文档对象模型（DOM）。 但是，直接的DOM更新是耗时的，所以相反，自Vue.js 2.0以来，DOM的虚拟副本保存在内存中，首先在那里进行更改，而高效更新定期将这些更改添加到真实DOM中。

[Hiring? Meet the Top 10 Freelance Vue.js Developers for Hire in May 2018](https://www.toptal.com/vue-js)

### 原文链接：

 https://www.toptal.com/vue-js/server-side-rendered-vue-js-using-nuxt-js
