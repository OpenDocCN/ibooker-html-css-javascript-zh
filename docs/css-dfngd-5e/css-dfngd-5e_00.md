# 前言

如果您是一位对精细页面样式、提升可访问性并节省时间和精力感兴趣的网页设计师或文档作者，这本书适合您。开始阅读这本书前，您真正需要了解的只有 HTML 4.0\. 您对 HTML 了解得越多，您准备得越好，但这不是必需条件。要跟随本书，您只需了解很少其他内容。

该书的第五版完成于 2022 年底，并尽力反映当时的 CSS 状态。任何在写作时具有广泛浏览器支持或已知即将在出版后不久到来的内容都有详细介绍。仍在开发中或已知支持即将停止的 CSS 特性在此未涵盖。

# 本书中使用的约定

本书使用以下排印约定（但务必阅读“值语法约定”以查看其中一些的修改方式）：

*斜体*

指示新术语、URL、电子邮件地址、文件名和文件扩展名。

`固定宽度`

用于程序清单，以及段落内指向程序元素如变量或函数名、数据库、数据类型、环境变量、语句和关键字。

*`常量宽度斜体`*

显示应由用户提供的值或由上下文确定的值的文本。

###### 提示

此元素表示提示或建议。

###### 注

此元素表示一般注释。

###### 警告

此元素表示警告或注意事项。

## 值语法约定

本书中的各个 CSS 属性的详细信息都在方框中解释，包括允许使用的值。这些内容基本上是从 CSS 规范中实质性复制的，但需要解释语法。

每个属性的允许值将以以下语法列出：

**值:** <*`family-name`*>#

**值:** <*`url`*> ‖ <*`color`*>

**值:** <*`url`*>? <*`color`*> [ / <*`color`*> ]?

**值:** [ <*`length`*> | `thick` | `thin` ]{1,4}

**值:** [ <*`background`*>, ]* <*`background-color`*>

任何在 `<` 和 `>` 之间的斜体字给出一个值类型或者对另一个属性值的引用。例如，属性 `font` 接受属于属性 `font-family` 的值。这用文本 <*`font-family`*> 表示。类似地，如果允许值类型如颜色，将用 <*`color`*> 表示。

所有以 `固定宽度` 呈现的单词都是必须按照字面意思出现的关键词。斜杠 (`/`) 和逗号 (,) 也必须字面使用。

值定义的组件可以通过多种方式组合：

+   两个或更多关键词连在一起，中间只有空格分隔，意味着所有关键词必须按给定顺序出现。例如，`help me` 表示该属性必须按照这些关键词顺序出现。

+   如果垂直线分隔备选项（*`X`* | *`Y`*），则任何一个选项必须出现，但仅限一个。例如，[ *`X`* | *`Y`* | *`Z`* ] 允许 *`X`*、*`Y`* 或 *`Z`* 中的任何一个。

+   一个垂直双竖线（*`X`* ‖ *`Y`*）意味着*`X`*、*`Y`*或两者都必须出现，但它们可以以任意顺序出现。因此：*`X`*、*`Y`*、*`X`* *`Y`* 和 *`Y`* *`X`* 都是有效的解释。

+   一个双与号（*`X`* && *`Y`*）意味着*`X`*和*`Y`*都必须出现，尽管它们可以以任意顺序出现。因此：*`X`* *`Y`* 或 *`Y`* *`X`* 都是有效的解释。

+   方括号（[…​]）用于将事物分组在一起。因此，[`please ‖ help ‖ me`] `do this` 表示 `please`、`help` 和 `me` 这三个词可以以任意顺序出现，但每个只能出现一次。而 `do this` 必须始终按照指定的顺序出现。以下是一些例子：`please help me do this`、`help me please do this`、`me please help do this`。

每个组件或括号组后面可能（或者可能不）跟随这些修饰符之一：

+   星号（*）表示前面的值或括号组可以重复零次或多次。因此，`bucket`* 表示单词 `bucket` 可以任意次数使用，包括零次。没有定义可以使用的最大次数。

+   加号（+）表示前面的值或括号组至少重复一次。因此，`mop`+ 表示单词 `mop` 必须至少使用一次，可能更多次。

+   井号（#），正式称为八角符号，表示前面的值或括号组以逗号分隔，可以重复一次或多次。因此，`floor`# 可以是 `floor` 或 `floor, floor, floor`，等等。这通常与括号组或值类型一起使用。

+   问号（?）表示前面的值或括号组是可选的。例如，[`pine tree`]? 表示 `pine tree` 这个词组不一定需要使用（但如果使用了，必须按照顺序出现）。

+   感叹号（!）表示前面的值或括号组是必需的，因此必须至少得到一个值，即使语法似乎表明不需要。例如，[ `what`? `is`? `happening`? ]! 必须至少出现其中一个被标记为可选的三个术语。

+   大括号中的一对数字（{*M*,*N*}）表示前面的值或括号组至少重复 *M* 次，最多重复 *N* 次。例如，`ha`{1,3} 表示单词 `ha` 可以出现一次、两次或三次。

以下是一些例子：

