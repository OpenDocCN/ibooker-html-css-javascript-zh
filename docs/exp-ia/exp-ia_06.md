# 7  视图与模板：Jade 与 EJS

在前面的章节中，我们学习了 Express 是什么，Express 如何工作，以及如何使用它的路由功能。从本章开始，我们将停止学习 Express！

...好吧，好吧，不是完全这样。我们仍然会使用 Express 来驱动我们的应用程序，不用担心。但正如我们在本书前面讨论的那样，Express 是无偏见的，并且需要大量的第三方配件来制作一个完整的应用程序。在本章以及之后，我们将真正深入一些这些模块，学习它们是如何工作的，以及它们如何让你的应用程序变得可爱。

在本章中，我们将讨论视图。视图很棒。它们为我们提供了一个方便的方式来动态生成内容（通常是 HTML）。我们之前已经见过一个视图引擎；EJS 帮助我们将特殊变量注入到 HTML 中。但尽管 EJS 给了我们视图的概念性理解，我们从未真正探索过 Express（以及其他视图引擎）所能提供的一切。我们将学习将值注入模板的多种方法，了解 EJS、Jade 以及其他与 Express 兼容的视图引擎的功能，以及视图世界中的某些微妙之处。

让我们开始吧。

## 7.1     Express 的视图功能

在我开始之前，让我定义一个我将大量使用的术语：视图引擎。当我提到“视图引擎”时，我基本上是指“执行视图实际渲染的模块”。

Jade 和 EJS 是视图引擎，还有很多其他的。

美国歌手兼词曲作者 India.Arie 有首名为 "Brown Skin" 的优秀歌曲。关于棕色皮肤，她唱道：“我分不清你的开始，也分不清我的结束”。同样，当我第一次开始使用 Express 视图时，我很困惑 Express 和视图引擎的界限在哪里。幸运的是，这并不太难！

Express 对你使用的视图引擎没有意见。只要视图引擎暴露了 Express 期望的 API，你就可以开始了。Express 提供了一个便利函数来帮助你渲染视图；让我们看看。

### 7.1.1  简单的视图渲染

我们之前已经看到了如何渲染视图的简单示例，但以防万一，这里有一个渲染简单 EJS 视图的示例应用：

列表 7.1 简单视图渲染示例

`var express = require("express");  #A` `var path = require("path");  #A` `var app = express();  #A` `app.set("view engine", "ejs");  #B` `app.set("views", path.resolve(__dirname, "views"));  #C` `app.get("/", function(req, res) { #D` `    res.render("index");  }); ` `app.listen(3000);  #E`

#A 首先，我们需要引入所需的模块并创建我们的应用程序。

#B 这告诉 Express，任何以 ".ejs" 结尾的文件都应该使用 require("ejs") 输出的内容进行渲染。一些视图引擎遵循这个约定；我们稍后会看到如何使用不遵循此约定的视图引擎。

#C 这告诉 Express 你的视图目录在哪里。它默认是这样的，但我更喜欢明确指出。这也确保了在 Windows 上也能正常工作。

#D 当我们访问根目录时，我们将渲染一个名为 "index" 的文件。这解析为 "views/index.ejs"，然后使用 EJS 进行渲染。

#E 在端口 3000 上启动服务器！

一旦你完成了 EJS（以及当然 Express）的 `npm install`，这应该就能工作！当你访问根目录时，它会找到 `views/index.ejs` 并使用 EJS 进行渲染！99% 的时间，你会做类似的事情；一直使用一个视图引擎。但如果决定混合使用，事情可能会变得更加复杂。

### 7.1.2  复杂的视图渲染

这里是一个从响应中渲染视图的复杂示例，使用了两种不同的视图引擎：Jade 和 EJS。这应该能说明这可能会变得多么疯狂：

列表 7.3 复杂渲染示例

