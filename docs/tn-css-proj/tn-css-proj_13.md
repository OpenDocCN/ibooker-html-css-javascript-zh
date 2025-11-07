# 附录。

## 与供应商前缀和功能标志一起工作

CSS 中最令人沮丧的方面之一，尤其是在使用新的 CSS 语法时，就是供应商前缀。每个浏览器都有一个类型的引擎，这被称为 *供应商*。引擎的目的是将代码（HTML、CSS 和 JavaScript）转换为最终用户看到的和与之交互的内容，例如网页或应用程序。主要有三个主要的浏览器引擎：

+   *Gecko*（也称为 Quantum）——由 Firefox 浏览器使用，并由 Mozilla 维护

+   *WebKit*——由 Safari 和 iOS Safari 使用，并由 Apple 开发

+   *Blink*——由 Chrome、Microsoft Edge 和 Opera 使用，并由 Google 维护

作为 CSS 的编写者，你可能会发现一些属性仍然需要供应商前缀，特别是如果你或你所在的组织支持旧浏览器引擎。前缀位于 CSS 属性之前。总共有四个前缀，列于表 A.1 中。

表 A.1 浏览器前缀列表

| Prefix | 浏览器 |
| --- | --- |
| `-webkit-` | Android, Chrome, iOS, Edge 和 Opera（较新版本），以及 Safari |
| `-ms-` | Internet Explorer 和 Edge（较旧版本） |
| `-o-` | Opera（较旧版本） |
| `-moz-` | Firefox |

尽管 Chrome 使用 Blink 引擎，但它仍然使用 `-webkit-` 前缀，因为 Chrome 是基于 WebKit 构建的。当 Chrome 转移到 Blink 引擎时，它决定继续使用 `-webkit-` 前缀而不是创建一个新的前缀，以减少混淆。正如你将在本附录中看到的那样，无论如何，正在逐渐放弃使用前缀。

当使用前缀时，你应该将前缀版本放在非前缀版本之前。包含非前缀版本的原因是，当浏览器完全支持该属性时，它将使用非前缀版本；然后你可以移除前缀。一个需要前缀的 CSS 属性示例是 `user-select`，如果你使用 `none` 作为值：

```
.prevent-selecting{
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
```

供应商前缀背后的想法是，你可以在新的 CSS 在浏览器之间标准化时尝试新的 CSS，而不会破坏用户体验。然而，我们不推荐将带有前缀的代码直接提供给用户，因为浏览器解释该代码的方式可能会改变。

由于浏览器前缀导致了部分实现和错误，并且长期以来让开发者感到困惑，因此正在逐渐放弃使用浏览器前缀。我们经常看到一些样式表带有前缀，而这些前缀已经好几年没有使用了，因为样式表没有更新，或者开发者不确定是否安全地移除前缀。相反，现在正转向使用功能标志，用户可以控制这些标志。当编写 CSS 时，你会发现一些仍在使用的 CSS 属性需要浏览器前缀。在这种情况下，CSS 属性的前缀版本应该放在非前缀版本之前。

## 使用浏览器开发者工具

Chrome、Safari、Firefox 和其他主流浏览器都提供了开发者工具，非常适合编辑和诊断问题，尤其是如果您正在进行前端开发。您可以在浏览器内编辑您的 CSS，然后将样式复制并粘贴到您的项目中。

工具及其呈现方式在不同浏览器中有所不同。以下是主要浏览器中普遍存在的一些有用功能：

+   *元素面板*，在这里您可以查看和更改文档对象模型（DOM）和 CSS。

+   *控制台面板*，它突出显示加载资源（如 CSS、图片和其他媒体项）时出现的任何错误。

+   *网络和性能面板*，这些在不同浏览器中可能会有所不同。在 Chrome 中，您可以使用这些面板查看网页的加载情况，并找到提高页面性能和效率的机会。

每个浏览器都有自己的开发者工具文档，这些资料在您学习 CSS 知识时值得探索（表 A.2）。

表 A.2 浏览器开发者工具文档

| 浏览器 | URL |
| --- | --- |
| Chrome | [`mng.bz/N2d2`](http://mng.bz/N2d2) |
| Firefox | [`mng.bz/D489`](http://mng.bz/D489) |
| Safari | [`developer.apple.com/safari/tools`](https://developer.apple.com/safari/tools) |
| Edge | [`mng.bz/lWEM`](http://mng.bz/lWEM) |
