## 附录 B. Grunt 简介

Grunt 是一个允许你编写、配置和自动化任务的工具——例如压缩 JavaScript 文件或编译 LESS 样式表——用于你的应用程序。

LESS 是一个 CSS 预处理器，在第二章中介绍。压缩基本上是通过删除空白和许多语法树优化来创建一个更小的文件。这些任务也可以与代码质量相关，例如运行单元测试（在第八章中介绍）或执行代码覆盖率工具，如 JSHint。它们当然可以与部署过程相关：可能是通过 FTP 部署应用程序，或者准备部署，生成 API 文档。

Grunt 仅仅是执行你的构建任务的工具。这些任务使用插件定义，如下一部分所述。

### B.1. Grunt 插件

Grunt 只提供框架；你负责选择正确的插件来执行你需要的任务。例如，你可能使用`grunt-contrib-concat`来捆绑资源。你还需要配置这些插件以完成你想要的工作；例如，提供一个要捆绑的文件列表和结果捆绑文件的路径。

插件可以定义一个或多个 Grunt 任务。这些插件使用 Node 平台上的 JavaScript 编写和配置。Node 社区开发了数百个现成的 Grunt 插件，你只需要配置它们，就像你马上会看到的那样。如果你找不到适合你特定需求的插件，你也可以自己创建 Grunt 插件。

### B.2. 任务和目标

任务可以被配置为符合多个目标，并且每个目标在配置任务时通过添加更多数据来定义。任务目标的一个常见用途是针对不同的发行版编译应用程序，如第三章所述。目标对于以略微不同的目的重复使用相同的任务非常有用。LESS 是一种表达式丰富的语言，它编译成 CSS。你可能会有 LESS 任务目标，用于编译应用程序的不同部分。也许你需要使用不同的目标，因为其中一个目标通过添加指向原始 LESS 代码的源映射来使调试更容易，而另一个目标可能将样式表压缩到最小。

### B.3. 命令行界面

Grunt 附带了一个命令行界面（CLI），称为`grunt`，你可以使用它来运行你的任务。为了分析这个工具的工作方式，让我们分析以下语句：

```
grunt less:debug mocha
```

假设你已经配置了 Grunt，你将在下一部分了解它，这个语句将执行`less`任务的`debug`目标，如果该任务成功，那么为`mocha`任务配置的任何目标都将被执行。重要的是要注意，如果 Grunt 任务失败，Grunt 不会尝试运行更多任务。相反，它将在打印失败原因后退出。

值得注意的是，任务是以串行方式执行的：下一个任务只有在当前任务完成后才开始。它们不会并行运行。你不必每次都给 CLI 一个完整的任务列表，你可以使用任务别名：执行一系列任务的别名。如果你在创建别名时使用特殊名称 `default`，那么分配给该别名的任务将在没有任务参数的情况下执行 `grunt` CLI。

理论已经足够了！让我们通过实际操作来学习 Grunting；你将从安装 Grunt 开始，并扩展我们讨论的所有领域。要安装 Grunt，你首先需要 Node，这是 Grunt 工作的平台。要安装 Node，请访问 附录 A，然后立即回到这里。我会等待。

好吧，让我们安装 Grunt CLI。在终端中使用 `npm`，输入以下命令：

```
npm install --global grunt-cli
```

`--global` 标志告诉 `npm` 这不是一个项目级别的包安装，而是一个全局安装。本质上，这将最终使你能够直接从命令行使用该包。你可以通过运行以下命令来验证 CLI 是否已正确安装：

```
grunt --version
```

这应该会输出当前安装的 Grunt CLI 的版本号。太好了！你到目前为止所做的一切都是一次性的；你不需要担心再次执行这些步骤。但你是如何使用 Grunt 的呢？

### B.4\. 在项目中使用 Grunt

假设你有一个 PHP 网络应用程序（尽管服务器端语言并不重要），并且你想在更改 JavaScript 文件时自动运行一个代码检查器，这是一个静态分析工具，可以告诉你关于你使用的语法的错误。

