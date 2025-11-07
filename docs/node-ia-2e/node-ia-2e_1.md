## 第二部分\. 使用 Node 进行 Web 开发

现在，你已经准备好深入了解后端开发了。Node 在服务器端代码之外找到了一个重要的细分市场：前端构建系统。在本部分中，你将学习如何使用 webpack 和 Gulp 开始项目。我们还将介绍最受欢迎的 Web 框架，并从多个开发者的角度进行比较，以帮助你决定适合你项目的最佳框架。

如果你想要详细了解 Connect 和 Express，第六章完全致力于使用这些模块构建 Web 应用程序。还有一个章节专门介绍模板和 Node 数据库的使用。

为了完成使用 Node 进行全栈 Web 开发的旅程，我们包括了测试和部署章节，这样你就可以准备你的第一个 Node 应用程序。

## 第四章\. 前端构建系统

*本章涵盖*

+   使用 npm 脚本简化复杂命令

+   使用 Gulp 管理重复性任务

+   使用 webpack 打包客户端 Web 应用

在现代 Web 开发中，Node 越来越多地被用来运行前端工程师依赖的工具和服务。作为一个 Node 程序员，你可能负责设置和维护这些工具。作为一个全栈开发者，你将希望使用这些工具来创建更快、更可靠的 Web 应用程序。在本章中，你将学习如何使用 npm 脚本、Gulp 和 webpack 构建可维护的项目。

使用前端构建系统的益处可能很大。它们可以帮助你编写更易读和面向未来的代码。当你可以用 Babel 进行转译时，无需担心 ES2015 浏览器支持问题。此外，因为你可以生成源映射，基于浏览器的调试仍然可行。

下一节简要介绍了使用 Node 进行前端开发。之后，你将看到一些现代前端技术，如 React 的示例，你可以将其用于自己的项目。

### 4.1\. 使用 Node 理解前端开发

最近，前端和后端开发者已经转向使用 npm 来分发 JavaScript。这意味着 npm 被用于前端模块，如 React，以及后端代码，如 Express。但有些模块并不完全属于任何一方：lodash 就是一个可以在 Node 和浏览器中使用的通用库的例子。通过精心打包 lodash，相同的模块可以被 Node 和浏览器使用，并且可以在项目中使用 npm 管理依赖项。

