## 附录 D. JavaScript 代码质量指南

本风格指南旨在为应用程序的 JavaScript 代码提供基本规则，以便它易于阅读且在不同开发者之间保持一致。重点是确保应用程序不同部分的质量和连贯性。

### D.1\. 模块组织

本风格指南假设你正在使用模块系统，例如 CommonJS,^([1]) AMD,^([2]) ES6 Modules,^([3]) 或任何其他类型的模块系统。要全面了解模块系统，请参阅第五章（kindle_split_017.html#ch05）；我会等待。

> ¹ CommonJS 模块规范在[`bevacqua.io/bf/commonjs`](http://bevacqua.io/bf/commonjs)上托管一个维基页面。
> 
> ² RequireJS 在[`bevacqua.io/bf/amd`](http://bevacqua.io/bf/amd)上有一篇关于 AMD 目的的综合文章。
> 
> ³ 现在，开始使用 ES6 要容易得多！请参阅[`bevacqua.io/bf/es6-intro`](http://bevacqua.io/bf/es6-intro)。

模块系统提供单独的作用域，避免全局项目的泄漏，并通过自动化依赖图生成来改进代码库的组织，而不是需要手动创建数十个 `<script>` 标签。

模块系统还提供了依赖注入模式，这在单独测试组件时至关重要。

#### D.1.1\. 严格模式

总是在模块顶部放置 `"use strict"`;^([4])。严格模式允许你捕获无意义的操作，阻止不良实践，并且由于它允许编译器对你的代码做出某些假设，因此运行速度更快。

> ⁴ Mozilla 开发者网络（MDN）有一篇很好的文章解释了 JavaScript 中的严格模式，请参阅[`bevacqua.io/bf/strict`](http://bevacqua.io/bf/strict)。

#### D.1.2\. 缩进

应用程序中每个文件中的缩进必须保持一致。为此，强烈建议使用 Editor-Config。EditorConfig 通过在项目根目录中放置一个 `.editorconfig`^([5]) 文件来实现，然后你应该为你的首选文本编辑器安装 EditorConfig 插件。以下是我建议的用于开始 JavaScript 缩进的默认设置：

> ⁵ 在[`bevacqua.io/bf/editorconfig`](http://bevacqua.io/bf/editorconfig)上了解更多关于 EditorConfig 的信息。

```
# editorconfig.orgroot = true
[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
[*.md]
trim_trailing_whitespace = false
```

EditorConfig 可以透明地处理缩进，并且每个人都可以通过按 Tab 键来一致地产生正确数量的制表符或空格。选择制表符或空格取决于项目的特定情况，但我建议使用两个空格进行缩进。

缩进不仅涉及制表符，还包括函数声明中参数之前、之后和之间的空格。这种类型的缩进通常很难正确设置，而且对于大多数团队来说，甚至很难达成一个能满足所有人的方案。

```
function () {}

function( a, b ){}

function(a, b) {}

function (a,b) {}
```

尽量将这些差异降到最低，但也不必过分考虑。

在可能的情况下，通过保持行宽在 80 个字符以下来提高可读性。

#### D.1.3\. 分号

自动分号插入（ASI）不是一个特性。不要依赖它.^([6]) 它非常复杂^([7])，并且没有实际的理由让团队中的所有开发者都承担起了解 ASI 工作原理这种琐碎知识的负担。避免头疼；避免使用 ASI。始终在需要的地方添加分号。

> ⁶ Ben Alman 在[`bevacqua.io/bf/semicolons`](http://bevacqua.io/bf/semicolons)上提供了关于为什么你应该使用分号而不是省略它们的良好建议。
> 
> ⁷ 自动分号插入（ASI）内部工作原理的指南可以在[`bevacqua.io/bf/asi`](http://bevacqua.io/bf/asi)找到。

#### D.1.4\. 代码检查

由于 JavaScript 不需要编译步骤来处理未声明的变量，因此代码检查几乎是必需的。再次强调，不要使用像`jslint`^([8])那样对代码风格有强烈偏见的代码检查器。相反，使用更宽容的工具，如`jshint`^([9])或`eslint`^([10]）。以下是在使用 JSHint 时的一些提示：

> ⁸ JSLint，最初的 JavaScript 代码检查器，今天仍然可以在线使用[`bevacqua.io/bf/jslint`](http://bevacqua.io/bf/jslint)。
> 
> ⁹ JSHint 是一个现代替代品，在构建过程中被广泛观察。可以在[`bevacqua.io/bf/jshint`](http://bevacqua.io/bf/jshint)找到。
> 
> ¹⁰ ESLint 是另一个代码检查工具，旨在减少对样式检查的关注。可以在[`bevacqua.io/bf/eslint`](http://bevacqua.io/bf/eslint)找到它。

+   声明一个`.jshintignore`文件，并包括`node_modules`、`bower_components`等。

+   你可以使用以下类似文件来保持你的规则在一起：

```
{
  "curly": true,
  "eqeqeq": true,
  "newcap": true,
  "noarg": true,
  "noempty": true,
  "nonew": true,
  "sub": true,
  "undef": true,
  "unused": true,
  "trailing": true,
  "boss": true,
  "eqnull": true,
  "strict": true,
  "immed": true,
  "expr": true,
  "latedef": "nofunc",
  "quotmark": "single",
  "indent": 2,
  "node": true
}
```

这些规则绝对不是你应该坚持的规则，但找到不进行代码检查和过于关注编码风格之间的平衡是很重要的。如果放任不管，你可能会陷入常见的错误，比如遗漏分号或错误地关闭字符串引号，但如果做得太过分，你可能会发现团队花更多的时间处理代码风格而不是编写有意义的代码。

### D.2\. 字符串

字符串应该始终使用相同的引号进行引用。在整个代码库中一致地使用`'`或`"`。确保团队在编写 JavaScript 代码的每个部分都使用相同的引号。

#### 差劲的字符串

```
var message = 'oh hai ' + name + "!";
```

#### 优秀的字符串

```
var message = 'oh hai ' + name + '!';
```

通常，如果你在 Node 中创建一个参数替换方法，比如`util.format`，你会成为一个更快乐的 JavaScript 开发者.^([11]) 这样做会使字符串格式化变得容易得多，代码看起来也更整洁。

> ¹¹ Node 的 util.format 文档可以在[`bevacqua.io/bf/util.format`](http://bevacqua.io/bf/util.format)找到。

#### 更好的字符串

```
var message = util.format('oh hai %s!', name);
```

你可以使用以下代码片段实现类似的功能：

```
function format () {
  var args = [].slice.call(arguments);
  var initial = args.shift();
  function replacer (text, replacement) {
    return text.replace('%s', replacement);
  }
  return args.reduce(replacer, initial);
}
```

为了声明多行字符串，尤其是当谈论 HTML 片段时，有时最好使用数组作为缓冲区，然后连接其部分。字符串连接样式可能更快，但跟踪起来也更困难：

```
var html = [
  '<div>',
    format('<span class="monster">%s</span>', name),
  '</div>'
].join('');
```

使用数组构建器样式，您也可以推送片段的部分，然后在最后将所有内容连接在一起。这是 Jade^(12))等字符串模板引擎喜欢做的事情。

> ^(12) 通过访问他们的 GitHub 仓库[`bevacqua.io/bf/jade`](http://bevacqua.io/bf/jade)了解更多关于 Jade 模板的信息。

#### D.2.1. 变量声明

总是以一致的方式声明变量，并在它们的范围内顶部。鼓励将变量声明保持为一行一个。逗号优先，单个`var`语句，多个`var`语句，这些都行，但要在整个项目中保持一致。确保团队中的每个人都遵循样式指南，以确保一致性。

##### 不一致的声明

```
var foo = 1,
    bar = 2;
var baz;
var pony;
var a
  , b;
```

或者

```
var foo = 1;
if (foo > 1) {
  var bar = 2;
}
```

注意，以下示例不仅因为其风格，还因为其陈述之间的一致性而可行。

##### 一致的声明

```
var foo = 1;
var bar = 2;
var baz;
var pony;
var a;
var b;
var foo = 1;
var bar;
if (foo > 1) {
  bar = 2;
}
```

没有立即赋值的变量声明可以共享同一行代码。

##### 可接受的声明

```
var a = 'a';
var b = 2;
var i, j;
```

### D.3. 条件语句

强制使用括号。这，加上合理的间距策略，将帮助您避免像苹果的 SSL/TLS 错误^(13))这样的错误。

> ^(13) 关于苹果“GOTO Fail”错误的详细报告可以在[`bevacqua.io/bf/gotofail`](http://bevacqua.io/bf/gotofail)找到。

#### 不良的条件语句

```
if (err) throw err;
```

#### 良好的条件语句

```
if (err) {
  throw err;
}
```

避免使用`==`和`!=`运算符；始终优先使用`===`和`!==`。这些运算符被称为“严格相等运算符”，而它们的对应物将尝试将操作数^(14))转换为相同的值类型。如果可能，尽量将单行条件语句也保持在多行格式中。

> ^(14) 相等运算符在 MDN 上有一个专门的页面[`bevacqua.io/bf/equality`](http://bevacqua.io/bf/equality)。

#### 不良的强制转换相等

```
function isEmptyString (text) {
  return text == '';

}
isEmptyString(0);
// <- true
```

#### 良好的严格相等

```
function isEmptyString (text) {
  return text === '';
}
isEmptyString(0);
// <- false
```

#### D.3.1. 三元运算符

三元运算符适用于清晰的条件语句，但不适用于令人困惑的选择。一般来说，如果你不能像你的大脑一样快速地通过眼睛解析它，那么它可能过于复杂，不利于其自身。

jQuery 是代码库中充满糟糕三元运算符的一个典型例子^(15)。

> ^(15) 可以在[`bevacqua.io/bf/jquery-ternary`](http://bevacqua.io/bf/jquery-ternary)找到一些 jQuery 中三元运算符误用的例子。

##### 不良的三元运算符

```
function calculate (a, b) {
  return a && b ? 11 : a ? 10 : b ? 1 : 0;
}
```

##### 良好的三元运算符

```
function getName (mobile) {
  return mobile ? mobile.name : 'Generic Player';
}
```

在可能引起混淆的情况下，使用`if`和`else`语句。

#### D.3.2. 函数

在声明函数时，始终使用函数声明形式^([16)) 而不是函数表达式.^([17)) 如果你在将函数表达式赋值给变量之前尝试使用它们，你会得到一个错误。相比之下，函数声明会被提升^([18)) 到作用域的顶部，这意味着无论你在代码中放置它们的位置如何，它们都会正常工作。你可以在第五章 [kindle_split_017.html#ch05] 中了解关于提升的所有细节。

> (16) StackOverflow 有一个涵盖函数声明的答案，链接为 [`bevacqua.io/bf/fn-declaration`](http://bevacqua.io/bf/fn-declaration)。
> 
> (17) 你可以在 MDN 上找到函数表达式的简洁定义，链接为 [`bevacqua.io/bf/fn-expr`](http://bevacqua.io/bf/fn-expr)。
> 
> (18) 变量提升的解释可以在 [`bevacqua.io/bf/hoisting`](http://bevacqua.io/bf/hoisting) 找到的代码示例中找到。

##### 使用表达式是坏的

```
var sum = function (x, y) {
  return x + y;
};
```

##### 使用声明是好的

```
function sum (x, y) {
  return x + y;
}
```

话虽如此，将函数表达式应用于另一个函数并没有什么错误.^([19))

> (19) 约翰·雷西格在他的博客上解释了如何在 [`bevacqua.io/bf/partial-application`](http://bevacqua.io/bf/partial-application) 上部分应用函数。

##### Currying 是好的

```
var plusThree = sum.bind(null, 3);
```

请记住，函数声明将被提升^([20)) 到作用域的顶部，所以它们的声明顺序并不重要。然而，你应该始终将它们保持在作用域的顶层，并且始终避免在条件语句中放置它们。

> (20) 变量提升的解释可以在 [`bevacqua.io/bf/hoisting`](http://bevacqua.io/bf/hoisting) 找到的代码示例中找到。

##### 差的函数

```
if (Math.random() > 0.5) {
  sum(1, 3);
  function sum (x, y) {
    return x + y;
  }
}
```

##### 好的函数

```
if (Math.random() > 0.5) {
  sum(1, 3);
}
function sum (x, y) {
  return x + y;
}
Or
function sum (x, y) {
  return x + y;
}
if (Math.random() > 0.5) {
  sum(1, 3);
}
```

如果你需要一个“无操作”方法，你可以使用 `Function.prototype` 或 `function noop () {}`。理想情况下，在整个应用程序中只使用一个 `noop` 引用。每次你必须操作 `arguments` 对象或其他类似数组的对象时，都要将它们转换为数组。

##### 差的数组循环

```
var divs = document.querySelectorAll('div');

for (i = 0; i < divs.length; i++) {
  console.log(divs[i].innerHTML);
}
```

##### 好的数组循环

```
var divs = document.querySelectorAll('div');
[].slice.call(divs).forEach(function (div) {
  console.log(div.innerHTML);
});
```

然而，请注意，在 V8 环境中使用这种方法在 `arguments.` 上会有显著的性能影响^([21))。如果性能是一个主要问题，请避免使用 `slice` 将 `arguments` 转换为数组，而改用 `for` 循环。

> (21) 请参阅一篇关于优化函数参数操作的精彩文章，链接为 [`bevacqua.io/bf/arguments`](http://bevacqua.io/bf/arguments)。

##### 差的参数访问器

```
var args = [].slice.call(arguments);
```

##### 更好的参数访问器

```
var i;
var args = new Array(arguments.length);
for (i = 0; i < args.length; i++) {
    args[i] = arguments[i];
}
```

永远不要在循环中声明函数。

##### 差的内联函数

```
var values = [1, 2, 3];
var i;
for (i = 0; i < values.length; i++) {
  setTimeout(function () {
    console.log(values[i]);
  }, 1000 * i);
}
```

或者

```
var values = [1, 2, 3];
var i;
for (i = 0; i < values.length; i++) {
  setTimeout(function (i) {
    return function () {
      console.log(values[i]);
    };
  }(i), 1000 * i);
}
```

##### 更好地提取函数

```
var values = [1, 2, 3];
var i;
for (i = 0; i < values.length; i++) {
  wait(i);
}
function wait (i) {
  setTimeout(function () {
    console.log(values[i]);
  }, 1000 * i);
}
```

或者更好，使用 `.forEach`，它没有在 `for` 循环中声明函数的相同限制。

##### 更好，使用 foreach 的函数数组

```
[1, 2, 3].forEach(function (value, i) {
  setTimeout(function () {
    console.log(value);
  }, 1000 * i);
});
```

##### 命名函数与匿名函数

每当一个方法不是微不足道的，都要努力使用命名的函数表达式而不是匿名函数。这使你在分析堆栈跟踪时更容易找到异常的根本原因。

##### 差的，匿名函数

```
function once (fn) {
  var ran = false;
  return function () {
    if (ran) { return };
    ran = true;
    fn.apply(this, arguments);
  };
}
```

##### 好的，有命名的函数

```
function once (fn) {
  var ran = false;
  return function run () {
    if (ran) { return };
    ran = true;
    fn.apply(this, arguments);
  };
}
```

通过使用保护子句而不是流动的 `if` 语句来避免不必要的缩进级别增加。

##### 差

```
if (car) {
  if (black) {
    if (turbine) {
      return 'batman!';
    }
  }
}
```

或者

```
if (condition) {
  // 10+ lines of code
}
```

##### 好

```
if (!car) {
  return;
}
if (!black) {
  return;

}
if (!turbine) {
  return;
}
return 'batman!';
```

或者

```
if (!condition) {
  return;
}
// 10+ lines of code
```

#### D.3.3\. 原型

应尽量避免修改本地类型的原型；使用方法代替。如果你必须扩展本地类型的功能，尝试使用 `poser`^([22]) 代替。Poser 提供了上下文无关的本地类型引用，你可以安全地构建和扩展。

> ^(22) Poser 提供了上下文无关的本地类型引用，你可以安全地构建和扩展。更多信息，请参阅 [`bevacqua.io/bf/poser`](http://bevacqua.io/bf/poser)。

##### 差

```
String.prototype.half = function () {
  return this.substr(0, this.length / 2);
};
```

##### 好

```
function half (text) {
  return text.substr(0, text.length / 2);
}
```

除非你有充分的性能理由来证明自己，否则请避免使用典型继承模型：

+   它们比使用普通对象更冗长。

+   在创建 `new` 对象时会引起头痛。

+   它们需要一个闭包来隐藏实例的宝贵私有状态。

+   只使用普通对象即可。

#### D.3.4\. 对象字面量

使用埃及符号 `{}` 来实例化。用工厂方法代替构造函数。以下是一个供你实现对象的通用模式：

```
function util (options) {
  // private methods and state go here
  var foo;
  function add () {
    return foo++;
  }
  function reset () { // note that this method isn't publicly exposed
    foo = options.start || 0;

  }
  reset();
  return {
    // public interface methods go here
    uuid: add
  };
}
```

#### D.3.5\. 数组字面量

使用方括号符号 `[]` 来实例化。如果你因为性能原因必须声明一个固定维度的数组，那么使用 `new Array(length)` 符号也是可以的。

JavaScript 中的数组拥有丰富的 API，你应该充分利用。你可以从数组操作的基础知识^([23]) 开始，然后过渡到更高级的使用场景。例如，你可以使用 `.forEach` 方法遍历集合中的所有项目。

> ^(23) 一篇关于 JavaScript 数组的介绍性文章在我的博客上可读：[`bevacqua.io/bf/arrays`](http://bevacqua.io/bf/arrays)。

以下列表显示了你可以对数组执行的基本操作：

+   使用 `.push` 来在集合末尾插入项目，或使用 `.shift` 来在开头插入项目。

+   使用 `.pop` 来获取最后一个项目并从集合中移除，或者使用 `.unshift` 来对第一个项目执行相同的操作。

+   精通 `.splice` 以通过索引删除项目，或在特定索引处插入项目，或同时执行这两项操作！

还要了解并使用函数式集合操作方法！这些方法可以节省你大量时间，否则你将不得不手动执行这些操作。以下是一些你可以做的例子：

+   使用 `.filter` 来丢弃不感兴趣的价值。

+   使用 `.map` 来将数组值转换成其他东西。

+   使用 `.reduce` 来遍历数组并生成一个单一的结果。

+   使用 `.some` 和 `.every` 来断言所有数组项目是否满足某个条件。

+   使用 `.sort` 来排列集合中的元素。

+   使用 `.reverse` 来反转数组中的顺序。

Mozilla 开发者网络 (MDN) 在 [`developer.mozilla.org/`](https://developer.mozilla.org/) 对所有这些方法以及更多方法进行了详尽的文档记录。

### D.4\. 正则表达式

将正则表达式保存在变量中；不要内联使用。这将大大提高可读性。

#### 不良的正则表达式

```
if (/\d+/.test(text)) {
  console.log('so many numbers!');
}
```

#### 良好的正则表达式

```
var numeric = /\d+/;
if (numeric.test(text)) {
  console.log('so many numbers!');
}
```

此外，学习编写正则表达式^([24])以及它们的作用。然后你还可以在线可视化它们^([25])。

> ^（24）在我的博客上有一篇关于正则表达式的入门文章，请参阅[`bevacqua.io/bf/regex`](http://bevacqua.io/bf/regex)。
> 
> ^（25）Regexper 允许你可视化任何正则表达式的工作方式，请参阅[`bevacqua.io/bf/regexper`](http://bevacqua.io/bf/regexper)。

#### D.4.1\. 调试语句

最好将你的`console`语句放入一个可以在生产中轻松禁用的服务中。或者，不要将任何`console.log`打印语句发送到生产分发。

#### D.4.2\. 注释

注释不是用来解释代码做了什么的。好的代码应该是自我解释的。如果你正在考虑写一个注释来解释一段代码做了什么，那么你可能需要改变代码本身。这个规则的例外是解释正则表达式做了什么。好的注释应该解释代码为什么做了可能没有明确目的的事情。

##### 不良的注释

```
// create the centered container
var p = $('<p/>');
p.center(div);
p.text('foo');
```

##### 良好的注释

```
var container = $('<p/>');
var contents = 'foo';
container.center(parent);
container.text(contents);
megaphone.on('data', function (value) {
  container.text(value); // the megaphone periodically emits updates for container
});
```

或者

```
var numeric = /\d+/; // one or more digits somewhere in the string
if (numeric.test(text)) {
  console.log('so many numbers!');
}
```

完全注释掉代码块应该完全避免；这就是为什么你设置了版本控制系统的原因！

#### D.4.3\. 变量命名

变量必须有有意义的名称，这样你就不必求助于注释来解释某个功能片段做了什么。相反，尽量做到表达清晰、简洁，并使用有意义的变量名：

##### 不良的命名

```
function a (x, y, z) {
  return z * y / x;
}
a(4, 2, 6);
// <- 3
```

##### 良好的命名

```
function ruleOfThree (had, got, have) {
  return have * got / had;
}
ruleOfThree(4, 2, 6);
// <- 3
```

#### D.4.4\. Polyfills

Polyfill 是一段代码，它透明地使你的应用程序能够在旧浏览器中使用现代功能。尽可能使用原生浏览器实现，并为不支持该功能的浏览器包含一个提供相同行为的 polyfill^([26))。这使得代码更容易处理，并且减少了为了使事情正常工作而进行的黑客行为。

> ^（26）Remy Sharp 简洁地解释了什么是 polyfill，请参阅[`bevacqua.io/bf/polyfill`](http://bevacqua.io/bf/polyfill)。

如果你不能用 polyfill 修复某个功能片段，那么请将所有修补代码的使用包裹在一个全局可访问的实现中，该实现可以从应用程序的任何地方访问^([27))。

> ^（27）我写了一篇关于开发高质量模块的文章，其中涉及实现包装的主题，请参阅[`bevacqua.io/bf/hq-modules`](http://bevacqua.io/bf/hq-modules)。

#### D.4.5\. 每日技巧

##### 创建默认值

使用`||`来定义默认值。如果左边的值是假值^([28))，则将使用右边的值。

> ^（28）在 JavaScript 中，在条件语句中，假值被视为 false。假值包括‘’，`null`，`undefined`和`0`。更多信息，请参阅[`bevacqua.io/bf/casting`](http://bevacqua.io/bf/casting)。

```
function a (value) {
  var defaultValue = 33;
  var used = value || defaultValue;
}
```

##### 使用 bind 部分应用函数

使用`.bind`来部分应用^(29)函数：

> ^(29) 以 jQuery 闻名的 John Resig 在[`bevacqua.io/bf/partial-application`](http://bevacqua.io/bf/partial-application)上发表了一篇关于部分 JavaScript 函数的有趣文章。

```
function sum (a, b) {
  return a + b;
}
var addSeven = sum.bind(null, 7);
addSeven(6);
// <- 13
```

##### 使用`Array.prototype.slice.call`将类数组对象转换为数组

使用`Array.prototype.slice.call`将类数组对象转换为真正的数组：

```
var args = Array.prototype.slice.call(arguments);
```

##### 所有事物的事件发射器

在所有事物上使用事件发射器^(30)！这种模式有助于您将实现与不同对象或应用层之间的消息传递解耦：

> ^(30) Contra 提供了一个易于使用的事件发射器实现[`bevacqua.io/bf/contra.emitter`](http://bevacqua.io/bf/contra.emitter)。

```
var emitter = contra.emitter();
body.addEventListener('click', function () {
  emitter.emit('click', e.target);
});
emitter.on('click', function (elem) {
  console.log(elem);
});
// simulate click
emitter.emit('click', document.body);
```

##### `Function.prototype`作为无操作

将`Function.prototype`用作“无操作”： 

```
function (cb) {
  setTimeout(cb || Function.prototype, 2000);
```