`var express = require("express");` `var path = require("path");` `var ejs = require("ejs");` `var app = express();` `app.locals.appName = "Song Lyrics";` `app.set("view engine", "jade");` `app.set("views", path.resolve(__dirname, "views"));` `app.engine("html", ejs.renderFile);` `app.use(function(req, res, next) {` `  res.locals.userAgent = req.headers["user-agent"];` `  next();` `});` `app.get("/about", function(req, res) {` `  res.render("about", {` `    currentUser: "india-arie123"` `  });` `});` `app.get("/contact", function(req, res) {` `  res.render("contact.ejs");` `});` `app.use(function(req, res) {` `  res.status(404);` `  res.render("404.html", {` `    urlAttempted: req.url` `  });` `});` `app.listen(3000);`

当你在这三种情况下调用 `render` 时，会发生以下情况。虽然从高层次来看看起来很复杂，但实际上只是几个简单的步骤：

1.  每次调用 render 时，Express 都会构建上下文对象。当需要渲染时，这些上下文对象将被传递给视图引擎。这些实际上是视图可用的变量。

它首先添加来自 `app.locals` 的所有属性，这是一个对每个请求都可用的对象。然后它添加 `res.locals` 中的所有属性，如果存在，将覆盖来自 `app.locals` 的任何添加。最后，它添加传递给 `render` 的对象的属性（再次覆盖之前添加的任何属性）。最终，如果我们访问 `/about`，我们将创建一个包含三个属性的上下文对象：`appName`、`userAgent` 和 `currentUser`。`/contact` 将只有 `appName` 和 `userAgent` 在其上下文中，而 404 处理程序将具有 `appName`、`userAgent` 和 `urlAttempted`。

2.  接下来，我们决定是否启用视图缓存。"视图缓存"可能听起来像 Express 缓存整个视图渲染过程，但它并不这样做；它只缓存视图文件的查找并将其分配给适当的视图引擎。例如，它会缓存 `views/my_view.ejs` 的查找，并确定这个视图使用 EJS，但它不会缓存视图的实际渲染。有点误导！

它以两种方式决定是否启用视图缓存，其中只有一种是文档化的。

记录的方法：你可以在应用程序上设置一个选项。如果`app.enabled("view cache")`是布尔值，Express 将缓存视图的查找。默认情况下，在开发模式下禁用此功能，在生产模式下启用，但你可以通过`app.enable("view cache")`或`app.disable("view cache")`来自定义更改。

未记录的方法：如果前一步生成的上下文对象有一个名为`cache`的布尔属性，则将为该视图启用缓存。这会覆盖任何应用程序设置。这使你能够按视图逐个缓存，但我认为更重要的是知道它的存在，这样你就可以避免无意中这样做！

3.  接下来，我们必须查找视图文件所在的位置以及要使用哪个视图引擎。在这种情况下，我们希望将“about”转换为`/path/to/my/app/views/about.jade` + Jade，将“contact.ejs”转换为`/path/to/my/app/views/contact.ejs` + EJS。404 处理程序应该通过查看我们之前对`app.engine`的调用，将`404.html`与 EJS 关联。如果我们之前已经执行过此查找并且启用了视图缓存，我们将从缓存中提取并跳到最后一步。如果没有，我们将继续执行。

4.  如果你没有提供文件扩展名（例如，“about”），Express 会附加你指定的默认值。在这种情况下，“about”变为“about.jade”，但“contact.ejs”和“404.html”保持不变。如果你没有提供扩展名且没有提供默认视图引擎，Express 将抛出错误。否则，它将继续执行。

5.  现在我们确实有一个文件扩展名，Express 将查看扩展名以确定要使用哪个引擎。如果它与任何你已指定的引擎匹配，它将使用那个。在这种情况下，它将匹配 Jade 对`about.jade`，因为它默认如此。`contact.ejs`将根据文件扩展名尝试`require("ejs")`。我们明确地将`404.html`分配给 EJS 的`renderFile`函数，所以它将使用那个。

6.  Express 在视图目录中查找文件。如果没有找到文件，它将抛出错误，但如果找到了，它将继续执行。

7.  如果启用了视图缓存，我们将为下一次调用缓存所有这些查找逻辑。

8.  最后，我们渲染视图！这会调用视图引擎，在 Express 的源代码中实际上只是一行。这就是视图引擎接管并生成实际 HTML（或你想要的任何内容）的地方。

