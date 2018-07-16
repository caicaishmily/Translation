![](https://p0.ssl.qhimg.com/t019902c7bf8c4765f1.png)

在最新的TC39会议上，选择了将进入**“ECMAScript®2018语言规范”**（ES2018）的新功能。 自[ES2017](https://www.bram.us/2017/07/18/es2017-es8-language-features/) 合并以来已达到第4阶段的所有提案都已被选中。 这篇文章让我们快速了解了进入ES2018的功能。 

**_❓ Stage-4_** _TC39委员会有一个5阶段的过程，从第0阶段到第4阶段，通过它开发新的语言功能。 第四阶段是“完成”阶段。GitHub上提供了第4阶段提案列表_

### 对象 Rest/Spread 属性

在解构对象时, [对象 Rest 属性](https://github.com/tc39/proposal-object-rest-spread) 允许您将对象的剩余属性收集到新对象上。 把它想象成吸引所有剩菜的神奇磁铁。

![](https://p0.ssl.qhimg.com/t018ae788fa61f569f8.png)

我自己经常使用这个，特别是在React（Native）上下文中，我从`this.props`中获取某些值供内部使用，然后通过再次传播将所有其他道具转发给返回的子组件。

![](https://p0.ssl.qhimg.com/t01e5b700f55b4f0e33.png)

另外，如果你稍微改变你的思维逻辑，对象休息属性为你提供了一种以不可变的方式, [从对象中删除属性的好方法](https://www.bram.us/2018/01/10/javascript-removing-a-property-from-an-object-immutably-by-destructuring-it/).

### 异步迭代

使用 [异步迭代](https://github.com/tc39/proposal-async-iteration) 我们得到异步迭代器和异步迭代。 异步迭代器就像常规迭代器一样，除了它们的`next（）`方法之外，它返回一个`{value，done}`对的promise。 为了使用异步迭代，我们现在可以使用带有`for ...`ofloops的`await`关键字。

![](https://p0.ssl.qhimg.com/t0185be641b30d874d6.png)

### Promise.prototype.finally()

`Promise.prototype.finally（）`最终确定整个promises实现，允许你注册一个在一个promise被解决（被满足或被拒绝）时被调用的回调。

一个典型的用例是在`fetch（）`请求之后隐藏一个微调器：而不是复制最后一个`.then（）`和`.catch（）`中的逻辑，现在可以将它放在`.finally（）`

![](https://p0.ssl.qhimg.com/t013b661bef1e678083.png)

### RegExp相关功能

共有4个“RegExp”相关提案进入ES2018：

*   [`s`](https://github.com/tc39/proposal-regexp-dotall-flag)([`dotAll`](https://github.com/tc39/proposal-regexp-dotall-flag) )[flag for regular expressions](https://github.com/tc39/proposal-regexp-dotall-flag)
*   [RegExp named capture groups](https://github.com/tc39/proposal-regexp-named-groups)
*   [RegExp Lookbehind Assertions](https://github.com/tc39/proposal-regexp-lookbehind)
*   [RegExp Unicode Property Escapes](https://github.com/tc39/proposal-regexp-unicode-property-escapes)

我特别挖掘了“RegEx命名捕获组”功能，因为它提高了可读性：

![](https://p0.ssl.qhimg.com/t01759a10d90da7ecc7.png)

有关这些功能的更多信息可以在Mathias Bynens找到 - 这些建议背后的驱动力之一 - 他的博客: [ECMAScript正则表达式越来越好！](https://mathiasbynens.be/notes/es-regexp-proposals)

### 其他新功能

最重要的是 [模板文字调整](https://github.com/tc39/proposal-template-literal-revision) 着陆：当使用标记模板文字时，对转义序列的限制被删除，从而允许像`\ xerxes`。 在此调整之前会抛出一个错误，因为`\ x`是十六进制转义的开始，而'erxes`不是有效的十六进制值。

**_❓ 标记模板文字根据MDN：如果模板文字前面有表达式，则模板字符串称为“标记模板文字”。 在这种情况下，标记表达式（通常是函数）将使用已处理的模板文字进行调用，然后您可以在输出之前对其进行操作。_

### 现在怎么办？

请注意，并非所有浏览器都能提供所有这些功能。 意思是他们是Stage-4意味着他们已经完成了，并且浏览器供应商应该实现它们_（一些已经有，其他人正在进行中）_

至于未来,我已经在期待JavaScript的未来发展方向. 就像 [可选链接操作员](https://www.bram.us/2017/01/30/javascript-null-propagation-operator/) 已经让我很兴奋了 😊

_💻 The examples embedded in this post are part of a talk on ESNext named_ **_“What’s next for JavaScript?”_**_, which I recently gave at a_ [_Fronteers België_](https://fronteers.nl/vereniging/commissies/belgie) _meetup. I’m currently still in the process of preparing the slides for publication. I’m available for bringing this talk at your meetup/conference._
