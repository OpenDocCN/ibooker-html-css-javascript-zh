## 第 3 课：使用 Browserify 打包模块

模块是 JavaScript 新增功能的重要组成部分。正如你将在本课中学到的，仅进行转译对于模块来说是不够的。这是因为模块最定义性的方面是将你的代码分割成单独的文件。这意味着你需要将它们*打包*成一个文件。有几种流行的工具可以用于打包 JavaScript 模块；两个流行的上升选项是 Webpack 和 Rollup。在本课中，你将使用最早出现的其中之一，Browserify。

### 3.1. 什么是模块？

许多编程语言都支持模块化代码。Ruby 将这些模块化代码称为*gem*，Python 称为*egg*，Java 称为*packages*。JavaScript 直到 ES2015 引入*模块*概念之前都没有官方支持这一概念。模块是单个模块化 JavaScript 代码文件。由于 JavaScript 在这个领域来得太晚，因此为 JavaScript 中的模块创建了多种社区解决方案，其中最普遍的是 node.js 模块。

### 3.2. Node.js 中的模块是如何工作的

Node.js 拥有一个出色的模块系统，配合 NPM 使用。NPM 是随 Node.js 一起打包的 Node 包管理器。大约有 25 万个包发布到 NPM 的注册库，每月下载量达数十亿次，NPM 是世界上代码生态系统最丰富的之一。

在 Node.js 中有一种特定的方式来导入和导出模块。当提到 Node.js 风格的模块时，许多人使用*CommonJS*这个术语，这是一个最初被称为 ServerJS 的规范，它被创建出来是为了让许多服务器端 JavaScript 实现能够共享一个兼容的模块定义。只有一个服务器端 JavaScript 实现流行起来——Node.js——因此没有必要标准化它们的模块定义。所以尽管 Node 的模块定义与 CommonJS 相似，并且经常被称为 CommonJS，但严格来说并不是。

Node.js 中的模块系统允许开发者将程序分割成封装逻辑并仅暴露必要 API 的模块。由于模块只暴露显式导出的内容，因此没有必要将所有内容包裹在一个立即执行的函数表达式中。更好的是，因为模块仅在导入的地方可用，它们不会污染全局命名空间，从而防止意外的命名冲突。

### 3.3. 什么是 Browserify？

*Browserify*是一个工具，它允许你以与 Node.js 在开发期间相同的方式定义模块，然后将它们打包成一个单独的文件。Browserify 在入口点操作，即你的主 JavaScript 文件，并分析哪些脚本被导入。然后它还会运行所有这些脚本，最终构建一个所需所有依赖项的树。Browserify 随后生成一个包含所有所需模块的单个 JavaScript 文件，同时保持它们适当的范围和命名空间。这个打包的 JavaScript 文件可以随后被包含在前端网页中。这允许前端开发者编写模块化的 JavaScript，甚至利用所有发布到 NPM 的丰富生态系统。Browserify 之所以得名，是因为它能够以 Node.js 风格编写代码并使用浏览器中的 Node.js 模块。

### 3.4. Browserify 是如何帮助处理 ES6 模块的？

你在第 2 课中学到了 Babel 将你的 ESNext 代码转换为可以在浏览器中执行的形式。但是 Babel 并不提供模块系统。它只是将你的 ESNext 源代码转换为 ES5 目标代码，并留给开发者解决打包的任务。另一方面，ES2015 确实为 JavaScript 定义了一个正式的模块规范。那么，如果 Babel 没有暴露模块加载器，你今天如何使用 ES2015 模块呢？如果你能让 Browserify 和 Babel 一起工作，使得 Babel 能够将 ES2015 模块转换为 Browserify 喜欢处理的模块类型，然后 Browserify 继续处理，会怎么样呢？幸运的是，Browserify 有*转换*的概念。转换允许在代码被 Browserify 操作之前对其进行转换。有一个名为*babelify*的 Babel 转换器。当你使用 babelify 时，每个文件在发送到 Browserify 之前都会被转换，这样你就可以使用 ES2015 模块。

### 3.5. 使用 Babel 设置 Browserify

现在你已经了解了 Browserify 是什么以及它所扮演的角色，让我们来安装它。

#### 3.5.1. 安装 Browserify

首先全局安装 Browserify。执行以下 shell 命令：

```
$ npm install browserify --global
```

这将在全局范围内安装 Browserify，因此它可以用于任何项目。除非你想升级到新版本，否则你不需要再次安装它。

#### 3.5.2. 使用 babelify 设置项目

现在你已经安装了 Browserify，你可以通过创建一个名为 babelify_example 的新项目来开始。创建一个名为 babelify_example 的新文件夹，包含一个.babelrc 文件、一个 dist 文件夹和一个 src 文件夹，其中 src 文件夹包含一个 index.js 和一个 app.js，这样你的项目结构看起来就像这样：