这可能有点棘手，但 99%的情况是“我选择一个视图引擎并坚持下去”，所以你很可能被大多数这种复杂性所屏蔽。

渲染非 HTML 视图

Express 的默认内容类型是 HTML，所以如果你不做任何特殊处理，res.render 将渲染你的响应并将它们作为 HTML 发送到客户端。大多数时候，我发现这已经足够了。但不必非得这样！你可以渲染纯文本、XML、JSON 或任何你想要的内容。只需通过更改参数 res.type 来更改内容类型：

`app.get("/", function(req, res) {` `  res.type("text");` `  res.render("myview", {` `    currentUser: "Gilligan"` `  });` `});`

经常有一些更好的方法来渲染这些事物——例如，应该使用 `res.json` 而不是渲染 JSON 的视图——但这个选项完全可用！

### 7.1.3  使所有视图引擎与 Express 兼容：Consolidate.js

我们已经讨论了一些视图引擎，如 EJS 和 Jade，但还有很多其他你可能想要选择的。你可能听说过 Mustache、Handlebars 或 Underscore.js 的模板。你也可能想要使用其他模板语言的 Node.js 版本，如 Jinja2 或 HAML。

许多这些视图引擎将与 Express “直接工作”，如 EJS 和 Jade。然而，其他一些没有 Express 兼容的 API，需要被包裹在 Express 可以理解的某些东西中。

