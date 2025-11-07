## 附录 A. ES6 中引入的 JavaScript 语法

在本附录中，我介绍了 ES6 中引入的 JavaScript 语法，这些语法适用于 Node.js。我从变量定义和新的`String`插值风格开始。然后我讨论了箭头函数。

## A.1\. ES6 中的新特性

自 2015 年以来，ECMAScript 6 为使用 JavaScript 进行开发提供了新的语法和约定。因此，本书涵盖了您将使用的一些 ES6 关键字和格式。*关键字*是具有 JavaScript 中保留意义的术语，用于提供代码的语法和可解释性。

#### A.1.1\. `let`关键字

您可能已经习惯了使用`var`关键字声明变量。在 ES6 中，使用`let`关键字定义变量更为合适，因为它们适用于特定的作用域块。直到在特定代码块中定义了变量，您才能访问它。

在`if`块中定义的`let`变量不能在块外访问，例如，而`var`变量的作用域限于定义它的函数内，如下面的列表所示。

##### 列表 A.1\. `let`关键字的示例用法

```
function sample() {
  var num = 60;
  if (num > 50){
    let num = 0;
  }
  console.log(num);
}
```

由于`let`变量的作用域限于函数之外的代码块，它们可以是模块或整个应用程序的全局变量。因此，`let`提供了更多的变量定义安全性，并且比`var`更受欢迎。

| |
| --- |

##### 注意

当使用`"use strict";`时，您不能重新定义相同的`let`变量，而可以使用`var`。

| |
| --- |

#### A.1.2\. 常量变量

`const`变量不能重新赋值。通常，您应该使用此关键字代替`let`，对于您不期望在代码中操作的变量。此指南也适用于在 Node.js 中加载库或模块，如单元 1 中所示。如果您尝试重新赋值`const`变量，则会得到“重复声明错误”。

下一个列表中的代码会崩溃，因为正在使用现有常量的名称声明一个新的`let`变量。

##### 列表 A.2\. `const`变量的示例用法

```
function applyDiscount(discountPrice) {
  const basePrice = 1000;
  let basePrice = basePrice - discountPrice;
  console.log(basePrice);
}
```

#### A.1.3\. 字符串插值

在 ES6 之前，要在字符串中打印或记录变量的值，您必须将字符串附加到变量周围，如下面的列表所示。

##### 列表 A.3\. 字符串连接示例

```
var x = 45;
console.log("It is " + x + " degrees outside!");
```