```
babelify_example
 dist
 src

 app.js
 index.js
 .babelrc
```

现在，在你的终端中，cd（更改目录）到你的项目根文件夹，使用 babelify 以及你在上一章中使用过的其他 Babel 预设和插件初始化为一个 NPM 项目：

```
$ cd babelify_example
$ npm init -y
$ npm install babelify --save-dev
$ npm install babel-preset-es2015 --save-dev
$ npm install babel-preset-stage-0 --save-dev
$ npm install babel-plugin-transform-decorators-legacy --save-dev
```

注意到你没有安装 babel-cli 包。这是因为你现在正在使用 Browserify 和 babelify，因此不再需要 Babel CLI（命令行界面）。继续添加你之前课程中的相同 Babel 配置到.babelrc 文件中：

```
{
  "presets": ["es2015", "stage-0"],
  "plugins": ["transform-decorators-legacy"],
  "sourceMaps": "inline"
}
```

好的，你现在应该已经准备好开始使用 Browserify 和 babelify 了！通过编写一个小模块来测试它。在 app.js 文件中添加以下代码：

```
const MyModule = {
  check() {
    console.log('Yahoo! modules are working!!');
  }

}

export default MyModule;
```

现在在 index.js 文件中，添加以下代码：

```
import MyModule from './app';

MyModule.check();
```

太棒了。现在使用以下 shell 命令打包：

```
$ browserify src/index.js --transform babelify --outfile dist/bundle.js
--debug
```

如果一切设置正确，你的 dist 文件夹现在应该包含一个包含一些转换后的 JavaScript 代码的新 bundle.js 文件。让我们分解这个命令。Browserify 的第一个参数是`src/index.js`。这告诉 Browserify 这是你的应用程序的*入口点*——入口点意味着导入其他模块的根 JavaScript 文件。然后`--transform babelify`告诉 Browserify 在打包之前使用 babelify 转换来转换你的代码。`--outfile dist/bundle.js`指定了打包和转换后的源代码的*输出文件*。最后，`--debug`标志是必要的，以包含源映射，没有它则不会包含。

你可以通过运行以下命令来查看 Browserify 可用的参数列表：

```
$ browserify help
```

现在使用 Node.js 测试你的代码。如果你以前从未使用 Node.js 执行 JavaScript，不要担心。你已经有它安装了，告诉它执行 JavaScript 就像指向一个 JavaScript 文件一样简单。所以告诉 Node 执行你的转换后的 bundle.js 文件，通过执行以下 shell 脚本：

```
$ node dist/bundle.js
```

你应该会收到热情的

```
Yahoo! modules are working!!
```

不要担心现在就理解模块的工作语义。我们将在第 20 课和第 21 课中介绍。现在，先在你的浏览器中让你的打包工作起来，而不是在 Node 中。在你的项目根目录中创建一个 index.html 文件，内容如下：

```
<!DOCTYPE html>
<html>
<head>
  <title>Babelify Example</title>
</head>
<body>
  <h1>Hello, ES6!</h1>

  <script src="dist/bundle.js"></script>
</body>
</html>
```

现在在你的网络浏览器中打开你的 index.html。检查控制台；如果使用 Google Chrome，请选择菜单 > 更多工具 > 开发者工具，然后选择控制台标签。你应该在你的控制台中看到相同的引用：*Yahoo! 模块正在工作！！*

让我们回顾一下你到目前为止所做的一切：

1.  你在 app.js 中创建了一个具有`check`方法的模块来记录消息。

1.  在你的 index.js 中，你导入了模块并调用了`check`方法。

1.  你使用了 Browserify 和 Babel 来转换和打包你的 JavaScript。

1.  然后你在 HTML 页面中包含了你的打包代码，并看到它工作得很好！

这包括了执行本书中所有代码所需的所有步骤。接下来的章节将假设你已经设置好并且能够执行你的示例。

每当你修改源文件时，不要忘记重新编译（通过执行 `browserify` 命令），以确保你的 bundle.js 反映你最新的代码。（了解 watchify 以实现自动捆绑。）你可以在 package.json 中添加 Browserify 壳命令作为 NPM 脚本，以便更容易运行。

### 3.6\. Browserify 的替代方案

有许多其他方法可以将你的 ESNext 代码进行转译和捆绑。Webpack 和 Rollup 目前是非常受欢迎的选项。哪个最适合你的项目将很大程度上取决于你项目的细节。Babel 为不同的场景提供了良好的设置示例，你可以在这里查看：[`babeljs.io/docs/setup`](http://babeljs.io/docs/setup)。

### 摘要

在本节课中，你学习了如何设置 Browserify 以捆绑你的 ES2015 模块。有关模块的更多信息，请参阅第二十部分–第二十一部分。
