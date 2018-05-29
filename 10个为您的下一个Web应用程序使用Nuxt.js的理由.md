如果你是一个Vue开发者，可能你听说过Nuxt.js。但是你可能不太知道关于它的所有炒作。你可能会问，为什么我要在一个框架里面再用一个框架，Vue已经让开发JavaScript应用变得很容易了，Nuxt.js背后的想法是什么？

这篇文章，我们将讲述为什么要在你的下一个项目中使用Nuxt的10个原因。

![](https://p0.ssl.qhimg.com/t01eb33e2c584463bc2.png)

### Nuxt.js 是什么?

Nuxt.js是一个更高级的框架，它构建在Vue之上。 它简化了通用或单页Vue应用程序的开发。

Nuxt.js抽象出服务器和客户端代码分发的细节，以便您可以专注于应用程序开发。 Nuxt的目标是让它足够灵活，可以作为主要的项目基础使用。 由于大部分Nuxt在开发阶段都会发生，因此只需要少量的额外千字节被添加到JavaScript文件中，您就可以获得很多功能。

让我们来探索一下为什么你需要考虑在你的下个Vue项目中要使用Nuxt的原因。
### 1\. 轻松创建通用应用程序

Nuxt.js的一个最大的卖点就是创建通用应用程序从未如此简单

#### 什么是一个通用应用程序?

一个通用应用程序用于描述可在客户端和服务器端执行的JavaScript代码。很多现代的JavaScript框架，比如说Vue，旨在创建单页面应用(SPAs)，在传统网站上使用SPA有很多好处，比如说，您可以构建快速更新且运行快速的用户界面。 但是，SPA还具有诸如加载时间长的缺点，并且谷歌正在与他们斗争，因为页面上最初没有内容用于搜索引擎优化目的。 所有的内容都是在事后用JavaScript生成的。

一个通用的应用程序是关于有一个SPA，但不是有一个空白的index.html页面，而是在Web服务器上预加载应用程序，并发送渲染的HTML页面作为对每条路线的浏览器请求的响应，以加快加载 并通过使Google更容易抓取网页来改进SEO。

#### Nuxt.js让你写一个通用应用程序更加简单

构建通用应用程序可能很乏味，因为您必须在服务器端和客户端都进行大量配置。

这是Nuxt.js旨在解决Vue应用程序的问题。 Nuxt.js使得在客户端和服务器之间共享代码变得简单，因此您可以专注于应用程序的逻辑。


Nuxt.js允许您访问组件上的isServer和isClient等属性（https://nuxtjs.org/api/context/)， 以便您可以轻松决定是在客户端还是在服务器上呈现某些内容。 还有一些特殊的组件，如no-ssr组件(https://nuxtjs.org/api/components-no-ssr/)， 用于故意阻止组件在服务器端呈现。

最后，Nuxt使您可以访问组件内部的[asyncData方法]（https://nuxtjs.org/api/）， 您可以使用它来获取数据并在服务器端渲染数据。

这是Nuxt如何帮助您创建通用应用程序的冰山一角。 [点击此处]（https://nuxtjs.org/guide） 了解更多关于Nuxt提供的渲染Universal应用程序的信息。

### 2\. 静态渲染您的Vue应用程序，并获得通用应用程序的所有优势，而无需服务器

Nuxt最大的创新在于它的nuxt generate命令。 该命令会生成一个完全静态的网站版本。 它会为您的每条路由生成HTML，并将其放入其自己的文件中。
例如，如果您有以下**页面**（Nuxt的路由术语）：

```
-| pages/----| about.vue----| index.vue
```

Nuxt将会为你生成一下文件结构：

```
-| dist/----| about/------| index.html----| index.html
```

这样做的好处与通用应用程序的优点非常相似。 有标记可以使网页更快加载，并帮助搜索引擎和社交媒体抓取工具抓取网站。

不同之处在于你不再需要服务器。 一切都在开发阶段产生。

它功能强大，因为您可以在不需要服务器的情况下获得通用渲染的好处。 您可以将您的应用程序托管在GitHub Pages或Amazon S3上。

了解关于更多 [静态生成 (预渲染)](https://nuxtjs.org/guide) 部分在Nuxt.js文档

### 3\. 获取自动代码分割（预渲染页面）

Nuxt.js能够使用特殊的Webpack配置生成您的网站的静态版本。

对于静态生成的每个路由（页面），路由也会获取自己的JavaScript文件，只需运行该路由所需的代码即可。

这对速度确实有帮助，因为它可以保持JavaScript文件的大小相对于整个应用程序的大小。

### 4\. 通过命令行使用入门模板进行设置

Nuxt.js提供了一个名为starter-template的入门模板，它为您提供所需的所有脚手架，以便您可以开始使用具有良好文件夹结构的项目。

确保你已经安装了vue-cli，然后运行如下命令：

$ vue init nuxt-community/starter-template <project-name>

从那里只需cd进入应用程序并运行npm install，这应该很容易。

[点击这儿](https://nuxtjs.org/guide/installation)了解更多关于使用命令行设置项目的信息。

### 6\. 获得优秀的默认项目结构

在许多小Vue应用程序中，您最终会像在多个文件中那样管理代码的结构。 默认的Nuxt.js应用程序结构为您以可理解的方式组织应用程序提供了一个很好的起点。

![](https://p0.ssl.qhimg.com/t0126857f0a26b8786d.png)

以下是您设置的几个主要目录：

*   组件 — 一个组织你单独的Vue组件文件夹。
*   布局 — 包含主要应用程序布局的文件夹.
*   页面 —一个文件夹来包含您的应用程序的路由。 Nuxt.js读取此目录中的所有.vue文件并创建应用程序路由器。
*   存储 - 一个包含所有应用程序的Vuex存储文件的文件夹。

[点击这儿](https://nuxtjs.org/guide/directory-structure) 以了解更多关于Nuxt.js为您提供的所有文件夹结构。

### 5\. 轻松设置您的路由之间的转换

Vue has a wrapper <transition> element that makes it simple to handle JavaScript animations, CSS animations, and CSS transitions on your elements or components.

If you need a refresher on Vue’s <transition> element and transitions in general, we wrote an article about them [here](https://medium.com/vue-mastery/how-to-create-vue-js-transitions-6487dffd0baa).

Nuxt.js sets up your routes in such a way that each page gets wrapped in a <transition> element so you can create transitions between pages simply.

[Click here](https://nuxtjs.org/examples/routes-transitions/) to see an example of how Nuxt.js helps you with page transitions.

### 7\. Easily write Single File Components

In many small Vue projects, components are defined using Vue.component, followed by new Vue({ el: ‘#container’ }) to target a container element in the body of every page.

This works well for small projects where JavaScript is only used to enhance certain views. But in bigger projects it can become difficult to manage.

All of these problems are solved by **single-file components** with a .vue extension. In order to use them, you have to set up a build process with tools like Webpack and Babel.

Here’s an example of a single-file .vue component

![](https://p0.ssl.qhimg.com/t014e678f63d5858d98.png)

Nuxt.js comes pre-configured out of the box with Webpack for you so you can start using .vue files without having to set up a complicated build process yourself.

To learn more about Single File Components visit the Vue documentation [here](https://vuejs.org/v2/guide/single-file-components.html).

### 8\. Get ES6/ES7 compilation without any extra work

Alongside Webpack, Nuxt.js also comes pre-packaged with Babel. Babel handles compiling the latest JavaScript versions like ES6 and ES7 into JavaScript that can be run on older browsers.

Nuxt.js sets up Babel for you so all of the .vue files and all of the ES6 code that you write inside of the <script> tags compiles down into JavaScript that will work on all browsers.

[Click here](https://babeljs.io/) to learn more about Babel.

![](https://cdn-images-1.medium.com/max/1600/1*IpbVaWq2fHkbVoHtSc-LDQ.png)

### 9\. Get setup with an auto-updating server for easy development

Compared to setting up this process yourself or the change-refresh-change-refresh process that we web developers are used to, developing with Nuxt.js is a breeze. It sets you up with an auto-updating development server.

While you’re developing and working on those .vue files, Nuxt.js uses a Webpack configuration to check for changes and compiles everything for you.

You can run the command npm run dev inside of a Nuxt.js project and it will set up the development server.

![](https://cdn-images-1.medium.com/max/1600/1*0zxBhC7ArC1I1MuDOd3uZg.png)

### 10\. Access to everything in the Nuxt.js community

Lastly, there’s [a GitHub collection](https://github.com/nuxt-community) called **Nuxt Community** that compiles helpful libraries, modules, starter kits, and more to make it even easier to create your app. Look through here to see if what you need is available before coding it yourself.

![](https://cdn-images-1.medium.com/max/1600/0*puQ2jCgJYY9sYTMs.png)

### 总结

所有这些功能都使Vue.js应用程序的开发更加美好。 即使您不需要通用应用程序，并希望坚持使用SPA，使用Nuxt.js仍然有好处。 它可以成为你项目的主要基础，具有诸如.vue文件，ES6编译等许多功能。

### 更多Nuxt内容

在[VueMastery.com]（https://www.vuemastery.com/） 学习Nuxt.js。 Nuxt专注的内容即将发布。 您可以创建一个免费帐户来获得通知。

### 继续深入阅读

*   [Best Practices for Nuxt.js SEO](https://medium.com/vue-mastery/best-practices-for-nuxt-js-seo-32399c49b2e5)
*   [VuePress vs. Nuxt.js](https://medium.com/p/ffc46cc38756/edit)
*   [How to Create Vue.js Transitions](https://medium.com/vue-mastery/how-to-create-vue-js-transitions-6487dffd0baa)