在 ES6 中，您可以使用反引号（`` ` ``）和`${}`将变量插值到字符串中，如下面的列表所示。

##### 列表 A.4\. 使用反引号插值字符串

```
var x = 45;
console.log(`It is ${x} degrees outside!`);
```

产生的代码更简洁、更易于阅读和编辑。

#### A.1.4\. 箭头函数

箭头函数是 ES6 使代码更简洁、更易于阅读的一种方式。使用 => 箭头符号和函数语法的改变，您可以将多行函数转换为单行。以下列表中的示例展示了这一点。

##### 列表 A.5\. 使用`function`关键字定义函数

```
function printName(name) {
  console.log(`My name is ${name}`);
}
```

您可以将此代码重写为以下列表所示。

##### 列表 A.6\. 定义箭头函数

```
let printName = name => console.log(`My name is ${name}`);
```

更重要的是，ES6 中的箭头函数保留了其外部作用域的 `this` 变量，如下一个列表所示。

##### 列表 A.7\. 函数内 `this` 关键字的示例用法

```
let dog = {
  name: "Sparky",
  printNameAfterTime: function() {
    setTimeout(function() {
      console.log(`My name is ${this.name}`);
    }, 1000);
  }
}
```

在这个例子中，`dog.printNameAfterTime()` 打印 `My name is undefined`，因为 `this .name` 超出了 `setTimeout` 函数的作用域，尽管假设 `this` 指的是 `dog` 对象。然而，使用箭头函数时，`this` 在 `setTimeout` 函数中保持不变，如下一个列表所示。

##### 列表 A.8\. 使用箭头函数的 `this` 关键字的示例

```
let dog = {
  name: "Sparky",
  printNameAfterTime() {
    setTimeout(() => {
      console.log(`My name is ${this.name}`);
    }, 1000);
  }
}
```

现在，你可以打印 `My name is Sparky`，代码更加紧凑！

要在 Node.js 中取得成功，你需要在 JavaScript 方面取得成功。因为 Node.js 需要足够的对一些核心 JavaScript 和编程概念的了解，所以本课回顾了你需要知道的内容才能开始。如果你在 JavaScript 方面没有太多经验，我建议阅读 John Resig 和 Bear Bibeault 所著的 *《JavaScript 忍者秘籍，第二版》*（Manning, 2016）。

## A.2\. REPL

当你安装了 Node.js 后，运行你的代码的第一个地方是 Read-Evaluate-Print Loop (REPL)。这个交互式环境类似于 Chrome 网络浏览器中的控制台窗口。在 REPL 中，你可以运行任何 JavaScript 代码。你还可以引入 Node.js 模块来测试应用程序的各个方面。

#### A.2.1\. 在 REPL 中运行 JavaScript

要启动 REPL，请导航到你的电脑上的任何终端窗口，并输入 `node`。此命令立即返回一个提示符（`>`），之后你可以输入任何 JavaScript 语句。你可以把 REPL 看作是一个运行中的 Node.js 应用程序，它会即时响应你的命令。也就是说，你不需要在单独的文件中编写你的 JavaScript 代码然后运行它；你可以在 REPL 窗口中直接输入那段 JavaScript 代码。尝试定义几个变量，如下一个列表所示。你会注意到，每运行一个 JavaScript 语句，REPL 就会输出该语句的返回值。对于变量赋值，返回值是 `undefined`。

##### 列表 A.9\. 在 REPL 中定义变量

```
> let x = 42;
undefined
> let sentence = "The meaning of life is ";
undefined
```

现在对这些变量执行一些操作。例如，你可以将两个值连接起来，如下面的列表所示。

##### 列表 A.10\. 在 REPL 中连接变量

```
> sentence + x;
The meaning of life is 42
```

你可以使用 REPL 环境的任何方式来模拟你使用或见过的任何 Node.js 应用程序。你还可以使用制表键来自动完成变量或函数名，并列出对象属性。例如，如果你通过变量名 `sentence` 定义了一个字符串，但你不确定可以调用该字符串的哪些函数，你可以在变量名末尾添加一个点（`.`）并按 Tab 键来列出该变量的可用函数和属性，如下一个列表所示。

##### 列表 A.11\. 在 REPL 中列出变量属性

```
> sentence.
sentence.anchor                sentence.big
sentence.blink                 sentence.bold
sentence.charAt                sentence.charCodeAt
sentence.codePointAt           sentence.concat
sentence.endsWith              sentence.fixed
sentence.fontcolor             sentence.fontsize
sentence.includes              sentence.indexOf
sentence.italics               sentence.lastIndexOf
```

你可以在第 1 课中找到额外的 REPL 命令。

#### A.2.2\. 在应用程序开发中使用 REPL

使用 REPL 的另一种有用方式是通过 Node.js 应用程序代码中的`repl`模块。随着您在项目中构建更多的自定义模块，您会注意到将所有这些文件加载到 REPL 中以测试您所编写代码的功能是繁琐的。如果您编写了一个名为 multiply.js（列表 A.12）的模块，该模块包含一个用于乘以两个数字的函数，您需要通过输入`require("./multiply")`将此模块导入 REPL，以及您创建的每个其他模块。更重要的是，您需要为每个新的 REPL 会话输入这些行。

##### 列表 A.12\. 在 multiply.js 中创建单函数模块

```
module.exports = {
  multiply: (x, y) => {
    return x * y;
  }
};
```

您不必在每个 REPL 会话中导入模块，您可以将 REPL 带入您的模块中。列表 A.13 展示了您如何在项目中使用`repl`模块。您可以在项目目录中创建一个名为 customRepl.js 的模块，同时导入您想要测试的所有模块。此文件显示了`repl`模块被导入，然后启动了一个 REPL 服务器。就像 Node.js HTTP 服务器一样，这个 REPL 服务器有一个上下文，您可以在其中加载自定义变量。REPL 服务器启动后，添加一个`name`变量和您的`multiply`模块。

##### 列表 A.13\. 在 customRepl.js 中使用`repl`模块

```
const repl = require("repl"),
  replServer = repl.start({
    prompt: "> ",
});

replServer.context.name = "Jon Wexler";
replServer.context.multiply = require("./multiply").multiply;
```

现在您只需在终端导航到您的项目目录，并输入`node customRepl`。您将看到 REPL 提示，但这次，您的 REPL 会话的上下文包含您想要测试的所有模块。当您想要测试在数据库中创建或修改记录，而不必复制和粘贴代码来导入数据库配置时，这种技术非常有用。

## 摘要

本附录概述了您在本书中应了解的 JavaScript 关键字和语法。随着 ES6 现在在开发社区中得到广泛应用，开始编写反映最新和最伟大 JavaScript 变化的代码变得非常重要。您对使用 REPL 和 JavaScript 命令越熟悉，开发应用程序就会越容易。
