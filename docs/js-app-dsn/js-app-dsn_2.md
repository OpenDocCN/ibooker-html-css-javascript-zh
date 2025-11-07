## 附录 A. Node.js 中的模块

本附录涵盖了你在 Grunt 构建中使用模块和 Node.js 时需要了解的内容。Node.js 是一个建立在 V8 JavaScript 引擎之上的平台，这个引擎使得 Google Chrome 中的 JavaScript 成为现实。本书中使用的构建工具 Grunt 运行在 Node 上。Node 是单线程的，就像所有的 JavaScript 一样。

Node 附带了一个小巧的命令行界面（CLI）实用工具，名为`npm`，用于从 node-packaged 模块注册表中检索和安装包。在整个书中，你将学习如何根据需要使用`npm`工具。让我们首先安装 Node.js，因为`npm`是捆绑在一起的！

### A.1. 安装 Node.js

安装 Node 有多种选择。如果你是点击类型的人，那么你可能想去他们的网站[`nodejs.org`](http://nodejs.org)，点击那个大绿色的安装按钮。一旦二进制文件下载完成，如果需要，解压它们，然后双击安装。就这样。

如果你更喜欢在终端中安装东西，可以考虑使用`nvm`，这是一个用户创建的 Node 版本管理器。要安装`nvm`，你可以在你的终端中输入以下行：

```
curl https://raw.github.com/creationix/nvm/master/install.sh | bash
```

一旦安装了`nvm`，重新打开你的终端窗口以获取对`nvm` CLI 的访问权限。如果你在安装`nvm`时遇到任何问题，请参考他们的公共仓库[`github.com/creationix/nvm`](https://github.com/creationix/nvm)。一旦你有了`nvm`，你就可以安装 Node 的一个版本，如下面的代码所示：

```
nvm install 0.10
nvm alias default stable
```

第一个命令安装了`0.10.x`分支的最新稳定版本的 Node。第二个命令使得从现在开始打开的任何终端窗口都可以访问你安装的 Node 版本。

太好了，现在你有了 Node！是时候学习更多关于它的模块系统了，该系统基于 CommonJS 模块规范。

### A.2. 模块系统

Node 应用程序在执行 node 进程时指定一个入口点。例如，如果你运行`node app.js`，你的 Node 进程将使用`app.js`作为入口点。要加载其他代码片段，你必须使用`require`函数。这个函数接受一个路径，并加载在该位置找到的模块。传递给`require`的路径可以是

+   相对于你`require`的脚本的路径，以`'.'`开头。例如，如果你执行`require('./main.js')`，你将加载与要求脚本相同的目录中的文件。我们也可以使用`..`来获取父目录中的脚本。

+   到一个目录的路径。在这些情况下，`require`将在提供的目录中查找名为`index.js`的文件，并将其提供给你。

+   绝对路径。这个很少使用，但你可能需要提供一个绝对文件路径，如下面的代码所示：

    ```
    require('/Users/nico/dev/buildfirst/main.js')
    ```

+   包的名称。你可以仅通过提供包名来引入包；例如，要获取 `async` 包，你应该使用 `require('async')`。大多数情况下，这实际上等同于执行 `require('./node_modules/async')`。

### A.3\. 导出功能

如果不能与模块交互，那么引入模块将没有用处。模块可以通过将内容赋值给 `module.exports` 来导出功能，实际上就是它们的 API。例如，考虑以下模块：

```
var mine = 'gold';

module.exports = function (pure) {
    return pure + mine;
};
```

如果你使用 `var thing = require('./thing.js')` 获取了这个模块，那么 `thing` 将被分配 `thing.js` 内部 `module.exports` 最终成为的内容。值得注意的是，与浏览器模型不同，在浏览器模型中，`window` 被隐式地分配了全局变量，而 CommonJS 模块系统将你在模块中声明的变量保持为私有，除非你通过将内容赋值给 `module.exports` 来显式地使它们公开。Node 有一个你可以分配的 `global` 对象，称为 `global`，但使用它是不被推荐的，因为这会破坏模块化原则。

### A.4\. 关于包

依赖关系被保存在一个 `package.json` 文件中，该文件被 `npm` 用于确定运行应用程序所需的包。在安装包时，你可以提供一个 `--save` 标志，让 `npm` 自动将那个依赖关系持久化到 `package.json` 清单中，这样你就不必手动操作。每当你在没有任何参数的情况下运行 `npm install` 时，`package.json` 中的依赖关系就会被安装。

本地依赖关系被安装到 `node_modules` 目录中，这个目录应该在版本控制中忽略。在 Git 的情况下，你可以在名为 `.gitignore` 的文件中添加包含 `node_modules` 的行，Git 就会知道不要对这些文件进行版本控制。

关于 Node 的知识，你只需要知道这些就能在你的 Grunt 构建中有效地使用它。
