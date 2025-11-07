## 第二部分\. 构建 Node 网络应用程序

Node.js 是任何 MEAN 应用程序的基础，因此您将从这里开始。在整个第二部分中，您将通过使用 Node.js、Express 和 MongoDB 来构建一个数据驱动的 Web 应用程序。您将在学习过程中了解这些技术，并逐步构建应用程序，直到您拥有一个完全功能的 Node 网络应用程序。

在第三章中，您将通过创建和设置一个 MEAN 项目开始，在深入了解 Express 之前先熟悉 Express，然后在第四章中通过构建应用程序的静态版本来获得更深入的理解。在第五章中，您将使用 MongoDB 和 Mongoose 来设计和构建您需要的数据库模型。

良好的应用程序架构应包括数据 API，而不是将数据库交互与应用程序逻辑紧密耦合。在第六章中，您将使用 Express、MongoDB 和 Mongoose 创建一个 REST API，然后在第七章中将它连接回应用程序，通过从静态应用程序中消费 REST API 来实现。当您到达第二部分的结尾时，您将拥有一个使用 Node.js、MongoDB 和 Express 构建的数据驱动网站，以及一个完全功能的 REST API。

## 第三章\. 创建和设置 MEAN 项目

*本章涵盖*

+   使用 npm 和 package.json 管理依赖项

+   创建和配置 Express 项目

+   设置 MVC 环境

+   添加 Twitter Bootstrap 进行布局

+   发布到实时 URL，并使用 Git 和 Heroku

在本章中，您将开始构建您的应用程序。记住，从第一章和第二章中，在整个本书中，您将构建一个名为 Loc8r 的应用程序——一个位置感知的 Web 应用程序，它显示用户附近的列表，并邀请人们登录并留下评论。

| |
| --- |

**获取源代码**

此应用程序的源代码位于 GitHub 上的[`github.com/cliveharber/gettingMean-2`](https://github.com/cliveharber/gettingMean-2)。每个有重大更新的章节都将有自己的分支。我们鼓励您从头开始构建，但如果您愿意，您可以从 GitHub 上的 chapter-03 分支获取本章中将要构建的代码。在终端的新文件夹中，如果您已经安装了 Git，以下两个命令将克隆它：

```
$ git clone -b chapter-03 https://github.com/cliveharber/
gettingMean-2.git
```

这为您提供了存储在 GitHub 上的代码副本。要运行应用程序，您需要使用以下命令安装一些依赖项：

```
$ cd gettingMean-2
$ npm install
```

如果其中一些内容现在还不明白，或者一些命令不起作用，请不要担心。在本章中，您将在学习过程中安装这些技术。

| |
| --- |

在 MEAN 堆栈中，Express 是 Node 网络应用程序框架。Node.js 和 Express 一起构成了整个堆栈的基础，所以你将从那里开始。在构建应用程序架构方面，图 3.1 显示了本章的重点。你将做两件事：

1.  创建项目以及封装的 Express 应用程序，该应用程序将包含除数据库之外的所有内容。

1.  设置主 Express 应用程序。

##### 图 3.1\. 创建封装的 Express 应用程序并开始设置主 Express 应用程序

![](img/03fig01_alt.jpg)

你将从一些基础工作开始，通过查看 Express 来了解如何使用 npm 和 package.json 文件来管理依赖项和模块。你需要这些背景知识来开始并设置一个 Express 项目。

在你做任何事情之前，确保你的机器上已经安装了所有你需要的东西。当这一切都准备好了，看看如何从命令行创建新的 Express 项目以及你可以在此时指定的各种选项。

Express 很好，但你可以通过稍微调整一些东西来使其更好，并更好地了解它。这涉及到对模型-视图-控制器（MVC）架构的快速了解。这里你可以稍微深入 Express 的内部，通过修改它来获得一个清晰的 MVC 设置。

当 Express 框架按照你的意愿设置好时，你将包括 Twitter 的 Bootstrap 框架，并通过更新 Pug 模板使网站响应式。在本章的最后一步，你将使用 Heroku 和 Git 将修改后的、响应式的 MVC Express 应用程序推送到一个实时 URL。

### 3.1\. 简要了解 Express、Node 和 npm

如前所述，Express 是 Node 的一个网络应用程序框架。在基本术语中，一个 Express 应用程序是一个恰好使用 Express 作为框架的 Node 应用程序。记得从第一章中提到的 npm 是一个包管理器，当你安装 Node 时会自动安装，这使你能够下载 Node 模块或包来扩展应用程序的功能。

但这些是如何协同工作的，以及你如何使用它们呢？这个谜题的关键部分是 package.json 文件。

#### 3.1.1\. 使用 package.json 定义包

在每个 Node 应用程序中，你都应该在应用程序的根目录中有一个名为 package.json 的文件。此文件可以包含有关项目的一些元数据，包括它运行所依赖的包。以下列表显示了一个你可能在 Express 项目的根目录中找到的示例 package.json 文件。

##### 列表 3.1\. 新 Express 项目中的示例 package.json 文件

```
{
  "name": "application-name",        ***1***
  "version": "0.0.0",                ***1***
  "private": true,                   ***1***
  "scripts": {                       ***1***
    "start": "node ./bin/www"        ***1***
  },                                 ***1***
  "dependencies":                   ***2***
    "body-parser": "~1.18.3",       ***2***
    "cookie-parser": "~1.4.3",      ***2***
    "debug": "~4.1.0",              ***2***
    "express": "⁴.16.4",           ***2***
    "morgan": "¹.9.1",             ***2***
    "pug": "².0.3",                ***2***
    "serve-favicon": "~2.5.0"       ***2***
  }                                 ***2***
}
```

+   ***1*** **定义应用程序的各种元数据**

+   ***2*** **应用程序运行所需的包依赖项**

此列表是文件的完整内容，因此它并不特别复杂。文件顶部的各种元数据后面跟着依赖项部分。在这个 Express 项目的默认安装中，Express 运行需要许多依赖项，但您不必担心每个依赖项的作用。Express 本身是模块化的，这样您可以单独添加组件或升级它们。

#### 3.1.2\. 在 package.json 中处理依赖项版本

在每个依赖项的名称旁边是应用程序将使用的版本号。请注意，它们前面带有 tilde (`~`) 或 caret (^)。

查看 Express 4.16.3 的依赖项定义，它指定了三个级别的特定版本：

+   主版本 (4)

+   次要版本 (16)

+   补丁版本 (3)

在整个版本号前加上 ~ 就像用通配符替换了补丁版本，这意味着应用程序将使用可用的最新补丁版本。同样，在版本前加上一个 caret (^) 就像用通配符替换了次要版本。这已成为最佳实践，因为补丁和次要版本应只包含不会对应用程序产生任何影响的修复。但是，当进行破坏性更改时，会发布新的主要版本，因此您希望避免自动使用这些版本的后续版本，以防破坏性更改影响您的应用程序。如果您发现一个违反这些规则的模块，可以通过移除任何前缀来轻松指定要使用的确切版本。请注意，出于这个原因，始终指定完整版本而不使用通配符是良好的实践：您始终有一个您 *知道* 可以正常工作的特定版本的参考。

#### 3.1.3\. 使用 npm 安装 Node 依赖项

任何 Node 应用程序或模块都可以在 package.json 文件中定义依赖项。安装它们很容易，并且无论应用程序或模块如何，都是用相同的方式进行安装。

在与 package.json 文件相同的文件夹中使用终端提示符，运行以下命令：

```
$ npm install
```

此命令告诉 npm 安装 package.json 文件中列出的所有依赖项。当您运行它时，npm 下载所有列出的依赖项包，并将它们安装到应用程序的特定文件夹中，该文件夹称为 node_modules。图 3.2 说明了三个关键部分。

##### 图 3.2\. 当您运行 `npm install` 终端命令时，package.json 文件中定义的 npm 模块将被下载并安装到应用程序的 node_modules 文件夹中。

![](img/03fig02_alt.jpg)

npm 将每个包安装到其自己的子文件夹中，因为每个包本身就是一个 Node 包。因此，每个包也有自己的 package.json 文件，定义了元数据，包括特定的依赖项。一个包拥有自己的 node_modules 文件夹是很常见的。尽管如此，您不必担心手动安装所有嵌套依赖项，因为这项任务由原始的 `npm install` 命令处理。

##### 向现有项目添加更多包

你可能一开始就不会有一个项目的完整依赖项列表。更有可能的是，你会从几个关键依赖项开始，这些依赖项你知道你需要，也许还有一些你在工作流程中总是使用的依赖项。

使用 npm，你可以随时轻松地向应用程序添加更多包。找到你想要安装的包的名称，在同一文件夹中打开 package.json 文件所在的命令提示符，然后运行一个简单的命令，例如：

```
$ npm install --save package-name
```

使用此命令，npm 将下载并安装 node_modules 文件夹中的新包。`--save`标志告诉 npm 将此包添加到 package.json 文件中的依赖项列表中。从 npm 版本 5 开始，`--save`标志不再需要，因为 NPM 会自动将更改保存到 package.json 文件中。我们在这里添加它是为了完整性。当运行此命令时，npm 会生成一个 package-lock.json 文件，以在环境之间维护依赖项的版本，这在从开发环境部署到实时服务器时非常有用。

##### 更新包到较新版本

npm 下载并重新安装现有包的唯一情况是你升级到新版本。当你运行`npm install`时，npm 会遍历所有依赖项并检查以下内容：

+   package-lock.json 文件中定义的版本（如果存在）或 package.json（如果不存在）

+   npm 上的最新匹配版本（这可能与你使用了`~`或`^`不同）

+   node_modules 文件夹中模块的版本（如果有的话）

如果你的安装版本与 package.json（或 package-lock.json）文件中的定义不同，npm 将下载并安装定义的版本。同样，如果你正在使用通配符，并且有更晚的匹配版本可用，npm 将下载并安装它以替换之前的版本。

在掌握这些知识的基础上，你可以开始创建你的第一个 Express 项目。

### 3.2\. 创建 Express 项目

所有旅程都必须有一个起点，对于构建 MEAN 应用程序来说，这个起点就是创建一个新的 Express 项目。要创建一个 Express 项目，你需要在你的开发机器上安装以下五个关键组件：

+   Node 和 npm

+   全局安装的 Express 生成器

+   Git

+   Heroku

+   适合的命令行界面（CLI）或终端

#### 3.2.1\. 安装组件

如果你还没有安装 Node、npm 或 Express 生成器，请参阅附录 A 中的说明和在线资源指南。所有这些都可以安装在 Windows、macOS 和所有主流 Linux 发行版上。

到本章结束时，你还将使用 Git 来管理 Loc8r 应用程序的源代码控制，并将其推送到由 Heroku 托管的实时 URL。请参阅附录 B，其中将指导你设置 Git 和 Heroku。 

根据您的操作系统，您可能需要安装一个新的 CLI 或终端。请参阅附录 B 以了解此要求是否适用于您。


##### 注意

在本书中，我们经常将 CLI 称为*终端*。当我们说“在终端中运行此命令”时，意味着在您使用的任何 CLI 中运行它。当终端命令作为本书中的代码片段包含时，它们以 `$` 开头。您不应在终端中键入此符号；它只是用来表示命令行语句。例如，如果您输入 echo 命令 `$ echo 'Welcome to Getting MEAN'`，则键入 `echo 'Welcome to Getting MEAN'`。


#### 3.2.2\. 验证安装

要创建一个新的 Express 项目，你必须已安装 Node 和 npm，并且还必须全局安装 Express 生成器。您可以通过在终端中检查版本号来验证，使用以下命令：

```
$ node --version
$ npm --version
$ express --version
```

每个这些命令都应该在终端输出一个版本号。如果其中之一失败，请转到附录 A 以获取如何重新安装的详细信息。

#### 3.2.3\. 创建项目文件夹

假设一切顺利，首先在您的机器上创建一个名为 loc8r 的新文件夹。这个文件夹可以放在您的桌面上、您的文档中或 Dropbox 文件夹中；位置不重要，只要您有对该文件夹的完整读写权限即可。

西蒙在他的 Dropbox 文件夹中进行了大量的 MEAN 开发，以便他的工作可以立即备份并在他的任何机器上访问。然而，如果您处于企业环境中，这种方法可能不适合您，因此请创建您认为最好的文件夹。

#### 3.2.4\. 配置 Express 安装

Express 项目是通过命令行安装的，配置是通过你使用的命令的参数传递的。如果你不熟悉使用命令行，不要担心；本书中我们将要讨论的内容并不特别复杂，而且都很容易记住。一旦你开始使用它，你可能会爱上它使某些操作变得如此快速。

您可以使用简单的命令在文件夹中安装 Express（但不要现在这样做）：

```
$ express
```

此命令使用默认设置在当前文件夹中安装框架。这一步可能是一个好的开始，但首先看看一些配置选项。

##### 创建 Express 项目时的配置选项

以这种方式创建 Express 项目时可以配置什么？您可以指定以下内容：

+   使用哪个 HTML 模板引擎

+   使用哪个 CSS 预处理器

+   是否创建一个 .gitignore 文件

默认安装使用 Jade 模板引擎，但它没有 CSS 预处理或会话支持。你可以指定一些选项，如表 3.1 中所述。

##### 表 3.1\. 创建新的 Express 项目的命令行配置选项

| 配置命令 | 影响 |
| --- | --- |
| --css=less&#124;stylus | 根据你在命令中输入的内容，将 CSS 预处理器添加到你的项目中，无论是 Less 还是 Stylus |
| --view=ejs&#124;hbs&#124;pug | 根据你输入的选项，将 HTML 模板引擎从 Jade 更改为 EJS、Handlebars 或 Pug |
| --git | 在目录中添加一个 .gitignore 文件 |

你在这里不会这样做，但如果你想创建一个使用 Less CSS 预处理器和 Handlebars 模板引擎并包含 .gitignore 文件的项目，你将在终端中运行以下命令：

```
$ express --css=less --view=hbs --git
```

为了使你的项目保持简单，你不会使用 CSS 预处理器，因此你可以坚持使用默认的纯 CSS。但是，你需要使用一个模板引擎，所以在下文中，你会快速浏览一下选项。

##### 不同的模板引擎

当你以这种方式使用 Express 时，有一些模板选项可供选择，包括 Jade、EJS、Handlebars 和 Pug。模板引擎的基本工作流程是创建 HTML 模板，包括数据占位符，然后传递一些数据。然后引擎将模板和数据一起编译，以创建浏览器将接收的最终 HTML 标记。

所有引擎都有自己的优点和特性，如果你已经有一个偏好的引擎，那很好。在这本书中，你将使用 Pug。Pug 功能强大，提供了你将要需要的所有功能。Pug 是 Jade 的下一代；由于商标问题，Jade 的创造者不得不将其重命名，他们选择了 Pug。Jade 仍然存在，所以现有的项目不会中断，但所有的新版本都使用 Pug 命名。Jade 曾经（并且仍然是）Express 的默认模板引擎，所以你会发现大多数在线的示例和项目都使用它，这意味着熟悉语法是有帮助的。最后，Jade 和 Pug 的最小化风格使它们非常适合在书中展示代码示例。

##### 快速了解 Pug

与其他模板引擎相比，Pug 很不寻常，因为它在模板中不包含 HTML 标签。相反，Pug 采用了一种相当简约的方法，使用标签名称、缩进和受 CSS 启发的引用方法来定义 HTML 的结构。例外的是 `<div>` 标签。因为它非常常见，如果模板中省略了标签名称，Pug 假设你想要一个 `<div>`。


##### 小贴士

Pug 模板必须使用空格缩进，而不是制表符。


以下代码片段显示了一个简单的 Pug 模板示例：

```
#banner.page-header               ***1***
  h1 My page                      ***1***
  p.lead Welcome to my page       ***1***
```

+   ***1*** **Pug 模板不包含 HTML 标签**

此代码片段显示了编译后的输出：

```
<div id="banner" class="page-header">        ***1***
  <h1>My page</h1>                           ***1***
  <p class="lead">Welcome to my page</p>     ***1***
</div>                                       ***1***
```

+   ***1*** **编译后的输出是可识别的 HTML**

从输入和输出的第一行，你应该能够看到：

+   如果你没有指定标签名称，将创建一个 `<div>`。

+   在 Pug 中 `#banner` 变成 HTML 中的 `id="banner"`。

+   在 Pug 中 `.page-header` 变成 HTML 中的 `class="page-header"`。

注意，Pug 中的缩进很重要，因为它定义了 HTML 输出的嵌套。记住，缩进必须使用空格，而不是制表符！

回顾一下，你不需要 CSS 预处理器，但确实想要 Pug 模板引擎。那么.gitignore 文件呢？

##### .gitignore 文件的快速介绍

.gitignore 文件是一个简单的配置文件，位于项目文件夹的根目录。此文件指定 Git 命令应忽略哪些文件和文件夹。本质上，它说，“假装这些文件不存在，不要跟踪它们”，这意味着它们不会出现在源控制中。

常见的例子包括日志文件和 node_modules 文件夹。日志文件不需要上传到 GitHub 供每个人查看，而且你的 Node 依赖项应该在应用程序下载时从 npm 安装。你将在第 3.5 节中使用 Git，所以请让 Express 生成器为你创建一个文件。

在你掌握这些基础知识之后，是时候创建一个项目了。

#### 3.2.5. 创建 Express 项目并尝试运行

你已经知道了创建 Express 项目的基本命令，并决定使用 Pug 模板引擎。你还将让它为你生成一个.gitignore 文件。现在创建一个新的项目。在第 3.2.3 节中，你应该创建了一个名为 loc8r 的新文件夹。在终端中导航到这个文件夹，并运行以下命令：

```
$ express --view=pug --git
```

此命令在 loc8r 文件夹内创建了一系列文件夹和文件，这些文件构成了你的 Loc8r 应用程序的基础。但你现在还没有完全准备好。接下来，你需要安装依赖项。你可能还记得，你可以通过在 package.json 文件所在的文件夹中的终端提示符下运行以下命令来完成此操作：

```
$ npm install
```

一旦运行，你的终端窗口就会亮起，显示所有正在下载的内容。当它完成后，应用程序就准备好测试了。

##### 尝试运行

确保一切按预期工作。在第 3.2.6 节中，我们将向您展示运行项目的更好方法。

在终端中，在 loc8r 文件夹中，运行以下命令（但如果你的应用程序在一个不同名称的文件夹中，相应地替换`loc8r`）：

```
$ DEBUG=loc8r:* npm start
```

你应该会看到类似的确认信息：

```
loc8r:server Listening on port 3000 +0ms
```

这个确认意味着 Express 应用程序正在运行。你可以通过打开浏览器并转到 localhost:3000 来看到它在行动。我们希望你会看到类似于图 3.3 中的截图。

##### 图 3.3. 纯粹的 Express 项目的着陆页

![图片 3.3 的替代文本](img/03fig03_alt.jpg)

虽然这并不完全是开创性的东西，但将 Express 应用程序运行起来并在浏览器中工作是非常容易的，对吧？

如果你现在回到终端，你应该会看到一些日志语句确认页面已被请求，并且样式表已被请求。为了更好地了解 Express，看看这里发生了什么。

##### Express 如何处理请求

默认的 Express 着陆页很简单。页面包含少量 HTML，其中一些文本内容是通过 Express 路由推送到数据中的。还有一个 CSS 文件。终端中的日志应该确认这是 Express 请求并返回给浏览器的内容。但这是如何实现的呢？

| |
| --- |

**关于 Express 中介件**

app.js 文件中包含一些以`app.use`开头的行。这些行被称为*中介件*。当请求进入应用程序时，它会依次通过每一块中介件。每一块中介件可能会也可能不会对请求进行操作，但它总是传递给下一块，直到达到应用程序逻辑本身，然后返回一个响应。

以`app.use(express.cookieParser());`为例。这一行接收一个传入的请求，解析出任何 cookie 信息，并以一种便于在控制器代码中引用的方式将数据附加到请求上。

你现在不需要知道每块中介件具体做什么，但随着你构建应用程序，你可能会发现自己正在添加到这个列表中。

| |
| --- |

所有对 Express 服务器的请求都会通过在 app.js 文件中定义的中介件（见侧边栏“关于 Express 中介件”）。除了做其他事情外，默认的中介件会查找静态文件的路径。当中介件将路径与文件匹配时，Express 会异步返回该文件，确保 Node.js 进程不会被此操作占用，从而阻止其他操作。当请求通过所有中介件后，Express 会尝试将请求的路径与定义的路由匹配。我们将在 3.3.3 节（#ch03lev2sec12）中更详细地介绍这个主题。

图 3.4 展示了这个流程，使用的是来自图 3.3 的默认 Express 主页的示例。图 3.4 中的流程显示了分别发出的请求以及 Express 如何不同地处理它们。两个请求都作为第一个动作通过中介件，但结果却不同。

##### 图 3.4. Express 在响应默认着陆页请求时经历的关键交互和过程。HTML 页面由 Node 处理以编译数据和视图模板，CSS 文件则从静态文件夹异步提供。

![](img/03fig04_alt.jpg)

#### 3.2.6. 重启应用程序

Node 应用程序在运行前会进行编译，因此如果你在应用程序运行时修改了代码，这些更改将不会在 Node 进程停止并重新启动之前被捕获。请注意，这仅适用于应用程序代码；Jade 模板、CSS 文件和客户端 JavaScript 都可以在运行时更新。

重新启动 Node 进程是一个两步过程。首先，你必须通过按 Ctrl-C 在终端中停止正在运行的过程。然后，你必须使用之前的相同命令在终端中再次启动该过程：`DEBUG=loc8r:* npm start`。

这个过程听起来并不成问题，但当你积极开发和测试应用程序时，每次想要检查更新时都必须执行这两个步骤，这会变得相当令人沮丧。幸运的是，有一个更好的方法。

##### 使用 nodemon 自动重启应用程序

一些服务已被开发出来以监控应用程序代码，并在检测到更改时重启进程。其中一个这样的服务，也是你将在本书中使用的服务，是 nodemon。nodemon 包装 Node 应用程序，除了监控更改外，不会造成干扰。

要使用 nodemon，首先全局安装它，就像你安装 Express 一样。在终端中使用 npm：

```
$ npm install -g nodemon
```

安装完成后，你将能够在任何地方使用 nodemon。使用它很简单。你不需要输入 `node` 来启动应用程序，而是输入 `nodemon`。所以，确保你在终端中位于 loc8r 文件夹中，并且已经停止了 Node 进程（如果它仍在运行），然后输入以下命令：

```
$ nodemon
```

你应该在终端中看到几行额外的输出，确认 nodemon 正在运行，并且它已经启动了 `node ./bin/www`。如果你回到浏览器并刷新，你应该看到应用程序仍然在那里。


##### 注意

nodemon 仅用于简化开发环境中的开发过程，不应在实时生产环境中使用。像 pm2 或 foreman 这样的项目是为生产使用而设计的。


##### 使用提供的 Docker 环境

每一章都附带一个 Dockerfile 设置。前往附录 B 查看如何安装和使用 Docker 容器。你不必使用 Docker 就能从这本书中受益；它已被添加为便利功能。

### 3.3\. 修改 Express 以支持 MVC

首先，什么是 MVC 架构？MVC 架构将数据（模型）、显示（视图）和应用程序逻辑（控制器）分开。这种分离旨在消除组件之间的紧密耦合，从理论上讲，使代码更易于维护和重用。一个额外的好处是，这些组件非常适合你的快速原型开发方法，并允许你一次专注于一个方面，正如我们讨论 MEAN 栈的每个部分时那样。

整本书都是关于 MVC 的细微差别，但在这里我们不会深入探讨。我们将保持对 MVC 的讨论在较高层次，并展示如何使用 Express 来构建你的 Loc8r 应用程序。

#### 3.3.1\. MVC 的鸟瞰图

你构建的大多数应用程序或网站都是设计用来接收传入的请求，对其进行处理，并返回响应。在简单的层面上，MVC 架构中的这个循环是这样工作的：

1.  一个请求进入应用程序。

1.  请求被路由到控制器。

1.  如果需要，控制器会向模型发出请求。

1.  模型响应控制器的请求。

1.  控制器将视图和数据合并以形成响应。

1.  控制器将生成的响应发送给原始请求者。

实际上，根据您的配置，控制器可能会在向访客发送响应之前编译视图。效果是相同的，所以请记住这个简单的流程，作为您 Loc8r 应用程序中将要发生的事情的视觉参考。参见 图 3.5 以了解这个循环的说明。

##### 图 3.5\. 基本 MVC 架构的请求-响应流程

![](img/03fig05_alt.jpg)

图 3.5 强调了 MVC 架构的各个部分，并展示了它们是如何相互连接的。它还说明了需要一个路由机制，以及模型、视图和控制器组件。

现在您已经看到了您希望 Loc8r 应用程序的基本流程如何工作，是时候修改 Express 设置以使其发生。

#### 3.3.2\. 修改文件夹结构

如果您查看位于 loc8r 文件夹中的新创建的 Express 项目内部，您应该会看到一个包括视图文件夹甚至路由文件夹的文件结构，但没有提到模型或控制器。与其在应用程序的根级别添加一些新文件夹而使事情变得混乱，不如通过创建一个新文件夹来整理所有 MVC 架构。遵循以下三个快速步骤：

1.  创建一个名为 app_server 的新文件夹。

1.  在 app_server 中，创建两个新的文件夹，分别命名为 models 和 controllers。

1.  将视图和路由文件夹从应用程序的根目录移动到 app_server 文件夹。

图 3.6 阐述了这些更改，并显示了修改前后的文件夹结构。

##### 图 3.6\. 将 Express 项目的文件夹结构修改为 MVC 架构

![](img/03fig06_alt.jpg)

现在您在应用程序中有一个明显的 MVC 设置，这使得分离您的关注点更容易。但如果你现在尝试运行应用程序，它将不会工作，因为您刚刚破坏了它。所以修复它。Express 不了解您已经添加了一些新文件夹或对它们有何用途有任何想法，所以您需要告诉它。

#### 3.3.3\. 使用重新定位的视图和路由文件夹

您需要做的第一件事是告诉 Express 您已经移动了视图和路由文件夹，因为 Express 将会在它们旧的位置寻找它们。

##### 使用新的视图文件夹位置

Express 将会寻找 /views，但它需要寻找 /app_server/views。修改路径很简单。在 app.js 中，找到以下行：

```
app.set('views', path.join(__dirname, 'views'));
```

修改如下（加粗部分）：

```
app.set('views', path.join(__dirname, 'app_server', 'views'));
```

您的应用程序仍然无法工作，因为您已经移动了路由，所以您也需要告诉 Express 关于它们。

##### 使用新的路由文件夹位置

Express 将会寻找 /routes，但它需要寻找 /app_server/routes。修改这个路径也很简单。在 app.js 中，找到以下行：

```
const indexRouter = require('./routes/index');
const usersRouter = require('./routes/users');
```

将这些行更改为以下内容（加粗部分为修改）：

```
const indexRouter = require('./app_server/routes/index');
const usersRouter = require('./app_server/routes/users');
```


**在 ES2015 中定义变量**

ES2015 中最基本的变化之一是废弃了 `var` 关键字来定义变量。它仍然有效，但你应该使用这两个新关键字之一：`const` 和 `let`。使用 `const` 定义的变量在代码的后续部分*不能*被更改，而使用 `let` 定义的变量*可以*被更改。

最佳实践是使用 `const` 来定义变量，除非它们的值将要改变。app.js 中的所有 `var` 实例都可以更改为 `const`。我们已经在本书的源代码中这样做过；你也可以这样做。

另有一点需要记住的是，`const` 和 `let` 是块级变量初始化器，而 `var` 是上下文级变量初始化器。如果这些术语对你来说毫无意义，请阅读附录 D，该附录可在电子书或从 manning.com 在线获取。


注意，你还将 `var` 更改为 `const` 以升级到 ES2015。如果你对这一概念感到陌生，请查看侧边栏“在 ES2015 中定义变量”。如果你保存更改并再次运行应用程序，你会发现它再次工作！

#### 3.3.4\. 从路由中分离控制器

在默认的 Express 设置中，控制器是路由的一部分，但你希望将它们分离出来。控制器应管理应用程序逻辑，而路由应将 URL 请求映射到控制器。

##### 理解路由定义

要了解路由是如何工作的，请查看用于提供默认 Express 主页的路由。在 app_server/routes 的 index.js 内，你应该看到以下代码片段：

```
/* GET homepage. */
router.get('/', function(req, res) {               ***1***
  res.render('index', { title: 'Express' });       ***2***
});
```

+   ***1*** **路由器查找 URL 的位置**

+   ***2*** **控制器内容，尽管目前非常基础**

在代码的 ***1*** 处你可以看到 `router.get('/')`。路由器内部检查映射到主页 URL 路径的 GET 请求，该路径是 `'/'`。运行代码 ***1*** 的匿名函数是控制器。这个基本示例没有任何应用程序代码。所以 ***1*** 和 ***2*** 是你想要在这里分离的部分。

不要直接将控制器代码放入控制器文件夹，而是首先在同一个文件中测试这种方法。为此，你可以将路由定义中的匿名函数定义为命名函数。然后，将此函数的名称作为回调传递到路由定义中。这两个步骤在下面的列表中都有，你可以将其放在 app_server/routes/index.js 内部。

##### 列表 3.2\. 将控制器代码从路由中移除：步骤 1

```
const homepageController = (req, res) => {      ***1***
  res.render('index', { title: 'Express' });    ***1***
};                                              ***1***
/* GET homepage. */
router.get('/', homepageController);            ***2***
```

+   ***1*** **为箭头函数命名**

+   ***2*** **在路由定义中将函数名称作为回调传递**

如果你现在刷新你的主页，它应该仍然像以前一样工作。你没有改变网站工作的任何方面——只是朝着分离关注点迈出一步。


**理解 res.render**

你将在第四章中更详细地了解这个主题，但`render`是 Express 函数，用于将视图模板编译为发送给浏览器的 HTML 响应。`render`方法接受视图模板的名称和一个 JavaScript 数据对象，如下所示：

![](img/p0069-01_alt.jpg)

注意，模板文件不需要文件扩展名后缀，所以 index.pug 可以引用为 index。你也不需要指定视图文件夹的路径，因为你已经在 Express 的主设置中这样做过了。


现在你已经清楚了解路由定义的工作原理，是时候将控制器代码放到合适的位置了。

##### 将控制器从路由文件中移出

在 Node 中，要引用外部文件中的代码，你需要在你的新文件中创建一个模块，然后在原始文件中`require`它。有关此过程的总体原则，请参阅侧边栏“创建和使用 Node 模块”。


**创建和使用 Node 模块**

将一些代码从 Node 文件中提取出来创建外部模块，幸运的是，这个过程很简单。本质上，你为你的代码创建一个新文件，选择你想要公开给原始文件的部分，然后在原始文件中`require`你的新文件。

在你的新模块文件中，你可以使用`module.exports`方法公开你想要的代码部分，如下所示：

```
module.exports = function () {
  console.log("This is exposed to the requester");
};
```

然后，你可以在你的主文件中这样`require`它：

```
require('./yourModule');
```

如果你希望你的模块公开具有单独命名的函数，你可以通过以下方式在你的新文件中定义它们：

```
module.exports.logThis = function (message){
  console.log(message);
};
```

更好的做法是在文件末尾定义一个命名函数并导出它。这让你可以在一个地方公开所有需要的函数，为你的未来（或后续的开发者）创建一个方便的列表。

```
const logThis = function (message) {
  console.log(message);
};
module.exports = {
 logThis
};
```

要在原始文件中引用此内容，你需要将你的模块分配给一个变量名，然后调用该方法。你可以在主文件中输入以下内容：

```
const yourModule = require('./yourModule');
yourModule.logThis("Hooray, it works!");
```

此代码将你的新模块分配给变量`yourModule`。导出的函数`logThis`现在作为`yourModule`的方法可用。

注意，当使用`require`函数时，你不需要指定文件扩展名。`require`函数会查找几件事情：一个 npm 模块、同名的 JavaScript 文件，或者给定名称文件夹内的 index.js 文件。


首件事是创建一个文件来保存控制器代码。在 app_server/controllers 中创建一个名为 main.js 的新文件。在这个文件中，创建并导出一个名为`index`的方法，并使用它来存放`res.render`代码，如下所示。

##### 列表 3.3\. 在 app_server/controllers/main.js 中设置主页控制器

```
/* GET homepage */
const index = (req, res) => {                    ***1***
  res.render('index', { title: 'Express' });     ***2***
};
module.exports = {                               ***3***
 index                                           ***3***
};                                               ***3***
```

+   ***1*** **创建一个 index 函数**

+   ***2*** **包含主页的控制器代码**

+   ***3*** **公开 index 函数作为方法**

导出控制器就这些了。下一步是在路由文件中`require`这个控制器模块，这样你就可以在路由定义中使用暴露的方法。以下列表显示了`app_server/routes`中的 index.js 文件应该看起来像什么。

##### 列表 3.4\. 更新路由文件以使用外部控制器

```
const express = require('express');
const router = express.Router();
const ctrlMain = require('../controllers/main');    ***1***
/* GET homepage. */
router.get('/', ctrlMain.index);                    ***2***
module.exports = router;
```

+   ***1*** **require 主控制器文件**

+   ***2*** **在路由定义中引用控制器的 index 方法**

这段代码通过“require”控制器文件***1***并在`router.get`函数的第二参数中引用控制器函数***2***来将路由链接到新的控制器。

现在你已经有了路由和控制器架构，如图 3.7 所示，其中 app.js `require`s routes/index.js，而 routes/index.js 又`require`s controllers/main.js。如果你现在在浏览器中测试，你应该会看到默认的 Express 主页再次正确显示。

##### 图 3.7\. 将控制器逻辑与路由定义分离

![](img/03fig07_alt.jpg)

目前一切都已经用 Express 设置好了，所以差不多是时候开始构建过程了。但是你还需要做几件事情。第一件事是将 Twitter Bootstrap 添加到应用程序中。

### 3.4\. 导入 Bootstrap 以实现快速、响应式布局

如第一章中所述，你的 Loc8r 应用程序使用 Twitter 的 Bootstrap 框架来加速响应式设计的开发。你还将通过添加一些字体图标和自定义样式来使应用程序脱颖而出。目的是帮助你快速构建应用程序，而不会因为开发响应式界面的语义而分心。

#### 3.4.1\. 下载 Bootstrap 并将其添加到应用程序中

有关下载 Bootstrap、下载字体图标（通过 Font Awesome）、创建自定义样式以及将文件添加到项目文件夹的说明请见附录 B。请注意，你使用的是 Bootstrap 4.1。一个关键点是下载的文件都是直接发送到浏览器的静态文件；它们不需要 Node 引擎的任何处理。你的 Express 应用程序已经有一个用于此目的的文件夹：public 文件夹。当你准备好时，public 文件夹应该看起来像图 3.8。

##### 图 3.8\. 添加 Bootstrap 后 Express 应用程序中 public 文件夹的结构

![](img/03fig08.jpg)

Bootstrap 还需要 jQuery 和 Popper.js 来使一些交互组件正常工作。因为它们不是你应用程序的核心，所以你将在下一步从内容分发网络（CDN）中引用它们。

#### 3.4.2\. 在应用程序中使用 Bootstrap

现在所有的 Bootstrap 零件都放在了应用程序中，是时候将其连接到前端了，这意味着查看 Pug 模板。

##### 使用 Pug 模板

Pug 模板通常有一个主布局文件，该文件为其他 Pug 文件定义了扩展区域。当你构建一个网络应用程序时，这非常有意义，因为许多屏幕或页面具有相同的底层结构，但顶部内容不同。

这是 Pug 在默认 Express 安装中的样子：如果你查看应用程序中的 views 文件夹，你会看到三个文件——layout.pug、index.pug 和 error.pug。index.pug 文件控制着应用程序索引页的内容。打开它，你会看到里面没有多少内容。整个内容在下面的列表中显示。

##### 列表 3.5\. 完整的 index.pug 文件

```
extends layout                  ***1***
block content                   ***2***
  h1= title                     ***3***
  p Welcome to #{title.}        ***3***
```

+   ***1*** **声明此文件正在扩展布局文件**

+   ***2*** **声明以下部分将进入布局文件中的 content 区域**

+   ***3*** **输出 h1 和 p 标签到内容区域**

这里发生的事情比表面看起来要多。在文件顶部有一个声明，表明此文件是另一个文件的扩展 ***1***——在这种情况下，是布局文件。接下来是一个定义属于布局文件特定区域（称为 `content` 的区域）的代码块的声明 ***2***。最后，是 Express 索引页上显示的最小内容：一个 `<h1>` 标签和一个 `<p>` 标签 ***3***。

这里没有对 `<head>` 或 `<body>` 标签的引用，也没有任何样式表引用。这些都在布局文件中处理，所以你想要去的地方是添加全局脚本和样式表到应用程序中。打开 layout.pug，你应该会看到类似于以下列表的内容。

##### 列表 3.6\. 默认布局.pug 文件

```
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content                       ***1***
```

+   ***1*** **空命名块可以被其他模板使用**

这显示了用于默认 Express 安装的基本索引页的布局文件。有一个 `head` 部分和一个 `body` 部分，在 `body` 部分内部有一个没有任何内容的 `block content` 行。这个名为 `block` 的内容可以被其他 Pug 模板引用，例如 列表 3.5 中的 index.pug 文件。当视图被编译时，index 文件中的 `block content` 被推入布局文件的 `block content` 区域。

##### 将 Bootstrap 添加到整个应用程序中

如果你想将一些外部引用文件添加到整个应用程序中，在当前设置中使用布局文件是有意义的。在 layout.pug 中，你需要完成以下四件事：

+   引用 Bootstrap 和 Font Awesome CSS 文件。

+   引用 Bootstrap JavaScript 文件。

+   引用 jQuery 和 Popper.js，这是 Bootstrap 所需要的。

+   添加视口元数据，以便页面在移动设备上良好缩放。

CSS 文件和视口元数据都应该在文档的 `head` 部分，而两个脚本文件应该在 `body` 部分的末尾。以下列表显示了 layout.pug 中的所有这些内容，新行以粗体显示。

##### 列表 3.7\. 包含 Bootstrap 引用的更新布局.pug 文件

```
doctype html
html
  head
    meta(name='viewport', content='width=device-width,
      initial-scale=1.0')                                               ***1***
    title= title
    link(rel='stylesheet',  href='/stylesheets/bootstrap.min.css')        ***2***
    link(rel='stylesheet', href='/stylesheets/all.min.css')               ***2***
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
    script(src='https://code.jquery.com/jquery-3.3.1.slim.min.js',        ***3***
    integrity='sha384-                                                  ***3***
    q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo',  ***3***
    crossorigin='anonymous')                                            ***3***
    script(src='https://cdnjs.cloudflare.com/ajax/libs/                   ***3***
    popper.js/1.14.3/umd/popper.min.js',integrity='sha384-              ***3***
    ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49',  ***3***
    crossorigin='anonymous')                                              ***3***
    script(src='/javascripts/bootstrap.min.js')                           ***4***
```

+   ***1*** **设置视口元数据，以在移动设备上更好地显示**

+   ***2*** **包含 Bootstrap 和 Font Awesome CSS**

+   ***3*** **引入 jQuery 和 Popper，Bootstrap 所需。确保所有的脚本标签都有相同的缩进。**

+   ***4*** **引入 Bootstrap JavaScript 文件**

完成这些后，你创建的任何新模板都会自动包含 Bootstrap，并且可以在移动设备上缩放——当然，前提是你的新模板扩展了布局模板。如果你在这个阶段遇到任何问题或意外结果，请记住 Pug 对缩进、间距和换行很敏感。所有缩进都必须使用空格来完成，以在 HTML 输出中获得正确的嵌套。


##### 小贴士

如果你按照附录 B 中的说明进行操作，你也会在 /public/stylesheets 目录下的 style.css 文件中找到一些自定义样式，以防止默认 Express 样式覆盖 Bootstrap 文件并帮助你获得你想要的样式。


现在你已经准备好测试了。

##### 验证其是否工作

如果应用程序还没有用 nodemon 运行，请启动它，并在浏览器中查看。内容没有变化，但外观应该有所改变。你应该有一个看起来像图 3.9 的东西。

##### 图 3.9. Bootstrap 和你的样式对默认 Express 索引页的影响

![](img/03fig09_alt.jpg)

如果你的样子不像这样，请确保你已经按照附录 B 中概述的方式添加了自定义样式。记住，你可以从 GitHub 上的 chapter-03 分支获取到目前为止的应用程序源代码。在终端的新文件夹中，使用以下命令克隆它：

```
$ git clone -b chapter-03 https://github.com/cliveharber/
    gettingMean-2.git
```

现在你已经在本地有了些东西在运行。在下一节中，你将了解如何将其部署到实时生产服务器。

### 3.5. 将其部署到 Heroku

Node 应用程序的一个常见问题是将它们部署到实时生产服务器。你将尽早解决这个问题，并将 Loc8r 应用程序直接推送到实时 URL。随着你迭代和构建它，你可以继续推送更新。对于原型设计，这种方法很棒，因为它使得向他人展示你的进度变得容易。

如第一章中所述，有一些 PaaS 提供商，如 Google Cloud Platform、Nodejitsu、OpenShift 和 Heroku。这里你将使用 Heroku，但没有任何阻止你尝试其他选项的理由。接下来，你将启动 Heroku 并运行它，然后通过几个基本的 Git 命令将你的应用程序部署到实时服务器。

#### 3.5.1. 设置 Heroku 的准备工作

在您可以使用 Heroku 之前，您需要注册一个免费账户并在您的开发机器上安装 Heroku CLI。附录 B 提供了更多关于如何进行此操作的信息。您还需要一个兼容 bash 的终端；Mac 用户的默认终端是好的，但 Windows 用户的默认 CLI 不行。如果您使用 Windows，您需要下载类似 GitHub 终端的东西，它是 GitHub 桌面应用程序的一部分。当您设置好一切后，您可以继续准备将应用程序推送到线上。

##### 更新 package.json

Heroku 可以在各种类型的代码库上运行应用程序，因此您需要告诉它您的应用程序正在运行什么。除了告诉它您正在使用 npm 作为包管理器运行 Node 应用程序之外，您还需要告诉它您正在运行哪个版本，以确保生产环境设置与开发环境设置相同。

如果您不确定您正在运行哪个版本的 Node 和 npm，您可以使用几个终端命令来找出：

```
$ node --version
$ npm --version
```

目前，这些命令分别返回`v11.0.0`和`6.4.1`。使用`~`语法添加一个用于次要版本的通配符，如您之前所见，您需要将这些添加到 package.json 文件中的新 engines 部分。完整的更新后的 package.json 文件如下所示，其中添加的部分用粗体表示。

##### 列表 3.8\. 在 package.json 中添加 engines 部分

```
{
  "name": "Loc8r",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "engines": {                    ***1***
    "node": ">=11.0.0",           ***1***
    "npm": ">=6.4.0"              ***1***
  },                              ***1***
  "dependencies": {
    "body-parser": "~1.18.3",
    "cookie-parser": "~1.4.3",
    "debug": "~3.1.0",
    "express": "~4.16.3",
    "morgan": "~1.9.0",
    "pug": "~2.0.0-beta11",
    "serve-favicon": "~2.5.0"
  }
}
```

+   ***1*** **在 package.json 中添加 engines 部分，以告诉 Heroku 您的应用程序所在的平台以及要使用的版本**

当代码被推送到 Heroku 时，这段代码会告诉 Heroku 您的应用程序使用 Node 11 的最新次要版本和 npm 6 的最新次要版本。

##### 创建 Procfile

package.json 文件告诉 Heroku 该应用程序是一个 Node 应用程序，但没有告诉它如何启动它。对于这个任务，您需要一个 Procfile，它用于声明应用程序使用的进程类型以及启动它们的命令。

对于 Loc8r，您需要一个 Web 进程，并且希望它运行 Node 应用程序。在应用程序的根目录中创建一个名为 Procfile 的文件。（文件名区分大小写，没有文件扩展名。）在 Procfile 中输入以下行：

```
web: npm start
```

当代码被推送到 Heroku 时，此文件会告诉 Heroku 应用程序需要一个 Web 进程，并且应该运行`npm start`。

##### 使用 Heroku Local 进行本地测试

Heroku CLI 附带一个名为 Heroku Local 的实用工具。您可以使用此实用工具验证您的设置，在将应用程序推送到 Heroku 之前在本地运行您的应用程序。如果应用程序当前正在运行，请按终端窗口中运行进程的 Ctrl-C 停止它。然后，在终端窗口中，确保您位于应用程序文件夹中，并输入以下命令：

```
$ heroku local
```

如果设置一切顺利，这个命令将再次在 localhost 上启动应用程序，但这次是在不同的端口：5000。你在终端中获得的确认信息应该是这样的：

```
16:09:02 web.1 | > loc8r@0.0.1 start /path/to/your/application/folder
16:09:02 web.1 | > node ./bin/www
```

你可能还会看到警告“未找到 ENV 文件”。在这个阶段，这条消息没有什么好担心的。如果你打开浏览器并访问 localhost:5000（注意端口号是 5000 而不是 3000），你应该会看到应用程序再次运行起来。

现在你已经知道设置是有效的，是时候将你的应用程序推送到 Heroku 了。

#### 3.5.2\. 使用 Git 将网站上线

Heroku 使用 Git 作为部署方法。如果你已经使用 Git，你会喜欢这种方法；如果你还没有，你可能会对它感到有点不安，因为 Git 的世界可能很复杂。但事实并非如此，当你开始使用时，你也会喜欢这种方法！

##### 在 Git 中存储应用程序

第一步是在你的本地机器上使用 Git 存储应用程序。这个过程涉及以下三个步骤：

1.  将应用程序文件夹初始化为 Git 仓库。

1.  告诉 Git 你想要添加到仓库中的文件。

1.  将这些更改提交到仓库中。

这个过程听起来可能很复杂，但实际上并不复杂。你需要为每个步骤输入一个简单、短的终端命令。如果应用程序正在本地运行，请在终端中停止它（Ctrl-C）。然后，确保你仍然位于应用程序的根目录中，保持在终端中，并运行以下命令：

```
$ git init                         ***1***
$ git add --all                    ***2***
$ git commit -m "First commit"     ***3***
```

+   ***1*** **初始化文件夹为本地 Git 仓库**

+   ***2*** **将文件夹中的所有内容添加到仓库中**

+   ***3*** **提交更改到仓库并附上消息**

这三件事一起创建了一个包含应用程序整个代码库的本地 Git 仓库。当你稍后更新应用程序并想要将一些更改实时推送到线上时，你将使用后两个命令，并附上不同的消息来更新仓库。你的本地仓库已经准备好了。现在是时候创建 Heroku 应用程序了。

##### 创建 Heroku 应用程序

此下一步是在 Heroku 上创建一个本地仓库的远程 Git 仓库的应用程序。你可以通过单个终端命令完成所有这些操作：

```
$ heroku create
```

你将在终端中看到一个确认信息，包括应用程序所在的 URL、Git 仓库地址和远程仓库的名称，如下例所示：

```
https://pure-temple-67771.herokuapp.com/ | git@heroku.com:pure-temple-
     67771.git
Git remote heroku added
```

如果你通过浏览器登录到你的 Heroku 账户，你也会看到应用程序在那里。现在你已经在 Heroku 上为应用程序找到了一个位置，下一步是将应用程序代码推送到那里。

##### 将应用程序部署到 Heroku

你已经将应用程序存储在本地 Git 仓库中，并在 Heroku 上创建了一个新的远程仓库。远程仓库是空的，因此你需要将本地仓库的内容推送到 `heroku` 远程仓库。

如果你不知道 Git，有一个用于此目的的单个命令，其结构如下：

![图片](img/p0079-01.jpg)

此命令将您本地 Git 仓库的内容推送到 `heroku` 远程仓库。目前，您的仓库中只有一个分支——主分支——所以您将推送到 Heroku。有关 Git 分支的更多信息，请参阅侧边栏“什么是 Git 分支？”。

当您运行此命令时，终端会显示大量日志消息，在处理过程中最终会显示（从末尾大约五行）一个确认信息，表明应用程序已部署到 Heroku。这个确认信息类似于以下内容，但当然，您会有一个不同的 URL：

```
http://pure-temple-67771.herokuapp.com deployed to Heroku
```

| |
| --- |

**什么是 Git 分支？**

如果您在相同的代码版本上工作，并定期将其推送到像 Heroku 或 GitHub 这样的远程仓库，您正在 *master* 分支上工作。这个过程对于只有一个开发者的线性开发来说绝对是可以的。但是，如果您有多个开发者，或者您的应用程序已经发布，您不希望在主分支上进行开发。相反，您可以从主代码中启动一个新的分支，在那里您可以继续开发、添加修复或构建新功能。当分支上的工作完成后，它可以合并回主分支。

| |
| --- |

##### 关于 Heroku 上的 web dynos

Heroku 使用 *dynos* 的概念来运行和扩展应用程序。您拥有的 dynos 越多，您为应用程序可用的系统资源和进程就越多。当您的应用程序变得更大、更受欢迎时，添加更多 dynos 是很容易的。

Heroku 还有一个出色的免费层，非常适合应用程序原型设计和构建概念验证。每个应用程序您都免费获得一个 web dyno，这对于您在这里的目的来说已经足够了。如果您有需要更多资源的应用程序，您始终可以登录您的账户并支付更多。

在下一节中，您将检查实时 URL。

##### 在实时 URL 上查看应用程序

一切准备就绪，应用程序已经在互联网上上线了！您可以通过在确认信息中输入给您的 URL、通过 Heroku 网站的您的账户，或者使用以下终端命令来查看它：

```
$ heroku open
```

此命令将在您的默认浏览器中启动应用程序，您应该会看到类似于 图 3.10 的内容。

##### 图 3.10\. 在实时 URL 上运行的 MVC Express 应用程序

![图片 3.10](img/03fig10_alt.jpg)

您的 URL 当然会不同，在 Heroku 中，您可以将它更改为使用您的域名而不是它提供的地址。在 Heroku 网站的“应用程序设置”中，您可以将它更改为更有意义的子域名 herokuapp.com。

将原型放在可访问的 URL 上对于跨浏览器和跨设备测试很有用，以及用于发送给同事和合作伙伴。

##### 简单的更新过程

现在 Heroku 应用程序已经设置好了，更新它很容易。每次您想要通过推送一些新更改时，您都需要三个终端命令：

```
$ git add --all                            ***1***
$ git commit -m "Commit message here"      ***2***
$ git push heroku master                   ***3***
```

+   ***1*** **将所有更改添加到本地 Git 仓库**

+   ***2*** **使用有用的消息提交更改到本地仓库**

+   ***3*** **将更改推送到 Heroku 仓库**

至少目前就是这样。如果你要处理多个开发人员和分支，事情可能会变得稍微复杂一些，但使用 Git 将代码推送到 Heroku 的过程保持不变。

在 第四章 中，当你构建 Loc8r 应用程序的原型时，你会更深入地了解 Express。

### 概述

在本章中，你学习了

+   如何创建一个新的 Express 应用程序

+   如何使用 npm 和 package.json 文件管理应用程序依赖

+   如何将标准的 Express 项目更改为满足 MVC 架构方法

+   路由和控制器如何配合

+   使用 Git 将 Express 应用程序以最简单的方式实时发布到 Heroku

## 第四章\. 使用 Node 和 Express 构建静态网站

*本章涵盖*

+   通过构建静态版本来原型化应用程序

+   定义应用程序 URL 的路由

+   使用 Pug 和 Bootstrap 在 Express 中创建视图

+   在 Express 中使用控制器将路由绑定到视图

+   从控制器传递数据到视图

到 第三章 的结尾，你应该已经有一个运行中的 Express 应用程序，以 MVC 方式设置，Bootstrap 已包含在内，有助于构建页面布局。你的下一步是在这个基础上构建，创建一个可以点击的静态网站。这一步对于构建任何网站或应用程序至关重要。即使你已经得到了一些设计或线框来工作，也没有什么可以替代快速创建一个可以在浏览器中使用的真实原型。在布局或可用性方面，总会有一些你没有注意到的细节。从这个静态原型中，你将从视图中提取数据并将其放入控制器中。到本章结束时，你将拥有智能视图，可以显示传递给它们的 数据，并且控制器将硬编码的数据传递到视图中。


**获取源代码**

如果你还没有从第三章构建应用程序，你可以从 GitHub 上的 chapter-03 分支获取代码，网址为 [`github.com/cliveharber/gettingMean-2`](https://github.com/cliveharber/gettingMean-2)。在终端的新文件夹中，输入以下命令以克隆它并安装 npm 模块依赖项：

```
$ git clone -b chapter-03 https://github.com/cliveharber/
gettingMean-2.git
$ cd gettingMean-2
$ npm install
```


在构建应用程序架构方面，本章重点介绍了如图 4.1 所示的 Express 应用程序。

##### 图 4.1\. 使用 Express 和 Node 构建用于测试视图的静态网站

![](img/04fig01_alt.jpg)

本章完成了两个主要步骤，因此提供了两种源代码版本。第一个版本包含所有视图中的数据，代表了在 4.4 节结束时的应用程序状态。此代码可在 GitHub 上的 chapter-04-views 分支找到。

第二个版本具有控制器中的数据，代表了在章节结束时的应用程序状态。此代码可在 GitHub 上的 chapter-04 分支找到。

要获取这些版本之一，请在终端的新文件夹中使用以下命令，记得指定你想要的分支：

```
$ git clone -b chapter-04 https://github.com/cliveharber/gettingMean-2.git
$ cd gettingMean2
$ npm install
```

如果你想要运行 Docker 环境，请参阅附录 B。现在你准备好回到 Express 了。

### 4.1\. 在 Express 中定义路由

在第二章中，你规划了应用程序并确定了你要构建的四页内容。你有一系列位置页面和“其他”集合中的一个页面，如图 4.2 所示。

##### 图 4.2\. 你将为 Loc8r 应用程序构建的屏幕集合

![](img/04fig02_alt.jpg)

拥有一组屏幕很棒，但这些屏幕需要与传入的 URL 相关联。在你开始任何编码之前，制定屏幕和 URL 之间的链接图，并建立一个良好的标准是个好主意。看看表 4.1，它展示了屏幕与 URL 的简单映射。这些映射将构成你应用程序路由的基础。

##### 表 4.1\. 为原型中每个屏幕定义 URL 路径或路由

| 集合 | 屏幕 | URL 路径 |
| --- | --- | --- |
| 位置 | 位置列表（主页） | / |
| 位置 | 位置详情 | /location |
| 位置 | 位置评论表单 | /location/review/new |
| 其他 | 关于 Loc8r | /about |

当有人访问主页时，例如，你希望向他们展示地点列表，但当有人访问/about URL 路径时，你希望向他们展示 Loc8r 的信息。

#### 4.1.1\. 不同集合的控制器文件

在第三章中，你将控制器逻辑从路由定义中移出，放入外部文件。展望未来，你知道你的应用程序将会增长，你不想所有控制器都在一个文件中。将它们分开的逻辑起点是按集合划分它们。

看看你已决定采用的集合，你决定将控制器分为“位置”和“其他”。为了从文件架构的角度了解这种方法可能如何工作，你可以绘制类似图 4.3 的东西。在这里，应用程序包括路由文件，该文件反过来又包括多个控制器文件，每个文件都根据相关的集合命名。

##### 图 4.3\. 应用程序中路由和控制器建议的文件架构

![](img/04fig03_alt.jpg)

你有一个单独的路由文件，以及每个逻辑屏幕集合的一个控制器文件。这种设置旨在帮助你根据应用程序的组织方式来组织代码。你将很快查看控制器，但首先，你需要处理路由。

规划的时间已经结束；现在是采取行动的时候了！回到你的开发环境并打开应用程序。你将从在 routes 文件 index.js 中工作开始。

##### 引入控制器文件

如图 4.3 所示，你想要在这个路由文件中引用两个控制器文件。你还没有创建这些控制器文件；你将在稍后完成这项任务。

这些文件将被命名为 locations.js 和 others.js。它们将被保存在 app_server/controllers 目录中。在 index.js 中，你需要`require`这两个文件并将它们分别分配给相关的变量名，如下面的列表所示。

##### 列表 4.1\. 在 app_server/routes/index.js 中引入控制器文件

```
const express = require('express');
const router = express.Router();
const ctrlLocations = require('../controllers/locations');     ***1***
const ctrlOthers = require('../controllers/others');           ***1***
```

+   ***1*** **用两个新的 require 替换现有的 ctrlMain 引用**

现在你有两个可以在路由定义中引用的变量，它们包含不同的路由集合。

##### 设置路由

在 index.js 中，你需要为 Locations 集合中的三个屏幕和 Others 集合中的关于页面设置路由。每个路由还需要一个控制器引用。记住，路由充当映射服务，将传入请求的 URL 映射到特定的应用程序功能。

从表 4.1 中，你已经知道你想要映射哪些路径，所以你需要将所有内容组合到 routes/index.js 文件中。文件中需要的内容在下面的列表中完整显示。

##### 列表 4.2\. 定义路由并将它们映射到控制器

```
const express = require('express');
const router = express.Router();
const ctrlLocations = require('../controllers/locations');       ***1***
const ctrlOthers = require('../controllers/others');             ***1***

/* Locations pages */
router.get('/', ctrlLocations.homelist);                         ***2***
router.get('/location', ctrlLocations.locationInfo);             ***2***
router.get('/location/review/new', ctrlLocations.addReview);     ***2***

/* Other pages */
router.get('/about', ctrlOthers.about);                          ***3***

module.exports = router;
```

+   ***1*** **引入控制器文件**

+   ***2*** **定义位置路由并将它们映射到控制器函数**

+   ***3*** **定义其他路由**

这个路由文件将定义的 URL 映射到一些特定的控制器，尽管你还没有创建它们。你将在下一节中处理这个任务。

### 4.2\. 构建基本控制器

在这一点上，你将保持控制器的基本性，以便你的应用程序可以运行，并且你可以测试 URL 和路由。

#### 4.2.1\. 设置控制器

你目前有一个文件：位于 app_server 文件夹中的 controllers 文件夹中的 main.js 文件，它包含一个控制主页的单个函数。这个函数在下面的代码片段中显示：

```
/* GET 'home' page */
const index = (req, res) => {
  res.render('index', { title: 'Express' });
};
```

你不再需要“主要”控制器文件，但你可以使用这个文件作为模板。首先，将此文件重命名为 others.js。

##### 添加其他控制器

从列表 4.2 回忆一下，你想要在 others.js 中有一个名为`about`的控制器。将现有的`index`控制器重命名为`about`；现在保持相同的视图模板；并将`title`属性更新为相关的内容。这种方法使得测试路由是否按预期工作变得容易。以下列表显示了经过这些小改动后 others.js 控制器文件的完整内容。

##### 列表 4.3\. 其他控制器文件

```
/* GET 'about' page */
const about = (req, res) => {                    ***1***
  res.render('index', { title: 'About' });       ***1***
};
module.exports = {
  about                                          ***2***
};
```

+   ***1*** **定义路由，使用相同的视图模板，但将标题改为关于**

+   ***2*** **更新导出以反映名称更改**

那是第一个完成的控制器，但应用程序仍然无法工作，因为没有为位置路由添加任何控制器。

##### 添加位置控制器

为位置路由添加控制器的过程基本上是相同的。在路由文件中，你指定了要查找的控制器文件名以及三个控制器函数的名称。

在控制器文件夹中，创建一个名为 locations.js 的文件，并创建和导出三个基本控制器函数：`homelist`、`locationInfo`和`addReview`。以下列表显示了该文件应该如何看起来。

##### 列表 4.4\. 位置控制器文件

```
/* GET 'home' page */
const homelist = (req, res) => {
  res.render('index', { title: 'Home' });
};

/* GET 'Location info' page */
const locationInfo = (req, res) => {
  res.render('index', { title: 'Location info' });
};

/* GET 'Add review' page */
const addReview = (req, res) => {
  res.render('index', { title: 'Add review' });
};

module.exports = {
  homelist,
  locationInfo,
  addReview
};
```

一切就绪，你现在可以开始测试了。

#### 4.2.2\. 测试控制器和路由

现在路由和基本控制器已经就位，你应该能够启动并运行应用程序。如果你还没有用 nodemon 启动它，请在终端中转到应用程序的根目录并启动它：

```
$ nodemon
```


**故障排除**

如果你在这个时候遇到重启应用程序的问题，主要需要检查的是所有文件、函数和引用的命名是否正确。查看终端窗口中的错误消息，看看是否提供了任何线索。一些消息比其他消息更有帮助。看看以下可能的错误，并挑选出对你有意义的部分：

```
module.js:340
    throw err;
          ^
Error: Cannot find module '../controllers/other'              ***1***
    at Function.Module._resolveFilename (module.js:338:15)
    at Function.Module._load (module.js:280:25)
    at Module.require (module.js:364:17)
    at require (module.js:380:17)
    at module.exports (/Users/sholmes/Dropbox/
     Manning/GettingMEAN/Code/Loc8r/
     BookCode/routes/index.js:2:3)                          ***2***
    at Object.<anonymous> (/Users/sholmes/Dropbox/
     Manning/GettingMEAN/Code/Loc8r/
     BookCode/app.js:26:20)
    at Module._compile (module.js:456:26)
    at Object.Module._extensions..js (module.js:474:10)
    at Module.load (module.js:356:32)
    at Function.Module._load (module.js:312:12)
```

+   ***1*** **线索 1：找不到模块。**

+   ***2*** **线索 2：发生了文件抛出错误。**

首先，你会看到无法找到名为`other`的模块 ***1***。在堆栈跟踪的更下方，你会看到错误起源的文件 ***2***。打开 routes/index.js 文件，你会发现你写了`require('../controllers/other')`，而你想要导入的文件是 others.js。为了解决这个问题，通过将其更改为`require('../controllers/others')`来纠正引用。


如果一切顺利，这次运行应该不会出现错误，这意味着路由已指向控制器。此时，您可以前往浏览器并检查您创建的四个路由，例如主页的 localhost:3000 和位置信息页的 localhost:3000/location。因为您已经更改了每个控制器发送到视图模板的数据，所以您可以轻松地看到每个都在正确运行——每个页面的标题和标题应该不同。图 4.4 显示了新创建的路由和控制器的截图集合。您可以看到每个路由都获得了独特的内容，因此您知道路由和控制器设置已经成功。

##### 图 4.4\. 到目前为止创建的四个路由的截图，其中包含来自与每个路由关联的特定控制器的不同标题文本

![图片](img/04fig04_alt.jpg)

在这个原型制作过程的下一阶段，是在每个屏幕上放置一些 HTML、布局和内容。您将通过使用视图来完成这项工作。

### 4.3\. 创建一些视图

当您已经整理好空页面、路径和路由后，是时候将一些内容和布局添加到您的应用程序中。这一步是将应用程序激活并开始看到您的想法变为现实的地方。为此步骤，您将使用的科技是 Pug 和 Bootstrap。Pug 是您在 Express 中使用的模板引擎（尽管如果您愿意，您也可以使用其他引擎），Bootstrap 是一个前端布局框架，它使得构建在不同桌面和移动设备上看起来不同的响应式网站变得容易。

#### 4.3.1\. Bootstrap 的概述

在开始之前，让我们快速了解一下 Bootstrap。我们不会深入探讨 Bootstrap 的所有细节以及它能做什么，但在您尝试将其放入模板文件之前，看到一些关键概念是有用的。

Bootstrap 使用 12 列网格。无论您使用的显示器的尺寸如何，总会存在这 12 列。在手机上，每列都很窄，而在大外部显示器上，每列都很宽。Bootstrap 的基本概念是您可以定义元素使用多少列，并且这个数字可以针对不同的屏幕尺寸不同。

Bootstrap 提供了各种 CSS 参考，允许您为布局设置多达五个不同像素宽度的断点。这些断点在表 4.2 中有说明，同时列出了每个尺寸针对的示例设备。

##### 表 4.2\. Bootstrap 为针对不同类型设备设置的断点

| 断点名称 | CSS 参考 | 示例设备 | 宽度 |
| --- | --- | --- | --- |
| 超小型设备 | (无) | 小型手机 | 少于 576 像素 |
| 小型设备 | sm | 智能手机 | 576 像素或以上 |
| 中型设备 | md | 平板电脑 | 768 像素或以上 |
| 大型设备 | lg | 笔记本电脑 | 992 像素或以上 |
| 超大设备 | xl | 外部显示器 | 1,200 像素或以上 |

要定义一个元素的宽度，你需要将来自表 4.2 的 CSS 引用与你想让它跨越的列数结合起来。一个表示列的类可以这样构建：

![图片](img/p0089-01.jpg)

这个`col-sm-6`类使得应用到的元素在`sm`尺寸及更大的屏幕上占据六个列。在平板电脑、笔记本电脑和显示器上，这个列将占据可用宽度的一半。

要使响应式功能正常工作，你可以将多个类应用到单个元素上。如果你想让一个`div`在手机上占据整个屏幕宽度，但在平板电脑和更大设备上只占据一半宽度，你可以使用以下代码片段：

```
<div class="col-12 col-md-6"></div>
```

`col-12`类告诉布局在超小设备上使用 12 列，而`col-md-6`类告诉布局在中型设备及更大设备上使用 6 列。图 4.5 展示了如果页面上有两个这样的类，一个接一个，它们在不同设备上的效果。

```
<div class="col-12 col-md-6">DIV ONE</div>
<div class="col-12 col-md-6">DIV TWO</div>
```

##### 图 4.5\. 在桌面设备和移动设备上 Bootstrap 的响应式列系统。CSS 类用于确定每个元素在不同屏幕分辨率下应占用的列数（12 列中的几列）。

![图片](img/04fig05_alt.jpg)

这种方法允许以语义化的方式组合响应式模板，你将大量依赖它来构建 Loc8r 页面。说到这一点，你将在下一节开始着手。

#### 4.3.2\. 使用 Pug 模板和 Bootstrap 设置 HTML 框架

应用程序中的页面有一些共同的要求。在每一页的顶部，你将需要一个导航栏和标志；在页面的底部，你将在页脚中有一个版权声明；中间将有一个内容区域。你追求的是类似于图 4.6 的东西。这个布局框架很简单，但能满足你的需求。它提供了统一的视觉和感觉，同时允许中间有不同布局。

##### 图 4.6\. 可重复使用的布局的基本结构，包括一个标准的导航栏和页脚，以及中间的可扩展、可更改的内容区域

![图片](img/04fig06.jpg)

正如你在第三章中看到的，Pug 模板使用可扩展布局的概念，使你能够在布局文件中定义这种可重复的结构一次。在布局文件中，你可以指定哪些部分可以扩展；当你设置了这种布局文件，你可以根据需要多次扩展它。在布局文件中创建框架意味着你只需做一次，你可以在一个地方维护它。

##### 查看布局

要构建通用框架，你主要会在`app_server/views`文件夹中的`layout.pug`文件上工作。这个文件很简单，看起来像以下代码片段：

```
doctype html
html
  head
    meta(name='viewport', content='width=device-width, initial-scale=1.0')
    title= title
    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
    link(rel='stylesheet', href='/stylesheets/all.min.css')
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
    script(src='https://code.jquery.com/jquery-3.3.1.slim.min.js',
      integrity='sha384
      q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo',
      crossorigin=anonymous)
    script(src=https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/
      umd/popper.min.js,
      integrity='sha384
      ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49',
      crossorigin='anonymous')
    script(src='/javascripts/bootstrap.min.js')
```

在主体区域还没有任何 HTML 内容——只有一个名为 `content` 的可扩展块和一些脚本引用。你想要保留所有这些，但在 `content` 块上方添加一个导航部分，在其下方添加一个页脚。

##### 构建导航

Bootstrap 提供了一系列元素和类，你可以使用它们来创建一个固定在顶部并可在移动设备上折叠选项的下拉菜单的粘性导航栏。我们在这里不会探讨 Bootstrap CSS 类的细节。你所需要做的就是从 Bootstrap 网站获取示例代码，稍作修改，并更新为正确的链接。

在导航中，你想要有

+   链接到主页的 Loc8r 标志

+   左侧的一个关于链接，指向 /about URL 页面

执行这些操作的代码在以下片段中，并且可以放置在 layout.pug 文件中 `block content` 行上方：

```
nav.navbar.fixed-top.navbar-expand-md.navbar-light                     ***1***
  .container
    a.navbar-brand(href='/') Loc8r                                     ***2***
      button.navbar-toggler(type='button', data-toggle='collapse',
      data-target='#navbarMain')                                     ***3***
        span.navbar-toggler-icon
     #navbarMain.navbar-collapse.collapse
    ul.navbar-nav.mr-auto
      li.nav-item
        a.nav-link(href='/about/') About                               ***4***
```

+   ***1*** **设置一个固定在窗口顶部的 Bootstrap 导航栏**

+   ***2*** **为主页添加一个品牌风格的链接**

+   ***3*** **为较小的屏幕分辨率设置可折叠的导航**

+   ***4*** **将关于链接添加到栏的左侧**

如果你将这段代码放入并运行，你会注意到导航现在覆盖了页面标题。当你构建内容区域的布局时，你将在第 4.3.3 和 4.4 节中修复这个问题，所以不用担心。


##### 提示

记住 Pug 不包含任何 HTML 标签，并且正确的缩进对于提供预期的结果至关重要。


这就是导航栏的全部内容，目前你只需要这些。如果你对 Pug 和 Bootstrap 还不熟悉，可能需要一点时间来习惯这种方法和语法，但正如你所看到的，你可以用很少的代码实现很多功能。

##### 包装内容

从页面顶部向下工作，下一个区域是 `content` 块。你不需要对这个块做太多，因为其他 Pug 文件决定了内容。然而，目前 `content` 块固定在左边缘，并且不受限制，这意味着它扩展到任何设备的全宽度。

通过 Bootstrap 解决这种情况很容易。仍然在 layout.pug 中，像这样将 `content` 块包裹在一个容器 `div` 中，同时确保缩进正确：

```
.container
  block content
```

具有类名为 `container` 的 `div` 在窗口中居中，并在大屏幕上限制在合理的最大宽度内。容器 `div` 的内容仍然按照常规向左对齐。

##### 添加页脚

在页面底部，你想要添加一个标准的页脚。你可以在这里添加一些链接，或者条款和条件，或者隐私政策。为了保持简单，你将添加一个版权声明。因为这个更改是在布局文件中进行的，所以如果你稍后需要更新这个通知，将很容易跨所有页面进行更新。

以下代码片段显示了在 layout.pug 中创建简单页脚所需的所有代码：

```
footer
  .row
    .col-12
      small &copy; Getting Mean - Simon Holmes/Clive Harber 2018
```

这段代码被放置在包含`content`块的容器`div`中，所以当你添加它时，确保`footer`行与`block content`行的缩进级别相同。

##### 一同完成

现在已经处理了导航栏、内容区域和页脚，你有了完整的布局文件。layout.pug 的完整代码如下所示。

##### 列表 4.5. app_server/views/layout.pug 中布局框架的最终代码

```
doctype html
html
  head
    meta(name='viewport', content='width=device-width, initial-scale=1.0')
    title= title
    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
    link(rel='stylesheet', href='/stylesheets/all.min.css')
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    nav.navbar.fixed-top.navbar-expand-md.navbar-light                    ***1***
      .container
        a.navbar-brand(href='/') Loc8r
          button.navbar-toggler(type='button', data-toggle='collapse',
          data-target='#navbarMain')
            span.navbar-toggler-icon
        #navbarMain.navbar-collapse.collapse
          ul.navbar-nav.mr-auto
            li.nav-item
              a.nav-link(href='/about/') About

    .container.content
      block content                                                       ***2***

      footer                                                              ***3***
        .row
          .col-12
            small &copy; Getting MEAN – Simon Holmes/Clive Harber 2018

    script(src='https://code.jquery.com/jquery-3.3.1.slim.min.js',
    integrity='sha384-
    q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo',
    crossorigin='anonymous')
    script(src='https://cdnjs.cloudflare.com/ajax/libs/
    popper.js/1.14.3/umd/popper.min.js' integrity='sha384- ZMP7rVo
    ]3mIykV+2+9J3UJ46jBk0WLaUAdn689a CwoqbBJiSnjAK/l8WvCWPIPm49',
    crossorigin='anonymous')
    script(src='/javascripts/bootstrap.min.js')
```

+   ***1*** **从固定导航栏开始布局**

+   ***2*** **可扩展的内容块现在被包裹在一个容器 div 中**

+   ***3*** **简单的版权页脚在内容块相同的容器中**

使用 Bootstrap、Pug 和 Express 创建响应式布局框架，只需做这些就足够了。如果你已经一切准备就绪，当你运行应用程序时，你应该能看到类似图 4.7 中的截图，这取决于你的设备。

##### 图 4.7. 布局模板设置后的主页。Bootstrap 自动折叠了手机小屏幕尺寸下的导航。导航栏覆盖了内容，但这个问题将在创建内容布局时得到解决。

![](img/04fig07_alt.jpg)

你会看到导航仍然覆盖了内容，但当你开始查看内容布局时，你会解决那个问题。这是一个很好的迹象，表明导航正在按你的意愿工作：你希望导航始终存在，固定在窗口顶部。同时请注意，Bootstrap 已经将导航折叠成下拉菜单，这是在你手机较小的屏幕上——这是你付出很少努力就能得到的好结果。


##### 小贴士

如果你无法在手机上访问你的开发网站，你可以尝试调整浏览器窗口的大小。所有主流的网页浏览器都允许你通过内置的开发者工具模拟各种移动设备和屏幕尺寸。


现在通用布局模板已经完成，是时候开始构建你应用程序的实际页面了。

#### 4.3.3. 构建模板

当你构建模板时，从对你最有意义的一个开始。这个模板可能是最复杂或最简单的，或者是主要用户旅程中的第一个。对于 Loc8r 来说，一个好的起点是主页，这是我们将会最详细地讨论的例子。

##### 定义布局

主页的主要目的是显示位置列表。每个位置都需要有名称、地址、距离用户的距离、用户评分和设施列表。你还需要添加一个页眉和一些文本，以便将列表置于上下文中，这样用户在访问时就知道他们在看什么。

你可能会觉得，像我们一样，在一张纸或白板上草拟一个或两个布局是有用的。我们发现这个草图对于创建布局的起点很有帮助，确保我们在页面上拥有所有需要的组件，而不会陷入代码的技术细节。![图 4.8](img/#ch04fig08)展示了你可能为 Loc8r 的首页草拟的内容。

##### 图 4.8. 首页的桌面和移动布局草图。为页面绘制布局可以快速了解你将要构建的内容，而不会因为 Adobe Photoshop 的复杂性或代码的技术细节而分心。

![Images/04fig08_alt.jpg]

你会看到有两种布局：一种用于桌面，另一种用于手机。在这个阶段，理解 Bootstrap 能做什么以及它是如何工作的，区分这两种布局是有意义的。这是开始思考响应式设计的起点。

在这个阶段，布局绝对不是最终的，你完全可以在构建代码的过程中调整和更改它们。但如果你有一个目的地和一张地图，旅程就会容易得多，草图为你提供了这些。你可以从正确的方向开始编写代码。创建草图所花费的几分钟可以节省你几个小时的时间，尤其是当你发现需要移动部分内容或甚至完全放弃并重新开始时。与一大堆代码相比，这个过程有了草图会容易得多。

现在你已经对布局和所需的内容组件有了概念，是时候将所有内容组合到一个新的模板中。

##### 设置视图和控制器

第一步是创建一个新的视图文件并将其链接到控制器。在 app_server/views 文件夹中，复制 index.pug 视图文件，并将其保存到与 locations-list.pug 相同的文件夹中。最好不要将文件命名为 homepage 或类似名称，因为某个时候，你可能会改变主意，不知道首页应该显示什么。这样，视图的名称可以清楚地标识它，并且可以在任何地方使用而不会引起混淆。

第二步是告诉首页的控制器你想要使用这个新的视图。首页的控制器位于 app_server/controllers 中的 locations.js 文件。更新此文件以更改由`homelist`控制器调用的视图，如下面的代码片段所示（加粗部分为修改）：

```
const homelist = (req, res) => {
  res.render('locations-list', { title: 'Home' });
};
```

现在，你已经准备好构建视图模板了。

##### 编写模板：页面布局

当我们编写布局的代码时，我们更喜欢从大的组件开始，然后逐步细化。随着布局文件的扩展，导航栏和页脚已经完成，但你仍然需要考虑页面标题、列表的主要区域和侧边栏。

在这个阶段，你需要尝试确定每个元素在不同设备上想要占用多少个 Bootstrap 列。以下代码片段显示了 locations-list.pug 中 Loc8r 列表页面的三个不同区域的布局：

```
.row.banner
  .col-12                                                               ***1***
    h1 Loc8r                                                            ***1***
      small &nbsp;Find places to work with wifi near you!               ***1***
.row
  .col-12.col-md-8                                                      ***2***
    p List area.                                                        ***2***
  .col-12.col-md-4                                                      ***3***
    p.lead Loc8r helps you find places to work when out and about.      ***3***
```

+   ***1*** **填充整个屏幕宽度的页面标题**

+   ***2*** **位置列表的容器，在超小号和小号设备上跨越所有 12 列，在中号设备和大号设备上跨越 8 列**

+   ***3*** **次要信息或侧边栏信息的容器，在超小号和小号设备上跨越所有 12 列，在中号设备和大号设备上跨越 4 列**

你可能需要来回测试几次，调整不同分辨率下的列，直到你对它们满意。拥有设备模拟器可以使这个过程更容易，但一个简单的方法是改变浏览器窗口的宽度，以强制不同的 Bootstrap 断点。当你认为某个布局可能合适时，你可以将其推送到 Heroku 上，并在你的手机或平板电脑上实际测试。

##### 编码模板：位置列表

现在主页的容器已经定义好了，接下来是主要区域。你可以从为页面布局绘制的草图中获得这里想要的内容的想法。每个地方都应该显示名称、地址、评分、用户距离以及关键设施。

因为你在创建一个可点击的原型，所以现在所有数据都将硬编码到模板中。这种方法是快速组合模板并确保你以你想要的方式显示所需信息的最快方式。你将在稍后处理数据方面的问题。如果你从一个现有的数据源工作或对可以使用的数据有约束，那么在创建布局时，你自然需要考虑这些事实。

再次强调，获得你满意的布局可能需要一些测试，但 Pug 和 Bootstrap 的结合使得这个过程比单独使用它们要容易得多。以下代码片段显示了你可以为单个位置提供的，以替换 locations-list.pug 中的 `p List area` 占位符：

```
.card                                                       ***1***
  .card-block                                               ***1***
    h4
      a(href="/location") Starcups                          ***2***
      small &nbsp;
        i.fas.fa-star                                       ***3***
        i.fas.fa-star                                       ***3***
        i.fas.fa-star                                       ***3***
        i.far.fa-star                                       ***3***
        i.far.fa-star                                       ***3***
      span.badge.badge-pill.badge-default.float-right 100m  ***4***
    p.address 125 High Street, Reading, RG6 1PS             ***5***
    .facilities
      span.badge.badge-warning Hot drinks                   ***6***
      span.badge.badge-warning Food                         ***6***
      span.badge.badge-warning Premium wifi                 ***6***
```

+   ***1*** **创建一个新的 Bootstrap 卡片和卡片块来包裹内容**

+   ***2*** **列表名称及其位置链接**

+   ***3*** **使用 Font Awesome 图标输出星级评分**

+   ***4*** **使用 Bootstrap 的徽章辅助类来显示距离**

+   ***5*** **位置的地址**

+   ***6*** **位置设施，使用 Bootstrap 的徽章类输出**

再次强调，你可以看到，通过相对较少的努力和代码，你可以实现多少。这都要归功于 Pug 和 Bootstrap 的结合。记住，一些用于美化的自定义类在 public/stylesheets 下的 styles.css 文件中，可在 GitHub 仓库中找到。没有这些类，你的视觉效果将大不相同。要了解前面的代码片段做了什么，请查看图 4.9。

##### 图 4.9\. 列表页面上单个位置的屏幕渲染

![](img/04fig09_alt.jpg)

此部分设置为跨越可用区域的全部宽度：所有设备上的 12 列。但请记住，尽管这个部分嵌套在响应式列中，所以“全宽”是包含列的全宽，而不是浏览器视口的宽度。当您将所有内容组合在一起并看到应用程序的实际运行时，这个解释将更有意义。

##### 编码模板：将其组合在一起

您已经有了页面元素布局、列表区域的结构和一些硬编码的数据，现在是时候看看一切看起来是什么样子了。为了更好地感受浏览器中的布局，复制并修改列表页面以显示多个位置是个好主意。以下列出的是包括单个位置以节省篇幅的代码。

##### 列表 4.6. app_server/views/locations-list.pug 的完整模板

```
extends layout

block content
  .row.banner                                                      ***1***
    .col-12
      h1 Loc8r
        small &nbsp;Find places to work with wifi near you!
  .row
    .col-12.col-md-8                                               ***2***
      .card                                                        ***3***
        .card-block                                                ***3***
          h4                                                       ***3***
            a(href="/location") Starcups                           ***3***
            small &nbsp;                                           ***3***
              i.fas.fa-star                                        ***3***
              i.fas.fa-star                                        ***3***
              i.fas.fa-star                                        ***3***
              i.far.fa-star                                        ***3***
              i.far.fa-star                                        ***3***
            span.badge.badge-pill.badge-default.float-right 100m   ***3***
          p.address 125 High Street, Reading, RG6 1PS              ***3***
          p.facilities                                             ***3***
            span.badge.badge-warning Hot drinks                    ***3***
            span.badge.badge-warning Food                          ***3***
            span.badge.badge-warning Premium wifi                  ***3***
    .col-12.col-md-4                                               ***4***
      p.lead Looking for wifi and a seat? Loc8r helps you find places to
      work when out and about. Perhaps with coffee, cake or a pint?
      Let Loc8r help you find the place you're looking for.
```

+   ***1*** **开始标题区域**

+   ***2*** **开始响应式主列表列部分**

+   ***3*** **单个列表；复制此部分以创建多个项目的列表**

+   ***4*** **设置侧边栏区域并填充一些内容**

当您将此代码放置到位后，您就完成了主页列表模板。如果您运行应用程序并转到 localhost:3000，您应该会看到类似于图 4.10 的内容。

##### 图 4.10. 在不同设备上运行的首页响应式模板

![图片](img/04fig10_alt.jpg)

您可以看到桌面视图和移动视图之间的布局如何变化吗？这种变化归功于 Bootstrap 的响应式框架和您选择的 CSS 类。在移动视图中向下滚动，您会在主列表和页脚之间看到侧边栏的文本内容。在较小的屏幕上，显示列表在可用空间中比显示文本更重要。

很好；您通过在 Express 和 Node 中使用 Pug 和 Bootstrap 创建了一个响应式布局的主页。接下来，您将添加其他视图。

### 4.4. 添加其余视图

位置列表页面已构建，因此您需要创建其他页面，以便用户可以点击浏览。在本节中，我们将介绍添加这些页面：

+   详情

+   添加评论

+   关于

尽管我们不会对所有这些过程进行详细说明，但只会提供一些解释、代码和输出。如果您更喜欢，可以始终从 GitHub 下载源代码。

#### 4.4.1. 详细信息页面

合理的步骤，并且可以说是查看下一个最重要的页面，即单个位置的详细信息页面。

此页面需要显示有关位置的所有信息，包括

+   名称

+   地址

+   评分

+   营业时间

+   设施

+   位置地图

+   评论，每个都有

    +   评分

    +   评论者姓名

    +   评论日期

    +   评论文本

    +   添加新评论的按钮

    +   设置页面上下文的文本

这相当多的信息！这个模板是您应用程序中最复杂的一个。

##### 准备

第一步是更新此页面的控制器以使用不同的视图。在 app_server/controllers 中的 locations.js 文件中查找`locationInfo`控制器。将视图的名称更改为`location-info`，如下代码片段所示：

```
const locationInfo = (req, res) => {
  res.render('location-info', { title: 'Location info' });
};
```

下一步是获取访问谷歌地图 API 的密钥。如果你还没有账户，你需要在该地址注册一个账户：

```
https://developers.google.com/maps/documentation/javascript/
     get-api-key?utm_source=geoblog&utm_medium=social&utm_campaign=
     2016-geo-na-website-gmedia-blogs-us-blogPost&utm_content=TBC
```

确保你保管好你的 API 密钥；你将在下一个列表中需要它。

记住，如果你在这个时候运行应用程序，它将无法工作，因为 Express 找不到视图模板——这并不奇怪，因为你还没有创建它。这就是下一个部分。

##### 视图

在 app_server/views 中创建一个新文件，并将其保存为 location-info.pug。该文件的内容显示在列表 4.7 中，这是本书中最大的列表。记住，在原型开发的这个阶段，你是在生成带有直接硬编码数据的可点击页面。

##### 列表 4.7\. 详情页面的视图，app_server/views/location-info.pug

```
extends layout

block content
  .row.banner                                                            ***1***
    .col-12                                                              ***1***
      h1 Starcups                                                        ***1***
  .row
    .col-12.col-lg-9                                                     ***2***
      .row
        .col-12.col-md-6                                                 ***2***
          p.rating
            i.fas.fa-star
            i.fas.fa-star
            i.fas.fa-star
            i.far.fa-star
            i.far.fa-star
          p 125 High Street, Reading, RG6 1PS
          .card.card-primary                                             ***3***
            .card-block                                                  ***3***
              h2.card-title Opening hours                                ***3***
              p.card-text Monday - Friday : 7:00am - 7:00pm              ***3***
              p.card-text Saturday : 8:00am - 5:00pm                     ***3***
              p.card-text Sunday : closed                                ***3***
          .card.card-primary                                             ***4***
            .card-block                                                  ***4***
              h2.card-title Facilities
              span.badge.badge-warning
                i.fa.fa-check                                            ***4***
                | &nbsp;Hot drinks                                       ***4***
              | &nbsp;
              span.badge.badge-warning
                i.fa.fa-check                                            ***4***
                | &nbsp;Food                                             ***4***
              | &nbsp;
              span.badge.badge-warning
                i.fa.fa-check
                | &nbsp;Premium wifi
              | &nbsp;
        .col-12.col-md-6.location-map
          .card.card-primary
            .card-block
              h2.card-title Location map
              img.img-fluid.rounded(src=
'http://maps.googleapis.com/maps/api/..............                    ***5***
staticmap?center=51.455041,-0.9690884&zoom=17&size=400x350             ***5***
&sensor=false&markers=51.455041,-0.9690884&scale=2&key=<API Key>')     ***5***
  .row
        .col-12
          .card.card-primary.review-card
            .card-block
              a.btn.btn-primary.float-right(href='/location/review/new')
              Add review                                               ***6***
              h2.card-title Customer reviews
              .row.review
                .col-12.no-gutters.review-header
                  span.rating
                    i.fas.fa-star
                    i.fas.fa-star
                    i.fas.fa-star
                    i.far.fa-star
                    i.far.fa-star
                  span.review Author Simon Holmes
                  small.review Timestamp 16 February 2017
                .col-12
                  p What a great place.
              .row.review
                .col-12.no-gutters.review-header
                  span.rating
                    i.fas.fa-star
                    i.fas.fa-star
                    i.fas.fa-star
                    i.far.fa-star
                    i.far.fa-star
                  span.reviewAuthor Charlie Chaplin
                  small.reviewTimestamp 14 February 2017
                .col-12
                  p It was okay. Coffee wasn't great.
    .col-12.col-lg-3                                                     ***7***
      p.lead
        | Starcups is on Loc8r because it has accessible wifi and space to
          sit down with your laptop and get some work done.
      p
        | If you've been and you like it - or if you don't - please leave
          a review to help other people just like you.
```

+   ***1*** **从页面标题开始**

+   ***2*** **设置模板所需的嵌套响应式列**

+   ***3*** **用于定义信息区域（在本例中为营业时间）的多个 Bootstrap 卡片组件之一**

+   ***4*** **使用 &nbsp; 实体是因为 Pug 并不总是理解空白，并且有删除它的习惯。**

+   ***5*** **使用静态谷歌地图图像，包括查询字符串中的坐标 51.455041,-0.9690884。记得用你之前获得的 Google API Key 替换<APIKey>。**

+   ***6*** **使用 Bootstrap 的按钮辅助类创建到添加评论页面的链接**

+   ***7*** **侧边栏上下文信息的最后一个响应式列**

这是一个很长的模板，你很快就会看到如何缩短它。但页面本身很复杂，包含大量信息和几个嵌套的响应式列。想象一下，如果它完全用 HTML 编写，将会多么长！

确保你包含了 GitHub 上的完整版本 style.css，因为你正在使用它为标准的 Bootstrap 主题添加一些活力。

完成所有这些后，详情页面布局就完成了；你可以转到 localhost:3000/location 查看。![图 4.11](img/04fig11_alt.jpg)显示了在浏览器和移动设备上此布局的外观。

##### 图 4.11\. 桌面和移动设备上的详情页面布局

![](img/04fig11_alt.jpg)

用户旅程的下一步是添加评论页面，它有更简单的要求。

#### 4.4.2\. 添加评论页面

这个页面很简单，包含一个包含用户姓名和评分及评论输入字段的表单。

第一步是更新控制器以引用新的视图。在 app_server/controllers/locations.js 中，将`addReview`控制器更改为使用新的视图`location-review-form`，如下代码片段所示：

```
const addReview = (req, res) => {
  res.render('location-review-form', { title: 'Add review' });
};
```

第二步是创建视图本身。在`app_server/views`目录下的`views`文件夹中，创建一个名为`location-review-form.pug`的新文件。因为这个页面被设计成可点击的原型，所以您不会将表单数据提交到任何地方，所以目标是让动作重定向到显示评论数据的详情页面。在表单中，然后设置动作为`/location`，方法为`get`。稍后，您将将其更改为`post`方法，但这个表单将为您提供目前需要的功能。评论表单页面的全部代码如下所示。

##### 列表 4.8\. 添加评论页面的视图，`app_server/views/location-review-form.pug`

```
extends layout

block content
  .row.banner
    .col-12
      h1 Review Starcups
  .row
    .col-12.col-md-8
      form(action="/location", method="get", role="form")               ***1***
        .form-group.row
          label.col-10.col-sm-2.col-form-label(for="name") Name
          .col-12.col-sm-10
            input#name.form-control(name="name")                        ***2***
        .form-group.row
          label.col-10.col-sm-2.col-form-label(for="rating") Rating
          .col-12.col-sm-2
            select#rating.form-control.input-sm(name="rating")          ***3***
              option 5                                                  ***3***
              option 4                                                  ***3***
              option 3                                                  ***3***
              option 2                                                  ***3***
              option 1                                                  ***3***
        .form-group.row
          label.col-sm-2.col-form-label(for="review") Review
          .col-sm-10
            textarea#review.form-control(name="review", rows="5")       ***4***
        button.btn.btn-primary.float-right Add my review                ***5***
    .col-12.col-md-4
```

+   ***1*** **设置表单动作到 /location，方法到 get**

+   ***2*** **输入框供评论者留下他们的名字**

+   ***3*** **用于评分 1 到...的下拉选择框**

+   ***4*** **用于评论文本内容的文本区域**

+   ***5*** **表单的提交按钮**

Bootstrap 提供了许多处理表单的辅助类，这在列表 4.8 中很明显。但页面很简单，当您运行它时，它应该看起来像图 4.12。

##### 图 4.12\. 桌面和移动视图中的完整添加评论页面

![图片](img/04fig12_alt.jpg)

添加评论页面的标记是用户通过屏幕集合的“位置”结束旅程。只剩下关于页面需要完成。

#### 4.4.3\. 添加关于页面

静态原型的最后一页是关于页面，它有一个页眉和一些内容——没有复杂的东西。布局可能对后续的其他页面有用，例如隐私政策或条款和条件页面，因此最好创建一个通用、可重用的视图。

关于页面的控制器位于`app_server/controllers`目录下的`others.js`文件中。您正在寻找名为`about`的控制器，并且想要将视图的名称更改为`generic-text`，如下代码片段所示：

```
const about = (req, res) => {
  res.render('generic-text', { title: 'About' });
};
```

接下来，在`app_server/views`中创建名为`generic-text.pug`的视图。这个模板很小，应该看起来如下所示。

##### 列表 4.9\. 仅文本页面的视图：`app_server/views/generic-text.pug`

```
extends layout
block content
  .row.banner
    .col-12
      h1= title
  .row
    .col-12.col-lg-8
      p                                                                   ***1***
        | Loc8r was created to help people find places to sit down and    ***1***
          get a bit of work done.                                       ***1***
        | <br /><br />                                                    ***1***
        | Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc   ***1***
          sed lorem ac nisi dignissim accumsan.                         ***1***
```

+   ***1*** **使用|在<p>标签内创建普通文本行。**

这是一个简单的布局。现在不要担心在通用视图中包含特定页面的内容；您很快就会承担这项任务并使页面可重用。为了完成可点击的静态原型，这是可以的。

您可能需要一些额外的行，以便页面看起来有真实的内容。注意，以竖线字符（`|`）开头的行可以包含 HTML 标签，如果您想的话。图 4.13 显示了页面在浏览器中带有更多内容时的样子。

##### 图 4.13\. 渲染关于页面的通用文本模板

![图片](img/04fig13_alt.jpg)

这就是静态网站所需的最后四页之一。你可以将此页面推送到 Heroku，让人们访问 URL 并四处点击。如果你忘记了如何操作，以下代码片段显示了你需要使用的终端命令，假设你已经设置了 Heroku。在终端中，你需要位于应用程序的根目录。然后发出以下命令：

```
$ git add --all
$ git commit –m "Adding the view templates"
$ git push heroku master
```


**获取源代码**

目前应用程序的源代码可在 GitHub 上的 chapter-04-views 分支中找到。在终端的新文件夹中，输入以下命令以克隆它并安装 npm 模块依赖项：

```
$ git clone -b chapter-04-views
     https://github.com/cliveharber/gettingMean-2.git
$ cd gettingMean-2
$ npm install
```


接下来是什么？路由、视图和控制器已设置好，你可以点击浏览静态网站，并且你已经将其推送到 Heroku，以便其他人也可以尝试。在某种程度上，你已经达到了这个阶段的目标；你可以在这里停下来，同时玩转旅程并获得反馈。这个阶段无疑是整个过程中最容易进行大规模、全面更改的点。

如果你确实计划构建一个 Angular SPA，并且假设你对到目前为止所做的工作感到满意，你可能不会进一步创建静态原型。相反，你将开始创建一个 Angular 应用程序。

但你现在将要采取的下一步将继续沿着创建 Express 应用程序的道路前进。所以，尽管保持静态网站，你将把数据从视图中移除，放入控制器中。

### 4.5\. 从视图中移除数据并使它们更智能

目前，所有内容和数据都存储在视图中。这种安排非常适合测试和移动内容，但你需要继续前进。MVC 架构的一个目标是拥有没有内容或数据的视图。视图应该提供数据给最终用户，同时对于提供的数据保持无知。视图需要一个数据结构，但数据本身的内容对视图本身并不重要。

考虑 MVC 架构：模型持有数据；控制器处理数据；最后，视图渲染处理后的数据。你现在还没有处理模型；你将在第五章开始处理。现在，你正在处理视图和控制器。

要使视图更智能并执行它们预期的功能，你需要将数据和内容从视图中移除，并将其放入控制器中。图 4.14 说明了 MVC 架构中的数据流以及你想要进行的更改。

##### 图 4.14\. MVC 模式中数据应该如何流动，从模型通过控制器到视图。在这个原型阶段，你的数据在视图中，但你希望将其退回一步到控制器中。

![](img/04fig14_alt.jpg)

现在做出这些更改，可以使你最终确定视图，以便为下一步做好准备。作为奖励，你将开始思考处理后的数据在控制器中应该是什么样子。与其从数据结构开始，不如从理想的前端开始，随着你对需求理解的加深，逐步逆向工程数据，通过 MVC 步骤返回。

你将如何做这些事情？从主页开始，你将把每一块内容从 Pug 视图中移除。你将更新 Pug 文件，用变量替换内容，并将内容作为变量放入控制器。然后控制器可以将这些值传递给视图。在浏览器中，结果应该看起来相同，用户不应该能够发现差异。各种部分的作用以及数据的移动和使用方式在 图 4.15 中展示。

##### 图 4.15\. 当控制器指定数据时，它将数据作为变量传递给视图；视图使用这些数据生成最终发送给用户的 HTML。

![](img/04fig15_alt.jpg)

在这个阶段结束时，数据仍然是硬编码的，但现在是控制器而不是视图。现在视图更智能，能够接受并显示发送给它们的任何数据（当然，前提是数据格式正确）。

#### 4.5.1\. 从视图中移动数据到控制器

你将从主页开始，将 `locations-list.pug` 视图中的数据移出，放入 `locations.js` 控制器文件中的 `homelist` 函数。从顶部开始，做一些简单的事情：页面标题。以下代码片段显示了 `locations-list.pug` 视图的页面标题部分，其中包含两块内容：

```
.row.banner
  .col-12
    h1 Loc8r                                                  ***1***
      small &nbsp;Find places to work with wifi near you!     ***2***
```

+   ***1*** **大字体页面标题**

+   ***2*** **页面的小字体标语**

这两块内容是你首先将移入控制器的。当前的主页控制器看起来如下所示：

```
const homelist = (req, res) => {
  res.render('locations-list', { title: 'Home' });
};
```

这个控制器已经向视图发送了一块数据。记住，`render` 函数中的第二个参数是一个包含要发送到视图的数据的 JavaScript 对象。在这里，`homelist` 控制器发送了数据对象 `{ title: 'Home' }` 到视图。这个对象被布局文件用来在 HTML `<title>` 中放置字符串 `Home`，这并不一定是最佳文本选择。

##### 更新控制器

将标题更改为更适合页面的内容，并添加两个页面标题数据项。首先对这些更改进行控制器更新，如下（加粗部分）：

```
const homelist = (req, res) => {
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',
    pageHeader: {                                               ***1***
      title: 'Loc8r',                                           ***1***
      strapline: 'Find places to work with wifi near you!'      ***1***
    }                                                           ***1***
  });
};
```

+   ***1*** **包含页面标题和标语属性的新的嵌套 pageHeader 对象**

为了整洁和未来的可管理性，标题和标语被组合在一个 `pageHeader` 对象中。这种做法是一个好习惯，会使控制器更容易更新和维护。

##### 更新视图

现在控制器将这些数据片段传递给视图后，你可以更新视图以在适当位置引用它们，而不是使用硬编码的内容。像这样的嵌套数据项使用点符号引用，就像你在 JavaScript 中从对象中获取数据时那样。要引用 `locations-list.pug` 视图中的页面标题标语，请使用 `pageHeader.strapline`。以下代码片段显示了视图的页面标题部分（加粗的部分）：

```
.row.banner
  .col-12
    h1= pageHeader.title                      ***1***
     small &nbsp;#{pageHeader.strapline}      ***2***
```

+   ***1*** **= 表示以下内容是缓冲代码——在这种情况下，是一个 JavaScript 对象。**

+   ***2*** **#{} 定界符用于将数据插入到特定位置，例如文本的一部分。**

代码在视图的相关位置输出 `pageHeader.title` 和 `pageHeader.strapline`。有关更多详细信息，请参阅侧边栏“在 Pug 模板中引用数据”。


**在 Pug 模板中引用数据**

在 Pug 模板中引用数据有两种主要的语法。第一种语法称为 *插值*，通常用于将数据插入到其他内容中间。插值数据由开始定界符 `#{` 和结束定界符 `}` 定义。你通常像这样使用它：

```
h1 Welcome to #{pageHeader.title}
```

如果你的数据包含 HTML，出于安全考虑，它会进行转义；最终用户不会看到任何 HTML 标签作为文本显示，浏览器也不会将它们解释为 HTML。如果你想让浏览器渲染数据中包含的任何 HTML，可以使用以下语法：

```
h1 Welcome to !{pageHeader.title}
```

然而，这种语法存在潜在的安全风险，并且仅应针对你信任的数据源进行。你不应该允许用户输入以这种方式显示，除非进行一些额外的安全检查。

输出数据的第二种方法是使用 *缓冲代码*。不是将数据插入到字符串中，而是使用 JavaScript 直接在标签声明后使用等号 `=` 来构建字符串，如下所示：

```
h1= "Welcome to " + pageHeader.title
```

再次强调，这是出于安全考虑而转义任何 HTML。如果你想在输出中包含未转义的 HTML，可以使用稍微不同的语法：

```
h1!= "Welcome to " + pageHeader.title
```

一次又一次，请小心。只要有可能，你应该使用其中一种转义方法以确保安全。

对于这种缓冲代码方法，你还可以使用 JavaScript 模板字符串，如下所示：

```
h1= `Welcome to ${pageHeader.title}`
```


如果你现在运行应用程序并返回主页，你应该注意到的唯一变化是 `<title>` 已被更新。其他一切看起来都一样，但一些数据现在来自控制器。

这个部分是一个简单的例子，说明你现在正在做什么以及你是如何做的。主页的复杂部分是列表部分，所以在下一段中，你将了解如何处理这项任务。

#### 4.5.2. 处理复杂、重复的数据模式

关于列表部分，首先要注意的是它有多个条目，所有条目都遵循相同的数据模式和布局模式。就像你刚刚对页面标题所做的那样，从数据开始，从视图到控制器获取数据。

在 JavaScript 数据方面，一个可重复的模式非常适合作为对象数组的想法。你想要一个数组来存储多个对象，每个对象都包含单个列表的所有相关信息。

##### 分析视图中的数据

看看列表以确定控制器需要发送哪些信息。图 4.16 提醒你列表在主页视图中的样子。

##### 图 4.16\. 单个列表，显示你需要的数据

![](img/04fig16_alt.jpg)

从这个屏幕截图，你可以看到主页上的单个列表需要以下数据要求：

+   名称

+   评分

+   距离

+   地址

+   设施列表

从图 4.16 中的屏幕截图获取数据，并从中创建一个 JavaScript 对象，你可以得到一个简单的代码片段，如下所示：

```
{
  name: 'Starcups',
  address: '125 High Street, Reading, RG6 1PS',
  rating: 3,
  facilities: ['Hot drinks', 'Food', 'Premium wifi'],       ***1***
  distance: '100m'
}
```

+   ***1*** **设施列表作为字符串值数组发送**

这就是表示单个位置作为对象所需的所有内容。对于多个位置，你需要这些对象的数组。

##### 将重复数据数组添加到控制器

如果你想要使用视图中的现有数据创建一个单位置对象的数组，并将其添加到控制器中传递给`render`函数的数据对象中。以下代码片段显示了更新的`homelist`控制器，包括位置数组：

```
const homelist = (req, res) => {
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',
    pageHeader: {
      title: 'Loc8r',
      strapline: 'Find places to work with wifi near you!'
    },
    locations: [{                                         ***1***
      name: 'Starcups',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 3,
      facilities: ['Hot drinks', 'Food', 'Premium wifi'],
      distance: '100m'
    },{
      name: 'Cafe Hero',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 4,
      facilities: ['Hot drinks', 'Food', 'Premium wifi'],
      distance: '200m'
    },{
      name: 'Burger Queen',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 2,
      facilities: ['Food', 'Premium wifi'],
      distance: '250m'
    }]
  });
};
```

+   ***1*** **将位置数组作为渲染的参数传递给视图**

在这里，你有三个位置的详细信息被发送到数组中。当然，你可以添加更多，但这段代码是一个很好的开始。现在你需要让视图渲染这些信息，而不是目前硬编码在其中的数据。

##### 在 Pug 视图中遍历数组

控制器正在将一个数组发送到 Pug 作为变量`locations`。Pug 提供了一种简单的语法来遍历数组。在一行中，你指定要使用哪个数组以及你想要用作键的变量名。键是对数组中当前项的命名引用，因此其内容在循环遍历数组时会改变。Pug 循环的结构如下：

![](img/p0112-01.jpg)

在 Pug 中，任何嵌套在这行内的内容都会针对数组中的每个项目进行迭代。看看使用位置数据和部分视图的示例。在视图文件`locations-list.pug`中，每个位置都以以下代码片段开始，每次使用不同的名字：

```
.card
  .card-block
    h4
      a(href="/location") Starcups
```

你可以使用 Pug 的`each`/`in`语法遍历`locations`数组中的所有位置，并输出每个位置的名字。这是如何工作的，下一代码片段将展示：

```
each location in locations                       ***1***
  .card                                          ***2***
    .card-block                                  ***2***
      h4                                         ***2***
        a(href="/location")= location.name       ***3***
```

+   ***1*** **设置循环，定义一个变量位置作为键**

+   ***2*** **嵌套项都会被遍历。**

+   ***3*** **输出每个位置的名字，访问每个位置的名字属性**

给定你拥有的控制器数据，其中包含三个位置，使用前面的代码与这些数据结合将产生以下 HTML：

```
<div class="card">
  <div class="card-block">
    <h4>
      <a href="/location">Starcups</a>
    </h4>
  </div>
</div>
<div class="card">
  <div class="card-block">
    <h4>
      <a href="/location">Cafe Hero</a>
    </h4>
  </div>
</div>
<div class="card">
  <div class="card-block">
    <h4>
      <a href="/location">Burger Queen</a>
    </h4>
  </div>
</div>
```

如您所见，HTML 结构——`div`元素和`h4`以及`a`标签——被重复了三次。但每个位置的名称都不同，对应控制器中的数据。

遍历数组很容易，通过这个小测试，你已经得到了更新视图文本所需的前几行。现在你需要继续处理列表中使用的其余数据。你不能用这种方式处理评分星标，所以现在先忽略它们，稍后再处理。

处理其余的数据，你可以生成以下代码片段，它输出每个列表的所有数据。由于设施作为数组传递，你需要为每个列表遍历该数组：

```
each location in locations
  .card
    .card-block
      h4
        a(href="/location")= location.name
        small &nbsp;
          i.fas.fa-star
          i.fas.fa-star
          i.fas.fa-star
          i.far.fa-star
          i.far.fa-star
        span.badge.badge-pill.badge-default.float-right= location.distance
      p.address= location.address
      .facilities
          span.badge.badge-warning= facility         ***1***
```

+   ***1*** **遍历嵌套数组以输出每个位置的设施**

遍历`facilities`数组没有问题，Pug 可以轻松处理这一点。提取其余的数据，如距离和地址，使用你已经使用过的技术是直接的。

剩下要处理的部分就是评分星标。为此任务，你需要一点内联 JavaScript 代码。

#### 4.5.3\. 使用代码操作数据和视图

对于星标评分，视图正在输出带有不同类的`span`元素，使用 Font Awesome 的图标系统。评分系统总共有五颗星，根据评分是实心还是空心。例如，五颗星的评分显示五颗实心星；三颗星的评分显示三颗实心星和两颗空心星，如图 4.17 所示；零颗星的评分显示五颗空心星。

##### 图 4.17\. Font Awesome 星标系统在实际应用中的效果，显示五颗星中的三颗星

![](img/04fig17.jpg)

要生成这种类型的输出，你将在 Pug 模板中使用一些代码。代码基本上是 JavaScript，加入了一些 Pug 特定的约定。要在 Pug 模板中添加一行内联代码，请在行前加上一个连字符（hyphen）。这个前缀告诉 Pug 运行 JavaScript 代码而不是将其传递到浏览器。

要生成星标的输出，你将使用几个`for`循环。第一个循环输出正确数量的实心星，第二个循环输出任何剩余的空心星。以下代码片段显示了这些循环在 Pug 中的外观和工作方式：

```
small &nbsp;
  - for (let i = 1; i <= location.rating; i++)
    i.fas.fa-star
  - for (let i = location.rating; i < 5; i++)
    i.far.fa-star
```

注意，语法看起来很熟悉，是 JavaScript，但没有用花括号定义代码块来运行。相反，代码块是通过缩进来定义的，就像 Pug 的其他部分一样。还要注意代码和 Pug 的混合。代码行表示，“每次评估为`true`时，渲染缩进的 Pug 内容。”这种设计很好，因为你不需要尝试用 JavaScript 构建你的 HTML。

这样，主页的内容和布局就整理好了，你可以继续进行。你还可以做一件事来改进你已有的内容，并使一些代码可重用。

#### 4.5.4. 使用 includes 和 mixins 创建可重用的布局组件

星级评分代码在其他布局中也会很有用。你可能想在详情页上使用它，也许未来还会在更多的地方使用。你不想手动添加到每个页面上。如果你决定不再喜欢 Font Awesome 图标并想更改标记，你当然不希望不得不在每个显示评分的页面上进行更改——如果你能避免的话。

幸运的是，Pug 允许你通过使用 mixin 和 includes 来创建可重用的组件。

##### 定义 Pug mixins

Pug 中的*mixin*本质上是一个函数。你可以在文件的顶部定义一个 mixin 并在多个地方使用它。mixin 的定义很简单：你定义 mixin 的名称，然后通过缩进来嵌套其内容。以下代码片段展示了基本的 mixin 定义：

```
mixin welcome
  p Welcome
```

这个定义在调用它的任何地方输出`Welcome`文本到`<p>`标签中。

Mixins 也可以接受参数，就像 JavaScript 函数一样。这个特性对于创建显示评分所需的 mixin 非常有用，因为 HTML 输出会根据实际评分而有所不同。以下代码片段展示了这个过程是如何工作的，定义了在主页上输出评分星级的 mixin：

```
mixin outputRating(rating)                 ***1***
  - for (let i = 1; i <= rating; i++)      ***2***
    i.fas.fa-star
  - for (let i = rating; i < 5; i++)       ***2***
    i.far.fa-star
```

+   ***1*** 定义期望单个参数 rating 的 mixin outputRating

+   ***2*** 使用评分参数在 for 循环中输出正确的 HTML

在某种意义上，这个 mixin 的工作方式就像一个 JavaScript 函数。当你定义 mixin 时，你可以指定它期望的参数。你可以在 mixin 中使用这些参数。你可以将前面的代码片段放入 locations-list.pug 文件的顶部，在`extends layout`和`block content`行之间。

##### 调用 Pug mixins

在定义 mixin 之后，你当然会想要使用它。调用 mixin 的语法是在其名称前放置一个`+`。如果你没有参数，例如`welcome`mixin，这个语法看起来如下所示：

```
+welcome
```

这个语法调用`welcome`mixin 并在`<p>`标签内输出文本`Welcome`。

使用参数调用 mixin 同样简单。你需要在括号内发送参数的值，就像调用 JavaScript 函数时一样。在 locations-list.pug 文件中，在你输出评分的地方，评分的值存储在变量`location.rating`中，如下所示：

```
small &nbsp;
  - for (let i = 1; i <= location.rating; i++)
    i.fas.fa-star
  - for (let i = location.rating; i < 5; i++)
    i.far.fa-star
```

你可以用调用你新创建的 mixin `outputRating`来替换这段代码，发送`location.rating`变量作为参数。这个调用看起来如下代码片段：

```
  h4
   a(href='/location')= location.name
+outputRating(location.rating)
```

这段代码输出的 HTML 与之前完全相同，但你已经将部分代码移出了布局的内容。目前，这段代码只能在同一文件内重用，但接下来，你将使用包含来使其对其他文件可用。

##### 在 Pug 中使用包含

要允许你的新 mixin 可以从其他 Pug 模板中调用，你需要将其制作成一个包含文件，这很简单。

在 app_server/views 文件夹内，创建一个名为 _includes 的子文件夹。（_ 前缀是我们发现对保持此类文件夹在顶部有用的约定。）在这个文件夹内，创建一个名为 sharedHTMLfunctions.pug 的新文件，并将 `outputRating` mixin 定义粘贴进去，如下所示：

```
mixin outputRating(rating)
  - for (let i = 1; i <= rating; i++)
    i.fas.fa-star
  - for (let i = rating; i < 5; i++)
    i.far.fa-star
```

保存文件，你就创建了一个包含文件。Pug 提供了一种简单的语法来在布局中使用包含文件：使用关键字 `include`，后跟包含文件的相对路径。以下代码片段显示了如何进行操作。这一行应立即跟在 locations-list.pug 顶部的 `extends layout` 行之后：

```
include _includes/sharedHTMLfunctions
```

现在，你不再需要在模板中内联 mixin 代码，而是从包含文件中调用它。注意，在调用包含文件时可以省略 .pug 文件扩展名。从现在开始，当你创建一个需要包含评分星的新模板时，你可以轻松地引用这个包含文件并调用 `outputRatings` mixin。

现在主页已经完成了！

#### 4.5.5\. 查看完成的主页

在本章中，你对主页模板进行了相当多的修改。现在，看看你最终得到了什么。首先，看看更新的控制器。以下列表显示了最终的 `homelist` 控制器，它包含了标题、页面标题、侧边栏和位置列表的硬编码数据。

##### 列表 4.10\. 将硬编码数据传递给视图的 `homelist` 控制器

```
const homelist = (req, res) => {
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',                      ***1***
    pageHeader: {                                                         ***2***
      title: 'Loc8r',                                                     ***2***
      strapline: 'Find places to work with wifi near you!'                ***2***
    },

    sidebar: "Looking for wifi and a seat? Loc8r helps you find places    ***3***
to work when out and about. Perhaps with coffee, cake or a pint?          ***3***
Let Loc8r help you find the place you're looking for.",
    locations: [{                                                         ***4***
      name: 'Starcups',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 3,
      facilities: ['Hot drinks', 'Food', 'Premium wifi'],
      distance: '100m'
    },{
      name: 'Cafe Hero',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 4,
      facilities: ['Hot drinks', 'Food', 'Premium wifi'],
      distance: '200m'
    },{
      name: 'Burger Queen',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 2,
      facilities: ['Food', 'Premium wifi'],
      distance: '250m'
    }]
  });
};
```

+   ***1*** **更新 HTML <title> 的文本**

+   ***2*** **在对象内部添加页面标题的文本作为两个条目**

+   ***3*** **添加侧边栏的文本**

+   ***4*** **为列表中的每个位置创建一个对象的数组**

看到所有这些代码放在一起，你可以开始欣赏这种方法的走向。你对 Loc8r 主页所需的所有数据有一个清晰的了解，这在第五章（[kindle_split_016.xhtml#ch05](https://kindle_split_016.xhtml#ch05)）中会很有用。这个控制器包含了侧边栏的文本。我们没有讨论这一步，但从视图中将文本移到控制器中就像在控制器中为它创建一个新变量并在视图中引用它一样简单。

通过这个过程，你实现了一个重要的事情，那就是从视图中移除了数据。用数据构建视图是一个很好的第一步，因为它允许你专注于最终用户体验，而不会被技术细节所分散。现在，你已经将数据从视图移到控制器中，你有一个更智能、更动态的视图。视图知道它需要哪些数据片段，但它不关心这些片段中的内容。以下列表显示了首页的最终视图。

##### 列表 4.11\. 首页的最终视图：app_server/views/locations-list.pug

```
extends layout
include _includes/sharedHTMLfunctions                    ***1***
block content
  .row.banner
    .col-12
      h1= pageHeader.title                               ***2***
        small &nbsp;#{pageHeader.strapline}              ***2***
  .row
    .col-12.col-md-8
      each location in locations                         ***3***
        .card
          .card-block
            h4
              a(href="/location")= location.name
              +outputRating(location.rating)             ***4***
              span.badge.badge-pill.badge-default.float-right=
     location.distance
            p.address= location.address
            .facilities
              each facility in location.facilities
                span.badge.badge-warning= facility

    .col-12.col-md-4
      p.lead= sidebar                                    ***5***
```

+   ***1*** **引入包含 outputRating 混入的外部包含文件**

+   ***2*** **使用不同的方法输出页面标题文本**

+   ***3*** **遍历位置数组**

+   ***4*** **为每个位置调用 outputRating 混入，传递当前位置评分的值**

+   ***5*** **从控制器引用侧边栏内容**

这是一个小模板，尤其是考虑到它所做的一切。这是 Pug 和 Bootstrap 共同作用的力量的证明，结合了移除所有内容。

你已经接近了 MVC——以及一般开发——的目标，即关注点的分离，至少对于首页来说是这样。

#### 4.5.6\. 更新其余的视图和控制器

我们已经详细地介绍了首页的过程，但不会在其他页面上花费太多时间。在你可以进入开发下一阶段——构建数据模型——之前，你需要对所有页面进行处理。目标是所有视图中都没有数据；相反，视图将更智能，数据将硬编码到相关的控制器中。

每个页面的处理过程如下：

1.  查看视图中的数据。

1.  在控制器中为该数据创建一个结构。

1.  将视图中的数据替换为对控制器数据的引用。

1.  寻找代码重用的机会。

附录 C 讲解了剩余三个页面的处理过程，展示了每个控制器和视图代码应该是什么样子。当你完成时，你的视图不应包含任何硬编码的数据；每个页面的控制器应传递所需的数据。图 4.18 展示了本阶段结束时你应该拥有的最终页面的截图集合。

##### 图 4.18\. 使用智能视图和控制器中硬编码的数据的静态原型中所有四个页面的截图

![](img/04fig18_alt.jpg)

你已经完成了快速原型开发的第一个阶段，并准备好开始下一阶段。


**获取源代码**

应用程序到目前为止的源代码可在 GitHub 的 gettingMean-2 仓库的 chapter-04 分支上找到。在终端的新文件夹中，输入以下命令以克隆它并安装依赖项：

```
$ git clone -b chapter-04 https://github.com/cliveharber/gettingMean-2.git
$ cd gettingMean-2
$ npm install
```


在第五章中，您将继续通过使用 MongoDB 和 Mongoose 创建数据模型来将数据回移到 MVC 架构中的旅程。没错；现在是数据库时间！

### 摘要

在本章中，您学习了

+   在 Express 中定义和组织路由的简单方法

+   如何使用 Node 模块来存储控制器

+   通过正确定义路由来设置多个控制器集的最佳方法

+   使用 Pug 和 Bootstrap 原型化视图

+   创建可重用 Pug 组件和混入

+   在 Pug 模板中显示动态数据

+   将数据从控制器传递到视图

## 第五章\. 使用 MongoDB 和 Mongoose 构建数据模型

*本章涵盖*

+   使用 Mongoose 将 Express/Node 应用程序连接到 MongoDB

+   使用 Mongoose 定义数据模型的模式

+   将应用程序连接到数据库

+   使用 MongoDB shell 管理数据库

+   将数据库推送到实时环境

在第四章中，您将数据从视图移出，并将 MVC 路径回退到控制器。最终，控制器将数据传递给视图，但它们不应该存储它。图 5.1 回顾了 MVC 模式中的数据流。

##### 图 5.1\. 在 MVC 模式中，数据存储在模型中，由控制器处理，然后由视图渲染。

![](img/05fig01_alt.jpg)

对于存储数据，您需要一个数据库——具体来说，是 MongoDB。这一步是过程中的下一步：创建数据库和数据模型。

| |
| --- |

##### 注意

如果您还没有从第四章构建应用程序，您可以在[`github.com/cliveharber/gettingMean-2`](https://github.com/cliveharber/gettingMean-2)的 chapter-04 分支上获取代码。在终端的新文件夹中，输入以下命令以克隆它：

```
$ git clone -b chapter-04 https://github.com/cliveharber/gettingMean-2.git
```

| |
| --- |

您将首先连接应用程序到数据库，然后使用 Mongoose 定义模式和模型。当您对结构满意时，可以直接向 MongoDB 数据库添加一些测试数据。最后一步是确保当推送到 Heroku 时，对数据存储的访问也能正常工作。图 5.2 显示了这四个步骤的流程。

##### 图 5.2\. 本章的四个主要步骤，从将应用程序连接到数据库到将整个内容推送到实时环境

![](img/05fig02_alt.jpg)

对于那些担心遗漏了一两个部分的人，不用担心；您还没有创建数据库。而且您不需要。在各种其他技术堆栈中，这种情况可能会引发问题并抛出错误。但与 MongoDB 一起，您在尝试使用它之前不需要创建数据库。图 5.3 显示了本章在整体架构方面的重点。

##### 图 5.3\. 查看 MongoDB 数据库，并在 Express 中使用 Mongoose 模型数据和管理数据库连接

![](img/05fig03_alt.jpg)

你将使用 MongoDB 数据库，但大部分工作将在 Express 和 Node 中完成。在第二章中，我们讨论了通过创建 API 而不是将数据紧密集成到主 Express 应用程序中来解耦数据集成的好处。尽管你将在 Express 和 Node 中工作，并且仍然在同一个封装的应用程序中，但你将开始构建你的 API 层的基础。


##### 注意

为了继续本章的学习，你需要安装 MongoDB。如果你还没有安装，可以在附录 A 中找到安装说明。本章末尾应用程序的源代码可在 GitHub 的 chapter-05 分支上找到。在终端的新文件夹中，输入以下命令以克隆它并安装 npm 模块依赖项：

```
$ git clone -b chapter-05 https://github.com/cliveharber/gettingMean-2.git
$ cd gettingMean-2
$ npm install
```


### 5.1\. 通过 Mongoose 将 Express 应用程序连接到 MongoDB

你可以直接将应用程序连接到 MongoDB，并使用原生驱动程序使两者交互。尽管原生 MongoDB 驱动程序功能强大，但使用起来并不特别容易。它也没有提供一种内置的方式来定义和维护数据结构。Mongoose 提供了原生驱动程序的大部分功能，但以更方便的方式提供，旨在适应应用程序开发的流程。

Mongoose 真正出色之处在于它使你能够定义数据结构和管理模型，并使用它们从应用程序代码中与数据库交互。作为这种方法的一部分，Mongoose 包括向你的数据定义添加验证的能力，这意味着你不需要在应用程序中每个发送数据回数据库的地方都编写验证代码。

Mongoose 通过作为应用程序和数据库之间的联络人而融入 Express 应用程序中，如图 5.4 所示。

##### 图 5.4\. MEAN 栈中的数据交互以及 Mongoose 的位置。Node/Express 应用程序通过 Mongoose 与 MongoDB 交互；Node 和 Express 也可以与 Angular 通信。

![](img/05fig04_alt.jpg)

MongoDB 只与 Mongoose 通信，而 Mongoose 又与 Node 和 Express 通信。Angular 不会直接与 MongoDB 或 Mongoose 通信——只与 Express 应用程序通信。

你应该已经在系统上安装了 MongoDB（在附录 A 中有介绍），但没有安装 Mongoose。Mongoose 不是全局安装，而是直接添加到你的应用程序中。你将在下一节中这样做。

#### 5.1.1\. 将 Mongoose 添加到你的应用程序中

Mongoose 可作为 npm 模块使用。正如你在第三章中看到的，安装 npm 模块最快、最简单的方法是通过命令行。你可以使用一条命令安装 Mongoose 并将其添加到 package.json 中的依赖列表中。

转到终端，并确保提示符位于应用程序的根目录，即 package.json 文件所在的目录。然后运行以下命令：

```
$ npm i mongoose
```

在这里，我们使用了一个替代版本；这个版本可以节省输入。当该命令运行完成后，你将在应用程序的 node_modules 文件夹内看到一个名为 mongoose 的新文件夹，package.json 文件中的依赖项部分应该看起来像以下代码片段：

```
"dependencies": {
  "body-parser": "~1.18.3",
  "cookie-parser": "~1.4.3",
  "debug": "~4.1.0",
  "express": "~4.16.4",
  "mongoose": "⁵.3.11",
  "morgan": "~1.9.1",
  "pug": "~2.0.3",
  "serve-favicon": "~2.5.0"
}
```

当然，你可能会有略微不同的版本号，但截至目前，Mongoose 的最新稳定版本是 5.3.11。现在 Mongoose 已经安装，你就可以准备将其连接到应用程序了。

#### 5.1.2\. 将 Mongoose 连接添加到你的应用程序

在这个阶段，你将连接应用程序到数据库。你还没有创建数据库，但这没关系，因为当你首次尝试使用 MongoDB 时，它会自动创建一个数据库。这可能会有些奇怪，但对于构建应用程序来说，这是一个巨大的优势：你不需要在应用程序代码中留下混乱，去操作不同的环境。

##### MongoDB 和 Mongoose 连接

当 Mongoose 连接到 MongoDB 数据库时，它会打开一个包含五个可重用连接的池。这个连接池被所有请求共享。五个是默认数量；如果你需要，可以增加或减少连接选项。

| |
| --- |

##### 最佳实践提示

打开和关闭数据库连接可能需要一点时间，尤其是如果你的数据库位于单独的服务器或服务上。最好只在需要时运行这些操作。最佳实践是在应用程序启动时打开连接，并在应用程序重启或关闭时保持连接打开。这就是你将要采取的方法。

| |
| --- |

##### 设置连接文件

当你首次整理应用程序的文件结构时，你在 app_server 文件夹内创建了三个文件夹：models、views 和 controllers。对于处理数据和模型，你将主要位于 app_server/models 文件夹。设置连接文件是一个两步过程——创建文件并将其引入应用程序以便使用：

+   第 1 步：在 app_server/models 中创建一个名为 db.js 的文件，并保存它。目前，你将使用以下单个命令行在文件中 `require` Mongoose：

    ```
    const mongoose = require('mongoose');
    ```

+   第 2 步：通过在 app.js 中引入此文件将此文件引入应用程序。由于创建应用程序和数据库之间的连接可能需要一点时间，因此你希望在设置初期就完成这项操作。修改 app.js 的顶部部分，使其看起来像以下代码片段（粗体表示修改）：

    ```
    const express = require('express');
    const path = require('path');
    const cookieParser = require('cookie-parser');
    const logger = require('morgan');
    const favicon = require('serve-favicon');
    require('./app_server/models/db');
    ```

你不需要从 db.js 导出任何函数，因此你不需要在 `require` 它时将其分配给变量。你需要它在应用程序中存在，但你不需要在 app.js 中调用其任何方法。

如果您重新启动应用程序，它应该像以前一样运行，但现在您在应用程序中有了 Mongoose。如果您遇到错误，请检查`require`语句中的路径是否与新文件的路径匹配，您的 package.json 是否包含 Mongoose 依赖项，并且您是否已从应用程序根目录的终端中运行了`npm install`。

##### 创建 Mongoose 连接

创建一个 Mongoose 连接可以像声明数据库的 URI 并将其传递给 Mongoose 的`connect`方法一样简单。数据库 URI 是一个遵循以下结构的字符串：

![](img/p0124-01_alt.jpg)

用户名、密码和端口是可选的。在您的本地机器上，您的数据库 URI 将很简单。目前，假设您已经在本地机器上安装了 MongoDB，将以下代码片段添加到 db.js 中就足够创建一个连接：

```
const dbURI = 'mongodb://localhost/Loc8r';
mongoose.connect(dbURI, {useNewUrlParser: true});
```

`connect()`的第二个参数告诉 Mongoose 使用其新的内部 URL 解析器，这避免了由于 MongoDB 弃用而产生的弃用警告，但仍然保留了旧的连接字符串解析器。如果您在 db.js 中添加此修改后运行应用程序，它应该像以前一样启动并运行。那么您如何知道您的连接是否正常工作呢？答案在于连接事件。

##### 使用 Mongoose 连接事件监控连接

Mongoose 根据连接的状态发布事件，并且这些事件很容易挂钩，以便您可以查看正在发生的事情。您将使用事件来查看何时建立连接、何时出现错误以及何时断开连接。当这些事件中的任何一个发生时，您将在控制台记录一条消息。以下代码片段显示了所需的代码：

```
mongoose.connection.on('connected', () => {            *1*
  console.log(`Mongoose connected to ${dbURI}`);       *1*
});                                                    *1*
mongoose.connection.on('error', err => {               *2*
  console.log('Mongoose connection error:', err);      *2*
});                                                    *2*
mongoose.connection.on('disconnected', () => {         *3*
  console.log('Mongoose disconnected');                *3*
});                                                    *3*
```

+   ***1* 监听通过 Mongoose 的成功连接**

+   ***2* 检查连接错误**

+   ***3* 检查断开连接事件**

在 db.js 中添加此代码后，当您重新启动应用程序时，您应该在终端窗口中看到以下确认信息：

```
Express server listening on port 3000
Mongoose connected to mongodb://localhost/Loc8r
```

然而，如果您再次重新启动应用程序，您会注意到您没有收到任何断开连接的消息，因为当应用程序停止或重新启动时，Mongoose 连接不会自动关闭。您需要监听 Node 进程的变化来处理这种情况。

##### 关闭 Mongoose 连接

当应用程序停止时关闭 mongoose 连接与在启动时打开连接一样，是最佳实践的一部分。连接有两个端点：一个在您的应用程序中，一个在 MongoDB 中。MongoDB 需要知道您何时想要关闭连接，以便它不会保持冗余的连接打开。

要监听应用程序停止时的情况，您需要监听 Node.js 进程的名为 SIGINT 的事件。

| |
| --- |

**在 Windows 上监听 SIGINT**

SIGINT 是操作系统级别的信号，在基于 UNIX 的系统（如 Linux 和 macOS）上触发。它也在一些 Windows 的较新版本上触发。如果你在 Windows 上运行，并且断开连接事件没有触发，你可以模拟它们。如果你需要在 Windows 上模拟此行为，首先向你的应用程序添加一个新的 npm 包，`readline`。像之前一样，使用命令行中的 `npm install` 命令，如下所示：

```
$ npm install --save readline
```

完成后，在 db.js 文件中，在事件监听器代码上方添加以下内容：

```
const readLine = require ('readline');
if (process.platform === 'win32'){
  const rl = readLine.createInterface ({
    input: process.stdin,
    output: process.stdout
  });
  rl.on ('SIGINT', () => {
    process.emit ("SIGINT");
  });
}
```

此代码在 Windows 机器上发出 SIGINT 信号，允许你捕获它，并在进程结束前优雅地关闭其他任何需要关闭的内容。


如果你使用 nodemon 自动重新启动应用程序，你还需要监听 Node 进程上的第二个事件：SIGUSR2。Heroku 使用不同的事件，SIGTERM，因此你需要监听该事件。

##### 捕获进程终止事件

捕获这些事件可以防止默认行为发生。你需要确保手动重新启动所需的操作（当然是在关闭 Mongoose 连接之后）。

要做到这一点，你需要三个事件监听器和一個用于关闭数据库连接的函数。关闭数据库是一个异步操作，因此你需要传递一个回调函数来重启或结束 Node 进程。同时，你可以在控制台输出一条消息，确认连接已关闭以及关闭的原因。你可以在 db.js 中将这些内容包装在一个名为 `gracefulShutdown` 的函数中：

```
const gracefulShutdown = (msg, callback) => {                *1*
  mongoose.connection.close( () => {                         *2*
    console.log(`Mongoose disconnected through ${msg}`);     *3*
    callback();                                              *3*
  });
};
```

+   ***1* 定义一个函数以接受消息和回调函数**

+   ***2* 关闭 Mongoose 连接，通过匿名函数在关闭时运行**

+   ***3* 输出消息并在 Mongoose 连接关闭时调用回调函数**

当应用程序终止或 nodemon 重新启动它时，你需要调用此函数。以下代码片段显示了你需要添加到 db.js 中的两个事件监听器，以便实现此功能：

```
process.once('SIGUSR2', () => {                        *1*
  gracefulShutdown('nodemon restart', () => {          *2*
    process.kill(process.pid, 'SIGUSR2');              *2*
  });
});
process.on('SIGINT', () => {                           *3*
  gracefulShutdown('app termination', () => {          *4*
    process.exit(0);                                   *4*
  });
});
process.on('SIGTERM', () => {                          *5*
  gracefulShutdown('Heroku app shutdown', () => {      *6*
    process.exit(0);                                   *6*
  });
});
```

+   ***1* 监听 SIGUSR2，这是 nodemon 使用的**

+   ***2* 向 graceful-Shutdown 发送消息，向终止进程发送回调函数，再次发出 SIGUSR2**

+   ***3* 监听应用程序终止时发出的 SIGINT 信号**

+   ***4* 向 gracefulShutdown 发送消息，向退出 Node 进程发送回调函数**

+   ***5* 监听 Heroku 关闭进程时发出的 SIGTERM 信号**

+   ***6* 向 gracefulShutdown 发送消息，向退出 Node 进程发送回调函数**

现在，当应用程序终止时，它会在结束前优雅地关闭 Mongoose 连接。同样，当 nodemon 由于源文件更改而重新启动应用程序时，应用程序首先关闭当前的 Mongoose 连接。nodemon 监听器使用 `process.once` 而不是 `process.on`，因为你只想监听 SIGUSR2 事件一次。nodemon 也监听相同的事件，你不希望每次都捕获它，以防止 nodemon 无法工作。

| |
| --- |

##### 小贴士

在你创建的每个应用程序中正确管理打开和关闭数据库连接非常重要。如果你使用具有不同进程终止信号的环境，你应该确保你监听所有这些信号。

| |
| --- |

##### 完整的连接文件

你已经向 db.js 文件添加了很多内容，所以花点时间回顾一下。到目前为止，你已经

+   定义了数据库连接字符串

+   在应用程序启动时打开了 Mongoose 连接

+   监控了 Mongoose 连接事件

+   监控了一些 Node 进程事件，以便在应用程序结束时关闭 Mongoose 连接

总体而言，db.js 文件应如下所示。注意，它包括 Windows 生成 SIGINT 事件所需的额外代码。

##### 列表 5.1\. 完整数据库连接文件 db.js 在 app_server/models

```
const mongoose = require('mongoose');
const dbURI = 'mongodb://localhost/Loc8r';                  *1*
mongoose.connect(dbURI, {useNewUrlParser: true});           *1*
mongoose.connection.on('connected', () => {                 *2*
  console.log(`Mongoose connected to ${dbURI}`);            *2*
});                                                         *2*
mongoose.connection.on('error', err => {                    *2*
  console.log(`Mongoose connection error: ${err}`);         *2*
});                                                         *2*
mongoose.connection.on('disconnected', () => {              *2*
  console.log('Mongoose disconnected');                     *2*
});                                                         *2*
const gracefulShutdown = (msg, callback) => {               *3*
  mongoose.connection.close( () => {                        *3*
    console.log(`Mongoose disconnected through ${msg}`);    *3*
    callback();                                             *3*
  });                                                       *3*
};                                                          *3*
// For nodemon restarts                                     *4*
process.once('SIGUSR2', () => {                             *4*
  gracefulShutdown('nodemon restart', () => {               *4*
    process.kill(process.pid, 'SIGUSR2');                   *4*
  });                                                       *4*
});                                                         *4*
// For app termination                                      *4*
process.on('SIGINT', () => {                                *4*
  gracefulShutdown('app termination', () => {               *4*
    process.exit(0);                                        *4*
  });                                                       *4*
});                                                         *4*
// For Heroku app termination                               *4*
process.on('SIGTERM', () => {                               *4*
  gracefulShutdown('Heroku app shutdown', () => {           *4*
    process.exit(0);                                        *4*
  });                                                       *4*
});                                                         *4*
```

+   ***1* 定义数据库连接字符串并使用它打开 Mongoose 连接**

+   ***2* 监听 Mongoose 连接事件并将状态输出到控制台**

+   ***3* 可重用函数用于关闭 Mongoose 连接**

+   ***4* 监听 Node 进程的终止或重启信号，并在适当的时候调用 gracefulShutdown 函数，传递一个延续回调**

当你有一个这样的文件时，你可以轻松地从应用程序复制到另一个应用程序，因为你正在监听的事件总是相同的。每次你只需要更改数据库连接字符串。记住，你也在 app.js 中`require`了这个文件，就在顶部附近，这样连接就可以在应用程序的生命周期早期打开。

| |
| --- |

**使用多个数据库**

你到目前为止看到的是默认连接，非常适合在整个应用程序运行期间保持单个连接打开。但如果你想要连接到第二个数据库，比如用于日志记录或管理用户会话，你可以使用命名连接。在`mongoose.connect`方法的地方，你会使用一个名为`mongoose.createConnection`的方法，并将其分配给一个变量。你可以在下面的代码片段中看到这一点：

```
const dbURIlog = 'mongodb://localhost/Loc8rLog';
const logDB = mongoose.createConnection(dbURIlog);
```

此代码片段创建了一个名为`logDB`的新 Mongoose 连接对象。你可以像与`mongoose.connection`默认连接一样与之交互。这里有一些例子：

```
logDB.on('connected', () => {                           *1*
  console.log(`Mongoose connected to ${dbURIlog}`);     *1*
});                                                     *1*
logDB.close( () => {                                    *2*
  console.log('Mongoose log disconnected');             *2*
});                                                     *2*
```

+   ***1* 监听命名连接的连接事件**

+   ***2* 关闭命名连接**

| |
| --- |

### 5.2\. 为什么对数据进行建模？

在第一章中，我们讨论了 MongoDB 是一个文档存储，而不是使用行和列的传统表式数据库。这一事实赋予了 MongoDB 极大的自由度和灵活性，但有时你希望——或者*需要*——对数据进行结构化。

以 Loc8r 首页为例。如图 5.5 所示，列表部分包含一个对所有位置都通用的特定数据集。

##### 图 5.5\. 首页列表部分已定义数据需求和结构

![](img/05fig05_alt.jpg)

页面需要所有位置的数据项，并且每个位置的数据记录必须有一个一致的命名结构。没有这个结构，应用程序将无法找到并使用数据。在开发的这个阶段，数据被保存在控制器中，并传递到视图中。从 MVC 架构的角度来看，你从*视图*中的数据开始，然后将其退回一步到*控制器*。现在你需要做的是将其退回最后一步到它应该属于的地方：在*模型*中。图 5.6 说明了你当前的位置，突出了目标。

##### 图 5.6\. MVC 模式中数据应该如何流动，从模型通过控制器到视图。在你当前的原型中，你的数据在控制器中，所以你想要将其退回一步到模型。

![图片](img/05fig06_alt.jpg)

将数据逐步通过 MVC 流程，正如你迄今为止所做的那样，一个结果是它有助于巩固数据结构的要求，确保数据结构准确地反映了你应用程序的需求。如果你首先尝试定义模型，你最终会猜测应用程序的外观和工作方式。

当你谈论数据建模时，你是在描述你希望数据如何结构化。在你的应用程序中，你可以手动创建和管理定义，并自己进行大量工作，或者你可以使用 Mongoose 并让它做艰苦的工作。

#### 5.2.1\. 什么是 Mongoose 以及它是如何工作的？

Mongoose 专门为 Node 应用程序构建，作为 MongoDB 对象文档模型器（ODM）。一个关键原则是你可以从你的应用程序内部管理你的数据模型。你不必直接与数据库或外部框架或关系型映射器打交道；你可以在应用程序的舒适环境中定义你的数据模型。

首先，我们将解决一些命名约定：

+   在 MongoDB 中，数据库中的每个条目被称为*文档*。

+   在 MongoDB 中，一组文档被称为*集合*。（如果你习惯于关系型数据库，可以将其视为*表*。）

+   在 Mongoose 中，文档的定义被称为*模式*。

+   在模式中定义的每个单独的数据实体被称为*路径*。

以名片堆叠为例，图 5.7 展示了这些命名约定以及它们是如何相互关联的。

##### 图 5.7\. MongoDB 和 Mongoose 中集合、文档、模式和路径之间的关系，使用名片隐喻

![图片](img/05fig07_alt.jpg)

最后一个定义是针对模型的。*模型*是模式的编译版本。所有使用 Mongoose 的数据交互都通过模型进行。你将在第六章中更多地使用模型，但就目前而言，你正专注于构建它们。

#### 5.2.2\. Mongoose 如何建模数据？

如果您在应用程序中定义数据，您将如何做？当然是在 JavaScript 中——更确切地说，是 JavaScript 对象。您已经在图 5.7 中瞥见了，但现在请看看一个简单的 MongoDB 文档，看看它的 Mongoose 模式可能是什么样子。以下代码片段显示了一个 MongoDB 文档，后面跟着相应的 Mongoose 模式：

```
{                                                *1*
  "firstname" : "Simon",                         *1*
  "surname" : "Holmes",                          *1*
  _id : ObjectId("52279effc62ca8b0c1000007")     *1*
}                                                *1*
{                                                *2*
  firstname : String,                            *2*
  surname : String                               *2*
}                                                *2*
```

+   ***1* 示例 MongoDB 文档**

+   ***2* 对应的 Mongoose 模式**

如您所见，模式与数据本身有很强的相似性。模式定义了每个数据路径的名称及其将包含的数据类型。在这个例子中，您只是简单地将路径`firstname`和`surname`声明为字符串。


**关于 _id 路径**

您可能已经注意到，您在模式中没有声明`id`路径。`_id`是唯一标识符——如果您愿意，可以称为主键——对于每个文档。MongoDB 在创建每个文档时自动创建此路径，并分配一个唯一的`ObjectId`值。该值的设计是通过结合自 UNIX 纪元以来的时间、机器和进程标识符以及一个计数器来确保始终唯一。

如果您更喜欢使用自己的唯一键系统（例如，如果您有一个现有的数据库），这是可能的。在这本书和 Loc8r 应用程序中，您将坚持使用默认的`ObjectId`。


#### 5.2.3\. 拆分模式路径

单个路径定义的基本结构是路径名称后跟一个属性对象。在前面的例子中，您查看了一个 Mongoose 模式，它展示了定义数据路径及其数据类型的一种简写方式。模式路径由路径名称和属性对象组成，如下所示：

![](img/p0132-01.jpg)


**允许的模式类型**

模式类型是定义给定路径数据类型的属性。对于所有路径都是必需的。如果路径的唯一属性是类型，您可以使用简写定义。您可以使用以下八种模式类型：

+   `String—`任何字符串，UTF-8 编码。

+   `Number—`Mongoose 不支持长或双精度数字，但可以使用 Mongoose 插件进行扩展；在大多数情况下，默认支持已经足够。

+   `Date—`通常由 MongoDB 作为`ISODate`对象返回。

+   `Boolean—`真或假。

+   `Buffer—`用于二进制信息，如图像。

+   `Mixed—`任何数据类型。

+   `Array—`可以是相同数据类型的数组或嵌套子文档的数组。

+   `ObjectId—`用于除 _id 之外的路径的唯一 ID；通常用于引用其他文档中的 _id 路径。

如果您需要使用不同的模式类型，您可以编写自己的自定义模式类型，或者使用来自[`plugins.mongoosejs.io`](http://plugins.mongoosejs.io)的现有 Mongoose 插件。


路径名遵循 JavaScript 对象定义的约定和要求。没有空格或特殊字符，你应该尽量避免使用保留词。我们的约定是使用 camelCase 作为路径名。如果你正在使用现有的数据库，请使用文档中已经存在的路径名。如果你正在创建一个新的数据库，模式中的路径名将用于文档，所以请仔细思考。

属性对象本质上是一个 JavaScript 对象。这个对象定义了路径中包含的数据的特征。至少，这个对象包含数据类型，但它可以包括验证特征、边界、默认值等。在接下来的几章中，当你将 Loc8r 转变为一个数据驱动应用程序时，你将探索并使用这些选项中的一些。

在下一节中，你将开始定义应用程序中需要的模式。

### 5.3. 定义简单的 Mongoose 模式

我们讨论了这样一个事实，即 Mongoose 模式本质上是一个 JavaScript 对象，你可以在应用程序内部定义它。首先，设置并包含该文件，以便完成并移除它，这样你就可以自由地专注于模式。

如你所料，你将在模型文件夹中定义模式，与 db.js 文件并列。实际上，你将把它`require`到 db.js 中，以便将其暴露给应用程序。在 app_server/models 文件夹中，创建一个名为 locations.js 的新空文件。你需要 Mongoose 来定义一个 Mongoose 模式，这是自然的，所以进入以下行到 locations.js：

```
const mongoose = require('mongoose');
```

你将通过在 db.js 中添加一个`require`来将此文件引入应用程序。在 db.js 的末尾添加以下行：

```
require('./locations');
```

有了这些，你就已经设置好了，准备出发了。

#### 5.3.1. 设置模式的基本知识

Mongoose 为你提供了一个用于定义新模式的构造函数，你通常将其分配给一个变量，以便以后可以访问它。这个函数看起来像以下行：

```
const locationSchema = new mongoose.Schema({ });
```

实际上，这正是你将要使用的结构。将其添加到 locations.js 模型中，在`require` Mongoose 的行下面。`mongoose-Schema({ })`括号内的空对象是你要定义模式的地方。

##### 从控制器数据定义模式

将数据从视图移回到控制器的一个结果是你将能够很好地了解你需要的数据结构。从查看 app_server/controllers/locations.js 中的`homelist`控制器开始，简单起见。`homelist`控制器将要在主页上显示的数据传递给视图。图 5.8 显示了主页上某个位置的外观。

##### 图 5.8. 主页列表中显示的单个位置

![Images/05fig08_alt.jpg](img/05fig08_alt.jpg)

以下代码片段显示了控制器中找到的此位置的数据：

```
locations: [{
  name: 'Starcups',                                      *1*
  address: '125 High Street, Reading, RG6 1PS',          *2*
  rating: 3,                                             *3*
  facilities: ['Hot drinks', 'Food', 'Premium wifi'],    *4*
  distance: '100m'
}]
```

+   **1** 级名称是一个字符串。

+   **2** 级地址是另一个字符串。

+   **3** 级评分是一个数字。

+   **4** 级设施是一个字符串数组。

你稍后会回到距离，因为那需要计算。其他四个数据项相当直接：两个字符串，一个数字和一个字符串数组。根据你目前所知，你可以使用这些信息来定义一个基本的模式，如下所示：

```
const locationSchema = new mongoose.Schema({
  name: String,
  address: String,
  rating: Number,
  facilities: [String]       *1*
});
```

+   ***1* 通过在方括号内声明类型来声明相同模式类型的数组**

注意声明设施为数组的简单方法 ***1***。如果你的数组将只包含一种模式类型，例如 `String`，你可以通过将模式类型括在方括号中来定义它。

##### 分配默认值

在某些情况下，当根据你的模式创建新的 MongoDB 文档时设置默认值是有用的。在 `locationSchema` 中，`rating` 路径是一个很好的候选者。当新的位置添加到数据库中时，它还没有任何评论，因此没有评分。但你的视图期望评分在零到五星级之间，这正是控制器需要传递的。

你想要做的是为每个新文档的评分设置默认值 `0`。Mongoose 允许你在模式内部这样做。记住 `rating: Number` 是 `rating: {type: Number}` 的简写吗？嗯，你可以在定义对象中添加其他选项，包括默认值。这意味着你可以像以下这样更新模式中的评分路径：

```
rating: {
  type: Number,
  'default': 0
}
```

单词 `default` 不 *必须* 用引号括起来，但在 JavaScript 中是一个保留字；因此，使用引号是一个好主意。

##### 添加一些基本验证：必需字段

通过 Mongoose，你可以在模式级别快速添加一些基本验证。这种做法有助于维护数据完整性，并可以保护你的数据库免受缺失或格式错误的数据的影响。Mongoose 的助手使得添加一些最常见的验证任务变得容易，这意味着你不必每次都编写或导入代码。

这种类型验证的第一个例子确保在将文档保存到数据库之前，所需的字段不为空。你不必在代码中为每个所需的字段编写检查，而是可以在你决定应强制执行的每个路径的定义对象中添加 `required: true` 标志。在 `locationSchema` 中，你当然想确保每个位置都有一个名称，因此你可以像这样更新名称路径：

```
name: {
  type: String,
  required: true
}
```

如果你尝试保存一个没有名称的位置，Mongoose 会返回一个验证错误，你可以在代码中立即捕获它，而无需往返数据库。

##### 添加一些基本验证：数字边界

你可以使用类似的技术来定义你想要为数字路径设置的最大和最小值。这些验证器称为 `max` 和 `min`。你拥有的每个位置都有一个评分分配给它，你给它设置了默认值 `0`。该值不应小于 `0` 或大于 `5`。如下更新 `rating` 路径：

```
rating: {
  type: Number,
  'default': 0,
  min: 0,
  max: 5
}
```

通过这次更新，Mongoose 不会让你保存小于`0`或大于`5`的评分值。如果你尝试这样做，它会返回一个验证错误，你可以在代码中处理。这种方法的一个优点是，应用程序不需要往返数据库来检查边界。另一个优点是，你不需要在应用程序中可能添加、更新或计算评分值的每个地方编写验证代码。

#### 5.3.2\. 在 MongoDB 和 Mongoose 中使用地理数据

当你开始将应用程序的数据从控制器映射到 Mongoose 模式时，你将距离问题留到了以后。现在，我们来讨论你将如何处理地理信息。

MongoDB 可以将地理数据存储为经纬度坐标，甚至可以根据这些数据创建和管理索引。这种能力使用户能够快速搜索彼此靠近或靠近特定经纬度的地点——这对于构建基于位置的应用程序非常有帮助！

| |
| --- |

**关于 MongoDB 索引**

任何数据库系统中的索引都能使查询更快、更高效，MongoDB 也不例外。当一个路径被索引时，MongoDB 可以使用这个索引快速获取数据子集，而无需扫描集合中的所有文档。

想象一下你可能在家的文件系统。假设你需要找到一张特定的信用卡对账单。你可能把所有的文件都放在一个抽屉或柜子里。如果所有东西都是随机放入的，你将不得不翻阅所有类型的无关文件，直到找到你想要的东西。然而，如果你已经将文件索引到文件夹中，你可以快速找到你的信用卡文件夹。当你挑选出这个文件夹后，你只需查看这一组文件，这使得你的搜索更加高效。

这种场景类似于数据库中索引的工作方式。然而，在数据库中，每个文档可以有多个索引，这使你能够针对不同的查询进行高效搜索。

索引确实需要维护和数据库资源，因为正确归档文件需要时间。为了获得最佳的整体性能，尽量将数据库索引限制在需要索引且用于大多数查询的路径上。

| |
| --- |

单个地理位置的数据根据 GeoJSON 格式规范存储，你很快就会看到它的实际应用。Mongoose 支持这种数据类型，允许你在模式中定义地理空间路径。由于 Mongoose 是 MongoDB 之上的抽象层，它努力使事情变得更容易。你只需在模式中添加 GeoJSON 路径：

1.  将路径定义为数字类型的数组。

1.  将路径定义为具有`2dsphere`索引。

要将此付诸实践，你可以在位置模式中添加一个`coords`路径。如果你遵循前两个步骤，你的模式应该看起来像这样：

```
const locationSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  address: String,
  rating: {
    type: Number,
    'default': 0,
    min: 0,
    max: 5
  },
  facilities: [String],
  coords: {
    type: { type: String },
    coordinates: [Number]
  }
});
locationSchema.index({coords: '2dsphere'});
```

这里的`2dsphere`是关键部分，因为它使得 MongoDB 在运行查询和返回结果时能够进行正确的计算。它允许 MongoDB 根据球形对象计算几何形状。当你构建 API 并开始与数据交互时，你将在第六章中更多地使用这个功能。


##### 小贴士

为了满足 GeoJSON 规范，坐标对必须按照正确的顺序输入到数组中：经度，然后是纬度。有效的经度值范围从-180 到 180，而有效的纬度值范围从-90 到 90。将坐标顺序搞错是一个容易犯的错误，所以在保存位置数据到集合时请记住这一点。


你已经掌握了基础知识，Loc8r 的当前模式包含了满足主页需求所需的所有内容。接下来，是时候看看详情页面了。这个页面有更复杂的数据需求，你将看到如何使用 Mongoose 模式来处理它们。

#### 5.3.3\. 使用子文档创建更复杂的模式

你到目前为止使用的数据很简单，可以放在一个相当扁平的模式中。你已经使用了一些数组来存储设施和位置坐标，但同样，这些数组很简单，每个数组只包含单一的数据类型。现在，你将看看当你处理一个稍微复杂的数据集时会发生什么。

首先，重新熟悉一下详情页面以及它显示的数据。图 5.9 显示了包含所有不同信息区域的页面截图。名称、评分和地址位于顶部；稍低一些是设施。右侧是一张基于地理坐标的地图。你已经用基本模式覆盖了这些元素。你还没有为营业时间和客户评论准备任何内容。

##### 图 5.9\. 详情页面显示的单个位置的信息

![](img/05fig09_alt.jpg)

支持这个视图的数据目前保存在`locationInfo`控制器中，位于`app_server/controllers/locations.js`。以下列表显示了该控制器中的相关数据部分。

##### 列表 5.2\. 驱动详情页面的控制器中的数据

```
location: {
  name: 'Starcups',                                         *1*
  address: '125 High Street, Reading, RG6 1PS',             *1*
  rating: 3,                                                *1*
  facilities: ['Hot drinks', 'Food', 'Premium wifi'],       *1*
  coords: {lat: 51.455041, lng: -0.9690884},                *1*

    days: 'Monday - Friday',                                *2*
    opening: '7:00am',                                      *2*
    closing: '7:00pm',                                      *2*
    closed: false                                           *2*
  },{                                                       *2*
    days: 'Saturday',                                       *2*
    opening: '8:00am',                                      *2*
    closing: '5:00pm',                                      *2*
    closed: false                                           *2*
  },{                                                       *2*
    days: 'Sunday',                                         *2*
    closed: true                                            *2*
  }],                                                       *2*
  reviews: [{                                               *3*
    author: 'Simon Holmes',                                 *3*
    rating: 5,                                              *3*
    timestamp: '16 July 2013',                              *3*
    reviewText: 'What a great place.                        *3*
  I can\'t say enough good things about it.'              *3*
  },{                                                       *3*
    author: 'Charlie Chaplin',                              *3*
    rating: 3,                                              *3*
    timestamp: '16 June 2013',                              *3*
    reviewText: 'It was okay. Coffee wasn\'t great,         *3*
  but the wifi was fast.'                                 *3*
  }]                                                        *3*
}
```

+   ***1* 已经用现有模式覆盖**

+   ***2* 营业时间的数据被存储为对象数组。**

+   ***3* 评论也作为对象数组传递给视图。**

在这里，你拥有用于营业时间的对象数组和用于评论的对象数组。在一个关系型数据库中，你会创建这些作为单独的表，并在需要信息时在查询中`join`它们。但文档数据库（包括 MongoDB）并不是这样工作的。在文档数据库中，任何特定于父文档的内容都应该包含在**该文档**内。图 5.10 说明了这两种方法之间的概念差异。

##### 图 5.10\. 关系型数据库和文档数据库在存储与父元素相关的重复信息方面的区别

![](img/05fig10_alt.jpg)

MongoDB 提供了 *子文档* 的概念来存储这种重复的嵌套数据。子文档与文档非常相似，它们都有自己的模式；每个子文档在创建时都会由 MongoDB 分配一个唯一的 `_id`。但是子文档嵌套在文档内部，并且只能通过父文档的路径来访问。

##### 在 Mongoose 中使用嵌套模式来定义子文档

Mongoose 通过嵌套模式定义子文档——一个模式嵌套在另一个模式内部。在本节中，你将创建一个子文档模式来查看它在代码中的工作方式。第一步是定义一个子文档的新模式。从营业时间开始，创建以下模式。请注意，此模式需要与 `locationSchema` 定义在同一文件中，并且（重要的是）必须在 `locationSchema` 定义之前：

```
const openingTimeSchema = new mongoose.Schema({
  days: {
    type: String,
    required: true
  },
  opening: String,
  closing: String,
  closed: {
    type: Boolean,
    required: true
  }
});
```


**存储时间信息的选项**

在营业时间模式中，你有一个有趣的情况：你想要保存时间信息，如早上 7:30，但不需要与之关联的日期。

在这里，你使用的是 `String` 方法，因为它在放入数据库或检索后不需要任何处理。这也使得每条记录都很容易理解。缺点是它使得对数据进行任何计算处理变得更加困难。

一种选项是创建一个带有任意数据值的数据对象，并手动设置小时和分钟，例如

```
const d = new Date();
d.setHours(15);
d.setMinutes(30);           *1*
```

+   ***1* d 现在是 Sun Mar 12 2017 15:30:40 GMT+0000 (GMT)。**

使用这种方法，你可以轻松地从数据中提取时间。缺点是你会存储不必要的数据，并且这种方法在技术上是不正确的。

另一种选项是存储午夜以来的分钟数。所以早上 7:30 是 (7 × 60) + 30 = 450。当你将数据放入数据库并再次检索时，这个计算相当简单。但数据看起来没有意义。

然而，这个第二种选项是我们更倾向于使用的，可以使日期更智能，如果想要尝试新事物，这也可以是一个很好的扩展。为了提高可读性和避免干扰，你将保持使用字符串方法贯穿整本书。


此模式定义很简单，并映射来自控制器中的数据。你有两个必填字段：`closed` 布尔标志和每个子文档引用的 `days`。

在位置模式内部嵌套此模式是另一个简单的任务。你需要向父模式添加一个新的路径，并将其定义为子文档模式的数组。以下代码片段显示了如何在 `locationSchema` 内部嵌套 `openingTimeSchema`：

```
const locationSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  address: String,
  rating: {
    type: Number,
    'default': 0,
    min: 0,
    max: 5
  },
  facilities: [String],
  coords: {
    type: {type: String},
    coordinates: [Number]
  },
  openingTimes: [openingTimeSchema]      *1*
});
```

+   ***1* 通过引用另一个模式对象作为数组添加嵌套模式**

在此基础上，您可以为特定位置添加多个开馆时间子文档，这些子文档存储在该位置文档中。以下代码片段展示了基于此模式的 MongoDB 示例文档，其中开馆时间的子文档以粗体显示：

```
{
  "_id": ObjectId("52ef3a9f79c44a86710fe7f5"),
  "name": "Starcups",
  "address": "125 High Street, Reading, RG6 1PS",
  "rating": 3,
  "facilities": ["Hot drinks", "Food", "Premium wifi"],
  "coords": [-0.9690884, 51.455041],
  "openingTimes": [{                                 *1*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f6"),     *1*
    "days": "Monday - Friday",                       *1*

    "opening": "7:00am",                             *1*
    "closing": "7:00pm",                             *1*
    "closed": false                                  *1*
  }, {                                               *1*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f7"),     *1*
    "days": "Saturday",                              *1*
    "opening": "8:00am",                             *1*
    "closing": "5:00pm",                             *1*
    "closed": false                                  *1*
  }, {                                               *1*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f8"),     *1*
    "days": "Sunday",                                *1*
    "closed": true                                   *1*
  }]                                                 *1*
}
```

+   ***1* 在 MongoDB 文档中，嵌套的开馆时间子文档位于位置文档内部。**

在处理开馆时间的方案得到妥善处理后，接下来您将查看添加评论子文档方案的步骤。

##### 添加第二组子文档

MongoDB 和 Mongoose 都不限制文档中子文档路径的数量，因此您可以自由地使用您为开馆时间所做的工作，并复制此过程用于评论：

+   第一步：查看评论中使用的数据：

    ```
     {
       author: 'Simon Holmes',
       rating: 5,
       timestamp: '16 July 2013',
       reviewText: 'What a great place. I can\'t say enough good things
    about it.'
     }
    ```

+   第二步：将此代码映射到 app_server/models/ location.js 中的新 `reviewSchema`：

    ```
    const reviewSchema = new mongoose.Schema({
      author: String,
      rating: {
        type: Number,
        required: true,
        min: 0,
        max: 5
      },
      reviewText: String,
      createdOn: {
        type: Date,
        'default': Date.now
      }
    });
    ```

+   第三步：将此 `reviewSchema` 作为新路径添加到 `locationSchema`：

    ```
    const locationSchema = new mongoose.Schema({
      name: {type: String, required: true},
      address: String,
      rating: {type: Number, "default": 0, min: 0, max: 5},
      facilities: [String],
      coords: {type: { type: String }, coordinates: [Number]},
      openingTimes: [openingTimeSchema],
      reviews: [reviewSchema]
    });
    ```

当您已定义评论的方案并将其添加到主位置方案中，您就有了一切所需，以结构化的方式存储所有位置的数据。

#### 5.3.4\. 最终方案

在本节中，您在文件中做了很多工作，所以一起看看，以了解发生了什么。以下列表显示了 app_server/models 中 locations.js 文件的全部内容，定义了位置数据的方案。

##### 列表 5.3\. 最终位置方案定义，包括嵌套方案

```
const mongoose = require( 'mongoose' );                      *1*
const openingTimeSchema = new mongoose.Schema({              *2*
  days: {type: String, required: true},                      *2*
  opening: String,                                           *2*
  closing: String,                                           *2*
  closed: {                                                  *2*
    type: Boolean,                                           *2*
    required: true                                           *2*
  }                                                          *2*
});                                                          *2*
const reviewSchema = new mongoose.Schema({                   *3*
  author: String,                                            *3*
  rating: {                                                  *3*
    type: Number,                                            *3*
    required: true,                                          *3*
    min: 0,                                                  *3*
    max: 5                                                   *3*
  },                                                         *3*
  reviewText: String,                                        *3*
  createdOn: {type: Date, default: Date.now}                 *3*
});                                                          *3*
const locationSchema = new mongoose.Schema({                 *4*
  name: {
    type: String,
    required: true
  },
  address: String,
  rating: {
    type: Number,
    'default': 0,
    min: 0,
    max: 5
  },
  facilities: [String],
  coords: {
    type: {type: String },
    coordinates:[Number]                                    *5*
  },
  openingTimes: [openingTimeSchema],                        *6*
  reviews: [reviewSchema]                                   *6*
});
locationSchema.index({coords: '2dsphere'});
```

+   ***1* 需要 Mongoose 以便使用其方法**

+   ***2* 定义开馆时间的方案**

+   ***3* 定义评论的方案**

+   ***4* 开始主要位置方案的定义**

+   ***5* 使用 2dsphere 以添加对 GeoJSON 经纬度坐标对的支撑**

+   ***6* 引用开馆时间和评论方案以添加嵌套子文档**

文档和子文档都有定义其结构的方案，您还添加了一些默认值和基本验证。为了使这个场景更真实，以下列表展示了基于此方案的示例 MongoDB 文档。

##### 列表 5.4\. 基于位置方案的示例 MongoDB 文档

```
{
  "_id": ObjectId("52ef3a9f79c44a86710fe7f5"),
  "name": "Starcups",
  "address": "125 High Street, Reading, RG6 1PS",
  "rating": 3,
  "facilities": ["Hot drinks", "Food", "Premium wifi"],
  "coords": [-0.9690884, 51.455041],                                         *1*
  "openingTimes": [{                                                         *2*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f6"),                             *2*
    "days": "Monday - Friday",                                               *2*
    "opening": "7:00am",                                                     *2*
    "closing": "7:00pm",                                                     *2*
    "closed": false                                                          *2*
  }, {                                                                       *2*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f7"),                             *2*
    "days": "Saturday",                                                      *2*
    "opening": "8:00am",                                                     *2*
    "closing": "5:00pm",                                                     *2*
    "closed": false                                                          *2*
  }, {                                                                       *2*
    "_id": ObjectId("52ef3a9f79c44a86710fe7f8"),                             *2*
    "days": "Sunday",                                                        *2*
    "closed": true                                                           *2*
  }],                                                                        *2*
  "reviews": [{
    "_id": ObjectId("52ef3a9f79c44a86710fe7f9"),                             *3*
    "author": "Simon Holmes",                                                *3*
    "rating": 5,                                                             *3*
    "createdOn": ISODate("2013-07-15T23:00:00Z"),                            *3*
    "reviewText": "What a great place. I can't say enough good               *3*
things about it."                                                            *3*
  }, {                                                                       *3*
    "_id": ObjectId("52ef3a9f79c44a86710fe7fa"),                             *3*
    "author": "Charlie Chaplin",                                             *3*
    "rating": 3,                                                             *3*
    "createdOn": ISODate("2013-06-15T23:00:00Z"),                            *3*
    "reviewText": "It was okay. Coffee wasn't great, but the wifi was fast." *3*
  }]
}
```

+   ***1* 坐标存储为 GeoJSON 对 [经度，纬度]。**

+   ***2* 开馆时间存储为嵌套对象数组（子文档）。**

+   ***3* 评论也是子文档的数组。**

该列表应能给您一个关于基于已知方案的 MongoDB 文档外观的概念，包括子文档。以这种可读的形式，它是一个 JSON 对象，尽管技术上 MongoDB 以 BSON（二进制 JSON）的形式存储它。

#### 5.3.5\. 编译 Mongoose 方案到模型

当与数据交互时，应用程序不会直接与方案交互；数据交互是通过模型完成的。

在 Mongoose 中，模型是模式的编译版本。当它被编译时，模型的单个实例直接映射到数据库中的单个文档。正是通过这种直接的一对一关系，模型可以创建、读取、保存和删除数据。图 5.11 说明了这种安排。

##### 图 5.11\. 应用程序和数据库通过模型进行交互。模型的单个实例与数据库中的单个文档有一对一的关系。正是通过这种关系，管理数据的创建、读取、更新和删除。

![图片](img/05fig11_alt.jpg)

这种方法使得 Mongoose 易于使用，你将在第六章中构建应用程序的内部 API 时深入了解它。

##### 从模式编译模型

任何包含单词*compiling*的东西听起来可能有点复杂。实际上，从模式编译 Mongoose 模型是一个简单的单行任务。在调用`model`命令之前，你需要确保模式是完整的。`model`命令遵循以下结构：

![图片](img/p0145-01_alt.jpg)


##### 小贴士

MongoDB 集合名称是可选的。如果你省略了它，Mongoose 将使用模型名称的小写复数形式。例如，模型名称为`Location`时，它会寻找名为 locations 的集合名称，除非你指定了不同的名称。


由于你正在创建数据库而不是连接到现有的数据源，你可以使用默认的集合名称，因此你不需要在`model`命令中包含该参数。要构建你的位置模式模型，你可以在`locationSchema`定义下面的代码中添加以下行：

```
mongoose.model('Location', locationSchema);
```

这就是全部内容。你已经为位置定义了数据模式，并将模式编译成了可以在应用程序中使用的模型。你现在需要一些数据。

### 5.4\. 使用 MongoDB shell 创建 MongoDB 数据库并添加数据

要构建 Loc8r 应用程序，你需要创建一个新的数据库并手动添加一些测试数据。你可以创建自己的 Loc8r 个人版本进行测试，同时直接与 MongoDB 交互。

#### 5.4.1\. MongoDB shell 基础

MongoDB shell 是一个与 MongoDB 一起安装的命令行实用程序，允许你与系统上的任何 MongoDB 数据库交互。它功能强大，可以做很多事情。你只需要熟悉基础知识来启动和运行。

##### 启动 MongoDB shell

通过在终端中运行以下行进入 shell：

```
$ mongo
```

此命令应在终端中响应几行确认

+   Shell 版本

+   它连接到的服务器和端口

+   它连接到的服务器版本

这些确认行应该看起来像这样，只要版本等于或高于 4：

```
MongoDB shell version 4.0.0
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 4.0.0
```

如果您正在使用 MongoDB 的较旧版本，您可能会看到不同的消息，但通常如果命令已成功或失败是显而易见的。您也可能看到一些以 `Server has startup warnings` 开头并继续说明 `Access control is not enabled for the database` 的几行。在您的本地开发机器上，这并不是什么需要担心的事情。


##### 小贴士

当您在 shell 中时，新行以 `>` 开头，以区分标准命令行入口点。本节中打印的 shell 命令以 `>` 开头而不是 `$`，以清楚地表明您正在使用 shell，但与 `$` 一样，您不需要输入它。


##### 列出所有本地数据库

接下来是一个简单的命令，显示了所有本地 MongoDB 数据库的列表。在 shell 中输入以下行：

```
> show dbs
```

这行返回了本地 MongoDB 数据库名称及其大小的列表。如果您在此点还没有创建任何数据库，您仍然会看到两个默认数据库，它们看起来像这样：

```
admin     0.000GB
local     0.000GB
```

##### 使用特定数据库

如果您想使用特定数据库，例如默认的名为 local 的数据库，您可以使用 `use` 命令，如下所示：

```
> use local
```

shell 会响应如下信息：

```
switched to db local
```

这条消息确认了 shell 连接到的数据库的名称。

##### 列出数据库中的集合

当您使用特定数据库时，使用以下命令很容易输出其集合列表：

```
> show collections
```

如果您正在使用本地数据库，您可能会在终端看到一个单独的集合名称输出：`startup_log`。

##### 查看集合的内容

MongoDB shell 还允许您查询数据库中的集合。查询或查找操作的构造如下：

![](img/p0148-01_alt.jpg)

`query` 对象用于指定您在集合中试图查找的内容，您将在第六章中查看这个 `query` 对象的示例。（Mongoose 也使用 `query` 对象。）最简单的查询是一个空查询，它返回集合中的所有文档。如果您的集合很大，请不要担心，因为 MongoDB 会返回一个您可以分页查看的文档子集。以 `startup_log` 集合为例，您可以运行以下命令：

```
> db.startup_log.find()
```

此命令返回 MongoDB 启动日志中的几个文档，其内容在这里展示并不足够有趣。当您在设置数据库并确保一切按预期保存时，此命令非常有用。

#### 5.4.2\. 创建 MongoDB 数据库

您不需要 *创建* MongoDB 数据库；您只需要开始使用它。对于 Loc8r 应用程序，拥有一个名为 Loc8r 的数据库是有意义的。在 shell 中，您可以使用以下命令使用它：

```
> use Loc8r
```

如果您运行 `show collections` 命令，它目前不会返回任何内容，即使您运行 `show dbs`，您也看不到它。但是，在向其中保存一些数据后，您将能够看到它。

##### 创建集合和文档

同样，你不需要显式创建集合，因为当你第一次向其中保存数据时，MongoDB 会为你创建它。


**更符合你个人需求的地理位置数据**

Loc8r 是关于基于位置的数据，示例中的地点都是虚构的，地理位置上靠近西蒙在英国的居住地。你可以通过更改名称、地址和坐标来使你的版本更加个性化。

要获取您的当前坐标，请访问 [`whatsmylatlng.com`](https://whatsmylatlng.com)。页面上有一个按钮，可以通过 JavaScript 查找您的位置，这比第一次尝试更准确。请注意，坐标以纬度-经度顺序显示给您，您需要将它们翻转以便数据库中经度在前。

要获取任何地址的坐标，你可以使用 [`mygeoposition.com`](http://mygeoposition.com)。此网站允许你输入地址或拖放指针以获取地理坐标。再次提醒，MongoDB 中的对必须先经度后纬度。


为了匹配 `Location` 模型，你将需要一个 `locations` 集合。记住，默认的集合名称是模型名称的复数小写版本。你可以通过将数据对象传递给集合的 `save` 命令来创建和保存一个新的文档，如下面的代码片段所示：

```
> db.locations.save({                               *1*
  name: 'Starcups',
  address: '125 High Street, Reading, RG6 1PS',
  rating: 3,
  facilities: ['Hot drinks', 'Food', 'Premium wifi'],
  coords: [-0.9690884, 51.455041],
  openingTimes: [{
    days: 'Monday - Friday',
    opening: '7:00am',
    closing: '7:00pm',
    closed: false
  }, {
    days: 'Saturday',
    opening: '8:00am',
    closing: '5:00pm',
    closed: false
  }, {
    days: 'Sunday',
    closed: true
  }]
})
```

+   ***1* 注意在保存命令中指定的集合名称**

在一步中，你创建了 `Loc8r` 数据库和新的 `locations` 集合，并将第一个文档添加到集合中。如果你现在在 MongoDB shell 中运行 `show dbs`，你应该会看到新的 `Loc8r` 数据库与其他数据库一起返回，如下所示：

```
> show dbs
Loc8r    0.000GB
admin    0.000GB
local    0.000GB
```

现在当你运行 MongoDB shell 中的 `show collections` 时，你应该会看到返回的新 `locations` 集合：

```
> show collections
locations
```

你可以查询集合以找到文档。目前只有一个文档，所以返回的信息很少。你还可以在集合上使用 `find` 命令：

```
> db.locations.find()                                          *1*
{
  "_id": ObjectId("530efe98d382e7fa4345f173"),                 *2*
  "address": "125 High Street, Reading, RG6 1PS",
  "coords": [-0.9690884, 51.455041],
  "facilities": ["Hot drinks", "Food", "Premium wifi"],
  "name": "Starcups",
  "openingTimes": [{
    "days": "Monday - Friday",
    "opening": "7:00am",
    "closing": "7:00pm",
    "closed": false
  }, {
    "days": "Saturday",
    "opening": "8:00am",
    "closing": "5:00pm",
    "closed": false
  }, {
    "days": "Sunday",
    "closed": true
  }],
  "rating": 3,
}
```

+   ***1* 记得在集合本身上运行查找操作。**

+   ***2* MongoDB 已经为该文档自动添加了一个唯一标识符。**

此代码片段已格式化以提高可读性；MongoDB 返回给 shell 的文档不会有换行符和缩进。但如果你在命令末尾添加 `.pretty()`，MongoDB shell 可以为你美化它，如下所示：

```
> db.locations.find().pretty()
```

注意，返回文档中的数据顺序与您提供的数据对象中的顺序不匹配。由于数据结构不是基于列的，MongoDB 如何存储文档内的单个路径顺序并不重要。数据始终在正确的路径中，并且数组内部的数据保持相同的顺序。

##### 添加子文档

你可能已经注意到，你的第一个文档没有完整的数据集；没有审查子文档。你可以像处理营业时间那样，在初始的 `save` 命令中添加它们，或者你可以更新现有文档并将它们推入。

MongoDB 有一个 `update` 命令，它接受两个参数：一个查询，以便它知道要更新哪个文档，以及当它找到文档时要执行的操作的说明。在这个阶段，你可以进行一个简单的查询，通过名称（Starcups）查找位置，因为你知道没有重复项。对于指令对象，你可以使用一个 `$push` 命令向评论路径添加一个新的对象。即使评论路径尚不存在，MongoDB 也会在推送操作中将其添加。

将所有这些放在一起，就像以下代码片段所示：

```
> db.locations.update({                        *1*
  name: 'Starcups'                             *1*
}, {
  $push: {                                     *2*
    reviews: {                                 *2*
      author: 'Simon Holmes',                  *3*
      _id: ObjectId(),                         *3*
      rating: 5,                               *3*
      timestamp: new Date("Mar 12, 2017"),     *3*
      reviewText: "What a great place."        *3*
    }
  }
})
```

+   ***1* 从查询对象开始以找到正确的文档**

+   ***2* 当文档找到时，将子文档推入评论路径**

+   ***3* 子文档包含此数据**

如果你使用 `Loc8r` 数据库在 MongoDB 壳中运行该命令，你将向文档添加一个评论。你可以根据需要重复该命令，更改数据以添加多个评论。

你可能已经注意到，在这里，你指定了 `_id` 属性并将其赋值为 `ObjectId()`。MongoDB 不会像对文档那样自动将 `_id` 添加到子文档中，但这个特性对你来说将非常有用。给评论子文档赋值 `ObjectId()` 告诉 MongoDB 为此子文档创建一个新的唯一标识符。

注意设置评论时间戳的 `new Date()` 函数调用。使用此时间戳确保 MongoDB 将日期存储为 ISO 日期对象，而不是字符串——这是你的模式所期望的，并且允许对日期数据进行更高级的操作。

##### 重复此过程

这几个命令已经为你提供了一个测试应用程序的位置，但理想情况下，你需要更多。向你的数据库添加一些更多位置。

完成此操作并设置好数据后，你几乎可以开始从应用程序中使用它了。在这种情况下，你将构建一个 API。但在你跳转到第六章（[kindle_split_017.xhtml#ch06](https://example.org/kindle_split_017.xhtml#ch06)）中的任务之前，还有一项家务要做。你想要定期向 Heroku 推送更新，现在你已经向应用程序添加了数据库连接和数据模型，你需要确保这些更新在 Heroku 上得到支持。

### 5.5\. 使数据库上线

如果您的应用程序已经发布到野外，将数据库放在本地主机上是没有用的。您的数据库也需要外部可访问。在本节中，您将把数据库推送到一个实时环境中，并更新您的 Loc8r 应用程序，使其使用发布站点的已发布数据库和开发站点的本地主机数据库。您将首先使用一个名为 mLab 的服务的免费层，该服务可以用作 Heroku 的附加组件。如果您有其他首选提供商或自己的数据库服务器，那没问题。本节的第一部分将介绍在 mLab 上的设置，但以下部分——迁移数据和在 Node 应用程序中设置连接字符串——不是平台特定的。

#### 5.5.1\. 设置 mLab 并获取数据库 URI

第一个目标是获取一个外部可访问的数据库 URI，以便您可以将数据推送到它并将其添加到应用程序中。您将使用 mLab 来实现这一目的，因为它有一个良好的免费层，优秀的在线文档，以及响应的支持团队。

您有几种在 mLab 上设置数据库的方法。最快、最简单的方法是通过 Heroku 的附加组件。这里您将使用这种方法，但它确实要求您在 Heroku 上注册一张有效的信用卡。Heroku 在您通过其生态系统使用附加组件时要求您这样做，以保护自己免受滥用行为。使用 mLab 的免费沙盒层不会产生任何费用。如果您不习惯使用信用卡直接通过 Heroku 设置您的 mLab 数据库，请查看侧边栏“手动设置 mLab”以获取有关手动设置 mLab 数据库并将其连接到您的 Heroku 应用程序的详细信息。如果您选择手动设置数据库，请勿遵循 Heroku 附加组件安装的说明；否则，您最终会拥有与您的应用程序关联的多个数据库。

| |
| --- |

**手动设置 mLab**

如果您不想使用 Heroku 附加组件系统，您可以这样做。您想要做的是在云中设置一个 MongoDB 数据库，并获取其连接字符串。

mLab 文档可以指导您完成此过程；请参阅[`docs.mlab.com`](https://docs.mlab.com)。

简而言之，步骤如下

1.  注册一个免费账户。

1.  创建一个新的数据库（对于免费层，选择单节点，沙盒）。

1.  添加一个用户。

1.  获取数据库 URI（连接字符串）。

连接字符串看起来可能如下所示：

```
mongodb://dbuser:dbpassword@ds059957.mlab.com:59957/loc8r-dev
```

当然，所有这些部分对于您来说都会有所不同，您将不得不将用户名和密码替换为步骤 3 中指定的内容。

当您拥有完整的连接字符串时，您应该将其保存为 Heroku 配置的一部分。在您的应用程序根目录的终端提示符下，您可以使用以下命令来完成此操作：

```
$ heroku config:set MLAB_URI=your_db_uri
```

将`your_db_uri`替换为包括`mongodb://`协议在内的完整连接字符串。快速且简单的方法会自动在 Heroku 配置中创建`MLAB_URI`设置。这些手动步骤将你带到与快速方法相同的位置，你可以跳回正文。


##### 将 mLab 附加组件添加到 Heroku 应用程序中

通过终端将 mLab 作为 Heroku 附加组件添加的最快方式是通过以下命令（使用 mLab 的旧名称 MongoLab）：确保你在应用程序的根目录中，并运行以下命令：

```
$ heroku addons:create mongolab
```

令人难以置信，就是这样！你已经在云端准备好并等待使用 MongoDB 数据库了。你可以通过以下命令来证明这一点并打开这个新数据库的 Web 界面：

```
$ heroku addons:open mongolab
```

要使用数据库，你需要知道它的 URI。

##### 获取数据库 URI

你可以通过命令行获取完整的数据库 URI。这种方法提供了你可以在应用程序中使用的完整连接字符串，同时也显示了你需要将数据推送到数据库的各种组件。

获取数据库 URI 的命令是

```
$ heroku config:get MONGODB_URI
```

此命令输出完整的连接字符串，看起来像这样：

```
mongodb://heroku_t0zs37gc:1k3t3pgo8sb5enovqd9sk314gj@ds159330.mlab.com:59330/
     heroku_t0zs37gc
```

保留你的版本，因为你在应用程序中很快就会用到它。首先，你需要将其分解为其组成部分。

##### 将 URI 分解为其组成部分

URI 看起来像是一团乱码，但你可以将其分解以使其有意义。从 5.1.2 部分，你知道这是数据库 URI 的构建方式：

![图片](img/p0154-01_alt.jpg)

将 mLab 提供的 URI 分解成类似以下的形式：

+   *用户名*—`heroku_t0zs37gc`

+   *密码*—`1k3t3pgo8sb5enovqd9sk314gj`

+   *服务器地址*—`ds159330.mlab.com`

+   *端口号*—`59330`

+   *数据库名称*—`heroku_t0zs37gc`

这些示例来自示例 URI。当然，你的将会有所不同，但请注意它们；它们会很有用。

#### 5.5.2. 推送数据

现在你已经设置了一个外部可访问的数据库，并且知道了连接到它的所有详细信息，你可以将数据推送到它。以下是步骤：

1.  导航到你的机器上的一个目录，这个目录适合存放数据转储。

1.  从你的开发 Loc8r 数据库中转储数据。

1.  将数据恢复到你的实时数据库。

1.  测试实时数据库。

所有这些步骤都可以通过终端快速完成，所以这就是你将要做的。这样可以避免在不同环境之间跳转。

##### 导航到一个合适的目录

当你在命令行中运行数据转储命令时，它会在当前目录下创建一个名为/dump 的文件夹，并将数据转储放在里面。因此，第一步是在终端导航到硬盘上的一个合适位置。你的主目录或文档文件夹都可以，或者如果你更喜欢，可以创建一个特定的文件夹。

##### 从开发数据库中转储数据

数据转储听起来像是您从本地开发版本中删除了所有内容，但这并不是情况。这个过程更像是导出而不是丢弃。

使用的命令是`mongodump`，它可以接受许多参数，其中您需要这两个：

+   `-h`—主机服务器（和端口）

+   `-d`—数据库名称

将所有这些放在一起，并使用默认的 MongoDB 端口`27017`，您应该得到以下类似的命令：

```
$ mongodump -h localhost:27017 -d Loc8r
```

运行该命令，您将有一个临时数据转储。

##### 将数据恢复到您的实时数据库

将数据推送到您的实时数据库的过程类似，这次使用`mongorestore`命令。此命令期望以下参数：

+   `-h`—实时主机和端口

+   `-d`—实时数据库名称

+   `-u`—实时数据库用户名

+   `-p`—实时数据库密码

+   数据转储目录的路径和数据库名称（位于命令末尾，没有像其他参数那样的对应标志）

将所有这些放在一起，使用您关于数据库 URI 的信息，您应该得到以下类似的命令：

```
$ mongorestore -h ds159330.mlab.com:59330 -d heroku_t0zs37gc
     -u heroku_t0zs37gc -p 1k3t3pgo8sb5enovqd9sk314gj dump/
```

您的命令将略有不同，当然，因为您将有一个不同的主机、实时数据库名称、用户名和密码。当您运行`mongorestore`命令时，它将数据转储中的数据推送到您的实时数据库。

##### 测试实时数据库

MongoDB shell 不仅限于访问本地机器上的数据库。您还可以使用 shell 连接到外部数据库（当然，如果您有正确的凭证的话）。

要将 MongoDB shell 连接到外部数据库，您使用相同的`mongo`命令，但添加您想要连接到的数据库的信息。您需要包括主机名、端口和数据库名称，如果需要，您还可以提供用户名和密码。使用以下结构：

```
$ mongo hostname:port/database_name -u username -p password
```

使用您在本节中查看的设置，您将得到以下命令：

```
$ mongo ds159330.mlab.com:59330/heroku_t0zs37gc -u heroku_t0zs37gc -p
1k3t3pgo8sb5enovqd9sk314gj
```

此命令通过 MongoDB shell 将您连接到数据库。当连接建立后，您可以使用您已经使用过的命令来查询它，例如

```
> show collections
> db.locations.find()
```

现在，您有两个数据库和两个连接字符串。在正确的时间使用正确的一个是重要的。

#### 5.5.3\. 使应用程序使用正确的数据库

您在本地机器上有原始的开发数据库，以及在上面的 mLab（或其他地方）上的新实时数据库。您希望在开发应用程序时继续使用开发数据库，并且希望应用程序的实时版本使用实时数据库。然而，两者都使用相同的源代码。图 5.12 显示了问题。

##### 图 5.12。源代码在两个位置运行，每个位置都需要连接到不同的数据库。

![图 5.12](img/05fig12_alt.jpg)

现在，您有一组源代码在两个环境中运行，每个环境都应该使用不同的数据库。处理此问题的方法是使用 Node 环境变量`NODE_ENV`。

##### NODE_ENV 环境变量

环境变量会影响核心进程的运行方式，你将在这里查看并使用的是 `NODE_ENV`。应用程序已经使用了 `NODE_ENV`；你不会在任何地方看到它被暴露出来。默认情况下，Heroku 应该将 `NODE_ENV` 设置为 `production`，这样应用程序就会在其服务器上以生产模式运行。


**确保 Heroku 使用生产模式**

在某些情况下，根据应用程序的设置方式，Heroku 应用程序可能不会以生产模式运行。你可以使用以下终端命令来确保 Heroku 环境变量设置正确：

```
$ heroku config:set NODE_ENV=production
```

你可以通过使用此命令的 `get` 版本来验证此设置，如下所示：

```
$ heroku config:get NODE_ENV
```


你可以在应用程序的任何地方使用以下语句来读取 `NODE_ENV`：

```
process.env.NODE_ENV
```

如果你的环境没有指定，此语句将返回 `undefined`。你可以通过在启动 Node 应用程序的命令前添加赋值来指定不同的环境变量，如下例所示：

```
$ NODE_ENV=production nodemon
```

此命令以生产模式启动应用程序，并将 `process.env.NODE_ENV` 的值设置为 `production`。


##### 小贴士

不要在应用程序内部设置 `NODE_ENV`；只需读取它。


##### 根据环境设置数据库 URI

你的应用程序的数据库连接存储在 `app_server/models` 文件夹中的 `db.js` 文件中。此文件中的连接部分目前看起来像这样：

```
const dbURI = 'mongodb://localhost/Loc8r';
mongoose.connect(dbURI);
```

根据当前环境更改 `dbURI` 的值就像使用一个 `if` 语句来检查 `NODE_ENV` 一样简单。下面的代码片段展示了如何使用它来传递你的实时 MongoDB 连接。请记住使用你自己的 MongoDB 连接字符串，而不是示例中的那个：

```
let dbURI = 'mongodb://localhost/Loc8r';
if (process.env.NODE_ENV === 'production') {
  dbURI =
     'mongodb://heroku_t0zs37gc:1k3t3pgo8sb5enosk314gj@ds159330.mlab.com:5933
    0/ heroku_t0zs37gc';
}
mongoose.connect(dbURI);
```

如果源代码将存储在公共仓库中，你可能不希望每个人都拥有你数据库的登录凭证。绕过这种情况的一种方法是用环境变量。在 Heroku 上的 mLab，你自动设置了一个；这是你最初获取连接字符串的方式。（如果你手动设置了 mLab 账户，这个变量就是你在 Heroku 上设置的配置变量。）如果你使用的是没有添加任何内容的 Heroku 配置的不同提供商，你可以使用你之前用来确保 Heroku 以生产模式运行的 `heroku config:set` 命令来添加你的 URI。

以下代码片段展示了如何使用在环境变量中设置的连接字符串：

```
let dbURI = 'mongodb://localhost/Loc8r';
if (process.env.NODE_ENV === 'production') {
  dbURI = process.env.MONGODB_URI;
}
mongoose.connect(dbURI, { useNewUrlParser: true });
```

现在，你可以分享你的代码，但只有你保留对数据库凭证的访问权限。

##### 发布前测试

在将代码推送到 Heroku 之前，你可以在终端启动应用程序时设置环境变量来在本地测试此代码更新。你之前设置的 Mongoose 连接事件在数据库连接时会在控制台输出日志，验证所使用的 URI。

要做到这一点，你需要在 nodemon 命令前添加`NODE_ENV`和`MJONGODB_URI`环境变量，如下所示（注意以下所有内容应作为一行输入）：

```
$ NODE_ENV=production
     MONGODB_URI=mongodb://<username>:<password>@<hostname>:<port>/<database>
     nodemon
```

现在启动时的控制台日志应该看起来像这样：

```
Mongoose connected to
     mongodb://heroku_t0zs37gc:1k3t3pgo8sb5enosk314gj@ds159330.mlab.com:59330
      / heroku_t0zs37gc
```

当运行此命令时，你可能会注意到 Mongoose 连接确认在生产环境中出现得较慢，这是由于使用单独的数据库服务器导致的延迟。这就是为什么在应用程序启动时打开数据库连接并保持其打开状态是个好主意。

##### 在 Heroku 上测试

如果你的本地测试成功，并且你可以通过临时以生产模式启动应用程序来连接到远程数据库，那么你就可以将其推送到 Heroku 了。使用与正常相同的命令来推送代码的最新版本：

```
$ git add --all
$ git commit –m "Commit message here"
$ git push heroku master
```

Heroku 允许你通过运行终端命令查看最新的 100 行日志。你可以检查这些日志以查看控制台日志消息的输出，其中之一将是你的`Mongoose connected to`日志。要查看日志，请在终端中运行以下命令：

```
$ heroku logs
```

此命令将最新的 100 行输出到终端窗口，最新的消息在底部。向上滚动，直到找到类似以下内容的`Mongoose connected to`消息：

```
2017-04-14T07:01:22.066997+00:00 app[web.1]: Mongoose connected to
     mongodb://heroku_t0zs37gc:1k3t3pgo8sb5enosk314gj@ds159330.mlab.com:59330/
     heroku_t0zs37gc
```

当你看到这条消息时，你就知道 Heroku 上的实时应用程序正在连接到你的实时数据库。

因此，这是定义和建模的数据，你的 Loc8r 应用程序已连接到数据库。但你现在还没有与数据库进行任何交互。接下来就是了！


**获取源代码**

到目前为止的应用程序源代码可在 GitHub 上找到，位于 gettingMean-2 存储库的 chapter-05 分支。在终端的新文件夹中，输入以下命令以克隆它并安装 npm 模块依赖项：

```
$ git clone -b chapter-05 https://github.com/cliveharber/
            gettingMean-2.git
$ cd gettingMean-2
$ npm install
```


在第六章中，你将使用 Express 创建一个 REST API，以便通过 Web 服务访问数据库。

### 概述

本章中，你学习了

+   一些将 MongoDB 数据库连接到 Express 应用程序使用 Mongoose 的方法

+   管理 Mongoose 连接的最佳实践

+   如何使用 Mongoose 模式建模数据

+   模式如何编译成模型

+   使用 MongoDB shell 直接与数据库工作

+   将数据库推送到实时 URI

+   从不同的环境连接到不同的数据库

## 第六章\. 编写 REST API：将 MongoDB 数据库暴露给应用程序

*本章涵盖*

+   检查 REST API 的规则

+   评估 API 模式

+   处理典型的 CRUD 函数（创建、读取、更新、删除）

+   使用 Express 和 Mongoose 与 MongoDB 交互

+   测试 API 端点

当你进入本章时，你已经设置了 MongoDB 数据库，但你只能通过 MongoDB shell 与之交互。在本章的过程中，你将构建一个 REST API，以便你可以通过 HTTP 调用与数据库交互并执行常见的 CRUD 功能：创建、读取、更新和删除。

你将主要使用 Node 和 Express，利用 Mongoose 来帮助进行交互。图 6.1 显示了本章在整个架构中的位置。

##### 图 6.1\. 本章重点在于构建与数据库交互的 API，为应用程序提供一个通信接口。

![](img/06fig01_alt.jpg)

你将从查看 REST API 的规则开始。我们将讨论正确定义 URL 结构的重要性，以及用于不同操作的不同的请求方法（`GET`、`POST`、`PUT`和`DELETE`），以及 API 应该如何响应数据和适当的 HTTP 状态码。当你掌握了这些知识后，你将开始构建 Loc8r 的 API，涵盖所有典型的 CRUD 操作。我们将沿途讨论 Mongoose，并涉及一些 Node 编程和更多的 Express 路由。

| |
| --- |

##### 注意

如果你还没有从第五章构建应用程序，你可以从 GitHub 的 chapter-05 分支[`github.com/cliveharber/gettingMean-2`](https://github.com/cliveharber/gettingMean-2)获取代码。在终端的新文件夹中，输入以下命令以克隆它并安装 npm 模块依赖项：

```
$ git clone -b chapter-05 https://github.com/cliveharber/
  gettingMean-2.git
$ cd gettingMean-2
$ npm install
```

| |
| --- |

### 6.1\. REST API 的规则

我们将从回顾 REST API 的特点开始。从第二章中，你可能还记得

+   *REST*代表*表示性状态转移*，这是一种架构风格，而不是严格的协议。REST 是无状态的；它对任何当前用户状态或历史没有任何概念。

+   *API*是*应用程序程序接口*的缩写，它使应用程序能够相互通信。

REST API 是应用程序的无状态接口。在 MEAN 栈的情况下，REST API 用于创建数据库的无状态接口，使其他应用程序能够与数据交互。

REST API 有一套相关的标准。虽然你不必坚持这些标准来构建自己的 API，但通常最好这样做，因为这意味着你创建的任何 API 都将遵循相同的方法。这也意味着如果你决定将你的 API 公开，你已经习惯了以“正确”的方式做事。

在基本术语中，REST API 接收传入的 HTTP 请求，进行一些处理，并始终发送回一个 HTTP 响应，如图 6.2 所示。

##### 图 6.2\. REST API 接收传入的 HTTP 请求，进行一些处理，并返回 HTTP 响应。

![](img/06fig02_alt.jpg)

你将遵循 Loc8r 的标准围绕请求和响应。

#### 6.1.1\. 请求 URL

REST API 的请求 URL 有一个简单的标准。遵循这个标准可以使您的 API 易于理解、使用和维护。

处理这个任务的思路是开始考虑您的数据库中的集合，因为您通常为每个集合有一组 API URL。您也可能为每组子文档有一组 URL。集合中的每个 URL 都有相同的基本路径，其中一些可能有额外的参数。

在一组 URL 中，您需要涵盖几个操作，通常基于标准的 CRUD 操作。您可能希望执行的一些常见操作是

+   创建一个新项目

+   读取多个项目的列表

+   读取特定项目

+   更新特定项目

+   删除特定项目

以 Loc8r 为例，数据库中有一个位置集合，您希望与之交互。表 6.1 展示了该集合的 URL 路径可能的样子。请注意，所有 URL 都有相同的基路径，并且在使用时，都有相同的地理位置 ID 参数。

##### 表 6.1\. 位置集合 API 的 URL 路径和参数

| 操作 | URL 路径 | 示例 |
| --- | --- | --- |
| 创建新位置 | /locations | [`loc8r.com/api/locations`](http://loc8r.com/api/locations) |
| 读取位置列表 | /locations | [`loc8r.com/api/locations`](http://loc8r.com/api/locations) |
| 读取特定位置 | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 更新特定位置 | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 删除特定位置 | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |

如表 6.1 所示，每个操作都有相同的 URL 路径，其中三个操作期望相同的参数来指定位置。这种情况提出了一个明显的问题：您如何使用相同的 URL 来启动不同的操作？答案在于请求方法。

#### 6.1.2\. 请求方法

HTTP 请求可以有不同方法，这些方法本质上告诉服务器执行什么类型的操作。最常见的一种请求是`GET`请求——这是您在浏览器地址栏中输入 URL 时使用的方法。另一种常见的方法是`POST`，通常用于提交表单数据。

表 6.2 展示了您在 API 中将使用的各种方法、它们的典型用途以及您期望返回的内容。

##### 表 6.2\. REST API 中使用的四种请求方法

| 请求方法 | 用途 | 响应 |
| --- | --- | --- |
| POST | 在数据库中创建新数据 | 数据库中看到的新数据对象 |
| GET | 从数据库读取数据 | 响应请求的数据对象 |
| PUT | 更新数据库中的文档 | 数据库中看到已更新的数据对象 |
| DELETE | 从数据库中删除对象 | Null |

您将使用的四种 HTTP 方法是 `POST`、`GET`、`PUT` 和 `DELETE`。如果您查看 Use 列中的相应条目，您会注意到每种方法都执行不同的 CRUD 操作。

方法很重要，因为设计良好的 REST API 通常对于不同的操作具有相同的 URL。在这些情况下，方法告诉服务器执行哪种类型的操作。我们将在本章后面讨论如何在 Express 中构建和组织方法的路由。

如果您将路径和参数映射到适当的请求方法，您可以为您的 API 制定一个计划，如 表 6.3 所示。

##### 表 6.3\. 将 URL 与所需操作链接的请求方法，使 API 能够使用相同的 URL 执行不同的操作

| 操作 | 方法 | URL 路径 | 示例 |
| --- | --- | --- | --- |
| 创建新位置 | POST | /locations | [`loc8r.com/api/locations`](http://loc8r.com/api/locations) |
| 读取位置列表 | GET | /locations | [`loc8r.com/api/locations`](http://loc8r.com/api/locations) |
| 读取特定位置 | GET | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 更新特定位置 | PUT | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 删除特定位置 | DELETE | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |

表 6.3 展示了您将用于与位置数据交互的路径和方法。有五个操作，但只有两种 URL 模式，因此您可以使用请求方法来获取所需的结果。

Loc8r 目前只有一个集合，因此这是您的起点。但 Locations 集合中的文档确实有作为子文档的评论，所以您也会很快将它们映射出来。

子文档的处理方式类似，但需要额外的参数。每个请求都需要指定位置的 ID，某些请求还需要指定评论的 ID。表 6.4 展示了操作列表及其相关的方法、URL 路径和参数。

##### 表 6.4\. 与子文档交互的 URL 规范；每个基本 URL 路径都必须包含父文档的 ID

| 操作 | 方法 | URL 路径 | 示例 |
| --- | --- | --- | --- |
| 创建新评论 | POST | /locations/:locationid/reviews | [`loc8r.com/api/locations/123/reviews`](http://loc8r.com/api/locations/123/reviews) |
| 读取特定评论 | GET | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |
| 更新特定评论 | PUT | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |
| 删除特定评论 | DELETE | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |

你可能已经注意到，对于子文档，你没有“读取评论列表”的操作，因为你将作为主文档的一部分检索评论列表。前面的表格应该给你一个如何创建基本的 API 请求规范的想法。URL、参数和操作将因应用程序而异，但方法应该保持一致。

那就是关于请求的故事。流程的另一部分，在你陷入某些代码之前，是响应。

#### 6.1.3\. 响应和状态码

一个好的 API 就像一个好的朋友。如果你去击掌，一个好的朋友不会让你感到无助。同样的，一个好的 API。如果你发起一个请求，一个好的 API 总是响应，不会让你感到无助。每个 API 请求都应该返回一个响应。好的 API 和坏的 API 之间的对比在图 6.3 中展示。

##### 图 6.3\. 一个好的 API 总是返回响应，不应该让你感到无助。

![](img/06fig03_alt.jpg)

对于一个成功的 REST API，标准化响应与标准化请求格式一样重要。响应有两个关键组成部分：

+   返回的数据

+   HTTP 状态码

将返回的数据与适当的状态码结合起来，应该给请求者提供所有继续所需的信息。

##### 从 API 返回数据

你的 API 应该返回一致的数据格式。REST API 的典型格式是 XML 和/或 JSON。你将使用 JSON 作为你的 API，因为它与 MEAN 栈是天然的匹配。MongoDB 输出 JSON，Node 和 Angular 都可以原生理解。毕竟，JSON 是 JavaScript 传输数据的方式。JSON 也比 XML 更紧凑，因此可以通过减少所需的带宽来帮助提高 API 的响应时间和效率。

你的 API 将为每个请求返回三件事之一：

+   包含响应查询数据的 JSON 对象

+   包含错误数据的 JSON 对象

+   空响应

在本章中，我们将讨论如何在构建 Loc8r API 的过程中完成所有这些事情。除了响应数据外，REST API 还应返回正确的 HTTP 状态码。

##### 使用 HTTP 状态码

一个好的 REST API 应该返回正确的 HTTP 状态码。大多数人熟悉的状态码是 404，这是当用户请求一个找不到的页面时，Web 服务器返回的状态码。这个错误码可能是互联网上最普遍的一个，但还有几十个其他的状态码，它们与客户端错误、服务器错误、重定向和成功请求有关。表 6.5 显示了 10 个最受欢迎的 HTTP 状态码以及它们在构建 API 时可能的有用之处。

##### 表 6.5\. 最受欢迎的 HTTP 状态码及其可能用于向 API 请求发送响应的方式

| 状态码 | 名称 | 用例 |
| --- | --- | --- |
| 200 | OK | 成功的 GET 或 PUT 请求 |
| 201 | 已创建 | 成功的 POST 请求 |
| 204 | 无内容 | 成功的 DELETE 请求 |
| 400 | 错误请求 | 由于内容无效而导致的 GET、POST 或 PUT 请求失败 |
| 401 | 未授权 | 使用不正确的凭据请求受限制的 URL |
| 403 | 禁止 | 进行不允许的请求 |
| 404 | 未找到 | 由于 URL 参数错误而导致的请求失败 |
| 405 | 不允许的方法 | 对于给定的 URL 不允许请求方法 |
| 409 | 冲突 | 当存在具有相同数据的另一个对象时，POST 请求失败 |
| 500 | 内部服务器错误 | 服务器或数据库服务器出现问题 |

当你阅读本章并构建 Loc8r API 时，你将使用这些状态码中的几个来返回适当的数据。

### 6.2. 设置 Express 中的 API

你已经对 API 需要执行的操作和执行这些操作所需的 URL 路径有了很好的了解。正如你在 第四章 中所知，为了使 Express 根据传入的 URL 请求执行某些操作，你需要设置控制器和路由。控制器执行操作，而路由将传入的请求映射到适当的控制器。

你已经在应用程序中为路由和控制器设置了文件，所以你可以使用这些文件。然而，更好的选择是将 API 代码保持独立，这样你就不必担心应用程序中的混淆和复杂化。实际上，这是创建 API 的原因之一。此外，将 API 代码保持独立使得在未来某个时刻将其剥离并放入一个单独的应用程序中变得更加容易。你确实希望实现轻松解耦。

你想要做的第一件事是在应用程序中为创建 API 的文件创建一个单独的区域。在应用程序的顶层，创建一个名为 app_api 的新文件夹。如果你一直在跟随并构建应用程序，则此文件夹位于 app_server 文件夹旁边。

此文件夹包含与 API 相关的所有特定内容：路由、控制器和模型。当你设置好一切后，看看一些测试这些 API 占位符的方法。

#### 6.2.1. 创建路由

正如你在主 Express 应用程序的路由中做的那样，你将在 app_api/routes 文件夹中有一个 index.js 文件，它将包含你将在 API 中使用的所有路由。首先，在主应用程序文件 app.js 中引用此文件。

##### 在应用程序中包含路由

第一步是告诉你的应用程序你正在添加更多需要关注的路由，以及何时应该使用它们。你可以在 app.js 中复制一行来 `require` 服务器应用程序的路由，并将路径设置为 API 路由，如下所示：

```
const indexRouter = require('./app_server/routes/index');
const apiRouter = require('./app_api/routes/index');
```

你可能在 app.js 中还有一行代码，仍然包含示例`user`路由。如果你有的话，现在可以删除它，因为你不需要它。接下来，你需要告诉应用程序何时使用这些路由。你目前在 app.js 中有以下行，告诉应用程序检查服务器应用程序路由以处理所有传入的请求：

```
app.use('/', indexRouter);
```

注意第一个参数是`'/'`。这个参数允许你指定一个子集的 URL，对于这些 URL，路由将适用。你将定义所有以/api/开头的 API 路由。通过添加以下代码片段中显示的行，你可以告诉应用程序仅在路由以/api/开头时使用 API 路由：

```
app.use('/', indexRouter);
app.use('/api', apiRouter);
```

如前所述，如果你有，可以删除类似的`user`路由行。现在，是时候设置这些 URL 了。

##### 在路由中指定请求方法

到目前为止，你只在路由中使用了`GET`方法，如你的主应用程序路由中的以下代码片段所示：

```
router. get ('/location', ctrlLocations.locationInfo);
```

使用其他方法——`POST`、`PUT`和`DELETE`——就像将`get`与相应的关键字`post`、`put`和`delete`交换一样简单。以下代码片段展示了使用`POST`方法创建新位置的示例：

```
router.post('/locations', ctrlLocations.locationsCreate);
```

注意，你不需要在路径的开头指定/api。你在 app.js 中指定，只有当路径以/api 开头时才使用这些路由，因此假设此文件中指定的所有路由都带有/api 前缀。

##### 指定必需的 URL 参数

API URL 中包含用于识别特定文档或子文档的参数是很常见的——在 Loc8r 的情况下，这些参数是位置和评论。在路由中指定这些参数很简单；在定义每个路由时，你只需在参数名称前加上冒号即可。

假设你正在尝试访问一个 ID 为`abc`的评论，该评论属于 ID 为`123`的位置。你的 URL 路径可能如下所示：

```
/api/locations/:locationid/reviews/:reviewid
```

将 ID 替换为参数名称（带有冒号前缀）给出如下路径：

```
/api/locations/:locationid/reviews/:reviewid
```

使用这样的路径，Express 只会匹配匹配该模式的 URL。因此，必须指定位置 ID，并且它必须在 URL 中的 locations/和/reviews 之间。同样，必须在 URL 的末尾指定评论 ID。当将这样的路径分配给控制器时，参数将在代码中使用，其名称由路径指定（在本例中为`locationid`和`reviewid`）。

我们将在稍后详细说明如何访问它们，但首先，你需要设置 Loc8r API 的路由。

##### 定义 Loc8r API 路由

现在你已经知道如何设置路由以接受参数，你也知道你想要在 API 中拥有的操作、方法和路径。你可以结合所有这些知识来创建 Loc8r API 的路由定义。

如果你还没有这样做，你应该在 app_api/routes 文件夹中创建一个 index.js 文件。为了控制单个文件的大小，将位置和评论控制器分别放入不同的文件中。

您还将使用一种在 Express 中定义路由的不同方法，这对于管理单个路由上的多个方法非常理想。使用这种方法，您首先定义路由，然后链式连接不同的 HTTP 方法。这个过程简化了路由定义，使它们更容易阅读。

下面的列表显示了定义的路由应该如何看起来。

##### 列表 6.1\. 在 app_api/routes/index.js 中定义的路由

```
const express = require('express');
const router = express.Router();
const ctrlLocations = require('../controllers/locations');    *1*
const ctrlReviews = require('../controllers/reviews');        *1*

// locations
router                                                        *2*
  .route('/locations')                                        *2*
  .get(ctrlLocations.locationsListByDistance)                 *2*
  .post(ctrlLocations.locationsCreate);                       *2*
router                                                        *2*
  .route('/locations/:locationid')                            *2*
  .get(ctrlLocations.locationsReadOne)                        *2*
  .put(ctrlLocations.locationsUpdateOne)                      *2*
  .delete(ctrlLocations.locationsDeleteOne);                  *2*

// reviews
router                                                        *3*
  .route('/locations/:locationid/reviews')                    *3*
  .post(ctrlReviews.reviewsCreate);                           *3*
router                                                        *3*
  .route('/locations/:locationid/reviews/:reviewid')          *3*
  .get(ctrlReviews.reviewsReadOne)                            *3*
  .put(ctrlReviews.reviewsUpdateOne)                          *3*
  .delete(ctrlReviews.reviewsDeleteOne);                      *3*

module.exports = router;                                      *4*
```

+   ***1*** **包含控制器文件。（您将在下面创建这些。）**

+   ***2*** **为位置定义路由**

+   ***3*** **为评论定义路由**

+   ***4*** **导出路由**

在此路由文件中，您需要 `require` 相关的控制器文件。您尚未创建这些控制器文件，将在稍后创建。这种方法是一个很好的方法，因为通过在这里定义所有路由并声明相关的控制器函数，您可以开发一个控制器所需的高级视图。

应用程序现在有两组路由：主 Express 应用程序路由和新的 API 路由。但是，由于 API 路由引用的任何控制器都不存在，应用程序目前无法启动。

#### 6.2.2\. 创建控制器占位符

为了使应用程序能够启动，您可以创建控制器的占位符函数。这些函数不会做任何事情，但它们会阻止应用程序在构建 API 功能时崩溃。

当然，第一步是创建控制器文件。您知道这些文件应该在何处以及它们应该被称为什么，因为您已经在 app_api/routes 文件夹中声明了它们。您需要在 app_api/controllers 文件夹中创建两个新文件，分别命名为 locations.js 和 reviews.js。

您可以为每个控制器函数创建一个占位符，作为一个空函数，如下代码片段所示：

```
const locationsCreate = (req, res) => { };
```

请记住，根据它是用于位置还是评论，将每个控制器放入正确的文件，并在文件底部导出它们，如下例所示：

```
module.exports = {
  locationsListByDistance,
  locationsCreate,
  locationsReadOne,
  locationsUpdateOne,
  locationsDeleteOne
};
```

然而，为了测试路由和函数，您需要返回一个响应。

#### 6.2.3\. 从 Express 请求返回 JSON

在构建 Express 应用程序时，您渲染了一个视图模板以将 HTML 发送到浏览器，但使用 API，您希望发送一个状态码和一些 JSON 数据。Express 通过以下行使这项任务变得简单：

```
res                       *1*
  .status(status)         *2*
  .json(content);         *3*
```

+   ***1*** **使用 Express 响应对象**

+   ***2*** **发送响应状态码，例如 200**

+   ***3*** **发送响应数据，例如 {“status” : “success”}**

您可以使用以下两个命令在占位符函数中测试成功，如下代码片段所示：

```
const locationsCreate = (req, res) => {
  res
    .status(200)
    .json({"status" : "success"});
};
```

随着您构建您的 API，您将大量使用此方法来发送不同的状态码和数据作为响应。

#### 6.2.4\. 包含模型

API 能够与数据库通信至关重要；没有它，API 将不会有多大用处！要使用 Mongoose 实现这一点，你首先需要将 Mongoose `require` 到控制器文件中，然后引入 `Location` 模型。在控制器文件的最顶部，在所有占位符函数之上，添加以下两行：

```
const mongoose = require('mongoose');
const Loc = mongoose.model('Location');
```

第一行给控制器提供了数据库连接的访问权限，第二行引入了 `Location` 模型，这样你就可以与 Locations 集合进行交互。

如果你查看应用程序的文件结构，你会看到包含数据库连接的 /models 文件夹，Mongoose 设置在 app_server 文件夹内。但处理数据库的是 API，而不是主要的 Express 应用程序。如果两个应用程序是分开的，模型将保留在 API 中，所以它应该在那里。

将 /models 文件夹从 app_server 文件夹移动到 app_api 文件夹，创建一个类似于 图 6.4 中所示的文件夹结构。

##### 图 6.4. 此时的应用程序文件夹结构。app_api 包含模型、控制器和路由，而 app_server 包含视图、控制器和路由。

![图片](img/06fig04.jpg)

当然，你需要告诉应用程序你已经移动了 app_api/models 文件夹，因此需要更新 app.js 中指向模型的行，使其指向正确的位置：

```
require('./app_api/models/db');
```

完成这些操作后，应用程序应该再次启动并仍然连接到你的数据库。接下来要问的问题是如何测试 API。

#### 6.2.5. 测试 API

你可以通过访问适当的 URL，例如 http://localhost:3000/api/locations/1234，快速在浏览器中测试 `GET` 路由。你应该看到成功响应被发送到浏览器，如图 图 6.5 所示。

##### 图 6.5. 在浏览器中测试 API 的 `GET` 请求

![图片](img/06fig05_alt.jpg)

这对于测试 `GET` 请求是可行的，但对于 `POST`、`PUT` 和 `DELETE` 方法帮助不大。一些工具可以帮助你测试这样的 API 调用，但我们目前最喜欢的免费应用程序是 Postman REST 客户端，它可以作为独立应用程序或浏览器扩展使用。

Postman 允许你使用多种请求方法测试 API URL，允许你指定额外的查询字符串参数或表单数据。点击发送按钮后，Postman 会向指定的 URL 发送请求，并显示响应数据和状态码。

图 6.6 展示了 Postman 向之前相同的 URL 发送 `PUT` 请求的截图。

##### 图 6.6. 使用 Postman REST 客户端测试 API 的 `PUT` 请求

![图片](img/06fig06_alt.jpg)

现在启动 Postman 或其他 REST 客户端是个好主意。在构建 REST API 的过程中，你将在本章中大量使用它。在下一节中，你将使用 `GET` 请求从 MongoDB 读取数据来开始 API 的操作。

### 6.3. GET 方法：从 MongoDB 读取数据

`GET`方法都是关于查询数据库并返回一些数据的。在你的 Loc8r 路由中，有三个`GET`请求执行不同的操作，如表 6.6 中列出。

##### 表 6.6\. Loc8r API 的三个`GET`请求

| 操作 | 方法 | URL 路径 | 示例 |
| --- | --- | --- | --- |
| 读取位置列表 | GET | /locations | [`loc8r.com/api/locations`](http://loc8r.com/api/locations) |
| 读取特定位置 | GET | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 读取特定评论 | GET | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |

你将首先查看如何查找单个位置，因为它为 Mongoose 的工作方式提供了一个很好的介绍。接下来，你将使用 ID 定位单个文档，然后你将扩展到搜索多个文档。

#### 6.3.1\. 在 MongoDB 中使用 Mongoose 查找单个文档

Mongoose 通过其模型与数据库交互，这就是为什么你将`Location`模型作为`Loc`导入到控制器文件顶部。Mongoose 模型有几个相关方法可以帮助管理交互，如侧边栏 Mongoose 查询方法中所述。


**Mongoose 查询方法**

Mongoose 模型有几种方法可以帮助查询数据库。以下是一些关键方法：

+   `find`—基于提供的查询对象进行通用搜索

+   `findById`—查找特定 ID

+   `findOne`—获取与提供的查询匹配的第一个文档

+   `geoNear`—查找与提供的纬度和经度地理上接近的地方

+   `geoSearch`—为`geoNear`操作添加查询功能

你将在这本书中使用其中的一些方法，但不是全部。


对于在 MongoDB 中查找具有已知 ID 的单个数据库文档，Mongoose 有`findById()`方法。

##### 将 findById 方法应用于模型

`findById()`方法相对简单，接受一个参数：要查找的 ID。作为模型方法，它应用于模型，如下所示：

```
Loc.findById(locationid)
```

此方法不会启动数据库查询操作；它告诉模型查询将是什么。要启动数据库查询，Mongoose 模型有一个`exec`方法。

##### 使用 exec 方法运行查询

`exec`方法执行查询，并传递一个回调函数，当操作完成时将运行。回调函数应接受两个参数：一个错误对象和找到的文档实例。作为回调函数，这些参数的名称可以随意命名。

方法可以按以下方式链接：

```
Loc
  .findById(locationid)                    *1*
  .exec((err, location) => {               *2*
    console.log("findById complete");      *3*
  });
```

+   ***1*** **将 findById 方法应用于 Location 模型，使用 Loc**

+   ***2*** **执行查询**

+   ***3*** **完成时记录消息**

这种方法确保数据库交互是异步的，因此不会阻塞主 Node 进程。

##### 在控制器中使用 findById 方法

你正在使用的用于通过 ID 查找单个位置的控制器是 `locationsReadOne()`，位于 app_api/controllers 中的 locations.js 文件。

你知道操作的基本结构：将 `findById()` 和 `exec` 方法应用于 `Location` 模型。为了在控制器上下文中使此操作生效，你需要做两件事：

+   从 URL 中获取 `locationid` 参数，并将其传递给 `findById()` 方法。

+   将输出函数提供给 `exec` 方法。

Express 使得获取你在路由中定义的 URL 参数变得简单。这些参数存储在附加到请求对象的 `params` 对象中。如果你的路由定义如下

```
router
  .route('/api/locations/:locationid')
```

你可以在控制器内部这样访问 `locationid` 参数：

```
req.params.locationid
```

对于输出函数，你可以使用一个简单的回调，将找到的位置作为 JSON 响应发送。将这些放在一起，你将得到以下内容：

```
const locationsReadOne = (req, res) => {
  Loc
    .findById(req.params.locationid)          *1*
    .exec((err, location) => {                *2*
      res                                     *3*
        .status(200)                          *3*
        .json(location);                      *3*
    });
};
```

+   ***1*** 从 URL 参数中获取 locationid，并将其传递给 findById 方法

+   ***2*** **定义回调以接受可能的参数**

+   ***3*** **以 HTTP 状态码 200 将找到的文档作为 JSON 响应发送**

现在你已经有一个基本的 API 控制器。你可以通过在浏览器中访问 MongoDB 中某个位置的 ID 的 URL 或者在 Postman 中调用它来尝试它。要获取一个 ID 值，你可以在 Mongo shell 中运行命令 `db.locations.find ()`，该命令会列出你拥有的所有位置，每个位置都包含 `_id` 值。当你组合好 URL 后，输出应该是一个完整的存储在 MongoDB 中的位置对象；你应该会看到类似图 6.7 的内容。

##### 图 6.7\. 通过 ID 查找单个位置的基本控制器在找到 ID 时向浏览器返回一个 JSON 对象。

![](img/06fig07_alt.jpg)

你尝试了基本控制器吗？你在 URL 中放入了一个无效的位置 ID 吗？如果你这样做了，你就会看到你没有收到任何东西——没有警告，没有消息；一个 200 状态码告诉你一切正常，但没有返回数据。

##### 捕获错误

那个基本控制器的问题在于它只输出成功响应，无论是否成功。这种行为对 API 来说并不好。一个好的 API 应该在出现问题时返回错误代码。

为了响应错误消息，控制器需要设置以捕获潜在的错误并发送适当的响应。这种方式的错误捕获通常涉及 `if` 语句。每个 `if` 语句都必须有一个相应的 `else` 语句或包含一个 `return` 语句。


##### 提示

你的 API 代码绝不能对请求置之不理。


使用你的基本控制器，你需要捕获三个错误：

+   请求参数不包含 `locationid`。

+   `findById()` 方法不返回位置。

+   `findById()` 方法返回一个错误。

不成功的`GET`请求的状态码为 404。考虑到这一点，查找并返回单个位置的控制器最终代码如下所示。

##### 列表 6.2. `locationsReadOne`控制器

```
const locationsReadOne = (req, res) => {
    Loc
      .findById(req.params.locationid)
      .exec((err, location) => {
        if (!location) {                           *1*
          return res                               *1*
            .status(404)                           *1*
            .json({                                *1*
              "message": "location not found"      *1*
            });                                    *1*
        } else if (err) {                          *2*
          return res                               *2*
            .status(404)                           *2*
            .json(err);                            *2*
        }
        res                                        *3*
          .status(200)                             *3*
          .json(location);                         *3*
       });
};
```

+   ***1*** **错误陷阱 1：如果 Mongoose 没有返回位置，则发送 404 消息并退出函数作用域，使用 return 语句**

+   ***2*** **错误陷阱 2：如果 Mongoose 返回错误，则将其作为 404 响应发送并退出控制器，使用 return 语句**

+   ***3*** **如果 Mongoose 没有出错，则继续之前的操作，并在 200 响应中发送位置对象**

列表 6.2 使用了`if`语句的两种捕获方法。错误陷阱 1 ***1*** 和错误陷阱 2 ***2*** 使用`if`语句检查 Mongoose 返回的错误。每个`if`语句都包含一个`return`语句，这阻止了回调作用域中任何后续代码的执行。如果没有找到错误，则忽略`return`语句，代码继续发送成功的响应 ***3***。

这些陷阱中的每一个都提供了成功和失败的响应，没有留下 API 让请求者悬而未决的空间。如果你愿意，你还可以加入一些`console.log()`语句，这样在终端中跟踪正在发生的事情会更容易；GitHub 上的源代码中也有一些。

图 6.8 展示了使用 Chrome 中的 Postman 扩展程序时，成功请求与失败请求之间的区别。

##### 图 6.8. 使用 Postman 测试成功（左）和失败（右）的 API 响应

![](img/06fig08_alt.jpg)

这样就处理完了一个完整的 API 路由。现在，是时候查看第二个`GET`请求以返回单个评论了。

#### 6.3.2. 基于 ID 查找单个子文档

要查找子文档，你首先必须找到父文档，然后使用其 ID 定位所需的位置。当你找到文档后，你可以查找特定的子文档。你可以以`locationsReadOne()`控制器为起点，并添加一些修改来创建`reviewsReadOne()`控制器。这些修改包括

+   接受并使用额外的`reviewid` URL 参数。

+   仅从文档中选择名称和评论，而不是让 MongoDB 返回整个文档。

+   查找具有匹配 ID 的评论。

+   返回适当的 JSON 响应。

要完成这些事情，你可以使用几个新的 Mongoose 方法。

##### 限制从 MongoDB 返回的路径

当你从 MongoDB 检索文档时，你并不总是需要完整的文档；有时，你只想获取一些特定的数据。限制传递的数据也有助于带宽消耗和速度。

Mongoose 通过将`select()`方法链接到模型查询来实现这一点。以下代码片段告诉 MongoDB 你只想获取位置的名字和评论：

```
Loc
  .findById(req.params.locationid)
  .select('name reviews')
  .exec();
```

`select()`方法接受一个由空格分隔的路径字符串，表示你想要检索的路径。

##### 使用 Mongoose 查找特定的子文档

Mongoose 还提供了一个辅助方法来通过 ID 查找子文档。给定一个子文档数组，Mongoose 的`id`方法接受你想要查找的 ID。`id`方法返回单个匹配的子文档，可以使用以下方式使用：

```
Loc
  .findById(req.params.locationid)
  .select('name reviews')
  .exec((err, location) => {
    const review = location.reviews.id(req.params.reviewid);      *1*
  }
);
```

+   ***1*** **将参数中的 reviewid 传递给 id 方法**

在这个代码片段中，回调函数将单个评论返回到`review`变量中。

##### 添加一些错误处理并将所有内容组合在一起

现在你已经拥有了制作`reviewsReadOne()`控制器的所需成分。从`locationsReadOne()`控制器的副本开始，你可以进行必要的修改以返回单个评论。

以下列表显示了`reviewsReadOne()`控制器在 review.js 中的内容（加粗的修改）：

##### 列表 6.3\. 查找一个单个评论的控制台

```
 const reviewsReadOne = (req, res) => {
    Loc
      .findById(req.params.locationid)
      .select('name reviews')                                                        *1*

      .exec((err, location) => {
        if (!location) {
          return res
            .status(404)
            .json({
              "message": "location not found"
            });
        } else if (err) {
          return res
            .status(400)
            .json(err);
        }
        if (location.reviews && location.reviews.length > 0) {                          *2*

          const review = location.reviews.id(req.params.reviewid);                      *3*
          if (!review) {                                                                *4*
            return res                                                                  *4*
              .status(400)                                                              *4*
              .json({                                                                   *4*
                "message": "review not found"                                           *4*
            });                                                                         *4*
          } else {                                                                      *5*
            response = {                                                                *5*
              location : {                                                              *5*
                name : location.name,                                                   *5*
                id : req.params.locationid                                              *5*
              },                                                                        *5*
              review                                                                    *5*
            };                                                                          *5*
            return res                                                                  *5*
              .status(200)                                                              *5*
              .json(response);                                                          *5*
          }                                                                             *5*
        } else {                                                                        *6*
          return res                                                                    *6*
            .status(404)                                                                *6*
            .json({                                                                     *6*
              "message": "No reviews found"                                             *6*
          });                                                                           *6*
        }                                                                               *6*
      }
    );
};
```

+   ***1*** **将 Mongoose 选择方法添加到模型查询中，表示你想要获取位置名称及其评论**

+   ***2*** **检查返回的位置是否有评论**

+   ***3*** **使用 Mongoose 子文档.id 方法作为搜索匹配 ID 的辅助工具**

+   ***4*** **如果没有找到评论，返回适当的响应**

+   ***5*** **如果找到评论，构建一个响应对象，返回评论和位置名称和 ID**

+   ***6*** **如果没有找到评论，返回适当的错误信息**

当这段代码保存并准备好后，你可以再次使用 Postman 进行测试。你需要有正确的 ID 值，这些值可以从你为检查单个位置所做的 Postman 查询中获取，或者可以直接从 MongoDB 通过 Mongo shell 获取。Mongo 命令`db.locations.find()`返回所有位置及其评论。请记住，URL 的结构是/locations/:locationid/reviews/:reviewid。

你还可以测试如果你输入了一个错误的位置或评论 ID 会发生什么，或者尝试来自不同位置的评论 ID。

#### 6.3.3\. 使用地理空间查询查找多个文档

Loc8r 的首页应根据用户的当前地理位置显示位置列表。MongoDB 和 Mongoose 提供了一些特殊的地理空间聚合方法，以帮助找到附近的地点。

在这里，你将使用 Mongoose 聚合$`geoNear`来查找一个接近指定点的位置列表，直到指定的最大距离。$`geoNear`是一个聚合方法，它接受多个配置选项，其中以下选项是必需的：

+   `near`作为一个`geoJSON`地理点

+   一个`distanceField`对象选项

+   一个`maxDistance`对象选项

以下代码片段显示了基本结构：

```
Loc.aggregate([{$geoNear: {near: {}, distanceField: "distance",
maxDistance: 100}}]);
```

与`findById`方法一样，$`geoNear`聚合返回一个 Promise，其值可以通过使用回调、其`exec`方法或 async/await 来获取。

##### 构建一个 geoJSON 点

`$geoNear`的第一个参数是一个`geoJSON`点：一个包含纬度和经度数组的简单 JSON 对象。`geoJSON`点的结构在以下代码片段中显示：

```
const point = {               *1*
  type: "Point",              *2*
  coordinates: [lng, lat]     *3*
};
```

+   ***1*** **声明对象**

+   ***2*** **将其定义为“Point”类型**

+   ***3*** **在数组中设置经纬度坐标，先设置经度**

这里设置的获取位置列表的路由没有在 URL 参数中包含坐标，这意味着它们将以不同的方式指定。查询字符串是这种数据类型的理想选择，因此请求 URL 将看起来更像是这样：

```
api/locations?lng=-0.7992599&lat=51.378091
```

Express 当然为您提供了访问查询字符串中的值的方法，将它们放入附加到请求对象的查询对象中，例如`req.query.lng`。当检索到经纬度值时，它们将是字符串，但需要将它们作为数字添加到点对象中。JavaScript 的`parseFloat()`函数可以处理这一点。以下代码片段显示了如何从查询字符串中获取坐标并创建`geoJSON`点，这是`$geoNear`聚合所必需的：

```
const locationsListByDistance = async (req, res) => {
  const lng = parseFloat(req.query.lng);               *1*
  const lat = parseFloat(req.query.lat);               *1*
  const near = {                                       *2*
    type: "Point",                                     *2*
    coordinates: [lng, lat]                            *2*
  };                                                   *2*
  const geoOptions = {
    distanceField: "distance.calculated",
    spherical: true,                                   *3*
    maxDistance: 20000,
    limit: 10
  };
  try {
    const results = await Loc.aggregate([              *4*
      {
        $geoNear: {
          near,
          ...geoOptions                                *5*
        }
      }
    ]);
  } catch (err) {
    console.log(err);
  }
};
```

+   ***1*** **从查询字符串中获取坐标并将其从字符串转换为数字**

+   ***2*** **创建 geoJSON 点**

+   ***3*** **在这里您使用的是 spherical: true，因为这会导致 MongoDB 使用$nearSphere 语义，它使用球面几何来计算距离。如果这是 false，它将使用 2D 几何。**

+   ***4*** **聚合**

+   ***5*** **扩展运算符（参见附近的侧边栏）**

尝试执行此控制器代码不会产生响应，因为数据处理尚未开始。请记住，此代码返回一个 Promise 对象。


**扩展运算符**

ES2015 中引入了扩展运算符。此运算符接受一个可迭代对象（一个数组、字符串或对象），并允许它扩展到期望零个或多个参数（在函数调用中使用）或元素（在数组字面量中使用）的位置。

在前一个代码块中的聚合函数情况下，它将`geoOptions`中的对象属性注入到`$geoNear`对象中。扩展运算符有很多用途；详细信息请参阅[`mng.bz/wEya`](http://mng.bz/wEya)。


##### 聚合规范中的球形选项

`geoOptions`对象包含一个球形键。此值必须设置为`true`，因为您已经将 MongoDB 数据存储中的搜索索引指定为`2dsphere`。如果您尝试将其设置为`false`，应用程序将抛出异常：

```
const geoOptions = {
  distanceField: "distance.calculated",
  spherical: true
};
```

##### 通过数量限制 geoNear 结果

当返回列表时，您通常会希望通过限制结果数量来关注 API 服务器以及最终用户看到的响应性。在$`geoNear`聚合中，添加`num`或`limit`选项可以做到这一点。您指定希望返回的最大结果数量。您可以指定两者，但`num`优先于`limit`。

以下代码片段显示了添加到之前的`geoOptions`对象中的`limit`，限制返回数据集的大小为 10 个对象：

```
const geoOptions = {
  distanceField: "distance.calculated",
  spherical: true,
  limit: 10
};
```

现在搜索只会返回最多 10 个最近的结果。

##### 通过距离限制 geoNear 结果

当返回基于位置的数据时，另一种保持 API 处理在控制之下的是通过距离限制结果列表。这是一个添加另一个名为`maxDistance`的选项的情况。当你使用球形选项时，MongoDB 会为你以米为单位进行计算，使生活变得简单。这并不总是这种情况。MongoDB 的旧版本使用弧度，这使得事情变得更加复杂。

如果你想要以英里为单位输出，你需要做一些计算，但你会坚持使用米和公里。你将设置一个 20 公里的限制，即 20,000 米。现在你可以将`maxDistance`值添加到选项中，并将这些选项按如下方式添加到控制器中：

```
const locationsListByDistance = (req, res) => {
  const lng = parseFloat(req.query.lng);
  const lat = parseFloat(req.query.lat);
  const near = {
    type: "Point",
    coordinates: [lng, lat]
  };
  const geoOptions = {                       *1*
    distanceField: "distance.calculated",
    spherical: true,                         *1*
    maxDistance: 20000,                      *1*
    num: 10                                  *1*

  };
  ...                                        *2*
};
```

+   ***1*** 创建一个选项对象，包括设置最大距离为 20 公里

+   ***2*****定义对象的其余部分**

| |
| --- |

**额外加分**

尝试从查询字符串值中获取最大距离，而不是将其硬编码到函数中。本章在 GitHub 上的代码有答案。

| |
| --- |

这是你需要的最后一个选项来对你的$`geoNear`数据库搜索，所以现在是时候开始处理输出了。

##### 查看 the $geoNear 聚合输出

$`geoNear`聚合方法的返回结果对象是数据库中匹配项的列表或错误对象。如果你使用回调函数，它将有以下签名：`callback(err, result)`。由于你使用`async`/`await`，你使用`try`/`catch`来执行操作或捕获错误。

在成功查询的情况下，错误对象是未定义的；结果对象是一个项目列表，如前所述。你将首先处理成功的查询响应，然后再添加错误处理。

在成功的$`geoNear`聚合之后，MongoDB 返回一个对象数组。每个对象包含一个距离值（由`distanceField`指定的路径）和从数据库返回的文档。换句话说，MongoDB 在数据中包含了距离。以下代码片段显示了返回数据的示例，为了简洁而截断：

```
[ { _id: 5b2c166f5caddf7cd8cea46b,
    name: 'Starcups',
    address: '125 High Street, Reading, RG6 1PS',
    rating: 3,
    facilities: [ 'Hot drinks', 'Food', 'Premium wifi' ],
    coords: { type: 'Point', coordinates: [Array] },
    openingTimes: [ [Object], [Object], [Object] ],
    distance: { calculated: 5005.183015553589 } } ]
```

这个数组只有一个对象，但成功的查询可能会一次性返回多个对象。$`geoNear`聚合返回数据存储中包含的整个文档，但 API 不应该返回比请求更多的数据。所以，而不是将返回的数据作为响应发送，你首先需要进行一些处理。

#### 处理$geoNear 输出

在 API 发送响应之前，你需要确保它发送的是正确的内容，并且只发送所需的内容。你知道主页列表需要什么数据；你已经在 app_server/controllers/location.js 中构建了主页控制器。`homelist()`函数发送几个位置对象，类似于以下示例：

```
{
  id: 111,
  name: 'Starcups',
  address: '125 High Street, Reading, RG6 1PS',
  rating: 3,
  facilities: ['Hot drinks', 'Food', 'Premium wifi'],
  distance: '100m'
}
```

为了从结果中创建一个类似的对象，你需要遍历结果并将相关数据映射到一个新的数组中。然后，可以以状态 200 的响应返回处理后的数据。以下代码片段显示了这种结果可能的样子：

```
try {
    const results = await Loc.aggregate([
      {
        $geoNear: {
          near,
           ...geoOptions
        }
      }
    ]);
    const locations = results.map(result => {                    *1*
      return {                                                   *2*
        id: result._id,
        name: result.name,
        address: result.address,
        rating: result.rating,
        facilities: result.facilities,
        distance: `${result.distance.calculated.toFixed()}m`     *3*
      }
    });
    return res                                                   *4*
      .status(200)
      .json(locations);
  } catch (err) {
    ...
```

+   ***1*** **创建一个新的数组来存储映射的结果数据**

+   ***2*** **返回映射的结果**

+   ***3*** **获取距离并将其修正为最接近的整数**

+   ***4*** 将处理后的数据作为 JSON 响应发送

如果你使用 Postman 测试这个 API 路由——记得在查询字符串中添加经纬度坐标——你会看到类似图 6.9 的内容。

##### 图 6.9\. 在 Postman 中测试位置列表路由应该返回 200 状态和结果列表，具体取决于查询字符串中发送的地理坐标。

![](img/06fig09_alt.jpg)


**额外加分**

尝试将结果传递给一个外部命名函数来构建位置列表。这个函数应该返回处理后的列表，然后可以将其传递到 JSON 响应中。


如果你通过发送距离测试数据太远的坐标来测试，你应该仍然得到 200 状态，但返回的数组将是空的。

##### 添加错误处理

再次，你首先构建了成功功能。现在你需要添加一些错误陷阱，以确保 API 总是发送适当的响应。

你需要设置的陷阱应该检查

+   所有参数都已正确发送。

+   $`geoNear` 聚合操作没有返回错误条件。

以下列表显示了最终的控制器，包括这些错误陷阱。

##### 列表 6.4\. 位置列表控制器 locationsListByDistance

```
const locationsListByDistance = async(req, res) => {
  const lng = parseFloat(req.query.lng);
  const lat = parseFloat(req.query.lat);
  const near = {
    type: "Point",
    coordinates: [lng, lat]
  };
  const geoOptions = {
    distanceField: "distance.calculated",
    key: 'coords',
    spherical: true,
    maxDistance: 20000,
    limit: 10
  };
  if (!lng || !lat) {                                            *1*
    return res                                                   *1*
      .status(404)                                               *1*
      .json({                                                    *1*
      "message": "lng and lat query parameters are required"     *1*
    });                                                          *1*
  }                                                              *1*
  try {
    const results = await Loc.aggregate([
      {
        $geoNear: {
          near,
          ...geoOptions
        }
      }
    ]);
    const locations = results.map(result => {
      return {
        id: result._id
        name: result.name,
        address: result.address,
        rating: result.rating,
        facilities: result.facilities,
        distance: `${result.distance.calculated.toFixed()}m`
      }
    });
    res
      .status(200)
      .json(locations);
  } catch (err) {
    res
      .status(404)                                               *2*
      .json(err);
  }
};
```

+   ***1*** 检查 lng 和 lat 查询参数是否存在正确的格式；如果不存在，则返回 404 错误和信息

+   ***2* 如果 $geoNear 聚合查询返回错误，则以 404 状态发送此响应**

这个列表完成了你的 API 需要服务的 `GET` 请求，因此现在是时候处理 `POST` 请求了。

### 6.4\. POST 方法：向 MongoDB 添加数据

`POST` 方法主要涉及在数据库中创建文档或子文档，然后返回保存的数据作为确认。在 Loc8r 的路由中，你有两个 `POST` 请求执行不同的操作，如表 6.7 中列出。

##### 表 6.7\. Loc8r API 的两个 `POST` 请求

| 操作 | 方法 | URL 路径 | 示例 |
| --- | --- | --- | --- |
| 创建新位置 | POST | /locations | [`api.loc8r.com/locations`](http://api.loc8r.com/locations) |
| 创建新评论 | POST | /locations/:locationid/reviews | [`api.loc8r.com/locations/123/reviews`](http://api.loc8r.com/locations/123/reviews) |

`POST` 方法通过获取发送给它们的表单数据并将其添加到数据库中工作。就像通过 `req.params` 访问 URL 参数和通过 `req.query` 访问查询字符串一样，Express 控制器通过 `req.body` 访问发送的表单数据。

首先看看如何创建文档。

#### 6.4.1\. 在 MongoDB 中创建新文档

在 Loc8r 的数据库中，每个位置都是一个文档，所以你将在本节中创建一个文档。Mongoose 无法使创建 MongoDB 文档的过程对你来说更加简单。你将 `create()` 方法应用于你的模型，并传递一些数据和回调函数。这个结构是最小的，因为它将附加到你的 `Loc` 模型：

![](img/p0187-01_alt.jpg)

这很简单。创建过程有两个主要步骤：

1.  使用发布的表单数据创建一个与架构匹配的 JavaScript 对象。

1.  根据创建操作的成功或失败，在回调中发送适当的响应。

看看步骤 1，你已经知道你可以通过使用 `req.body` 来获取发送给你的数据，步骤 2 现在应该很熟悉了。直接进入代码。

以下列表显示了创建新文档的完整 `locationsCreate()` 控制器。

##### 列表 6.5\. 创建新位置的完整控制器

```
const locationsCreate = (req, res) => {
  Loc.create({                                     *1*
    name: req.body.name,
    address: req.body.address,
    facilities: req.body.facilities.split(","),    *2*
    coords: {                                      *3*
     type: "Point",
     [
       parseFloat(req.body.lng),
       parseFloat(req.body.lat)
     ]
    }, {
      days: req.body.days2,
      opening: req.body.opening2,
      closing: req.body.closing2,
      closed: req.body.closed2,
    }]
  }, (err, location) => {                          *4*
    if (err) {
      res
        .status(400)
        .json(err);
    } else {
      res
        .status(201)
        .json(location);
    }
  });
};
```

+   ***1*** **将创建方法应用于模型**

+   ***2*** **通过分割逗号分隔的列表创建设施数组**

+   ***3*** **将坐标从字符串解析为数字**

+   ***4*** **提供一个包含成功和失败适当响应的回调函数**

此列表显示了在 MongoDB 中创建新文档并保存一些数据是多么容易。为了简洁起见，你已将 `openingTimes` 数组限制为两个条目，但这个数组可以很容易地扩展，或者更好的是，放入循环中检查值的存在。

你可能还会注意到没有设置 `rating`。记住，在架构中，你设置了默认值 `0`，如下面的代码片段所示：

```
rating: {
  type: Number,
  "default": 0,
  min: 0,
  max: 5
},
```

当文档创建时应用此代码片段，将初始值设置为 `0`。关于这段代码的其他一些可能让你感到惊讶的事情：没有验证！

#### 6.4.2\. 使用 Mongoose 验证数据

这个控制器内部没有验证代码，那么阻止某人输入大量空或部分文档的又是什么呢？再次强调，你开始在 Mongoose 架构中构建验证。在架构中，你将一些路径的 `required` 标志设置为 `true`。当这个标志被设置时，Mongoose 不会将数据发送到 MongoDB。

给定以下位置的基础架构，例如，你可以看到只有 `name` 是必填字段：

```
const locationSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  address: String,
  rating: {
    type: Number,
    'default': 0,
    min: 0,
    max: 5
  },
  facilities: [String],
  coords: {
    type: {type: String},
    coordinates: [Number]
  },
  openingTimes: [openingTimeSchema],
  reviews: [reviewSchema]
});
```

如果这个字段缺失，`create()` 方法会引发错误，并且不会尝试将文档保存到数据库中。

在 Postman 中测试这个 API 路由看起来像图 6.10。注意方法设置为 `post`，并且选择的数据类型（在名称和值列表上方）是 `x-www-form-urlencoded`。你将在 Postman 界面中输入要随 `POST` 请求一起提交的键和值，如图所示。注意不要在 Postman 字段中输入的键前后留空白，因为空格会导致意外的输入。

##### 图 6.10\. 在 Postman 中测试`POST`方法，确保方法和表单数据设置正确

![](img/06fig10_alt.jpg)

#### 6.4.3\. 在 MongoDB 中创建新的子文档

在 Loc8r 位置上下文中，评论是子文档。子文档通过其父文档创建和保存。换句话说，要创建和保存一个新的子文档，您必须

1.  找到正确的父文档。

1.  添加一个新的子文档。

1.  保存父文档。

找到正确的父文档不是问题，因为您已经完成了这个操作，并且可以用它作为下一个控制器`reviewsCreate()`的框架。当您找到父文档后，您可以调用一个外部函数来完成下一部分（您很快就会编写这个函数），如下所示。

##### 列表 6.6\. 创建评论的控制器

```
const reviewsCreate = (req, res) => {
  const locationId = req.params.locationid;
  if (locationId) {
    Loc
      .findById(locationId)
      .select('reviews')
      .exec((err, location) => {
        if (err) {
          res
            .status(400)
            .json(err);
        } else {
          doAddReview(req, res, location);        *1*
         }
       });
  } else {
    res
      .status(404)
      .json({"message": "Location not found"});
  }
};
```

+   ***1*** **成功的查找操作将调用一个新函数来添加评论，传递请求、响应和位置对象**

这段代码并没有做特别新的事情；您之前都见过。通过调用一个新函数，您可以通过减少嵌套和缩进的数量来使代码更整洁，同时也更容易进行测试。

##### 添加和保存子文档

找到父文档并检索现有的子文档列表后，您需要添加一个新的子文档。子文档是对象的数组，向数组中添加新对象的最简单方法是创建数据对象并使用 JavaScript 的`push()`方法，如下面的代码片段所示：

```
location.reviews.push({
  author: req.body.author,
  rating: req.body.rating,
  reviewText: req.body.reviewText
});
```

这段代码正在获取表单数据；因此，它使用`req.body`。

当子文档被添加后，必须保存父文档，因为子文档不能单独保存。为了保存文档，Mongoose 有一个模型方法`save()`，它期望一个带有错误参数和返回对象参数的回调。以下代码片段展示了这个方法的作用：

```
location.save((err, location) => {
  if (err) {
    res
      .status(400)
      .json(err);
  } else {
    let thisReview = location.reviews[location.reviews.length - 1];    *1*
    res
      .status(201)
      .json(thisReview);
  }
});
```

+   ***1*** **在返回的数组中找到最后一个评论，因为 MongoDB 返回整个父文档，而不仅仅是新的子文档**

通过`save`方法返回的文档是完整的父文档，而不是仅有的新子文档。为了在 API 响应中返回正确的数据——即子文档——您需要从数组中检索最后一个子文档 ***1***。

当添加文档和子文档时，您需要考虑到这个操作可能对其他数据产生的影响。例如，在 Loc8r 中，添加评论会增加一个新的评分，这个新的评分会影响文档的整体评分。在评论成功保存后，您将调用另一个函数来更新平均评分。

将您拥有的所有内容组合到`doAddReview()`函数中，再加上一点错误处理，可以得到以下列表。

##### 列表 6.7\. 添加和保存子文档

```
const doAddReview = (req, res, location) => {                    *1*
  if (!location) {
    res
      .status(404)
      .json({"message": "Location not found"});
  } else {
    const {author, rating, reviewText} = req.body;
    location.reviews.push({                                      *2*
      author,
      rating,
      reviewText
    });
    location.save((err, location) => {                           *3*
      if (err) {
        res
          .status(400)
          .json(err);
      } else {
        updateAverageRating(location._id);                       *4*
        const thisReview = location.reviews.slice(-1).pop();     *5*
        res                                                      *5*
          .status(201)
          .json(thisReview);
       }
     });
  }
};
```

+   ***1*** **当提供一个父文档 . . .**

+   ***2*** **. . . 将新数据推送到子文档数组 . . .**

+   ***3*** **. . . 在保存之前。**

+   ***4*** **在成功保存操作后，调用一个函数来更新平均评分**

+   ***5*** **检索数组中添加的最后一个评论，并将其作为 JSON 确认响应返回**

##### 更新平均评分

计算平均评分并不特别复杂，所以我们不会过多停留。步骤如下

1.  根据提供的 ID 查找正确的文档。

1.  将所有评论子文档的评分加起来。

1.  计算平均评分值。

1.  更新父文档的评分值。

1.  保存文档。

将此步骤列表转换为代码，你将得到以下类似列表，应将其放置在 reviews.js 控制器文件中，与基于评论的控制器一起。

##### 列表 6.8\. 计算和更新平均评分

```
const doSetAverageRating = (location) => {                        *1*
  if (location.reviews && location.reviews.length > 0) {
    const count = location.reviews.length;
    const total = location.reviews.reduce((acc, {rating}) => {    *2*
      return acc + rating;
    }, 0);
    location.rating = parseInt(total / count, 10);                *3*
    location.save(err => {                                        *4*
      if (err) {
        console.log(err);
      } else {
        console.log(`Average rating updated to ${location.rating}`);
       }
     });
  }
};
const updateAverageRating = (locationId) => {                     *5*
  Loc.findById(locationId)
    .select('rating reviews')
    .exec((err, location) => {
      if (!err) {
        doSetAverageRating(location);
       }
     });
};
```

+   ***1*** **使用提供的位置数据**

+   ***2*****使用 JavaScript 数组 reduce 方法汇总子文档的评分**

+   ***3*** **计算平均评分值并更新父文档的评分值**

+   ***4*** **保存父文档**

+   ***5*** **根据提供的 locationid 数据查找位置**

你可能已经注意到你没有发送任何 JSON 响应，因为你已经发送了。整个操作是异步的，不需要影响发送确认已保存评论的 API 响应。

添加评论不是唯一需要更新平均评分的情况，这就是为什么让这些函数可以从其他控制器访问，而不是紧密耦合到创建评论的操作中，更有意义。

你在这里所做的是使用 Mongoose 更新 MongoDB 数据的预览，因此现在你将进入 API 的 `PUT` 方法。

### 6.5\. PUT 方法：在 MongoDB 中更新数据

`PUT` 方法全部关于在数据库中更新现有文档或子文档，并将保存的数据作为确认返回。在 Loc8r 的路由中，你有两个 `PUT` 请求执行不同的操作，如 表 6.8 所列。

##### 表 6.8\. Loc8r API 更新位置和评论的两种 `PUT` 请求

| Action | Method | URL path | Example |
| --- | --- | --- | --- |
| 更新特定位置 | PUT | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 更新特定评论 | PUT | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |

`PUT` 方法与 `POST` 方法类似，因为它们通过接收发送给它们的表单数据来工作。但与使用数据在数据库中创建新文档不同，`PUT` 方法使用数据来更新现有文档。

#### 6.5.1\. 使用 Mongoose 更新 MongoDB 中的文档

在 Loc8r 中，你可能想要更新位置以添加新设施、更改开放时间或修改其他数据。在文档中更新数据的方法可能已经开始看起来熟悉：

1.  查找相关文档。

1.  对实例进行一些更改。

1.  保存文档。

1.  发送 JSON 响应。

这种方法是通过 Mongoose 模型实例直接映射到 MongoDB 中的文档的方式实现的。当你的查询找到文档时，你得到一个模型实例。如果你对这个实例进行更改然后保存它，Mongoose 将使用你的更改更新数据库中的原始文档。

#### 6.5.2\. 使用 Mongoose 的 save 方法

当你更新平均评分值时，你看到了这个方法的作用。`save` 方法应用于 `find()` 函数返回的模型实例。它期望一个带有标准参数的错误对象和返回数据对象的回调。

以下代码片段显示了这种方法的一个简化的骨架：

```
  Loc
    .findById(req.params.locationid)      *1*
    .exec((err, location) => {
      location.name = req.body.name;      *2*
      location.save((err, loc) => {       *3*
        if (err) {
          res
            .status(404)                  *4*
            .json(err);                   *4*
        } else {
          res
            .status(200)                  *4*
            .json(loc);                   *4*
         }
       });
    }
  );
};
```

+   ***1*** **查找要更新的文档**

+   ***2*** **对模型实例进行更改，更改一个路径的值**

+   ***3*** **使用 Mongoose 的 save 方法保存文档**

+   ***4*** **返回成功或失败响应**

在这里，你可以清楚地看到查找、更新、保存和响应的单独步骤。通过添加一些错误处理和你要保存的数据，将这个骨架扩展到 `locationsUpdateOne()` 控制器，可以得到以下列表。

##### 列表 6.9\. 在 MongoDB 中修改现有文档

```
const locationsUpdateOne = (req, res) => {
  if (!req.params.locationid) {
    return res
      .status(404)
      .json({
        "message": "Not found, locationid is required"
      });
  }
  Loc
    .findById(req.params.locationid)                          *1*
    .select('-reviews -rating')
    .exec((err, location) => {
      if (!location) {
        return res
          .json(404)
          .status({
            "message": "locationid not found"
          });
      } else if (err) {
        return res
          .status(400)
          .json(err);
      }
      location.name = req.body.name;                            *2*
      location.address = req.body.address;                      *2*
      location.facilities = req.body.facilities.split(',');     *2*
      location.coords = {                                       *2*
        type: "Point",                                          *2*
        [                                                       *2*
          parseFloat(req.body.lng),                             *2*
          parseFloat(req.body.lat)                              *2*
        ]                                                       *2*
      };                                                        *2*
      location.openingTimes = [{                                *2*
        days: req.body.days1,                                   *2*
        opening: req.body.opening1,                             *2*
        closing: req.body.closing1,                             *2*
        closed: req.body.closed1,                               *2*
      }, {                                                      *2*
        days: req.body.days2,                                   *2*
        opening: req.body.opening2,                             *2*
        closing: req.body.closing2,                             *2*
        closed: req.body.closed2,                               *2*
      }];
      location.save((err, loc) => {                             *3*
        if (err) {
          res                                                   *4*
            .status(404)
            .json(err);
        } else {
          res                                                   *4*
            .status(200)
            .json(loc);
        }
      });
    }
  );
};
```

+   ***1*** **通过提供的 ID 查找位置文档**

+   ***2*** **使用提交表单中的值更新路径**

+   ***3*** **保存实例**

+   ***4*** **根据保存操作的结果发送适当的响应**

现在，由于代码已经完全展开，这里显然有更多的代码，但你仍然可以轻松地识别更新过程的关键步骤。

眼尖的你们中的一些人可能已经注意到了 `select` 语句中的奇怪之处：

```
.select('-reviews -rating')
```

之前，你使用 `select()` 方法来说明你*想要*选择哪些列。通过在路径名前添加一个连字符，你声明你*不想要*从数据库中检索它。因此，这个 `select()` 语句表示要检索除 `reviews` 和 `rating` 之外的所有内容。

#### 6.5.3\. 在 MongoDB 中更新现有子文档

更新子文档与更新文档的过程完全相同，只有一个例外：在找到文档后，你必须找到正确的子文档以进行更改。然后应用 `save` 方法到文档上，而不是子文档上。因此，更新现有子文档的步骤如下

1.  查找相关文档。

1.  找到相关的子文档。

1.  在子文档中进行一些更改。

1.  保存文档。

1.  发送 JSON 响应。

对于 Loc8r，你正在更新的子文档是评论，因此当评论被更改时，你必须记得重新计算平均评分。这将是你需要添加的唯一额外内容。以下列表显示了在 `reviewsUpdateOne()` 控制器中放置的所有内容。

##### 列表 6.10\. 在 MongoDB 中更新子文档

```
const reviewsUpdateOne = (req, res) => {
  if (!req.params.locationid || !req.params.reviewid) {
    return res
      .status(404)
      .json({
        "message": "Not found, locationid and reviewid are both required"
      });
  }
  Loc
    .findById(req.params.locationid)                                    *1*
    .select('reviews')
    .exec((err, location) => {
      if (!location) {
        return res
          .status(404)
          .json({
            "message": "Location not found"
           });
      } else if (err) {
        return res
          .status(400)
          .json(err);
      }
      if (location.reviews && location.reviews.length > 0) {
        const thisReview = location.reviews.id(req.params.reviewid);    *2*
        if (!thisReview) {
          res
            .status(404)
            .json({
              "message": "Review not found"
            });
        } else {

          thisReview.author = req.body.author;                          *3*
          thisReview.rating = req.body.rating;                          *3*
          thisReview.reviewText = req.body.reviewText;                  *3*
          location.save((err, location) => {                            *4*
            if (err) {
              res                                                       *5*
                .status(404)
                .json(err);
            } else {
              updateAverageRating(location._id);
              res                                                       *5*
                .status(200)
                .json(thisReview);
            }
          });
        }
      } else {
        res
          .status(404)
          .json({
            "message": "No review to update"
          });
      }
    }
  );
};
```

+   ***1*** **查找父文档**

+   ***2*** **查找子文档**

+   ***3*** **根据提供的表单数据对子文档进行更改**

+   ***4*** **保存父文档**

+   ***5*** **返回 JSON 响应，根据成功保存发送子文档对象**

在这个列表中可以清楚地看到更新的五个步骤：查找文档；查找子文档；进行更改；保存；并响应。再次强调，这里的大部分代码都是错误处理，但对于创建一个稳定、响应式的 API 至关重要。你不想保存错误的数据，发送错误的响应，或者删除你不想删除的数据。说到删除数据，你现在可以继续到最后一个你使用的四个 API 方法：`DELETE`。

### 6.6\. DELETE 方法：从 MongoDB 中删除数据

`DELETE`方法不出所料，完全是关于在数据库中删除现有文档或子文档。在 Loc8r 的路由中，你有一个用于删除位置的`DELETE`请求，还有一个用于删除评论的请求。详细信息列在表 6.9 中。首先，让我们看看如何删除文档。

##### 表 6.9\. Loc8r API 删除位置和评论的两个`DELETE`请求

| 操作 | 方法 | URL 路径 | 示例 |
| --- | --- | --- | --- |
| 删除特定位置 | DELETE | /locations/:locationid | [`loc8r.com/api/locations/123`](http://loc8r.com/api/locations/123) |
| 删除特定评论 | DELETE | /locations/:locationid/reviews/:reviewid | [`loc8r.com/api/locations/123/reviews/abc`](http://loc8r.com/api/locations/123/reviews/abc) |

#### 6.6.1\. 在 MongoDB 中删除文档

Mongoose 通过提供`findByIdAndRemove()`方法使在 MongoDB 中删除文档变得极其简单。此方法期望一个参数：要删除的文档的 ID。

API 在出错时应响应 404，在成功时应响应 204。以下列表显示了`locationsDeleteOne()`控制器中的所有内容。

##### 列表 6.11\. 根据 ID 从 MongoDB 中删除文档

```
const locationsDeleteOne = (req, res) =>  {
  const {locationid} = req.params;
  if (locationid) {
    Loc
      .findByIdAndRemove(locationid)          *1*
      .exec((err, location) =>  {             *2*
          if (err) {
            return res                        *3*
              .status(404)
              .json(err);
          }
          res                                 *3*
            .status(204)
            .json(null);
        }
    );
  } else {
    res
      .status(404)
      .json({
        "message": "No Location"
      });
  }
};
```

+   ***1*** **调用 findByIdAndRemove 方法，传入 locationid**

+   ***2*** **执行方法**

+   ***3*** **响应失败或成功**

这是一种快速简单的删除文档的方法，但你也可以将其分解为两步过程，或者如果你更喜欢，先找到它然后删除。这给了你在删除之前对文档进行操作的机会（如果你需要的话）。以下代码片段展示了这一点：

```
Loc
  .findById(locationid)
  .exec((err, location) => {
    // Do something with the document
    location.remove((err, loc) => {
      // Confirm success or failure
    });
  }
);
```

这个片段有一个额外的嵌套层，但如果你需要的话，它带来了额外的灵活性。

#### 6.6.2\. 从 MongoDB 中删除子文档

删除子文档的过程与其他你使用子文档所做的操作没有区别；所有操作都是通过父文档来管理的。删除子文档的步骤如下

1.  查找父文档。

1.  查找相关的子文档。

1.  删除子文档。

1.  保存父文档。

1.  确认操作的成功或失败。

删除子文档本身很简单，因为 Mongoose 给了你另一个辅助方法。你已经看到你可以使用 `id` 方法通过 ID 查找子文档，如下所示：

```
location.reviews.id(reviewid)
```

Mongoose 允许你像这样将 `remove` 方法链接到这个语句的末尾：

```
location.reviews.id(reviewid).remove()
```

这条指令会从数组中删除子文档。请记住保存父文档以将更改持久化回数据库。将所有步骤（包括大量错误处理）组合到 `reviewsDeleteOne()` 控制器中，如下所示。

##### 列表 6.12\. 在 MongoDB 中查找和删除子文档

```
const reviewsDeleteOne = (req, res) => {
  const {locationid, reviewid} = req.params;
  if (!locationid || !reviewid) {
    return res
      .status(404)
      .json({'message': 'Not found, locationid and reviewid are both
     required'});
  }
  Loc
    .findById(locationid)                                  *1*
    .select('reviews')
    .exec((err, location) => {
       if (!location) {
         return res
           .status(404)
           .json({'message': 'Location not found'});
      } else if (err) {
        return res
          .status(400)
          .json(err);
      }

      if (location.reviews && location.reviews.length > 0) {
        if (!location.reviews.id(reviewid)) {
          return res
            .status(404)
            .json({'message': 'Review not found'});
        } else {
          location.reviews.id(reviewid).remove();          *2*
          location.save(err => {                           *3*
            if (err) {
              return res                                   *4*
                .status(404)
                .json(err);
            } else {
              updateAverageRating(location._id);
              res                                          *4*
                .status(204)
                .json(null);
            }
          });
        }
      } else {
        res
          .status(404)
          .json({'message': 'No Review to delete'});
      }
    });
  };
```

+   ***1*** **查找相关的父文档**

+   ***2*** **一步查找并删除相关的子文档**

+   ***3*** **保存父文档**

+   ***4*** **返回适当的成功或失败响应**

再次强调，这里的代码大部分是错误处理。API 可能会返回七个可能的响应，只有一个才是成功的。删除子文档很简单；务必确保你删除的是正确的文档。

当你删除一个带有相关评分的评论时，你还得记得调用 `updateAverageRating()` 函数来重新计算位置的平均评分。这个函数只有在删除操作成功时才应该被调用。

就这样。你已经构建了一个 Express 和 Node 的 REST API，它可以接受 `GET`、`POST`、`PUT` 和 `DELETE` HTTP 请求来在 MongoDB 数据库上执行 CRUD 操作。

在 第七章 中将要介绍，你将看到如何从 Express 应用程序内部使用此 API，最终使 Loc8r 网站数据库驱动化！

### 摘要

在本章中，你学习了

+   创建 REST API 的最佳实践，包括 URL、请求方法和响应代码

+   `POST`、`GET`、`PUT` 和 `DELETE` HTTP 请求方法如何映射到常见的 CRUD 操作

+   Mongoose 辅助方法用于创建辅助方法

+   通过 Mongoose 模型与数据交互的方式，以及模型的一个实例如何直接映射到数据库中的一个文档

+   如何通过父文档管理子文档

+   一些通过检查你所能想到的任何可能的错误来使 API 坚固的方法，以确保请求永远不会得到无响应

## 第七章\. 消费 REST API：在 Express 中使用 API

*本章涵盖*

+   从 Express 应用程序中调用 API

+   处理和使用 API 返回的数据

+   与 API 响应代码一起工作

+   从浏览器将数据提交回 API

+   验证和捕获错误

这章内容非常精彩！这是你第一次将前端与后端连接起来。你将移除控制器中的硬编码数据，并最终在浏览器中显示数据库中的数据。你还将通过 API 将数据从浏览器推送到数据库，创建新的子文档。

本章的技术重点是 Node 和 Express。图 7.1 展示了本章在整个架构和宏伟计划中的位置。

##### 图 7.1\. 本章的重点是将 Express 应用程序从第四章更新为与在第六章中开发的 REST API 交互。

![](img/07fig01_alt.jpg)

在本章中，我们将讨论如何在 Express 中调用 API 以及如何处理响应。您将对 API 进行调用以从数据库中读取和写入数据。在这个过程中，我们将探讨错误处理、数据处理以及通过分离关注点来创建可重用代码。在结束时，我们将涵盖架构的各个层次，您可以在这些层次上添加验证，以及为什么这些不同的层次是有用的。

首先，看看如何从 Express 应用程序调用 API。

### 7.1\. 如何从 Express 调用 API

我们需要首先解决的问题是如何从 Express 调用 API。这种方法不仅限于您的 API；您可以使用它来调用任何 API。

您的 Express 应用程序需要能够调用您在第六章中设置的 API URL，当然，发送正确的请求方法，并且能够解释响应。为此，您将使用一个名为 `request` 的模块。

#### 7.1.1\. 将请求模块添加到您的项目中

`request` 模块就像您迄今为止使用的其他任何包一样，可以通过 npm 添加到您的项目中。要安装最新版本并将其添加到 package.json 文件中，请转到终端，并输入以下命令：

```
$ npm install --save request
```

当 npm 完成其操作后，您可以将 `request` 包含在将使用它的文件中。在 Loc8r 中，您只有一个文件需要进行 API 调用：包含主要服务器端应用程序控制器的文件。因此，在 app_server/controllers 中的 locations.js 文件顶部添加以下行以引入 `request` 模块：

```
const request = require('request');
```

现在您已经准备就绪了！

#### 7.1.2\. 设置默认选项

每次使用 `request` 调用的 API 都必须有一个完全限定的 URL，这意味着它必须包含完整的地址，而不是一个相对链接。但这个 URL 在开发和生产环境中是不同的。

为了避免在每个进行 API 调用的控制器中都要进行此检查，您可以在控制器文件顶部设置一个默认配置选项。为了根据环境使用正确的 URL，您可以使用您熟悉的环境变量 `NODE_ENV`。

在实践中，app_server/controllers/locations.js 文件的开头应该看起来像以下列表。

##### 列表 7.1\. 将请求和默认 API 选项添加到 locations.js 控制器文件

```
const request = require('request');
const apiOptions = {                                                *1*
  server: 'http://localhost:3000'                                   *1*
};                                                                  *1*
if (process.env.NODE_ENV === 'production') {                        *2*
  apiOptions.server = 'https://pure-temple-67771.herokuapp.com';    *2*
}                                                                   *2*
```

+   ***1*** **设置本地开发的默认服务器 URL**

+   ***2*** **如果应用程序以生产模式运行，则设置不同的基本 URL；更改应用程序的实时地址**

在此代码到位后，您对 API 的每次调用都可以引用`apiOptions.server`并使用正确的基 URL。

#### 7.1.3\. 使用请求模块

发送请求的基本结构很简单，是一个接受选项和回调参数的单个命令，如下所示：

![](img/p0203-01.jpg)

选项指定了请求的所有内容，包括 URL、请求方法、请求正文和查询字符串参数。这些选项确实是您在本章中将使用的选项，它们在表 7.1 中有详细说明。

##### 表 7.1\. 定义 API 调用时的四个常见请求选项

| 选项 | 描述 | 必需 |
| --- | --- | --- |
| url | 要进行的请求的完整 URL，包括协议、域名、路径和 URL 参数 | 是 |
| method | 请求方法，如 GET、POST、PUT 或 DELETE | 否——如果未指定，则默认为 GET |
| json | 请求体作为 JavaScript 对象；如果不需要正文数据，则发送空对象 | 是——确保响应体也被解析为 JSON |
| qs | 表示任何查询字符串参数的 JavaScript 对象 | 否 |

以下代码片段显示了您可能如何将这些选项组合起来进行`GET`请求。`GET`请求不应发送正文，但可能包含查询字符串参数：

```
const requestOptions = {
  url: 'http://yourapi.com/api/path',        *1*
  method: 'GET',                             *2*
  json: {},                                  *3*
  qs: {                                      *4*
    offset: 20                               *4*
  }                                          *4*
};
```

+   ***1*** **定义要进行的 API 调用的 URL**

+   ***2*** **设置请求方法**

+   ***3*** **定义请求的正文，即使它是一个空的 JSON 对象**

+   ***4*** **可选地添加 API 可能使用的任何查询字符串参数**

您可以指定更多选项，但这四个是最常见的，也是您在本章中将使用的选项。有关其他可能选项的更多信息，请参阅 GitHub 仓库中的参考：[`github.com/mikeal/request`](https://github.com/mikeal/request)。

当 API 返回响应时，回调函数会运行，并具有三个参数：一个错误对象、完整的响应和解析后的响应正文。除非捕获到错误，否则错误对象是`null`。在您的代码中，最有用的三份数据将是响应的状态码、响应体以及抛出的任何错误。以下代码片段显示了您可能如何为`request()`函数结构化一个回调：

```
(err, response, body) => {
  if (err) {                                           *1*
    console.log(err);                                  *1*
  } else if (response.statusCode === 200) {            *2*
    console.log(body);                                 *2*
  } else {                                             *3*

    console.log(response.statusCode);                  *3*
  }
}
```

+   ***1*** **如果已通过错误，则对其进行处理**

+   ***2*** **如果响应状态码为 200（请求成功），则输出响应的 JSON 正文**

+   ***3*** **如果请求返回不同的状态码，则输出该代码**

完整的响应对象包含大量信息，所以我们在这里不会深入探讨。您可以通过在开始将 API 调用添加到应用程序时使用`console.log`语句来自己检查它。

将这些部分组合起来，进行 API 调用的基本结构如下所示：

```
const requestOptions = {                                *1*
  url: 'http://yourapi.com/api/path',                   *1*
  method: 'GET',                                        *1*
  json: {},                                             *1*
  qs: {                                                 *1*
    offset: 20                                          *1*
  }                                                     *1*
};                                                      *1*
request(requestOptions, (err, response, body) => {      *2*
  if (err) {                                            *2*
    console.log(err);                                   *2*
  } else if (response.statusCode === 200) {             *2*
    console.log(body);                                  *2*
  } else {                                              *2*
    console.log(response.statusCode);                   *2*
  }                                                     *2*
});
```

+   ***1*** **定义请求选项**

+   ***2*** **发送请求，通过选项发送并通过提供回调函数按需使用响应**

在下一节中，您将把这种理论付诸实践，并开始构建 Loc8r 控制器以使用您已经构建的 API。

### 7.2\. 使用来自 API 的数据列表：Loc8r 主页

到目前为止，将要执行工作的控制器文件应该已经包含了`request`模块的导入和一些默认值的设置。现在来点有趣的：更新控制器以调用 API 并从数据库中提取页面数据。

您有两个主要页面需要提取数据：主页，显示位置列表，以及详细信息页面，提供特定位置更多信息。从开始处获取主页的数据。

当前主页控制器包含一个`res.render()`函数调用，将硬编码的数据发送到视图。但您希望的方式是在 API 返回一些数据后渲染主页。主页控制器无论如何都会有相当多的事情要做，所以将这个渲染移动到它自己的函数中。

#### 7.2.1\. 分离关注点：将渲染移动到命名函数中

将渲染移动到自己的命名函数中有几个原因。首先，您将渲染与应用逻辑解耦。渲染过程不关心数据是从哪里或如何获得的；如果提供了正确格式的数据，它就会使用这些数据。使用单独的函数有助于您更接近可测试的理想，即每个函数应该做一件事情。一个相关的额外好处是，该函数变得可重用，因此您可以从多个地方调用它。

创建用于主页渲染的新函数的第二个原因是渲染过程发生在 API 请求的回调中。除了使代码难以测试外，它还使代码难以阅读。所需的嵌套级别使得控制器函数相当大，缩进很多。作为一个最佳实践，您应该尽量避免深度缩进代码：当您再次回到它时，它很难阅读和理解。

第一步是在`app_server/controllers`文件夹中的 locations.js 文件中创建一个新的函数`renderHomepage()`，并将`homelist`控制器的内容移动到其中。请记住确保新函数也接受`req`和`res`参数。以下列表显示了您在这里所做的工作的简化版本。您可以从`homelist`控制器中调用此代码，如列表所示，一切将像以前一样工作。

##### 列表 7.2\. 将`homelist`控制器的内容移动到外部函数中

```
const renderHomepage = (req, res) => {
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',    *1*
    ...                                                 *1*
  });                                                   *1*
};
const homelist = (req, res) => {                        *2*
  renderHomepage(req, res);                             *2*
};                                                      *2*
```

+   ***1*** **在此处包含从 res.render 调用中所有的代码（为了简洁而省略）**

+   ***2*** **从 homelist 控制器调用新的 renderHomepage 函数**

这一步是一个开始，但您还没有完成；您需要数据！

#### 7.2.2\. 构建 API 请求

你可以通过请求 API 来获取你想要的数据，为此，你需要构建请求。要构建请求，你需要知道要发送的 URL、方法、JSON body 和查询字符串。回顾第六章，或者查看 API 代码本身，你可以看到你需要提供表 7.2 中所示的信息。

##### 表 7.2\. 向 API 请求位置列表所需的信息

| 参数 | 值 |
| --- | --- |
| URL | SERVER:PORT/api/locations |
| 方法 | GET |
| JSON body | null |
| 查询字符串 | lng, lat, maxDistance |

将此信息映射到请求是直接的。正如你在本章前面所看到的，请求的选项是 JavaScript 对象。目前，你将硬编码经纬度值到选项中，这是一种更快、更简单的方法来测试。在本书的后面部分，你将使应用程序具有位置感知能力。现在，你将选择接近测试数据存储位置的坐标。最大距离设置为 20 公里。

当你发起请求时，你将通过一个简单的回调函数来调用`renderHomepage()`函数，这样就不会让浏览器挂起。将这个想法用代码表达如下所示。

##### 列表 7.3\. 更新`homelist`控制器以在渲染页面之前调用 API

```
const homelist = (req, res) => {
  const path = '/api/locations';              *1*
  const requestOptions = {                    *2*
    url: `${apiOptions.server}${path}`,       *2*
    method: 'GET',                            *2*
    json: {},                                 *2*
    qs: {                                     *2*
      lng: -0.7992599,                        *2*
      lat: 51.378091,                         *2*
      maxDistance: 20                         *2*
    }                                         *2*
  };                                          *2*
  request(                                    *3*
    requestOptions,                           *3*
    (err, response, body) => {                *4*
      renderHomepage(req, res);               *4*
    }                                         *4*
  );
};
```

+   ***1*** **设置 API 请求的路径。（服务器已在文件顶部设置。）**

+   ***2*** **设置请求选项，包括 URL、方法、空的 JSON body 和硬编码的查询字符串参数**

+   ***3*** **发起请求，通过请求选项发送**

+   ***4*** **提供回调以渲染主页**

如果你保存此代码并再次运行应用程序，主页应该会显示得和之前完全一样。你现在可能正在向 API 发起请求，但你正在忽略响应。

#### 7.2.3\. 使用 API 响应数据

既然你费心去调用 API，至少你应该使用它返回的数据。你可以在以后更稳健地处理响应，但你现在将从使处理器工作开始。为了实现这一点，你将假设响应体被返回到回调中，你可以直接将其传递到`renderHomepage()`函数中，如下所示。

##### 列表 7.4\. 更新`homelist`控制器的内容以使用 API 响应

```
  request(
    requestOptions,
    (err, response, body) => {
      renderHomepage(req, res, body);        *1*
    }
  );
```

+   ***1*** **将请求返回的 body 传递给 renderHomepage()函数**

你编写了 API，所以你知道 API 返回的响应体应该是一个位置数组。`renderHomepage()`函数需要一个位置数组来发送到视图，所以尝试直接传递，对以下列表中加粗的部分进行更改。

##### 列表 7.5\. 更新`renderHomepage`函数以使用 API 数据

```
const renderHomepage = (req, res, responseBody) => {             *1*
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',
    pageHeader: {
      title: 'Loc8r',
      strapline: 'Find places to work with wifi near you!'
    },
    sidebar: "Looking for wifi and a seat? Loc8r helps you find places
     to work when out and about. Perhaps with coffee, cake or a pint?
     Let Loc8r help you find the place you're looking for.",
    locations: responseBody                                      *2*
  });
};
```

+   ***1*** **在函数声明中添加一个额外的 responseBody 参数**

+   ***2*** **移除硬编码的位置数组，并通过 responseBody 传递**

这个过程能这么简单吗？在浏览器中尝试一下，看看会发生什么。我们希望您会得到类似图 7.2 的结果。

##### 图 7.2\. 使用浏览器中的数据库数据得到的初始结果：接近期望的结果

![](img/07fig02_alt.jpg)

看起来相当不错，对吧？您需要处理一下距离的显示方式，但除此之外，数据正如您所期望的那样传输过来。由于您在前期设计视图、基于视图构建控制器以及基于控制器开发模型的工作，插入数据变得快速且简单。

您已经让它工作了。现在您需要让它变得更好。目前还没有错误处理，距离需要一些改进。

#### 7.2.4\. 在显示之前修改数据：修正距离

目前，列表中的距离显示有 15 位小数，没有单位，因此它们非常精确，但完全无用！您想要说明每个距离是以米还是千米为单位，并将数字四舍五入到单个米或千米的一位小数。您应该在将数据发送到 `renderHomepage()` 函数之前这样做，因为这个函数应该保留用于处理实际渲染，而不是整理数据。

您需要遍历返回的位置数组，格式化每个位置的距离值。而不是直接这样做，您将创建一个外部函数（在同一文件中），称为 `formatDistance()`，它接受一个距离值并返回格式良好的值。在控制器文件中，在 `renderHomepage()` 之前放置以下函数。

##### 列表 7.6\. 添加 formatDistance 函数

```
const formatDistance = (distance) => {
  let thisDistance = 0;
  let unit = 'm';
  if (distance > 1000) {                                        *1*
    thisDistance = parseFloat(distance / 1000).toFixed(1);      *1*
    unit = 'km';                                                *1*
  } else {                                                      *2*
    thisDistance = Math.floor(distance);                        *2*
  }
  return thisDistance + unit;
};
```

+   ***1*** **如果提供的距离超过 1000 米，则转换为千米，四舍五入到一位小数，并添加千米单位**

+   ***2*** **否则，向下取整到最近的米**

现在按照以下列表中加粗所示进行更改。请注意，此代码片段中省略了 `homelist` 控制器的框架。为了使内容简短，`request` 语句仍然位于控制器内部。

##### 列表 7.7\. 添加并使用一个函数来格式化 API 返回的距离

```
request(
  requestOptions,
  (err, response, body) => {
    let data = [];                                          *1*
    data = body.map( (item) => {                            *2*
      item.distance = formatDistance(item.distance);
      return item;
    });
    renderHomepage(req, res, data);                         *3*
  }
);
```

+   ***1*** **创建一个变量供将来使用**

+   ***2*** **将数据映射到数组中，格式化位置的距离值**

+   ***3*** **发送修改后的数据以进行渲染，而不是原始的正文内容**

您需要做一个小小的额外修改。您在 API 输出的距离中添加了 `m`，但使用 `formatDistance()` 函数后，这个添加就不再需要了，所以请在 `/app_api/controllers/locations.js` 中进行以下更改。

##### 列表 7.8\. 从 API 响应中移除单位

```
const locations = results.map(result =>  {
  return {
    name: result.name,
    address: result.address,
    rating: result.rating,
    facilities: result.facilities,

    distance: `${result.distance.calculated.toFixed()}`     *1*
  }
});
```

+   ***1*** **从这一行中移除 m**

如果您进行这些更改并刷新页面，应该会看到距离被整理了一下，变得更有用，如图图 7.3 所示。

##### 图 7.3. 在您格式化 API 返回的距离后，主页看起来更好。

![](img/07fig03_alt.jpg)

这样更好；主页看起来更符合您的期望。为了加分，您可以在`formatDistance()`函数中添加一些错误捕获，以确保已经传递了一个`distance`参数，并且它是一个数字。

#### 7.2.5. 捕获 API 返回的错误

到目前为止，您一直假设 API 将始终返回一个包含 200 成功状态码的数据数组。但这并不一定。您编写了 API，即使没有找到附近的位置，也会返回 200 状态码。目前的情况是，当这种情况发生时，主页将显示一个中央区域没有任何内容。更好的用户体验将是向用户输出一条消息，说明附近没有地方。

您也知道，您的 API 可能会返回 404 错误，因此您需要确保您适当地处理这些错误。在这种情况下，您不希望向用户显示 404 错误，因为错误不会是由于主页缺失造成的。更好的选择是，再次发送一条消息到浏览器，在主页的上下文中。

处理这些场景不应该太难。以下章节将向您展示如何操作，从控制器开始。

##### 使请求回调更健壮

捕获错误的主要原因是为了确保它们不会导致代码失败。第一个弱点在于`request`回调，在那里您在将数据发送到渲染之前正在操作响应。如果数据始终一致，这是可以的，但您没有这样的奢侈。

`request`回调目前运行一个`for`循环来格式化距离，无论 API 返回什么数据。您应该只在 API 返回 200 状态码和一些结果时运行这个循环。

以下列表展示了如何通过添加一个简单的`if`语句（app_server/controllers/locations.js）来检查状态码和返回数据的长度，从而实现这一结果。

##### 列表 7.9. 在尝试使用 API 返回的数据之前验证 API 是否已返回数据

```
request(
  requestOptions,
  (err, {statusCode}, body) =>                       *1*
    let data = [];
    if (statusCode === 200 && body.length) {         *2*
      data = body.map( (item) =&gt; {
        item.distance = formatDistance(item.distance):
        return item;
      });
    }
    renderHomepage(req, res, data);
  }
);
```

+   ***1*** **使用对象解构来获取 statusCode，因为您只需要这个**

+   ***2*** **仅在 API 返回 200 状态码和一些数据时运行循环来格式化距离**

更新这段代码应该可以防止在 API 响应状态码不是 200 时，这个回调崩溃并抛出错误。链中的链接是`renderHomepage()`函数。

##### 根据响应数据定义输出消息

与`request`回调一样，您最初对`renderHomepage()`函数的关注是使其在传递要显示的位置数组时工作。现在，由于这个函数可能会收到不同类型的数据，您需要使其能够适当地处理这些可能性。

响应体可能是以下三种情况之一：

+   位置数组

+   当没有找到位置时，数组为空

+   当 API 返回错误时，包含消息的字符串

你已经有了处理地点数组的代码，所以你需要处理其他两种可能性。在捕获这些错误时，你还需要设置一个可以发送到视图的消息。

为了做到这一点，你需要更新`renderHomepage()`函数，同时还要执行以下操作：

+   设置一个用于消息的变量容器

+   检查响应体是否为数组；如果不是，设置一个适当的消息

+   如果响应是数组，如果它是空的（即没有返回地点），则设置不同的消息

+   将消息发送到视图

以下列表显示了代码中的样子。

##### 列表 7.10\. 如果 API 不返回位置数据则输出消息

```
const renderHomepage = function(req, res, responseBody){
  let message = null;                                       *1*
  if (!(responseBody instanceof Array)) {                   *2*
    message = "API lookup error";                           *2*
    responseBody = [];                                      *2*
  } else {                                                  *3*
    if (!responseBody.length) {                             *3*
      message = "No places found nearby";                   *3*
    }                                                       *3*
  }
  res.render('locations-list', {
    title: 'Loc8r - find a place to work with wifi',
    pageHeader: {
      title: 'Loc8r',
      strapline: 'Find places to work with wifi near you!'
    },
    sidebar: "Looking for wifi and a seat? Loc8r helps you find
    places to work when out and about. Perhaps with coffee, cake or a
    pint? Let Loc8r help you find the place you're looking for.",
    locations: responseBody,
    message                                                 *4*
  });
};
```

+   ***1*** **定义一个变量来保存消息**

+   ***2*** **如果响应不是数组，设置一个消息并将 responseBody 设置为空数组**

+   ***3*** **如果响应是长度为零的数组，设置一个消息**

+   ***4*** **向要发送到视图的变量中添加消息**

唯一的惊喜是当你将`responseBody`设置为空数组时，如果它最初是通过字符串传递的。你这样做是为了防止视图抛出错误。视图期望在`locations`变量中发送一个数组；如果发送一个空数组，它会忽略它，但如果发送一个字符串，则会抛出错误。

这条链的最后一个链接是更新视图以在发送消息时显示消息。

##### 更新视图以显示错误消息

你正在捕获 API 的错误，并且你也在处理它们以将某些内容返回给用户。最后一步是通过在视图模板中添加占位符让用户看到消息。

你在这里不需要做任何复杂的事情；一个简单的带有`error`类的`div`来包含任何消息就足够了。以下列表显示了应用服务器视图`locations-list.pug`的主页视图的`block content`部分。

##### 列表 7.11\. 更新视图以在需要时显示错误消息

```
block content
  .row.banner
    .col-12
      h1= pageHeader.title
        small &nbsp;#{pageHeader.strapline}
  .row
    .col-12.col-md-8
      .error= message            *1*

      each location in locations
        .card
          .card-block
            h4
              a(href="/location")= location.name
              small &nbsp;
                +outputRating(location.rating)
              span.badge.badge-pill.badge-default.float-right=
              location.distance
            p.address= location.address
            .facilities
              each facility in location.facilities
                span.badge.badge-warning= facility

    .col-12.col-md-4
      p.lead= sidebar
```

+   ***1*** **在主要内容区域添加一个 div，并在发送消息时显示它**

这很简单——基本但简单。现在应该足够了。剩下要做的就是测试它。

##### 测试 API 错误捕获

任何新的代码，你都需要确保它能正常工作。测试这段代码的一个简单方法是更改`requestOptions`中发送的查询字符串值。

为了测试“附近没有找到地点”的陷阱，你可以将`maxDistance`设置为一个小数值（记住它是以公里为单位的）或者将`lng`和`lat`设置为没有地点的点，如下例所示：

```
requestOptions = {
  url: `${apiOptions.server}${path}`,
  method: 'GET',
  json: {},
  qs: {
    lng: 1,                 *1*
    lat: 1,                 *1*
    maxDistance : 0.002     *1*
  }
};
```

+   ***1*** **更改请求中发送的查询字符串值以获取无结果返回**


**修复一个有趣的错误**

你尝试通过将`lng`或`lat`设置为`0`来测试 API 错误捕获了吗？你应该期望看到“附近没有找到地点”的消息，但相反你看到了“API 查找错误”，这是由于你的 API 代码中的错误捕获错误导致的。

在`locationsListByDistance`控制器中，使用通用的“falsey”JavaScript 测试来检查是否省略了`lng`和`lat`查询字符串参数。您的代码如下：`if (!lng || !lat)`。

在此类“falsey”测试中，JavaScript 会寻找它认为的任何假值，例如空字符串、未定义、`null`以及（对您来说很重要）`0`。这会在您的代码中引入一个意外的错误。如果某人恰好位于赤道或本初子午线（即格林威治标准时间线），他们将收到 API 错误。

您可以通过验证“falsey”测试来修复此错误，使其表示“如果它是假的但不是零”。在代码中，此语句看起来像这样：`if ((!lng && lng !== 0) || (!lat && lat !== 0))`。

更新 API 中的控制器将消除此错误。


您可以使用类似的策略来测试 404 错误。API 期望所有查询字符串参数都被发送，如果其中一个缺失，则返回 404。为了快速测试代码，您可以注释掉其中一个参数：

```
const requestOptions = {
  url: `${apiOptions.server}${path}`,
  method: 'GET',
  json: {},
  qs: {
    // lng: -0.7992599,          *1*
    lat: 51.378091,
    maxDistance: 20
  }
};
```

+   ***1*** **在请求中注释掉一个查询字符串参数以帮助测试当 API 返回 404 时会发生什么。**

一次做这两件事，刷新主页以查看不同的消息。这些消息在图 7.4 中显示。

##### 图 7.4. 显示在捕获返回的错误后在视图中显示的错误消息

![](img/07fig04_alt.jpg)

那个图显示了设置得很好的主页。您的 Express 应用程序查询您构建的 API，该 API 从 MongoDB 数据库中提取数据并将其传递回应用程序。当应用程序从 API 收到响应时，它会确定如何处理它，并在浏览器中显示数据或错误消息。

接下来，您将为详情页面做同样的事情，这次处理单个数据实例。

### 7.3. 从 API 获取单个文档：Loc8r 详情页面

详情页面应显示您关于特定位置的所有信息，从名称和地址到评分、评论、设施和位置地图。目前，此页面正在使用控制器中硬编码的数据，看起来像图 7.5。

##### 图 7.5. 现在的详情页面，使用控制器中硬编码的数据

![](img/07fig05_alt.jpg)

在本节中，您将更新应用程序，使其允许您指定您想要获取详情的位置，从 API 获取详情，并将它们输出到浏览器。当然，您还会添加一些错误处理。

#### 7.3.1. 设置 URL 和路由以访问特定的 MongoDB 文档

您当前访问详情页面的路径是/location。此路径没有提供指定您想要查看的位置的方法。为了解决这个问题，您可以借鉴 API 路由的方法，在那里您将位置文档的 ID 指定为 URL 参数。

单个位置的路由 API 是 /api/location/:locationid。你可以为主要的 Express 应用程序做同样的事情，并更新路由以包含 `locationid` 参数。位置的主要应用程序路由在 `/routes` 文件夹中的 index.js 文件中。以下代码片段显示了更新位置详情路由以接受 `locationid` URL 参数所需的简单更改（app_server/routes/index.js）：

```
router.get('/', ctrlLocations.homelist);
router.get('/location/:locationid', ctrlLocations.locationInfo);       *1*
router.get('/location/review/new', ctrlLocations.addReview);
```

+   ***1* 为单个位置的路由添加 locationid 参数**

好的，很好，但你是从哪里获取位置 ID 的呢？从整个应用程序的角度考虑，主页是最好的起点，因为详情页的链接都是从主页来的。

当主页 API 返回位置数组时，每个位置对象都包含其唯一的 ID。这个整个对象已经传递到视图中，所以更新主页视图以添加这个 ID 作为 URL 参数不应该太难。

这一点也不难！以下列表显示了在 locations-list.pug 文件中需要进行的微小更改，以便将每个位置的唯一 ID 添加到链接中，以便通过详情页。

##### 列表 7.12\. 更新列表视图以添加位置 ID 到相关链接

```
block content
  .row.banner
    .col-12
      h1= pageHeader.title
        small &nbsp;#{pageHeader.strapline}
  .row
    .col-12.col-md-8
      .error= message

      each location in locations
        .card
          .card-block
            h4
              a(href=`/location/${location._id}`)= location.name       *1*
              small &nbsp;
                +outputRating(location.rating)
              span.badge.badge-pill.badge-default.float-right=
              location.distance
            p.address= location.address
            .facilities
              each facility in location.facilities
                span.badge.badge-warning= facility
```

+   ***1*** **在遍历数组中的每个位置时，从对象中提取唯一的 ID 并将其添加到链接的 href 中** 

如果生活中的一切都这么简单就好了。现在主页包含了每个位置的唯一链接，所有链接都点击通过到详情页。现在你需要让它们显示正确的数据。

#### 7.3.2\. 分离关注点：将渲染移动到命名函数

正如你对主页所做的那样，你将详情页的渲染移动到其自己的命名函数中。再次强调，你这样做是为了将渲染功能与 API 调用和数据处理分离。

以下列表显示了新的 `renderDetailPage()` 函数的简化版本以及它是如何从 `locationInfo` 控制器中调用的。

##### 列表 7.13\. 将 `locationInfo` 控制器的内容移动到外部函数

```
const renderDetailPage = (req, res) => {      *1*
  res.render('location-info', {               *1*
    title: 'Starcups',                        *1*
    ...                                       *1*
  });                                         *1*
const locationInfo = (req, res) => {
  renderDetailPage(req, res);                 *2*
};
```

+   ***1*** **创建了一个名为 renderDetailPage 的新函数，并将 locationInfo 控制器的所有内容移动到其中**

+   ***2*** **从控制器调用一个新函数，记得传递 req 和 res 参数**

现在你已经设置了一个清晰、简洁的控制器，准备查询 API。

#### 7.3.3\. 使用 URL 参数中的唯一 ID 查询 API

API 调用的 URL 需要包含位置的 ID。你的详情页现在有这个 ID，作为 URL 参数 `locationid`，因此你可以通过使用 `req.params` 获取这个值，并将其添加到请求选项中的 `path`。请求是一个 `GET` 请求，所以 `json` 值将是一个空对象。

了解这些后，你可以使用在主页控制器中创建的模式来构建对 API 的请求。当 API 响应时，你会调用`renderDetailPage()`函数。所有这些都在以下列表中展示。

##### 列表 7.14\. 更新`locationInfo`控制器以调用 API

```
const locationInfo = (req, res) => {
  const path = `/api/locations/${req.params.locationid}`;      *1*
  const requestOptions = {                                     *2*
    url: `${apiOptions.server}${path}`,                        *2*
    method: 'GET',                                             *2*
    json: {}                                                   *2*
  };                                                           *2*
  request(
    requestOptions,
    (err, response, body) => {
      renderDetailPage(req, res);                              *3*
    }
  );
};
```

+   ***1*** **从 URL 中获取 locationid 参数并将其附加到 API 路径**

+   ***2*** **设置所有调用 API 所需的请求选项**

+   ***3*** **当 API 响应时调用 renderDetailPage()函数**

如果你现在运行此代码，你会看到与之前相同的静态数据，因为你还没有将 API 返回的数据传递到视图中。如果你想快速查看返回的内容，可以在`request`回调中添加一些控制台日志语句。

如果你确认一切按预期工作，那么是时候将数据传递到视图中了。

#### 7.3.4\. 将 API 数据传递给视图

你目前假设 API 返回的是正确数据；你很快就会处理错误检测。这些数据需要一点预处理：API 返回的坐标是一个数组，但视图需要它们作为对象中的命名键值对。

以下列表展示了如何在`request`语句的上下文中执行此操作，在将数据发送到`renderDetailPage()`函数之前，将 API 中的数据转换。

##### 列表 7.15\. 在控制器中预处理数据

```
request(
  requestOptions,
  (err, response, body) => {
    const data = body;                      *1*
    data.coords = {                         *2*
      lng: body.coords[0],                  *2*
      lat: body.coords[1]                   *2*
    };                                      *2*
    renderDetailPage(req, res, data);       *3*
  }
);
```

+   ***1*** **在新的变量中创建返回数据的副本**

+   ***2*** **将 coords 属性重置为对象，使用从 API 响应中提取的值设置 lng 和 lat**

+   ***3*** **发送要渲染的转换后数据**

下一个逻辑步骤是更新`renderDetailPage()`函数以使用这些数据而不是硬编码的数据。为了使这可行，你需要确保该函数接受数据作为参数，然后根据需要更新传递给视图的值。以下列表以粗体突出显示了所需的更改。

##### 列表 7.16\. 更新`renderDetailPage`以接受和使用 API 数据

```
const renderDetailPage = function (req, res, location) {      *1*
  res.render('location-info', {
    title: location.name,                                     *2*
    pageHeader: {                                             *2*
      title: location.name                                    *2*
      },                                                      *2*
    sidebar: {
      context: 'is on Loc8r because it has accessible wifi and
      space to sit down with your laptop and get some work done.',
      callToAction: "If you've been and you like it - or if you
      don't - please leave a review to help other people just
      like you."
    },
    location                                                  *3*
  });
};
```

+   ***1*** **在函数定义中添加一个用于数据的新的参数**

+   ***2*** **在函数中根据需要引用具体的数据项**

+   ***3*** **将包含所有详细信息的完整位置数据对象传递给视图**

你可以采取发送完整对象的方法，因为你最初基于视图和控制器所需的数据构建数据模型。如果你现在运行应用程序，你应该会看到页面加载了从数据库中检索的数据，如图图 7.6 所示。

##### 图 7.6\. 详细页面通过 API 从 MongoDB 中拉取数据

![](img/07fig06_alt.jpg)

留意细节的读者会发现图 7.6 中的截图存在问题：评论没有与之关联的日期。

#### 7.3.5\. 调试和修复视图错误

因此，你遇到了一个视图问题，它没有正确输出评论日期。你基于视图和控制器提供的数据构建了数据模型，但现在你发现信息不足。在本节中，你将查看发生了什么。

从查看`app_server/views`中的 Pug 文件 location-info.pug 开始，你可以隔离输出此部分的行：

```
small.reviewTimestamp #{review.timestamp}
```

现在，你需要检查模式（schema）以查看在定义模型时是否进行了更改。评论的 schema 位于`app_api/models`中的 locations.js，如下代码片段所示：

```
const reviewSchema = new mongoose.Schema({
  author: String,

  rating: {
    type: Number,
    required: true,
    min: 0,
    max: 5
  },
  reviewText: String,
  createdOn: {
    type: Date,
    'default': Date.now
  }
});
```

哎，是的；在这里你可以看到你将时间戳改为了`createdOn`，这是一个更准确的路径名称。

使用此值更新 Pug 文件看起来如下：

```
small.reviewTimestamp #{review.createdOn}
```

进行这些更改并刷新页面后，你会得到图 7.7。

##### 图 7.7\. 直接从返回的数据中提取名称和日期；日期的格式对用户来说不够友好。

![](img/07fig07_alt.jpg)

成功！某种程度上。日期现在显示出来了，但并不是你希望看到的那种用户可读的格式。你应该能够通过使用 Pug 来修复这个问题。

#### 7.3.6\. 使用 Pug 混合格式化日期

在设置视图时，你使用了一个 Pug 混合来根据提供的评分数字输出评分星级。在 Pug 中，混合就像函数一样；你可以调用它们时传递参数，如果你想的话，运行一些 JavaScript 代码，并让它们生成一些输出。

在多个地方格式化日期可能很有用，因此创建一个混合（mixin）来完成这项工作。你的`outputRating`混合在`app_server/views/_includes`中的共享 HTMLfunctions.pug 文件里。向该文件添加一个新的混合，名为`formatDate`。

在这个混合中，你将主要使用 JavaScript 将日期从长 ISO 格式转换为更易读的格式*Day Month Year*（例如*10 May 2017*）。ISO 日期对象以字符串形式到达这里，所以首先要做的事情是将它转换为 JavaScript 日期对象。完成这个操作后，你将能够使用各种 JavaScript 日期方法来访问日期的各个部分。

下面的列表展示了如何在混合中完成这个操作。记住，在 Pug 文件中的 JavaScript 代码行必须以一个短横线为前缀。

##### 列表 7.17\. 创建一个 Jade 混合来格式化日期

```
mixin formatDate(dateString)
  - const date = new Date(dateString);                                      *1*
  - const monthNames = ['January', 'February', 'March', 'April',
  'May', 'June', 'July', 'August', 'September', 'October', 'November',
  'December'];                                                            *2*
  - const d = date.getDate();                                               *3*
  - const m = monthNames[date.getMonth()];                                  *3*
  - const y = date.getFullYear();                                           *3*
  - const output = `${d} ${m} ${y}`;                                        *4*
  =output                                                                   *4*
```

+   ***1*** **将提供的日期字符串转换为日期对象**

+   ***2*** **设置一个包含月份名称的值的数组**

+   ***3*** **使用 JavaScript 数据方法提取和转换日期的所需部分**

+   ***4*** **将部分重新组合成所需的格式并渲染输出**

现在，这个混合接受一个日期并对其进行处理，以输出你想要的格式。当混合渲染输出时，你只需从代码中的正确位置调用它。以下代码演示了这次调用，再次基于整个模板中的相同两个独立的行：

```
span.reviewAuthor #{review.author.displayName}
small.reviewTimestamp
  +formatDate(review.createdOn)          *1*
```

+   ***1*** **从自己的行调用 mixin，传递评论的创建日期（确保新行正确缩进）**

应将 mixin 的调用放在新的一行上，所以你需要记住注意缩进；日期应该嵌套在`<small>`标签内。现在详情页面已经完成，看起来应该像图 7.8 中所示的那样。

##### 图 7.8\. 完整的详情页面。位置 ID 从 URL 传递到 API，API 检索数据并将其格式化后返回到页面进行正确渲染。

![](img/07fig08_alt.jpg)

很好；这正是你想要的。如果 URL 中包含数据库中找到的 ID，页面将很好地显示。但如果 ID 错误或未在数据库中找到呢？

#### 7.3.7\. 创建特定状态的错误页面

如果 URL 中的 ID 在数据库中找不到，API 将返回 404 错误。这个错误源于浏览器中的 URL，因此浏览器也应该返回 404；找不到该 ID 的数据，所以本质上页面是无法找到的。

通过使用本章中已经看到的技术，你可以轻松地捕获 API 返回 404 状态码的情况，在`request`回调中使用`response.statusCode`。你不想在回调中处理它，所以你会将流程传递到一个新的函数中，你可以调用它：`showError()`。

##### 捕获所有错误代码

捕获 404 响应比捕获更好，你可以将其颠倒过来，寻找 API 返回的任何不是 200 成功响应的响应。你可以将状态码传递给`showError()`函数，让它决定如何处理。为了使`showError()`函数保持控制，你还将传递`req`和`res`对象。

以下列表展示了如何更新`request`回调以渲染成功 API 调用详情页面，并将所有其他错误路由到`showError()`捕获函数。

##### 列表 7.18\. 捕获 API 未返回 200 状态码引起的任何错误

```
request(
  requestOptions,
  (err, {statusCode}, body) => {               *1*
    let data = body;
    if (statusCode === 200) {                  *2*
      data.coords = {                          *3*
        lng : body.coords[0],                  *3*
        lat : body.coords[1]                   *3*
      };                                       *3*
      renderDetailPage(req, res, data);        *3*
    } else {                                   *4*
      showError(req, res, statusCode);         *4*
    }                                          *4*

  }
);
```

+   ***1*** **你只对 statusCode 感兴趣，所以只获取它。**

+   ***2*** **检查 API 是否返回成功响应**

+   ***3*** **如果检查成功，则继续渲染页面**

+   ***4*** **如果检查不成功，将错误传递给`showError()`函数。**

很好；现在如果你从 API 获取了一些要显示的内容，你将尝试渲染详情页面。对于错误，你打算怎么办？嗯，目前你想要向用户发送一条消息，让他们知道存在问题。

##### 显示错误消息

你不想在这里做任何花哨的事情——只需让用户知道正在发生某些事情，并给他们一些关于它是什么的指示。你已经有一个适合此目的的通用 Pug 模板；它被称为`generic-text.pug`，只需要一个标题和一些内容。这就足够了。

如果你想，你可以为每种错误类型创建一个独特的页面和布局，但到目前为止，你只是满足于捕获它并让用户知道。此外，你应通过在页面显示时返回适当的状态码来让浏览器知道。

以下列表显示了`showError()`函数的样子，它接受一个状态参数，除了作为响应状态码传递之外，还用于定义页面的标题和内容。在这里，您有一个针对 404 页面的特定消息，以及针对传递的任何其他错误的通用消息。

##### 列表 7.19\. 为非 200 状态码的 API 状态码创建错误处理函数

```
const showError = (req, res, status) =>  {
  let title = '';
  let content = '';
  if (status === 404) {                                                   *1*
    title = '404, page not found';                                        *1*
    content = 'Oh dear. Looks like you can\'t find this page. Sorry.';    *1*
  } else {                                                                *2*
    title = `${status}, something's gone wrong`;                          *2*
    content = 'Something, somewhere, has gone just a little bit wrong.';  *2*
  }
  res.status(status);                                                     *3*
  res.render('generic-text', {                                            *4*
    title,                                                                *4*
    content                                                               *4*
  });                                                                     *4*
};
```

+   ***1*** **如果通过的状态是 404，则设置页面的标题和内容**

+   ***2*** **否则，设置一个通用的捕获消息**

+   ***3*** **使用状态参数设置响应状态**

+   ***4*** **将数据发送到视图进行编译并发送到浏览器**

这个函数可以从任何你可能觉得有用的控制器中重用。它还构建得易于添加新的、特定的错误消息，如果你想要的话。

您可以通过稍微更改 URL 中的位置 ID 来测试 404 错误页面，您应该会看到类似于图 7.9 的内容。

##### 图 7.9\. 当 API 在数据库中找不到 URL 中的位置 ID 时显示的 404 错误页面

![](img/07fig09_alt.jpg)

这就带您来到了详情页的结尾。您可以成功显示给定位置的数据库中的所有信息，如果找不到位置，还可以向访客显示 404 消息。

沿着用户旅程继续前进，您的下一个也是最后的任务是添加添加评论的功能。

### 7.4\. 通过 API 将数据添加到数据库：添加 Loc8r 评论

在本节中，您将了解如何处理用户提交的表单数据，对其进行处理，并将其发布到 API。通过点击位置详情页上的“添加评论”按钮，填写表单并提交，评论被添加到 Loc8r。无论如何，这是计划。您目前有执行此操作的视图，但没有实现其底层功能的代码。您需要立即改变这种情况。

下面是一个你将要做的列表：

1.  让评论表单知道评论将针对哪个位置。

1.  为表单创建一个`POST`路由。

1.  将新的评论数据发送到 API。

1.  在详情页上显示新的评论。

注意，在当前的开发阶段，你还没有设置认证方法，因此你没有用户账户的概念。

#### 7.4.1\. 设置路由和视图

您列表中的第一项涉及将位置的 ID 添加到“添加评论”页面，以便在表单提交时使用。毕竟，这个 ID 是 API 添加评论所需的唯一标识符。将 ID 添加到页面的最佳方法是将它包含在 URL 中，就像您为详情页面本身所做的那样。

##### 定义两个评论路由

将位置 ID 放入 URL 意味着更改“添加评论”页面的路由，以添加一个`locationid`参数。在此过程中，您还可以处理列表上的第二项，为表单创建一个`POST`请求的路由。理想情况下，这个路由应该与评论表单有相同的路径，并关联不同的请求方法和不同的控制器。为此，您将更新到`router.route`语法，使其明确您正在使用一个具有两种不同方法的单个路由。

以下代码片段显示了如何在`app_server/routes`文件夹中的 index.js 中更新路由：

```
router.get('/', ctrlLocations.homelist);
router.get('/location/:locationid', ctrlLocations.locationInfo);
router
  .route('/location/:locationid/review/new')          *1*
  .get(ctrlLocations.addReview)
  .post(ctrlLocations.doAddReview);                   *2*
```

+   ***1*** **更新 router.route 语法，并在评论表单路由中插入 locationid 参数**

+   ***2*** **在相同的 URL 上创建一个新的路由，但使用 POST 方法并引用不同的控制器**

这些路由就是本节所需的所有内容，但重新启动应用程序将失败，因为`POST`路由引用了一个不存在的控制器。您可以通过向控制器文件添加一个占位符函数来修复这个问题。将以下代码片段添加到`app_server/controllers`中的 locations.js，并将`doAddReview`添加到导出列表的底部。然后应用程序将再次成功启动：

```
const doAddReview = (req, res) => {
};
```

然而，如果您点击进入“添加评论”页面，您会得到一个错误。哦，是的——您需要从详情页面更新到“添加评论”页面的链接。

##### 修复位置详情视图

您需要将位置 ID 添加到详情页面上的“添加评论”按钮指定的`href`中。该页面的控制器将传递从 API 返回的完整数据对象，其中包含`_id`字段，以及其他数据。当传递到视图时，这个数据对象被称为`location`。

以下代码片段显示了`app_server/views`文件夹中的 location-info.pug 模板中的一行。这一行显示了如何将位置 ID 添加到“添加评论”按钮的链接中；请注意，您现在使用 JavaScript 模板字符串作为`href`值：

```
a.btn.btn-primary.float-right(href=`/location/${location._id}
/review/new`) Add review
```

在模板更新并保存后，您可以为每个单独的位置点击进入评论表单。然而，仍然存在一些问题：表单没有提交到任何地方，并且位置名称被硬编码到控制器中。

##### 更新评论表单视图

接下来，您想要确保表单提交到正确的 URL。现在，当表单提交时，它会向/location URL 发起一个`GET`请求：

```
form(action="/location", method="get", role="form")
```

这行代码来自`app_server/views`中的`location-review-form.pug`文件。在应用程序中，/location 路径不再有效，你也不想使用`GET`请求。你想要将表单发送到的 URL 与添加评论的 URL 相同：/location/:locationid/reviews/new。

实现此任务的一个简单方法是将表单的动作设置为空字符串，并将方法设置为`post`，如下所示：

```
form(action="", method="post", role="form")
```

现在，当表单提交时，它会向当前页面的 URL 发送一个`POST`请求。

##### 为渲染添加评论页面创建一个命名函数

与其他页面一样，你将把页面的渲染移动到单独的命名函数中。这一步骤允许你在编码时分离关注点，并为下一步做好准备。

以下列表显示了在代码中应该看起来是什么样子。请在`app_server/controllers`中的 locations.js 中进行更改。

##### 列表 7.20\. 为`addReview`控制器主体创建渲染函数

```
const renderReviewForm = (req, res) => {          *1*
  res.render('location-review-form', {
    title: 'Review Starcups on Loc8r',
    pageHeader: { title: 'Review Starcups' }
  });
};
/* GET 'Add review' page */
const addReview = (req, res) => {
  renderReviewForm(req, res);                     *2*
};
```

+   ***1*** 创建新的函数`renderReviewForm()`，并将`addReview`控制器的内容移动到其中

+   ***2*** 在`addReview`控制器内部调用新函数，并传递相同的参数

这段代码可能看起来有点奇怪——创建一个命名函数，然后让对该函数的调用成为控制器中唯一的事情——但它在稍后将会很有用。

##### 获取位置详情

在添加评论页面上，你想要显示位置名称以保留用户的上下文感。你想要再次调用 API，给它提供位置 ID，并将信息返回到控制器和视图中。你已经为详情页面做了这件事，尽管使用了不同的控制器。如果你正确地处理这个任务，你不需要编写太多新的代码。

而不是复制代码并维护两份，你会选择 DRY（不要重复自己）的方法。详情页面和添加评论页面都想要调用 API 获取位置信息，然后对其进行处理。那么为什么不创建一个执行此操作的新函数呢？你已经在`locationInfo`控制器中有了大部分代码；你需要改变它调用最终函数的方式。你将不再显式调用`renderDetailPage()`，而是将其作为一个回调。

你将有一个名为`getLocationInfo()`的新函数，它执行 API 请求。在请求成功后，此函数应调用传递的任何回调函数。`locationInfo`控制器调用此函数，传递一个调用`renderDetailPage()`函数的回调函数。同样，`addReview`控制器也可以调用这个新函数，传递`renderReviewForm()`函数作为回调。

这些更改使你拥有一个执行 API 调用并具有不同结果的函数，这取决于发送的回调函数。以下列表显示了所有内容。

##### 列表 7.21\. 创建一个新的可重用函数以获取位置信息

```
const getLocationInfo = (req, res, callback) => {                         *1*
  const path = `/api/locations/${req.params.locationid}`;
  const requestOptions = {
    url : `${apiOptions.server}${path}`,
    method : 'GET',
    json : {}
  };
  request(
    requestOptions,
    (err, {statusCode}, body) => {
      let data = body;
      if (statusCode === 200) {
        data.coords = {
          lng : body.coords[0],
          lat : body.coords[1]
        };
        callback(req, res, data);                                           *2*
      } else {
        showError(req, res, statusCode);
      }
    }
  );
};

const locationInfo = (req, res) => {
  getLocationInfo(req, res,
    (req, res, responseData) => renderDetailPage(req, res, responseData)
  );                                                                        *3*
};

const addReview = (req, res) => {
  getLocationInfo(req, res,
    (req, res, responseData) => renderReviewForm(req, res, responseData)
  );                                                                        *4*
};
```

+   ***1*** **新函数 getLocationInfo() 接受一个回调作为第三个参数，并包含原来在 locationInfo 控制器中使用的所有代码**

+   ***2*** **在成功的 API 响应后，调用回调而不是命名函数**

+   ***3*** **在 locationInfo 控制器中调用 getLocationInfo() 函数，传递一个回调函数，该函数在完成时将调用 renderDetailPage() 函数**

+   ***4*** **也从 addReview 控制器中调用 getLocationInfo()，但这次传递 renderReviewForm() 作为回调**

这样，你就得到了一个优雅的 DRY 方法来解决这个问题。很容易从控制器中复制粘贴 API 代码——如果我们说实话，如果你在弄清楚代码以及你需要做什么来让它工作，这是绝对可以接受的。但是当你看到两段几乎做同样的事情的代码时，总是要问自己如何使其 DRY，以使你的代码更干净、更容易维护。

##### 显示位置详情

你还有一件事要处理。用于渲染表单的函数仍然包含硬编码的数据而不是使用来自 API 的数据。对函数进行快速调整就可以改变这种情况。

##### 列表 7.22\. 从 `renderReviewForm` 函数中移除硬编码的数据

```
const renderReviewForm = function (req, res, {name}) {       *1*
  res.render('location-review-form', {
    title: `Review ${name} on Loc8r`,                        *2*
    pageHeader: { title: `Review ${name}` }                  *2*
  });
};
```

+   ***1*** **更新 renderReviewForm() 函数以接受一个包含数据的新的参数，解构为所需的内容**

+   ***2*** **使用模板字符串替换硬编码的数据为数据引用**

“添加评论”页面再次看起来不错，根据在 URL 中找到的 ID 显示正确的名称，如图 7.10 所示。

##### 图 7.10\. 通过 API 拉取包含在 URL 中的位置名称的“添加评论”页面

![](img/07fig10_alt.jpg)

#### 7.4.2\. 将评论数据 POST 到 API

到现在为止，你已经设置了“添加评论”页面并准备就绪，包括发布目的地。你甚至已经设置了`POST`操作的路线和控制台。不过，控制台`doAddReview`是一个空的占位符。

此控制器的计划如下：

1.  从 URL 中获取位置 ID 以构建 API 请求 URL。

1.  获取表单中提交的数据，并将其打包以供 API 使用。

1.  执行 API 调用。

1.  如果成功，则在当前位置显示新的评论。

1.  如果不成功，则显示错误页面。

你还没有看到此过程的唯一部分是将数据传递给 API；到目前为止，你已经传递了一个空的 JSON 对象以确保响应格式为 JSON。现在你将表单数据以 API 期望的格式传递给它。表单上有三个字段，API 期望有三个引用。你所需要做的就是将它们映射到一起。表单字段和模型路径显示在表 7.3 中。

##### 表 7.3\. 将表单字段名称映射到 API 期望的模型路径

| 表单字段 | API 引用 |
| --- | --- |
| 名称 | 作者 |
| 评分 | 评分 |
| 评论 | 评论文本 |

将此映射转换为 JavaScript 对象是直接的。创建一个新的对象，包含 API 期望的变量名，并使用`req.body`从提交的表单中获取值。以下代码片段显示了此对象，你将在稍后将其放入控制器中：

```
const postdata = {
  author: req.body.name,
  rating: parseInt(req.body.rating, 10),
  reviewText: req.body.review
};
```

现在你已经看到了它是如何工作的，你可以将其添加到你一直在使用的这些 API 控制器标准模式中，并构建出`doAddReview`控制器。记住，API 返回的成功的`POST`操作的状态码是 201，而不是你迄今为止在`GET`请求中使用的 200。以下列表显示了`doAddReview`控制器，使用了你迄今为止学到的所有内容。

##### 列表 7.23. `doAddReview`控制器用于将评论数据发布到 API

```
const doAddReview = (req, res) => {
  const locationid = req.params.locationid;                 *1*
  const path = `/api/locations/${locationid}/reviews`;      *1*
  const postdata = {                                        *2*
    author: req.body.name,                                  *2*
    rating: parseInt(req.body.rating, 10),                  *2*
    reviewText: req.body.review                             *2*
  };                                                        *2*
  const requestOptions = {
    url: `${apiOptions.server}${path}`,                     *3*
    method: 'POST',                                         *3*
    json: postdata                                          *3*
  };
  request(                                                  *4*
    requestOptions,
    (err, {statusCode}, body) => {
      if (statusCode === 201) {                             *5*
        res.redirect(`/location/${locationid}`);            *5*
      } else {                                              *5*
        showError(req, res, statusCode);                    *5*
      }
    }
  );
};
```

+   ***1*** 从 URL 获取位置 ID 以构建 API URL

+   ***2*** 创建一个数据对象以发送到 API，使用提交的表单数据

+   ***3*** 设置请求选项，包括路径，设置 POST 方法，并将提交的表单数据传递给 json 参数

+   ***4*** 发送请求

+   ***5*** 如果评论添加成功，则重定向到详情页面，如果 API 返回错误，则显示错误页面

现在你可以创建一个评论，提交它，然后可以在详情页面上看到它，如图 7.11 所示。

##### 图 7.11. 填写并提交评论表单后，评论在详情页面上显示。

![Images/07fig11_alt.jpg](img/07fig11_alt.jpg)

现在一切正常工作，让我们快速看一下如何添加表单验证。

### 7.5. 使用数据验证保护数据完整性

每当应用程序接受外部输入并将其添加到数据库时，你需要确保数据尽可能完整和准确——尽可能多或者有意义的那么多。如果有人添加一个电子邮件地址，你应该检查它是否是有效的电子邮件格式，但你不能通过编程方式验证它是否是一个*真实*的电子邮件地址。

在本节中，你将了解你可以如何向应用程序添加验证，以防止人们提交空评论。你可以在三个地方添加验证：

+   在使用 Mongoose 的模式级别，在数据保存之前

+   在应用级别，在数据发布到 API 之前

+   在客户端，在表单提交之前

你将依次查看这些地方，并在每个步骤中添加一些验证。

#### 7.5.1. 使用 Mongoose 在模式级别进行验证

在保存数据之前验证数据可能是最重要的阶段。这一步是最后一步，是确保一切尽可能正确的最后机会。当数据通过 API 公开时，这一阶段尤为重要；如果你无法控制所有使用 API 的应用程序，你无法保证你将获得的数据质量。在保存之前确保数据有效是很重要的。

##### 更新模式

当你在第五章中首次设置模式时，你查看在 Mongoose 中添加一些验证。你将`rating`路径设置为必需的，但你还想将`author displayName`和`reviewText`设置为必需的。如果这些字段中的任何一个缺失，评论就没有意义。将此添加到模式中很简单，如下所示列表。（模式在 app_api/model 文件夹中的 locations.js 中。）

##### 列表 7.24\. 在模式级别添加评论的验证

```
const reviewSchema = new mongoose.Schema({
  author: {                    *1*
    type: String,              *1*
    required: true             *1*
  },                           *1*
  rating: {                    *1*
    type: Number,              *1*
    required: true,            *1*
    min: 0,                    *1*
    max: 5                     *1*
  },                           *1*
  reviewText: {                *1*
    type: String,              *1*
    required: true             *1*
  },                           *1*
  createdOn: {                 *2*
    type: Date,                *2*
    'default': Date.now        *2*
  }                            *2*
});
```

+   ***1*** **将这些路径中的每一个都设置为必需字段，因为如果其中任何一个缺失，评论就没有意义**

+   ***2*** **createdOn 不需要是必需的，因为 Mongoose 在创建新评论时会自动填充它。**

当这段代码被保存时，你将无法在没有任何评论文本的情况下保存评论。你可以尝试，但你会看到图 7.12 中显示的错误页面。

##### 图 7.12\. 当尝试保存没有评论文本的评论时显示的错误消息，因为模式表明它是必需的

![](img/07fig12_alt.jpg)

一方面，你保护数据库是好事，但用户体验并不好。你应该尝试捕捉那个错误，并让访客再次尝试。

##### 捕获 Mongoose 验证错误

如果你尝试保存一个缺少一个或多个必需路径或为空的文档，Mongoose 会返回一个错误。它这样做而不需要调用数据库，因为 Mongoose 本身持有模式并知道什么是必需的。以下代码片段显示了一个这样的错误消息示例：

```
{
  message: 'Validation failed',
  name: 'ValidationError',
  errors: {
    'reviews.1.reviewText': {
      message: 'Path `reviewText` is required.',
      name: 'ValidatorError',
      path: 'reviewText',
      type: 'required',
      value: ''
    }
  }
}
```

在应用程序的流程中，这发生在`save`函数的回调中。如果你查看`doAddReview()`函数（在 app_api/controllers/reviews.js 中）内的`save`命令，你可以看到错误是如何冒泡的以及你设置了 400 状态。以下代码片段显示了这一点，包括一个临时的控制台日志语句来显示错误输出到终端：

```
location.save((err, location) =>  {
  if (err) {                              *1*
    console.log(err);                     *1*
    res                                   *1*
      .status(400)                        *1*
      .json(err);                         *1*
  } else {
    updateAverageRating(location._id);
    let thisReview = location.reviews[location.reviews.length - 1];
    res
      .status(201)
         .json(thisReview);
  }
});
```

+   ***1*** **Mongoose 验证错误是在尝试保存操作后通过一个错误对象返回的。**

你的 API 将此消息作为响应体返回，并附带 400 状态。你可以在 API 返回 400 状态时查看响应体以查找此信息。

做这件事的地方是在 app_server 中——在 controllers/locations.js 中的`doAddReview()`函数中，更确切地说。当你捕捉到一个验证错误时，你想要通过重定向到添加评论页面让用户再次尝试。为了使页面知道已经尝试过，你可以在查询字符串中传递一个标志。

以下列表显示了这段代码的位置，在`doAddReview()`函数的请求语句回调中。

##### 列表 7.25\. 捕获 API 返回的验证错误

```
request(
  requestOptions,
  (err, {statusCode},{name}) => {
    if (statusCode === 201) {
      res.redirect(`/location/${locationid}`);
    } else if (statusCode === 400
        && name && name === 'ValidationError') {                      *1*
      res.redirect(`/location/${locationid}/review/new?err=val`);     *2*
    } else {
      console.log(body);
      showError(req, res, statusCode);
    }
  }
);
```

+   ***1*** **添加了一个检查，看状态是否为 400，正文是否有名称，并且该名称是** **ValidationError**

+   ***2*** **如果为真，则通过查询字符串传递错误标志重定向到评论表单**

现在当 API 返回验证错误时，你可以捕获它并将用户送回表单再次尝试。在查询字符串中传递一个值意味着你可以在显示评论表单的控制器中查找它，并发送一条消息到视图以提醒用户问题。

##### 在浏览器中显示错误消息

在视图中显示错误消息，如果你在查询字符串中看到了传递的`err`参数，你需要将一个变量发送到视图中。`renderReviewForm()`函数负责将变量传递到视图中。当它被调用时，它也会传递`req`对象，该对象包含`query`对象，这使得在存在的情况下传递`err`参数变得容易。以下列表显示了实现这一功能所需进行的简单更改。

##### 列表 7.26\. 更新控制器以从查询对象传递错误字符串到视图

```
const renderReviewForm = (req, res,{name}) => {
  res.render('location-review-form', {
    title: `Review ${name} on Loc8r`,
    pageHeader: { title: `Review ${name}` },
    error: req.query.err                      *1*
  });
};
```

+   ***1*** **向视图发送新的错误变量，传递任何现有的查询参数**

`query`对象始终是`req`对象的一部分，无论它是否有内容。这就是为什么你不需要错误捕获此对象来检查它是否存在；如果找不到`err`参数，它返回`undefined`。

剩下的就是要在视图中处理这些信息，让用户知道问题是什么。如果验证错误被冒泡到顶部，你将在表单顶部向用户显示一条消息。为了给这条消息一些样式和页面上的存在感，你将使用一个 Bootstrap 警告组件：一个带有相关类和属性的`div`。以下代码片段显示了要添加到`location-review-form`视图中的两行代码：

```
form(action="", method="post", role="form")
  - if (error == "val")
    .alert.alert-danger(role="alert") All fields required, please try again
```

现在当 API 返回验证错误时，你捕获它并向用户显示一条消息。图 7.13 显示了这条消息的外观。

##### 图 7.13\. 浏览器中的验证错误消息，这是由 Mongoose 捕获错误并返回错误的结果

![](img/07fig13_alt.jpg)

这种在 API 级别的验证很重要，并且通常是一个很好的起点，因为它可以保护数据库免受不一致或不完整数据的影响，无论其来源如何。但最终用户的体验并不总是最好的；他们必须提交表单，表单请求在页面重新加载并显示错误之前会绕一圈到 API。显然，还有改进的空间，第一步是在将数据传递到 API 之前在应用级别执行一些验证。

#### 7.5.2\. 使用 Node 和 Express 在应用级别进行验证

在模式级别进行验证是最后的防线，是数据库前的最后一道防线。然而，应用程序不应仅依赖于这个防线，你应该尝试减少对 API 的不必要调用，以减少开销并加快用户的速度。一种方法是在应用程序级别添加验证，在将数据发送到 API 之前检查提交的数据。

在你的应用程序中，评论所需的验证很简单；你可以添加一些简单的检查以确保每个字段都有值。如果这个测试失败，你将用户重定向回表单，添加与之前相同的查询字符串错误标志。如果验证检查成功，你允许控制器继续到请求方法。以下列表显示了在 app_server/controllers 文件夹中的 locations.js 中的 `doAddReview` 控制器所需添加的内容。

##### 列表 7.27\. 向 Express 控制器添加一些简单的验证

```
const doAddReview = (req, res) =&gt; {
  const locationid = req.params.locationid;
  const path = `/api/locations/${locationid}/reviews`;
  const postdata = {
    author: req.body.name,
    rating: parseInt(req.body.rating, 10),
    reviewText: req.body.review
  };
  const requestOptions = {
    url: apiOptions.server + path,
    method: 'POST',
    json: postdata
  };

  if (!postdata.author || !postdata.rating || !postdata.reviewText) {    *1*
    res.redirect(`/location/${locationid}/review/new?err=val`);          *1*
  } else {                                                               *3*
    request(
      requestOptions,
      (err, {statusCode},{name}) =&gt; {
        if (statusCode === 201) {
          res.redirect(`/location/${locationid}`);
        } else if (statusCode === 400 && name
          && name === 'ValidationError' ) {
          res.redirect(`/location/${locationid}/review/new?err=val`);
        } else {
          showError(req, res, statusCode);
        }
      }
    );
  }
};
```

+   ***1*** **如果三个必需的数据字段中的任何一个为假，则重定向到“添加评论”页面，并附加用于显示错误消息的查询字符串**

+   ***2*** **否则，继续之前的操作**

结果与之前相同：如果评论文本缺失，用户将在“添加评论”页面上看到错误消息。用户不知道你不再向 API 发送数据，但这减少了往返次数，因此应该是一个更快的体验。但是，你可以通过第三级验证：基于浏览器的验证来使其更快。

#### 7.5.3\. 使用 jQuery 在浏览器中进行验证

由于应用程序级别的验证通过不需要调用 API 来加快速度，因此浏览器端的客户端验证可以通过在表单提交到应用程序之前捕获错误来加快速度，通过减少另一个调用。在这一点上捕获错误将保持用户在同一个页面上。

要在浏览器中运行 JavaScript，你需要将其放置在应用程序的 public 文件夹中。Express 将此文件夹的内容视为要下载到浏览器的静态文件，而不是在服务器上运行。如果你在 public 文件夹中没有名为 javascripts 的文件夹，现在创建一个。在这个文件夹中，创建一个名为 validation.js 的新文件。

##### 编写 jQuery 验证

在这个新的 validation.js 文件中，放置一个 jQuery 函数，执行以下操作：

+   监听评论表单的提交事件

+   检查所有必需字段是否有值

+   如果其中一个为空，显示类似于其他类型验证中使用的错误消息，并阻止表单提交

以下列表显示了执行此操作的代码。我们不会深入探讨 jQuery 的语义，假设你对它或类似的库有所了解。

##### 列表 7.28\. 创建一个 jQuery 表单验证函数

```
$('#addReview').submit(function (e) {                                    *1*
  $('.alert.alert-danger').hide();
  if (!$('input#name').val() || !$('select#rating').val() ||
  !$('textarea#review').val()) {                                       *1*
    if ($('.alert.alert-danger').length) {                               *3*
      $('.alert.alert-danger').show();                                   *3*
    } else {                                                             *3*
      $(this).prepend('<div role="alert" class="alert alert-danger">     *3*
      All fields required, please try again</div>');                   *3*
    }                                                                    *3*
    return false;                                                        *4*
  }
});
```

+   ***1*** **监听评论表单的提交事件**

+   ***2*** **检查是否有缺失的值**

+   ***3*** **如果值缺失，则在页面上显示或注入错误消息**

+   ***4*** 防止表单在缺少值时提交

你需要确保表单有一个名为`addReview`的 ID，这样 jQuery 可以监听正确的事件。你还需要将此脚本添加到页面中，以便浏览器可以运行它。

##### 将 jQuery 添加到页面中

你将在 body 的末尾包含此 jQuery 文件，以及其他客户端 JavaScript 文件。这些文件在`layout.pug`视图中设置，位于 app_server/views 的底部。在下面的其他文件下方添加新行，指向新文件：

```
script(src='/bootstrap/js/bootstrap.min.js')
script(src='/javascripts/validation.js')
```

就这些了。现在表单在浏览器中进行了验证，而无需提交任何数据，从而消除了页面刷新和任何相关的服务器调用。

| |
| --- |

##### 小贴士

客户端验证可能看起来是你所需要的全部，但其他类型对于应用程序的健壮性至关重要。在浏览器中可以关闭 JavaScript，这将移除运行此验证的能力，或者验证可以被绕过，数据可以直接提交到表单操作 URL 或 API 端点。

| |
| --- |

在第八章中，你将把 Angular 引入其中，并开始在 Express 应用程序之上玩一些交互式前端组件。

### 摘要

在本章中，你学习了

+   如何使用`request`模块从 Express 中发起 API 调用，以及如何向 API 端点发送`POST`和`GET`请求

+   一些通过将渲染函数与 API 请求逻辑分离来分离关注点的方法

+   如何将简单模式应用于每个控制器的 API 逻辑

+   在架构中的三个位置应用数据验证，以及何时以及为什么使用每个
