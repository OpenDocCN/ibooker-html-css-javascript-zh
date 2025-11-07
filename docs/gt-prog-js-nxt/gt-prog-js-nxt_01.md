## 第 2 课：使用 Babel 进行转译

当 JavaScript 添加新功能时，浏览器总是不得不玩一场追赶的游戏。在所有现代浏览器完全实现并支持 JavaScript 规范更新之后，需要一段时间。为了使用本书涵盖的所有功能，你将利用本节课介绍的技术：转译。

### 2.1\. 什么是转译？

*Transpile*是由*translate*和*compile*两个词组合而成的。编译器通常将一种编程语言的书面语言编译成难以理解的机器代码。转译器是一种特殊的编译器，它将一种编程语言的源代码转换为另一种编程语言的源代码。

> ¹
> 
> 至少对人类来说是这样的。

#### 2.1.1\. 编译到 JavaScript 语言

转译器已经存在一段时间了，但它们在 2009 年随着*CoffeeScript*（[`coffeescript.org`](http://coffeescript.org)）的引入而突然出现在 JavaScript 舞台上。CoffeeScript 是由 Jeremy Ashkenas 创建的编译到 JavaScript 的语言，他也是 Underscore 和 Backbone 等流行 JavaScript 库的创建者。它从 Ruby、Python 和 Haskell 中汲取了许多灵感，并专注于 Douglas Crockford 在其著作*JavaScript: The Good Parts*（O’Reilly Media，2008）中推广的 JavaScript 的“优点”。CoffeeScript 通过隐藏用户所说的 JavaScript 的许多缺点，只暴露更安全的部分来实现这一点。

然而，CoffeeScript 既不是 JavaScript 的子集，也不是超集。它暴露了新的语法和许多新概念，其中一些成为了 ES2015 中功能灵感的来源，例如箭头函数。随着 CoffeeScript 的成功，许多其他编译到 JavaScript 的语言开始出现，例如 ClojureScript、PureScript、TypeScript 和 Elm，仅举几个例子。

JavaScript 不一定是最适合编译到的语言，但为了在网络上运行代码，别无选择。最近宣布了一种名为*WebAssembly*（通常简称为*wasm)*的新技术。WebAssembly 承诺将成为前端开发中比 JavaScript 更好的编译目标，如果成功，将为在浏览器中选择运行的语言提供更多样化的途径。

#### 2.1.2\. Babel 的位置

到目前为止，你可能正在想，“转译器听起来很酷，但谁在乎呢？我正在读一本关于 JavaScript 的书，而不是编译到它的语言。”好吧，转译器不仅用于编译到 JavaScript 的语言。它们还可以帮助你编写 ESNext 代码，并在今天将其用于浏览器。想想看：当另一种语言编译到 JavaScript 时，它不仅针对 JavaScript，还针对 JavaScript 的特定版本。例如，CoffeeScript 针对 ES3。所以如果一种完全不同的语言可以被转译成 JavaScript 的特定版本，那么另一种版本的 JavaScript 不也应该能够做到吗？

有几种转换器可以将 ESNext JavaScript 转换为今天可以在浏览器中执行的合适版本。最常用的两种是*Traceur*和*Babel*。Babel 曾经被称为 ES6to5，因为它将 ES6 代码转换为 ES5 代码，但自从它开始支持所有未来的 JavaScript 特性，并且考虑到 ES6 的名称正式改为 ES2015，ES6to5 背后的团队决定将项目名称更改为 Babel。

### 2.2. 设置 Babel 6