你首先需要在项目根目录中有一个 `package.json` 文件。这个文件由 `npm` 使用来维护所有依赖项的清单。这个文件不需要太多；它需要是一个有效的 JSON 对象，所以 `{}` 就可以了。将目录切换到你的应用程序根目录，并在终端中输入以下内容：

```
echo "{}" > package.json
```

接下来，你将不得不安装一些依赖项。你需要安装 `grunt`，这是框架本身，不要与 `grunt-cli` 混淆，`grunt-cli` 用于查找内容并将任务执行委托给本地安装的 `grunt` 包。要开始，你还需要安装 `grunt-contrib-jshint`，这是一个易于配置的任务，可以将 JSHint（一个 JavaScript 代码检查工具）作为 Grunt 任务运行。`npm install` 命令允许你一次性安装多个包，所以让我们这样做：

```
npm install --save-dev grunt grunt-contrib-jshint
```

`--save-dev` 标志告诉 `npm` 将这些包包含在 `package.json` 清单中，并将它们标记为开发依赖项。将不应在生产服务器上执行的内容标记为开发依赖项是一种最佳实践。构建组件应始终在执行应用程序之前运行。

你已经有了框架、插件和 CLI；唯一缺少的是配置任务，这样你就可以开始使用 Grunt 了。

### B.5\. 配置 Grunt

要配置 Grunt，你需要创建一个`Gruntfile.js`文件。所有你的构建任务配置和定义都将存储在这里。以下是一个示例`Gruntfile.js`：

```
module.exports = function (grunt) {
  grunt.initConfig({
    jshint: {
      browser: ['public/js/**/*.js']
    }
  });
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.registerTask('default', ['jshint']);
};
```

如附录 A（kindle_split_022.html#app01）中所述，我们在其中讨论了 Common.JS 模块，这里模块导出一个函数，Grunt 将调用该函数来配置你的任务。`initConfig`方法接受一个对象，该对象将作为所有不同任务和目标的配置。这个配置对象中的每个顶级属性代表特定任务的配置。例如，`jshint`包含`jshint`任务的配置。每个任务配置中的属性代表目标配置。

在这种情况下，你正在为`jshint`的`browser`目标配置`['public/js/**/*.js']`。这被称为文件匹配模式，它用于声明要针对哪些文件。你稍后将会学到所有关于文件匹配模式的知识；现在只需说它将匹配`public/js`或其子目录中的任何`.js`文件就足够了。

`loadNpmTasks`方法告诉 Grunt，“嘿，加载你在这个 Grunt 插件中能找到的所有任务”，所以它本质上是在加载`jshint`任务。你稍后会学到如何编写自己的任务。

最后，`registerTask`可以通过传递一个任务名称和应该执行的任务数组来定义任务别名。你将把它设置为`jshint`，这样它就会运行`jshint:browser`以及你未来可能添加的任何其他`jshint`目标。默认名称意味着当你不提供任务参数在命令行中执行`grunt`时，这个任务将会运行。让我们试试看！

```
grunt
```

恭喜你，你已经执行了你的第一个 Grunt 任务！然而，你可能对整个“文件匹配”的概念感到困惑；让我们来解决这个问题。

### B.6. 文件匹配模式

使用如`['public/js/**/*.js']`这样的模式可以帮助快速定义要处理的文件。只要你能适当地理解如何使用它们，这些模式就很容易遵循。Glob 允许你使用纯文本来引用真实的文件系统路径。例如，你可以使用`docs/api.txt`而不需要任何特殊字符，这将匹配`docs/api.txt`中的文件。请注意，这是一个相对路径，并且它将相对于你的`Gruntfile.js`。

如果你混合了特殊字符，事情就变得有趣了。例如，将你的最后一个例子改为`docs/*.txt`可以帮助我们匹配`docs`目录中的所有文本文件。如果你想包括子目录，那么你需要使用`**`，也就是 globstar 模式：`docs/**/*.txt`。