`give` ‖ `me` ‖ `liberty`

三个词中至少要使用一个，并且它们可以以任意顺序使用。例如，`give liberty`、`give me`、`liberty me give` 和 `give me liberty` 都是有效的解释。

[ `I` | `am` ]? `the` ‖ `walrus`

可以使用单词`I`或`am`，但不能同时使用，使用任何一个是可选的。此外，`the`或`walrus`或两者必须以任意顺序跟随。因此，您可以构建`I the walrus`，`am walrus the`，`am the`，`I walrus`，`walrus the`等等。

`koo`+ `ka-choo`

一个或多个`koo`必须跟随`ka-choo`。因此`koo koo ka-choo`，`koo koo koo ka-choo`和`koo ka-choo`都是合法的。`koo`的数量可能是无限的，尽管可能存在特定于实现的限制。

`I really`{1,4}? [ `love` | `hate` ][ `Microsoft` | `Firefox` | `Opera` | `Safari` | `Chrome` ]

通用 Web 设计师的意见表达器。这可以解释为`I love Firefox`，`I really love Microsoft`等表达。可以使用从零到四个`really`，但它们不能用逗号分隔。您还可以在`love`和`hate`之间进行选择，这似乎确实像某种隐喻。

`It’s a` [ `mad` ]# `world`

这提供了将尽可能多个逗号分隔的`mad`放入其中的机会，至少需要一个`mad`。如果只有一个`mad`，则不添加逗号。因此：`It’s a mad world`和`It’s a mad, mad, mad, mad, mad world`都是有效的结果。

[ [`Alpha` ‖ `Baker` ‖ `Cray`], ]{2,3}`和 Delphi`

`Alpha`、`Baker`和`Delta`的两到三个必须跟随`and Delphi`。一个可能的结果是`Cray, Alpha 和 Delphi`。在这种情况下，逗号是因为其在嵌套括号组内的位置而放置的。（一些旧版本的 CSS 通过这种方式强制使用逗号分隔，而不是通过`#`修饰符。）

# 使用代码示例