Babel 作为 NPM([`www.npmjs.com/`](https://www.npmjs.com/))包提供，并随 Node.js([`nodejs.org/en/`](https://nodejs.org/en/))一起打包。您可以从他们的网站下载 Node.js 的安装程序。本书假设您已安装 Node.js 版本 4 或更高版本以及 NPM 版本 3 或更高版本。NPM 随 Node.js 一起打包，因此不需要单独安装。

为了使用 Babel，您需要设置一个 node.js 包，以便您可以安装所需的依赖项。安装了 Node.js 和 NPM 后，打开命令行程序（OSX 中的 Terminal .app 或 Windows 中的 cmd.exe）并执行以下 shell 命令以初始化一个新项目（确保将占位符`project_name`替换为您项目的名称）：^([1])

> ¹
> 
> 开头处的**$**表示这是一个要在命令行程序中执行的 shell 命令。**$**不是实际命令的一部分，不应输入。

```
$ mkdir project_name
$ cd project_name
$ npm init –y
```

让我们分解这个命令。`mkdir project_name`这一行将创建一个名为您提供的名称的新目录（文件夹）。然后`cd project_name`将切换到为新项目创建的新目录。最后，`npm init`将初始化它为一个新项目。`-y`标志告诉 NPM 跳过提问并使用所有默认值。

您现在应该能在项目中看到一个名为 package.json 的新文件，表明这是一个 Node.js 项目。现在您已经初始化了项目，可以设置 Babel。执行以下 shell 命令以安装 Babel 的命令行界面：^([2])

> ²
> 
> 在撰写本文时，Babel 的当前版本是 6.5.2。本书中的说明适用于 Babel 6.x 版本，这是从 5.x 版本的重大变化。您可以使用版本范围`>=6.0.0 <7.0.0`来约束 Babel 6 的某个版本，例如：`npm install babel@">=6.0.0 <7.0.0";`，请参阅[`docs.npmjs.com/cli/install`](https://docs.npmjs.com/cli/install)。

```
$ npm install babel-cli --save-dev
```

从版本 6 开始，Babel 默认不进行任何转换，您必须安装插件或预设才能应用任何转换。要使用插件或预设，您必须在项目中安装它并指定其在 Babel 配置中的使用。

Babel 使用一个名为 .babelrc 的特殊文件进行配置。你必须将此文件放在项目的根目录下，并且文件内容必须是有效的 JSON 格式。为了指定你希望 Babel 转译所有 ES2015 特性，你可以使用 ES2015 预设。编辑 .babelrc 文件，使其内容如下

```
{
  "presets": ["es2015"]
}
```

现在你已经告诉 Babel 使用 ES2015 预设，你必须也安装它：

```
$ npm install babel-preset-es2015 --save-dev
```

你现在应该准备好转译一些 ES6 代码了！测试一下。首先在你的项目中添加一个名为 src 的新文件夹，并在其中添加一个名为 index.js 的新文件。你的项目结构现在应该看起来像这样：

```
project_name
 src
    index.js
```

现在添加一些 ES2015 代码进行转译。将以下代码添加到你的 index.js 文件中：

```
let foo = "bar";
```

你现在可以告诉 Babel 转译你的源代码。在你的终端中，运行以下命令。

##### 列表 2.1. 从 src 文件夹编译到 dist 文件夹

```
$ babel src -d dist
```

运行此命令后，将创建一个名为 dist 的新目录，其中包含转译后的代码。让我们分解这个命令。当你指定 `babel src` 时，你是在告诉 Babel 对 src 目录的内容进行操作。默认情况下，Babel 将转译后的代码输出到终端。当你添加 `–d <directory_name>` 时，你可以告诉 Babel 将转译后的代码输出到一个目录中。

你的项目结构现在应该看起来像这样：

```
project_name
 dist
 index.js
 src
    index.js
```

dist/index.js 文件包含以下转译后的代码：

```
"use strict";

var foo = "bar";
```

### 2.3. 本书所需的 Babel 配置

TC39 的每个阶段都有一个预设。你可以包含任何五个阶段中的预设，Babel 将能够编译达到该阶段（或更高阶段）的代码。例如，如果你使用 stage-2 预设，你可以使用达到阶段 2、3 或 4 的特性，但不能使用阶段 0 或 1 的特性。

由于我无法预测你在阅读时每个提案将处于哪个阶段，请参考第一部分中的 TC39 阶段链接，以确定你需要哪些预设。

或者，你也可以使用以下 .babelrc 预设来获取所有阶段的内容。

##### 列表 2.2. Babel stage-0 预设

```
{
  "presets": ["es2015", "stage-0"],
  "plugins": ["transform-decorators-legacy"],
  "sourceMaps": "inline"
}
```

在列表 2.2 中，你使用了 ES2015 和 stage-0 预设来包含所有 ES2015 和现有的所有提案特性。你还需要包含 transform-decorators-legacy 插件来转译装饰器。最后，你告诉 Babel 包含内联源映射以简化调试。现在，为了使 Babel 能够使用这些插件和预设，你需要安装它们:^([1])

> ¹
> 
> 你可以一次性安装它们，而不是像我一样一个接一个地安装。我这样做是为了在书籍小边距中的可读性。

```
$ npm install babel-preset-es2015 --save-dev
$ npm install babel-preset-stage-0 --save-dev
$ npm install babel-plugin-transform-decorators-legacy --save-dev
```

#### 2.3.1. 关于源映射的说明

在你的 Babel 配置中，你添加了一个关于 *source maps* 的部分。如果你不熟悉 source maps，它们是一种为了使调试压缩代码更容易而发明的技术。大多数生产应用程序都带有压缩后的代码，以节省带宽，使应用程序加载更快。然而，这种压缩代码调试起来可能是一个噩梦，因此 source maps 被发明出来，可以将代码映射回其原始形式。编译到 JavaScript 的语言开始使用 source maps 来显示原始语言的源代码，而不是转换后的 JavaScript，Babel 也是如此。要了解更多关于 source maps 的信息，请参阅[`www.html5rocks.com/en/tutorials/developertools/sourcemaps/`](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/)。

#### 2.3.2\. 将 Babel 设置为 NPM 脚本

你可能不想反复告诉 Babel 从哪个文件夹转换到哪个文件夹（就像你在列表 2.1 中所做的那样）。你可以通过设置一个 NPM 脚本来简化你的生活。如果你不熟悉 NPM 脚本的工作方式，其实很简单。在你的 NPM 配置文件 package.json 中，有一个特殊的 scripts 部分，允许你指定可以通过名称执行的 shell 命令。有关 NPM 脚本的更多信息，请参阅[`docs.npmjs.com/misc/scripts`](https://docs.npmjs.com/misc/scripts)。

默认情况下，你的 package.json 文件中应该已经添加了一个测试脚本。如果你打开 package.json 文件并定位到 scripts 部分，它应该看起来像这样:^([1])

> ¹
> 
> 根据你的操作系统，它可能看起来不同。

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1"
},
```

你可以将我们的 Babel 命令作为脚本添加，如下所示：

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "babel": "babel src –d dist",
},
```

不要忘记在测试命令后添加逗号！现在你应该可以通过执行以下 shell 命令来执行此 NPM 脚本：

```
$ npm run babel
```

这不仅更简单、更容易记忆，而且随着你的需求变化，你可以修改你的 Babel 脚本，而你的命令始终保持不变。

### 摘要

在本课中，你学习了什么是转换编译以及如何使用它来开始使用 ESNext 功能。你还学习了如何设置 Babel 来转换本书中的代码。
