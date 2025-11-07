# 前置内容

## 前言

学习一门新语言或技能的难点之一是将学到的个别技能应用到我们试图构建的东西中。虽然我们可能了解网格的机制或理解 flex 的工作原理，但学习选择哪种技能和何时（或如何）实现我们设想的特定目标可能会很具挑战性。在这本书中，我们采取了相反的方法，不是从理论开始然后应用到我们的项目中，而是从项目开始，然后看看哪些技能和技术是必要的以实现我们的目标。

但为什么要谈论 CSS 呢？我们可以仅使用浏览器提供的默认设置来编写整个应用程序，但这不会有多少个性，对吧？有了 CSS，我们可以为我们的用户和业务需求实现很多。从品牌识别到使用一致的风格和设计范式引导用户，再到使项目引人注目，CSS 是我们工具箱中的重要工具。

无论是有库、预处理器还是框架，驱动我们应用程序和网站外观的底层技术是 CSS。考虑到这一点，为了避免被库和框架的个别特性和功能所分散注意力，我们选择回归基础，用纯 CSS 编写这本书，因为如果我们理解了 CSS，那么将其应用到任何其他技术栈或环境中都会变得容易得多。

## 致谢

我们，玛蒂娜和迈克尔，感谢安德鲁·沃尔德伦，收购编辑，以及伊恩·豪，助理收购编辑，他们对我们启动这本书并在整个开发过程中的支持和热情。我们感谢伊丽莎·海德，发展编辑，她从始至终都是我们巨大的支持来源，提供了专业的指导、编辑和鼓励。路易斯·拉扎里斯，技术校对员，以及亚瑟·祖巴列夫，技术发展编辑，提供了深思熟虑、有用的技术反馈和代码审查。感谢你们两位的所有贡献。最后，我们向在整个过程中提供早期访问和审阅的所有读者致以衷心的感谢，他们的反馈帮助塑造和发展了这本书。

我们感谢所有审阅者：Abhijith Nayak，Al Norman，Alain Couniot，Aldo Solis Zenteno，Andy Robinson，Anil Radhakrishna，Anton Rich，Aryan Maurya，Ashley Eatly，Beardsley Ruml，Bruno Sonnino，Carla Butler，Charles Lam，Danilo Zekovic´，Derick Hitchcock，Francesco Argese，Hiroyuki Musha，Humberto A. Sanchez II，James Alonso，James Carella，Jereme Allen，Jeremy Chen，Joel Clermont，Joel Holmes，Jon Riddle，Jonathan Reeves，Jonny Nisbet，Josh Cohen，Kelum Senanayake，Lee Harding，Lin Zhang，Lucian Enache，Marco Carnini，Marc-Oliver Scheele，Margret “Pax” Williams，Matt Deimel，Mladen Ðuric´，Neil Croll，Nick McGinness，Nitin Ainani，Pavel Šimon，Ranjit Sahai，Ricardo Marotti，Rodney Weis，Steffen Gläser，Stephan Max，Steve Grey-Wilson，以及 Vincent Delcoigne。你们的建议帮助使这本书变得更好。

马丁·道登：我要感谢我的家人、朋友和在安德里梅达银河解决方案的同事，他们在我的职业生涯和这本书的写作过程中给予了我坚定不移的支持和鼓励。

我还想感谢 Mozilla 基金会和无数为 MDN 文档做出贡献的个人，他们不辞辛劳地为开发者社区提供了关于 CSS 等网络语言的文档。最后，我要感谢 Caniuse 的创建者 Lennart Schoors 和 Alexis Deveria，以及所有为 Caniuse 做出贡献的人，因为他们使得了解哪些浏览器将支持哪些 CSS 功能变得容易。

迈克尔·吉尔农：这是我写的第一本书，制作它是一个既有趣又具有挑战性的过程。我想感谢所有家庭成员的支持，尤其是我的妻子艾米·史密斯，她在整个过程中一直支持着我。我必须也要特别感谢我的猫， puffin 和 porg，它们试图（但失败了）在书中加入一些奇怪的词。

## 关于这本书

*小型 CSS 项目*通过一系列 12 个项目，让设计师和开发者学习 CSS。

### 适合阅读这本书的人？

*小型 CSS 项目*适合那些了解 HTML 和前端开发基础知识的读者。不需要 CSS 经验。无论是初学者还是有经验的编码者，通过这本书都能更深入地理解 CSS。本书不是提供 CSS 的理论观点，而是将 CSS 的不同部分应用到项目中，以实践的方式展示 CSS 是如何工作的。

### 本书组织结构：路线图

本书共有 12 章，每章都是一个独立的项目：

+   第一章，“CSS 简介”——本章的项目引导读者了解 CSS 的基础知识，检查层叠、特异性和选择器。

+   第二章，“使用 CSS 网格设计布局”——本章通过为文章设计布局来探索 CSS 网格，在这个过程中，还考察了网格轨道、`minmax()`、重复函数和分数单位等概念。

+   第三章，“创建响应式动画加载界面”——本项目使用 CSS 创建一个响应式的动画加载界面，通过可缩放矢量图形和动画来设计 HTML 进度条。

+   第四章，“创建响应式网络报纸布局”——本章讨论设计多列响应式网络报纸布局。它探讨了 CSS 多列布局模块、计数器样式、损坏的图像，以及如何通过使用媒体查询来调整布局。

+   第五章，“具有悬停交互的总结卡片”——本项目通过使用背景图像、悬停时显示内容的过渡效果以及媒体查询来检查能力和浏览器窗口大小，创建了一系列卡片。

+   第六章，“创建个人资料卡片”——本章的项目创建了一个个人资料卡片，并探讨了自定义属性、背景渐变，以及设置图像大小和使用 Flexbox 进行布局。