#### B.6.1. 大括号表达式

然后是括号展开。假设你想匹配多种不同类型的图像；你可能想使用以下模式：`images/*.{png,gif,jpg}`。这将匹配以 `.png`、`.gif` 和 `.jpg` 结尾的任何图像。它不仅限于扩展名，尽管这是最常见的情况。你也可以使用括号展开来匹配不同的目录：`public/{js,css}/**/*`。请注意，我们排除了扩展名。这没问题；星号将匹配任何文件类型，而不仅限于特定的一种。

#### B.6.2\. 否定表达式

最后，还有否定表达式，这些表达式有些难以正确使用。*否定表达式*可以定义为“从你之前匹配的内容中移除匹配的结果。”模式按顺序处理，因此包含和排除的顺序很重要。否定模式以 `!.` 开头。这里有一个常见的用例：`['js/**/*.js', '!js/vendor/**/*.js']`。这意味着，“包含 `js` 目录中的所有内容，但如果不包含在 `js/vendor` 中。”这对于检查你编写的代码很有用，同时保持第三方库不变。

我想特别指出 globbing 中的一个注意事项；我经常看到有人抱怨 `['js', '!js/vendor']` “不起作用”，现在你知道了 globbing 的工作原理，这个原因就很容易理解了。第一个 globbing 模式会匹配 `js` 目录本身，而 `!js/vendor` 不会做任何事情。稍后，`js` 目录将扩展到其中的每个文件，包括 `js/vendor` 中的文件。解决这个问题的一个快速方法是让 Globber 为你展开目录，使用 globstars：`['js/**/*.js', '!js/vendor/**']`。

还有两个主题需要你掌握：配置任务和创建自己的任务。让我们继续看看如何从头开始配置 Grunt 来运行任务。

### B.7\. 设置任务

现在，你将学习如何通过浏览互联网来设置一个随机任务...作为一个快速入门技巧，让我们回到 B.1 节 的原始示例。记得你是如何配置它来运行 JSHint 的吗？这里是你使用的代码：

```
module.exports = function (grunt) {
  grunt.initConfig({
    jshint: {
      browser: ['public/js/**/*.js']
    }
  });
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.registerTask('default', ['jshint']);
};
```