每当您遇到看起来像![](img/play-icon-round.png)的图标时，这意味着有相关的代码示例。实时示例可在[*https://meyerweb.github.io/csstdg5figs*](https://meyerweb.github.io/csstdg5figs)找到。如果您在连接互联网的设备上阅读本书，可以单击![](img/play-icon-round.png)图标直接访问引用的代码示例的实时版本。

附加材料——用于生成本书几乎所有图示的 HTML、CSS 和图像文件可通过[*https://github.com/meyerweb/csstdg5figs*](https://github.com/meyerweb/csstdg5figs)下载。请务必阅读存储库的*README.md*文件，了解有关存储库内容的任何注意事项。

如果您有技术问题或使用代码示例时出现问题，请发送电子邮件至*bookquestions@oreilly.com*。

本书旨在帮助您完成工作任务。通常情况下，如果本书提供了示例代码，您可以在您的程序和文档中使用它。除非您复制了代码的大部分内容，否则无需联系我们获得许可。例如，编写一个使用本书多个代码块的程序不需要许可。销售或分发 O'Reilly 书籍中的示例代码需要获得许可。引用本书并引用示例代码回答问题不需要许可。将本书大量示例代码整合到产品文档中需要获得许可。

我们感谢，但通常不要求署名。署名通常包括标题，作者，出版商和 ISBN。例如：“*CSS: The Definitive Guide* by Eric A. Meyer and Estelle Weyl (O’Reilly). Copyright 2023 Eric A. Meyer and Estelle Weyl, 978-1-098-11761-0.”

如果您认为您对代码示例的使用超出了合理使用或上述许可的范围，请随时联系我们*permissions@oreilly.com*。

# O’Reilly 在线学习

###### 注

40 多年来，[*O’Reilly Media*](https://oreilly.com)已经为公司提供技术和商业培训，知识和见解，帮助其取得成功。

我们独特的专家和创新者网络通过书籍，文章和我们的在线学习平台分享他们的知识和专业知识。O'Reilly 的在线学习平台为您提供按需访问的实时培训课程，深入学习路径，交互式编码环境以及来自 O'Reilly 和其他 200 多个出版商的广泛文本和视频收藏。有关更多信息，请访问[*https://oreilly.com*](https://oreilly.com)。

# 如何联系我们

请将有关本书的评论和问题发送至出版商：

+   O’Reilly Media, Inc.

+   1005 Gravenstein Highway North

+   加利福尼亚州塞巴斯托波尔 95472

+   800-998-9938（美国或加拿大）

+   707-829-0515（国际或本地）

+   707-829-0104（传真）

我们有一个专门的网页为本书列出勘误表，示例和任何额外信息。您可以访问此页面[*https://oreil.ly/css-the-definitive-guide-5e*](https://oreil.ly/css-the-definitive-guide-5e)。

发送电子邮件至*bookquestions@oreilly.com*以评论或询问有关本书的技术问题。

有关我们的书籍和课程的新闻和信息，请访问[*https://oreilly.com*](https://oreilly.com)。

在 LinkedIn 上找到我们：[*https://linkedin.com/company/oreilly-media*](https://linkedin.com/company/oreilly-media)。

在 Twitter 上关注我们：[*https://twitter.com/oreillymedia*](https://twitter.com/oreillymedia)。

观看我们的 YouTube 频道：[*https://youtube.com/oreillymedia*](https://youtube.com/oreillymedia)。

# 致谢

## Eric Meyer

首先，我要感谢本版的所有技术审阅者，他们投入了时间和专业知识来发现我所有错误的地方，而且收获远远不及他们应得的回报。按照姓氏字母顺序排列：Ire Aderinokun、Rachel Andrew、Adam Argyle、Amelia Bellamy-Royds、Chen Hui Jing、Stephanie Eckles、Eva Ferreira、Mandy Michael、Schalk Neethling、Jason Pamental、Janelle Pizarro、Eric Portis、Miriam Suzanne、Lea Verou 和 Dan Wilson。任何错误都是我的错，而不是他们的。

我也要感谢所有过去版本的技术审阅者，这里不能一一列举，以及多年来帮助我理解各种 CSS 细节的人，这些人同样也太多了无法一一列出。如果你曾经向我解释过一些 CSS，请在下面的空白处写下你的名字：_______________________，我对你的感激之情无以言表。

感谢所有 CSS 工作组的成员，无论是过去还是现在，你们把一个令人惊叹的语言引向了令人惊叹的高度......即使你们的工作意味着我们在下一版书籍中将面临真正的生产困境，这本书已经在合理技术可管理的极限范围内。

感谢所有保持 Mozilla 开发者网络（MDN）运行和更新的人。

特别感谢所有在 Open Web Docs 做出贡献的优秀人士，以及邀请我担任你们领导委员会成员的机会。

感谢我的合著者 Estelle，感谢你为本书的所有贡献、专业知识和推动所做的努力。

感谢所有的朋友、同事、同僚、熟人和路人，你们给予我奇怪喜好和古怪举止的空间，以及理解、耐心和善意。

而且，我永远对我的家人无限的感激——我的妻子 Kat 和我的孩子们，Carolyn、Rebecca *z’’l*和 Joshua。你们是庇护我的家园，是我天空中的太阳，是我航向的星星。感谢你们教会我的一切。

俄亥俄州克利夫兰高地

December 4, 2022

## Estelle Weyl

我要感谢所有为使 CSS 成为今天的样子付出努力的人，以及所有在技术领域促进多样性和包容性的人。

许多人在与浏览器供应商和开发者合作撰写 CSS 规范时不知疲倦地工作着。如果没有 CSS 工作组的成员们——无论是过去的、现在的还是未来的——我们就不会有规范，没有标准，也没有跨浏览器兼容性。我对每个 CSS 属性和值被添加或从规范中省略的思考过程感到敬畏。像 Tab Atkins、Elika Etimad、Dave Baron、Léonie Watson 和 Greg Whitworth 这样的人不仅致力于规范的制定，还花时间回答问题，并向广大 CSS 公众解释细微之处，特别是我。

我还要感谢所有那些，无论他们是否参与 CSS 工作组，都深入研究 CSS 特性并帮助翻译规范给我们其他人看的人，包括 Sarah Drasner、Val Head、Sara Souidan、Chris Coyier、Jen Simmons 和 Rachel Andrew。此外，我要感谢那些创造使所有 CSS 开发者生活更轻松的工具的人，特别是 Alexis Deveria 因创建和维护[Can I Use 工具](https://caniuse.com)。

我还感激所有为改善开发者社区各个领域的多样性和包容性而贡献时间和精力的人。是的，CSS 很棒。但与伟大的人们在一个伟大的社区中共事同样重要。

当我在 2007 年参加我的第一次技术会议时，演讲者中有 93%是男性，100%是白人。观众的性别多样性稍低，而种族多样性略高。我选择那个会议是因为相比大多数会议，它的阵容更为多样化：实际上有一个女性在其中。环顾四周，我知道事情需要改变，我意识到这是我需要做的事情。那时我没有意识到在接下来的 10 年里，我会遇到多少默默无闻的英雄，他们致力于改善科技行业和生活中各个领域的多样性和包容性。

有太多人默默无闻地、不知疲倦地工作，通常几乎没有人认可他们的贡献，我无法一一列举，但我想重点提到一些人。像 Erica Stanley 的 Women Who Code Atlanta，Carina Zona 的 Callback Women，以及 Jenn Mei Wu 的 Oakland Maker Space 这些人对我产生了多么积极的影响。像 The Last Mile、Black Girls Code、Girls Incorporated、Sisters Code 等群体，还有许多其他组织，激励我创建了一个[Feeding the Diversity Pipeline list](http://www.standardista.com/feeding-the-diversity-pipeline)，以确保通向网页开发职业的道路不仅仅是为特权阶层开放的。

谢谢大家。感谢所有人。由于你们的努力，比起我 10 年前在那个会议上能想象到的，做得更多。

加利福尼亚州旧金山

2023 年 2 月 14 日