+   第七章，“充分利用浮动”——本章展示了 CSS 浮动在定位图片、围绕 CSS 形状排列内容以及创建首字母下沉效果方面的强大功能。

+   第八章，“设计结账购物车”——本章讲述了设计结账购物车，这涉及到样式化响应式表格、使用 CSS 网格进行布局、格式化数字以及通过使用媒体查询根据视口大小条件性地设置 CSS。

+   第九章，“创建虚拟信用卡”——本章重点介绍创建虚拟信用卡，并通过在悬停时翻转卡片来实现 3D 效果。

+   第十章，“样式化表单”——本章探讨了设计表单，包括单选按钮、输入框和下拉菜单，以及提高可访问性。

+   第十一章，“动画社交媒体分享链接”——这个项目使用 CSS 过渡来动画化社交媒体分享链接，并探讨了 CSS 架构选项，如 OOCSS、SMACSS 和 BEM。

+   第十二章，“使用预处理器”——最后一章展示了我们在编写 CSS 时如何使用预处理器，并介绍了 Sass 语法。

### 关于代码

本书包含许多源代码示例，无论是编号列表还是与普通文本内联。在两种情况下，源代码都使用`固定宽度字体`如这样来格式化，以将其与普通文本区分开来。有时代码也会`**加粗**`以突出显示章节中先前步骤的变化，例如当新功能添加到现有代码行时。

在许多情况下，原始源代码已被重新格式化；我们添加了换行并重新调整了缩进，以适应书籍中的可用页面空间。在某些情况下，即使这样也不够，列表中还包括了行续接标记（➥）。许多列表旁边都有代码注释，突出显示重要概念。

您可以从本书的 liveBook（在线）版本中获取可执行的代码片段[`livebook.manning.com/book/tiny-css-projects`](https://livebook.manning.com/book/tiny-css-projects)。书中示例的完整代码可以从 Manning 网站[`www.manning.com`](https://www.manning.com)和 GitHub[`github.com/michaelgearon/Tiny-CSS-Projects`](https://github.com/michaelgearon/Tiny-CSS-Projects)下载。

### liveBook 讨论论坛

购买 *Tiny CSS Projects* 包括免费访问 liveBook，Manning 的在线阅读平台。使用 liveBook 的独家讨论功能，您可以在全球范围内或针对特定部分或段落附加评论。为自己做笔记、提问和回答技术问题以及从作者和其他用户那里获得帮助都非常简单。要访问论坛，请访问[`livebook.manning.com/book/tiny-css-projects/discussion`](https://livebook.manning.com/book/tiny-css-projects/discussion)。您还可以在[`livebook.manning.com/discussion`](https://livebook.manning.com/discussion)了解更多关于 Manning 论坛和行为准则的信息。

曼宁对读者的承诺是提供一个场所，让读者之间以及读者和作者之间可以进行有意义的对话。这并不是对作者参与特定数量承诺的承诺，作者对论坛的贡献仍然是自愿的（且未付费）。我们建议您尝试向他们提出一些挑战性的问题，以免他们的兴趣转移！只要这本书有售，论坛和先前讨论的存档将可通过出版社的网站访问。

### 其他在线资源

通常，我们无法记住一个属性是如何工作的，或者我们有哪些可用的值。查找特定属性、函数或值的工作方式的一个极好资源是 MDN 文档 ([`developer.mozilla.org/en-US`](https://developer.mozilla.org/en-US))。

尽管 CSS 功能中的任何特定方面都可以在 CSS 规范中定义，但这并不意味着所有浏览器都支持它。我们经常发现自己需要了解哪些浏览器支持什么，以及我们是否应该创建回退或使用替代方法来实现我们的目标。Caniuse ([`caniuse.com`](https://caniuse.com)) 是一个极好的资源，它允许我们检查特定的属性或函数，以查看它在各个浏览器版本中的支持程度如何。

最后，为了确保每个人都能访问和使用我们的网站和应用，我们不能忘记无障碍的重要性。万维网联盟的 Web 无障碍倡议提供的文档是开始的好地方，它们链接到许多其他资源，包括网络内容无障碍指南 ([`www.w3.org/WAI/fundamentals`](https://www.w3.org/WAI/fundamentals))。

## 关于作者

![Dowden 作者](img/Dowden_author.png)

Martine Dowden 是一位作家、国际演讲者和 Andromeda Galactic Solutions 获奖首席技术官。她的专业知识包括心理学、设计、艺术、无障碍、教育、咨询和软件开发。"Tiny CSS Projects"是她关于网络技术的第四本书，汇集了她在构建美观、功能和无障碍网络界面方面的 15 年经验。对于她的社区贡献，Martine 被命名为微软开发者技术 MVP 和谷歌网络技术及 Angular 开发者专家。

![Gearon 作者](img/Gearon_author.png)

Michael Gearon 是一位来自英国威尔士的用户体验设计师和前端开发者。他在南威尔士大学学习媒体技术并获得学士学位的同时，练习编码和设计。从那时起，Mike 与知名的英国品牌合作，包括 Go.Compare 和 Ageas。他现在在公务员部门工作，之前在公司注册处工作，目前在国家数字服务处工作。

## 关于封面插图

《Tiny CSS Projects》封面上的图像标题为“M’de. de bouquets à Vienne”，或“维也纳的花贩”，这幅画取自雅克·格拉塞·德·圣索沃尔的作品集，该作品集于 1797 年出版。每一幅插图都是手工精细绘制和着色的。

在那些日子里，仅凭人们的服饰就能轻易地识别出他们居住的地方以及他们的职业或社会地位。曼宁通过基于几个世纪前丰富多样的地域文化的书封面来庆祝计算机行业的创新精神和主动性，这些文化通过像这一系列这样的图片被重新带回生活。
