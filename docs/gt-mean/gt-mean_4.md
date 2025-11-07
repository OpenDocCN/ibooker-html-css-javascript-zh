## 附录 A. 安装堆栈

*本附录涵盖*

+   安装 Node 和 npm

+   全局安装 Express

+   安装 MongoDB

+   安装 Angular

在您可以在 MEAN 堆栈上构建任何内容之前，您需要安装运行它的软件。在 Windows、macOS 和流行的 Linux 发行版（如 Ubuntu）上，这项任务都很简单。

由于 Node 是堆栈的基础，因此这是开始的最佳位置。Node 随附 npm，这对于安装其他软件将很有用。

## 安装 Node 和 npm

安装 Node 和 npm 的最佳方式取决于您的操作系统。只要可能，我们建议您从 Node 网站下载安装程序，网址为 [`nodejs.org/download`](https://nodejs.org/download)。该位置总是有 Node 核心团队维护的最新版本。

#### Node 的长期支持版本

我们建议使用 Node 的长期支持（LTS）版本。这些版本具有偶数的主版本号，例如 Node 8 和 Node 10。这些版本是 Node 的稳定分支，将在 18 个月内进行维护和修补，不会引入破坏性更改。本书中的应用程序是针对 Node 11 构建的，因此最佳 LTS 版本是 10。本书中使用的功能在版本之间没有不兼容之处，因此请随意使用任何一个。

#### 在 Windows 上安装 Node

Windows 用户应从 Node 网站下载安装程序。

#### 在 macOS 上安装 Node

对于 macOS 用户来说，从 Node 网站下载安装程序是最佳选择。或者，您可以使用 Homebrew 软件包管理器安装 Node 和 npm，具体细节请参考 Joyent 的 Node 维基，网址为 [`github.com/joyent/node/wiki/Installing-Node.js-via-package-manager`](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)。

#### 在 Linux 上安装 Node

对于 Linux 用户来说，没有安装程序，但如果您熟悉它们，可以从 Node 网站下载二进制文件。

或者，Linux 用户可以从软件包管理器中安装 Node。软件包管理器并不总是有最新版本，请注意这一点。一个特别过时的例子是 Ubuntu 上流行的 APT 系统。您可以在 GitHub 上 Joyent 的 Node 维基上找到使用各种软件包管理器的说明，包括 Ubuntu 上 APT 的修复，网址为 [`github.com/joyent/node/wiki/Installing-Node.js-via-package-manager`](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)。

#### 通过检查版本来验证安装

在您安装了 Node 和 npm 之后，您可以使用几个终端命令来检查您拥有的版本：

```
$ node --version
$ npm --version
```

这些命令会输出您机器上安装的 Node 和 npm 版本。本书中的代码是用 Node 11.2.0 和 npm 6.4.1 构建的。

## 全局安装 Express

要能够从命令行即时创建新的 Express 应用程序，您需要安装 Express 生成器。您可以使用 npm 从命令行执行此操作。在终端中，运行以下命令：

```
$ npm install -g express-generator
```

如果此命令因权限错误而失败，你需要以管理员身份运行它。在 Windows 上，右键单击命令提示符图标，从上下文菜单中选择“以管理员身份运行”。然后，在生成的窗口中再次尝试前面的命令。

在 macOS 和 Linux 上，你可以在命令前加上 `sudo`，如下面的代码片段所示；你将被提示输入密码。

```
$ sudo npm install -g express-generator
```

当生成器完成 Express 的安装后，你可以通过在终端检查版本号来验证它：

```
$ express --version
```

本书代码示例中使用的 Express 版本为 4.16.4。

如果你在安装过程中遇到任何问题，Express 的文档可在其网站上找到，网址为 [`expressjs.com`](http://expressjs.com)。

## 安装 MongoDB

MongoDB 也可用于 Windows、macOS 和 Linux。有关所有以下选项的详细说明，请参阅文档中的[`docs.mongodb.com/manual/administration/install-community`](https://docs.mongodb.com/manual/administration/install-community)。

#### 在 Windows 上安装 MongoDB

根据你运行的 Windows 版本，一些直接下载可在[`docs.mongodb.org/manual/installation`](https://docs.mongodb.org/manual/installation)找到。

#### 在 macOS 上安装 MongoDB

对于 macOS，最简单的安装 MongoDB 的方法是使用 Homebrew 软件包管理器，但如果你愿意，你也可以选择手动安装 MongoDB。

#### 在 Linux 上安装 MongoDB

所有主流 Linux 发行版都提供了相应的软件包，具体详情请参阅[`docs.mongodb.org/manual/installation`](https://docs.mongodb.org/manual/installation)。如果你运行的 Linux 版本中没有提供 MongoDB 软件包，你可以选择手动安装它。

#### 将 MongoDB 作为服务运行

在你安装 MongoDB 后，你可能希望将其作为服务运行，以便在重启时自动重启。同样，你可以在 MongoDB 安装文档中找到相关说明。

#### 检查 MongoDB 版本号

MongoDB 不仅安装了自身，还安装了一个 Mongo shell，这样你就可以通过命令行与 MongoDB 数据库进行交互。你可以独立检查 MongoDB 和 Mongo shell 的版本号。要检查 shell 版本，请在终端运行以下命令：

```
$ mongo --version
```

要检查 MongoDB 的版本，请运行以下命令：

```
$ mongod --version
```

本书使用 MongoDB 和 Mongo shell 的版本均为 4.0.4。

## 安装 Angular

只要你已经安装了 Node 和 npm，Angular 就很容易安装。你实际上安装的是 Angular CLI 作为全局 npm 软件包。为此，请在终端运行以下命令：

```
$ npm install -g @angular/cli
Currently,  this  command  installs  Angular  CLI  version  7.0.6,  which  covers
Angular 7.1.0.
```