你可能见过其他专门针对客户端开发的模块系统，例如 Bower ([`bower.io/`](http://bower.io/))。你仍然可以使用这些工具，但作为一个 Node 开发者，你应该考虑使用 npm。

Node 不仅仅用于包分发。前端开发者也越来越依赖于生成可移植、向后兼容的 JavaScript 的工具。转换器，如 Babel ([`babeljs.io/`](https://babeljs.io/))，用于将现代 ES2015 转换为更广泛支持的 ES5 代码。其他工具包括压缩器（例如，UglifyJS；[`github.com/mishoo/UglifyJS`](https://github.com/mishoo/UglifyJS)）和代码检查器（例如，ESLint，[`eslint.org/`](http://eslint.org/)），用于在发货前验证代码的正确性。

测试运行器也通常由 Node 驱动。您可以在 Node 进程中运行 UI 代码的测试，或者使用 Node 脚本驱动在浏览器中运行的测试。

同时使用这些工具也很典型。当您开始处理转换器、压缩器、代码检查器和测试运行器时，您需要记录构建过程的工作方式。一些项目使用 npm 脚本；其他项目使用 Gulp 或 webpack。您将在本章中查看所有这些方法，并了解一些相关的最佳实践。

### 4.2\. 使用 npm 运行脚本

Node 自带 npm，npm 具有运行脚本的内置功能。因此，您可以依赖您的合作者或用户能够调用`npm start`和`npm test`等命令。要为 npm start 添加自己的命令，您需要将其添加到项目 package.json 文件的`scripts`属性中：

```
{
  ...
  "scripts": {
    "start": "node server.js"
  },
  ...
}
```

即使您没有定义`start`，`node server.js`也是默认的，所以技术上您可以留空，如果这就是您需要的所有内容——只需记住创建一个名为 server.js 的文件。定义`test`属性很有用，因为您可以将测试框架作为依赖项包含在内，并通过输入`npm test`来运行它。假设您正在使用 Mocha ([www.npmjs.com/package/mocha](http://www.npmjs.com/package/mocha)) 进行测试，并且您已使用`npm install --save-dev`安装了它。为了避免全局安装 Mocha，您可以将以下语句添加到您的 package.json 文件中：

```
{
  ...
  "scripts": {
    "test": "./node_modules/.bin/mocha test/*.js"
  },
  ...
}
```

注意，在前面的示例中，向 Mocha 传递了参数。您也可以通过使用两个短横线来在运行 npm 脚本时传递参数：

```
npm test -- test/*.js
```

表 4.1 展示了部分可用的 npm 命令的分解。

##### 表 4.1\. npm 命令

| 命令 | package.json 属性 | 示例用法 |
| --- | --- | --- |
| 启动 | scripts.start | 启动 Web 应用服务器或 Electron 应用。 |
| 停止 | scripts.stop | 停止 Web 服务器。 |
| 重启 |   | 运行停止然后重启。 |
| 安装, 安装后 | scripts.install, scripts.postinstall | 在安装包之后运行原生构建命令。注意，安装后只能在 npm **run** postinstall 下运行。 |

更多命令可用，包括在发布前清理包的命令，以及迁移包版本的前/后版本命令。但对于大多数 Web 开发任务，`start`和`test`是您想要的命令。

你可能想要定义的大量任务可能不适合支持的命令名称。例如，假设你正在处理一个简单的项目，该项目是用 ES2015 编写的，但你希望将其转换为 ES5。你可以使用 `npm run` 来做这件事。在下一节中，你将运行一个教程，设置一个新的项目，可以构建 ES2015 文件。

#### 4.2.1\. 创建自定义 npm 脚本

`npm run` 命令，从 `npm run-script` 别名，用于定义任意脚本，通过 `npm run script-name` 调用。让我们看看如何创建一个使用 Babel 构建客户端脚本的脚本。

首先，设置一个新的项目和安装必要的依赖项：

```
mkdir es2015-example
cd es2015-example
npm init -y
npm install --save-dev babel-cli babel-preset-es2015
echo '{ "presets": ["es2015"] }' > .babelrc
```

现在你应该有一个新的 Node 项目，其中包含了基本的 Babel ES2015 工具和插件。接下来，打开 package.json 文件，并在 `scripts` 下添加一个 `babel` 属性。它应该运行已安装到项目 `node_modules/.bin` 文件夹中的脚本：

```
"babel": "./node_modules/.bin/babel browser.js -d build/"
```

这里有一个使用 ES2015 语法示例文件，你可以使用它；保存为 browser.js：

```
class Example {
  render() {
    return '<h1>Example</h1>';
  }
}

const example = new Example();
console.log(example.render());
```

你可以通过运行 `npm run babel` 来测试这一点。如果一切配置正确，你现在应该有一个包含 browser.js 的构建文件夹。打开 browser.js 以确认它确实是一个 ES5 文件。由于太长无法打印，所以请在文件顶部附近寻找类似 `var_createClass` 的内容。

如果你的项目在构建时只做这些，你可以在 package.json 文件中将它命名为 `build` 而不是 `babel`。但你可以更进一步，添加 UglifyJS：

```
npm i --save-dev uglify-es
```

可以通过使用 node_modules/.bin/uglifyjs 来调用 UglifyJS，所以将其添加到 package.json 中的 `scripts` 部分，命名为 `uglify`：

```
./node_modules/.bin/uglifyjs build/browser.js -o build/browser.min.js
```

现在你应该能够调用 `npm run uglify`。你可以通过结合这两个脚本将所有这些整合在一起。添加另一个名为 `build` 的 `script` 属性，它调用这两个任务：

```
"build": "npm run babel && npm run uglify"
```

两个脚本都是通过输入 `npm run build` 来运行的。现在，你们团队的人可以通过调用这个简单的命令来组合多个前端打包工具。这样做的原因是 Babel 和 UglifyJS 可以作为命令行脚本运行，并且它们都接受命令行参数，因此很容易将它们添加到 package.json 文件中的一行代码中。在 Babel 的例子中，你可以通过定义一个 .babelrc 文件来管理复杂的行为，这在本章的早期部分你已经做过。

#### 4.2.2\. 配置前端构建工具

通常，当与 npm 脚本一起使用时，你可以通过三种方式配置前端构建工具：

+   指定命令行参数。例如，`./node_modules/.bin/uglify --source-map`。

+   创建一个具有选项的项目特定配置文件。这通常用于 Babel 和 ESLint。

+   在 package.json 中添加配置选项。Babel 也支持这一点。

如果你的构建需求有更多步骤，包括复制、连接或移动文件等操作，你会怎么办？你可以创建一个 shell 脚本并通过 npm 脚本来调用它，但如果你使用 Java-Script，可能会帮助你的 JavaScript 熟练的协作者。许多构建系统提供了用于自动化构建的 JavaScript API。在下一节中，你将了解这样一个解决方案：Gulp。

### 4.3\. 使用 Gulp 提供自动化

*Gulp* ([`gulpjs.com/`](http://gulpjs.com/)) 是一个基于流的构建系统。你可以将流路由在一起来创建执行更多操作（不仅仅是转译或压缩代码）的构建过程。想象一下，你有一个使用 Angular 构建的管理区域，但你有一个基于 React 的公共区域；这两个子项目共享某些构建需求。使用 Gulp，你可以为每个阶段重用构建过程的一部分。图 4.1 展示了这两个共享功能的构建过程的示例。

##### 图 4.1\. 两个共享功能的构建过程

![](img/04fig01_alt.jpg)

Gulp 通过两种技术帮助你实现高度的重用：使用插件和定义自己的构建任务。如图所示，构建过程是一个流，因此你可以将任务和插件通过彼此进行管道。例如，你可以使用 Gulp Babel ([www.npmjs.com/package/gulp-babel/](http://www.npmjs.com/package/gulp-babel/)) 和内置的 gulp.src 文件 globbing 方法来处理上一个示例中的 React 部分：

```
gulp.src('public/index.jsx')
  .pipe(babel({
    presets: ['es2015', 'react']
  }))
  .pipe(minify())
  .pipe(gulp.dest('build/public.js'));
```

你甚至可以非常容易地将连接阶段添加到这个链中。在更仔细地查看这个语法之前，让我们看看如何设置一个小型 Gulp 项目。

#### 4.3.1\. 将 Gulp 添加到项目中

要将 Gulp 添加到项目中，你需要使用 npm 安装 gulp-cli 和 gulp 包。大多数人会将 gulp-cli 全局安装，这样就可以简单地通过输入 `gulp` 来运行 Gulp 脚本。请注意，如果你之前已经全局安装了 `gulp` 包，你应该运行 `npm rm --global gulp`。在下一个片段中，你将全局安装 gulp-cli 并创建一个新的具有 Gulp 开发依赖项的 Node 项目：

```
npm i --global gulp-cli
mkdir gulp-example
cd gulp-example
npm init -y
npm i –save-dev gulp
```

接下来创建一个名为 gulpfile.js 的文件：

```
touch gulpfile.js
```

打开 gulpfile 文件。现在你将使用 Gulp 来构建一个小型 React 项目。它将使用 gulp-babel ([www.npmjs.com/package/gulp-babel](http://www.npmjs.com/package/gulp-babel))、gulp-sourcemaps 和 gulp-concat：

```
npm i --save-dev gulp-sourcemaps gulp-babel babel-preset-es2015
npm i --save-dev gulp-concat react react-dom babel-preset-react
```

记得在将 Gulp 插件添加到项目时使用 `--save-dev` 选项。如果你在尝试新的插件并决定移除它们，可以使用 `npm uninstall --save-dev` 来从 ./node_modules 中移除它们，并更新项目的 package.json 文件。

#### 4.3.2\. 创建和运行 Gulp 任务

创建 Gulp 任务涉及在名为 gulpfile.js 的文件中编写带有 Gulp API 的 Node 代码。Gulp 的 API 有用于查找文件并将它们通过以某种方式转换它们的插件进行管道的方法。

要亲自尝试：打开 gulpfile.js 并设置一个构建任务，使用 gulp.src 来查找 JSX 文件，使用 Babel 来处理 ES2015 和 React，然后使用 concat 将每个文件连接起来，如下所示。

##### 列表 4.1\. 使用 Babel 的 ES2015 和 React 的 gulpfile

![](img/04lis01_alt.jpg)

列表 4.1 使用了多个 Gulp 插件来捕获、处理和写入文件。首先，使用文件通配符找到所有输入文件，然后使用 gulp-sourcemaps 插件收集客户端调试的源映射度量。请注意，源映射需要两个阶段：一个用于声明你想要使用源映射，另一个用于写入源映射文件。同时，gulp-babel 被配置为处理带有 ES2015 和 React 的文件。

这个 Gulp 任务可以通过在终端中输入 `gulp` 来运行。

在这个例子中，所有文件都通过使用单个插件进行转换。碰巧 Babel 正在转译 React JSX 代码并将 ES2015 转换为 ES5。一旦完成，文件就使用 gulp-concat 插件进行连接。现在所有转译都已完成，可以安全地编写源映射，并将最终构建放置在 dist 文件夹中。

你可以通过创建一个名为 app/index.jsx 的 JSX 文件来尝试这个 gulpfile。以下是一个简单的 JSX 文件，你可以用它来测试 Gulp：

```
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

Gulp 使得用 JavaScript 表达构建阶段变得容易，通过使用 `gulp.task()`，你可以向此文件添加自己的任务。任务通常遵循相同的模式：

1.  ***源文件—*** 收集输入文件

1.  ***转译—*** 通过一个插件将它们转换

1.  ***合并—*** 将文件连接起来创建一个单体构建

1.  ***输出—*** 设置文件目标或移动输出文件

在上一个例子中，`sourcemaps` 是一个特殊情况，因为它需要两个管道：一个用于配置，另一个用于输出文件。这是有道理的，因为源映射依赖于将原始行号映射到转译构建的行号。

#### 4.3.3\. 监视更改

前端开发者最不想看到的就是构建/刷新周期。简化构建的最简单方法就是使用 Gulp 插件来监视文件系统中的更改。但还有其他选择。一些库与热重载配合得很好，而更通用的基于 DOM 和 CSS 的项目也可以与 LiveReload ([`livereload.com/`](http://livereload.com/)) 项目很好地工作。

例如，你可以在 列表 4.1 中的上一个项目中添加 gulp-watch ([www.npmjs.com/package/gulp-watch](http://www.npmjs.com/package/gulp-watch))。将包添加到项目中：

```
npm i --save-dev gulp-watch
```

现在请记住在 gulpfile.js 中加载该包：

```
const watch = require('gulp-watch');
```

并添加一个监视任务，调用上一个示例中的默认任务：

```
gulp.task('watch', () => {
  watch('app/**.jsx', () => gulp.start('default'));
});
```

这定义了一个名为 `watch` 的任务，然后使用 `watch()` 来监视 React JSX 文件的变化。每当文件发生变化时，默认的构建任务就会运行。经过一些小的修改，这个配方可以用来构建 Syntactically Awesome Style Sheets (SASS) 文件，优化图像，或者几乎任何你可能需要的用于前端项目的东西。

#### 4.3.4\. 为大型项目使用单独的文件

随着项目的增长，它们往往需要更多的 Gulp 任务。最终，你可能会得到一个难以理解的冗长文件。然而，你可以这样做：将你的代码拆分成单独的模块。

正如你所见，Gulp 使用 Node 的模块系统来加载插件。没有特殊的插件加载系统；它只是使用标准模块。你也可以使用 Node 的模块系统来拆分长的 gulpfiles，使你的文件更易于维护。要使用单独的文件，你需要遵循以下步骤：

1.  创建一个名为 gulp 的文件夹，以及一个名为 tasks 的子文件夹。

1.  通过在单独的文件中使用常规的 `gulp.task()` 语法来定义你的任务。每个任务一个文件是一个很好的经验法则。

1.  创建一个名为 gulp/index.js 的文件，用于引入每个 Gulp 任务文件。

1.  在 gulpfile.js 中引入 gulp/index.js 文件。

文件树应该看起来像以下片段：

```
gulpfile.js
gulp/
gulp/index.js
gulp/tasks/development-build.js
gulp/tasks/production-build.js
```

这种技术可以帮助你组织具有复杂构建任务的项目，但它也可以与 gulp-help ([www.npmjs.com/package/gulp-help](http://www.npmjs.com/package/gulp-help)) 模块结合使用。此模块允许你记录 Gulp 任务；运行 `gulp help` 会显示每个任务的信息。当你在一个团队中工作，或者在不同使用 Gulp 的众多项目之间切换时，这很有帮助。图 4.2 展示了输出看起来像什么。

##### 图 4.2\. 示例 gulp-help 输出

![](img/04fig02.jpg)

Gulp 是一个通用的项目自动化工具。当向项目中添加跨平台的维护脚本时很好——例如，运行复杂的客户端测试或为数据库提供固定值。尽管它可以用于构建客户端资源，但也有专门为此目的设计的工具，这意味着它们通常比 Gulp 需要更少的代码和配置。其中一个这样的工具是 webpack，它专注于打包 JavaScript 和 CSS 模块。下一节将演示如何为 React 项目使用 webpack。

### 4.4\. 使用 webpack 构建 web 应用程序

webpack 是专门为构建 Web 应用程序而设计的。想象一下，你正在与一个已经为单页 Web 应用程序创建了一个静态站点的设计师合作，你想要将其调整为构建更高效的 CSS 和 ES2015 JavaScript。使用 Gulp，你编写 JavaScript 代码来驱动构建系统，因此这将涉及编写 gulpfile 和几个构建任务。使用 webpack，你编写一个配置文件，然后通过使用插件和加载器引入新的功能。在某些情况下，不需要额外的配置：你只需在命令行上输入 `webpack` 并指定源文件路径的参数，它就会构建你的项目。跳转到 章节 4.4.4 以查看其外观。

Webpack 的一大优点是它更容易快速设置一个支持增量构建的构建系统。如果你将其设置为在文件更改时自动构建，那么在单个文件更改时，它不需要重新构建整个项目。因此，构建可以更快且更容易理解。

本节展示了如何使用 webpack 为一个小型 React 项目。首先，让我们定义 webpack 使用的术语。

#### 4.4.1\. 使用包和插件

在设置 webpack 项目之前，应该明确一些术语。webpack 插件用于改变构建过程的行为。这可以包括自动将资源上传到 Amazon S3 ([`github.com/MikaAK/s3-plugin-webpack`](https://github.com/MikaAK/s3-plugin-webpack)) 或从输出中删除重复文件。

与插件不同，加载器是对资源文件应用的转换。如果你需要将 SASS 转换为 CSS，或将 ES2015 转换为 ES5，你需要一个加载器。*加载器*是函数，将输入源文本转换为输出。它们可以是异步的或同步的。插件是类的实例，可以钩入 webpack 的更底层 API。

如果你需要转换 React 代码、CoffeeScript、SASS 或任何其他转译语言，你正在寻找一个 *loader*。如果你需要对你的 JavaScript 进行检测，或者以某种方式操作文件集，你需要一个 *plugin*。

在下一节中，你将看到如何使用 Babel 加载器将 React ES2015 项目转换为浏览器友好的包。

#### 4.4.2\. 配置和运行 webpack

你将通过使用 webpack 重新创建 列表 4.1 中的 React 示例。要开始，在一个新项目中安装 React：

```
mkdir webpack-example
npm init -y
npm install --save react react-dom
npm install --save-dev webpack babel-loader babel-core
npm install --save-dev babel-preset-es2015 babel-preset-react
```

最后一行安装了 Babel 的 ES2015 插件和 React 转换器。现在你需要创建一个名为 webpack.config.js 的文件，该文件指导 webpack 在哪里找到输入文件，在哪里写入输出，以及使用哪些加载器。你将使用带有一些额外设置的 babel-loader，如下一列表所示。

##### 列表 4.2\. webpack.config.js 文件

![图片 4.2](img/04lis02_alt.jpg)

此配置文件封装了你成功构建一个使用 ES2015 的 React 应用的所有必需内容。设置非常简单：定义一个 `entry`，这是加载应用程序的主要文件。然后指定输出应该写入的目录；如果该目录尚不存在，则会创建它。接下来，定义一个 loader 并通过使用 `test` 属性将其与文件 glob 搜索关联起来。最后，确保为 loader 设置任何选项。在这个例子中，这些选项加载了 ES2015 和 React Babel 插件。

你需要在 app/index.jsx 中包含一个示例 React JSX 文件；使用 第 4.3.2 节 中的片段。现在运行 ./node_modules/.bin/webpack 将编译一个带有 React 依赖项的 ES5 版本的文件。

#### 4.4.3\. 使用 webpack 开发服务器

如果你想要避免每次 React 文件更改时都重新构建项目，你可以使用 webpack 开发服务器 ([`webpack.github.io/docs/webpack-dev-server.html`](http://webpack.github.io/docs/webpack-dev-server.html))。在本书的源代码中，这可以在 webpack--hotload-example (ch04-front-end/webpack-hotload-example) 下找到。这个小 Express 服务器会在文件更改时运行 webpack 与你的 webpack 配置文件，然后向浏览器提供更改后的资源。你应该在主 Web 服务器之外的不同端口上运行它，这意味着你的脚本标签在开发期间将包含不同的 URL。服务器构建资源并将它们存储在内存中，而不是在你的 webpack 输出文件夹中。你还可以使用 webpack-dev-server 进行热模块加载，类似于 LiveReload 服务器。

要将 webpack-dev-server 添加到项目中，请按照以下步骤操作：

1.  使用 `npm i --save-dev webpack-dev-server@1.14.1` 安装 webpack-dev-server。

1.  在 webpack.config.js 的 `output` 属性中添加一个 `publicPath` 选项。

1.  将一个 index.html 文件添加到你的构建目录中，作为加载你的 JavaScript 和 CSS 打包文件的 harness。确保端口与下一步指定的端口相同。

1.  使用你想要的选项运行服务器。例如，`webpack-dev-server --hot --inline --content-base dist/ --port 3001`。

1.  访问 http://localhost:3001/ 并加载应用程序。

从 列表 4.2 打开 webpack.config.js 并将 `output` 属性更改为包含 `publicPath`：

```
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'bundle.js',
  publicPath: '/assets/'
},
```

创建一个名为 dist/index.html 的新文件，如下所示。

##### 列表 4.3\. 一个 React 网络应用的示例 HTML 模板

![图片](img/04lis03_alt.jpg)

接下来打开 package.json 并在 `scripts` 属性下添加运行 webpack 服务器的命令：

```
"scripts": {
  "server:dev": "webpack-dev-server --hot –inline
     --content-base dist/ --port 3001"
 },
```

`--hot` 选项使开发服务器使用热模块重新加载。如果你编辑 app/index.jsx 中的示例 React 文件，你应该看到浏览器刷新。刷新机制由 `--inline` 选项指定。内联刷新意味着开发服务器将注入代码来管理刷新包。还有一个 iframe 版本，它将整个页面包裹在一个 iframe 中。

现在运行开发服务器：

```
npm run server:dev
```

运行 webpack 开发服务器将触发构建并启动一个监听 3001 端口的服务器。你可以在浏览器中通过访问 http://localhost:3001 来测试一切。

|  |
| --- |

**热重载**

由于 React 和其他框架（包括 AngularJS），存在针对特定框架的热模块重载项目。一些考虑了数据流框架，如 Redux 和 Relay，这意味着可以在保持当前状态的同时刷新代码。这是执行代码重载的理想方式，因为你不需要重复执行步骤来重新创建你正在工作的 UI 状态。

然而，我们在这里提供的示例不那么特定于 React，并且是开始使用 webpack 开发服务器的好方法。务必进行实验，以找到最适合你项目的选项。

|  |
| --- |

#### 4.4.4\. 加载 CommonJS 模块和资源

我们在本章中使用了 React 和 Babel，但如果你使用 webpack 与更纯净的 CommonJS 项目一起使用，那么 webpack 可以提供你需要的一切，无需 CommonJS 浏览器补丁。它甚至能够加载 CSS 文件。

##### webpack 和 CommonJS

要在 webpack 中使用 CommonJS 模块语法，你不需要进行任何设置。假设你有一个使用 `require` 的文件：

```
const hello = require('./hello');

hello();
```

另一个定义了 `hello` 函数的文件：

```
module.exports = function() {
  return 'hello';
};
```

然后，你只需要一个小的 webpack 配置文件来定义入口点（第一个代码片段）和构建目标路径：

```
const path = require('path');
const webpack = require('webpack');

module.exports = {
  entry: './app/index.js',
  output: { path: __dirname, filename: 'dist/bundle.js' },
};
```

此示例说明了 Gulp 和 webpack 的不同之处。webpack 完全专注于构建包，作为其中的一部分，能够生成带有 CommonJS 补丁的包。如果你打开 dist/bundle.js，你会在文件顶部看到 `webpackBootstrap` 补丁，然后每个原始源树中的文件都被封装在闭包中以模拟模块系统。以下代码片段是包的一部分：

```
function(module, exports, __webpack_require__) {

  const hello = __webpack_require__(1);

  hello();

  /***/ },
  /* 1 */
  /***/ function(module, exports) {

  module.exports = function() {
    return 'hello';
  };
```

代码注释显示了模块的定义位置，文件通过其闭包的参数访问 `module` 和 `exports` 对象，以模拟 CommonJS 模块 API。

##### 使用 webpack 包含 npm 包

你可以通过包含从 npm 下载的模块来进一步操作。假设你想使用 jQuery。你不必在页面上创建一个 `script` 标签，而是可以使用 `npm i --save-dev jquery` 安装它，然后就像加载 Node 模块一样加载它：

```
const jquery = require('jquery');
```

这意味着 webpack 默认为你提供 CommonJS 模块和访问 npm 模块的能力，无需任何额外配置！

|  |
| --- |

**查找加载器和插件**

webpack 网站有一个加载器列表（[`webpack.github.io/docs/list-of-loaders.html`](https://webpack.github.io/docs/list-of-loaders.html)）和插件列表（[`webpack.github.io/docs/list-of-plugins.html`](https://webpack.github.io/docs/list-of-plugins.html)）。你还可以在 npm 上找到 webpack 工具；`webpack` 关键字是一个不错的起点（[www.npmjs.com/browse/keyword/webpack](http://www.npmjs.com/browse/keyword/webpack)）。

|  |
| --- |

### 4.5\. 摘要

+   如果你需要自动化简单的任务或调用脚本，npm 脚本非常完美。

+   Gulp 可以用 JavaScript 编写更复杂的任务，并且是跨平台的。

+   当 gulp 文件过长时，你可以将代码分成单独的文件。

+   webpack 可以用来生成客户端包。

+   如果你只需要构建客户端包，使用 webpack 可能比设置等效的 Gulp 脚本要简单。

+   webpack 支持热模块重新加载，这意味着你将看到代码更改而无需刷新浏览器。

## 第五章\. 服务器端框架

*本章涵盖*

+   与流行的 Node Web 框架一起工作

+   选择正确的框架

+   使用 Web 框架构建 Web 应用程序

这章全部关于服务器端 Web 开发。它回答了诸如我如何为特定项目选择完美的框架，以及每个框架的优缺点是什么等问题？

选择正确的框架很困难，因为很难在公平的竞争环境中比较它们。大多数人没有时间学习所有这些框架，所以我们往往只对那些我们有经验的框架做出肤浅的决定。在某些情况下，你可能会同时使用不同的框架。例如，Express 可以用于大型应用程序，而支持大型应用程序的微服务可以编写在 hapi 中。

想象你正在构建一个内容管理系统（CMS）。它用于管理由研究公司收集的法律文件。它可以输出 PDF，并具有电子商务组件。这样的系统可以用以下方式使用单独的框架构建：

+   ***文档上传、下载和阅读——*** Express

+   *PDF 生成微服务——*hapi

+   ***电子商务组件——*** Sails.js

对于特定项目，完美的框架取决于项目的需求和团队的需求。在本章中，我们使用角色——假设的人——作为一种探索特定类型项目适合哪种框架的方式。通过这些虚构的程序员，你将了解 Koa、hapi、Sails.js、DerbyJS、Flatiron 和 LoopBack。角色在下一节中定义。

### 5.1\. 角色

我们不想推销一个你将在每个项目中使用的单一框架。更好的做法是多样化，使用适合每个问题的工具组合。使用角色来思考设计是一种普遍的做法，部分原因是因为它有助于设计师与用户产生共鸣。

在本章中，角色被用来帮助你从第三人称的角度思考框架，看看不同类别的项目适合不同的解决方案。角色是根据专业情况和开发工具来定义的。你应该能够与我们这里创造的三个角色中的至少一个产生共鸣。

#### 5.1.1\. Phil：代理开发者

Phil 作为一名全栈 Web 开发者工作了三年。他做过一点 Ruby、Python 和客户端 JavaScript：

+   ***工作情况——*** 员工，全栈开发者

+   ***工作类型—*** 前端工程，服务器端开发

+   ***计算机—*** MacBook Pro

+   ***工具—*** Sublime Text, Dash, xScope, Pixelmator, Sketch, GitHub

+   ***背景—*** 高中教育；最初是一名业余程序员

Phil 的典型一天涉及与设计师和用户体验专家在敏捷风格的会议中一起开发或审查新功能，以及维护和错误修复。

#### 5.1.2\. Nadine：开源开发者

Nadine 在作为企业级网络开发者成功起步后转向了合同工：

+   ***工作情况—*** 合同工，JavaScript 专家

+   ***工作类型—*** 服务器端编程，偶尔使用 Go 和 Erlang 进行高性能编程。还编写了一个流行的开源、基于 Web 的电影目录应用程序

+   ***计算机—*** 高端 PC，Linux

+   ***工具—*** Vim, tmux, Mercurial, shell 中的任何东西

+   背景—计算机科学学位

Nadine 的一天通常涉及在为她的两个主要客户工作足够的时间与从事她的开源项目之间取得平衡。她的客户工作是以测试驱动的，但她的开源项目更注重功能驱动。

#### 5.1.3\. Alice：产品开发者

Alice 在一个成功的 iOS 应用程序上工作，同时也帮助她的公司处理 Web API：

+   ***工作情况—*** 员工，程序员

+   ***工作类型—*** iOS 开发；也负责 Web 应用程序和网络服务

+   ***计算机—*** MacBook Pro, iPad Pro

+   ***工具—*** Xcode, Atom, Babel, Perforce

+   ***背景—*** 科学学位；她当前创业公司的前五名员工之一

Alice 不情愿地使用 Xcode、Objective-C 和 Swift，但秘密地更喜欢 JavaScript，并对 ES2015 和 Babel 感到兴奋。她喜欢开发新的网络服务来支持她公司的 iOS 和桌面应用程序，并希望更频繁地从事基于 React 的网络应用程序开发。

现在已经定义了角色，让我们来定义术语 *框架*。

### 5.2\. 什么是框架？

本章讨论的一些服务器端框架在技术上根本不是框架。术语 *框架* 很不幸地被过度使用，对不同程序员意味着不同的事情。在 Node 社区中，更准确地称这些项目为 *模块*，但在直接比较这一系列库时，一个更细致的定义是有用的。

LoopBack 项目 ([`loopback.io/resources/#compare`](http://loopback.io/resources/#compare)) 使用以下定义：

+   ***API 框架—*** 用于构建 Web API 的库，由帮助结构化应用程序的框架支持。LoopBack 本身被定义为这种类型的框架。

+   ***HTTP 服务器库—*** 基于 Express 的任何东西都属于这一类别，包括 Koa 和 Kraken.js。这些库帮助您构建基于 HTTP 动词和路由的应用程序。

+   ***HTTP 服务器框架—*** 用于构建使用 HTTP 通信的模块化服务器的框架。hapi 是这种类型框架的例子。

+   ***Web MVC 框架—*** 包括 Sails.js 在内的模型-视图-控制器框架属于这一类别。

+   ***全栈框架—*** 这些框架在服务器和浏览器上使用 JavaScript，并且能够在两端之间共享代码。这被称为 *同构代码*。DerbyJS 是一个全栈 MVC 框架。

大多数 Node 开发者将 *框架* 理解为第二个术语：HTTP 服务器库。下一节将介绍 Koa，这是一个使用创新 ES2015 语法（称为 *生成器*）来提供独特方式处理 HTTP 中间件的服务器库。

### 5.3\. Koa

Koa ([`koajs.com/)is`](http://koajs.com/)is) 基于 Express，但使用 ES2015 生成器语法来定义中间件。这意味着你可以以几乎同步的方式编写中间件。这部分解决了高度依赖回调的中间件问题。在 Koa 中，你可以使用 `yield` 关键字退出并重新进入中间件。表 5.1 是 Koa 主要功能的概述。

##### 表 5.1\. Koa 的主要功能

| **库类型** | HTTP 服务器库 |
| --- | --- |
| **功能** | 基于生成器的中间件，请求/响应模型 |
| **建议用途** | 轻量级 Web 应用，非严格 HTTP API，服务单页 Web 应用 |
| **插件架构** | 中间件 |
| **文档** | [`koajs.com/`](http://koajs.com/) |
| **流行度** | 10,000 GitHub 星标 |
| **许可证** | MIT |

以下列表展示了如何使用 Koa 通过将执行权传递给下一个中间件组件并在完成时在调用者中继续执行来基准测试请求。

##### 列表 5.1\. Koa 的中间件排序

![](img/05lis01_alt.jpg)

列表 5.1 使用生成器 ![](img/circ1.jpg) 在两个中间件组件之间切换上下文。请注意，我们使用关键字 `function*`——在这里不能使用箭头函数。通过使用 `yield` 关键字 ![](img/circ2.jpg)，执行步骤会下降到中间件堆栈，然后在下一个中间件组件返回时再次返回 ![](img/circ3.jpg)。使用生成器函数的一个额外好处是你可以直接设置 `this.body`。相比之下，Express 使用一个函数来发送响应：`res.send(response)`。在 Koa 中间件中，`this` 被称为 *上下文*。为每个请求创建一个上下文，并用于封装 Node 的 HTTP `request` 和 `response` 对象 ([`nodejs.org/api/http.html`](https://nodejs.org/api/http.html))。每次你需要从请求中访问某些内容时，例如 GET 参数或 cookies，你都可以使用上下文。对于响应也是如此：正如你在 列表 5.1 中看到的，你可以通过在 `this.body` 上设置值来控制发送到浏览器的数据。

如果你之前使用过 Express 中间件和生成器语法，Koa 应该很容易学习。如果你对这两者中的任何一个都不熟悉，Koa 可能很难理解——或者至少可能很难看到这种风格的好处。![图 5.1](img/#ch05fig01) 更详细地展示了 `yield` 如何在中间件组件之间传递执行。

##### 图 5.1\. Koa 中间件执行顺序

![](img/05fig01_alt.jpg)

图 5.1 中的每个阶段都对应于 列表 5.1 中的数字。首先，计时器在第一个中间件组件中设置 ![](img/circ1.jpg)，然后执行权传递给第二个中间件组件，该组件渲染主体 ![](img/circ2.jpg)。在发送响应后，执行权返回到第一个中间件组件，并计算时间 ![](img/circ3.jpg)。这通过 `console.log` 在终端中显示，然后请求完成 ![](img/circ4.jpg)。请注意，阶段 ![](img/circ4.jpg) 在 列表 5.1 中不可见；它由 Koa 和 Node 的 HTTP 服务器处理。

#### 5.3.1. 设置

使用 Koa 设置项目需要安装模块，然后定义中间件。如果你想要更多功能，例如一个路由 API，它可以使定义和响应各种类型的 HTTP 请求更容易，那么你需要安装路由中间件。这意味着典型的流程需要在事先规划项目将使用的中间件，因此你需要首先研究流行的模块。

|  |
| --- |

**人物想法**

*爱丽丝*：“作为一名产品开发者，我喜欢 Koa 的最小功能集——因为我们的项目有独特的要求，我们真的希望根据我们的需求塑造整个堆栈。”

*菲利普*：“作为一名机构开发者，我发现处理中间件研究阶段太麻烦了。我宁愿有人帮我处理，因为我的许多项目有类似的要求，我不想反复安装相同的模块来做基本的事情。”

|  |
| --- |

下一个部分演示了一个第三方模块，它为 Koa 实现了一个强大的路由库。

#### 5.3.2. 定义路由

一个流行的路由中间件组件是 koa-router ([`www.npmjs.com/package/koa-router`](https://www.npmjs.com/package/koa-router))。像 Express 一样，它基于 HTTP 动词，但与 Express 不同，它有一个可链式调用的 API。下面的代码片段显示了如何定义路由组：

```
router
 .post('/pages', function*(next) {
   // Create a page
 })
 .get('/pages/:id', function*(next) {
   // Render the page
 })
 .put('pages-update', '/pages/:id', function*(next) {
   // Update a page
 });
```

可以使用额外的参数来命名路由。这很好，因为你可以生成 URL，而并非所有 Node Web 框架都支持这一点。以下是一个示例：

```
router.url('pages-update', '99');
```

此模块结合了 Express 和其他 Web 框架的独特功能。

|  |
| --- |

**人物想法**

*菲利普*：“这个路由库让我想起了我喜欢 Ruby on Rails 的一些东西，所以 Koa 最终可能会赢得我的青睐！”

*纳丁*：“我可以看到使用 Koa 对现有项目进行模块化的机会，然后与社区分享这段代码。”

|  |
| --- |

#### 5.3.3. REST API

Koa 不自带实现某种路由处理中间件所需的工具来制作 RESTful API。前面的示例可以扩展到在 Koa 中实现 RESTful API。

#### 5.3.4. 优势

很容易说 Koa 的优势来自于其对生成器语法的早期采用，但现在随着 ES2015 在 Node 社区中的普及，这已经不再像以前那样独特。目前，Koa 的主要优势在于它既简洁又拥有一些优秀的第三方模块；查看 Koa 维基以获取更多信息 ([`github.com/koajs/koa/wiki#middleware`](https://github.com/koajs/koa/wiki#middleware))。产品开发者喜欢它，因为它具有优雅的语法，并且可以根据特定要求进行定制。

#### 5.3.5. 弱点

Koa 的可配置性程度让一些开发者感到困惑。使用 Koa 创建许多小型项目可能会导致代码重用率低，除非你已经实施了代码共享策略。

### 5.4. 克隆龙

克隆龙基于 Express，但通过 PayPal 开发的自定义模块增加了新功能。特别是有一个有用的模块是 Lusca ([`github.com/krakenjs/lusca`](https://github.com/krakenjs/lusca))，它提供了一个应用安全层。尽管可以在没有克隆龙的情况下使用 Lusca，但克隆龙的一个好处是它预定义了项目结构。Express 和 Koa 应用不需要特定的项目结构，所以如果你需要帮助开始新项目，克隆龙可以帮助你开始。 表 5.2 展示了克隆龙主要功能的概述。

##### 表 5.2. 克隆龙的主要功能

| **库类型** | HTTP 服务器库 |
| --- | --- |
| **功能** | 严格的项目结构、模型、模板（Dust）、安全加固（Lusca）、配置管理、国际化 |
| **推荐用途** | 企业级 Web 应用 |
| **插件架构** | Express 中间件 |
| **文档** | [`www.kraken.com/help/api`](https://www.kraken.com/help/api) |
| **流行度** | 4,000 GitHub 星标 |
| **许可** | Apache 2.0 |

#### 5.4.1. 设置

如果你已经有了一个 Express 项目，你可以将克隆龙作为中间件组件添加：

```
const express = require('express'),
const kraken = require('kraken-js');

const app = express();
app.use(kraken());
app.listen(3000);
```

但如果你想开始一个新项目，你应该尝试克隆龙的 Yeoman 生成器。Yeoman 是一个帮助你生成新项目的工具。通过使用 Yeoman 生成器，你可以为各种框架创建初始化的项目。以下是使用 Yeoman 创建定制克隆龙项目的步骤，以及克隆龙首选的文件系统布局：

```
$ npm install -g yo generator-kraken bower grunt-cli
$ yo kraken

     ,'""`.
hh  / _  _ \
    |(@)(@)|   Release the Kraken!
    )  __  (
   /,'))((`.\
  (( ((  )) ))
   `\ `)(' /'

Tell me a bit about your application:

[?] Name: kraken-test
[?] Description: A Kraken application
[?] Author: Alex R. Young
...
```

生成器会创建一个新的目录，因此你不需要自己这样做。生成器完成后，你应该能够启动服务器并访问 http://localhost:8000 来尝试它。

#### 5.4.2. 定义路由

在克隆龙中，路由定义与控制器并列。与 Express 将路由定义和路由处理程序分离不同，克隆龙采用了一种受 MVC 启发的轻量级方法，这得益于 ES6 箭头函数的使用：

```
module.exports = (router) => {
  router.get('/', (req, res) => {
    res.render('index');
  });
};
```

路由可以在 URL 中包含参数：

```
module.exports = (router) => {
  router.get('/people/:id', (req, res) => {
    const people = { alex: { name: 'Alex' } };
    res.render('people/edit', people[req.param.id]);
  });
};
```

Kraken 的路由 API 是 express-enrouten ([`github.com/krakenjs/express-enrouten`](https://github.com/krakenjs/express-enrouten))，它部分从文件所在的目录推断路由。假设你有一个这样的文件布局：

```
controllers
 |-user
     |-create.js
     |-list.js
```

然后，Kraken 将生成如/user/create 和/user/list 之类的路由。

#### 5.4.3\. REST API

Kraken 可以用来制作 REST API，但不提供针对它们的特定支持。express-enrouten 的功能与解析 JSON 的中间件结合意味着你可以使用 Kraken 来实现 REST API。

Kraken 的路由器支持 HTTP 动词 DELETE、GET、POST、PUT 等，这使得实现 REST 与 Express 类似。

#### 5.4.4\. 优点

由于 Kraken 自带生成器，从高层次来看，Kraken 项目看起来很相似。尽管 Express 项目的布局可能千差万别，但 Kraken 项目通常将文件和目录放在相同的位置。

由于 Kraken 提供了模板库（Dust）和国际化（Makara），这两个功能无缝集成。要编写具有国际化的 Dust 模板，你需要指定一个键：

```
<h1>{@pre type="content" key="greeting"/}</h1>
```

然后在 locales/language-code/view-name.properties 中添加一个.properties 文件。这些属性文件是简单的键值对，所以如果前面的例子在一个名为 public/templates/profile.dust 的视图文件中，那么.profile 文件将是 locales/US/en/profile.properties。

|  |
| --- |

**角色思考**

*Phil*: “Kraken 具有文件系统布局并使用控制器进行路由的事实让我印象很深。我们团队中的一些人了解 Django 和 Ruby on Rails，因此这对他们来说将是一个容易的过渡。Kraken 的文档看起来也非常好；博客上有很多有用的内容。”

*Alice*: “我喜欢通过 Lusca 获得更好的应用程序安全性的想法，但 Kraken 提供了一些我并不真正需要的东西。我将尝试仅使用 Lusca。”

|  |
| --- |

#### 5.4.5\. 缺点

学习 Kraken 比学习 Koa 或 Express 需要更多的努力。在 Express 中通过编程完成的某些任务，在 Kraken 中是通过 JSON 配置文件完成的，有时很难确定需要哪些 JSON 属性才能按预期的方式工作。

### 5.5\. hapi

hapi ([`hapijs.com/`](http://hapijs.com/))是一个专注于 Web API 开发的服务器框架。它有自己的 hapi 插件 API，不提供任何客户端支持或数据库模型层。它包含一个路由 API 和自己的 HTTP 服务器包装器。在 hapi 中，你通过将服务器视为主要抽象来设计 API。内置的连接和日志记录功能使 hapi 在 DevOps 方面具有良好的扩展性和管理能力。表 5.3 包含 hapi 主要功能的概述。

##### 表 5.3\. hapi 的主要功能

| **库类型** | HTTP 服务器框架 |
| --- | --- |
| **功能** | 高级服务器容器抽象，安全头部 |
| **建议用途** | 单页 Web 应用，HTTP API |
| **插件架构** | hapi 插件 |
| **文档** | [`hapijs.com/api`](http://hapijs.com/api) |
| **流行度** | 6,000 GitHub 星标 |
| **许可** | BSD 3 条款 |

#### 5.5.1\. 设置

首先，创建一个新的 Node 项目并安装 hapi：

```
mkdir listing5_2
cd listing5_2
npm init –y
npm install --save hapi
```

然后创建一个名为 server.js 的新文件。添加以下列表中的代码。

##### 列表 5.2\. 基本 hapi 服务器

```
const Hapi = require('hapi');
const server = new Hapi.Server();

server.connection({
  host: 'localhost',
  port: 8000
});

server.start((err) => {
  if (err) {
    throw err;
  }
  console.log('Server running at:', server.info.uri);
});
```

你可以直接运行这个示例，但没有任何路由它不会做很多事情。继续阅读以了解 hapi 如何处理路由。

#### 5.5.2\. 定义路由

hapi 内置了一个用于创建路由的 API。你必须提供一个包含请求方法、URL 和一个运行回调的对象，这个回调被称为 *handler*。下面的列表展示了如何使用处理方法定义一个路由。

##### 列表 5.3\. hapi hello world 服务器

```
const Hapi = require('hapi');
const server = new Hapi.Server();

server.connection({
  host: 'localhost',
  port: 8000
});

server.route({
  method: 'GET',
  path:'/hello',
  handler: (request, reply) => {
    return reply('hello world');
  }
});

server.start((err) => {
  if (err) {
    throw err;
  }
  console.log('Server running at:', server.info.uri);
});
```

将以下代码添加到前面的列表中，以定义一个将响应文本 *hello world* 的路由和处理程序。你可以通过输入 `npm start` 来运行此示例。打开 http://localhost:8000/hello 来查看响应。

hapi 不包含预定义的文件夹结构或任何 MVC 功能；它完全基于服务器。在这方面，它与 Express 类似。然而，请注意一个关键的区别：`request, reply` 路由处理程序签名与 Express 的 `req, res` 不同。hapi 的请求和回复对象也与 Express 的等效对象不同：你必须调用 `reply` 而不是操作 Express 的 `res` 对象。Express 更类似于 Node 内置的 HTTP 服务器。

要超越这个简单的示例并获得更多功能，例如提供静态文件，你需要插件。

#### 5.5.3\. 插件

hapi 有自己的插件架构，大多数项目需要几个插件来提供诸如身份验证和用户输入验证等功能。大多数项目都需要一个简单的插件 inert ([`github.com/hapijs/inert`](https://github.com/hapijs/inert))，它添加了静态文件和目录处理程序。

要将 inert 添加到 hapi 项目中，你需要首先使用 `server.register` 方法注册插件。这添加了 `reply.file` 方法用于发送单个文件，以及内置的目录处理程序。让我们看看目录处理程序。

确保你已经基于 列表 5.2 设置了一个项目。接下来，安装 inert：

```
npm install --save inert
```

现在，插件可以被加载并注册。打开 server.js 文件并添加以下行。

##### 列表 5.4\. 使用 hapi 添加插件

```
const Inert = require('inert');

server.register(Inert, () => {});

server.route({
  method: 'GET',
  path: '/{param*}',
  handler: {
    directory: {
      path: '.',
      redirectToSlash: true,
      index: true
    }
  }
});
```

hapi 路由不仅可以接受函数，还可以接受插件的配置对象。在这个列表中，`directory` 对象包含了惰性设置，用于在当前路径下提供文件服务并显示该目录中的文件索引。这与 Express 中间件不同，展示了插件如何在 hapi 应用程序中扩展服务器的行为。

#### 5.5.4\. REST API

hapi 支持 HTTP 动词和 URL 参数化，允许通过标准的 hapi 路由 API 实现 REST API。以下是一个用于通用删除方法的路由片段：

```
server.route({
  method: 'DELETE',
  path: '/items/{id}',
  handler: (req, reply) => {
    // Delete "item" here, based on req.params.id
    reply(true);
  }
});
```

此外，插件使创建 RESTful API 变得更加容易。例如，hapi-sequelize-crud ([`www.npmjs.com/package/hapi-sequelize-crud`](https://www.npmjs.com/package/hapi-sequelize-crud)) 可以根据 Sequelize 模型 ([`docs.sequelizejs.com/en/latest/`](http://docs.sequelizejs.com/en/latest/)) 自动生成 RESTful API。

|  |
| --- |

**角色思考**

*Phil*: “我肯定会尝试 hapi-sequelize-crud，因为我们已经有使用 PostgreSQL 和 MySQL 的应用程序，所以 Sequelize 可能是一个不错的选择。但是，由于 hapi 本身不提供这类功能，我担心这个插件可能会失去支持，所以我不确定 hapi 是否能在代理场景中良好工作。”

*Alice*: “作为一名产品开发者，我认为 hapi 很有趣，因为它像 Express 一样简洁，但插件 API 更加正式和表达性强。”

*Nadine*: “我可以看到为 hapi 制作开源插件的几个机会，并且现有的插件看起来都写得很好。hapi 似乎有一个技术能力强的受众，这对我很有吸引力。”

|  |
| --- |

#### 5.5.5\. 优点

hapi 的插件 API 是使用 hapi 的最大优势之一。插件可以扩展 hapi 的服务器，也可以添加各种其他行为，从数据验证到模板化。此外，由于 hapi 基于 HTTP 服务器，它适合某些类型的部署场景。如果你需要部署许多需要连接在一起或负载均衡的服务器，你可能更喜欢 hapi 基于服务器的 API 而不是 Express 或 Koa。

#### 5.5.6\. 弱点

hapi 与 Express 有类似的弱点：它很简洁，因此没有关于项目结构的指导。你永远不能确定某个插件的开发是否会停止，所以过多地依赖插件可能会在未来造成维护问题。

### 5.6\. Sails.js

你迄今为止看到的框架都是最小化的服务器库。Sails ([`sailsjs.org/`](http://sailsjs.org/)) 是一个模型-视图-控制器（MVC）框架，它与服务器库有根本的不同。它包含一个用于与数据库工作的对象关系映射（ORM）库，并且可以自动生成 REST API。它还具有现代功能，包括内置的 WebSocket 支持。如果你是 React 或 Angular 的粉丝，你会很高兴知道它是前端无关的：它不是一个全栈框架，所以你可以与几乎任何前端库或框架一起使用。 表 5.4 显示了 Sails 的主要功能。

##### 表 5.4\. Sails 的主要功能

| **库类型** | MVC 框架 |
| --- | --- |
| **功能** | 带有 ORM 的数据库支持，REST API 生成，WebSocket |
| **建议用途** | Rails 风格的 MVC 应用程序 |
| **插件架构** | Express 中间件 |
| **文档** | [`sailsjs.org/documentation/concepts/`](http://sailsjs.org/documentation/concepts/) |
| **流行度** | 6,000 GitHub 星标 |
| **许可证** | BSD 3 条款 |
|  |

**角色思考**

*Phil*: “这听起来正是我想要的——有什么陷阱吗？！”

*Alice*: “我原以为这不适合我，因为我们已经在 React 应用上投入了开发时间，但因为它专注于服务器，它可能适合我们的产品。”

| |
| --- |

#### 5.6.1\. 设置

Sails 自带项目生成器，所以最好将其全局安装以简化创建新项目的过程。使用 npm 安装它，然后使用`sails new`来创建项目：

```
npm install -g sails
sails new example-project
```

这将创建一个新的目录，其中包含一个用于基本 Sails 依赖项的 package.json。新项目包括 Sails 本身、EJS 和 Grunt。你可以运行`npm start`来启动服务器，或者输入`sails lift`。当服务器运行时，你可以通过访问 http://localhost:1337 来查看内置的入门页面。

#### 5.6.2\. 定义路由

要添加路由，在 Sails 中称为*自定义路由*，请打开 config/routes.js 并为导出的路由添加一个属性。这个属性是 HTTP 动词和部分 URL。例如，以下是一些有效的 Sails 路由：

```
module.exports.routes = {
  'get /example': { view: 'example' },
  'post /items': 'ItemController.create
};
```

第一个路由期望一个名为 views/example.ejs 的文件。第二个路由期望一个名为 api/controllers/ItemController 的文件，其中包含一个名为`create`的方法。你可以通过运行`sails generate controller item create`来生成这个控制器。类似的命令可以用来快速创建 RESTful API。

#### 5.6.3\. REST API

Sails 将数据库模型和控制器组合成 API，所以要快速创建 RESTful API，请使用`sails generate api resource-name`。要使用数据库，你首先需要安装数据库适配器。添加 MySQL 涉及到找到 Waterline MySQL 包的名称([`github.com/balderdashy/waterline`](https://github.com/balderdashy/waterline))并将其添加到项目中：

```
npm install --save waterline sails-mysql
```

接下来，打开 config/connections.js 并填写你 MySQL 服务器的连接详情。Sails 模型文件允许你指定数据库连接，因此你可以使用不同的模型与不同的数据库一起使用。这允许像 Redis 中的用户会话数据库和其他在关系型数据库（如 MySQL）中的更永久资源这样的情况。

Waterline 是 Sails 的数据库库，并且它有自己的文档仓库([`github.com/balderdashy/waterline-docs`](https://github.com/balderdashy/waterline-docs))。除了支持多种数据库外，Waterline 还有一些有用的功能：你可以定义表和列名以支持旧版模式，并且查询 API 支持 Promise，使得查询看起来像是现代 JavaScript。

| |
| --- |

**人物想法**

*Phil*: “创建 API 的便捷性以及 Waterline 模型能够支持现有的数据库模式意味着 Sails 对我们来说听起来非常理想。我们有一些客户，我们希望他们逐步从 MySQL 迁移到 PostgreSQL，所以我们可能可以使用 Waterline 来实现这一点。我们的一些开发者和设计师已经使用过 Ruby on Rails，所以我认为他们很快就能掌握使用 Node 的现代化 ES2015 语法的 Sails。”

*Alice*: “这个框架提供了一些我们产品不需要的功能。我觉得 Koa 或 hapi 可能更适合。”

| |
| --- |

#### 5.6.4\. 优点

内置的项目创建和 API 生成意味着设置项目和添加典型的 REST API 非常快。这对于快速创建新项目和协作很有用，因为 Sails 项目具有相同的文件系统布局。Sails 的创建者 Mike McNeil 和我 rl Nathan 写了一本名为《Sails.js in Action》（Manning Publications，2017）的书，展示了 Sails 如何欢迎 Node 初学者。

#### 5.6.5\. 缺点

Sails 与其他服务器端 MVC 框架共享一些弱点：路由 API 意味着您必须考虑 Sails 的路由功能来设计应用程序，并且您可能会发现很难将您的模式适应 Waterline 处理事物的方式。

### 5.7\. DerbyJS

DerbyJS 是一个支持数据同步和视图服务器端渲染的全栈框架。它依赖于 MongoDB 和 Redis。数据同步层由 ShareJS 提供，并支持自动冲突解决。表 5.5 总结了 DerbyJS 的主要特性。

##### 表 5.5\. DerbyJS 特性

| **库类型** | 全栈框架 |
| --- | --- |
| **特性** | 使用 ORM（Racer）的数据库支持，同构 |
| **推荐用途** | 具有服务器端支持的单一页面 Web 应用程序 |
| **插件架构** | DerbyJS 插件 |
| **文档** | [`derbyjs.com/docs/derby-0.6`](http://derbyjs.com/docs/derby-0.6) |
| **流行度** | 4,000 GitHub stars |
| **许可证** | MIT |

#### 5.7.1\. 设置

如果您没有 MongoDB 或 Redis，您需要安装这两个来运行 DerbyJS 示例。DerbyJS 文档解释了如何在 Mac OS、Linux 和 Windows 上完成此操作（[`derbyjs.com/started#environment`](http://derbyjs.com/started#environment)）。

要快速创建一个新的 DerbyJS 项目，安装 derby 和 derby-starter。derby-starter 包用于引导 Derby 应用程序：

```
mkdir example-derby-app
cd example-derby-app
npm init -f
npm install --save derby derby-starter derby-debug
```

Derby 应用程序被分割成几个较小的应用程序，因此创建一个新的应用程序目录，包含三个文件：index.js、server.js 和 index.html。以下列表展示了一个简单的 Derby 应用程序，它渲染了一个模板。

##### 列表 5.5\. Derby 应用程序的 index.js 文件

```
const app = module.exports = require('derby')
  .createApp('hello', __filename);
app.loadViews(__dirname);

app.get('/', (page, model) => {
  const message = model.at('hello.message');
  message.subscribe(err => {
    if (err) return next(err);
    message.createNull('');
    page.render();
  });
});
```

服务器文件只需要加载 derby-starter 模块，如下面的代码片段所示。将其保存为 app/server.js：

```
require('derby-starter').run(__dirname, { port: 8005 });
```

app/index.html 文件渲染一个输入字段和用户输入的消息：

```
<Body:>
  Holler: <input value="{{hello.message}}">
  <h2>{{hello.message}}</h2>
```

您应该能够通过在 example-derby-app 目录中输入`node derby/server.js`来运行应用程序。一旦它开始运行，编辑 app/index.html 文件将导致应用程序重新启动；在编辑代码和模板时，您将自动获得实时更新。

#### 5.7.2\. 定义路由

DerbyJS 使用 derby-router 进行路由。因为 DerbyJS 由 Express 驱动，所以服务器端路由的 API 相似，并且在浏览器中也使用相同的路由模块。当在 DerbyJS 应用程序中点击链接时，它将尝试在客户端渲染响应。

DerbyJS 是一个全栈框架，所以添加路由的方式与其他你在本章中查看的库略有不同。添加基本路由的最地道方式是通过添加视图。打开 apps/app/index.js 并使用 `app.get` 添加路由：

```
app.get('hello', '/hello');
```

接下来，打开 apps/app/views/hello.pug 并添加一个简单的 Pug 模板：

```
index:
  h2 Hello
  p Hello world
```

现在打开 apps/app/views/index.pug 并导入模板：

```
import:(src="./hello")
```

如果你已经运行了 `npm start`，项目应该会不断更新，所以现在打开 http://localhost:3000/hello 将会显示新的视图。

读取 `index:` 的行是视图的 *命名空间*。在 DerbyJS 中，视图名称有冒号分隔的命名空间，所以你创建了 `hello:index`。背后的想法是将视图封装起来，以便在大型项目中不会发生冲突。

#### 5.7.3. REST API

在 DerbyJS 项目中，你需要通过添加路由和路由处理程序来创建 RESTful API。你的 DerbyJS 项目将有一个使用 Express 创建服务器的 server.js 文件。如果你打开 server/routes.js，你会找到一个使用标准 Express 路由 API 定义的示例路由。

在服务器路由文件中，你可以使用 `app.use` 来挂载另一个 Express 应用程序，因此你可以将 REST API 模拟为一个完全独立的 Express 应用程序，该应用程序由主 DerbyJS 应用程序挂载。

#### 5.7.4. 优势

DerbyJS 有数据库模型 API 和数据同步 API。你可以用它来构建单页 Web 应用程序和现代实时应用程序。因为它内置了 WebSocket 和同步功能，所以你不必担心使用哪个 WebSocket 库，或者如何在客户端和服务器之间同步数据。

|  |
| --- |

**角色思考**

*Phil*：“我们有一个客户询问基于实时数据构建数据可视化项目的事情，所以我认为 DerbyJS 对此可能很有用。但是学习曲线似乎很陡峭，所以我不确定我能否说服我们的开发者使用它。”

*Alice*：“作为一个产品开发者，我发现很难看到如何将我们产品的需求与 DerbyJS 的架构相匹配，所以我认为它不适合我的项目。”

|  |
| --- |

#### 5.7.5. 劣势

让已经熟悉服务器端或客户端库的人使用 DerbyJS 是一件困难的事情。例如，喜欢 React 的客户端开发者通常不想使用 DerbyJS。喜欢制作 REST API 或 MVC 项目并且对 WebSocket 感到舒适的服务器端开发者也未能被激励去学习 DerbyJS。

### 5.8. Flatiron.js

Flatiron 是一个包含 URL 路由、数据管理、中间件、插件和日志功能的 Web 框架。与大多数 Web 框架不同，Flatiron 的模块被设计为解耦的，因此你不必使用它们全部。你甚至可以在自己的项目中使用一个或多个——例如，如果你喜欢日志模块，你可以在 Express 项目中将其添加进去。与许多 Node 框架不同，Flatiron 的 URL 路由和中间件层不是使用 Express 或 Connect 编写的，尽管中间件与 Connect 向后兼容。表 5.6 总结了 Flatiron 的功能。

##### 表 5.6\. Flatiron 的功能

| **库类型** | 模块化 MVC 框架 |
| --- | --- |
| **功能** | 数据库管理层（资源丰富），解耦的可重用模块 |
| **建议用途** | 轻量级 MVC 应用，在其他框架中使用 Flatiron 模块 |
| **插件架构** | Broadway 插件 API |
| **文档** | [`github.com/flatiron`](https://github.com/flatiron) |
| **流行度** | 1,500 GitHub stars |
| **许可证** | MIT |

#### 5.8.1\. 设置

安装 Flatiron 需要全局安装命令行工具以创建新的 Flatiron 项目：

```
npm install -g flatiron
flatiron create example-flatiron-app
```

运行这些命令后，你将找到一个包含带有必要依赖项的 package.json 文件的新目录。运行`npm install`来安装依赖项，然后运行`npm start`来启动应用。

主 app.js 文件看起来很像一个典型的 Express 应用：

```
const flatiron = require('flatiron');
const path = require('path');
const app = flatiron.app;

app.config.file({ file: path.join(__dirname, 'config', 'config.json') });

app.use(flatiron.plugins.http);

app.router.get('/', () => {
  this.res.json({ 'hello': 'world' })
});

app.start(3000);
```

注意，然而，路由器与 Express 和 Koa 都不同。响应是通过使用`this.res`返回的，而不是响应回调函数的参数。让我们更详细地看看 Flatiron 的路由。

#### 5.8.2\. 定义路由

Flatiron 的路由库称为 Director。尽管它可以用于服务器路由，但它也支持浏览器中的路由，因此可以用于制作单页应用。Director 将 Express 风格的 HTTP 动词路由称为 ad hoc：

```
router.get('/example', example);
router.post('/example', examplePost);
```

路由可以有参数，参数可以用正则表达式定义：

```
router.param('id', /([\\w\\-]+)/);
router.on('/pages/:id', pageId => {});
```

要生成响应，使用`res.writeHead`发送头部信息，并使用`res.end`发送响应体：

```
router.get('/',  () => {
  this.res.writeHead(200, { 'content-type': 'text/plain' });
  this.res.end('Hello, World');
});
```

路由 API 也可以作为一个类使用，带有路由表对象。要使用它，实例化一个新的路由器，然后在 HTTP 请求到达时使用 dispatch 方法：

```
const http = require('http');
const director = require('director');
const router = new director.http.Router({
  '/example': {
    get: () => {
      this.res.writeHead(200, { 'Content-Type': 'text/plain' })
      this.res.end('hello world');
    }
  }
});
const server = http.createServer((req, res) =>
  router.dispatch(req, res);
});
```

使用路由 API 作为类也意味着你可以挂钩到流式 API。这使得以快速和简单的方式处理大型请求成为可能，这对于诸如解析上传数据并提前退出等操作是有益的：

```
const director = require('director');
const router = new director.http.Router();

router.get('/', { stream: true }, () => {
  this.req.on('data', (chunk) => {
    console.log(chunk);
  });
});
```

Director 具有作用域路由 API，这对于创建 REST API 很有用。

#### 5.8.3\. REST API

可以使用标准的 Express HTTP 动词风格方法或 Director 的作用域路由功能创建 REST API。这允许根据 URL 片段和 URL 参数将路由分组在一起：

```
 router.path(/\/users\/(\w+)/, () => {
  this.get((id) => {});
  this.delete((id) => {});
  this.put((id) => {});
});
```

Flatiron 还提供了一个高级 REST 包装器，称为 Resourceful ([`github.com/flatiron/resourceful`](https://github.com/flatiron/resourceful))，它支持 CouchDB、MongoDB、Socket.IO 和数据验证。

#### 5.8.4\. 优势

框架要获得影响力是很困难的，这就是为什么 Flatiron 的解耦设计是一个主要优势。你可以使用其模块而不必使用整个框架。例如，Winston 日志模块 ([`github.com/winstonjs/winston`](https://github.com/winstonjs/winston)) 被许多不使用 Flatiron 其他部分的项目所使用。这意味着 Flatiron 的某些部分收到了良好的开源贡献。

The Director URL-routing API is isomorphic, so you can use it as a solution for both client- and server-side development. Director’s API differs from the Express-style routing APIs as well: Director has a simplified streaming API, and the routing object emits events before and after routes are executed.

与大多数 Node 网络框架不同，Flatiron 有一个插件管理器。因此，使用社区支持的插件扩展 Flatiron 项目更容易。

|  |
| --- |

**人物观点**

*Nadine*: “我喜欢 Flatiron 的模块化设计，插件管理器也很棒。我已经能想到一些我想制作的插件。”

*Alice*: “我不喜欢 Flatiron 所有模块的声音，所以我想要尝试使用不同的 ORM 和模板库。”

|  |
| --- |

#### 5.8.5\. 劣势

相比于其他一些框架，Flatiron 对于大型 MVC 风格的项目来说并不那么容易使用。例如，Sails 更容易设置。如果你正在创建几个中等大小的传统 Web 应用，Flatiron 可能会工作得很好。能够配置 Flatiron 是一个额外的优势，但请确保首先将其与其他选项进行比较评估。

LoopBack 是一个强大的竞争对手，它是本章中最后介绍的一个框架。

### 5.9\. LoopBack

LoopBack 是由 StrongLoop 创建的，该公司提供多种支持 Node 网络应用开发的商业服务。它被定位为一个 API 框架，但它具有使它与数据库和 MVC 应用很好地工作的功能。它甚至提供了一个用于探索和管理 REST API 的网络界面。如果你在寻找可以帮助为移动和桌面客户端创建 Web API 的东西，LoopBack 的功能是理想的。见 表 5.7 了解 LoopBack 的详细信息。

##### 表 5.7\. LoopBack 的功能

| **库类型** | API 框架 |
| --- | --- |
| **功能** | ORM、API 用户界面、WebSocket、客户端 SDK（包括 iOS） |
| **推荐用途** | 支持多个客户端（移动、桌面、Web）的 API |
| **插件架构** | Express 中间件 |
| **文档** | [`loopback.io/doc/`](http://loopback.io/doc/) |
| **流行度** | 6,500 GitHub 星标 |
| **许可证** | 双重许可：MIT 和 StrongLoop 订阅协议 |

LoopBack 是开源的，自从 StrongLoop 被 IBM 收购以来，该框架现在得到了主要商业认可。这使得它在 Node 社区中成为一个独特的提供物。它附带 Yeoman 生成器，可以快速设置应用程序骨架。在下一节中，您将看到如何创建新的 LoopBack 应用程序。

#### 5.9.1\. 设置

要设置新的 LoopBack 项目，您需要使用 StrongLoop 命令行工具([www.npmjs.com/package/strongloop](http://www.npmjs.com/package/strongloop))。全局安装 strongloop 包使得通过`slc`命令可用命令行工具。此包包括进程管理功能，但我们感兴趣的是 LoopBack 项目生成器：

```
npm install -g strongloop
slc loopback
```

StrongLoop 命令行工具会引导您完成设置新项目所需的步骤。输入项目名称，然后选择 api-server 应用程序骨架。当生成器完成安装项目的依赖项后，它将显示一些关于如何使用新项目的实用提示。图 5.2 显示了它应该看起来是什么样子。

##### 图 5.2\. LoopBack 的项目生成器

![](img/05fig02_alt.jpg)

要运行项目，请输入 `node .`，要创建模型，请使用 `slc loopback:model`。在设置新的 LoopBack 项目时，您将经常使用 `slc` 命令。

当项目运行时，您应该能够访问 API 探索器，网址为 http://0.0.0.0:3000/explorer/。点击 User 以展开 User 端点。您应该看到一个包含标准 RESTful 路由（如 PUT /Users 和 DELETE /Users/{id}）在内的可用 API 方法的大列表。图 5.3 显示了 API 探索器。

##### 图 5.3\. 强 Loop API 探索器显示 User 路由

![](img/05fig03_alt.jpg)

#### 5.9.2\. 定义路由

在 LoopBack 中，您可以在 Express 级别添加路由。添加一个名为 server/boot/routes.js 的新文件，并通过访问 LoopBack Router 实例来添加路由：

```
module.exports = (app) => {
  const router = app.loopback.Router();
  router.get('/hello', (req, res) => {
    res.send('Hello, world');
  });
  app.use(router);
};
```

访问 http://localhost:3000/hello 现在将响应*Hello, world*。然而，以这种方式添加路由在 LoopBack 项目中并不典型。这可能对于某些不寻常的 API 端点来说是必需的，但通常，当生成模型时，路由会自动添加。

#### 5.9.3\. REST API

在 LoopBack 项目中创建 REST API 的最简单方法是使用模型生成器。这是`slc`命令功能的一部分。例如，如果您想添加一个名为*product*的新模型，请运行`slc loopback:model`：

```
slc loopback:model product
```

`slc`命令会引导您完成创建模型的步骤，允许您选择模型是否为服务器端专用，并设置一些属性和验证器。添加模型后，查看相应的 JSON 文件——它应该在 common/models/product.json 中。这个 JSON 文件是一种轻量级的方式来定义模型的行为，包括您在上一步中指定的所有属性。

如果您想添加更多属性，请输入`slc loopback:property`。您可以在任何时候向模型添加属性。

|  |
| --- |

**人物想法**

*Phil*：“我们的团队非常喜欢 LoopBack，主要是因为它能够快速添加 RESTful 资源并通过 API 浏览器浏览它们。但我喜欢它，因为它看起来足够灵活，可以支持我们的遗留 MVC Web 应用。我们可以将其连接到旧数据库，并将这些项目迁移到 Node。”

*Alice*：“这是唯一真正针对 iOS、Android 以及丰富 Web 客户端的框架。LoopBack 为 iOS 和 Android 提供了客户端库，对我们这些依赖移动应用的产品开发者来说这是一个很大的优势。”

|  |
| --- |

#### 5.9.4\. 优点

即使从这简短的介绍中，也应该清楚 LoopBack 的一个优点是它消除了编写样板代码的需要。命令行工具生成您需要的几乎所有轻量级 RESTful Web API，甚至包括数据库模型和验证。同时，LoopBack 对前端代码的限制也不多。它还使您能够考虑哪些模型应该对浏览器可访问，哪些仅限于服务器端。一些框架在这方面做得不对，将所有内容都推送到浏览器。

如果你拥有需要与你的 Web API 通信的移动应用，请查看 LoopBack 的客户端 SDKs ([`loopback.io/doc/en/lb2/Client-SDKs.html`](http://loopback.io/doc/en/lb2/Client-SDKs.html))。LoopBack 支持 iOS 和 Android 的 API 集成和推送消息。

#### 5.9.5\. 缺点

LoopBack 基于 JSON 的模式 API 与大多数 JavaScript 数据库 API 不同。可能需要一段时间才能学会如何将其映射到现有项目的数据库模式。而且，由于 HTTP 层基于 Express，它在一定程度上受到 Express 支持的限制。尽管 Express 是一个可靠的 HTTP 服务器库，但 Node 现在有更多现代 API 的库可用。LoopBack 没有特定的插件 API。您可以使用 Express 中间件，但这不如 Flatiron 或 hapi 的插件 API 方便。

这结束了本章中涵盖的框架。在进入下一章之前，让我们比较这些框架，以帮助您决定哪个框架适合您的下一个项目。

### 5.10\. 比较

如果你一直关注本章中的人物想法，你可能已经决定使用哪个框架。如果没有，本章的其余部分将比较每个框架的优点。而且，如果你仍然感到困惑，图 5.4 将通过回答一些问题来帮助你选择正确的框架。

##### 图 5.4\. 选择 Node 框架

![](img/05fig04_alt.jpg)

如果你浏览一下 Node 流行的服务器端框架，它们听起来都很相似。它们提供轻量级的 HTTP API，并且使用服务器模型而不是 PHP 的页面模型。但它们设计上的差异对使用它们的项目的意义很大，因此为了比较这些框架，我们将从 HTTP 级别开始。

#### 5.10.1\. HTTP 服务器和路由

大多数 Node 框架都是基于 Connect 或 Express 构建的。在本章中，你已经看到了三个完全不基于 Express 的框架，它们有自己的 HTTP API 解决方案：Koa、hapi 和 Flatiron。

Koa 是由与 Express 相同的作者创建的，但通过使用更现代的 JavaScript 特性提供了新的方法。如果你喜欢 Express 但想使用 ES2015 生成器语法，Koa 可能适合你。

hapi 的服务器和路由 API 高度模块化，感觉与 Express 的不同。如果你觉得 Express 的语法不自然，你应该尝试一下 hapi。hapi 使得推理 HTTP 服务器变得更容易，所以如果你需要做诸如连接服务器或集群服务器的事情，你可能更喜欢 hapi 而不是 Express 的后代。

Flatiron 的路由器与 Express 向后兼容，但具有额外功能。路由器会发出事件并使用路由表。这与 Express 风格的中间件组件堆栈不同。你可以向 Flatiron 的路由器传递一个对象字面量。路由器也可以在浏览器中工作，所以如果你有试图处理现代客户端开发的服务器端开发者，他们可能会觉得 Flatiron 比使用 React Router 等工具更自在。

### 5.11\. 编写模块化代码

并非所有在本章中讨论的框架都直接支持插件，但它们都以某种方式可扩展。基于 Express 的框架可以使用 Connect 中间件，但 hapi 和 Flatiron 有自己的插件 API。定义良好的插件 API 很有用，因为它们使得框架的新用户更容易扩展它。

如果你使用的是像 Sails.js 或 LoopBack 这样的较大 MVC 框架，插件 API 使得设置新项目变得容易得多。LoopBack 通过提供高度功能的项目管理工具部分避免了需要插件 API。如果你查看 StrongLoop 的 npm 账户([www.npmjs.com/~strongloop](http://www.npmjs.com/~strongloop))，你会看到许多与 loopback 相关的项目，它们增加了对 Angular 和几个数据库的支持。

### 5.12\. 角色选择

本章中的人物角色现在已有足够的背景知识，可以为他们下一个项目做出正确的选择：

Phil: “最终我决定选择 LoopBack。这是一个艰难的选择，因为 Sails 和 Kraken 都拥有我们团队喜欢的出色功能，但我们感觉 LoopBack 有更强的长期支持，并且可以减少在服务器端开发上的大量努力。”

Nadine: “作为一个开源开发者，我选择了 Flatiron。它将适应我正在工作的各种项目。例如，一些项目将仅使用 Winston 和 Director，但其他项目将使用整个堆栈。”

Alice: “我已经为我的下一个项目选择了 hapi。它很简洁，因此我可以根据项目的独特需求进行适配。大部分代码将是 Node，并且不依赖于任何特定的框架，所以我感觉这与 hapi 很匹配。”

### 5.13\. 总结

+   Koa 轻量级、最小化，并使用 ES2015 生成器语法进行中间件。它适合托管依赖外部 Web API 的单页 Web 应用。

+   hapi 专注于 HTTP 服务器和路由。它适用于由许多小型服务组成的轻量级后端。

+   Flatiron 是一组解耦的模块，可以用作 Web MVC 框架或更轻量级的 Express 库。Flatiron 与 Connect 中间件兼容。

+   Kraken 基于 Express 构建，并增加了安全特性。它可以用于 MVC。

+   Sails.js 是一个受 Rails/Django 启发的 MVC 框架。它有一个 ORM 和一个模板系统。

+   DerbyJS 是一个适用于实时应用程序的同构框架。

+   LoopBack 消除了编写用于快速生成带有数据库支持和 API 浏览器的 REST API 的样板代码的需求。

## 第六章\. 深入理解 Connect 和 Express

*本章涵盖*

+   理解 Connect 和 Express 的用途

+   使用和创建中间件

+   创建和配置 Express 应用程序

+   使用关键的 Express 技术进行错误处理、渲染视图和表单

+   使用 Express 架构技术进行路由、REST API 和身份验证

在第三章中，您看到了如何构建一个简单的 Express 应用程序。本章提供了对 Express 和 Connect 的更深入研究。这两个流行的 Node 模块被许多 Web 开发者使用。本章向您展示了如何使用最常用的模式构建 Web 应用程序和 REST API。

|  |
| --- |

**Connect 和 Express**

下文中讨论的概念可以直接应用于高级框架 Express，因为它通过添加额外的更高级的糖来扩展和构建在 Connect 之上。阅读本节后，您将深刻理解 Connect 中间件的工作原理以及如何组合组件来创建一个应用程序。其他 Node 网络框架以类似的方式工作，因此学习 Connect 将在学习新框架时给您带来先机。

|  |
| --- |

首先，让我们看看如何创建一个基本的 Connect 应用程序。在本章的后面部分，您将看到如何使用流行的 Express 技术构建一个更复杂的 Express 应用程序。

### 6.1\. Connect

在本节中，您将了解*Connect*。您将看到如何使用其中间件来构建简单的 Web 应用程序，以及中间件排序的重要性。这将帮助您在以后构建更模块化的 Express 应用程序。

#### 6.1.1\. 设置 Connect 应用程序

Express 是用 Connect 构建的，但您知道您可以使用 Connect 单独创建一个功能齐全的 Web 应用程序吗？您可以通过以下命令从 npm 注册表中下载和安装 Connect：

```
$ npm install connect@3.4.0
```

这是一个最小化 Connect 应用程序的例子：

```
const app = require('connect')();
app.use((req, res, next) => {
  res.end('Hello, world!');
});
app.listen(3000);
```

这个简单的应用程序（在示例代码的 ch06-connect-and-express/hello-world 下找到）将响应*Hello, world!*传递给 app.use 的函数是一个中间件组件，通过发送*Hello, world!*文本作为响应来结束请求。中间件组件是所有 Connect 和 Express 应用程序的基础。让我们更详细地看看它们。

#### 6.1.2\. 理解 Connect 中间件的工作原理

在 Connect 中，一个 *中间件组件* 是一个 JavaScript 函数，按照惯例接受三个参数：一个请求对象、一个响应对象，以及一个通常命名为 `next` 的参数，它是一个回调函数，表示该组件已完成，可以执行后续的中间件组件。

在你的中间件运行之前，Connect 使用一个调度器，它接收请求并将它们传递给第一个添加到应用程序中的中间件组件。图 6.1 展示了一个典型的 Connect 应用程序，它由调度器以及包括日志记录器、体解析器、静态文件服务器和自定义中间件的中间件排列组成。

##### 图 6.1\. 两个 HTTP 请求通过 Connect 服务器的生命周期

![](img/06fig01_alt.jpg)

如你所见，中间件 API 的设计意味着更复杂的行为可以由更小的构建块组成。在下一节中，你将看到如何通过组合组件来实现这一点。

#### 6.1.3\. 组合中间件

Connect 提供了一个名为 `use` 的方法来组合中间件组件。让我们定义两个中间件函数并将它们都添加到应用程序中。一个是之前简单的 Hello World 函数，另一个是日志记录器。

##### 列表 6.1\. 使用多个 Connect 中间件组件

![](img/06lis01_alt.jpg)

这个中间件有两个签名：一个带有 `next`，另一个不带。这是因为该组件完成 HTTP 响应，并且永远不需要将控制权交还给调度器。

`use()` 函数返回一个 Connect 应用程序的实例以支持方法链式调用，如前所述。请注意，链式调用 `.use()` 调用不是必需的，如下面的代码片段所示：

```
const app = connect();
app.use(logger);
app.use(hello);
app.listen(3000);
```

现在你已经有一个简单的 Hello World 应用程序运行起来，我们将探讨为什么中间件 `.use()` 调用的顺序很重要，以及如何战略性地使用这种顺序来改变应用程序的工作方式。

#### 6.1.4\. 中间件排序

应用程序中中间件的顺序可以极大地影响其行为。可以通过省略 `next()` 来停止执行，并且可以将中间件组合起来实现如身份验证等功能。

当中间件组件没有调用 `next` 时会发生什么？考虑之前的 Hello World 示例，其中 `logger` 中间件组件首先使用，然后是 `hello` 组件。在那个例子中，Connect 将日志记录到 stdout 并响应 HTTP 请求。但考虑如果顺序被切换，如下所示会发生什么。

##### 列表 6.2\. 错误：`hello` 中间件组件在 `logger` 组件之前

![](img/06lis02_alt.jpg)

在这个例子中，`hello`中间件组件首先被调用，并按预期响应 HTTP 请求。但是`logger`从未被调用，因为`hello`从未调用`next()`，所以控制权从未返回给调度器以调用下一个中间件组件。这里的教训是，当一个组件没有调用`next()`时，命令链中的任何剩余中间件都不会被调用。

![图 6.2](img/#ch06fig02)显示了此示例将跳过日志的情况以及如何纠正它。

##### 图 6.2\. 中间件的顺序很重要。

![图片](img/06fig02_alt.jpg)

如你所见，将`hello`放在`logger`之前是相当无用的，但使用得当，顺序可以为你带来好处。

#### 6.1.5\. 创建可配置的中间件

你已经学了一些中间件的基础知识；现在我们将深入探讨如何创建更通用和可重用的中间件。

中间件通常遵循一个简单的约定，以便为开发者提供配置能力：使用一个返回另一个函数（闭包）的函数。这种类型的可配置中间件的基本结构看起来像这样：

![图片](img/p0112_01.jpg)

这种类型的中间件的使用方法如下：

```
app.use(setup({ some: 'options' }));
```

注意，`setup`函数在`app.use`行中被调用，而在之前的示例中，你只是传递了一个函数的引用。

在本节中，你将应用这种技术来构建三个可重用和可配置的中间件组件：

+   一个具有可配置打印格式的日志组件

+   一个基于请求 URL 调用函数的路由组件

+   一个将 URL 别名转换为 ID 的 URL 重写组件

你将从扩展你的日志组件开始，使其更具可配置性。本章前面创建的`logger`中间件组件不是可配置的。它在调用时硬编码为打印出请求的`req.method`和`req.url`。但如果你想在未来的某个时刻改变日志显示的内容呢？

在实践中，使用可配置的中间件就像使用你迄今为止创建的任何中间件一样，只是你可以向中间件组件传递额外的参数来改变其行为。在你的应用程序中使用可配置组件可能看起来像以下示例，其中`logger`可以接受一个字符串，描述它应该打印的格式：

```
const app = connect()
  .use(logger(':method :url'))
  .use(hello);
```

要实现可配置的`logger`组件，你首先需要定义一个`setup`函数，它接受一个字符串参数（在这个例子中，你将命名为`format`）。当`setup`被调用时，返回一个函数，这是 Connect 将使用的中间件组件。返回的组件保留了访问`format`变量的权限，即使在`setup`函数返回之后，因为它是在同一个 JavaScript 闭包中定义的。然后`logger`将`format`字符串中的占位符替换为`req`对象上关联的请求属性，记录到标准输出，并调用`next()`，如下面的列表所示。

##### 列表 6.3\. Connect 的可配置`logger`中间件组件

![](img/06lis03_alt.jpg)

因为你已经将这个`logger`中间件组件创建为可配置的中间件，所以你可以在单个应用程序中多次使用`.use()`来配置 logger，或者在不同的应用程序中重用这个 logger 代码。这个简单的可配置中间件概念在整个 Connect 社区中被使用，并且用于所有核心 Connect 中间件以保持一致性。

要在列表 6.3 中使用 logger 中间件，你需要传递一个字符串，该字符串包含请求对象上的一些属性。例如，`.use(setup(':method :url'))`会打印出每个请求的 HTTP 方法（`GET`、`POST`等）和 URL。

在继续到 Express 之前，让我们看看 Connect 是如何支持错误处理的。

#### 6.1.6\. 使用错误处理中间件

所有应用程序都会出现错误，无论是在系统级别还是用户级别，为错误情况做好准备——即使是你没有预料到的——也是一件明智的事情。Connect 实现了一个遵循与常规中间件相同规则的错误处理变体中间件，它接受一个错误对象以及请求和响应对象。

Connect 的错误处理故意保持最小化，允许开发者指定错误处理的方式。例如，你可以只通过中间件传递系统错误和应用程序错误（例如，*foo 未定义*）或用户错误（*密码无效*）或两者的组合。Connect 让你选择最适合你应用程序的选项。

在本节中，你将使用这两种类型，并学习错误处理中间件的工作方式。你还将学习一些有用的模式，这些模式可以在我们查看以下内容时应用：

+   使用 Connect 的默认错误处理器

+   自行处理应用程序错误

+   使用多个错误处理中间件组件

让我们直接看看 Connect 在没有任何配置的情况下是如何处理错误的。

##### 使用 Connect 的默认错误处理器

考虑以下中间件组件，它将抛出一个`ReferenceError`错误，因为应用程序没有定义`foo()`函数：

```
const connect = require('connect')
connect()
  .use((req, res) => {
    foo();
    res.setHeader('Content-Type', 'text/plain');
    res.end('hello world');
  })
.listen(3000)
```

默认情况下，Connect 将以 500 状态码响应，包含文本*内部服务器错误*的响应体以及有关错误本身更多的信息。这很好，但在任何类型的实际应用程序中，你可能希望对那些错误进行更专业的处理，例如将它们发送到日志守护进程。

##### 自行处理应用程序错误

Connect 还提供了一种方法，让您可以使用错误处理中间件自行处理应用程序错误。例如，在开发过程中，您可能希望向客户端返回错误的 JSON 表示形式，以便快速轻松地报告，而在生产环境中，您可能希望返回一个简单的 *服务器错误*，以免向潜在的攻击者暴露敏感的内部信息（如堆栈跟踪、文件名和行号）。

错误处理中间件函数必须定义以接受四个参数——`err`、`req`、`res` 和 `next`——如 列表 6.4 所示，而常规中间件则接受 `req`、`res` 和 `next` 参数。以下列表显示了一个示例错误中间件。要查看带有服务器的完整示例，请参阅本书源代码中的 ch06-connect-and-express/listing6_4。

##### 列表 6.4\. Connect 中的错误处理中间件

![](img/06lis04_alt.jpg)

|  |
| --- |

##### 使用 NODE_ENV 设置应用程序的模式

Connect 的一个常见约定是使用 `NODE_ENV` 环境变量 (`process.env.NODE_ENV`) 在服务器环境之间切换行为，例如生产环境和开发环境。

|  |
| --- |

当 Connect 遇到错误时，它将切换到仅调用错误处理中间件，正如您可以在 图 6.3 中看到的那样。

##### 图 6.3\. 在 Connect 服务器中引发错误的 HTTP 请求的生命周期

![](img/06fig03_alt.jpg)

假设您有一个允许人们登录博客管理区的应用程序。如果用户路由的中间件组件引发错误，则博客和 admin 中间件组件都将被跳过，因为它们不作为错误处理中间件——它们只定义了三个参数。然后 Connect 将看到 `errorHandler` 接受错误参数，并将调用它。中间件组件可能看起来像这样：

```
connect()
  .use(router(require('./routes/user')))
  .use(router(require('./routes/blog'))) // Skipped
  .use(router(require('./routes/admin'))) // Skipped
  .use(errorHandler);
```

基于中间件执行进行短路的功能是组织 Express 应用程序的基本概念。现在您已经了解了 Connect 的基础知识，是时候更详细地了解 Express 了。

### 6.2\. 表达

*Express* 是一个流行的 Web 框架，最初建立在 Connect 之上，但仍然与 Connect 中间件兼容。尽管 Express 提供了基本功能，如静态文件服务、URL 路由和应用配置，但它仍然非常简洁。它提供了足够的结构，以便您可以在不限制开发实践的情况下组合可重用的块。

在接下来的几节中，您将通过使用 Express 骨干应用程序生成器来实现一个新的 Express 应用程序。这个过程比第三章中的简要概述更详细，因此到本章结束时，您应该对 Express 有足够的了解，可以构建自己的 Express Web 应用程序和 RESTful API。随着本章的继续，您将不断向骨架添加功能，最终生成一个完整的应用程序。

#### 6.2.1\. 生成应用程序骨架

Express 不会强迫开发者使用特定的应用程序结构。你可以将路由放在你想要的任何文件中，将公共资产放在你想要的任何目录中，等等。一个最小的 Express 应用程序可以小到以下列表所示，它实现了一个完全功能的 HTTP 服务器。

##### 列表 6.5。一个最小的 Express 应用程序

![图片](img/06lis05_alt.jpg)

express-generator 包中提供的`express(1)`命令行工具（[www.npmjs.com/package/express-generator](http://www.npmjs.com/package/express-generator)）可以为你设置应用程序骨架。如果你是 Express 的新手，使用生成的应用程序是一个很好的开始方式，因为它会设置一个包含模板、公共资产、配置等的应用程序。

`express(1)`生成的默认应用程序骨架仅包含几个目录和文件，如图 6.4 所示。这种结构旨在让开发者能在几秒钟内开始使用 Express，但应用程序的结构完全由你和你的团队来创建。

##### 图 6.4。使用 EJS 模板的默认应用程序骨架结构

![图片](img/06fig04.jpg)

本章的示例使用嵌入式 JavaScript（EJS）模板，其结构与 HTML 相似。EJS 类似于 PHP、JSP（用于 Java）和 ERB（用于 Ruby），因为服务器端 JavaScript 嵌入在 HTML 文档中并在发送到客户端之前执行。你将在第七章中更详细地了解 EJS。

在本节中，你将执行以下操作：

+   使用 npm 全局安装 Express

+   生成应用程序

+   探索应用程序并安装依赖项

让我们开始吧。

##### 安装 Express 可执行文件

首先，使用 npm 全局安装 express-generator：

```
$ npm install -g express-generator
```

接下来，你可以使用`--help`标志来查看可用的选项，如图 6.5 所示。

##### 图 6.5。Express 帮助

![图片](img/06fig05.jpg)

其中一些选项会为你生成应用程序的小部分。例如，你可以指定一个模板引擎来为所选模板引擎生成一个占位符模板文件。同样，如果你使用`--css`选项指定了一个 CSS 预处理器，将为它生成一个占位符模板文件。

可执行文件安装完成后，让我们看看如何生成将成为照片应用程序的内容。

##### 生成应用程序

对于这个应用程序，你使用`-e`（或`--ejs`）标志来使用 EJS 模板引擎。执行`express -e shoutbox`。如果你想复制我们 GitHub 仓库中的代码示例，使用`express -e listing6_6`。

一个完全功能的应用程序在 shoutbox 目录中创建。它包含一个 package.json 文件来描述项目和依赖项，应用程序文件本身，公共文件目录，以及路由处理程序的目录。

##### 探索应用程序

让我们更仔细地看看生成了什么。在您的编辑器中打开 package.json 文件，查看应用程序的依赖项，如图 6.6 所示。Express 无法猜测你想要的依赖项版本，因此提供模块的主版本、次版本和补丁级别是良好的实践，这样你就不太可能引入任何意外错误。例如，`"express": "~4.13.1"` 是明确的，并且将在每次安装时提供相同的代码。

##### 图 6.6\. 生成的 package.json 内容

![图片](img/06fig06.jpg)

现在看看由 `express(1)` 生成的应用程序文件，如下所示。目前，你可以保持这个文件不变。你应该熟悉本章早期 Connect 部分中的这些中间件组件，但看看默认中间件配置是如何设置的也是值得的。

##### 列表 6.6\. 生成的 Express 应用程序骨架

![图片](img/ch06ex06-0.jpg)

![图片](img/ch06ex06-1.jpg)

你有 package.json 和 app.js 文件，但应用程序还不能运行，因为依赖项尚未安装。每次你从 `express(1)` 生成 package.json 文件时，都需要安装依赖项。执行 `npm install` 来完成此操作，然后执行 `npm start` 来运行应用程序。

通过在浏览器中访问 http://localhost:3000 来检查应用程序。默认应用程序看起来像 图 6.7 中的那样。

##### 图 6.7\. 默认 Express 应用程序

![图片](img/06fig07.jpg)

现在你已经看到了生成的骨架，你可以开始构建一个真实的 Express 应用程序。该应用程序将是一个允许人们发布消息的 shoutbox。在构建此类应用程序时，大多数经验丰富的 Express 开发者会先规划他们的 API，因此需要所需的路线和资源。

##### 规划 shoutbox 应用程序

这是 shoutbox 应用程序的要求：

1.  它应该允许用户注册账户、登录和登出。

1.  用户应该能够发布消息（条目）。

1.  网站访客应该能够分页浏览条目。

1.  应该有一个简单的 REST API 来支持身份验证。

你需要存储数据并处理身份验证。你还需要验证用户输入。必要的路由看起来像这样：

+   API 路由

+   GET /api/entries: 获取条目列表

+   GET /api/entries/page: 获取单个页面的条目

+   POST /api/entry: 创建新的 shoutbox 条目

+   Web UI 路由

+   GET /post: 新条目的表单

+   POST /post: 发布新条目

+   GET /register: 显示注册表单

+   POST /register: 创建新账户

+   GET /login: 显示登录表单

+   POST /login: 登录

+   GET /logout: 登出

这种布局与大多数 Web 应用程序类似。希望你能将本章中的示例作为你未来应用程序的模板。

在前面的列表中，你可能注意到了对 `app.set` 的调用：

```
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');
```

这就是 Express 应用程序的配置方式。下一节将更详细地解释 Express 的配置。

#### 6.2.2\. 配置 Express 和你的应用程序

你的应用程序需求将取决于其运行的环境。例如，当你的产品处于开发阶段时，你可能希望有详细的日志记录，而当它处于生产阶段时，你可能希望有一组更精简的日志和 gzip 压缩。除了配置特定于环境的功能外，你可能还想定义一些应用程序级别的设置，以便 Express 知道你正在使用哪个模板引擎以及它可以在哪里找到模板。Express 还允许你定义自定义配置键/值对。

|  |
| --- |

**设置环境变量**

要在 UNIX 系统中设置环境变量，你可以使用以下命令：

```
$ NODE_ENV=production node app
```

在 Windows 系统中，你可以使用以下代码：

```
$ set NODE_ENV=production
$ node app
```

这些环境变量将在你的应用程序的`process.env`对象中可用。

|  |
| --- |

Express 有一个基于环境的最小配置系统，由几个方法组成，这些方法都由`NODE_ENV`环境变量驱动：

+   `app.set()`

+   `app.get()`

+   `app.enable()`

+   `app.disable()`

+   `app.enabled()`

+   `app.disabled()`

在本节中，你将了解如何使用配置系统来自定义 Express 的行为方式，以及如何在开发过程中使用此系统满足自己的目的。

让我们更详细地看看基于环境的配置意味着什么。尽管`NODE_ENV`环境变量起源于 Express，但许多其他 Node 框架都采用了它作为通知 Node 应用程序其操作环境的手段，默认为开发环境。

`app.configure()`方法接受表示环境的可选字符串和一个函数。当环境与传递的字符串匹配时，回调函数立即被调用；当只提供一个函数时，它将在所有环境中被调用。这些环境名称完全是任意的。例如，你可能会有`development`、`stage`、`test`和`production`，或者简称为`prod`：

```
if (app.get('env') === 'development') {
  app.use(express.errorHandler());
}
```

Express 内部使用配置系统，允许你自定义 Express 的行为方式，但它也适用于你自己的使用。

Express 还提供了`app.set()`和`app.get()`的布尔变体。例如，`app.enable(setting)`等同于`app.set(setting, true)`，而`app.enabled (setting)`可以用来检查值是否已启用。`app.disable (setting)`和`app.disabled(setting)`方法与真实变体互补。

在使用 Express 开发 API 时，一个有用的设置是`json spaces`选项。如果你将其添加到你的 app.js 文件中，你的 JSON 将以更可读的格式打印出来：

```
app.set('json spaces', 2);
```

现在你已经看到了如何利用配置系统来满足自己的需求，让我们看看如何在 Express 中渲染视图。

#### 6.2.3\. 渲染视图

在本章的应用中，你将使用 EJS 模板，尽管如前所述，Node 社区中的几乎任何模板引擎都可以使用。如果你不熟悉 EJS，不要担心。它与在其他 Web 开发平台（PHP、JSP、ERB）中找到的模板语言类似。我们在本章中介绍了 EJS 的一些基础知识，但在第七章中我们将更详细地讨论 EJS 和几个其他模板引擎。第七章。

无论是在渲染整个 HTML 页面、HTML 片段还是 RSS 源，对于几乎所有应用来说，渲染视图都是至关重要的。这个概念很简单：你将数据传递给一个 *视图*，然后这些数据被转换，通常是转换为 HTML 以供 Web 应用使用。你很可能熟悉视图的概念，因为大多数框架都提供了类似的功能；图 6.8 说明了视图如何为数据形成新的表示。

##### 图 6.8\. HTML 模板加数据 = 数据的 HTML 视图

![图片](img/06fig08.jpg)

生成模板 6.8 的模板可以在以下片段中找到：

```
<h1><%= name %></h1>
<p><%= name %> is a 2 year old <%= species %>.</p>
```

Express 提供了两种渲染视图的方式：在应用级别使用 `app.render()`，以及在响应中使用 `res.render()`，后者内部使用前者。在本章中，你将只使用 `res.render()`。如果你查看 ./routes/index.js，会发现定义了一个函数，它调用 `res.render('index')` 以渲染 ./views/index.ejs 模板，如下面的代码（在列表 6_8 中找到）所示：

```
router.get('/', (req, res, next) => {
  res.render('index', { title: 'Express' });
});
```

在更详细地查看 `res.render()` 之前，让我们看看如何配置视图系统。

##### 配置视图系统

配置 Express 视图系统很简单。尽管 `express(1)` 为你生成了配置，但了解幕后发生的事情仍然很有用，这样你就可以进行更改。我们将重点关注三个区域：

+   调整视图查找

+   配置默认模板引擎

+   启用视图缓存以减少文件 I/O

首先是 `views` 设置。

##### 更改查找目录

以下片段显示了 Express 可执行文件创建的 `views` 设置：

```
app.set('views', __dirname + '/views');
```

这指定了 Express 在视图查找期间将使用的目录。使用 `__dirname` 是一个好主意，这样你的应用程序就不会依赖于当前工作目录是应用程序的根目录。

|  |
| --- |

**__dirname**

`__dirname`（带有两个前置下划线）是 Node 中的一个全局变量，用于标识当前运行文件所在的目录。通常在开发中，这个目录将与你的当前工作目录（CWD）相同，但在生产中，Node 可执行文件可能从另一个目录运行。使用 `__dirname` 有助于在不同环境中保持路径的一致性。

| |
| --- |

下一个设置是 `view engine`。

##### 使用默认模板引擎

当`express(1)`生成应用程序时，`view engine`设置被分配为`ejs`，因为 EJS 是`-e`命令行选项选择的模板引擎。这个设置使你能够渲染`index`而不是 index.ejs。否则，Express 需要扩展名来确定要使用哪个模板引擎。

你可能想知道为什么 Express 甚至考虑扩展名。扩展名的使用允许你在单个 Express 应用程序中使用多个模板引擎，同时为常见用例提供干净的 API，因为大多数应用程序将使用一个模板引擎。

例如，如果你发现使用另一个模板引擎编写 RSS 源更容易，或者你可能正在从一个模板引擎迁移到另一个模板引擎。你可能将 Pug 作为默认引擎，将 EJS 用于/feeds 路由，如下面的代码所示，通过.ejs 扩展名：

```
app.set('view engine', 'pug');
app.get('/', function(){
  res.render('index');
 });
app.get('/feed', function(){
  res.render('rss.ejs')
;
});
```

| |
| --- |

##### 保持 package.json 同步

请记住，你希望使用的任何额外的模板引擎都应该添加到你的 package.json 依赖对象中。尽量记得使用`npm install --save package-name`来安装包。使用`npm uninstall --save package-name`来删除它们，从而从 node_modules 和 package.json 中移除。这使你在尝试确定要使用哪个模板引擎时，实验不同的模板引擎变得更加容易。

| |
| --- |

##### 视图缓存

在生产环境中，默认启用`view cache`设置，防止后续的`render()`调用执行磁盘 I/O。模板的内容保存在内存中，大大提高了性能。启用此设置的副作用是，你将无法在不重新启动服务器的情况下编辑模板文件，这就是为什么它在开发中是禁用的。如果你正在运行预发布环境，你可能会想要启用此选项。

如图 6.9 所示，当禁用`view cache`时，每次请求都会从磁盘读取模板。这就是为什么你可以修改模板而无需重新启动应用程序的原因。当启用`view cache`时，每个模板只访问磁盘一次。

##### 图 6.9\. 视图缓存设置

![](img/06fig09_alt.jpg)

你已经看到了视图缓存机制如何帮助在非开发环境中提高性能。现在让我们看看 Express 是如何定位视图以便渲染的。

##### 视图查找

查找视图的过程类似于 Node 的`require()`工作方式。当调用`res.render()`或`app.render()`时，Express 首先检查在绝对路径下是否存在文件。接下来，Express 在视图目录中查找。最后，Express 尝试索引文件。这个过程在图 6.10 中用流程图表示。

##### 图 6.10\. 表达视图查找过程

![](img/06fig10.jpg)

因为`ejs`被设置为默认引擎，所以渲染调用省略了.ejs 扩展名，模板文件仍然可以正确解析。

随着应用程序的发展，你可能需要更多的视图，有时甚至需要为单个资源创建多个视图。使用 `view lookup` 可以帮助组织——例如，你可以使用与资源相关的子目录，并在其中创建视图。

添加子目录允许你消除名称中的冗余部分（如 edit-entry.ejs 和 show-entry.ejs）。然后 Express 添加 `view engine` 扩展，并将 `res.render('entries/edit')` 解析为 ./views/entries/edit.ejs。

Express 会检查视图目录的子目录中是否存在名为 *index* 的文件。当文件以复数资源命名，如 *entries* 时，这通常意味着资源列表。这意味着你可以使用 `res.render('entries')` 来渲染 views/entries/index.ejs 中的文件。

##### 数据展示给视图的方法

你已经看到了如何直接将局部变量传递给 `res.render()` 调用，但你也可以使用一些其他机制来完成这个任务。例如，你可以使用 `app.locals` 来存储应用级别的变量，以及 `res.locals` 来存储请求级别的局部变量，这些变量通常由中间件组件在渲染视图的最终路由处理方法之前设置。 

直接传递给 `res.render()` 的值优先于在 `res.locals` 和 `app.locals` 中设置的值，如 图 6.11 所示。

##### 图 6.11\. 在渲染模板时，直接传递给 `render` 函数的值具有优先级。

![Images/06fig11.jpg]

默认情况下，Express 只向视图暴露一个应用级别的变量 `settings`，它是一个包含所有使用 `app.set()` 设置的值的对象。例如，使用 `app.set('title', 'My Application')` 会在模板中暴露 `settings.title`，如下面的 EJS 片段所示：

```
<html>
  <head>
     <title><%= settings.title %></title>
  </head>
  <body>
    <h1><%= settings.title %></h1>
    <p>Welcome to <%= settings.title %>.</p>
  </body>
```

在内部，Express 使用以下 JavaScript 暴露此对象：

```
app.locals.settings = app.settings;
```

就这些了！现在你已经看到了如何渲染视图并将数据发送给它们，让我们看看如何定义路由，并了解如何编写可以为 shoutbox 应用程序渲染视图的路由处理程序。你还将设置数据库模型以持久化数据。

#### 6.2.4\. Express 路由 101

Express 路由的主要功能是将 URL 模式与响应逻辑配对。但路由也可以将 URL 模式与中间件配对。这允许你使用中间件为某些路由提供可重用的功能。

在本节中，你将执行以下操作：

+   使用路由特定的中间件验证用户提交的内容

+   实现路由特定的验证

+   实现分页

让我们探索一些使用路由特定中间件的方法。

##### 验证用户内容提交

为了让你有东西可以应用验证，你最终将添加到 shoutbox 应用程序中发布的能力。为了添加发布的能力，你需要做几件事情：

+   创建条目模型

+   添加与条目相关的路由

+   创建条目表单

+   添加逻辑以使用提交的表单数据创建条目

你将首先创建一个条目模型。

##### 创建条目模型

在继续之前，你需要将 Node redis 模块安装到项目中。使用 `npm install --save redis` 安装它。如果你没有安装 Redis，请访问 [`redis.io/`](http://redis.io/) 了解如何安装它；如果你使用 macOS，你可以很容易地使用 Homebrew ([`brew.sh/`](http://brew.sh/)) 安装它，Windows 有 Redis Chocolatey 软件包 ([`chocolatey.org/`](https://chocolatey.org/))。

我们使用 Redis 来稍微作弊一下：Redis 的功能和 ES6 使得在没有复杂数据库库的情况下创建轻量级模型变得容易。如果你有雄心壮志，可以使用另一个数据库库（有关 Node 中的数据库的更多信息，请参阅第八章）。

让我们看看如何创建一个轻量级模型来存储你的喊话条目。创建一个文件来包含条目模型定义，在 models/entry.js 中。将以下列表中的代码添加到此文件。条目模型将是一个简单的 ES6 类，它将数据保存到 Redis 列表中。

##### 列表 6.7\. 条目模型

![](img/06lis07_alt.jpg)

在基本模型完善之后，你现在需要添加一个名为 `getRange` 的函数，使用以下列表的内容。此函数将允许你检索条目。

##### 列表 6.8\. 获取条目范围的逻辑

![](img/06lis08_alt.jpg)

在创建了一个模型之后，你现在可以添加创建和列出条目的路由。

##### 创建条目表单

应用程序具有列出条目的能力，但没有添加条目的方法。你将在下一个步骤中添加此功能，首先在 app.js 的路由部分添加以下行：

```
app.get('/post', entries.form);
app.post('/post', entries.submit);
```

接下来，将以下路由添加到 routes/entries.js 中。此路由逻辑将渲染包含表单的模板：

```
exports.form = (req, res) => {
  res.render('post', { title: 'Post' });
};
```

接下来，使用以下列表中的 EJS 模板创建表单模板并将其保存到 views/post.ejs 中。

##### 列表 6.9\. 输入帖子数据的表单

![](img/06lis09_alt.jpg)

此表单使用如 `entry[title]` 这样的输入名称，因此需要扩展体解析。要更改体解析器，打开 app.js，并移动到读取

```
app.use(bodyParser.urlencoded({ extended: false }));
```

将此更改为使用扩展解析：

```
app.use(bodyParser.urlencoded({ extended: true }));
```

在处理表单显示之后，让我们继续从提交的表单数据创建条目。

##### 实现条目创建

要添加从提交的表单数据创建条目的功能，请将以下列表中的逻辑添加到文件 routes/entries.js 中。此逻辑将在表单数据提交时添加条目。

##### 列表 6.10\. 使用提交的表单数据添加条目

![](img/06lis10_alt.jpg)

现在你使用浏览器访问应用程序上的 /post，你将能够添加条目。你将在 列表 6.21 中处理强制用户先登录。

在处理完帖子内容后，现在是时候渲染条目列表了。

##### 添加条目的前页显示

首先创建文件 routes/entries.js。然后添加以下列表中的代码，以导入条目模型并导出一个用于渲染条目列表的函数。

##### 列表 6.11\. 列出条目

![](img/06lis11_alt.jpg)

在为列出条目定义路由逻辑后，您现在需要添加一个 EJS 模板来显示它们。在 views 目录中创建一个名为 entries.ejs 的文件，并将以下 EJS 放入其中。

##### 列表 6.12\. entries.ejs 视图

```
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
  </head>
  <body>
    <% include menu %>
    <% entries.forEach((entry) => { %>
      <div class='entry'>
        <h3><%= entry.title %></h3>
        <p><%= entry.body %></p>
        <p>Posted by <%= entry.username %></p>
      </div>
    <% }) %>
  </body>
</html>
```

在运行应用程序之前，运行 `touch views/menu.ejs` 以创建一个临时文件，该文件将在稍后阶段保存菜单。当视图和路由准备就绪时，您需要告诉应用程序在哪里找到路由。

##### 添加与条目相关的路由

在您将条目相关的路由添加到应用程序之前，您需要对 app.js 进行修改。首先，在您的 app.js 文件顶部添加以下 `require` 语句：

```
const entries = require('./routes/entries');
```

接下来，也在 app.js 中，将包含文本 `app.get('/')` 的行更改为以下内容，以便任何对路径 `/` 的请求都返回条目列表：

```
app.get('/', entries.list);
```

当您运行应用程序时，主页将显示条目列表。现在，条目可以创建和列出，让我们继续使用特定路由的中间件来验证表单数据。

##### 使用特定路由的中间件

假设您希望帖子表单中的条目文本字段是必需的。您可能首先想到的解决这个问题的方法是在您的路由回调中直接添加它，如下面的代码片段所示。然而，这种方法并不理想，因为它将验证逻辑紧密地绑定到这个特定的表单上。在许多情况下，验证逻辑可以被抽象成可重用的组件，使开发更容易、更快、更声明性：

```
...
exports.submit = (req, res, next) => {
  let data = req.body.entry;
  if (!data.title) {
    res.error('Title is required.');
    res.redirect('back');
    return;
  }
  if (data.title.length < 4) {
    res.error('Title must be longer than 4 characters.');
    res.redirect('back');
    return;
  }
...
```

Express 路由可以可选地接受自己的中间件，仅在匹配该路由时应用，在最终的路线回调之前。您在本章中使用的路线回调本身并没有被特别处理。它们与其他中间件相同，甚至包括您即将为验证创建的中间件！

让我们从查看一种简单但不够灵活的实现验证作为特定路由中间件的方法开始。

##### 使用特定路由的中间件进行表单验证

第一种可能性是编写一些简单但具体的中间件组件来执行验证。通过扩展 `POST /post` 路由使用此中间件可能看起来像以下这样：

```
app.post('/post',
  requireEntryTitle,
  requireEntryTitleLengthAbove(4),
  entries.submit
);
```

注意，这个路由定义，通常只有路径和路由逻辑作为参数，有两个额外的参数指定验证中间件。

下面的列表中的两个示例中间件组件说明了如何抽象出原始验证。但它们仍然不是模块化的，并且只为单个字段 `entry[title]` 工作。

##### 列表 6.13\. 两个更多潜在的但并不完美的验证中间件尝试

```
function requireEntryTitle(req, res, next) {
  const title = req.body.entry.title;
  if (title) {
    next();
  } else {
    res.error('Title is required.');
    res.redirect('back');
  }
}
function requireEntryTitleLengthAbove(len) {
  return (req, res, next) => {
    const title = req.body.entry.title;
    if (title.length > len) {
      next();
    } else {
      res.error(`Title must be longer than ${len}.`);
      res.redirect('back');
    }
  };
}
```

一个更可行的解决方案是抽象验证器并传递目标字段名。让我们看看如何以这种方式接近它。

##### 构建灵活的验证中间件

你可以传递字段名，如下面的片段所示。这允许你重用验证逻辑，减少你需要编写的代码量：

```
app.post('/post',
          validate.required('entry[title]'),
          validate.lengthAbove('entry[title]', 4),
          entries.submit);
```

将 app.js 路由部分中的`app.post('/post', entries.submit);`行替换为以下片段。值得注意的是，Express 社区已经为公众创建了众多类似的库，但了解验证中间件的工作原理以及如何编写自己的中间件是无价的。

让我们继续前进。使用程序代码在列表 6.14 中创建一个名为./middleware/validate.js 的文件。在 validate.js 中，你将导出几个中间件组件——在这种情况下，`validate.required()`和`validate.lengthAbove()`。实现细节并不重要；这个例子要说明的是，如果代码在应用程序中是通用的，那么一点小小的努力就能走得很远。

##### 列表 6.14\. 验证中间件实现

![图片](img/ch06ex14-0.jpg)

![图片](img/ch06ex14-1.jpg)

要使此中间件对应用程序可用，请在 app.js 顶部添加以下行：

```
const validate = require('./middleware/validate');
```

如果你现在尝试应用程序，你会发现验证将生效。这个验证 API 可以变得更加流畅，但我们将其留给你去调查。

#### 6.2.5\. 验证用户

在本节中，你将从零开始为应用程序创建一个身份验证系统。你将经历以下步骤：

+   实现存储和验证已注册用户的逻辑

+   添加账户注册功能

+   允许人们登录

+   创建并使用中间件来加载用户

你将继续使用 Redis 来实现用户账户。现在让我们看看如何创建一个用户模型，以便在 Node 代码中更轻松地使用 Redis。

##### 保存和加载用户记录

在本节中，你将实现用户加载、保存和身份验证。你将执行以下操作：

+   使用 package.json 文件定义应用程序依赖项

+   创建用户模型

+   使用 Redis 添加加载和保存用户数据的逻辑

+   使用 bcrypt 安全地存储用户密码

+   添加验证登录尝试的逻辑

*Bcrypt* 是一个加盐散列函数，作为一个专门为散列密码设计的第三方模块提供。Bcrypt 非常适合密码，因为它包含一个迭代计数参数，使其随着时间的推移而变慢。

在继续之前，将 bcrypt 添加到你的 shoutbox 项目中：

```
npm install --save redis bcrypt
```

##### 创建用户模型

你现在需要创建一个用户模型。在 models/目录中添加一个名为 user.js 的文件。

列表 6.15 是用户模型。在这段代码中，需要`redis`和`bcrypt`依赖项，然后使用`redis.createClient()`打开一个 Redis 连接。`User`函数接受一个对象，并将该对象的属性合并到其自身。例如，`new User({ name: 'tobi' })`创建一个对象，并将对象的`name`属性设置为`Tobi`。

##### 列表 6.15\. 开始创建用户模型

![图片](img/06lis15_alt.jpg)

目前，用户模式只是一个占位符。你需要添加创建和更新用户记录的方法。

##### 将用户保存到 Redis 中

下一个你需要的功能是保存用户的能力，使用 Redis 存储用户数据。在 列表 6.16 中显示的 `save` 方法检查用户是否已经有了一个 ID，如果有，则 `save` 调用 `update` 方法，按名称索引用户 ID，并用对象的属性填充 Redis 哈希。否则，没有 ID 的用户被视为新用户；然后 `user:ids` 的值增加，给用户一个唯一的 ID，密码在保存到 Redis 之前使用相同的 `update` 方法进行哈希处理。

将以下代码添加到 models/user.js 中。

##### 列表 6.16\. 更新用户记录

![](img/06lis16_alt.jpg)

##### 保护用户密码

当用户首次创建时，你需要将 `.pass` 属性设置为用户的密码。然后用户保存逻辑会将 `.pass` 属性替换为使用密码生成的哈希。

哈希是 *加盐的*。每个用户的加盐有助于防止雨彩虹表攻击：盐作为哈希机制的秘密密钥。你可以使用 bcrypt 的 `genSalt()` 生成一个 12 位的盐。

| |
| --- |

##### 雨彩虹表攻击

雨彩虹表攻击使用预先计算的表来破解哈希密码。你可以在维基百科上了解更多关于这个主题的信息：[`en.wikipedia.org/wiki/Rainbow_table`](http://en.wikipedia.org/wiki/Rainbow_table)。

| |
| --- |

盐生成后，调用 `bcrypt.hash()`，它将 `.pass` 属性和盐进行哈希处理。这个最终的 `hash` 值然后替换 `.pass` 属性，在 `.update()` 存储到 Redis 之前，确保不保存明文密码，只保存哈希。

以下列表，你将添加到 models/user.js 中，定义了一个函数，该函数创建加盐的哈希并将其存储在用户的 `.pass` 属性中。

##### 列表 6.17\. 向用户模型添加 bcrypt 加密

![](img/06lis17_alt.jpg)

就这些了。

##### 测试用户保存逻辑

要尝试保存用户，通过命令行输入 `redis-server` 启动 Redis 服务器。然后，将以下列表中的代码添加到 models/user.js 的底部，该代码创建了一个示例用户。然后，你可以在命令行上运行 `node models/user.js` 来执行示例用户的创建。

##### 列表 6.18\. 测试用户模型

![](img/06lis18_alt.jpg)

你应该看到表示用户已创建的输出：例如 `user id 1`。在测试用户模型后，从 models/user.js 中删除 列表 6.18 中的代码。

当你使用随 Redis 一起提供的 redis-cli 工具时，你可以使用 `HGETALL` 命令来获取哈希中的每个键和值，如下面的命令行会话所示。

##### 列表 6.19\. 使用 Redis 命令行工具进行查询

![](img/06lis19_alt.jpg)

定义了保存用户的逻辑后，你现在需要添加检索用户信息的逻辑。

| |
| --- |

##### 你可以在 redis-cli 工具中运行的 Redis 命令

有关 Redis 命令的更多信息，请参阅 Redis 命令参考 [`redis.io/commands`](http://redis.io/commands)。

| |
| --- |

##### 获取用户数据

当用户尝试登录到网络应用程序时，用户通常会在表单中输入用户名和密码，然后这些数据将被提交给应用程序进行认证。一旦提交登录表单，你需要一种方法通过名称获取用户。

此逻辑在以下列表中定义为 `User.getByName()`。该函数首先使用 `User.getId()` 执行 ID 查找，然后将找到的 ID 传递给 `User.get()`，该函数获取该用户的 Redis 哈希数据。将以下方法添加到 models/user.js 中。

##### 列表 6.20\. 从 Redis 获取用户

![图片](img/06lis20_alt.jpg)

如果你想要尝试获取用户，你可以尝试以下代码：

```
User.getByName('tobi', (err, user) => {
  console.log(user);
});
```

在获取到散列密码后，你现在可以继续进行用户认证。

##### 认证用户登录

用户认证所需的最后一个组件是以下列表中定义的方法，该方法利用了之前定义的用户数据检索函数。将此逻辑添加到 models/user.js 中。

##### 列表 6.21\. 认证用户的用户名和密码

![图片](img/06lis21_alt.jpg)

认证逻辑首先通过名称获取用户。如果找不到用户，回调函数将立即调用。否则，用户的存储盐和提交的密码将被散列以生成应与存储的 `user.pass` 散列相同的值。如果提交的散列和存储的散列不匹配，则用户输入了无效的凭据。当查找不存在的键时，Redis 将返回一个空哈希，这就是为什么使用 `!user.id` 而不是 `!user` 进行检查的原因。

现在你能够认证用户了，你需要一种让用户注册的方法。

#### 6.2.6\. 注册新用户

为了允许用户创建新账户并登录，你需要注册和登录功能。

在本节中，你将执行以下操作以实现注册：

+   将注册和登录路由映射到 URL 路径

+   向路由逻辑中添加显示注册表单

+   添加逻辑以存储从表单提交的用户数据

表单看起来像 图 6.12。

##### 图 6.12\. 用户注册表单

![图片](img/06fig12.jpg)

当用户使用网络浏览器访问 /register 时，将显示此表单。稍后你将创建一个类似的表单，允许用户登录。

##### 添加注册路由

为了让注册表单显示出来，你首先需要创建一个路由来渲染表单并将其返回给用户的浏览器进行显示。

列表 6.22 展示了如何使用 Node 的模块系统从 routes 目录导入定义注册路由行为的模块，并将 HTTP 方法与 URL 路径关联到路由函数。这形成了一种“前端控制器”。正如你所见，既有 `GET` 和 `POST` 注册路由。

##### 列表 6.22\. 添加注册路由

![图片](img/06lis22_alt.jpg)

接下来，为了定义路由逻辑，在路由目录中创建一个名为 register.js 的空文件。通过从 routes/register.js 导出以下函数来开始定义注册路由的行为——一个渲染注册模板的路由：

```
exports.form = (req, res) => {
  res.render('register', { title: 'Register' });
};
```

此路由使用你接下来将要创建的 EJS 模板来定义注册表单的 HTML。

##### 创建注册表单

为了定义注册表单的 HTML，在视图目录中创建一个名为 register.ejs 的文件。你可以使用以下列表中详细说明的 HTML/EJS 来定义此表单。

##### 列表 6.23\. 提供注册表单的视图模板

![图片](img/06lis23_alt.jpg)

注意到使用了 `include messages`，这包括另一个模板：messages.ejs。这个模板，你接下来将定义，用于与用户通信。

##### 向用户传达反馈

在用户注册过程中，以及在典型应用的许多其他部分，向用户传达反馈可能是必要的。例如，用户可能会尝试使用已被他人使用的用户名进行注册。在这种情况下，你需要告诉用户选择另一个名字。

在你的应用中，messages.ejs 模板将用于显示错误。应用中的许多模板都将包含 messages.ejs 模板。

要创建消息模板，在视图目录中创建一个名为 messages.ejs 的文件，并将以下代码片段放入该文件中。模板逻辑检查 `locals.messages` 变量是否已设置。如果是，模板将遍历该变量，显示消息对象。每个消息对象都有一个 `type` 属性（允许你在需要时使用消息进行非错误通知）和一个 `string` 属性（消息文本）。应用逻辑可以通过向 `res.locals.messages` 数组中添加错误来排队显示错误。消息显示后，调用 `removeMessages` 来清空消息队列：

```
<% if (locals.messages) { %>
  <% messages.forEach((message) => { %>
    <p class='<%= message.type %>'><%= message.string %></p>
  <% }) %>
  <% removeMessages() %>
<% } %>
```

图 6.13 展示了显示错误消息时的注册表单。

##### 图 6.13\. 注册表单错误报告

![图片](img/06fig13.jpg)

将消息添加到 `res.locals.messages` 是与用户通信的一种简单方式，但由于 `res.locals` 在重定向之间不持久，你需要通过使用会话在请求之间存储消息来使其更加健壮。

##### 在会话中存储临时消息

常见的 Web 应用程序设计模式是 Post/Redirect/Get（PRG）模式。在这个模式中，用户请求一个表单，表单数据作为 HTTP `POST`请求提交，然后用户被重定向到另一个网页。用户被重定向到哪个网页取决于应用程序是否认为表单数据有效。如果表单数据被认为无效，应用程序将用户重定向回表单页面。如果表单数据有效，用户将被重定向到一个新网页。PRG 模式主要用于防止重复提交表单。

在 Express 中，当用户被重定向时，`res.locals`的内容会被重置。如果你在`res.locals`中存储消息给用户，消息在显示之前就会丢失。然而，通过将消息存储在会话变量中，你可以解决这个问题。然后可以在最终的跳转页面上显示消息。

为了适应将消息队列到会话变量的能力，你需要在应用程序中添加一个模块。创建一个名为./middleware/messages.js 的文件，并添加以下代码：

```
const express = require('express');

function message(req) {
  return (msg, type) => {
    type = type || 'info';
    let sess = req.session;
    sess.messages = sess.messages || [];
    sess.messages.push({ type: type, string: msg });
  };
};
```

`res.message`函数提供了一种从任何 Express 请求向会话变量添加消息的方法。`express.response`对象是 Express 用于响应对象的原型。向此对象添加属性意味着它们将随后对所有中间件和路由可用。在前面的代码片段中，`express.response`被分配给一个名为`res`的变量，以便更容易在对象上添加属性并提高可读性。

此功能需要会话支持。为了添加会话支持，你需要一个与 Express 兼容的中间件模块。有一个官方支持的包叫做 express-session。使用`npm install --save express-session`安装它，然后将中间件添加到 app.js 中，如下所示：

```
const session = require('express-session');
...
app.use(session({
  secret: 'secret',
  resave: false, saveUninitialized: true
}));
```

最佳做法是在将 cookie 中间件插入之后放置中间件（它应该在大约第 26 行附近）。

为了使添加消息更加容易，添加以下代码片段中的代码。`res.error`函数允许你轻松地将类型为`error`的消息添加到消息队列中。使用你在模块中先前定义的`res.message`函数：

```
res.error =  msg => this.message(msg, 'error');
```

最后一步是将这些消息暴露给模板以进行输出。如果你不这样做，你必须将`req.session.messages`传递到应用程序中的每个`res.render()`调用中，这并不完全理想。

为了解决这个问题，你将创建一个中间件，在每个请求中将`res.locals.messages`填充为`res.session.messages`的内容，从而有效地将消息暴露给任何渲染的模板。到目前为止，./middleware/messages.js 扩展了响应原型，但它没有导出任何内容。但是，向此文件添加以下代码片段可以导出所需的中间件：

```
module.exports = (req, res, next) => {
  res.message = message(req);
  res.error = (msg) => {
    return res.message(msg, 'error');
  };
  res.locals.messages = req.session.messages || [];
  res.locals.removeMessages = () => {
    req.session.messages = [];
  };
  next();
};
```

首先，定义一个 `messages` 模板变量来存储会话的消息；它是一个数组，可能存在于之前的请求中（记住这些是会话持久化的消息）。接下来，您需要一种方法从会话中删除消息；否则，它们会不断累积，因为没有清除它们。

现在，您要集成此新功能，只需在 app.js 中 `require()` 该文件即可。您应该在会话中间件下方挂载此中间件，因为它依赖于 `req.session` 被定义。请注意，因为这个中间件被设计为不接受选项且不返回第二个函数，所以您可以使用 `app.use(messages)` 而不是 `app.use(messages())`。为了确保兼容性，通常最好让第三方中间件使用 `app.use(messages())`，无论它是否接受选项：

```
...
const register = require('./routes/register');
const messages = require('./middleware/messages');
...
app.use(express.methodOverride());
app.use(express.cookieParser());
   app.use(session({
     secret: 'secret',
     resave: false,
     saveUninitialized: true
   }));
app.use(messages);
...
```

现在，您可以在任何视图中访问 `messages` 和 `removeMessages()`，因此当 messages.ejs 包含在任何模板中时，应该可以完美工作。

完成注册表单的显示并制定了一种向用户传达任何必要反馈的方法后，让我们继续处理注册提交。

##### 实现用户注册

您需要创建一个路由函数来处理对 /register 的 HTTP `POST` 请求。这个函数被称为 `submit`。

当表单数据提交时，`bodyParser()` 中间件将提交的数据填充到 `req.body` 中。注册表单使用对象表示法 `user[name]`，在经过 body parser 解析后转换为 `req.body.user.name`。同样，`req.body.user.pass` 用于密码字段。

在提交路由中，您只需要少量代码来处理验证，例如确保用户名尚未被占用，并将新用户保存，如列表 6.24 所示。

注册完成后，`user.id` 被分配给用户的会话，您稍后会检查以验证用户是否已认证。如果验证失败，则将消息暴露给模板作为 `messages` 变量，通过 `res.locals.messages`，并将用户重定向回注册表单。

要添加此功能，请将以下列表的内容添加到 routes/register.js 中。

##### 列表 6.24. 使用提交的数据创建用户

![图片](img/06lis24_alt.jpg)

您现在可以启动应用程序，访问 /register，并注册一个用户。接下来，您需要一种方式让已注册用户通过 /login 表单进行身份验证。

#### 6.2.7. 登录已注册用户

添加登录功能甚至比注册更简单，因为大部分必要的逻辑已经包含在之前定义的通用身份验证方法 `User.authenticate()` 中。在本节中，您将添加以下内容：

+   路由逻辑以显示登录表单

+   验证从表单提交的用户数据的逻辑

表单看起来像图 6.14。

##### 图 6.14. 用户登录表单

![图片](img/06fig14.jpg)

你将首先修改 app.js 以确保登录路由是必需的，并且建立了路由路径：

```
...
const login = require('./routes/login');
...
app.get('/login', login.form);
app.post('/login', login.submit);
app.get('/logout', login.logout);
...
```

接下来，你将添加显示登录表单的功能。

##### 显示登录表单

实现登录表单的第一步是创建一个用于登录和注销相关路由的文件：routes/login.js。你需要添加以显示登录表单的路由逻辑几乎与之前用于显示注册表单的逻辑相同；唯一的区别是显示的模板名称和页面标题：

```
exports.form = (req, res) => {
  res.render('login', { title: 'Login' });
};
```

你将在 ./views/login.ejs 中定义的 EJS 登录表单与 register.ejs 非常相似；唯一的区别是说明文本和数据提交到的路由：

##### 列表 6.25\. 登录表单的视图模板

![](img/06lis25_alt.jpg)

现在你已经添加了显示登录表单所需的路由和模板，下一步是添加处理登录尝试的逻辑。

##### 验证登录

为了处理登录尝试，你需要添加路由逻辑来检查提交的用户名和密码，如果它们是正确的，则将一个会话变量设置为用户的 ID，并将用户重定向到主页。以下列表包含此逻辑，你应该将其添加到 routes/login.js 中。

##### 列表 6.26\. 处理登录的路由

![](img/06lis26_alt.jpg)

在这里，如果用户通过 `User.authenticate()` 进行认证，`req.session.uid` 将以与 `POST` /register 路由相同的方式分配：会话将持久化此值，你可以在以后使用它来检索 `User` 或其他相关用户数据。如果没有找到匹配项，将设置错误并重新显示表单。

用户也可能更喜欢明确地注销，因此你应该在应用程序的某个位置提供一个链接。在 app.js 中，设置具有此功能的路由：

```
const login = require('./routes/login');
...
app.get('/logout', login.logout);
```

然后在 ./routes/login.js 中，以下函数将移除由 `session()` 中间件检测到的会话，导致会话被分配给后续请求：

```
exports.logout = (req, res) => {
  req.session.destroy((err) => {
    if (err) throw err;
    res.redirect('/');
  })
};
```

现在注册和登录页面已经创建，接下来你需要添加一个菜单，以便用户可以访问它们。让我们看看如何创建一个。

##### 为认证用户和匿名用户创建菜单

在本节中，你将创建一个用于匿名用户和认证用户的菜单，允许他们登录、注册、提交条目和注销。图 6.15 显示了匿名用户的菜单。

##### 图 6.15\. 用于访问你创建的表单的用户登录和注册菜单

![](img/06fig15_alt.jpg)

当用户认证时，你将显示一个不同的菜单，显示该用户的用户名，以及一个用于向喊盒发布消息的页面链接和一个允许用户注销的链接。图 6.16 显示了此菜单。

##### 图 6.16\. 用户认证时的菜单

![](img/06fig16_alt.jpg)

你创建的每个 EJS 模板，代表一个应用页面，都在 `<body>` 标签之后包含了 `<% include menu %>` 代码。这包括了 ./views/menu.ejs 模板，你将在下一个列表中创建它。

##### 列表 6.27\. 匿名和认证用户菜单模板

![](img/06lis27_alt.jpg)

在这个应用中，你可以假设如果 `user` 变量被暴露给模板，则用户已认证，因为你不会在其他情况下暴露这个变量；你将在下面看到这一点。当这个变量存在时，你可以显示用户名以及提交条目和注销链接。当匿名用户访问时，显示网站登录和注册链接。

你可能想知道这个 `user` 本地变量是从哪里来的——你还没有编写它。接下来，你将编写一些代码来为每个请求加载已登录用户的数据，并使这些数据可用于模板。

#### 6.2.8\. 与用户加载中间件协同工作

当你与网络应用协同工作时，一个常见的任务是从数据库中加载用户信息，通常表示为一个 JavaScript 对象。拥有这些数据可以简化与用户的交互。对于本章的应用，你将使用中间件在每次请求时加载用户数据。

你将把这个中间件脚本放在 ./middleware/user.js 中，从上面的目录（../models）中引入 `User` 模型。中间件函数首先被导出，然后它检查会话中的用户 ID。当用户 ID 存在时，用户已认证，因此可以从 Redis 中安全地获取用户数据。

由于 Node 是单线程的，所以没有线程局部存储。在 HTTP 服务器的案例中，请求和响应变量是唯一可用的上下文对象。高级框架可以在 Node 的基础上构建，以提供额外的对象来存储已认证的用户，但 Express 选择坚持 Node 提供的原始对象。因此，上下文数据通常存储在请求对象上，如 列表 6.28 中所示，其中用户存储为 `req.user`；后续的中间件和路由可以通过使用相同的属性来访问用户对象。

你可能会想知道将 `res.locals.user` 赋值的目的是什么。`res.locals` 是 Express 提供的请求级别对象，用于将数据暴露给模板，类似于 `app.locals`。它也是一个可以用来将现有对象合并到自身的函数。

##### 列表 6.28\. 加载已登录用户数据的中间件

![](img/06lis28_alt.jpg)

要使用这个新的中间件，首先删除 app.js 中包含文本`user`的所有行。然后你可以像通常一样 require 模块，并将其传递给`app.use()`。在这个应用程序中，`user`在路由器之上使用，因此只有`user`之后的路由和中间件可以访问`req.user`。如果你使用的是像这个中间件一样加载数据的中间件，你可能希望将`express.static`中间件放在它之上；否则，每次服务静态文件时，都会进行不必要的数据库往返操作来获取用户。

下面的列表展示了如何在 app.js 中启用此中间件。

##### 列表 6.29\. 启用用户加载中间件

![](img/06lis29_alt.jpg)

如果你再次启动应用程序并在浏览器中访问/login 或/register 页面，你应该能看到菜单。如果你想对菜单进行样式设计，请将以下 CSS 代码行添加到 public/stylesheets/style.css 中。

##### 列表 6.30\. 可添加到 style.css 中以样式化应用程序菜单的 CSS

```
#menu {
  position: absolute;
  top: 15px;
  right: 20px;
  font-size: 12px;
  color: #888;
}
#menu .name:after {
  content: ' -';
}
#menu a {
  text-decoration: none;
  margin-left: 5px;
  color: black;
}
```

菜单设置好后，你应该能够注册自己作为用户。然后你应该看到带有发布链接的已认证用户菜单。

在下一节中，你将学习如何为应用程序创建一个公共 REST API。

#### 6.2.9\. 创建公共 REST API

在本节中，你将实现一个针对 shoutbox 应用程序的 RESTful 公共 API，以便第三方应用程序可以访问并添加发布数据。REST 允许使用动词和名词（分别由 HTTP 方法和 URL 表示）查询和更改应用程序数据。REST 请求通常以机器可读的格式（如 JSON 或 XML）返回数据。

要实现 API，你需要执行以下操作：

+   设计一个 API，允许用户展示、列出、删除和发布条目

+   添加基本认证

+   实现路由

+   提供 JSON 和 XML 响应

可以使用各种技术来验证和签名 API 请求，但实现更复杂解决方案超出了本书的范围。为了说明如何集成认证，我们将使用 basic-auth 包。

##### 设计 API

在开始实施之前，先大致规划一下涉及的路线是个好主意。对于这个应用，你将在 RESTful API 前加上/api 路径，但这是一个你可以更改的设计选择。例如，你可能希望使用一个子域名，如[`api.myapplication.com`](http://api.myapplication.com)。

下面的代码片段说明了为什么将回调函数移动到单独的 Node 模块中，而不是与`app.VERB()`调用内联定义，可能是一个好的选择。一个清晰的路线列表可以让你和你的团队清楚地了解已经实现了什么，以及实现回调函数的位置：

```
app.get('/api/user/:id', api.user);
app.get('/api/entries/:page?', api.entries);
app.post('/api/entry', api.add);
```

##### 添加基本认证

如前所述，有许多方法可以处理 API 安全性和限制，但这些方法超出了本书的范围。但用基本认证来展示这个过程是值得的。

`api.auth` 中间件将抽象这个过程，因为实现将位于即将创建的 ./routes/api.js 模块中。`app.use()` 方法可以传递一个路径名，在 Express 中称为 *挂载点*。使用这个挂载点，以 /api 开头的路径名和任何 HTTP 动词都会触发此中间件的调用。

以下代码片段中显示的行 `app.use('/api', api.auth)` 应该放在加载用户数据的中间件之前。这样做是为了您可以在以后修改用户加载中间件以加载认证 API 用户的资料：

```
...
const api = require('./routes/api');
...
app.use('/api', api.auth);
app.use(user);
...
```

要执行基本认证，安装 basic-auth 模块：`npm install --save basic-auth`。接下来，创建 ./routes/api.js 文件，并像以下代码片段所示，引入 Express 和用户模型。basic-auth 包接受一个函数来执行认证，该函数签名是 `(username, password, callback)`。您的 `User.authenticate` 方法是完美的选择：

```
const auth = require('basic-auth');
const express = require('express');
const User = require('../models/user');

exports.auth = (req, res, next) => {
  const { name, pass } = auth(req);
  User.authenticate(name, pass, (err, user) => {
    if (user) req.remoteUser = user;
    next(err);
  });
};
```

认证准备就绪。让我们继续实施 API 路由。

##### 实施路由

您将实现的第一条路由是 `GET /api/user/:id`。此路由的逻辑必须首先通过 ID 获取用户，如果用户不存在，则响应 *404 Not Found* 代码。如果用户存在，用户数据将被传递到 `res.send()` 进行序列化，应用程序将响应数据的 JSON 表示。在 routes/api.js 中添加以下代码片段的逻辑：

```
exports.user = (req, res, next) => {
  User.get(req.params.id, (err, user) => {
    if (err) return next(err);
    if (!user.id) return res.sendStatus(404);
    res.json(user);
  });
};
```

接下来，将以下路由路径添加到 app.js 中：

```
app.get('/api/user/:id', api.user);
```

您现在可以开始测试了。

##### 测试用户数据检索

启动应用程序，并使用 cURL 命令行工具进行测试。以下代码片段显示了如何测试应用程序的 REST 认证。凭据在 URL `tobi:ferret` 中提供，cURL 使用它来生成 `Authorization` 头字段：

```
$ curl http://tobi:ferret@127.0.0.1:3000/api/user/1 -v
```

以下列表显示了成功测试的结果。要执行类似的测试，您需要确保您知道用户的 ID。如果 `1` 不起作用并且您已注册用户，请尝试使用 redis-cli 和 `GET user:ids`。

##### 列表 6.31\. 测试输出

![Images/06lis31_alt.jpg]

##### 删除敏感用户数据

如您从 JSON 响应中看到的，用户的密码和盐都包含在响应中。要更改此，您可以在 models/user.js 中的 `User` 上实现 `.toJSON()`：

```
class User {
  // ...
  toJSON() {
    return {
      id: this.id,
      name: this.name
    };
  }
```

如果一个对象上存在 `.toJSON`，它将被 `JSON.stringify` 调用用于获取 JSON 格式。如果之前显示的 cURL 请求再次发出，您现在将只收到 ID 和名称属性：

```
{
  "id": "1",
  "name": "tobi"
}
```

您接下来要添加到 API 中的是创建条目的能力。

##### 添加条目

通过 HTML 表单和通过 API 添加条目的过程几乎相同，因此您可能希望重用之前实现的 `entries.submit()` 路由逻辑。

然而，在添加条目时，路由逻辑会存储用户的名称，除了其他详细信息外，还会添加条目。因此，您需要修改用户加载中间件，以便使用`basic-auth`中间件加载的用户数据填充`res.locals.user`。`basic-auth`中间件返回这些数据，并将它们设置为`req.remoteUser`。在用户加载中间件中添加对此的检查很简单；按照以下方式更改中间件/user.js 中的`module.exports`定义，以便用户加载中间件与 API 一起工作：

```
...
module.exports = (req, res, next) => {
  if (req.remoteUser) {
    res.locals.user = req.remoteUser;
  }
  const uid = req.session.uid;
  if (!uid) return next();
  User.get(uid, (err, user) => {
    if (err) return next(err);
    req.user = res.locals.user = user;
    next();
  });
};
```

进行此更改后，您现在可以通过 API 添加条目。

然而，还需要进行的一项更改是提供 API 友好的响应，而不是重定向到应用程序的主页。为了添加此功能，将 routes/entries.js 中的`entry.save`调用更改为以下内容：

```
...
  entry.save(err => {
    if (err) return next(err);
    if (req.remoteUser) {
      res.json({ message: 'Entry added.' });
    } else {
      res.redirect('/');
    }
  });
...
```

最后，为了在您的应用程序中激活条目添加 API，将以下片段的内容添加到 app.js 的路由部分：

```
app.post('/api/entry', entries.submit);
```

通过使用以下 cURL 命令，您可以测试通过 API 添加条目。在这里，标题和正文数据使用与 HTML 表单中相同的字段名称发送：

```
$ curl -X POST -d "entry[title]='Ho ho ho'&entry[body]='Santa loves you'"
      http://tobi:ferret@127.0.0.1:3000/api/entry
```

现在您已经添加了创建条目的能力，您还需要添加检索条目数据的能力。

##### 添加条目列表支持

下一个要实现的 API 路由是`GET /api/entries/:page?`。路由实现几乎与./routes/entries.js 中现有的条目列表路由相同。您还需要添加分页中间件，即以下片段中的`page()`。您很快就会添加`page()`。

因为路由逻辑将访问条目，所以您需要在 routes/api.js 的顶部使用以下行来引入`Entry`模型：

```
const Entry = require('../models/entry');
```

接下来，将以下片段中的行添加到 app.js 中：

```
const Entry = require('./models/entry');
...
app.get('/api/entries/:page?', page(Entry.count), api.entries);
```

现在将以下片段中的路由逻辑添加到 routes/api.js 中。此路由逻辑与 routes/entries.js 中类似逻辑之间的区别反映了您不再渲染模板，而是渲染 JSON 的事实：

```
exports.entries = (req, res, next) => {
  const page = req.page;
  Entry.getRange(page.from, page.to, (err, entries) => {
    if (err) return next(err);
    res.json(entries);
  });
};
```

##### 实现分页中间件

对于分页，您使用查询字符串`?page=N`的值来确定当前页。将以下中间件函数添加到./middleware/page.js 中。

##### 列表 6.32\. 分页中间件

![](img/06lis32_alt.jpg)

此中间件获取分配给`?page=N`的值；例如，`?page=1`。然后它获取结果总数，并将预计算的`page`对象暴露给任何可能稍后渲染的视图。这些值在模板外进行计算，以允许模板更干净，包含更少的逻辑。

##### 测试条目路由

以下 cURL 命令从 API 请求条目数据：

```
$ curl http://tobi:ferret@127.0.0.1:3000/api/entries
```

这个 cURL 命令应该产生类似于以下 JSON 的输出：

```
[
  {
    "username": "rick",
    "title": "Cats can't read minds",
    "body": "I think you're wrong about the cat thing."
  },
  {
    "username": "mike",
    "title": "I think my cat can read my mind",
    "body": "I think cat can hear my thoughts."
  },
...
```

在基本的 API 实现完成后，让我们继续探讨 API 如何支持多种响应格式。

#### 6.2.10\. 启用内容协商

*内容协商*允许客户端指定它愿意接受的格式以及它偏好的格式。在本节中，您将提供 API 内容的 JSON 和 XML 表示，以便 API 消费者可以决定他们想要什么。

HTTP 通过 `Accept` 头字段提供内容协商机制。例如，一个偏好 HTML 但愿意接受纯文本的客户端可以设置以下请求头：

```
Accept: text/plain; q=0.5, text/html
```

*qvalue*，或 *质量值*（例如本例中的 `q=0.5`），表示尽管指定了 `text/html`，但它比 `text/plain` 更受青睐 50%。Express 解析此信息并提供一个标准化的 `req.accepted` 数组：

```
[{ value: 'text/html', quality: 1 },
 { value: 'text/plain', quality: 0.5 }]
```

Express 还提供了 `res.format()` 方法，它接受一个 MIME 类型数组和回调。Express 将确定客户端愿意接受什么以及您愿意提供什么，并将调用适当的回调。

##### 实现内容协商

在 routes/api.js 中实现 `GET /api/entries` 路径的内容协商可能看起来像 列表 6.33。JSON 支持如之前一样——您使用 `res.send()` 将条目序列化为 JSON。XML 回调迭代条目并在这样做时写入套接字。请注意，无需显式设置 `Content-Type`；`res.format()` 会自动将其设置为相关类型。

##### 列表 6.33. 实现内容协商

![](img/06lis33_alt.jpg)

如果您设置了默认响应格式回调，则当用户未请求您明确处理过的格式时，将执行此操作。

`res.format()` 方法还接受一个扩展名，该扩展名映射到相关 MIME 类型。例如，`json` 和 `xml` 可以用来代替 `application/json` 和 `application/xml`，如下面的代码片段所示：

```
...
res.format({
  json: () => {
    res.send(entries);
  },
  xml: () => {
    res.write('<entries>\n');
    entries.forEach((entry) => {
      res.write(```

        <entry>

            <title>${entry.title}</title>

        <body>${entry.body}</body>

        <username>${entry.username}</username>

        </entry>

        ```
      );
    });
    res.end('</entries>');
  }
})
...
```

##### 以 XML 格式响应

在路由中编写大量自定义逻辑以响应 XML 可能不是最干净的方法，所以让我们看看如何使用视图系统来清理这个问题。

创建一个名为 ./views/entries/xml.ejs 的模板，该模板使用 EJS 迭代条目以生成 `<entry>` 标签。

##### 列表 6.34. 使用 EJS 模板生成 XML

![](img/06lis34_alt.jpg)

现在可以将 XML 回调替换为单个 `res.render()` 调用，传递 `entries` 数组，如下所示：

```
...
  xml: () => {
    res.render('entries/xml', { entries: entries });
  }
})
...
```

现在您已经准备好测试 API 的 XML 版本。在命令行中输入以下内容以查看 XML 输出：

```
curl -i -H 'Accept: application/xml'
http://tobi:ferret@127.0.0.1:3000/api/entries
```

### 6.3. 摘要

+   Connect 是一个 HTTP 框架，允许您在请求处理前后堆叠中间件组件。

+   Connect 中间件组件是接受 Node 的请求和响应对象、一个调用下一个中间件的函数以及一个可选的错误对象的函数。

+   Express 网络应用程序也是使用中间件组件构建的。

+   你可以使用 Express 通过使用 HTTP 动词来定义路由来构建 REST API。

+   Express 路由可以响应 JSON、HTML 或其他数据格式。

+   Express 有一个简单的模板引擎 API，支持许多引擎。

## 第七章\. Web 应用程序模板化

*本章涵盖*

+   使用模板组织应用程序

+   通过嵌入式 JavaScript 创建模板

+   使用 Hogan 学习极简模板化

+   使用 Pug 创建模板

在第三章和第六章中，你学习了在 Express 应用程序中使用模板的一些基础知识，以便创建视图。在本章中，你将专注于模板化，学习如何使用三个流行的模板引擎，以及如何通过将逻辑与表示标记分离来使用模板化使任何 Web 应用程序的代码保持整洁。

如果你熟悉模板化和模型-视图-控制器（MVC）模式，你可以快速浏览到第 7.2 节，在那里你将开始学习本章详细介绍的模板引擎，包括嵌入式 JavaScript、Hogan 和 Pug。如果你不熟悉模板化，请继续阅读——你将在接下来的几节中从概念上探索它。

### 7.1\. 使用模板保持代码整洁

你可以使用 MVC 模式在 Node 中以及几乎所有的其他 Web 技术中开发传统 Web 应用程序。MVC 中的一个关键概念是逻辑、数据和表示的分离。在 MVC Web 应用程序中，用户通常从服务器请求资源，这会导致*控制器*从*模型*请求应用程序数据，然后将数据传递给*视图*，最终为最终用户格式化数据。MVC 模式中的这个视图部分通常通过使用各种模板语言之一来实现。当一个应用程序使用模板化时，视图将模型返回的选定值传递给*模板引擎*，并指定定义如何显示提供的值的模板文件。

图 7.1 显示了模板逻辑如何适合 MVC 应用程序的整体架构。模板文件通常包含应用程序值的占位符以及 HTML、CSS，有时还有一小部分客户端 JavaScript 来实现动态行为，包括显示第三方小部件，如 Facebook 的“赞”按钮，或触发界面行为，如隐藏或显示页面的一部分。因为模板文件侧重于表示而不是逻辑，所以前端开发人员和后端开发人员可以共同工作，这有助于项目的劳动分工。

##### 图 7.1\. MVC 应用程序的流程及其与模板层的交互

![](img/07fig01_alt.jpg)

在本节中，我们将展示带有和没有模板的 HTML 渲染，以展示它们之间的差异。但首先，让我们从一个模板应用的实例开始。

#### 7.1.1\. 模板应用实例

作为应用模板的一个快速示例，让我们看看如何从简单的博客应用中优雅地输出 HTML。每篇博客条目都有一个标题、条目日期和正文文本。在浏览器中，博客看起来类似于图 7.2。

##### 图 7.2\. 示例博客应用浏览器输出

![](img/07fig02.jpg)

博客条目从格式类似于以下`entries.txt`片段的文本文件中读取。以下列表中的`---`表示一个条目的结束和另一个条目的开始。

##### 列表 7.1\. 博客条目文本文件

```
title: It's my birthday!
date: January 12, 2016
I am getting old, but thankfully I'm not in jail!
---
title: Movies are pretty good
date: January 2, 2016
I've been watching a lot of movies lately. It's relaxing,
except when they have clowns in them.
```

博客应用代码在`blog.js`中开始时，通过以下列表展示了所需的模块导入和博客条目的读取操作。

##### 列表 7.2\. 简单博客应用中的博客条目文件解析逻辑

![](img/07lis02_alt.jpg)

以下代码，当添加到博客应用中时，定义了一个 HTTP 服务器。当服务器收到 HTTP 请求时，它返回一个包含所有博客条目的页面。这个页面是通过一个名为`blogPage`的函数渲染的，您将在下一个步骤中定义它：

```
const server = http.createServer((req, res) => {
  const output = blogPage(entries);
  res.writeHead(200, {'Content-Type': 'text/html'});
  res.end(output);
});
server.listen(8000);
```

现在您需要定义`blogPage`函数，该函数将博客条目渲染成可以发送到用户浏览器的 HTML 页面。您将通过尝试两种方法来实现这一点：

+   不使用模板渲染 HTML

+   使用模板渲染 HTML

首先，让我们看看不使用模板的渲染。

#### 7.1.2\. 不使用模板渲染 HTML

博客应用可以直接输出 HTML，但将 HTML 包含在应用程序逻辑中会导致代码杂乱。在以下列表中，`blogPage`函数展示了显示博客条目的非模板方法。

##### 列表 7.3\. 模板引擎将表示细节与应用逻辑分离

![](img/07lis03_alt.jpg)

注意，所有这些与表示相关的内容、CSS 定义和 HTML 都为应用程序添加了许多行。

##### 使用模板渲染 HTML

使用模板渲染 HTML 允许您将 HTML 从应用程序逻辑中移除，从而显著清理代码。

要尝试本节中的示例，您需要将嵌入式 JavaScript (EJS) 模块安装到您的应用程序目录中。您可以在命令行中输入以下内容来完成此操作：

```
npm install ejs
```

以下代码片段从文件中加载一个模板，然后定义了一个新的`blogPage`函数版本，这次使用 EJS 模板引擎，我们将在 7.2 节中向您展示如何使用它：

```
const fs = require('fs');
const ejs = require('ejs');
const template = fs.readFileSync('./templatess/blog_page.ejs', 'utf8');
function blogPage(entries) {
  const values = { entries };
  return ejs.render(template, values);
}
```

完整的列表可以在本书的列表中找到，位于 ch07-templates/listing7_4/。EJS 模板文件包含 HTML 标记（将其保留在应用程序逻辑之外）和占位符，这些占位符指示数据传递到模板引擎时应放置的位置。以下列表展示了显示博客条目的 HTML 和占位符：

##### 列表 7.4\. 用于显示博客条目的 EJS 模板

![](img/07lis04_alt.jpg)

社区贡献的 Node 模块也提供了模板引擎，并且存在各种各样的它们。如果你认为 HTML 和/或 CSS 不够优雅，因为 HTML 需要关闭标签，CSS 需要开闭花括号，那么请更仔细地看看模板引擎。它们允许模板文件使用特殊语言（例如我们在本章后面将要介绍的 Pug 语言）来提供一种指定 HTML、CSS 或两者的简写方式。

这些模板引擎可以使你的模板更简洁，但你可能不想花时间去学习一种指定 HTML 和 CSS 的替代方法。最终，你决定使用什么取决于个人偏好。

在本章的其余部分，你将通过三个流行的模板引擎的视角学习如何在你的 Node 应用程序中集成模板：

+   嵌入式 JavaScript (EJS) 引擎

+   极简主义的 Hogan 引擎

+   Pug 模板引擎

每个引擎都允许你以不同的方式编写 HTML。让我们从 EJS 开始。

### 7.2. 使用嵌入式 JavaScript 进行模板化

嵌入式 JavaScript ([`github.com/visionmedia/ejs`](https://github.com/visionmedia/ejs))在模板方面采取了一种相当直接的方法，对于那些在其他语言中使用过模板引擎（如 Java Server Pages (JSP)、Smarty (PHP)、嵌入式 Ruby (ERB)等）的人来说，这将是一个熟悉的地方。EJS 允许你在 HTML 中嵌入 EJS 标签作为数据占位符。EJS 还允许你在模板中执行原始 JavaScript 逻辑，用于诸如条件分支和迭代等任务，就像 PHP 一样。

在本节中，你将学习以下内容：

+   创建 EJS 模板

+   使用 EJS 过滤器提供常用的、与展示相关的功能，例如文本操作、排序和迭代

+   将 EJS 集成到你的 Node 应用程序中

+   在客户端应用程序中使用 EJS

让我们更深入地探索 EJS 模板的世界。

#### 7.2.1. 创建模板

在模板的世界里，发送给模板引擎进行渲染的数据有时被称为*上下文*。以下是一个使用 Node 和 EJS 在上下文中渲染简单模板的裸骨示例：

```
const ejs = require('ejs');
const template = '<%= message %>';
const context = { message: 'Hello template!' };
console.log(ejs.render(template, context));
```

注意在`render`函数的第二个参数中使用了`locals`。第二个参数可以包括渲染选项以及上下文数据，这意味着使用`locals`确保上下文数据的各个部分不会被解释为 EJS 选项。但在大多数情况下，可以将上下文本身作为第二个选项传递，如下面的`render`调用所示：

```
console.log(ejs.render(template, context));
```

如果你直接将上下文作为`render`函数的第二个参数传递给 EJS，确保不要使用以下任何术语来命名上下文值：`cache`、`client`、`close`、`compileDebug`、`debug`、`filename`、`open`或`scope`。这些值被保留以允许更改模板引擎设置。

##### 字符转义

在渲染时，EJS 会转义上下文值中的任何特殊字符，并用 HTML 实体代码替换它们。这是为了防止跨站脚本（XSS）攻击，恶意网络应用程序用户试图将 JavaScript 作为数据提交，希望当显示时，它会在其他用户的浏览器中执行。以下代码显示了 EJS 的转义操作：

```
const ejs = require('ejs');
const template = '<%= message %>';
const context = {message: "<script>alert('XSS attack!');</script>"};
console.log(ejs.render(template, context));
```

之前的代码显示了以下输出：

```
&lt;script&gt;alert('XSS attack!');&lt;/script&gt;
```

如果您信任模板中使用的数据，并且不想在 EJS 模板中转义上下文值，您可以在模板标签中使用`<%-`而不是`<%=`，如下面的代码所示：

```
const ejs = require('ejs');
const template = '<%- message %>';
const context = {
  message: "<script>alert('Trusted JavaScript!');</script>"
};
console.log(ejs.render(template, context));
```

注意，如果您不喜欢 EJS 用于指定标签的字符，您可以自定义它们，如下所示：

```
const ejs = require('ejs');
ejs.delimiter = '$'
const template = '<$= message $>';
const context = { message: 'Hello template!' };
console.log(ejs.render(template, context));
```

现在您已经了解了 EJS 的基础知识，让我们看看一些更详细的示例。

#### 7.2.2。将 EJS 集成到您的应用程序中

由于将模板存储在与应用程序代码相同的文件中很不方便，这样做还会使你的代码变得杂乱，我们将向您展示如何使用 Node 的文件系统 API 从单独的文件中读取它们。

移动到工作目录，创建一个名为 app.js 的文件，包含以下列表中的代码。

##### 列表 7.5。在文件中存储模板代码

![图片](img/07lis05_alt.jpg)

接下来，创建一个名为 templates 的子目录。您将在此目录中保存模板。在 templates 目录中创建一个名为 students.ejs 的文件。将以下列表中的代码输入到 templates/students.ejs 中。

##### 列表 7.6。渲染学生数组的 EJS 模板

```
<% if (students.length) { %>
  <ul>
    <% students.forEach((student) => { %>
      <li><%= student.name %> (<%= student.age %>)</li>
    <% }) %>
  </ul>
<% } %>
```

##### 缓存 EJS 模板

EJS 支持可选的内存缓存模板函数：在解析您的模板文件一次后，EJS 将存储由解析创建的函数。渲染缓存的模板将更快，因为可以跳过解析步骤。

如果您在进行 Node 网络应用程序的初始开发，并且希望立即看到您对模板文件所做的任何更改，请不要启用缓存。但如果是将应用程序部署到生产环境，启用缓存是一个快速、简单的方法。缓存是通过`NODE_ENV`环境变量条件性启用的。

要尝试缓存，将上一个示例中对 EJS 的`render`函数的调用更改为以下内容：

```
const cache = process.env.NODE_ENV === 'production';
const output = ejs.render(
  template,
  { students, cache, filename }
);
```

注意，`filename`选项不一定是文件；您可以使用一个唯一值来标识您正在渲染的任何模板。

现在您已经学习了如何将 EJS 集成到 Node 应用程序中，让我们看看 EJS 可以以不同的方式使用：在网页浏览器中。

#### 7.2.3。在客户端应用程序中使用 EJS

要在客户端使用 EJS，您首先需要将 EJS 引擎下载到工作目录，如下面的命令所示：

```
cd /your/working/directory
curl -O https://raw.githubusercontent.com/tj/ejs/master/lib/ejs.js
```

下载 ejs.js 文件后，您可以在客户端代码中使用 EJS。以下列表显示了 EJS 的一个简单客户端应用程序。如果您将此文件保存为 index.html，您应该能够在浏览器中打开它以查看结果。

##### 列表 7.7\. 使用 EJS 向客户端添加模板功能

![图片](img/07lis07_alt.jpg)

您现在已经学会了如何使用功能齐全的 Node 模板引擎，现在是时候看看 Hogan 模板引擎了，它故意限制了模板代码可用的功能。

### 7.3\. 使用 Hogan 与 Mustache 模板语言

Hogan.js ([`github.com/twitter/hogan.js`](https://github.com/twitter/hogan.js)) 是由 Twitter 为其模板需求创建的一个模板引擎。Hogan 是流行的 Mustache ([`mustache.github.com/`](http://mustache.github.com/)) 模板语言标准的实现，该标准由 GitHub 的 Chris Wanstrath 创建。

Mustache 对模板采用了一种极简主义的方法。与 EJS 不同，Mustache 标准故意不包括条件逻辑，或除了转义内容以防止 XSS 攻击之外，没有其他内置的内容过滤功能。Mustache 倡导模板代码应尽可能简单。

在本节中，您将学习

+   如何在您的应用程序中创建和实现 Mustache 模板

+   如何使用 Mustache 标准中的各种模板标签

+   如何使用部分来组织模板

+   如何使用您自己的定界符和其他选项微调 Hogan

让我们看看 Hogan 提供的替代模板方法。

#### 7.3.1\. 创建模板

要在应用程序中使用 Hogan，或尝试本节中的演示，您需要在应用程序目录（ch07-templates/hogan-snippet）中安装 Hogan。您可以通过在命令行中输入以下命令来完成此操作：

```
npm i --save hogan.js
```

以下是一个使用 Hogan 在上下文中渲染简单模板的 Node 的裸骨示例。运行它将输出文本*Hello template!*

```
const hogan = require('hogan.js');
const templateSource = '{{message}}';
const context = { message: 'Hello template!' };
const template = hogan.compile(templateSource);
console.log(template.render(context));
```

现在您已经知道了如何使用 Hogan 处理 Mustache 模板，让我们看看 Mustache 支持哪些标签。

#### 7.3.2\. 使用 Mustache 标签

Mustache 标签在概念上类似于 EJS 的标签。Mustache 标签作为变量值的占位符，指示需要迭代的位置，并允许您增强 Mustache 的功能并在模板中添加注释。

##### 显示简单值

要在 Mustache 模板中显示上下文值，请将值的名称包含在双大括号中。在 Mustache 社区中，大括号被称为*胡子*。如果您想显示上下文项`name`的值，例如，您可以使用 Hogan 标签`{{name}}`。

与大多数模板引擎一样，Hogan 默认通过转义内容来防止 XSS 攻击。但要在 Hogan 中显示未转义值，您可以通过添加第三个胡子或在大括号前加上上下文项的名称来实现。使用之前的`name`示例，您可以通过使用`{{{name}}}`或`{{&name}}`标签格式来未转义地显示上下文值。

如果您想在 Mustache 模板中添加注释，可以使用此格式：`{{! This is a comment }}`。

##### 部分：遍历多个值

虽然 Hogan 不允许在模板中包含逻辑，但它确实包含了一种优雅的方法，通过使用 Mustache 的*部分*来迭代上下文项中的多个值。例如，以下上下文包含一个包含值的数组：

```
const context = {
  students: [
    { name: 'Jane Narwhal', age: 21 },
    { name: 'Rick LaRue', age: 26 }
  ]
};
```

如果您想创建一个模板，该模板显示每个学生都在单独的 HTML 段落中，输出类似于以下内容，使用 Hogan 模板是一个简单直接的任务：

```
<p>Name: Jane Narwhal, Age: 21 years old</p>
<p>Name: Rick LaRue, Age: 26 years old</p>
```

以下模板生成了所需的 HTML：

```
{{#students}}
  <p>Name: {{name}}, Age: {{age}} years old</p>
{{/students}}
```

##### 倒置部分：不存在值时的默认 HTML

如果上下文数据中`students`项的值不是数组怎么办？如果值是一个单独的对象，例如，模板将显示它。但是，如果对应项的值是未定义的、false 的，或者是一个空数组，则部分不会显示。

如果您希望模板输出一个消息，指示某个部分不存在值，Hogan 支持 Mustache 所说的*倒置部分*。以下模板代码，如果添加到之前的学生显示模板中，当上下文中不存在学生数据时将显示一条消息：

```
{{^students}}
  <p>No students found.</p>
{{/students}}
```

##### 部分 lambda：部分块中的自定义功能

为了允许开发者增强 Mustache 的功能，Mustache 标准允许您定义处理模板内容的函数调用的部分标签，而不是迭代数组。这被称为*部分 lambda*。

列表 7.8 展示了使用部分 lambda 在渲染模板时添加 Markdown 支持的一个示例。注意，该示例使用了 github-flavored-markdown 模块，您可以通过在命令行中输入`npm install github-flavored-markdown --dev`来安装它。如果您正在使用本书的源代码，请从`ch07-templates/listing7_8`运行`npm install`以运行示例。

在以下列表中，模板中的`**Name**`在通过由部分 lambda 逻辑调用的 Markdown 解析器渲染时，会被转换为`<strong>Name</strong>`。

##### 列表 7.8. 在 Hogan 中使用 lambda

![](img/07lis08_alt.jpg)

部分 lambda 允许您轻松实现诸如缓存和翻译机制等特性。

##### 部分模板：在其他模板中重用模板

在编写模板时，您希望避免在多个模板中不必要地重复相同的代码。避免这种情况的一种方法是通过创建部分模板。*部分模板*是作为构建块使用的模板，它们包含在其他模板中。部分模板的另一个用途是将复杂的模板分解成更简单的模板。

例如，以下列表使用部分模板将用于显示学生数据的模板代码与主模板分开。

##### 列表 7.9. 在 Hogan 中使用部分模板

![](img/07lis09_alt.jpg)

#### 7.3.3. 精调 Hogan

Hogan 相对简单易用——在您学会了其标签词汇后，您应该能够顺利使用。在使用过程中，您可能只需要调整几个选项。

如果你不喜欢 Mustache 样式的花括号，你可以通过传递一个选项来覆盖 Hogan 使用的分隔符，将`compile`方法传递给选项。以下示例显示了使用 EJS 样式的分隔符在 Hogan 中进行编译：

```
hogan.compile(text, { delimiters: '<% %>' });
```

除了 Mustache 之外，还有其他模板语言可供选择。其中一种尝试尽可能消除 HTML 噪声的语言是 Pug。

### 7.4. 使用 Pug 进行模板化

Pug（[`pugjs.org`](http://pugjs.org)），以前称为 Jade，提供了一种指定 HTML 的替代方法。它是 Express 中的默认模板引擎。Pug 与其他大多数模板系统的主要区别在于使用有意义的空白。在 Pug 中创建模板时，你使用缩进来表示 HTML 标签嵌套。HTML 标签也不必显式关闭，这消除了意外提前关闭标签或根本不关闭标签的问题。使用缩进还可以使模板在视觉上不那么密集，更容易维护。

为了快速了解这个功能的工作原理，让我们看看如何表示以下 HTML 片段：

```
<html>
  <head>
    <title>Welcome</title>
  </head>
  <body>
    <div id="main" class="content">
      <strong>"Hello world!"</strong>
    </div>
  </body>
</html>
```

以下 Pug 模板可以表示这个 HTML：

```
html
  head
    title Welcome
  body
    div.content#main
      strong "Hello world!"
```

与 EJS 一样，Pug 允许你在服务器或客户端嵌入 JavaScript，但你还可以使用 Pug 提供的一些额外功能，例如支持模板继承和混入。混入允许你轻松定义可重用的迷你模板，以表示常用视觉元素（如项目列表和框）所使用的 HTML。混入在概念上类似于你在上一节中学到的 Hogan.js 部分。模板继承使得将渲染单个 HTML 页面所需的 Pug 模板组织到多个文件中变得容易。你将在本节后面详细了解这些功能。

要在 Node 应用程序目录中安装 Pug，请在命令行中输入以下内容：

```
npm install pug --save
```

在本节中，你将学习

+   Pug 基础知识，例如指定类名、属性和块扩展

+   如何通过使用内置关键字为你的 Pug 模板添加逻辑

+   如何通过继承、块和混入来组织你的模板

要开始，让我们看看 Pug 的使用基础和语法。

#### 7.4.1. Pug 基础知识

Pug 使用与 HTML 相同的标签名，但它允许你省略开闭的`<`和`>`字符，而是使用缩进来表示标签嵌套。一个标签可以通过添加`.<classname>`与一个或多个 CSS 类相关联。一个应用了`content`和`sidebar`类的`div`元素将表示如下：

```
div.content.sidebar
```

CSS ID 通过在标签中添加`#<ID>`来分配。你可以在上一个示例中使用以下 Pug 表示法添加`featured_content` CSS ID：

```
div.content.sidebar#featured_content
```

##### 使用 div 标签简写

由于`div`标签在 HTML 中常用，Pug 提供了一种简写的方式来指定它。以下示例渲染的 HTML 与上一个示例相同：

```
.content.sidebar#featured_content
```

现在你已经知道了如何指定 HTML 标签及其 CSS 类和 ID，让我们看看如何指定 HTML 标签属性。

##### 指定标签属性

您可以通过将属性放在括号内来指定标签属性，用逗号分隔每个属性的指定。您可以使用以下 Pug 表示法指定一个将在新标签页中打开的超链接：

```
a(href='http://nodejs.org', target='_blank')
```

由于标签属性的指定可能导致 Pug 中出现长行，模板引擎为您提供了某些灵活性。以下 Pug 是有效的，并且与上一个示例等价：

```
a(href='http://nodejs.org',
  target='_blank')
```

您还可以指定不需要值的属性。以下 Pug 示例显示了包含预选`select`元素的 HTML 表单的指定：

```
strong Select your favorite food:
form
  select
    option(value='Cheese') Cheese
    option(value='Tofu', selected) Tofu
Specifying tag content
```

在前面的代码片段中，您还可以看到标签内容的示例：`strong`标签后的`Select your favorite food:`；第一个`option`标签后的`Cheese`；以及第二个`option`标签后的`Tofu`。

这是指定 Pug 中标签内容的标准方式，但并非唯一方式。尽管这种风格非常适合短内容片段，但如果标签内容较长，可能会导致 Pug 模板中出现过长行。幸运的是，如下例所示，Pug 允许您使用`|`字符指定标签内容：

```
textarea
  | This is some default text
  | that the user should be
  | provided with.
```

如果 HTML 标签，如`style`和`script`标签，只接受文本（意味着它不允许嵌套 HTML 元素），则可以完全省略`|`字符，如下例所示：

```
style
  h1 {
    font-size: 6em;
    color: #9DFF0C;
  }
```

有两种单独的方式来表达长标签内容和短标签内容，这有助于您保持 Pug 模板的优雅。Pug 还支持一种称为*块扩展*的替代嵌套表示方法。

##### 使用块扩展保持组织结构

Pug 通常通过缩进来表示嵌套，但有时缩进会导致多余的空白。例如，以下是一个使用缩进来定义简单链接列表的 Pug 模板：

```
ul
  li
    a(href='http://nodejs.org/') Node.js homepage
  li
    a(href='http://npmjs.org/') NPM homepage
  li
    a(href='http://nodebits.org/') Nodebits blog
```

使用 Pug 块扩展表达上一个示例的更紧凑方式。使用块扩展，您在标签后添加一个冒号来指示嵌套。以下代码生成的输出与上一个列表相同，但只有四行而不是七行：

```
ul
  li: a(href='http://nodejs.org/') Node.js homepage
  li: a(href='http://npmjs.org/') NPM homepage
  li: a(href='http://nodebits.org/') Nodebits blog
```

现在您已经了解了如何使用 Pug 表示标记，让我们看看如何将 Pug 集成到您的 Web 应用程序中。

##### 在 Pug 模板中嵌入数据

数据以与 EJS 相同的基本方式传递给 Pug 引擎。模板首先编译成一个函数，然后通过上下文调用该函数以渲染 HTML 输出。以下是一个示例：

```
const pug = require('pug');
const template = 'strong #{message}';
const context = { message: 'Hello template!' };
const fn = pug.compile(template);
console.log(fn(context));
```

在这里，模板中的`#{message}`指定了一个占位符，该占位符将由上下文值替换。

上下文值也可以用来为属性提供值。以下示例渲染了`<a href="http://google.com"></a>`：

```
const pug = require('pug');
const template = 'a(href = url)';
const context = { url: 'http://google.com' };
const fn = pug.compile(template);
console.log(fn(context));
```

现在您已经了解了如何使用 Pug 表示 HTML，以及如何向 Pug 模板提供应用程序数据，让我们看看如何在 Pug 中嵌入逻辑。

#### 7.4.2\. Pug 模板中的逻辑

在你为 Pug 模板提供应用程序数据后，你需要逻辑来处理这些数据。Pug 允许你直接将 JavaScript 代码行嵌入到模板中，这就是你在模板中定义逻辑的方式。如 `if` 语句、`for` 循环和 `var` 声明等代码是常见的。在我们深入细节之前，这里有一个模板渲染联系名单的例子，以给你一个在实际应用中使用 Pug 逻辑的感觉：

```
h3.contacts-header My Contacts
if contacts.length
  each contact in contacts
    - var fullName = contact.firstName + ' ' + contact.lastName
    .contact-box
      p fullName
      if contact.isEditable
        p: a(href='/edit/+contact.id) Edit Record
      p
        case contact.status
          when 'Active'
            strong User is active in the system
          when 'Inactive'
            em User is inactive
          when 'Pending'
            | User has a pending invitation
else
  p You currently do not have any contacts
```

让我们先看看 Pug 在嵌入 JavaScript 代码时处理输出的各种方式。

##### 在 Pug 模板中使用 JavaScript

在一行 JavaScript 逻辑前加上 `-` 将会执行 JavaScript，但不会将代码返回的任何值包含在模板的输出中。在 JavaScript 逻辑前加上 `=` 将会包含代码返回的值，并通过转义来防止 XSS 攻击。但如果你的 JavaScript 生成的代码不需要转义，你可以用 `!=` 前缀。表 7.1（Table 7.1）总结了这些前缀产生的输出。

##### 表 7.1\. 用于在 Pug 中嵌入 JavaScript 的前缀

| 前缀 | 输出 |
| --- | --- |
| = | 转义输出（用于不受信任或不预测的值，XSS 安全） |
| != | 无转义输出（用于受信任或可预测的值） |
| - | 无输出 |

Pug 包含了一些常用的条件性和迭代性语句，可以不使用前缀编写：`if`、`else`、`case`、`when`、`default`、`until`、`while`、`each` 和 `unless`。

Pug 还允许你定义变量。以下展示了在 Pug 中赋值两种等效的方式：

```
- count = 0
count = 0
```

无前缀的语句没有输出，就像之前讨论的 `-` 前缀一样。

##### 遍历对象和数组

在上下文中传递的值在 Pug 中的 JavaScript 中是可访问的。在下一个例子中，你将从文件中读取一个 Pug 模板，并将包含一些你打算在数组中显示的消息的上下文传递给 Pug 模板：

```
const pug = require('pug');
const fs = require('fs');
const template = fs.readFileSync('./template.pug');
const context = { messages: [
  'You have logged in successfully.',
  'Welcome back!'
]};
const fn = pug.compile(template);
console.log(fn(context));
```

Pug 模板包含以下内容：

```
- messages.forEach(message => {
  p= message
- })
```

最终的 HTML 输出如下所示：

```
<p>You have logged in successfully.</p><p>Welcome back!</p>
```

Pug 还支持一种非 JavaScript 形式的迭代：`each` 语句，它允许你轻松地遍历数组和对象属性。

以下与上一个例子等效，但使用了 `each`：

```
each message in messages
  p= message
```

你可以通过轻微的变化来遍历对象属性，如下所示：

```
each value, key in post
  div
    strong #{key}
    p value
```

##### 条件渲染模板代码

有时模板需要根据数据的值来决定如何显示数据。下一个例子演示了一个条件，大约一半的时间，`script` 标签会被输出为 HTML：

```
- n = Math.round(Math.random() * 1) + 1
- if (n == 1) {
  script
    alert('You win!');
- }
```

条件语句也可以通过使用更简洁的替代形式在 Pug 中编写：

```
- n = Math.round(Math.random() * 1) + 1
  if n == 1
    script
      alert('You win!');
```

如果你正在编写一个否定条件，例如 `if (n != 1)`，你可以使用 Pug 的 `unless` 关键字：

```
- n = Math.round(Math.random() * 1) + 1
  unless n == 1
    script
      alert('You win!');
```

##### 在 Pug 中使用 case 语句

Pug 还支持类似于 `switch` 的非 JavaScript 形式的条件语句：`case` 语句，它允许你根据各种模板场景指定一个结果。

以下示例模板展示了如何使用 `case` 语句以三种方式显示博客搜索的结果。如果没有找到任何内容，会显示一条消息。如果找到一个博客帖子，会详细显示。如果找到多个博客帖子，会使用 `each` 语句遍历帖子，显示它们的标题：

```
case results.length
  when 0
    p No results found.
  when 1
    p= results[0].content
  default
    each result in results
      p= result.title
```

#### 7.4.3\. 组织 Pug 模板

在定义了模板之后，接下来你需要了解如何组织它们。与应用程序逻辑一样，你不想让你的模板文件过大。单个模板文件应该对应一个概念性的构建块：一个页面、一个侧边栏或博客帖子内容等。

在本节中，你将了解一些机制，这些机制允许模板文件协同工作以渲染内容：

+   使用模板继承结构化多个模板

+   通过使用块的前缀/后缀实现布局

+   包含模板

+   使用混入重用模板逻辑

让我们从查看 Pug 中的模板继承开始。

##### 使用模板继承结构化多个模板

模板继承是结构化多个模板的一种方法。从概念上讲，它将模板视为面向对象编程范式中的类。一个模板可以扩展另一个，而这个模板又可以扩展另一个。你可以使用任何有意义的继承级别。

作为简单的示例，让我们看看如何使用模板继承提供一个基本的 HTML 包装器，你可以用它来包装页面内容。在一个工作目录中，创建一个名为 templates 的文件夹，你将在其中放置示例的 Pug 文件。对于页面模板，你将创建一个名为 layout.pug 的文件，包含以下 Pug 代码：

```
html
  head
    block title
  body
    block content
```

layout.pug 模板包含了 HTML 页面的基本定义以及两个 *块*。块在模板继承中用于定义子模板可以提供内容的位置。在 layout.pug 中，有一个 `title` 块，允许子模板设置标题，还有一个 `content` 块，允许子模板设置要在页面上显示的内容。

接下来，在你的工作目录的模板目录中，创建一个名为 page.pug 的文件。这个模板文件将填充 `title` 和 `content` 块：

```
extends layout
block title
  title Messages
block content
  each message in messages
    p= message
```

最后，添加以下列表中的逻辑（本节中较早示例的修改），这将显示模板结果，展示继承的实际应用。

##### 列表 7.10\. 模板继承的实际应用

```
const pug = require('pug');
const fs = require('fs');
const templateFile = './templates/page.pug';
const iterTemplate = fs.readFileSync(templateFile);
const context = { messages: [
  'You have logged in successfully.',
  'Welcome back!'
]};
const iterFn = pug.compile(
  iterTemplate,
  { filename: templateFile }
);
console.log(iterFn(context));
```

现在，让我们看看另一个模板继承功能：块的前缀和后缀。

##### 通过使用块的前缀/后缀实现布局

在前面的示例中，layout.pug 中的块不包含任何内容，这使得在 page.pug 模板中设置内容变得简单。但如果继承模板中的块确实包含内容，则此内容可以通过子模板使用块前缀和后缀来构建，而不是替换。这允许你定义常见内容并添加到其中，而不是替换它。

以下 layout.pug 模板包含一个额外的块，名为 `scripts`，其中包含内容——一个加载 jQuery JavaScript 库的 `script` 标签：

```
html
  head
    - const baseUrl = "http://ajax.googleapis.com/ajax/libs/jqueryui/1.8/"
    block title
    block style
    block scripts
  body
    block content
```

如果你想要 page.pug 模板额外加载 jQuery UI 库，你可以通过以下列表中的模板来实现。

##### 列表 7.11\. 使用 `block append` 添加额外的 JavaScript 文件

![](img/07lis11_alt.jpg)

模板继承不是集成多个模板的唯一方法。你还可以使用 `include` Pug 命令。

##### 模板包含

组织模板的另一个工具是 Pug 的 `include` 命令。此命令结合了另一个模板的内容。如果你将 `include footer` 行添加到前面示例中的 layout.pug 模板中，你最终会得到以下模板：

```
html
  head
    block title
    block style
    block scripts
      script(src='//ajax.googleapis.com/ajax/libs/jquery/1.8/jquery.js')
  body
    block content
    include footer
```

此模板在 layout.pug 的渲染输出中包含了名为 footer.pug 的模板的内容，如图 7.3 所示。[#ch07fig03]

##### 图 7.3\. Pug 的 `include` 机制提供了一个简单的方法，在渲染期间将一个模板的内容包含到另一个模板中。

![](img/07fig03.jpg)

这可以用来向 layout.pug 添加有关网站或设计元素的信息。你还可以通过指定文件扩展名（例如，`include twitter_widget.html`）来包含非 Pug 文件。

##### 使用混合重用模板逻辑

虽然 Pug 的 `include` 命令对于引入先前创建的代码块很有用，但它并不是创建可在页面和应用程序之间共享的可重用功能库的理想选择。为此，Pug 提供了 `mixin` 命令，它允许你定义可重用的 Pug 片段。

Pug 混合类似于 JavaScript 函数。混合可以像函数一样接受参数，并且可以使用这些参数生成 Pug 代码。

假设，例如，你的应用程序处理类似于以下的数据结构：

```
const students = [
  { name: 'Rick LaRue', age: 23 },
  { name: 'Sarah Cathands', age: 25 },
  { name: 'Bob Dobbs', age: 37 }
];
```

如果你想要定义一种方法来输出一个由每个对象的给定属性派生的 HTML 列表，你可以定义一个如以下所示的混合来完成此操作：

```
mixin list_object_property(objects, property)
  ul
    each object in objects
      li= object[property]
```

然后，你可以使用以下 Pug 代码行来使用混合显示数据：

```
mixin list_object_property(students, 'name')
```

通过使用模板继承、`include` 语句和混合，你可以轻松地重用表示标记，并防止你的模板文件变得比必要的还要大。

### 7.5\. 概述

+   模板引擎有助于保持应用程序逻辑和表示的有序性。

+   Node 有几个流行的模板引擎，包括 EJS、Hogan.js 和 Pug。

+   EJS 支持简单的控制流和转义或未转义的插值。

+   Hogan.js 是一个简单的模板引擎，不支持控制流，但支持 Mustache 标准。

+   Pug 是一个更复杂的模板语言，可以输出 HTML，但不使用尖括号。

+   Pug 依赖于空白字符来嵌入标签。

## 第八章. 存储应用程序数据

*本章涵盖*

+   关系型数据库：PostgreSQL

+   NoSQL 数据库：MongoDB

+   ACID 类别

+   托管云数据库和存储服务

Node.js 为各种开发者提供了一系列极其多样化的需求。没有单一的数据库或存储解决方案能够解决 Node 处理的使用案例数量。本章提供了数据存储可能性的广泛概述，以及一些重要的高级概念和术语。

### 8.1. 关系型数据库

在网络的大部分历史中，关系型数据库一直是应用程序数据存储的主导选择。这个主题在其他许多文本和大学课程中已经详细讨论过，所以我们本章不会在这个主题上花费太多时间进行阐述。

建立在关系代数和集合理论数学思想之上的关系型数据库自 20 世纪 70 年代以来一直存在。*模式*指定了各种数据类型的格式以及这些类型之间的关系。例如，如果您正在构建一个社交网络，您可能有`User`和`Post`数据类型，并定义`User`和`Post`之间的一对多关系。然后使用结构化查询语言（SQL），您可以对此数据进行查询，例如，“给我所有属于 ID 为 123 的用户的所有帖子”，或者用 SQL 表示：`SELECT * FROM post WHERE user_id=123`。

### 8.2. PostgreSQL

MySQL 和 PostgreSQL（Postgres）都是 Node 应用程序中流行的关系型数据库选择。关系型数据库之间的差异主要是美学上的，所以本节的大部分内容也适用于在 Node 中使用其他关系型数据库，如 MySQL。首先，让我们看看如何在您的开发机上安装 Postgres。

#### 8.2.1. 执行安装和设置

Postgres 需要在您的系统上安装。您不能简单地使用 npm 安装它。安装说明因平台而异。在 macOS 上，安装就像这样：

```
brew update
brew install postgres
```

如果您已经安装了 Postgres，可能会遇到升级问题。按照您平台的说明迁移现有数据库，或者清除数据库目录：

```
# WARNING: will delete existing postgres configuration & data
rm –rf /usr/local/var/postgres
```

然后初始化并启动 Postgres：

```
initdb -D /usr/local/var/postgres
pg_ctl -D /usr/local/var/postgres -l logfile start
```

这将启动 Postgres 守护进程。每次您启动计算机时都需要启动此守护进程。您可能希望在启动时自动启动 Postgres 守护进程，许多在线指南详细说明了针对您特定操作系统的此过程。

类似地，大多数 Linux 系统都有用于安装 Postgres 的软件包。对于 Windows，您应从 postgresql.org 下载安装程序（[www.postgresql.org/download/windows/](http://www.postgresql.org/download/windows/))）。

Postgres 随安装附带了一些命令行管理工具。你可能想通过阅读它们的 `man` 页面来熟悉其中的一些。

#### 8.2.2\. 创建数据库

在 Postgres 守护进程运行后，你需要创建一个数据库来使用。这只需要做一次。最简单的方法是使用命令行中的 `createdb`。在这里，你创建一个名为 *articles* 的数据库：

```
createdb articles
```

如果成功，则没有输出。如果已存在具有此名称的数据库，则此命令不会执行任何操作并报告失败。

尽管可能配置了多个数据库，但大多数应用程序一次只连接到一个数据库，这取决于数据库运行的 *环境*。许多应用程序至少有两个环境：开发和生产。

要从现有数据库中删除所有数据，你可以从终端运行 `dropdb` 命令，并将数据库名称作为参数传递：

```
dropdb articles
```

在再次使用此数据库之前，你需要运行 `createdb`。

#### 8.2.3\. 从 Node 连接到 Postgres

从 Node 连接到 Postgres 最受欢迎的包是 `pg`。你可以使用 `npm` 安装 `pg`：

```
npm install pg --save
```

当 Postgres 服务器运行，数据库已创建，并且已安装 `pg` 包时，你就可以开始从 Node 使用数据库了。在你可以对服务器发出任何命令之前，你需要建立与它的连接，如下一列表所示。

##### 列表 8.1\. 连接到数据库

![](img/08lis01_alt.jpg)

`pg.Client` 和其他方法的全面文档可以在 GitHub 上 `pg` 包的 wiki 页面上找到：[`github.com/brianc/node-postgres/wiki`](https://github.com/brianc/node-postgres/wiki)。

#### 8.2.4\. 定义表

为了在 PostgreSQL 中存储数据，你首先需要定义一些表以及要存储的数据的形状，如以下列表所示（书中源代码的 ch08-databases/listing8_3）。

##### 列表 8.2\. 定义模式

```
db.query(`
  CREATE TABLE IF NOT EXISTS snippets (
    id SERIAL,
    PRIMARY KEY(id),
    body text
  );
`, (err, result) => {
  if (err) throw err;
  console.log('Created table "snippets"');
  db.end();
});
```

#### 8.2.5\. 插入数据

在你的表定义完成后，你可以通过使用 `INSERT` 查询将其中的数据插入，如下一列表所示。如果你没有指定 `id` 值，PostgreSQL 将为你选择一个 ID。要了解为特定行选择了哪个 ID，你可以在查询中附加 `RETURNING id`，它将出现在传递给回调函数的结果行的行中。

##### 列表 8.3\. 插入数据

```
const body = 'hello world';
db.query(`
  INSERT INTO snippets (body) VALUES (
    '${body}'
  )
  RETURNING id
`, (err, result) => {
  if (err) throw err;
  const id = result.rows[0].id;
  console.log('Inserted row with id %s', id);
  db.query(`
    INSERT INTO snippets (body) VALUES (
      '${body}'
    )
    RETURNING id
  `, () => {
    if (err) throw err;
    const id = result.rows[0].id;
    console.log('Inserted row with id %s', id);
  });
});
```

#### 8.2.6\. 更新数据

数据插入后，你可以通过使用 `UPDATE` 查询来更新数据，如下一列表所示。受影响行数将在查询结果的 `rowCount` 属性中可用。你可以在这个列表的完整示例中找到 ch08-databases/listing8_4。

##### 列表 8.4\. 更新数据

```
const id = 1;
const body = 'greetings, world';
db.query(`
  UPDATE snippets SET (body) = (
    '${body}'
  ) WHERE id=${id};
`, (err, result) => {
  if (err) throw err;
  console.log('Updated %s rows.', result.rowCount);
});
```

#### 8.2.7\. 查询数据

关系型数据库最强大的功能之一是能够对你的数据进行复杂的即席查询。查询是通过使用 `SELECT` 语句来执行的，以下列表展示了这一点的最简单示例。

##### 列表 8.5\. 查询数据

```
db.query(`
  SELECT * FROM snippets ORDER BY id
`, (err, result) => {
  if (err) throw err;
  console.log(result.rows);
});
```

### 8.3\. Knex

许多开发者更喜欢不在他们的应用程序中直接处理 SQL 语句，而是使用抽象层。这是可以理解的，因为将字符串连接到 SQL 语句可能是一个笨拙的过程，而且查询可能会变得难以理解和维护。这对于 JavaScript 来说尤其如此，因为直到 ES2015 引入了模板字符串（请参阅 [`developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)），JavaScript 都没有表示多行字符串的语法。图 8.1 显示了 Knex 的统计数据，包括下载次数，这证明了它的流行。

##### 图 8.1\. Knex 的使用统计

![](img/08fig01.jpg)

*Knex* 是一个 Node 包，它实现了一种轻量级的 SQL 抽象，称为 *查询构建器*。查询构建器通过一个声明式 API 构建 SQL 字符串，该 API 与生成的 SQL 非常相似。Knex API 直观且不出所料：

```
knex({ client: 'mysql' })
  .select()
  .from('users')
  .where({ id: '123' })
  .toSQL();
```

这在 MySQL 方言的 SQL 中生成一个参数化 SQL 查询：

```
select * from `users` where `id` = ?
```

#### 8.3.1\. 数据库的 jQuery

尽管 ANSI 和 ISO SQL 标准自 1980 年代中期以来就已经存在，但大多数数据库仍然使用自己的 SQL 方言。PostgreSQL 是一个值得注意的例外；它自豪地宣称自己遵循 SQL:2008 标准。查询构建器可以标准化不同 SQL 方言之间的差异，为多种技术提供统一的 SQL 生成接口。这对经常在多种数据库技术之间切换的团队来说具有明显的优势。

Knex.js 目前支持以下数据库：

+   PostgreSQL

+   MSSQL

+   MySQL

+   MariaDB

+   SQLite3

+   Oracle

表 8.1 比较了 Knex 根据所选数据库生成插入语句的方式。

##### 表 8.1\. 比较 Knex 生成的各种数据库的 SQL

| 数据库 | SQL |
| --- | --- |
| PostgreSQL、SQLite 和 Oracle | insert into "users" ("name", "age") values (?, ?) |
| MySQL 和 MariaDB | insert into `users` (`name`, `age`) values (?, ?) |
| Microsoft SQL Server | insert into [users] ([name], [age]) values (?, ?) |

Knex 支持 promises 和 Node 风格的回调。

#### 8.3.2\. 使用 Knex 连接和运行查询

与许多其他查询构建器不同，Knex 还可以为你选择的数据驱动程序连接和执行查询：

```
db('articles')
  .select('title')
  .where({ title: 'Today's News' })
  .then(articles => {
    console.log(articles);
  });
```

Knex 查询默认返回承诺，但也可以通过 `.asCallback` 支持 Node 的回调约定：

```
db('articles')
  .select('title')
  .where({ title: 'Today's News' })
  .asCallback((err, articles) => {
    if (err) throw err;
    console.log(articles);
  });
```

在 第三章 中，你通过直接使用 sqlite3 包与 SQLite 数据库交互。可以使用 Knex 重写此 API。要运行此示例，首先确保已从 npm 安装了 knex 和 sqlite3 包：

```
npm install knex@~0.12.0 sqlite3@~3.1.0 --save
```

下一个列表使用 sqlite3 实现了一个简单的 `Article` 模型。将此文件保存为 db.js；你将在 列表 8.7 中使用它来与数据库交互。

##### 列表 8.6\. 使用 Knex 连接和查询 sqlite3

![](img/ch08ex06-0.jpg)

![](img/ch08ex06-1.jpg)

现在，可以使用 `db.Article` 添加 `Article` 条目。以下列表可以与上一个列表一起使用来创建文章并打印它们。有关完整示例，请参阅 ch08-databases/listing8_7/index.js。

##### 列表 8.7\. 与 Knex 驱动的 API 交互

```
db().then(() => {
  db.Article.create({
    title: 'my article',
    content: 'article content'
  }).then(() => {
    db.Article.all().then(articles => {
      console.log(articles);
      process.exit();
    });
  });
})
.catch(err => { throw err });
```

SQLite 需要最少的配置：您不需要启动服务器守护进程或从应用程序外部创建数据库。SQLite 将所有内容写入单个文件。如果您运行前面的代码，您将在当前目录中找到一个 articles.sqlite 文件。擦除 SQLite 数据库就像删除这个文件一样简单：

```
rm articles.sqlite
```

SQLite 也支持内存模式，这种模式可以完全避免写入磁盘。这种模式通常用于减少自动化测试的运行时间。您可以通过使用特殊的 `:memory:` 文件名来配置内存模式。打开多个连接到 `:memory:` 文件会为每个连接提供其自己的私有数据库：

```
const db = knex({
  client: 'sqlite3',
  connection: {
    filename: ':memory:'
  },
  useNullAsDefault: true
});
```

#### 8.3.3\. 交换数据库后端

由于您正在使用 Knex，将 列表 8.6 和 8.7 更改为使用 PostgreSQL 而不是 sqlite3 是非常简单的。Knex 需要安装 pg 包以与 PostgreSQL 服务器通信，您需要安装并运行它。将 pg 包安装到包含 列表 8.7 的文件夹中（书中的代码为 ch08-databases/listing8_7），并记得使用 PostgreSQL 的 `createdb` 命令行工具创建适当的数据库：

```
npm install pg --save
createdb articles
```

使用此新数据库所需进行的唯一代码更改是在 Knex 配置中；否则，消费者 API 和使用方式相同：

```
const db = knex({
  client: 'pg',
  connection: {
    database: 'articles'
  }
})
```

注意，在实际场景中，您还需要迁移任何现有数据。

#### 8.3.4\. 谨防抽象泄漏

查询构建器可以标准化 SQL 语法，但对标准化行为所能做的很少。某些功能仅在特定数据库中受支持，并且某些数据库可能会在相同的查询下表现出完全不同的行为。例如，以下是在使用 Knex 定义主键时的两种方法：

+   `table.increments('id').primary();`

+   `table.integer('id').primary();`

在 SQLite3 中，这两种选项都能按预期工作，但第二种选项在 PostgreSQL 中插入新记录时会导致错误：

```
"null value in column "id" violates not-null constraint"
```

将 `null` 作为主键插入 SQLite 的值将被分配一个自动递增的 ID，无论主键列是否明确配置为自动递增。另一方面，PostgreSQL 需要显式定义自动递增列。数据库之间存在许多这样的行为差异，并且一些差异可能很微妙，没有明显的错误。如果您确实选择过渡到不同的数据库，则需要彻底测试。

### 8.4\. MySQL 与 PostgreSQL

MySQL 和 PostgreSQL 都是成熟且强大的数据库，对于许多项目来说，在选择其中一个时与其他选择之间的差异可能很小。许多区别，直到项目需要扩展时才变得显著，存在于或位于面向应用程序开发者的接口边缘或下方。

详尽的关系型数据库比较通常超出了本书的范围，因为该主题很复杂。这里列出了一些显著的差异：

+   PostgreSQL 支持更丰富的数据类型，包括数组、JSON 和用户定义的类型。

+   PostgreSQL 内置全文搜索功能。

+   PostgreSQL 支持完整的 ANSI SQL:2008 标准。

+   PostgreSQL 的复制支持不如 MySQL 强大或经过实战检验。

+   MySQL 更老，拥有更大的社区。MySQL 有更多兼容的工具和资源。

+   MySQL 社区由于细微的分支（例如，来自 Facebook、Google、Twitter 等的 MariaDB 和 WebScaleSQL）而更加碎片化。

+   MySQL 的可插拔存储引擎可能会使其理解、管理和调优变得更加复杂。另一方面，这也可以被视为对性能进行更精细控制的机会。

MySQL 和 PostgreSQL 在不同类型的工作负载下表现出不同的性能特征，你的工作负载的微妙之处可能直到项目成熟才变得明显。

许多在线资源提供了关于关系型数据库之间更深入的比较：

+   [www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems](http://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems)

+   [`blog.udemy.com/mysql-vs-postgresql/`](https://blog.udemy.com/mysql-vs-postgresql/)

+   [`eng.uber.com/mysql-migration/`](https://eng.uber.com/mysql-migration/)

你最初选择哪种关系型数据库不太可能是你项目成功的关键因素，所以不必过于担心这个决定。你可以稍后迁移到另一个数据库，但 Postgres 应该足够强大，能够提供你需要的几乎所有功能和可扩展性。但如果你有机会评估几个数据库，你应该熟悉 ACID 保证的概念。

### 8.5. ACID 保证

*ACID* 描述了数据库事务的一组理想属性：原子性、一致性、隔离性和持久性。这些术语的确切定义可能有所不同。一般来说，一个系统越严格地保证 ACID 属性，其性能妥协就越大。这种 ACID 分类是开发者快速沟通特定解决方案（如 NoSQL 系统中发现的）权衡的常见方式。

#### 8.5.1. 原子性：事务要么完全成功，要么完全失败

一个*原子*事务不能部分执行：要么整个操作完成，要么数据库保持不变。例如，如果事务是要删除特定用户的全部评论，要么所有评论都会被删除，要么一个评论都不会被删除。不可能出现一些评论被删除而另一些没有被删除的情况。原子性即使在系统错误或电源故障的情况下也应该适用。"原子"在这里使用其原始意义，即*不可分割*。

#### 8.5.2\. 一致性：约束始终得到执行

成功事务的完成必须维护系统定义的所有数据完整性约束。一些示例约束包括主键必须是唯一的，数据符合特定的模式，或者外键必须引用存在的实体。可能导致不一致状态的事务通常会导致事务失败，尽管一些小问题可能会自动解决；例如，将数据强制转换为正确的形状。这不要与 CAP 定理中的*一致性*（C）混淆，它指的是保证分布式存储中所有读者看到的数据视图的一致性。

#### 8.5.3\. 隔离：并发事务不干扰

隔离事务应该产生相同的结果，无论是并发执行还是顺序执行相同的交易。系统提供的隔离级别直接影响到其执行并发操作的能力。一个简单的隔离方案是使用*全局锁*，在整个事务期间锁定整个数据库，从而有效地按顺序处理所有事务。这提供了强大的隔离保证，但同时也非常低效：操作完全不同数据集的事务会被无谓地阻塞（例如，一个用户添加评论理想情况下不应该阻止另一个用户更新他们的个人资料）。在实践中，系统通过使用更细粒度和选择性的锁定方案（例如，按表、行或字段）提供各种隔离级别。更复杂的系统甚至可能会乐观地尝试以最小锁定并发执行所有事务，只有在检测到冲突的情况下才会通过使用越来越粗粒度的锁来重试事务。

#### 8.5.4\. 持久性：事务是永久的

事务的*持久性*是指其效果在重启、电源故障、系统错误甚至硬件故障后仍保证持续的程度。例如，使用 SQLite 内存模式的应用程序没有事务持久性；当进程退出时，所有数据都会丢失。另一方面，SQLite 将数据持久化到磁盘将具有良好的事务持久性，因为数据在机器重启后仍然持续存在。

这可能看起来是个显而易见的事情：只需将数据写入磁盘——然后，你就有持久的交易了。但磁盘 I/O 是应用程序可以执行的最慢的操作之一，并且可能会迅速成为应用程序中的瓶颈，即使在中等规模下也是如此。一些数据库提供了不同的持久性权衡，可以用来维持可接受的系统性能。

### 8.6\. NoSQL

不适合关系模型的数据库的统称是*NoSQL*。今天，由于一些 NoSQL 数据库确实支持 SQL，因此 NoSQL 这个术语的含义更接近于*非关系型*或作为缩写词*不仅 SQL*。

这里有一些可以被认为是 NoSQL 的范式和示例数据库的子集：

+   ***键值/元组—*** DynamoDB, LevelDB, Redis, etcd, Riak, Aerospike, Berkeley DB

+   ***图—*** Neo4J, OrientDB

+   ***文档—*** CouchDB, MongoDB, Elastic（前身为 Elasticsearch）

+   ***列—*** Cassandra, HBase

+   ***时间序列—*** Graphite, InfluxDB, RRDtool

+   ***多范式—*** Couchbase（文档数据库，键/值存储，分布式缓存）

对于 NoSQL 数据库的更完整列表，请参阅[`nosql-database.org/`](http://nosql-database.org/)。

如果你只使用过关系型数据库，那么理解 NoSQL 概念可能会有些困难，因为 NoSQL 的使用往往直接违反了已经确立的最佳实践：没有定义的架构。数据重复。约束宽松。NoSQL 系统承担了通常由数据库承担的责任，并将其置于应用领域。这一切可能看起来有些混乱。

通常，只有一小部分访问模式会为数据库创建大部分工作负载，例如生成应用程序着陆页的查询，其中需要检索多个领域对象。提高关系型数据库读取性能的常见技术是反规范化，其中领域查询被预处理并塑造成减少客户端消费所需读取次数的形式。

默认情况下，NoSQL 数据更频繁地使用反规范化。领域建模步骤可能完全被跳过。这可以阻止对数据模型进行过度设计，允许更快地执行更改，并导致整体上更简单、性能更好的设计。

### 8.7\. 分布式数据库

应用程序可以通过增加运行它的机器的容量来垂直扩展，或者通过添加更多机器来水平扩展。垂直扩展通常是更简单的选项，但硬件的限制决定了单台机器可以扩展的范围。垂直扩展也往往会迅速变得昂贵。另一方面，水平扩展通过添加更多进程和机器来增加容量，具有更高的增长能力。这以在编排更多移动部件中的复杂性为代价。所有增长系统最终都会达到一个必须水平扩展的点。

分布式数据库从一开始就被设计为以横向扩展为基础。跨多台机器存储的数据通过消除任何单点故障来提高数据的持久性。许多关系型系统都有一些执行横向扩展的能力，形式为分片、主/从、主/主复制，尽管即使有这些能力，关系型系统也不是为了扩展到几百个节点以上而设计的。例如，MySQL 集群的上限是 255 个节点。另一方面，分布式数据库通过设计可以扩展到数千个节点。

### 8.8\. MongoDB

*MongoDB* 是一种面向文档的、分布式数据库，在 Node 开发者中非常受欢迎。它是时尚的 MEAN 堆栈（MongoDB, Express, Angular, Node）中的 *M*，并且通常是人们开始使用 Node 时遇到的第一个数据库。图 8.2 显示了 mongodb 模块在 npm 上的流行程度。

##### 图 8.2\. MongoDB 的使用统计

![图片](img/08fig02.jpg)

MongoDB 吸引了比其应得的更多批评和争议；尽管如此，它仍然是许多开发者的主要数据存储。MongoDB 在包括 Adobe、LinkedIn 和 eBay 在内的知名公司中有已知部署，甚至在欧洲核子研究组织（CERN）的大型强子对撞机的一个组件中也被使用。

MongoDB 数据库以无模式的 *集合* 存储文档。文档不需要有预定义的模式，单个集合中的文档也不需要共享相同的模式。这赋予了 MongoDB 很大的灵活性，尽管现在应用程序需要确保文档保持可预测的结构（保证一致性——ACID 中的 *C*）。

#### 8.8.1\. 执行安装和设置

MongoDB 需要安装到您的系统上。安装方式因平台而异。在 macOS 上，安装过程就像这样：

```
brew install mongodb
```

使用`mongod`可执行文件启动 MongoDB 服务器：

```
mongod --config /usr/local/etc/mongod.conf
```

最受欢迎的 MongoDB 驱动程序是 Christian Amor Kvalheim 的官方 mongodb 包：

```
npm install mongodb@².1.0 --save
```

Windows 用户应注意的是，驱动程序安装需要 msbuild.exe，该文件由 Microsoft Visual Studio 安装。

#### 8.8.2\. 连接到 MongoDB

在安装 mongodb 包并启动 mongod 服务器后，您可以从 Node 作为客户端连接，如下所示。

##### 列表 8.8\. 连接到 MongoDB

```
const { MongoClient } = require('mongodb');

MongoClient.connect('mongodb://localhost:27017/articles')
  .then(db  => {
    console.log('Client ready');
    db.close();
  }, console.error);
```

连接的成功处理程序会传递一个数据库客户端实例，所有数据库命令都从这个实例执行。

与数据库的大多数交互都是通过集合 API 进行的：

+   `collection.insert(doc)`—插入一个或多个文档

+   `collection.find(query)`—查找匹配查询的文档

+   `collection.remove(query)`—删除匹配查询的文档

+   `collection.drop()`—删除整个集合

+   `collection.update(query)`—更新匹配查询的文档

+   `collection.count(query)`—计算匹配查询的文档数量

`find`, `insert`, 和 `delete` 等操作根据操作的是单个还是多个值，有多种形式。例如：

+   `collection.insertOne`(doc)`—插入单个文档

+   `collection.insertMany([doc1, doc2])`—插入多个文档

+   `collection.findOne`(query)`—查找匹配查询的单个文档

+   `collection.updateMany(query)`—更新所有匹配查询的文档

#### 8.8.3\. 插入文档

`collection.insertOne` 将单个对象作为一个文档插入到集合中，如下一列表所示。成功处理程序传递一个包含操作状态元数据的对象。

##### 列表 8.9\. 插入文档

![](img/08lis09_alt.jpg)

`insertMany` 调用类似，但它接受多个文档的数组。`insertMany` 响应将包含一个 `insertedIds` 数组，按文档提供的顺序排列，而不是单个 `insertedId`。

#### 8.8.4\. 查询

从集合中读取文档的方法（如 `find`, `update`, 和 `remove`）接受一个查询参数，用于匹配文档。查询的最简单形式是一个对象，MongoDB 将使用该对象匹配具有相同结构和相同值的文档。例如，以下查询找到所有标题为“我喜欢蛋糕”的文章：

![](img/p0196_01.jpg)

查询可以用来通过其唯一 `_id` 匹配对象：

```
collection.findOne({ _id: someID })
```

或者根据*查询运算符*进行匹配：

![](img/p0196_02.jpg)

MongoDB 查询语言中存在许多*查询运算符*——例如：

+   `$eq`—等于特定值

+   `$neq`—不等于特定值

+   `$in`—在数组中

+   `$nin`—不在数组中

+   `$lt`, `$lte`, `$gt`, `$gte`—大于/小于或等于比较

+   `$near`—地理值接近某个坐标

+   `$not`, `$and`, `$or`, `$nor`—逻辑运算符

这些可以组合起来匹配几乎任何条件，并创建一个高度可读、复杂和表达性的查询语言。有关查询和查询操作器的更多信息，请参阅[`docs.mongodb.com/-manual/reference/operator/query/`](https://docs.mongodb.com/-manual/reference/operator/query/)。

下一列表展示了之前通过 MongoDB 实现的 Articles API 的示例，同时保持了几乎相同的外部接口。将此文件保存为 db.js（在本书的示例代码中为 listing8_10/db.js）。

##### 列表 8.10\. 使用 MongoDB 实现文章 API

![](img/ch08ex10-0.jpg)

![](img/ch08ex10-1.jpg)

以下代码片段展示了如何使用列表 8.10（示例代码中的 listing8_10/index.js）：

```
const db = require('./db');

db().then(() => {
  db.Article.create({ title: 'An article!' }).then(() => {
    db.Article.all().then(articles => {
      console.log(articles);
      process.exit();
    });
  });
});
```

这使用来自列表 8.10 的一个承诺连接到数据库，然后使用 `Article` 的 `create` 方法创建一个文章。之后，它加载所有文章并将它们记录下来。

#### 8.8.5\. 使用 MongoDB 标识符

MongoDB 中的标识符以*二进制 JSON*（BSON）格式编码。文档上的`_id`属性是一个 JavaScript 对象，它包装了一个 BSON 格式的`ObjectID`值。MongoDB 使用 BSON 来表示内部文档和作为传输格式。BSON 比 JSON 更节省空间，并且可以更快地解析，这意味着使用更少的带宽进行更快的数据库交互。

BSON `ObjectID`不仅仅是一个随机的字节序列；它编码了关于 ID 在哪里以及何时生成的元数据。例如，`ObjectID`的前四个字节是一个时间戳。这消除了在文档中存储`createdAt`时间戳属性的需求：

![](img/p0198_01_alt.jpg)

有关`ObjectID`格式的更多信息，请参阅[`docs.mongodb.com/manual/reference/method/ObjectId/`](https://docs.mongodb.com/manual/reference/method/ObjectId/)。

`ObjectIDs`可能看起来像是字符串，因为它们在终端中的打印方式，但它们是对象。`ObjectIDs`受到经典对象比较陷阱的影响：看似完全等效的值报告为不等效，因为它们引用了不同的对象。

在以下代码片段中，我们两次提取了相同的值。使用节点的内置 assert 模块，我们尝试断言对象或 ID 是等效的，但两者都失败了：

```
const Articles = db.collection('articles');
Articles.find().then(articles => {
  const article1 = articles[0];
  return Articles
    .findOne({_id: article1._id})
    .then(article2 => {
      assert.equal(article2._id, article1._id);
    });
});
```

这些断言产生的错误消息最初看起来很令人困惑，因为实际值似乎与预期值匹配：

```
operator: equal
expected: 577f6b45549a3b991e1c3c18
actual:   577f6b45549a3b991e1c3c18
operator: equal
expected:
  { _id: 577f6b45549a3b991e1c3c18, title: 'attractive-money' ... }
actual:
  { _id: 577f6b45549a3b991e1c3c18, title: 'attractive-money' ... }
```

可以通过使用每个`_id`上可用的`ObjectID`的`equal`方法正确检测等效性，或者你可以强制转换标识符并将它们作为字符串比较，或者使用 Node 内置 assert 模块上的`deepEquals`方法：

![](img/p0198_02_alt.jpg)

传递给 Node mongodb 驱动程序的标识符必须是 BSON 格式的`ObjectID`。可以使用`ObjectID`构造函数将字符串转换为`ObjectID`：

```
const { ObjectID } = require('mongodb');
const stringID = '577f6b45549a3b991e1c3c18';
const bsonID = new ObjectID(stringID);
```

在可能的情况下，应保持 BSON 形式；将字符串序列化和反序列化的成本与 MongoDB 通过将客户端标识符作为 BSON 提供所希望实现的性能提升相抵消。有关 BSON 格式的详细信息，请参阅[`bsonspec.org/`](http://bsonspec.org/)。

#### 8.8.6\. 使用副本集

MongoDB 的分布式功能大多超出了本书的范围，但在这个部分中，我们快速介绍了副本集的基本知识。许多`mongod`进程可以作为副本集的节点/成员运行。一个*副本集*由一个主节点和多个次节点组成。副本集的每个成员都需要分配一个唯一的端口和目录来存储其数据。实例不能共享端口或目录，并且目录必须在启动之前存在。

在以下列表中，您为每个成员创建一个唯一的目录，并从 27017 开始按顺序端口启动它们。您可能希望在新的终端标签中运行每个 `mongod` 命令，而不将其放入后台（不带尾随的 `&`）。

##### 列表 8.11\. 启动副本集

![图片](img/08lis11_alt.jpg)

副本集运行后，MongoDB 需要执行一些初始化操作。您需要连接到您希望成为第一个 *主节点* 的实例的端口（默认为 27017），并调用 `rs.initiate()`，如下所示。然后您需要将每个实例添加为副本集的成员。请注意，您需要提供您连接到的机器的主机名。

##### 列表 8.12\. 初始化副本集

![图片](img/08lis12_alt.jpg)

当 MongoDB 客户端连接时，需要了解所有可能的副本集成员，尽管并非所有成员都需要当前在线。连接后，您可以像往常一样使用 MongoDB 客户端。列表 8.13 展示了如何创建包含三个成员的副本集。

##### 列表 8.13\. 连接到副本集

![图片](img/08lis13_alt.jpg)

如果任何 mongod 节点崩溃，只要至少有两个实例正在运行，系统将继续工作。如果主节点崩溃，将自动选举一个次要节点提升为主节点。

#### 8.8.7\. 理解写关注点

MongoDB 允许开发者对应用程序各个区域可接受的性能和安全权衡进行细粒度控制。为了在不出现意外的情况下使用 MongoDB，特别是当副本集节点数量增加时，理解读写关注点的概念非常重要。在本节中，我们仅涉及写关注点，因为它们是最重要的。

*写关注点* 指定了在整体操作响应成功之前，数据需要成功写入多少个 mongod 实例。如果没有明确指定，默认写关注点是 1，这确保了数据至少已写入一个节点。它可能无法为关键数据提供足够的保证；如果节点在复制到其他节点之前崩溃，数据可能会丢失。

设置零写关注点是可能的，并且通常是期望的，这样应用程序就不需要等待任何响应：

```
db.collection('data').insertOne(data, { w: 0 });
```

零写关注点提供了最高的性能，但提供的持久性保证最少，通常仅用于临时或不重要的数据（例如日志或缓存的写入）。

如果您连接到副本集，您可以指定大于 1 的写关注点。将数据复制到更多节点可以降低数据丢失的可能性，但会以执行操作时增加延迟为代价：

```
db.collection('data').insertOne(data, { w: 2 });
db.collection('data').insertOne(data, { w: 5 });
```

您可能希望根据集群中节点数量的变化来扩展写入关注级别。如果将写入关注级别设置为`majority`，MongoDB 本身可以动态地完成此操作。这确保数据被写入超过 50%的可用节点：

```
db.collection('data').insertOne(data, { w: 'majority' });
```

默认的写入关注级别为 1 可能无法为关键数据提供足够的保证。如果节点在复制到其他节点之前崩溃，数据可能会丢失。

将写入关注级别设置为大于 1 确保在继续之前数据存在于多个 mongod 实例中。在同一台机器上运行多个实例可以提供保护，但无法帮助解决系统级故障，如磁盘空间或 RAM 耗尽。您可以通过在多台机器上运行实例并确保写入传播到这些节点来防止机器故障，但这又会使速度变慢，并且无法防止数据中心级故障。在不同数据中心运行节点可以防止数据中心中断，但确保数据在数据中心之间复制将大大影响性能。

总的来说，你添加的保证越多，系统就会变得越慢、越复杂。这不仅仅是一个特定于 MongoDB 的问题；它涉及到任何和所有数据存储的问题。没有完美的解决方案，你需要为应用程序的各个部分决定可接受的危险水平。

有关 MongoDB 复制工作方式的更多信息，请参阅以下资源：

+   [`docs.mongodb.com/manual/faq/replica-sets/`](https://docs.mongodb.com/manual/faq/replica-sets/)

+   [`docs.mongodb.com/manual/faq/concurrency/`](https://docs.mongodb.com/manual/faq/concurrency/)

### 8.9. 键值存储

键值存储中的每条记录由一个键和一个值组成。在许多键值系统中，值可以是任何数据类型，任何长度或结构。从数据库的角度来看，值是不透明的原子：数据库不知道也不关心数据的类型，并且不能被细分或访问，除非作为整体。这与关系型数据库中的值存储形成对比：数据存储在一系列表中，这些表包含数据行，这些行被分隔到预定义的列中。在键值存储中，管理数据格式的责任交给了应用程序。

键值存储通常可以在应用程序的性能关键路径中找到。理想情况下，值以这种方式排列，即完成任务所需的读取次数绝对最少。与其它数据库类型相比，键值存储具有更简单的查询能力。理想情况下，复杂查询是预先计算的；否则，它们需要在应用程序内部而不是数据库中执行。这种限制可能导致易于理解和预测的性能特征。

最受欢迎的键/值存储，如 Redis 和 Memcached，通常用于易失性存储（如果进程退出，数据将丢失）。避免写入磁盘是提高性能的最佳方法之一。当数据可以重新生成或损失无关紧要时，这可以是一个可接受的权衡；例如，缓存和用户会话。

键/值存储可能存在一种偏见，即它们不能用于主存储，但这并不总是正确的。许多键/值存储提供的持久性“与真实”数据库相当。

### 8.10\. Redis

*Redis* 是一个流行的内存数据结构存储。尽管许多人认为 Redis 是一个键/值存储，但键和值仅代表 Redis 在各种有用、基本数据结构中支持的功能的一个子集。图 8.3 显示了 npm 上 redis 包的使用统计。

##### 图 8.3\. npm 上 redis 包的统计信息

![](img/08fig03.jpg)

内置在 Redis 中的数据结构包括以下内容：

+   字符串

+   哈希

+   列表

+   集合

+   有序集合

Redis 还自带了许多其他有用的功能：

+   ***位图数据—*** 在值中进行直接位操作。

+   ***地理空间索引—*** 使用半径查询存储地理空间数据。

+   ***通道—*** 一种发布/订阅数据交付机制。

+   ***TTLs—*** 可以配置过期时间，在此之后它们将自动删除。

+   ***LRU 过期—*** 可选地移除最近未使用过的值，以保持最大内存使用。

+   ***HyperLogLog—*** 高性能的集合基数近似，同时保持低内存占用（不需要存储每个成员）。

+   ***复制、集群和分区—*** 水平扩展和数据持久性。

+   ***Lua 脚本—*** 通过自定义命令扩展 Redis。

在本节中，你会找到几个 Redis 命令的列表。它们不是参考，而是为了让你了解 Redis 的可能性。这是一个功能强大且多才多艺的工具。有关更多详细信息，请参阅 [`redis.io/commands`](http://redis.io/commands)。

#### 8.10.1\. 执行安装和设置

你可以通过系统包管理工具安装 Redis。在 macOS 上，你可以使用 Homebrew 轻松安装它：

```
brew install redis
```

使用 `redis-server` 可执行文件启动服务器：

```
redis-server /usr/local/etc/redis.conf
```

服务器默认监听端口 6379。

#### 8.10.2\. 执行初始化

Redis 客户端实例是通过 redis npm 包的 `createClient` 函数创建的：

```
const redis = require('redis');
const db = redis.createClient(6379, '127.0.0.1');
```

此函数接受端口和主机作为参数。但如果你在本地机器上使用 Redis 服务器默认端口，你根本不需要提供任何参数：

```
const db = redis.createClient();
```

Redis 客户端实例是一个 `EventEmitter`，因此你可以为各种 Redis 状态事件附加监听器，如下一列表所示。你可以立即开始向客户端发送命令，它们将被缓冲，直到连接就绪。

##### 列表 8.14\. 连接到 Redis 并监听状态事件

```
const redis = require('redis');

const db = redis.createClient();
db.on('connect', () => console.log('Redis client connected to server.'));
db.on('ready', () => console.log('Redis server is ready.'));
db.on('error', err => console.error('Redis error', err));
```

如果发生连接或客户端问题，错误处理程序将触发。如果触发 `error` 事件但没有附加错误处理程序，应用程序进程将抛出错误并崩溃；这是 Node 中所有 `EventEmitters` 的特性。如果连接失败并提供了错误处理程序，Redis 客户端将尝试重试连接。

#### 8.10.3\. 操作键/值对

Redis 可以用作字符串和任意二进制数据的通用键/值存储。可以使用 `set` 和 `get` 方法分别读取和写入键/值对：

```
db.set('color', 'red', err => {
  if (err) throw err;
});

db.get('color', (err, value) => {
  if (err) throw err;
  console.log('Got:', value);
});
```

如果您`set`一个现有的键，值将被覆盖。如果您尝试获取一个不存在的键，值将是`null`；这不被认为是错误。

可以使用以下命令来检索和操作值：

+   `append`

+   `decr`

+   `decrby`

+   `get`

+   `getrange`

+   `getset`

+   `incr`

+   `incrby`

+   `incrbyfloat`

+   `mget`

+   `mset`

+   `msetnx`

+   `psetex`

+   `set`

+   `setex`

+   `setnx`

+   `setrange`

+   `strlen`

#### 8.10.4\. 操作键

您可以使用 `exists` 检查键是否存在。这适用于任何数据类型：

```
db.exists('users', (err, doesExist) => {
  if (err) throw err;   console.log('users exists:', doesExist);
});
```

除了 `exists` 之外，以下所有命令都可以与任何键一起使用，无论值的类型如何（这些命令适用于字符串、集合、列表等）：

+   `del`

+   `exists`

+   `rename`

+   `renamenx`

+   `sort`

+   `scan`

+   `type`

#### 8.10.5\. 编码和数据类型

Redis 服务器将键和值存储为二进制对象；它不依赖于传递给客户端的值的编码。任何有效的 JavaScript 字符串（UCS2/UTF16）都可以用作有效的键或值：

```
db.set('greeting', '你好', redis.print);
db.get('greeting', redis.print);
db.set('icon', '?', redis.print);
db.get('icon', redis.print);
```

默认情况下，键和值在写入时会转换为字符串。例如，如果您使用数字`set`一个键，当您尝试获取相同的键时，它将是一个字符串：

![](img/p0205_01_alt.jpg)

Redis 客户端将数字、布尔值和日期无声地转换为字符串，并且它也乐意接受缓冲区对象。尝试将任何其他 JavaScript 类型（例如，Object、Array、RegExp）作为值设置，将打印出应予以注意的警告：

```
db.set('users', {}, redis.print);

Deprecated: The SET command contains a argument of type Object.
This is converted to "[object Object]" by using .toString() now
  and will return an error from v.3.0 on.
Please handle this in your code to make sure everything works
  as you intended it to.
```

在未来，这将是一个错误，因此调用应用程序*必须*负责确保向 Redis 客户端传递正确的类型。

##### 注意：单值与多值数组

如果您尝试设置一个值数组，客户端将产生一个神秘的错误，“ReplyError: ERR syntax error”。

```
db.set('users', ['Alice', 'Bob'], redis.print);
```

但请注意，当数组中只有一个值时，不会发生错误：

```
db.set('user', ['Alice'], redis.print);
db.get('user', redis.print);
```

这种类型的错误可能只有在您在生产环境中运行时才会显示症状，因为它可以轻易地逃避检测，如果测试套件恰好只产生单值数组，这在简化测试数据时很常见。请注意！

##### 带缓冲区的二进制数据

Redis 能够存储任意字节数据，这意味着您可以在其中存储任何类型的数据。Node 客户端通过为 Node 的 `Buffer` 类型提供特殊处理来支持此功能。当缓冲区作为键或值传递给 Redis 客户端时，字节将未修改地发送到 Redis 服务器。这避免了意外数据损坏和字符串与缓冲区之间不必要的 marshalling 之间的性能惩罚。例如，如果您想将磁盘或网络中的数据直接写入 Redis，直接将缓冲区写入 Redis 比先将其转换为字符串更有效。

|  |
| --- |

**缓冲区**

*缓冲区* 是您从 Node 的核心文件和网络 API 默认接收的内容。它们是围绕连续的二进制数据块的容器，在 Node 在 JavaScript 还没有自己的原生二进制数据类型（`Uint8Array`、`Float32Array` 等等）之前就已经被引入。今天，缓冲区在 Node 中作为 `Uint8Array` 的专用子类实现。Buffer API 在 Node 中全局可用；您不需要 require 任何内容就可以使用它。

查看 [`github.com/nodejs/node/blob/master/lib/buffer.js`](https://github.com/nodejs/node/blob/master/lib/buffer.js)

|  |
| --- |

Redis 最近添加了用于操作字符串值单个位的命令，这在处理缓冲区时可能很有用：

+   `bitcount`

+   `bitfield`

+   `bitop`

+   `setbit`

+   `bitpos`

#### 8.10.6\. 使用哈希

*哈希* 是键/值对的集合。`hmset` 命令接受一个键和一个表示哈希键/值对的对象。您可以通过使用 `hmget` 来获取键/值对，如下一列表所示。

##### 列表 8.15\. 在 Redis 哈希的元素中存储数据

![](img/08lis15_alt.jpg)

您不能在 Redis 哈希中存储嵌套对象。它只提供单层键和值。

以下命令作用于哈希：

+   `hdel`

+   `hexists`

+   `hget`

+   `hgetall`

+   `hincrby`

+   `hincrbyfloat`

+   `hkeys`

+   `hlen`

+   `hmget`

+   `hmset`

+   `hset`

+   `hsetnx`

+   `hstrlen`

+   `hvals`

+   `hscan`

#### 8.10.7\. 使用列表

*列表* 是字符串值的有序集合。列表可以包含相同值的多个副本。列表在概念上与数组相似。列表最适合用作栈（LIFO：后进先出）或队列（FIFO：先进先出）数据结构。

以下代码展示了在列表中存储和检索值。`lpush` 命令向列表添加一个值。`lrange` 命令通过使用起始和结束索引检索值范围。以下代码中的 `-1` 参数表示列表的最后一个项目，因此这种使用 `lrange` 的方式检索所有列表项：

```
client.lpush('tasks', 'Paint the bikeshed red.', redis.print);
client.lpush('tasks', 'Paint the bikeshed green.', redis.print);
client.lrange('tasks', 0, -1, (err, items) => {
  if (err) throw err;
  items.forEach(item => console.log(`  ${item}`));
});
```

列表不包含任何内置方法来确定值是否在列表中，或者任何发现特定值在列表中索引的方法。您可以手动遍历列表以获取这些信息，但这是一种效率非常低的方法，应该避免。如果您需要这些类型的功能，您应该考虑不同的数据结构，例如集合，甚至可能是在列表之外使用。在多个数据结构之间复制数据通常是为了利用各种性能特性。

以下命令在列表上操作：

+   `blpop`

+   `brpop`

+   `lindex`

+   `linsert`

+   `llen`

+   `lpop`

+   `lpush`

+   `lpushx`

+   `lrange`

+   `lrem`

+   `lset`

+   `ltrim`

+   `rpop`

+   `rpush`

+   `rpushx`

#### 8.10.8\. 使用集合

*集合* 是一个无序的唯一值集合。测试成员资格，以及从集合中添加和删除项目可以在 O(1) 时间内完成，使其成为一个高性能的结构，适用于许多任务：

```
db.sadd('admins', 'Alice', redis.print);
db.sadd('admins', 'Bob', redis.print);
db.sadd('admins', 'Alice', redis.print);
db.smembers('admins', (err, members) => {
  if (err) throw err;
  console.log(members);
});
```

以下命令在 Redis 集合上操作：

+   `sadd`

+   `scard`

+   `sdiff`

+   `sdiffstore`

+   `sinter`

+   `sinterstore`

+   `sismember`

+   `smembers`

+   `spop`

+   `srandmember`

+   `srem`

+   `sunion`

+   `sunionstore`

+   `sscan`

#### 8.10.9\. 使用频道提供 pub/sub

Redis 通过提供频道超越了传统数据存储的角色。*频道* 是一种数据交付机制，它提供了发布/订阅功能，如图 8.4 所示的概念图。它们对于实时应用，如聊天和游戏，非常有用。

##### 图 8.4\. Redis 频道为常见的数据交付场景提供了一个简单的解决方案。

![](img/08fig04.jpg)

Redis 客户端可以订阅或发布到频道。发布到频道的消息将被发送到所有订阅者。发布者不需要了解订阅者，订阅者也不需要了解发布者。这种发布者和订阅者之间的解耦使得这种模式既强大又简洁。

以下列表显示了如何使用 Redis 的发布/订阅功能来实现 TCP/IP 聊天服务器的一个示例。

##### 列表 8.16\. 使用 Redis pub/sub 功能实现的简单聊天服务器

![](img/08lis16_alt.jpg)

#### 8.10.10\. 提高 Redis 性能

hiredis npm 包是从 JavaScript 到官方 Hiredis C 库中的协议解析器的本地绑定。Hiredis 可以显著提高 Node Redis 应用程序的性能，尤其是如果您使用 `sunion, sinter, lrange`, 和 `zrange` 操作与大数据集。

要使用 hiredis，只需将其与应用程序中的 redis 包一起安装，Node redis 包将在下次启动时自动检测并使用它：

```
npm install hiredis --save
```

使用 hiredis 几乎没有缺点，但由于它是从 C 代码编译的，因此在为某些平台构建 hiredis 时可能会出现一些复杂或限制。与所有原生插件一样，在更新 Node 后，您可能需要使用 `npm rebuild` 重新构建 hiredis。

### 8.11\. 嵌入式数据库

嵌入式数据库不需要安装或管理外部服务器。它直接嵌入到你的应用程序进程中运行。与嵌入式数据库的通信通常通过应用程序中的直接过程调用进行，而不是通过进程间通信（IPC）通道或网络。

在许多情况下，应用程序需要自给自足，因此嵌入式数据库是唯一的选择（例如，移动或桌面应用程序）。嵌入式数据库也可以用于 Web 服务器，通常用于驱动高吞吐量功能，如用户会话或缓存，有时甚至作为主要存储。

在 Node 和 Electron 应用程序中常用的一些嵌入式数据库如下：

+   SQLite

+   LevelDB

+   RocksDB

+   Aerospike

+   EJDB

+   NeDB

+   LokiJS

+   Lowdb

NeDB、LokiJS 和 Lowdb 都是用纯 JavaScript 编写的，这使得它们可以嵌入到 Node/Electron 应用程序中。大多数嵌入式数据库都是简单的键/值或文档存储，尽管 SQLite 是一个值得注意的例外，它是一个可嵌入的 *关系型* 存储。

### 8.12\. LevelDB

*LevelDB* 是由 Google 在 2011 年初开发的一个可嵌入的、持久化的键/值存储，最初用于 Chrome 中 IndexedDB 实现的后端存储。LevelDB 的设计基于 Google Bigtable 数据库的概念。LevelDB 可以与 Berkley DB、Tokyo/Kyoto Cabinet 和 Aerospike 等数据库相媲美，但在本书的上下文中，你可以将 LevelDB 视为仅具有最基本功能的可嵌入 Redis。像许多嵌入式数据库一样，LevelDB 不是多线程的，也不支持使用相同底层文件存储的多个实例，因此在没有包装应用程序的情况下无法在分布式环境中工作。

LevelDB 以字典序对键进行排序存储任意字节数组。值通过使用 Google 的 Snappy 压缩算法进行压缩。数据始终持久化到磁盘；总数据容量不受机器上 RAM 量的限制，与 Redis 这样的内存存储不同。

LevelDB 提供了一组非常简单的自解释操作：`Get`、`Put`、`Del` 和 `Batch`。LevelDB 还可以捕获当前数据库状态的快照，并创建双向迭代器以在数据集中向前或向后移动。创建迭代器会创建一个隐式快照；迭代器可以看到的数据不能通过后续写入进行更改。

LevelDB 以 LevelDB 分支的形式构成了其他数据库的基础。LevelDB 的重要分支数量可以归因于 LevelDB 本身的简单性：

+   RocksDB by Facebook

+   HyperLevelDB by Hyperdex

+   Riak by Basho

+   leveldb-mcpe by Mojang（Minecraft 的创造者）

+   bitcoin/leveldb for the bitcoind project

关于 LevelDB 的更多信息，请参阅 [`leveldb.org/`](http://leveldb.org/)。

#### 8.12.1\. LevelUP 和 LevelDOWN

Node 的 LevelDB 支持由 Node 基金会主席和多产的澳大利亚开发者 Rod Vagg 编写的 LevelUP 和 LevelDOWN 包提供。LevelDOWN 是 LevelDB 的一个简单、无糖的 C++ 绑定，用于 Node，你不太可能直接与之交互。LevelUP 使用更方便、更符合 Node 习惯的接口包装 LevelDOWN API，增加了对键/值编码、JSON、缓冲写入直到数据库打开以及将 LevelDB 迭代器接口包装在 Node 流中的支持。图 8.5 展示了 levelup 在 npm 上的流行度。

##### 图 8.5\. levelup 包在 npm 上的统计信息

![](img/08fig05.jpg)

#### 8.12.2\. 安装

在 Node 应用程序中使用 LevelDB 的一大便利是它**内嵌**：你可以仅使用 npm 安装所需的一切。你不需要安装任何额外的软件；只需执行以下命令，你就可以开始使用 LevelDB：

```
npm install level --save
```

level 包是 LevelUP 和 LevelDOWN 包的简单便利包装，提供了一个预配置的 LevelUP API，用于使用 LevelDown 后端。可以在 LevelUP 的 README 文件中找到 level 包暴露的 LevelUP API 的文档：

+   [www.npmjs.com/package/levelup](http://www.npmjs.com/package/levelup)

+   [www.npmjs.com/package/leveldown](http://www.npmjs.com/package/leveldown)

#### 8.12.3\. API 概述

LevelDB 客户端存储和检索值的主要方法如下：

+   `db.put(key, value, callback)`—在键下存储一个值

+   `db.get(key, callback)`—获取键下的值

+   `db.del(key, callback)`—删除键下的值

+   `db.batch().write()`—执行批量操作

+   `db.createKeyStream(options)`—数据库中的键流

+   `db.createValueStream(options)`—数据库中的值流

#### 8.12.4\. 初始化

当你初始化 level 时，需要提供一个存储数据的目录路径，如下所示；如果目录不存在，则会创建该目录。有一个宽松的社区约定，即给这个目录添加一个 .db 扩展名（例如，./app.db）。

##### 列表 8.17\. 初始化 level 数据库

```
const level = require('level');

const db = level('./app.db', {
  valueEncoding: 'json'
});
```

在调用 `level()` 之后，返回的 LevelUP 实例立即准备好开始接受命令，同步地。在 LevelDB 存储打开之前发出的命令将被缓冲，直到存储打开。

#### 8.12.5\. 键/值编码

由于 LevelDB 可以存储任意类型的数据，无论是键还是值，因此处理数据序列化和反序列化的任务就交给了调用应用程序。LevelUp 可以通过以下数据类型直接配置来编码键和值：

+   `utf8`

+   `json`

+   `binary`

+   `id`

+   `hex`

+   `ascii`

+   `base64`

+   `ucs2`

+   `utf16le`

默认情况下，键和值都编码为 UTF-8 字符串。在列表 8.17 中，键将保持为 UTF-8 字符串，但值将编码/解码为 JSON。JSON 编码允许以类似于文档存储（如 MongoDB）的方式存储和检索结构化值，如对象或数组。但请注意，与真正的文档存储不同，使用 LevelDB 无法访问值内的键；值是透明的。用户还可以提供自己的自定义编码——例如，支持不同的结构化数据格式，如 MessagePack。

#### 8.12.6\. 读取和写入键/值对

核心 API 很简单：使用`put(key, value)`来写入值，`get(key)`来读取值，以及`del(key)`来删除值，如下一列表所示。下一列表中的代码应附加到列表 8.17 中的代码；要查看完整示例，请参阅书中示例代码的 ch08-databases/listing8_18/index.js。

##### 列表 8.18\. 读取和写入值

```
const key = 'user';
const value = {
  name: 'Alice'
};

db.put(key, value, err => {
  if (err) throw err;
  db.get(key, (err, result) => {
    if (err) throw err;
    console.log('got value:', result);
    db.del(key, (err) => {
      if (err) throw err;
      console.log('value was deleted');
    });
  });
});
```

如果你在一个已存在的键上`put`一个值，旧值将被覆盖。尝试获取一个不存在的键将导致错误。这个错误对象将是一个特定的类型，`NotFoundError`，并且有一个特殊属性`err.notFound`，可以用来区分它和其他类型的错误。这看起来可能有些不寻常，但因为在 LevelDB 中没有内置的方法来检查存在性，LevelUp 需要能够区分不存在的值和`undefined`的值。与`get`不同，尝试删除一个不存在的键不会导致错误。

##### 列表 8.19\. 获取不存在的键

```
db.get('this-key-does-not-exist', (err, value) => {
  if (err && !err.notFound) throw err;
  if (err && err.notFound) return console.log('Value was not found.');
  console.log('Value was found:', value);
});
```

所有的数据读取和写入操作都接受一个可选的`options`参数，用于覆盖当前操作的编码选项，如下一列表所示。

##### 列表 8.20\. 覆盖特定操作的编码

```
const options = {
  keyEncoding: 'binary',
  valueEncoding: 'hex'
};

db.put(new Uint8Array([1, 2, 3]), '0xFF0099', options, (err) => {
  if (err) throw err;
  db.get(new Uint8Array([1, 2, 3]), options, (err, value) => {
    if (err) throw err;
    console.log(value);
  });
});
```

#### 8.12.7\. 可插拔的后端

LevelUP/LevelDOWN 分离的一个令人高兴的副作用是 LevelUP 不受限于使用 LevelDB 作为存储后端。你可以使用 MemDown API 包装的任何内容作为 LevelUP 的存储后端，这允许你使用完全相同的 API 与许多数据存储进行接口。

一些替代后端的示例如下：

+   MySQL

+   Redis

+   MongoDB

+   JSON 文件

+   Google 电子表格

+   AWS DynamoDB

+   Windows Azure 表存储

+   浏览器网页存储（IndexedDB/localStorage）

这种轻松更换存储介质或甚至编写自己的自定义后端的能力意味着你可以在许多情况和环境中使用单一、一致的数据库 API 和工具集。一个数据库 API 统治一切！

常用的替代后端是 *memdown*，它将值完全存储在内存中而不是磁盘上，类似于使用 SQLite 的内存模式。这在测试环境中可以特别有用，以减少测试设置和拆除的成本。

要运行以下列表，请确保你已经安装了 LevelUP 和 memdown 包：

```
npm install --save levelup memdown
```

##### 列表 8.21. 使用 memdown 与 LevelUP

![](img/08lis21_alt.jpg)

在这个示例中，你可以使用之前使用的相同级别的包，因为它只是 LevelUP 的包装器。但如果你没有使用 level 附带捆绑的 LevelDB 支持的 LevelDOWN，你只需使用 LevelUP，并通过 LevelDOWN 避免对 LevelDB 的二进制依赖。

#### 8.12.8. 模块化数据库

LevelDB 的性能和简约风格与许多 Node 开发者产生共鸣，并在 Node 社区中催生了模块化数据库运动。概念是能够选择并选择应用程序需要的确切功能，并为特定的用例定制数据库。

这里只是 npm 包中通过模块化 LevelDB 功能的一些示例：

+   原子更新

+   自动递增键

+   地理空间查询

+   实时更新流

+   LRU 驱逐

+   Map/reduce 作业

+   主/主复制

+   主/从复制

+   SQL 查询

+   二级索引

+   触发器

+   版本化数据

LevelUP 维基百科维护了一个相当全面的 LevelDB 生态系统概述：[`github.com/Level/levelup/wiki/Modules`](https://github.com/Level/levelup/wiki/Modules)，或者你可以在 npm 上搜索*leveldb*，在撰写本文时，有 898 个包。图 8.6 显示了 LevelDB 在 npm 上的流行程度。

##### 图 8.6. npm 上第三方 LevelDB 包的示例

![](img/08fig06_alt.jpg)

### 8.13. 序列化和反序列化成本高昂

重要的是要记住，内置的 JSON 操作既昂贵又阻塞；在将数据从 JSON 序列化和反序列化时，你的进程无法执行其他任何操作。对于大多数其他序列化格式也是如此。在 Web 服务器上，序列化通常是一个关键瓶颈。减少其影响的最佳方法是尽量减少其执行频率和需要处理的数据量。

通过使用不同的序列化格式（例如，MessagePack 或 Protocol Buffers），你可能会有一些速度提升，但在从减少有效载荷大小和不必要的序列化/反序列化步骤中挤出可能的收益之后，才应考虑替代格式。

`JSON.stringify`和`JSON.parse`是原生函数，并且已经过充分优化，但当需要处理兆字节级的数据时，它们很容易被压垮。为了演示，以下列表对大约 10 MB 的数据进行了序列化和反序列化的基准测试。

##### 列表 8.22. 序列化基准测试

```
const bytes = require('pretty-bytes');
const obj = {};
for (let i = 0; i < 200000; i++) {
  obj[i] = {
    [Math.random()]: Math.random()
  };
}

console.time('serialise');
const jsonString = JSON.stringify(obj);
console.timeEnd('serialise');
console.log('Serialised Size', bytes(Buffer.byteLength(jsonString)));
console.time('deserialise');
const obj2 = JSON.parse(jsonString);
console.timeEnd('deserialise');
```

在一台 2015 年 3.1 GHz Intel Core i7 MacBook Pro 上运行 Node 6.2.2，大约需要 140 毫秒来序列化，335 毫秒来反序列化大约 10 MB 的数据。如果这样的负载发生在 Web 服务器上，这将是一场灾难，因为这些步骤是完全阻塞的，并且必须按顺序处理。这样的服务器在序列化时只能处理大约每秒令人沮丧的七个请求，在反序列化时大约每秒三个请求。

### 8.14. 浏览器存储

Node 中使用的异步编程模型适用于许多用例，因为假设 I/O 是大多数 Web 应用中最大的瓶颈。你可以同时减少服务器负载并提高用户体验的最重要的事情之一是利用客户端数据存储。一个快乐的用户是不需要等待完整的网络往返才能得到结果的。客户端存储还可以通过允许应用程序在用户或您的服务离线时至少保持半功能状态，从而促进应用程序可用性的提高。

#### 8.14.1\. Web 存储：localStorage 和 sessionStorage

Web 存储定义了一个简单的键/值存储，并且在桌面和移动浏览器中都得到了很好的支持。使用 Web 存储，一个域可以在浏览器中持久化一定量的数据，并在稍后检索它，即使网站已被刷新，标签页已关闭或浏览器已关闭。Web 存储是客户端持久化的首选方案。其获胜的特点是它的简单性。

有两个 Web 存储 API：localStorage 和 sessionStorage。sessionStorage 实现了与 localStorage 相同的 API，尽管它在持久化行为上有所不同。与 localStorage 一样，存储在 sessionStorage 中的数据在页面重新加载时保持持久，但与 localStorage 不同，当页面会话结束时（当标签页或浏览器关闭时），所有 sessionStorage 数据都会过期。sessionStorage 数据不能从不同的浏览器窗口中访问。

Web 存储 API 的开发是为了克服浏览器 Cookie 的限制。具体来说，Cookie 不适合在同一个域的多个活动标签页之间共享数据。如果用户在多个标签页中执行活动，可以使用 sessionStorage 在这些标签页之间共享状态，而无需使用网络。

Cookie 不适用于处理需要在多个会话、标签页和窗口中持续存在的长期数据；例如，用户创建的文档或电子邮件。这正是 localStorage 被设计来处理的使用场景。根据特定浏览器的不同，可以存储在 Web 存储中的数据量上限也有所不同。移动浏览器仅限于 5 MB 的存储空间。

##### API 概述

localStorage API 提供了以下方法来处理键和值：

+   `localStorage.setItem(key, value)`—在键下存储一个值

+   `localStorage.getItem(key)`—获取键下的值

+   `localStorage.removeItem(key)`—移除键下的值

+   `localStorage.clear()`—移除所有键和值

+   `localStorage.key(index)`—获取索引处的值

+   `localStorage.length`—localStorage 中的键总数

#### 8.14.2\. 读取和写入值

键和值都必须是字符串。如果你传递了一个非字符串的值，它将被自动转换为字符串。这种转换不会生成 JSON 字符串；相反，它使用`.toString`进行简单的转换。对象最终会被序列化为字符串`[object Object]`。应用程序必须将值序列化和反序列化成字符串，以便在 Web 存储中存储更复杂的数据类型。下面的列表展示了如何将 JSON 存储在 localStorage 中。

##### 列表 8.23\. 在 Web 存储中存储 JSON

```
const examplePreferences = {
  temperature: 'Celcius'
};

// serialize on write
localStorage.setItem('preferences', JSON.stringify(examplePreferences));

// deserialize on read
const preferences = JSON.parse(localStorage.getItem('preferences'));
console.log('Loaded preferences:', preferences);
```

访问 Web 存储数据相对较快，尽管它也是同步的。Web 存储在执行读写操作时会阻塞 UI 线程。对于小负载，这种开销可能不明显，但应避免过度读取或写入，尤其是大量数据时。不幸的是，Web 存储在 Web Worker 中不可用，因此所有读取和写入都必须在主 UI 线程上执行。有关各种客户端存储技术性能影响的详细分析，请参阅 PouchDB 作者 Nolan Lawson 的这篇帖子：[`nolanlawson.com/2015/09/29/indexeddb-websql-localstorage-what-blocks-the-dom/`](http://nolanlawson.com/2015/09/29/indexeddb-websql-localstorage-what-blocks-the-dom/)。

Web 存储 API 没有提供内置的查询、按范围选择键或搜索值的工具。你只能通过键访问项目。要执行搜索，你可以设置和维护自己的索引；或者如果你的数据集足够小，你可以遍历整个数据集。以下列表遍历了 localStorage 中的所有键。

##### 列表 8.24\. 在 localStorage 中遍历整个数据集

```
function getAllKeys() {
  return Object.keys(localStorage);
}

function getAllKeysAndValues() {
  return getAllKeys()
    .reduce((obj, str) => {
      obj[str] = localStorage.getItem(str);
      return obj;
    }, {});
}

// Get all values
const allValues = getAllKeys().map(key => localStorage.getItem(key));

// As an object
console.log(getAllKeysAndValues());
```

与大多数键/值存储类似，键只有一个命名空间。例如，如果你有`posts`和`comments`，就无法为帖子或评论创建单独的存储。通过在每个键前使用前缀来创建自己的“命名空间”很容易，如以下列表所示。

##### 列表 8.25\. 命名空间键

```
localStorage.setItem(`/posts/${post.id}`, post);
localStorage.setItem(`/comments/${comment.id}`, comment);
```

要获取命名空间内的所有项目，你可以使用前面的`getAllKeys`函数过滤所有项目，如以下列表所示。

##### 列表 8.26\. 获取命名空间中的所有项目

```
function getNamespaceItems(namespace) {
  return getAllKeys().filter(key => key.startsWith(namespace));
}
console.log(getNamespaceItems('/exampleNamespace'));
```

注意，这个循环遍历 localStorage 中的每个键，所以在遍历大量项目时要当心性能。

由于 localStorage API 是同步的，因此在使用时存在一些限制。例如，你可以使用 localStorage 永久缓存任何接受并返回可序列化 JSON 数据的函数的结果，如以下列表所示。

##### 列表 8.27\. 使用 localStorage 进行持久化缓存

```
// subsequent calls with the same argument will fetch the memoized result
function memoizedExpensiveOperation(data) {
  const key = `/memoized/${JSON.stringify(data)}`;
  const memoizedResult = localStorage.getItem(key);
  if (memoizedResult != null) return memoizedResult;
  // do expensive work
  const result = expensiveWork(data);
  // save result to localStorage, never calculate again
  localStorage.setItem(key, result);
  return result;
}
```

注意，如果操作特别慢，那么缓存的好处才能超过序列化/反序列化过程的开销（例如，加密算法）。因此，localStorage 在节省跨网络移动数据的时间方面效果最佳。

Web 存储确实存在限制，但对于合适的任务，它可以是一个强大且简单的工具。其他需要调查的浏览器内存储主题如下：

+   IndexedDB

+   服务工作者

+   离线优先

#### 8.14.3\. localForage

Web 存储的主要缺点是其阻塞的、同步的 API 以及在某些浏览器中有限的存储容量。除了 Web 存储之外，大多数现代浏览器还支持 WebSQL 和 IndexedDB 中的一个或两个。这两个数据存储都是非阻塞的，并且可以可靠地存储比 Web 存储多得多的数据。

但是，像我们使用 Web 存储 API 那样直接使用这些数据库是不可取的。WebSQL 已弃用，其继任者 IndexedDB 有一个特别不友好且冗长的 API，更不用说浏览器支持的碎片化了。为了方便且可靠地在浏览器中存储数据而不阻塞，我们被迫使用非标准工具来“规范化”环境。来自 Mozilla 的 localForage 库（[`mozilla.github.io/localForage/`](http://mozilla.github.io/localForage/））就是这样一种规范化的工具。

##### API 概述

便利的是，localForage 接口紧密地模仿了 Web 存储，尽管是以异步、非阻塞的形式：

+   `localforage.setItem(key, value, callback)`—在键下存储一个值

+   `localforage.getItem(key, callback)`—获取键下的值

+   `localforage.removeItem(key, callback)`—移除键下的值

+   `localforage.clear(callback)`—移除所有键和值

+   `localforage.key(index, callback)`—获取索引处的值

+   `localforage.length(callback)`—localForage 中的键的数量

localForage API 还包括一些没有 Web 存储等价的实用功能：

+   `localforage.keys(callback)`—移除所有键和值

+   `localforage.iterate(iterator, callback)`—遍历键和值

#### 8.14.4\. 读取和写入

localForage API 支持 promise 和 Node 的错误优先回调约定。

##### 列表 8.28\. 使用 localStorage 与 localForage 获取数据的比较

![](img/08lis28_alt.jpg)

在底层，localForage 利用当前浏览器环境中可用的最佳存储机制。如果 IndexedDB 可用，localForage 将使用它。否则，它将尝试回退到 WebSQL，甚至在需要时使用 Web 存储。您可以配置尝试存储的顺序，甚至可以黑名单某些选项：

![](img/p0221_01_alt.jpg)

与 localStorage 不同，localForage 不仅限于存储字符串。它支持大多数 JavaScript 原语，如数组和对象，以及二进制数据类型：`Typed-Arrays`、`ArrayBuffers`和`Blobs`。请注意，IndexedDB 是唯一可以原生存储二进制数据的后端：WebSQL 和 localStorage 后端将产生序列化开销：

```
Promise.all([
  localforage.setItem('number', 3),
  localforage.setItem('object', { key: 'value' }),
  localforage.setItem('typedarray', new Uint32Array([1,2,3]))
]);
```

通过镜像 Web 存储 API，localForage 的使用直观，同时也克服了在浏览器中存储数据时许多缺点和兼容性问题。

### 8.15\. 托管存储

托管存储是你可以用来避免管理自己的服务器端存储的另一种策略。由亚马逊网络服务（AWS）等提供的主机基础设施服务通常被认为只是扩展和性能优化，但早期智能地使用托管服务可以节省大量时间，避免实施不必要的糟糕基础设施。

本章中列出的许多数据库（如果不是所有数据库）都有托管服务。托管服务允许你快速尝试工具，甚至可以部署公开可访问的生产应用程序，而无需自己设置数据库托管。但自己托管正变得越来越容易。许多云服务提供预构建的服务器镜像，其中包含了运行数据库所需的所有正确软件和配置。

#### 8.15.1\. 简单存储服务

亚马逊简单存储服务（S3）是作为流行的 AWS 套件的一部分提供的远程文件托管服务。S3 是一种经济高效的存储和托管网络可访问文件的方法。它是一个云端的文件系统。使用 RESTful HTTP 调用，可以将文件上传到存储桶，同时附带最多 2 KB 的元数据。然后可以通过 HTTP `GET`或 BitTorrent 协议访问存储桶内容。

存储桶及其内容可以通过各种权限进行配置，包括基于时间的访问权限。你还可以为存储桶内容本身指定一个生存时间（TTL），在此之后，它们将变得不可访问，并从你的存储桶中删除。将你的 S3 数据提升到内容分发网络（CDN）非常容易。AWS 提供了 CloudFront CDN，它可以轻松连接到你的文件，并且可以从世界各地以低延迟访问。

并非所有数据都需要或应该存储在数据库中。你的数据中是否有可以作为文件处理的组件？在你为用户完成一次昂贵的计算后，也许你可以将这些结果推送到 S3，然后永远退出这个环节。

S3 的一个常见且明显的用途是托管用户上传的资产，如图像。上传的资产存储在应用程序机器上的临时目录中，通过使用 ImageMagick 等工具来减小文件大小，然后上传到 S3 以供浏览器托管。这个过程可以通过直接将上传流式传输到 S3 进一步简化，在那里它们可以触发进一步的加工。客户端应用程序也可以直接上传到 S3。一些更面向开发者的服务甚至选择提供绝对零存储，要求用户提供访问令牌，以便应用程序可以使用他们的 S3 存储桶。

##### S3 不仅限于存储图像

S3 可以用来存储任何类型的文件，大小最高可达 5 太字节，格式不限。S3 最适合存储不经常更改且需要作为单个原子访问的大量数据块。

将数据存储在 S3 中可以绕过设置和维护用于文件托管和存储的服务器的复杂性和复杂性。它非常适合写入不频繁、需要作为单个原子访问大量数据，并且有多个读取和潜在多个读取位置的情况。

### 8.16. 哪个数据库？

在本章中，我们只介绍了 Node 应用程序中常用的一些数据库。成功的应用程序可以使用这些数据库中的任何一个来构建。在单个应用程序中，并不总是有一个理想的数据存储解决方案；没有一劳永逸的解决方案。每个数据库都提供其独特的权衡，开发者需要评估哪些权衡对当前项目状态是有意义的。技术的混合通常是最合适的。

而不是问“我应该使用哪个数据库？”，你可能会问，“完全不使用数据库我能走多远？”你能用最少的长期决策构建你项目的多少部分？通常最好推迟决策；当你有更多信息时，你总是会做出更好的决策。

### 8.17. 摘要

+   无论是关系型数据库还是 NoSQL 数据库，都可以与 Node 一起使用。

+   简单的 pg Node 模块非常适合与 SQL 语言一起工作。

+   Knex 模块允许您使用 Node 与多个数据库交互。

+   ACID 是数据库事务的一组属性，确保了安全性。

+   MongoDB 是一种使用 JavaScript 的 NoSQL 数据库。

+   Redis 是一种可以作为数据库和缓存使用的数据结构存储。

+   LevelDB 是 Google 开发的一种快速键/值存储，它将字符串映射到值。

+   LevelDB 是一个模块化数据库。

+   基于网络的存储，包括 localForage 和 localStorage，可以用于在浏览器中保存数据。

+   存储服务，如 Amazon S3，可以用于将数据持久化到云服务提供商。

## 第九章. 测试 Node 应用程序

*本章涵盖*

+   使用 Node 的 assert 模块测试逻辑

+   使用其他断言库

+   使用 Node 单元测试框架

+   使用 Node 模拟和控制网络浏览器

+   当测试失败时获取更多详细信息

随着您应用程序功能的增加，引入错误的风险也在增加。如果一个应用程序未经测试，那么它就不完整。由于手动测试既繁琐又容易出错，因此自动测试在开发者中越来越受欢迎。自动测试涉及编写逻辑来测试你的代码，而不是手动运行应用程序的功能。

如果自动测试的概念对你来说是新的，那么你可以将其想象成一个机器人做所有无聊的工作，而你则专注于有趣的工作。每次你对代码进行更改时，机器人都会确保没有错误悄悄地进入。尽管你可能还没有完成或开始你的第一个 Node 应用程序，但了解如何实现自动测试是很好的，因为你在开发过程中将能够编写测试。

在本章中，你将了解两种类型的自动化测试：单元测试和验收测试。*单元测试* 用于验证逻辑，通常在函数或方法级别，并且适用于所有类型的应用程序。单元测试方法可以分为两种主要形式：测试驱动开发（TDD）和行为驱动开发（BDD）。从实际的角度来看，TDD 和 BDD 在很大程度上是同一件事，但它们在风格上有所不同。这很重要，取决于谁需要阅读你的测试。TDD 和 BDD 之间的其他差异存在，但它们超出了本书的范围。*验收测试* 是一种常用的测试层，通常用于 Web 应用程序。验收测试涉及脚本控制浏览器，并尝试使用它触发 Web 应用程序的功能。

本章涵盖了单元测试和验收测试的既定解决方案。对于单元测试，我们涵盖了 Node 的 assert 模块；Mocha、Vows 和 Should.js 框架；以及 Chai。对于验收测试，我们探讨了使用 Selenium 与 Node 的结合。图 9.1 将工具放置在其各自的测试方法和风味旁边。

##### 图 9.1\. 测试框架概述

![](img/09fig01_alt.jpg)

让我们从单元测试开始。

### 9.1\. 单元测试

*单元测试* 是一种自动化测试类型，其中你编写逻辑来测试应用程序的离散部分。编写测试有助于你更批判性地思考应用程序的设计选择，并帮助你早期避免陷阱。测试还让你对自己的最近更改没有引入错误充满信心。尽管单元测试在编写时需要一些前期工作，但它们可以通过减少每次更改应用程序时手动重新测试的需要来节省你的时间。

单元测试可能很棘手，测试异步逻辑可能也会带来自己的挑战。异步单元测试可以并行运行，因此你必须小心确保测试不会相互干扰。例如，如果你的测试在磁盘上创建临时文件，你必须小心，在测试后删除文件时，不要删除尚未完成的另一个测试的工作文件。因此，许多单元测试框架包括流程控制来序列化测试的运行。

在本节中，我们将向您展示如何使用以下内容：

+   ***Node 的内置 assert 模块—*** TDD 风格自动化测试的良好构建块

+   ***Mocha—*** 一个相对较新的测试框架，可用于 TDD 或 BDD 风格的测试

+   ***Vows—*** 一个广泛使用的 BDD 风格测试框架

+   ***Should.js—*** 一个模块，它基于 Node 的 assert 模块来提供 BDD 风格的断言

下一节将演示如何使用与 Node 一起提供的 assert 模块测试业务逻辑。

#### 9.1.1\. assert 模块

大多数 Node 单元测试的基础是内置的 assert 模块，它测试一个条件，如果条件不满足，则抛出错误。Node 的 assert 模块被许多第三方测试框架使用。即使没有测试框架，您也可以使用它进行有用的测试。如果您正在尝试快速的想法，您可以使用 assert 模块本身来编写快速测试。

##### 一个简单的例子

假设您有一个简单的待办事项应用，它将项目存储在内存中，并且您想断言它正在做您认为它正在做的事情。

以下列表定义了一个包含核心应用功能的模块。模块逻辑支持创建、检索和删除待办事项。它还包括一个简单的`doAsync`方法，因此您还可以查看测试异步方法。将此文件保存为 todo.js。

##### 列表 9.1\. 待办事项列表的模型

![](img/09lis01_alt.jpg)

现在，您可以使用 Node 的 assert 模块来测试代码。在一个名为 test.js 的文件中，输入以下代码以加载必要的模块，设置一个新的待办事项列表，并设置一个跟踪完成测试数量的变量。

##### 列表 9.2\. 设置必要的模块

```
const assert = require('assert');
const Todo = require('./todo');
const todo = new Todo();
let testsCompleted = 0;
```

##### 使用等于测试变量的内容

接下来，您可以添加对待办事项应用删除功能的测试。将以下列表中的函数添加到 test.js 的末尾。

##### 列表 9.3\. 确保删除后没有待办事项剩余

![](img/09lis03_alt.jpg)

此测试添加一个`todo`项然后删除它。因为在这个测试结束时应该没有待办事项，所以如果应用逻辑正常工作，`todo.length`的值应该是`0`。如果出现问题，将抛出异常。如果`todo.length`返回的值没有设置为`0`，断言将导致显示错误消息“不应该存在任何项”的堆栈跟踪输出到控制台。在断言之后，`testsCompleted`增加以记录一个测试已完成。

##### 使用 notEqual 查找逻辑中的问题

接下来，将以下列表中的代码添加到 test.js 中。此代码是对待办事项应用添加功能的测试。

##### 列表 9.4\. 确保添加待办事项的测试

![](img/09lis04_alt.jpg)

assert 模块还允许`notEqual`断言。这种断言在应用程序代码生成特定值时指示逻辑问题很有用。列表 9.4 显示了`notEqual`断言的使用。删除所有待办事项，添加一个项，然后应用逻辑获取所有项。如果项的数量是`0`，断言将失败并抛出异常。

##### 使用附加功能：strictEqual，notStrictEqual，deep- pEqual，notDeepEqual

除了`equal`和`notEqual`功能外，assert 模块还提供了称为`strictEqual`和`notStrictEqual`的严格断言版本。这些使用严格相等运算符(`===`)而不是更宽容的版本(`==`)。

为了比较对象，assert 模块提供了`deepEqual`和`notDeepEqual`。这些断言名称中的`deep`表示它们会递归地比较两个对象，比较两个对象的属性，如果属性本身也是对象，则也会进行比较。

##### 使用`ok`测试异步值是否为真

现在是时候添加对待办应用`doAsync`方法的测试了，如列表 9.5 所示。因为这是一个异步测试，你需要提供一个回调函数（`cb`）来通知测试运行器测试何时完成；你不能像同步测试那样依赖函数返回来告诉你，因为同步测试可以这样做。为了检查`doAsync`的结果是否为值`true`，使用`ok`断言。`ok`断言提供了一个简单的方式来测试一个值是否为`true`。

##### 列表 9.5\. 测试`doAsync`回调是否传递了`true`

![图片](img/09lis05_alt.jpg)

##### 测试抛出的错误是否正确

你还可以使用 assert 模块来检查抛出的错误消息是否正确，如下面的列表所示。`throws`调用中的第二个参数是一个正则表达式，用于在错误消息中查找文本`requires`。

##### 列表 9.6\. 测试`add`在缺少参数时是否抛出异常

![图片](img/09lis06_alt.jpg)

##### 添加逻辑来运行你的测试

现在你已经定义了测试，你可以在文件中添加逻辑来运行每个测试。以下列表中的逻辑运行每个测试，然后打印已运行和完成的测试数量。

##### 列表 9.7\. 运行测试并报告测试完成

![图片](img/09lis07_alt.jpg)

你可以使用以下命令运行测试：

```
$ node chapter09-testing/listing_09_1-7/test.js
```

如果测试没有失败，脚本会告诉你已完成的测试数量。它还可以智能地跟踪测试何时开始执行以及何时完成，以防止单个测试中的缺陷。例如，一个测试可能执行而没有达到断言。

为了使用 Node 的内置功能，每个测试用例都必须包含大量的样板代码来设置测试（例如删除所有项目）以及跟踪进度（`completed`计数器）。所有这些样板代码都将注意力从编写测试用例的主要关注点转移开，最好留给一个专门的框架来做这些繁重的工作，而你则专注于测试业务逻辑。让我们看看如何通过使用第三方单元测试框架 Mocha 来简化事情。

#### 9.1.2\. Mocha

**Mocha**，一个流行的测试框架，易于掌握。尽管它默认使用 BDD 风格，但你也可以用它来使用 TDD 风格。Mocha 具有许多功能，包括全局变量泄漏检测和客户端测试。

|  |
| --- |

**全局变量泄漏检测**

你很少需要可读的全局变量，并且将它们的使用量最小化被认为是编程的最佳实践。但在 ES5 中，很容易忘记在声明变量时包含 `var` 关键字而意外创建全局变量。Mocha 通过在测试期间创建全局变量时抛出错误来帮助检测意外的全局变量泄漏。

如果你想要禁用全局泄漏检测，请使用 `--ignored-leaks` 命令行选项运行 `mocha`。或者，如果你想允许使用选定数量的全局变量，你可以通过使用 `--globals` 命令行选项后跟一个逗号分隔的允许的全局变量列表来指定它们。

| |
| --- |

默认情况下，Mocha 测试是通过使用名为 `describe`、`it`、`before`、`after`、`beforeEach` 和 `afterEach` 的 BDD 风格函数来定义和设置其逻辑的。或者，你也可以使用 Mocha 的 TDD 接口，它用 `suite` 替换 `describe`，用 `test` 替换 `it`，用 `setup` 替换 `before`，用 `teardown` 替换 `after`。在我们的例子中，你将坚持使用默认的 BDD 接口。

##### 使用 Mocha 测试 Node 应用程序

让我们直接深入探讨如何创建一个名为 *memdb* 的小型项目——一个内存数据库，并使用 Mocha 来测试它。首先，你需要为项目创建目录和文件：

```
$ mkdir -p memdb/test
$ cd memdb
$ touch index.js
$ touch test/memdb.js
$ npm init -y
$ npm install --save-dev mocha
```

打开 package.json 并添加一个 `scripts` 属性，该属性定义了如何运行测试：

```
"scripts": {
  "test": "mocha"
},
```

*test* 目录是测试将驻留的地方。默认情况下，Mocha 使用 BDD 接口。以下列表显示了它的样子（本书示例代码中的 chapter09-testing/memdb）。

##### 列表 9.8\. Mocha 测试的基本结构

```
const memdb = require('..');
describe('memdb', () => {
  describe('.saveSync(doc)', () => {
    it('should save the document', () => {
    });
  });
});
```

Mocha 还支持 TDD 和 qunit，并导出样式接口，这些接口在项目网站上详细说明（[`mochajs.org/`](https://mochajs.org/)）。为了说明接口的概念，这里有一个 `exports` 接口：

```
module.exports = {
  'memdb': {
    '.saveSync(doc)': {
      'should save the document': () => {
      }
    }
  }
}
```

所有这些接口都提供相同的功能，但你现在将坚持使用 BDD 接口，并在 test/memdb.js 中编写第一个测试，如下所示。这个测试使用 Node 的 assert 模块来执行断言。

##### 列表 9.9\. 描述 memdb 的 `.save` 功能

![图片 9.09](img/09lis09_alt.jpg)

要运行测试，你只需要执行 `npm test`。Mocha 默认在 ./test 目录中查找要执行的 JavaScript 文件。因为你还没有实现 `.saveSync()` 方法，所以你会看到定义的单个测试失败，如图 9.2 所示。

##### 图 9.2\. Mocha 中的失败测试

![图片 9.2](img/09fig02_alt.jpg)

让它通过！将以下列表中的代码添加到 index.js 中。

##### 列表 9.10\. 添加了保存功能

![图片 9.10](img/09lis10_alt.jpg)

再次使用 npm 运行测试，结果应该类似于 图 9.3。

##### 图 9.3\. Mocha 中的成功测试

![图片 9.3](img/09fig03_alt.jpg)

##### 使用 Mocha 钩子定义设置和清理逻辑

列表 9.10 中的测试用例假设 `memdb.first()` 正确工作，因此你也会想为它添加一些测试用例。修订后的测试文件，列表 9.11，包括了一个新概念——Mocha 的 *钩子* 概念。BDD 接口暴露了 `beforeEach()`、`afterEach()`、`before()` 和 `after()`，它们接受回调来定义设置和清理逻辑。

##### 列表 9.11\. 添加 `beforeEach` 钩子

![](img/ch09ex11-0.jpg)

![](img/ch09ex11-1.jpg)

理想情况下，测试用例没有任何共享状态。为了在 memdb 中实现这一点，你需要通过在 index.js 中实现 `.clear()` 方法来删除所有文档：

```
exports.clear = () => {
  db.length = 0;
};
```

再次运行 Mocha 应该会显示有三个测试通过了。

##### 测试异步逻辑

我们在 Mocha 中还没有看到的是测试异步逻辑。为了了解这是如何完成的，你将对 index.js 中定义的其中一个函数进行小的修改。通过将 `save` 函数更改为以下内容，可以可选地提供一个回调，该回调将在一小段时间后执行（用于模拟某种异步操作）：

```
exports.save = (doc, cb) => {
  db.push(doc);
  if (cb) {
    setTimeout(() => {
      cb();
    }, 1000);
  }
};
```

Mocha 测试用例可以通过向定义测试逻辑的函数添加一个参数来定义为异步。该参数通常命名为 `done`。以下列表展示了如何编写异步 `save` 方法的测试。

##### 列表 9.12\. 测试异步逻辑

![](img/09lis12_alt.jpg)

这条规则适用于所有钩子。例如，用于清除数据库的 `beforeEach()` 钩子可以添加一个回调，Mocha 将等待它被调用，然后继续。如果 `done()` 以错误作为第一个参数被调用，Mocha 将报告错误并将钩子或测试用例标记为失败：

```
beforeEach((done) => {
  memdb.clear(done);
});
```

更多关于 Mocha 的信息，请查看其完整的在线文档：[`mochajs.org`](http://mochajs.org)。Mocha 也可以用于客户端 JavaScript。

| |
| --- |

**Mocha 的非并行测试使用**

Mocha 按顺序执行测试，而不是并行执行，这使得测试套件执行得更慢，但编写测试更容易。但 Mocha 不会让任何测试运行过长时间。默认情况下，Mocha 允许任何给定的测试运行最多 2,000 毫秒，然后将其标记为失败。如果你有运行时间较长的测试，你可以使用 `--timeout` 命令行选项运行 Mocha，然后指定一个更大的数字。

对于大多数测试，串行运行测试是可行的。如果你发现这有问题，其他框架，如 Vows，可以并行执行，这将在下一节中介绍。

| |
| --- |

#### 9.1.3\. Vows

使用 Vows 单元测试框架可以编写的测试用例比许多其他框架的结构更清晰，这种结构旨在使测试易于阅读和维护。

Vows 使用自己的 BDD 风格的术语来定义测试结构。在 Vows 的领域里，一个测试套件包含一个或多个批次。一个*批次*可以被视为一组相关的*上下文*，或者您想要测试的概念性关注区域。批次和上下文并行运行。一个上下文可能包含一个*主题*、一个或多个*誓言*和/或一个或多个相关上下文（内部上下文也并行运行）。一个*主题*是与上下文相关的测试逻辑。一个*誓言*是对主题结果的测试。图 9.4 显示了 Vows 如何结构测试。

##### 图 9.4\. Vows 可以使用批次、上下文、主题和誓言来结构测试套件。

![图片](img/09fig04.jpg)

Vows，就像 Mocha 一样，旨在自动化应用程序测试。主要区别在于风味和并行性，Vows 测试需要特定的结构和术语。在本节中，我们将通过一个示例应用程序测试来解释如何使用 Vows 测试同时运行多个测试。

通过使用 npm 安装来将 Vows 添加到待办事项项目中：

```
mkdir -p vows-todo/test
cd vows-todo
touch todo.js
touch test/todo-test.js
npm init -y
npm install --save-dev –g vows
```

您需要将 Vows 添加到 package.json 中的测试属性，以便可以通过输入`npm test`来运行测试：

```
"scripts": {
  "test": "vows test/*.js"
},
```

##### 使用 Vows 测试应用程序逻辑

您可以通过运行包含测试逻辑的脚本或使用`vows`命令行测试运行器来在 Vows 中触发测试。以下是一个独立测试脚本的示例（可以像其他任何 Node 脚本一样运行），它使用了待办事项应用程序核心逻辑的一个测试。

列表 9.13 创建了一个批次。在批次内，您定义一个上下文。在上下文中，您定义一个主题和一个誓言。注意代码如何使用回调来处理主题中的异步逻辑。如果一个主题不是异步的，则可以返回一个值，而不是通过回调发送。将文件保存为 test/todo-test.js。

##### 列表 9.13\. 使用 Vows 测试待办事项应用程序

![图片](img/09lis13_alt.jpg)

您应该能够通过输入`npm test`来运行此测试。如果您使用`npm i -g vows`全局安装 Vows，您还可以通过输入以下命令来运行名为*test*的文件夹中的所有测试：

```
$ vows test/*
```

更多关于 Vows 的信息，请查看项目的在线文档([`vowsjs.org/`](http://vowsjs.org/))，如图 9.5 所示。

##### 图 9.5\. Vows 结合了具有宏和流程控制的完整功能 BDD 测试。

![图片](img/09fig05_alt.jpg)

Vows 提供了一套全面的测试解决方案，但您可以通过使用不同的断言库来混合和匹配测试库功能。也许你喜欢 Mocha，但不喜欢 Node 的断言库。下一节介绍了 Chai，这是一个可以替代 Node assert 模块的断言库。

#### 9.1.4\. 茶叶

*Chai* ([`chaijs.com/`](http://chaijs.com/)) 是一个流行的断言库，它提供了三个接口：*should*、*expect* 和 *assert*。下面的列表中展示了 `assert` 接口，它看起来像 Node 的内置断言模块，但它提供了比较对象、数组和它们的属性的有用工具。例如，`typeOf` 可以用来比较类型，而 `property` 检查对象是否具有所需的属性。

##### 列表 9.14\. Chai 的 `assert` 接口

![](img/09lis14_alt.jpg)

你可能想要尝试 Chai 的主要原因是 `should` 和 `expect` 接口。它们提供了流畅的 API，更像是 BDD 风格的库。以下是 `expect` 接口：

```
const chai = require('chai');
const expect = chai.expect;
const foo = 'bar';
expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
```

这个 API 读起来更像一个英文句子——声明式风格更冗长，但更容易朗读。`should` 接口则相反：对象被装饰以具有额外的属性，因此你不需要像 `expect` 那样在调用中包裹断言：

```
const chai = require('chai');
chai.should();
const foo = 'bar';
foo.should.be.a('string');
foo.should.equal('bar');
```

决定使用哪个接口取决于项目。如果你是先编写测试，然后使用它们来记录项目，那么详尽的 `expect` 和 `should` 接口将工作得很好。JavaScript 纯粹主义者更喜欢 `expect`，因为它不会改变原型，但那些有 Ruby 经验的人可能熟悉 `should` 这样的 API。

使用 Chai 的主要优势是插件的范围。这包括一些方便的工具，如 chai-as-promised ([`chaijs.com/plugins/chai-as-promised/`](http://chaijs.com/plugins/chai-as-promised/))，它有助于测试使用 promises 的代码，以及 chai-stats ([`chaijs.com/plugins/chai-stats/`](http://chaijs.com/plugins/chai-stats/))，这是一个根据统计方法比较数字的库。请注意，Chai 是一个断言库，因此你应该与像 Mocha 这样的测试运行器一起使用。

与 Chai 类似的另一个 BDD 断言库是 Should.js。下一节将介绍 Should.js 并演示如何使用它编写测试。

#### 9.1.5\. Should.js

Should.js 是一个断言库，它允许你以 BDD（行为驱动开发）风格表达断言，从而使你的测试更容易阅读。它设计用于与其他测试框架一起使用，这样你就可以继续使用你自己的首选框架。在本节中，你将学习如何使用 Should.js 编写断言，并以一个自定义模块的测试为例。

Should.js 与其他框架易于使用，因为它通过单个属性 `should` 增强了 `Object. --proto-type`。这允许你编写如 `user.role.should.equal('admin')` 或 `users.should.include ('rick')` 这样的表达性断言。

假设你正在编写一个 Node 命令行小费计算器，你希望用它来确定当你和朋友分账时谁应该支付多少钱。你希望以易于你的非程序员朋友理解的方式编写你的计算逻辑的测试，因为这样他们就不会认为你在欺骗他们。

要设置小费计算器应用程序，输入以下命令，这些命令设置应用程序的文件夹，然后安装 Should.js 进行测试：

```
mkdir -p tips/test
cd tips
touch index.js
touch test/tips.js
```

现在你可以通过运行以下命令来安装 Should.js：

```
npm init -y
npm install --save-dev should
```

接下来，编辑 index.js 文件，该文件将包含定义应用程序核心功能的逻辑。具体来说，小费计算器的逻辑包括四个辅助函数：

+   `addPercentageToEach`—将给定百分比增加到数组中的每个数字

+   `sum`—计算数组中每个元素的总和

+   `percentFormat`—格式化用于显示的百分比

+   `dollarFormat`—格式化用于显示的美元值

通过在 index.js 中填充以下列表的内容来添加此逻辑。

##### 列表 9.15\. 分账时计算小费的逻辑

![](img/09lis15_alt.jpg)

现在编辑 test/tips.js 中的测试脚本，如下所示。该脚本加载小费逻辑模块；定义了税费、小费百分比和要测试的账单项目；测试了将百分比添加到每个数组元素；并测试了账单总额。

##### 列表 9.16\. 分账时计算小费的逻辑

![](img/09lis16_alt.jpg)

使用以下命令运行脚本。如果一切顺利，脚本应该不会生成任何输出，因为没有抛出断言，你的朋友们会放心你的诚实：

```
$ node test/tips.js
```

为了使其更容易运行，将其添加到 package.json 中 scripts 下的测试属性：

```
"scripts": {
  "test": "node test/tips.js"
}
```

Should.js 支持许多类型的断言—从使用正则表达式的断言到检查对象属性的断言—允许全面测试应用程序生成的数据和对象。项目的 GitHub 页面 ([`github.com/shouldjs/should.js`](https://github.com/shouldjs/should.js)) 提供了 Should.js 功能的全面文档。

间谍、存根和模拟通常与断言库一起使用，以控制被测试代码的执行方式。下一节将演示如何使用 Sinon.JS 进行这些操作。

#### 9.1.6\. 使用 Sinon.JS 的间谍和存根

测试工具箱中的最后一个工具是模拟和存根库。我们编写单元测试的原因是隔离系统的一部分进行测试，但有时这很困难。例如，想象你正在测试调整图像大小的代码。你不想写入真实的图像文件，那么你该如何编写测试？代码不应该有特殊的测试分支来避免接触文件系统，因为那样你就不会真正测试代码。在这种情况下，你需要*存根*文件系统功能。编写存根的实践也有助于你进行真正的 TDD，因为你可以存根尚未准备好的依赖项。

在本节中，你将学习如何使用 Sinon.JS ([`sinonjs.org/`](http://sinonjs.org/)) 来编写测试间谍、存根和模拟。在开始之前，创建一个新的项目并安装 Sinon：

```
mkdir sinon-js-examples
cd sinon-js-examples
npm init -y
mkdir test
npm i --save-dev sinon
```

接下来创建一个用于测试的示例文件。我们使用的例子是一个简单的 JSON 键/值数据库。我们的目标是能够存根文件系统 API，使其不在文件系统中创建真实文件。这将允许我们只测试数据库代码，而不是文件处理代码，如下一个列表所示。

##### 列表 9.17\. 数据库类

```
const fs = require('fs');

class Database {
  constructor(filename) {
    this.filename = filename;
    this.data = {};
  }

  save(cb) {
    fs.writeFile(this.filename, JSON.stringify(this.data), cb);
  }

  insert(key, value) {
    this.data[key] = value;
  }
}

module.exports = Database;
```

将列表保存为 db.js。现在你将尝试使用 Sinon 的间谍进行测试。

##### 间谍

有时你只是想查看一个方法是否被调用。间谍非常适合这个用途。API 允许你用一个你可以用来进行断言的东西替换一个方法。为了模拟 db.js 中的 `fs.writeFile` 调用，使用 Sinon 的方法替换方法 `spy`：

```
sinon.spy(fs, 'writeFile');
```

测试完成后，你可以使用 `restore` 获取原始方法：

```
fs.writeFile.restore();
```

在像 Mocha 这样的测试库中，你会在 `beforeEach` 和 `afterEach` 块中放置这些调用。以下列表展示了使用间谍的完整示例。将此文件保存为 spies.js。

##### 列表 9.18\. 使用间谍

![](img/09lis18_alt.jpg)

在设置间谍 ![](img/circ1.jpg) 之后，运行待测试的代码。然后你确保预期的方法被 `sinon.assert` ![](img/circ2.jpg) 调用。然后恢复原始方法 ![](img/circ3.jpg)。在这个测试中，恢复它并不是严格必要的，但始终恢复你更改的方法是最佳实践。

##### 存根

有时你需要控制代码流程。例如，你可能想强制执行错误分支以测试你的代码中的错误处理。前面的例子可以重写为使用存根而不是间谍来执行 `writeFile` 的回调。请注意，你仍然想避免调用原始方法，而是强制测试代码运行提供的回调。下一个列表展示了如何使用存根替换函数。将其保存为 stub.js。

##### 列表 9.19\. 使用存根

![](img/09lis19_alt.jpg)

使用存根和间谍的组合是测试大量使用用户提供的函数、回调和承诺的 Node 代码的理想选择。既然你已经了解了为单元测试设计的工具，让我们继续探讨一种完全不同的测试风格：功能测试。

### 9.2\. 功能测试

在大多数 Web 开发项目中，*功能测试*通过驱动浏览器，然后检查各种 DOM 变化是否符合用户特定的要求列表。想象你正在构建一个内容管理系统。对于图像库上传功能的功能测试将上传一个图像，检查它是否被添加，然后检查它是否被添加到相应的图像列表中。

在 Node 中实现功能测试的工具选择令人眼花缭乱。然而，从高层次来看，它们可以分为两大类：无头测试和基于浏览器的测试。*无头测试*通常使用类似 PhantomJS 的工具来提供一个终端友好的浏览器环境，但更轻量级的解决方案则使用 Cheerio 和 JSDOM 等库。*基于浏览器的测试*使用 Selenium 等浏览器自动化工具([www.seleniumhq.org](http://www.seleniumhq.org))，这样你可以编写脚本驱动真实浏览器。两种方法都可以使用相同的底层 Node 测试工具，因此你可以使用 Mocha、Jasmine，甚至 Cucumber 来驱动 Selenium 测试你的应用程序。图 9.6 展示了示例测试环境。

##### 图 9.6.使用浏览器自动化进行测试

![图 9.6](img/09fig06.jpg)

在本节中，你将了解 Node 的功能测试解决方案，以便你可以根据你的要求设置测试环境。

#### 9.2.1. Selenium

*Selenium*是一个流行的基于 Java 的浏览器自动化库。借助特定语言的驱动程序，你可以连接到 Selenium 服务器并针对真实浏览器运行测试。在本节中，你将学习如何使用 WebdriverIO([`webdriver.io/`](http://webdriver.io/))，这是一个 Node Selenium 驱动程序。

运行 Selenium 比纯 Node 测试库更复杂，因为你需要安装 Java 并下载 Selenium JAR 文件。为你的操作系统下载 Java，然后转到 Selenium 下载网站([`docs.seleniumhq.org/download/`](http://docs.seleniumhq.org/download/))下载 JAR 文件。然后你可以这样运行 Selenium 服务器：

```
java -jar selenium-server-standalone-2.53.0.jar
```

注意，你的 Selenium 版本可能不同。你可能还需要提供一个浏览器二进制的路径。例如，在 Windows 10 上，将 Firefox 设置为`browserName`，你可以这样指定 Firefox 的完整路径：

```
java -jar -Dwebdriver.firefox.driver="C:\path\to\firefox.exe" selenium-server-standalone-3.0.1.jar
```

完整路径将取决于 Firefox 在你的机器上的安装方式。有关 Firefox 驱动程序的更多信息，请参阅 SeleniumHQ 文档([`github.com/SeleniumHQ/selenium/wiki/FirefoxDriver`](https://github.com/SeleniumHQ/selenium/wiki/FirefoxDriver))。Chrome 和 Microsoft Edge 的驱动程序配置方式类似。

现在创建一个新的 Node 项目并安装 WebdriverIO：

```
mkdir -p selenium/test/specs
cd selenium
npm init -y
npm install --save-dev webdriverio
npm install --save express
```

WebdriverIO 附带一个友好的配置文件生成器。要运行它，请运行`wdio config`：

```
./node_modules/.bin/wdio config
```

按照提示并接受默认设置。图 9.7 显示了我的会话。

##### 图 9.7.使用`wdio`配置 Selenium 测试

![图 9.7](img/09fig07.jpg)

在 package.json 文件中更新`wdio`命令，以便可以使用`npm test`运行测试：

```
"scripts": {
  "test": "wdio wdio.conf.js"
},
```

现在向测试中添加一些内容。一个基本的 Express 服务器就足够了。以下列表中的示例将在后续列表中用于测试。将此列表保存为 index.js（在本书的示例代码中为 c09-testing/selenium/index.js）。

##### 列表 9.20.示例 Express 项目

```
const express = require('express');
const app = express();
const port = process.env.PORT || 4000;

app.get('/', (req, res) => {
  res.send(`
<html>
  <head>
    <title>My to-do list</title>
  </head>
  <body>
    <h1>Welcome to my awesome to-do list</h1>
  </body>
</html>
  `);
});

app.listen(port, () => {
  console.log('Running on port', port);
});
```

WebdriverIO 的好处是它提供了一个简单、流畅的 API 来编写 Selenium 测试。语法清晰，易于学习——你甚至可以使用 CSS 选择器编写测试。下一节（在本书示例代码的 test/specs/todo-test.js 中找到）展示了设置 WebdriverIO 客户端并检查页面标题的简单测试。

##### 列表 9.21. 一个 WebdriverIO 测试

![图片](img/09lis21_alt.jpg)

在 WebdriverIO 连接 ![](img/circ1.jpg) 之后，你可以使用客户端实例从你的应用程序中获取页面 ![](img/circ2.jpg)。然后你可以查询浏览器中文档的当前状态——此示例使用 `getTitle` 从文档的 `head` 中获取 `title` 元素。如果你要查询文档中的 CSS 元素，可以使用 `.elements` 代替 ([`webdriver.io/api/protocol/elements.html`](http://webdriver.io/api/protocol/elements.html))。有各种方法可以操作文档、表单，甚至是 cookie。

这个测试，看起来像本章中的其他 Mocha 测试，能够在一个 Node Web 应用程序上运行真实的浏览器。要运行它，请在端口 4000 上启动服务器：

```
PORT=4000 node index.js
```

然后输入 `npm test`。你应该看到 Firefox 打开，并在命令行中运行测试。如果你想使用 Chrome，打开 wdio.conf.js 并更改 `browserName` 属性。

| |
| --- |

**使用 Selenium 进行更高级的测试**

如果你使用 WebdriverIO 和 Selenium 测试一个更复杂的 Web 应用程序，该应用程序使用类似 React 或 Angular 这样的技术，你将想要查看 `utility` 方法。其中一些方法会在某些元素可用之前暂停测试，这对于可能异步渲染文档、根据远程数据可用性更新多次的 React 应用程序来说非常好。

查看一下 `waitFor`* 方法，例如 `waitForVisible` ([`webdriver.io/api/utility/waitForVisible.html`](http://webdriver.io/api/utility/waitForVisible.html)) 以了解更多信息。

| |
| --- |

### 9.3. 处理失败的测试

当你在进行一个成熟的项目工作时，总会有测试开始失败的时候。Node 提供了几个工具来获取更多关于失败测试的详细信息，在本节中，你将了解如何丰富调试失败测试时生成的输出。

当测试失败时，首先要做的是生成更详细的日志输出。下一节将演示如何使用 `NODE_DEBUG` 来实现这一点。

#### 9.3.1. 获取更详细的日志

当测试失败时，了解程序正在做什么非常有用。Node 有两种方式来做这件事：一种用于 Node 的内部模块，另一种用于 npm 模块。要调试 Node 的核心模块，请使用 `NODE_DEBUG`。

##### 使用 NODE_DEBUG

要了解 `NODE_DEBUG` 的工作原理，想象你有一个深层嵌套的文件系统调用，而你忘记使用回调函数。例如，以下示例将抛出异常：

```
const fs = require('fs');

function deeplyNested() {
fs.readFile('/');
}

deeplyNested();
```

堆栈跟踪只显示了关于异常的有限细节，特别是不包括异常起源的调用点的完整信息：

```
fs.js:60
      throw err;  // Forgot a callback but don't know where? Use NODE_DEBUG=fs
      ^

Error: EISDIR: illegal operation on a directory, read
    at Error (native)
```

没有有帮助的注释，许多程序员看到这样的跟踪并责怪 Node 的无帮助错误。但是，正如注释所指出的，可以使用 `NODE_DEBUG=fs` 来获取 fs 模块更多的信息。用这种方式运行脚本：

```
NODE_DEBUG=fs node node-debug-example.js
```

现在，您将看到一个更详细的跟踪，有助于调试问题：

```
fs.js:53
        throw backtrace;
        ^

Error: EISDIR: illegal operation on a directory, read
    at rethrow (fs.js:48:21)
    at maybeCallback (fs.js:66:42)
    at Object.fs.readFile (fs.js:227:18)
    at deeplyNested (node-debug-example.js:4:6)
    at Object.<anonymous> (node-debug-example.js:7:1)
    at Module._compile (module.js:435:26)
    at Object.Module._extensions..js (module.js:442:10)
    at Module.load (module.js:356:32)
    at Function.Module._load (module.js:311:12)
    at Function.Module.runMain (module.js:467:10)
```

从这个跟踪中可以清楚地看出，问题出在我们的文件中，在第四行的一个函数里，这个函数最初是从第七行调用的。这使得调试使用核心模块的任何代码都变得更容易，这不仅包括文件系统，还包括网络库，如 Node 的 HTTP 客户端和服务器模块。

##### 使用 DEBUG

`NODE_DEBUG` 的公共替代品是 `DEBUG`。npm 上的许多包都在寻找 `DEBUG` 环境变量。它模仿了 `NODE_DEBUG` 使用的参数样式，因此您可以指定要调试的模块列表或使用 `DEBUG='*'` 来查看所有模块。图 9.8 展示了使用 `DEBUG='*'` 运行的来自 第四章 的项目。

##### 图 9.8\. 使用 `DEBUG='*'` 运行 Express 应用程序

![](img/09fig08_alt.jpg)

如果您想将 `NODE_DEBUG` 功能集成到自己的项目中，请使用内置的 `util.debuglog` 方法：

```
const debuglog = require('util').debuglog('example');
debuglog('You can only see these messages by setting NODE_DEBUG=example!');
```

要创建配置了 `DEBUG` 的自定义调试日志记录器，您需要使用 npm 中的调试包（[www.npmjs.com/package/debug](http://www.npmjs.com/package/debug)）。您可以创建任意数量的日志记录器。想象您正在构建一个 MVC 网络应用程序。您可以分别为模型、视图和控制器创建单独的日志记录器。然后，当测试失败时，您将能够指定调试日志，以便调试应用程序的特定部分。以下列表（位于 ch09-testing/debug-example/index.js 中）展示了如何使用调试模块。

##### 列表 9.22\. 使用 debug 包

```
const debugViews = require('debug')('debug-example:views');
const debugModels = require('debug')('debug-example:models');

debugViews('Example view message');
debugModels('Example model message');
```

要运行此示例并查看视图日志，请将 `DEBUG` 设置为 `debug-example:views`：

```
DEBUG=debug-example:views node index.js
```

调试日志的最后一个特性是，您可以在调试部分前加一个连字符来将其从日志中移除：

```
DEBUG='* -debug-example:views' node index.js
```

隐藏某些模块意味着您仍然可以使用通配符，但可以从输出中省略不需要或嘈杂的部分。

#### 9.3.2\. 获取更好的堆栈跟踪

如果您正在使用异步操作，包括您使用异步回调或承诺编写的任何内容，那么当堆栈跟踪不够详细时，您可能会遇到问题。npm 上的包可以帮助您在这种情况下。例如，当回调异步运行时，Node 不会保留操作排队时的调用栈。为了测试这一点，创建两个文件，一个名为 async.js，它定义了一个异步函数，另一个名为 index.js，它需要 async.js。以下片段是 async.js（位于书籍示例代码 ch09-testing/debug-stacktraces/async.js 中）：

```
module.exports = () => {
  setTimeout(() => {
    throw new Error();
  })
};
```

而 index.js 只需要引入 async.js：

```
require('./async.js')();
```

现在如果你使用`node index.js`运行 index.js，你将得到一个简短的堆栈跟踪，它不会显示失败函数的调用者，只会显示抛出异常的位置：

```
    throw new Error();
    ^

Error
    at null._onTimeout (async.js:3:11)
    at Timer.listOnTimeout (timers.js:92:15)
```

为了改进这种报告，安装 trace 包 ([www.npmjs.com/package/trace](http://www.npmjs.com/package/trace)) 并使用`node -r trace index.js`运行它。`-r`标志告诉 Node 在加载其他任何内容之前先 require trace 模块。

堆栈跟踪的另一个问题是它们可能过于详细。这发生在跟踪包括太多关于 Node 内部细节的情况下。为了清理你的堆栈跟踪，使用`clarify` ([www.npmjs.com/package/clarify](http://www.npmjs.com/package/clarify))。同样，你可以使用`-r`标志运行它：

```
$ node -r clarify index.js
    throw new Error();
    ^

Error
    at null._onTimeout (async.js:3:11)
```

`clarify`特别有用，如果你想在 Web 应用的错误警报电子邮件中包含堆栈跟踪。

如果你正在 Node 中运行针对浏览器的代码，可能是作为同构 Web 应用的一部分，那么你可以通过使用 source-map-support ([www.npmjs.com/package/source-map-support](http://www.npmjs.com/package/source-map-support))来获得更好的堆栈跟踪。这可以通过`-r`标志运行，但它也适用于一些测试框架：

```
$ node -r source-map-support/register index.js
$ mocha --require source-map-support/register index.js
```

下次你遇到由异步代码生成的堆栈跟踪时，寻找像`trace`和`clarify`这样的工具，以确保你从 V8 和 Node 提供的最佳功能中获益。

### 9.4. 摘要

+   编写单元测试需要测试运行器，如 Mocha。

+   Node 有一个内置的断言库，称为 assert。

+   还有其他断言库，包括 Chai 和 Should.js。

+   如果你不想运行某些代码，例如网络请求，你可以使用 Sinon.JS。

+   Sinon.JS 还允许你监视代码并验证某些函数或方法是否已运行。

+   Selenium 可以通过脚本真实浏览器来编写浏览器测试。

## 第十章：部署 Node 应用程序和维护高可用性

*本章涵盖*

+   选择托管 Node 应用程序的位置

+   部署典型应用程序

+   维护高可用性和最大化性能

开发 Web 应用是一回事，但将其投入生产是另一回事。对于每种 Web 技术，都有一些技巧和窍门可以提高稳定性和最大化性能，Node 也不例外。在本章中，你将了解如何为你的应用程序选择正确的部署环境，你还将了解如何维护高可用性。

以下部分概述了您将要部署的主要环境类型。然后您将了解如何保持高可用性。

### 10.1. 托管 Node 应用程序

你在这本书中开发的 Web 应用使用基于 Node 的 HTTP 服务器。浏览器可以与你的应用通信，而无需 Apache 或 Nginx 等专用 HTTP 服务器。然而，你可以在你的应用前面放置一个服务器，如 Nginx，这样 Node 就可以在之前能够运行 Web 服务器的任何地方托管。

云服务提供商，包括 Heroku 和 Amazon，也支持 Node。因此，您有三种方式以可靠和可扩展的方式运行您的应用程序：

+   ***平台即服务—*** 在 Amazon、Azure 或 Heroku 上运行您的应用程序

+   ***服务器或虚拟机—*** 在云中的 UNIX 或 Windows 服务器、私人托管公司或您的工作场所内部运行您的应用程序

+   ***容器—*** 使用 Docker 等软件容器运行您的应用程序和任何其他相关服务

选择使用这三种方法中的哪一种可能会很困难，尤其是尝试它们并不总是容易。请注意，每个选项并不绑定到特定的供应商：例如，Amazon 和 Azure 都能够提供所有这些部署策略。为了了解哪种选项适合您，本节解释了它们的要求以及它们的优缺点。幸运的是，每个选项都有免费或负担得起的选项，因此它们都应该对业余爱好者和专业人士 alike 都可访问。

#### 10.1.1\. 平台即服务

使用平台即服务（PaaS），您通常通过注册服务、创建新应用程序并将 Git 远程添加到项目中来准备应用程序的部署。将应用程序推送到该远程位置将部署您的应用程序。默认情况下，它将在单个容器上运行——容器确切的定义因供应商而异——如果应用程序崩溃，服务将尝试重新启动应用程序。您将获得对日志、Web 和命令行界面的有限访问权限，用于管理您的应用程序。为了扩展，您将运行更多实例的应用程序，这会带来额外的费用。表 10.1 包含了典型 PaaS 提供的特性的概述。

##### 表 10.1\. PaaS 特性

| **易用性** | 高 |
| --- | --- |
| **特性** | Git 推送部署，简单的水平扩展性 |
| **基础设施** | 抽象/黑盒 |
| **商业适用性** | 好：应用程序通常是网络隔离的 |
| **定价**^([1]) | 低流量：$$；热门网站：$$$$ |
| **供应商** | Heroku、Azure、AWS Elastic Beanstalk |

> ¹
> 
> $: 价格便宜，$$$$: 价格昂贵

PaaS 提供商支持他们自己的首选数据库和第三方数据库。对于 Heroku，这是 PostgreSQL；对于 Azure，则是 SQL 数据库。数据库连接细节将在环境变量中，因此您可以在不将数据库凭据添加到项目源代码的情况下连接。PaaS 对业余爱好者来说很棒，因为它可以以便宜或有时免费的方式运行流量低的小项目。

一些供应商比其他供应商更容易使用：对于熟悉 Git 的程序员来说，Heroku 非常容易使用，即使几乎没有系统管理员或 DevOps 知识。PaaS 系统通常知道如何运行使用 Node、Rails 和 Django 等流行工具创建的项目，因此它们几乎是即插即用的。

##### 示例：10 分钟内在 Heroku 上运行 Node

在本节中，你将部署一个应用程序到 Heroku。使用 Heroku 的默认设置，你将部署应用程序到一个轻量级的 Linux 容器，在 Heroku 术语中称为*dyno*，以服务你的应用程序。要将基本的 Node 应用程序部署到 Heroku，你需要以下先决条件：

+   部署的应用程序

+   Heroku 账户：[`signup.heroku.com/`](https://signup.heroku.com/)

+   Heroku CLI：[`devcenter.heroku.com/articles/heroku-cli`](https://devcenter.heroku.com/articles/heroku-cli)

在你有了这些元素之后，在命令行中登录 Heroku：

```
heroku login
```

Heroku 随后会提示你输入你的电子邮件地址和 Heroku 密码。接下来，创建一个简单的 Express 应用程序：

```
mkdir heroku-example
npm i -g express-generator
express
npm i
```

你可以运行`npm start`并访问 http://localhost:3000 来确保一切运行正常。下一步是创建一个 Git 仓库并创建一个 Heroku 应用程序：

```
git init
git add .
git commit –m 'Initial commit'
heroku create
git push heroku master
```

这显示了为你的应用程序生成的随机 URL 和一个 Git 远程仓库。无论何时你想部署，都可以使用 Git 提交你的更改并推送到`heroku master`。你可以使用`heroku rename`更改 URL 和应用程序的名称。

现在访问上一步骤中的[herokuapp.com](http://herokuapp.com) URL，查看你的基本 Express 应用程序。要查看应用程序日志，运行`heroku logs`，要进入应用程序的 dyno 的 shell，运行`heroku run bash`。

Heroku 是运行 Node 应用程序的一种快速简单的方式。请注意，你不需要进行任何 Node 特定的定制——Heroku 默认运行基本的 Node 应用程序，无需额外配置。然而，有时你需要对环境有更多的控制，因此在下文中，我们将介绍使用服务器托管 Node 应用程序。

#### 10.1.2\. 服务器

拥有自己服务器相比 PaaS 有一些优势。你不必担心数据库的运行位置，如果你想的话，可以在同一服务器上安装 PostgreSQL、MySQL 甚至 Redis。你可以安装任何你喜欢的软件：自定义日志软件、HTTP 服务器、缓存层——这取决于你。表 10.2 总结了运行自己服务器的主要特性。

##### 表 10.2\. 服务器功能

| **易用性** | 低 |
| --- | --- |
| **功能** | 对整个堆栈有完全控制权，运行自己的数据库和缓存层 |
| **基础设施** | 对开发者（或系统管理员/DevOps）开放 |
| **商业适用性** | 如果你有能够维护服务器的员工，则适用性良好 |
| **定价** | 小型虚拟机：$；大型托管服务器：$$$$$ |
| **供应商** | Azure、Amazon、托管公司 |

你可以通过多种方式获取和维护服务器。你可以从 Linode 或 Digital Ocean 等公司获得便宜的虚拟机；这将是一个你可以按需配置的全服务器，但它将与同一硬件上的其他虚拟机共享资源。你也可以购买自己的硬件或租用服务器。一些托管公司提供托管服务，他们会帮助你维护服务器的操作系统。

你必须决定你想使用哪个操作系统。Debian 有几个版本，Node 在 Windows 和 Solaris 上也能很好地工作，所以选择比看起来更困难。

另一个关键的决定是如何将你的应用程序暴露给世界：流量可以从 80 端口和 443 端口重定向到你的应用程序，但你也可以在它前面放置 Nginx 来代理请求并可能处理静态文件。

你有多种方式将你的代码从你的仓库移动到服务器。你可以使用 scp、sftp 或 rsync 手动复制文件，或者使用 Chef 来控制多个服务器并管理发布。有些人设置了一个类似 Heroku 的 Git 钩子，它将根据对某个 Git 分支的推送自动更新服务器上的应用程序。

重要的是要认识到管理自己的服务器是困难的。配置需要大量工作，服务器还需要维护最新的操作系统错误修复和安全更新。如果你是爱好者，这可能会让你望而却步——但你将学到很多东西，也许会发现对 DevOps 的兴趣。

在虚拟机或完整服务器上运行 Node 应用程序不需要任何特殊的东西。如果你想看看在服务器上运行 Node 应用程序并长时间运行所使用的某些技术，请跳转到第 10.2 节，了解部署基础知识。否则，继续阅读以了解 Node 和 Docker。

#### 10.1.3\. 容器

使用软件容器是一种操作系统虚拟化，它自动化了应用程序的部署。最著名的项目是 Docker，它是开源的，但也提供商业服务，帮助你部署生产应用程序。表 10.3 显示了容器的主要功能。

##### 表 10.3\. 服务器功能

| **易用性** | 中等 |
| --- | --- |
| **功能** | 对整个堆栈有完全控制权，运行自己的数据库和缓存层，可以重新部署到各种提供商和本地机器 |
| **基础设施** | 对开发者（或系统管理员/DevOps）开放 |
| **商业适用性** | 极佳：部署到托管主机、Docker 主机或自己的数据中心 |
| **定价** | $$$ |
| **供应商** | Azure、Amazon、Docker Cloud、Google Cloud Platform（带 Kubernetes）、允许你运行 Docker 容器的托管公司 |

Docker 允许你以镜像的形式定义你的应用程序。如果你构建了一个典型的内容管理系统，它有一个用于图像处理的微服务、一个用于存储应用程序数据的主要服务，然后是一个后端数据库，你可以使用四个单独的 Docker 镜像来部署它：

+   ***图 1—*** 用于调整上传到 CMS 的图像的微服务

+   ***图 2—*** PostgreSQL

+   ***图 3—*** 带有管理界面的主要 CMS Web 应用程序

+   ***图 4—*** 公共前端 Web 应用程序

由于 Docker 是开源的，您在部署 Docker 化应用程序时不受单一供应商的限制。您可以使用 Amazon 的 Elastic Beanstalk 来部署您的镜像，Docker Cloud，甚至 Microsoft 的 Azure。Amazon 还提供 EC2 容器服务（ECS），以及 AWS CodeCommit 用于云 Git 仓库，这些都可以以类似于 Heroku 的方式部署到 Elastic Beanstalk。

使用容器的一个令人惊叹之处在于，在您将应用程序容器化之后，您只需一个命令就可以启动它的一个全新实例。如果您得到一台新电脑，您只需检出应用程序的仓库，在本地安装 Docker，然后运行脚本以启动您的应用程序。因为您的应用程序有一个定义良好的部署配方，所以您和您的合作者更容易理解应用程序应该在本地开发环境之外如何运行。

##### 示例：使用 Docker 运行 Node 应用程序

示例：[`nodejs.org/en/docs/guides/nodejs-docker-webapp/`](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

要使用 Docker 运行 Node 应用程序，您首先需要做一些事情：

1.  安装 Docker: [`docs.docker.com/engine/installation/`](https://docs.docker.com/engine/installation/).

1.  创建一个 Node 应用程序。有关如何快速创建示例 Express 应用程序的详细信息，请参阅第 10.1.1 节，平台即服务。

1.  在项目中添加一个名为 Dockerfile 的新文件。

Dockerfile 告诉 Docker 如何构建您的应用程序镜像，以及如何安装和运行应用程序。您将通过在 Dockerfile 中指定`FROM node:boron`来使用官方 Node Docker 镜像 ([`hub.docker.com/_/node/`](https://hub.docker.com/_/node/))，然后使用`RUN`和`CMD`指令运行`npm install`和`npm start`。以下是一个适用于简单 Node 应用程序的完整 Dockerfile 示例：

```
FROM node:argon

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

COPY package.json /usr/src/app/
RUN npm install

COPY . /usr/src/app

EXPOSE 3000
CMD ["npm", "start"]
```

创建 Dockerfile 后，在终端中运行`docker build` ([`docs.docker.com/engine/reference/commandline/build/`](https://docs.docker.com/engine/reference/commandline/build/)) 命令来创建应用程序镜像。您只需指定要构建的目录，因此如果您在示例 Express 应用程序中，应该能够键入`docker build .`来构建镜像并将其发送到 Docker 守护进程。

运行`docker images`以查看镜像列表。获取镜像 ID，然后运行`docker run -p 8080:3000 -d <image ID>`来运行应用程序。我们已经将内部端口（3000）绑定到本地的 8080 端口，因此要访问应用程序，我们可以在浏览器中使用 http://localhost:8080。

### 10.2. 理解部署基础知识

假设您创建了一个您想要展示的 Web 应用程序，或者您可能创建了一个商业应用程序，在将其投入全面生产之前需要对其进行测试。您可能从简单的部署开始，然后在以后的工作中最大限度地提高正常运行时间和性能。在本节中，我们将向您介绍一个简单的、临时的 Git 部署，以及如何使用 Forever 保持应用程序运行和运行的详细信息。临时部署在重启后不会持久存在，但它们的优势是设置快速。

#### 10.2.1\. 从 Git 仓库部署

让我们快速通过使用 Git 仓库的基本部署步骤来让您了解基本步骤。部署通常是通过以下步骤完成的：

1.  使用 SSH 连接到服务器。

1.  如果需要，在服务器上安装 Node 和版本控制工具（如 Git 或 Subversion）。

1.  从版本控制仓库将应用程序文件（包括 Node 脚本、图像和 CSS 样式表）下载到服务器上。

1.  启动应用程序。

这是在使用 Git 下载应用程序文件后启动应用程序的一个示例：

```
git clone https://github.com/Marak/hellonode.git
cd hellonode
node server.js
```

与 PHP 一样，Node 不会作为后台任务运行。因此，我们概述的基本部署需要保持 SSH 连接打开。一旦 SSH 连接关闭，应用程序将终止。幸运的是，通过使用简单的工具，保持应用程序运行相当容易。

| |
| --- |

**自动化部署**

您可以通过多种方式自动化部署您的 Node 应用程序。其中一种方法是使用像 Fleet（[`github.com/substack/fleet`](https://github.com/substack/fleet)）这样的工具，它允许您通过使用`git push`将应用程序部署到一台或多台服务器上。另一种更传统的方法是使用 Capistrano，这在 Evan Tahler 的 Bricolage 博客上的“使用 Capistrano 部署 node.js 应用程序”文章中有详细说明（[`blog.evantahler.com/deploying-node-js-applications-with-capistrano-af675cdaa7c6#.8r9v0kz3l`](https://blog.evantahler.com/deploying-node-js-applications-with-capistrano-af675cdaa7c6#.8r9v0kz3l)）。

| |
| --- |

#### 10.2.2\. 保持 Node 运行

假设您使用 Ghost 博客应用程序（[`ghost.org/`](https://ghost.org/)）创建了一个个人博客，并且您想要部署它，确保即使您断开 SSH 连接，它也能保持运行。

在 Node 社区中，处理此问题的最受欢迎的工具是 Nodejitsu 的 Forever（[`github.com/foreverjs/forever`](https://github.com/foreverjs/forever)）。它可以在您断开 SSH 连接后保持应用程序运行，并在应用程序崩溃时重新启动它。图 10.1 从概念上展示了 Forever 是如何工作的。

##### 图 10.1\. Forever 工具帮助您保持应用程序运行，即使它崩溃。

![](img/10fig01_alt.jpg)

您可以使用`sudo`命令全局安装 Forever。

| |
| --- |

##### sudo 命令

有时在全局安装 npm 模块（使用`-g`标志）时，你需要将`npm`命令前缀为`sudo`命令（[www.sudo.ws](http://www.sudo.ws)），以便以超级用户权限运行 npm。第一次使用`sudo`命令时，你将被提示输入你的密码。然后运行它后面的指定命令。

|  |
| --- |

如果你正在跟随，现在请使用以下命令安装 Forever：

```
npm install -g forever
```

安装 Forever 后，你可以使用以下命令来启动你的博客并保持其运行：

```
forever start server.js
```

如果你想出于某种原因停止你的博客，你可以使用 Forever 的`stop`命令：

```
forever stop server.js
```

当使用 Forever 时，你可以使用它的`list`命令来获取它正在管理的应用程序列表：

```
forever list
```

Forever 的另一个有用功能是它可以在任何源文件更改时可选地重新启动你的应用程序。这让你从每次添加功能或修复错误时都需要手动重新启动的麻烦中解脱出来。

要以这种方式启动 Forever，请使用`-w`标志：

```
forever -w start server.js
```

虽然 Forever 是一个部署应用程序的极其有用的工具，但你可能想要使用功能更全面的工具进行长期部署。在下一节中，你将了解一些工业级监控解决方案，并了解如何最大化应用程序性能。

### 10.3. 最大化运行时间和性能

当一个 Node 应用程序值得发布时，你想要确保它在服务器启动和停止时启动和停止，并且在服务器崩溃时自动重新启动。很容易忘记在重启前停止应用程序，或者忘记在之后重新启动应用程序。

你还想要确保你正在采取步骤来最大化性能。例如，当你在一个拥有四核 CPU 的服务器上运行你的应用程序时，只使用单个核心是没有意义的。如果你只使用单个核心，并且你的 Web 应用程序的流量显著增加，单个核心可能没有足够的处理能力来处理流量，你的 Web 应用程序将无法持续响应。

除了使用所有 CPU 核心外，你还想避免使用 Node 为高流量生产站点托管静态文件。Node 针对交互式应用程序，如 Web 应用程序和 TCP/IP 协议，它不能像专门为此优化的软件那样有效地服务静态文件。对于服务静态文件，你应该使用像 Nginx（[`nginx.org/en/`](http://nginx.org/en/)）这样的技术，它专门用于服务静态文件。或者，你也可以将所有静态文件上传到内容分发网络（CDN），如 Amazon S3（[`aws.amazon.com/s3/`](http://aws.amazon.com/s3/)），并在你的应用程序中引用这些文件。

本节涵盖了一些服务器运行时间和性能提示：

+   使用 Upstart 保持你的应用程序在重启和崩溃中持续运行

+   使用 Node 的集群 API 进行多核处理器

+   使用 Nginx 服务 Node 应用程序的静态文件

让我们先看看一个强大且易于使用的工具，用于维护正常运行时间：Upstart。

#### 10.3.1\. 使用 Upstart 维护正常运行时间

假设你对一个应用程序感到满意，并希望将其推向全球市场。你想要确保，如果你重启服务器，不要忘记重启你的应用程序。你还想确保，如果你的应用程序崩溃，它不仅会自动重启，而且崩溃会被记录下来，你会收到通知，这样你可以诊断任何潜在的问题。

Upstart ([`upstart.ubuntu.com`](http://upstart.ubuntu.com)) 是一个项目，它提供了一种优雅的方式来管理任何 Linux 应用程序的启动和停止，包括 Node 应用程序。现代版本的 Ubuntu 和 CentOS 支持使用 Upstart。对于 macOS 的替代方案是创建 launchd 文件（npm 上的 node-launchd 可以做到这一点），而 Windows 的等效方案是使用 Windows 服务，这由 npm 上的 node-windows 包支持。

如果你还没有安装 Upstart，可以使用以下命令在 Ubuntu 上安装：

```
sudo apt-get install upstart
```

如果你还没有安装 Upstart，可以使用以下命令在 CentOS 上安装：

```
sudo yum install upstart
```

在安装 Upstart 之后，你需要为你的每个应用程序添加一个 Upstart 配置文件。这些文件位于 /etc/init 目录中，命名类似于 my_application_name.conf。配置文件不需要标记为可执行。

以下为本章示例应用程序创建一个空的 Upstart 配置文件：

```
sudo touch /etc/init/hellonode.conf
```

现在将以下列表的内容添加到你的配置文件中。此设置将在服务器启动时运行应用程序，并在关闭时停止应用程序。`exec` 部分将由 Upstart 执行。

##### 列表 10.1\. 一个典型的 Upstart 配置文件

![](img/10lis01_alt.jpg)

此配置将在服务器重启后以及意外崩溃后保持进程运行。所有应用程序生成的输出都将发送到 /var/log/upstart/hellonode.log，Upstart 将为你管理日志轮转。

现在你已经创建了一个 Upstart 配置文件，你可以使用以下命令启动你的应用程序：

```
sudo service hellonode
```

如果你的应用程序启动成功，你会看到类似以下的行：

```
hellonode start/running, process 6770
```

Upstart 具有高度的可配置性。查看在线食谱（[`upstart.ubuntu.com/cookbook/`](http://upstart.ubuntu.com/cookbook/)），了解所有可用选项。

##### Upstart 和重启

当使用 `respawn` 选项时，Upstart 在默认情况下会在崩溃时不断重新加载你的应用程序，除非应用程序在 5 秒内重启了 10 次。你可以通过使用 `respawn limit COUNT INTERVAL` 选项来更改此限制，其中 `COUNT` 是在 `INTERVAL`（以秒为单位指定）内的次数。例如，你可以这样设置 5 秒内 20 次的限制：

```
respawn
respawn limit 20 5
```

如果你的应用程序在 5 秒内（默认限制）重新加载 10 次，通常意味着代码或配置有问题，它将无法成功启动。Upstart 达到限制后不会尝试重新启动，以节省其他进程的资源。

在 Upstart 之外进行健康检查是个好主意，它可以通过电子邮件或其他快速通信方式向开发团队提供警报。对于一个 Web 应用程序，健康检查可以简单地涉及访问网站并查看你是否得到了有效的响应。你可以自己编写方法或使用 Monit ([`mmonit.com/monit/`](http://mmonit.com/monit/)) 或 Zabbix ([www.zabbix.com](http://www.zabbix.com)) 等工具来完成这项工作。

现在你已经知道了如何让你的应用程序在崩溃和服务器重启的情况下继续运行，接下来的一个合乎逻辑的担忧是性能。Node 的集群 API 可以帮助解决这个问题。

#### 10.3.2. 集群 API：利用多个核心

大多数现代计算机 CPU 都有多个核心，但 Node 进程在运行时只使用其中之一。如果你在服务器上托管 Node 应用程序并希望最大化服务器的使用，你可以手动在不同的 TCP/IP 端口上启动多个应用程序实例，并使用负载均衡器将这些 Web 流量分配到这些实例，但这需要繁琐的设置。

为了使单个应用程序更容易使用多个核心，Node 中添加了集群 API。这个 API 使得你的应用程序能够同时在不同的核心上运行多个工作者，每个工作者都执行相同的事情并响应相同的 TCP/IP 端口。图 10.2 展示了在四核处理器上使用集群 API 组织应用程序处理的方式。

##### 图 10.2. 在四核处理器上主进程产生三个工作者

![](img/10fig02.jpg)

下面的列表会自动为每个额外的核心启动一个主进程和一个工作者。

##### 列表 10.2. Node 的集群 API 演示

![](img/10lis02_alt.jpg)

由于主进程和工作者运行在独立的操作系统进程中，这是它们在单独的核心上运行所必需的，因此它们不能通过全局变量共享状态。但集群 API 确实为主进程和工作者提供了通信的途径。

下面的列表显示了一个示例，其中消息在主进程和工作者之间传递。主进程维护所有请求的计数，每当工作者报告处理了一个请求，它就会被转发给每个工作者。

##### 列表 10.3. Node 的集群 API 演示

![](img/ch10ex03-0.jpg)

![](img/ch10ex03-1.jpg)

使用 Node 的集群 API 是创建利用现代硬件优势的应用程序的一种简单方法。

#### 10.3.3. 托管静态文件和代理

虽然 Node 是提供动态 Web 内容的有效解决方案，但它不是提供静态文件（如图片、CSS 样式表或客户端 JavaScript）的最有效方式。在 HTTP 上提供静态文件是一项特定的任务，特定的软件项目已经针对这项任务进行了优化，因为它们已经专注于这项任务多年。

幸运的是，Nginx ([`nginx.org/en/`](http://nginx.org/en/))，一个针对提供静态文件优化的开源 Web 服务器，与 Node 一起设置起来非常简单，用于提供这些文件。在典型的 Nginx/Node 配置中，Nginx 最初处理每个 Web 请求，将不是静态文件的请求回传给 Node。图 10.3 展示了这种配置。

##### 图 10.3. 您可以使用 Nginx 作为代理快速将静态资源回传给 Web 客户端。

![图片](img/10fig03.jpg)

下面的列表中的配置，将被放置在 Nginx 配置文件的 `http` 部分，实现了这种设置。配置文件通常存储在 Unix 服务器的 /etc 目录中的 /etc/nginx/nginx.conf。

##### 列表 10.4. 使用 Nginx 代理 Node.js 和提供静态文件的配置文件

![图片](img/ch10ex04-0.jpg)

![图片](img/ch10ex04-1.jpg)

通过使用 Nginx 处理您的静态网络资源，您确保 Node 专注于它最擅长的事情。

### 10.4. 概述

+   Node 应用程序可以由 PaaS 提供商、专用服务、虚拟专用服务器和云托管来托管。

+   您可以使用 Forever 和 Upstart 快速将 Node 应用程序部署到 Linux 服务器。

+   为了使您的应用程序性能更佳，Node 的集群模块允许您运行多个进程。