进入 Consolidate.js（在 [`github.com/tj/consolidate.js`](https://github.com/visionmedia/consolidate.js)），这是一个封装了大量视图引擎以与 Express 兼容的库。它支持经典如 EJS、Jade、Mustache、Handlebars 和 Hogan。它还支持许多其他引擎，以防你使用更不常见/另类视图引擎。你可以在项目的页面上查看支持的所有引擎列表。

例如，假设你正在使用 Walrus，这是一个与 Express 默认不兼容的 JavaScript 视图引擎。我们需要使用 Consolidate 来使其与 Express 兼容。

安装 Walrus 和 Consolidate（使用 `npm install walrus consolidate`）后，你将能够使用 Walrus 与 Express 一起使用！

列表 7.4 使用 Walrus 渲染

`var express = require("express");` `var engines = require("consolidate"); #A` `var path = require("path");` `var app = express();` `app.set("view engine", "wal"); #B` `app.engine("wal", engines.walrus); #C` `app.set("views", path.resolve(__dirname, "views")); #D` `app.get("/", function(req, res) {` `   res.render("index"); });` `app.listen(3000);`

#A 首先，我们必须引入 Consolidate 库。为了可读性，我们将其放置在一个名为 "engines" 的变量中。

#B 接下来，我们指定 .wal 文件作为我们的默认视图文件扩展名。

#C 在这里，我们将 .wal 文件与 Walrus 视图引擎关联。

#D 如同往常，我们指定我们的视图目录。

#E 最后，我们渲染视图！这将渲染 views/index.wal。

我建议使用 Consolidate 而不是自己尝试处理不兼容的视图引擎。

## 7.2     关于 EJS 的所有你需要知道的内容

目前最简单且最受欢迎的视图引擎之一被称为 EJS，代表“嵌入式 JavaScript”。它可以对简单字符串、HTML、纯文本等进行模板化——你叫它什么，它就轻量级地与任何你使用的工具集成。它在浏览器和 Node.js 中工作。如果你曾经使用过 Ruby 世界中的 ERB，EJS 非常相似。无论如何，它非常简单！

目前有 EJS 的两个版本 实际上，有两个不同的群体维护着两个版本的 EJS。它们很相似，但并不完全相同。我们将使用的是由 Express 的创建者 TJ Holowaychuck 维护的版本。如果你在 npm 上寻找名为 "ejs" 的包，这就是你将找到的版本。但如果你访问 http://embeddedjs.com/，你会找到一个非常相似的库，它也声称有相同的名字。许多功能都是相同的，但它是一个在 2009 年最后更新的不同库。它不适用于 Node，甚至在它的文档中还有一些有争议的性别歧视性句子；请避免使用它！

### 7.2.1 EJS 的语法

EJS 可以用于 HTML 模板，但它可以用于任何东西。让我们看看一段简短的 EJS 代码，以及当你渲染它时它看起来像什么。

列表 7.5 一个 EJS 模板

`Hi <%= name %>!` `You were born in <%= birthyear %>, so that means you're[CA]` `<%= (new Date()).getFullYear() - birthyear %> years old.` `<% if (career) { -%>` `  <%=: career | capitalize %> is a cool career!` `<% } else { -%>` `  Haven't started a career yet? That's cool.` `<% } -%>``Oh, let's read your bio: <%- bio %> See you later!`

如果我们将以下上下文传递给 EJS...

列表 7.6 一个 EJS 上下文

`{` `  name: "Tony Hawk",` `  birthyear: 1968,` `  career: "skateboarding",` `  bio: "<b>Tony Hawk</b> is the coolest skateboarder around."``}`

然后，我们会得到以下结果（至少在 2015 年是这样的）：

`Hi Tony Hawk!` `You were born in 1968, so that means you're 47 years old.` `Skateboarding is a cool career!``Oh, let's read your bio: Tony Hawk is the coolest skateboarder around. See you later!`

这个小例子展示了 EJS 的四个主要功能：评估、转义和打印的 JavaScript，评估但不打印的 JavaScript，评估并打印（但不转义 HTML）的 JavaScript，以及过滤器。

你可以通过两种方式打印 JavaScript 表达式的结果，正如我们所看到的。`<% expression %>` 打印表达式的结果，而 `<%= expression %>` 打印表达式的结果，并转义任何可能存在的 HTML 实体。一般来说，当你能使用时，我建议使用后者，因为它更安全。

你还可以运行任意 JavaScript 并防止它被打印。这对于像循环和条件语句这样的东西很有用，正如我们在上面的例子中所看到的。这是通过 `<% expression %>` 来实现的。正如你所看到的，你可以使用括号来在多行中分组循环和条件语句。你还可以使用 `<% expression -%>` 来避免添加额外的换行（注意结尾的连字符）。

最后，在输出后添加一个冒号（`:`）将允许应用过滤器。过滤器会过滤表达式的输出并改变输出。在上面的例子中，我们使用了大写字母过滤器，但还有很多其他的过滤器，你也可以定义自己的过滤器（正如我们马上就会看到的！）。

注意：如果您想尝试 EJS，我制作了一个“尝试 EJS”的简单浏览器应用（在 https://evanhahn.github.io/try-EJS/），可以在浏览器中玩 EJS。我承认它不够完善，但足以在浏览器中尝试 EJS 并查看渲染输出。

在您的 EJS 模板中包含其他 EJS 模板

EJS 还允许您包含其他 EJS 模板。这非常有用，原因有很多。您可以为页面添加页眉和页脚，分离常见的 widget，等等！如果您发现自己多次编写相同的代码，可能就是时候使用 EJS 的 include 功能了。

让我们看看两个例子。

首先，让我们想象您有一些共享相同页眉和页脚的页面。而不是一遍又一遍地重复所有内容，您可以创建一个页眉 EJS 文件，一个页脚 EJS 文件，然后是位于页眉和页脚之间的页面。

下面是一个页眉文件（保存为 `header.ejs`）可能的样子：

列表 7.7 标题 EJS 文件

`<!DOCTYPE html>` `<html>` `<head>` `  <meta charset="utf-8">` `  <link rel="stylesheet" href="/the.css">` `  <title><%= appTitle %>/title>` `</head>` `<body>` `  <header>` `    <h1><%= appTitle %>``  </header>`

然后，您会在 footer.ejs 中定义页脚：

列表 7.8 页脚 EJS 文件

`<footer>` `  All content copyright <%= new Date().getFullYear() %> <%= appName %>.` `</footer>` `</body>``</html>`

现在您已经定义了页眉和页脚，您可以在子页面上轻松地包含它们！

列表 7.9 从 EJS 包含页眉和页脚

`<% include header %>` `  <h1>欢迎来到我的页面！</h1>` `  <p>这是一个相当酷的页面，我必须说。</p>` `<% include footer %>`

我们使用 include 来，嗯，包含其他 EJS 文件。请注意，我们不使用 `<%= ... %>` 或 `<%- ... %>`；最终所有内容都是由 EJS 打印出来的，而不是你。

我们也可以想象自己使用它来构建小部件。例如，假设我们有一个显示用户资料的 widget。给定一个名为 user 的对象，这个模板会为该用户输出一些 HTML。以下是 `userwidget.ejs` 可能的样子：

列表 7.10 用户小部件在 userwidget.ejs 中

`<div class="user-widget">` `  <img src="<%= user.profilePicture %>">` `  <div class="user-name"><%= user.name %></div>` `  <div class="user-bio"><%= user.bio %></div>``</div>`

现在，我们可以在渲染当前用户时使用该模板...

列表 7.11 渲染当前用户的用户小部件

`<% user = currentUser %>` `<% include userwidget %>`

...或者当渲染用户列表时。

列表 7.12 多次渲染用户小部件

`<% userList.forEach(function(user) { %>` `  <% include userwidget %>``<% } %>`

EJS 的 include 功能非常灵活；它可以用来创建模板或多次渲染子视图。

添加您自己的过滤器

有 22 个内置过滤器，从数学运算到数组/字符串反转到排序。它们通常足以满足您的需求，但有时您可能需要添加自己的。

假设你已经将 EJS 引入到一个名为 `ejs` 的变量中，你只需向 `ejs.filters` 添加一个属性即可。如果我们经常计算数组之和，我们可能会发现创建自己的自定义“数组求和”过滤器很有用。

下面是我们可能添加此类过滤器的示例：

列表 7.13 添加 EJS 过滤器以计算数组之和

`ejs.filters.sum = function(arr) {` `  var result = 0;` `  for (var i = 0; i < arr.length; i ++) {` `    result += arr[i];` `  }` `  return result;``};`

现在，你可以像使用任何其他过滤器一样使用它了！

列表 7.14 使用我们新的 EJS 求和过滤器

`<%=: myarray | sum %>`

非常简单！你可以想象出很多过滤器——按需编写它们！

## 7.3  关于 Jade 你需要知道的一切

视图引擎如 Handlebars、Mustache 和 EJS 并不完全取代 HTML——它们通过一些新特性来增强它。如果你有设计师，例如，他们已经学会了 HTML 而不想学习一门全新的语言，这会非常不错。对于非 HTML 类的模板解决方案来说，这也很有用。如果你处于这种情况下，Jade 可能不是最佳选择。

但 Jade 也承诺了一些其他特性。它允许你编写更少的代码行，而且你写的代码行看起来更漂亮。文档类型很容易；标签通过缩进来嵌套，而不是通过闭合标签。它内置了多个 EJS 风格的特性，如条件和循环。学习起来更多，但功能更强大。

### 7.3.1  Jade 的语法

类似于 HTML 的语言是嵌套的。有一个根元素 (`<html>`)，然后是各种子元素（如 `<head>` 和 `<body>`），每个子元素都有自己的子元素……以此类推。HTML 和 XML 选择为每个元素提供一个打开的 (`<a>`) 和一个关闭的 (`</a>`) 标签。

Jade 通过使用缩进和不同的 HTML 语法采取了不同的方法。以下是一个使用 Jade 的非常简单的网页示例：

列表 7.15 一个简单的 Jade 示例

`doctype html` `html(lang="en")  #A` `  head` `    title Hello world!` `  body` `    h1 This is a Jade example` `    #container   #B``      p Wow.`

#A 将属性添加到元素看起来像函数调用。（如果你熟悉 Python 中的关键字方法调用，它们看起来非常像！）

#B 没有指定元素，所以这是一个 div。

这将转换为以下 HTML：

列表 7.16 列表 7.15 渲染为 HTML

`<!DOCTYPE html>` `<html lang="en">` `  <head>` `    <title>Hello world!</title>` `  </head>` `  <body>` `    <h1>This is a Jade example</h1>` `    <div id="container">` `      <p>Wow.</p>` `    </div>` `  </body>``</html>`

你可以在项目的首页上尝试 Jade [`jade-lang.com/`](http://jade-lang.com/)——尝试实验看看会发生什么！

### 7.3.2  Jade 中的布局

布局是任何模板语言的一个重要特性。它们允许你以某种形式包含其他 HTML。这允许你一次性定义你的页眉和页脚，然后在需要它们的地方包含它们。

一个非常常见的用例是为你的页面定义一个布局文件。这样，所有页面都可以有一个一致的页眉和页脚，同时允许内容按页面变化。

首先，我们定义 "master" 布局。这是每个页面都通用的 Jade，如页眉和页脚。这个主布局定义了空块，这些块将被使用此主布局的任何页面填充。让我们看看一个例子。

首先，让我们定义一个简单的布局文件。这个文件将由我们所有的页面共享。

列表 7.15 Jade 的简单布局文件

`doctype html` `html`   `  head` `     meta(charset="utf-8")` `     title Cute Animals website` `     link(rel="stylesheet" href="the.css")`   `     block header  #A`   `  body`   `     h1 Cute Animals website`  `     block body  #B`

#A 在父布局文件中，我们定义了一个 "header" 块和 "body" 块。这些将被扩展此布局的任何人使用。

注意我们如何定义了两个带有 block header 和 block body 的块。这些将被使用此布局的 Jade 文件填充。将此文件保存到 layout.jade。我们可以在使用此布局的 "真实" 页面中使用这些，如下所示：

列表 7.16 使用 Jade 布局文件

`extends layout.jade` `block body``  p Welcome to my cute animals page!`

这将渲染以下 HTML：

列表 7.17 使用 Jade 布局输出的结果

`<!DOCTYPE html>` `<html>` `  <head>` `    <meta charset="utf-8">` `    <title>Cute Animals website</title>` `    <link rel="stylesheet" href="the.css">` `  </head>` `  <body>` `    <h1>Cute Animals website</h1>` `    <p>Welcome to my cute animals page!</p>` `  </body>``</html>`

注意，当我们扩展布局时，我们只需在块中放入一些内容，它就会神奇地插入！也请注意，我们不需要使用块，即使它被定义了——我们从未触摸页眉块，因为我们不需要。

如果我们愿意，可以非常容易地定义另一个使用此布局的页面。

列表 7.18 再次使用 Jade 布局文件

`extends layout.jade` `block body` `  p This is another page using the layout.` `  img(src="cute_dog.jpg" alt="A cute dog!")``  p Isn't that a cute dog!`

布局让我们分离出常见的组件，这意味着我们不必一遍又一遍地重复相同的代码。

### 7.3.3 在 Jade 中的混合

Jade 有另一个酷炫的功能叫做混合。混合基本上是你定义在 Jade 文件中的函数，用于减少重复性任务。

让我们重新实现 EJS 部分的用户小部件示例。我们将创建一个接受一个名为 user 的对象的 widget，并返回该用户的 HTML widget。以下是我们可以这样做的方式：

列表 7.19 用户小部件混合

`mixin user-widget(user)` `  .user-widget` `    img(src=user.profilePicture)` `    .user-name= user.name` `    .user-bio= user.bio`   `//- Render the user widget for the current user` `+user-widget(currentUser)`   `//- Render the user widget for a bunch of users` `- each user in userList``  +user-widget(user)`

这将渲染 `currentUser` 的用户小部件以及 `userList` 中其他每个用户的用户小部件。我们不需要重复的代码！

这就是我们今天要讨论的 Jade。有关 Jade 语法更详细的内容，你可以查看 Jade 的参考文档，链接为 [`jade-lang.com/reference/`](http://jade-lang.com/reference/).

## 7.4    概述

在本章中，我们学习了以下内容：

· Express 的视图系统。我们学习了如何将变量传递给视图以动态生成 HTML，以及我们学习了视图引擎是如何工作的。

· EJS 模板语言，用一点 JavaScript 动态生成 HTML。

· Jade 模板语言，用全新的语言重新构想 HTML 并动态生成它。