假设你想压缩（在第二章中介绍）你的 CSS 样式表，并将它们合并成一个文件。你可以搜索 Google 上的 grunt 插件来做这件事，或者你可能访问 [`gruntjs.com/plugins`](http://gruntjs.com/plugins) 并自行查找。前往那个页面，然后输入 `css`。你将看到的第一项结果之一是 `grunt-contrib-cssmin`，它将链接到该软件包在 `npm` 网站上的页面。

在 `npm` 上，你通常会找到详细的 README 文件，以及指向 GitHub 仓库中完整源代码的链接。在这种情况下，它指导你从 `npm` 安装该软件包，并将 `loadNpmTasks` 添加到你的 `Gruntfile.js` 中，如下面的代码所示：

```
module.exports = function (grunt) {
  grunt.initConfig({
    jshint: {
      browser: ['public/js/**/*.js']
    }
  });
  grunt.loadNpmTasks('grunt-contrib-jshint');

  grunt.loadNpmTasks('grunt-contrib-cssmin');
  grunt.registerTask('default', ['jshint']);
};
```

你还必须以与之前安装 `grunt-contrib-jshint` 时相同的方式从 `npm` 安装该软件包：

```
npm install --save-dev grunt-contrib-cssmin
```

现在你只需要进行配置。Grunt 项目通常有很好的文档，在它们的首页上提供了几个配置示例，以及所有可用选项的详细列表。名为`grunt-contrib-*`的包是由 Grunt 本身背后的团队开发的，所以它们应该基本上没有问题就能正常工作。在寻找适合任务的正确包时，如果某个包不起作用或者文档不完善，就继续寻找。你不必非得选择它们。流行度（npm 安装和 GitHub 星标）是衡量一个包好坏的好指标。

结果表明，第一个使用示例显示你也可以使用这个包来连接你的 CSS，因此你不需要额外的任务来做这件事。以下是一个示例，展示了如何使用`grunt-contrib-cssmin`在压缩的同时合并两个文件：

```
cssmin: {
  combine: {
    files: {
      'path/to/output.css': ['path/to/input_one.css', 'path/to/input_two.css']
    }
  }
}
```

你可以轻松地根据你的需求进行修改和整合。你还将添加一个`build`任务别名。别名对于定义工作流程非常有用，正如你将在第一部分中看到的。例如，第三章使用它们来定义调试和发布工作流程：

```
module.exports = function (grunt) {
  grunt.initConfig({
    jshint: {
      browser: ['public/js/**/*.js']
    },
    cssmin: {
      all: {
        files: { 'build/css/all.min.css': ['public/css/**/*.css'] }
      }
    }
  });
  grunt.loadNpmTasks('grunt-contrib-jshint');
  grunt.loadNpmTasks('grunt-contrib-cssmin');
  grunt.registerTask('default', ['jshint']);
  grunt.registerTask('build', ['cssmin']);
};
```

就这样！如果你在终端中运行`grunt build`，它将把你的 CSS 文件捆绑在一起，然后进行压缩，并将结果写入`all.min.css`文件。你可以在附带的源代码示例中找到这个示例，以及其他我们之前讨论过的示例，在`appendix/introduction-to-grunt`部分。让我们通过解释如何编写你自己的 Grunt 任务来结束这个附录。

### B.8\. 创建自定义任务

Grunt 有两种任务：多任务和常规任务。正如你可能猜到的，多任务允许消费者设置不同的任务目标并单独运行它们。在实践中，几乎所有的 Grunt 任务都是多任务。让我们一步一步地创建一个多任务！

你将创建一个可以统计文件列表中单词数量的任务，并且如果统计的单词数量超过了预期，任务将失败。首先，让我们快速浏览一下这段代码：

```
grunt.registerMultiTask('wordcount', function () {
    var options = this.options({
      threshold: 0
    });
});
```

在这里，你正在为`threshold`选项设置一个默认值，这个值在任务配置时可以被覆盖，正如你将在下一分钟看到的。因为你使用了`registerMultiTask`，你可以支持多个任务目标。现在你需要遍历文件列表，读取它们，并计算其中的单词数量：

```
var total = 0;
this.files.forEach(function (file) {
  file.src.forEach(function (src) {
    if (grunt.file.isDir(src)) {
      return;
    }
    var data = grunt.file.read(src);
    var words = data.split(/[^\w]+/g).length;
    total += words;
    grunt.verbose.writeln(src, 'contains', words, 'words.');
  });
});
```

Grunt 将提供一个`files`对象，你可以使用它来遍历文件，过滤掉目录，并从文件中读取数据。一旦你计算出了单词数量，你可以打印结果，如果`threshold`被超过则失败：

```
if (options.threshold) {
  if (total > options.threshold) {
    grunt.log.error('Threshold of', options.threshold, 'exceeded. Found', total, 'words.');
    grunt.fail.warn('Too many words');
  } else {
    grunt.log.ok(total, 'words found in total.');
  }

} else {
  grunt.log.writeln(total, 'words found in total.');
}
```

最后，你只需要像之前那样配置一个任务目标：

```
wordcount: {
  capped: {
    files: {
      src: ['text/**/*.txt']
    },
    options: {
      threshold: 3000
    }
  }
}
```

如果所有这些文件的总字数超过 3,000 字，任务将失败。请注意，如果您没有提供阈值，它将使用默认值`0`，这是您在任务中指定的。这些信息足以理解我们在第一章中介绍的 Grunt。在第二章中，您将更深入地了解构建任务本身，它们应该如何工作，以及您如何组合任务来创建用于开发和发布及部署的构建工作流程。
