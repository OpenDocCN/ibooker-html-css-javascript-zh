# 前置内容

## 前言

感谢您阅读《全栈 GraphQL 应用》。本书的目标是展示如何将 GraphQL、React、Apollo 和 Neo4j 数据库（所谓的 GRANDstack）结合使用，以构建复杂、数据密集型的全栈应用。您可能想知道我们为什么选择了这种特定的技术组合。随着您阅读本书，我希望您能意识到在整个堆栈中使用图数据模型（从数据库到 API，再到前端客户端的数据获取代码）带来的开发者生产力、性能和直观优势。

这是我作为一家小型初创公司的第一位工程人员时希望存在的书籍，当时我被分配构建我们的全栈 Web 应用程序。我们花费了几个月的时间评估我们的技术堆栈，并探索它们如何结合在一起。最终，我们找到了解决方案，并使用我们满意的技术组合投入了生产，但到达那里需要许多迭代。

在过去几年中，GraphQL 这一技术从根本上改变了开发者对 Web 开发的看法。本书专注于 GraphQL；然而，仅仅了解如何构建 GraphQL 服务器和编写 GraphQL 操作是不够的，要将全栈应用投入生产。我们需要考虑如何在我们的前端应用程序中启用 GraphQL 数据获取和状态管理，如何保护我们的 API，如何部署我们的应用程序，以及许多其他考虑因素。这就是为什么本书不仅仅关于 GraphQL；相反，我们通过展示各个部分如何结合在一起，全面探索使用 GraphQL。如果您负责使用 GraphQL 构建全栈应用，那么这本书就是为您准备的！

## 致谢

撰写一本书是一个漫长的过程，需要许多人的帮助和支持。如果不遗漏任何人的话，不可能感谢所有帮助本书成书的个人。当然，如果没有参与创建我们所涵盖的令人惊叹的技术的人员，这本书是不可能的。

感谢迈克尔·斯蒂普斯提出撰写关于 GraphQL 的书籍的想法，并帮助构思全栈 GraphQL 的概念，感谢凯伦·米勒对每一章早期版本的宝贵反馈，以及所有参与 Manning 出版社的同事们：道格、亚历山大、安迪、克里斯蒂安、梅洛迪、尼克、戈兰和玛丽亚。感谢我的家人在我撰写本书期间的理解和支持。特别感谢图数据库社区在验证本书中的理念以及为 Neo4j GraphQL 库提供优秀反馈和贡献。

致所有审稿人：Andres Sacco、Brandon Friar、Christopher Haupt、Damian Esteban、Danilo Zekovic、Deniz Vehbi、Ferit Topcu、Frans Oilinki、Gustavo Gomes、Harsh Raval、Ivo Sánchez Checa Crosato、José Antonio Hernández Orozco、José San Leandro、Kevin Ready、Konstantinos Leimonis、Krzysztof Kamyczek、Michele Adduci、Miguel Isidoro、Richard Meinsen、Richard Vaughan、Rob Lacey、Ronald Borman、Ryan Huber、Satej Kumar Sahu、Simeon Leyzerzon、Stefan Turalski、Tanya Wilke、Theofanis Despoudis 和 Vladimir Pasman，你们的建议帮助使这本书变得更好。

## 关于本书

《全栈 GraphQL 应用》的目标是展示全栈 GraphQL 应用程序的各个部分是如何结合在一起的，以及全栈开发者如何利用在线服务来实现开发和部署。这是通过介绍概念并在构建和部署全栈业务审查应用程序的过程中逐步构建和部署来实现的。

### 适合阅读本书的人群？

这本书旨在为对 GraphQL 感兴趣的全栈 Web 开发者而写，他们至少对 Node.js API 应用程序和连接到这些 API 的客户端 JavaScript 应用程序有基本的理解。成功的读者将对 Node.js 有一些基本了解，并对客户端 JavaScript 有基本理解，但最重要的是，他们将有一个理解如何利用 GraphQL 构建 GraphQL 服务和应用的动机。

### 本书组织结构：路线图

本书由九章组成，分为三个部分。每个章节都在构建全栈业务审查应用程序的背景下介绍新的概念和技术。

在第一部分，我们介绍了 GraphQL、Neo4j 图数据库以及图思维的概念：

+   第一章讨论了全栈 GraphQL 应用程序的组成部分，包括对本书中我们将使用到的每种特定技术（GraphQL、React、Apollo 和 Neo4j 数据库）的介绍。

+   第二章介绍了 GraphQL 和构建 GraphQL API 的基础知识（类型定义和解析器函数）。

+   第三章介绍了 Neo4j 图数据库、属性图数据模型和 Cypher 查询语言。

+   第四章展示了如何使用 Neo4j GraphQL 库将 GraphQL 的力量带到 Neo4j 图数据库中。

在第二部分，我们专注于使用 React 开发我们的客户端应用程序：

+   第五章介绍了 React 框架和我们在构建前端应用程序时需要了解的重要概念。

+   第六章展示了如何使用 React 和 GraphQL 启用数据获取和客户端状态管理，正如我们在前几章中构建的 GraphQL API 一样。

在第三部分，我们探索了如何使用云服务来保护我们的应用程序并部署它：

+   第七章展示了我们可以如何使用 GraphQL 和 Auth0 来保护我们的应用程序。

+   第八章介绍了我们将用于部署数据库、GraphQL API 和 React 应用程序的云服务。

+   第九章通过探讨如何在 GraphQL 中利用抽象类型、基于游标的分页以及处理 GraphQL 中的关系属性来结束本书。

本书旨在从头到尾阅读，因为每一章都是基于前一章的工作，所有这些工作都是为了构建一个完整的全栈业务审查应用程序。读者可以选择专注于单个章节，深入探讨特定感兴趣的主题，但务必参考前几章，以了解其他部分的应用程序是如何和为什么被构建的。

### 关于代码

本书包含许多源代码示例，既有编号列表，也有与普通文本混排。在两种情况下，源代码都以固定宽度字体格式化，如这样，以将其与普通文本区分开来。有时代码也会被**加粗**，以突出显示章节中从先前步骤更改的代码，例如当新功能添加到现有代码行时。

在许多情况下，原始源代码已被重新格式化；我们添加了换行并重新调整了缩进，以适应书籍中的可用页面空间。在极少数情况下，即使这样也不够，列表中还包括了行续符（➥）。此外，当代码在文本中描述时，源代码中的注释通常也会从列表中删除。许多列表都有代码注释，突出显示重要概念。

您可以从本书的 liveBook（在线）版本中获取可执行的代码片段 [`livebook.manning.com/book/fullstack-graphql-applications`](https://livebook.manning.com/book/fullstack-graphql-applications)。书中示例的完整代码可以从 Manning 网站 [www.manning.com](http://www.manning.com) 和 GitHub [`github.com/johnymontana/fullstack-graphql-book`](https://github.com/johnymontana/fullstack-graphql-book) 下载。

### 软件硬件要求

读者需要安装 Node.js 的最新版本。我使用了最新版本 v16，因此建议使用 nvm 工具来安装和管理 Node.js 版本。nvm 的安装和使用说明可以在 [`github.com/nvm-sh/nvm`](https://github.com/nvm-sh/nvm) 找到。

我们还将使用几个（免费）在线服务进行部署。大多数这些服务都可以使用 GitHub 账户访问，因此请确保创建一个 GitHub 账户，如果您目前还没有的话，可以在 [`github.com/`](https://github.com/) 创建。

### liveBook 讨论论坛

购买《全栈 GraphQL 应用》包括对 Manning 的在线阅读平台 liveBook 的免费访问。使用 liveBook 的独家讨论功能，您可以在全球范围内或针对特定部分或段落附加评论。为自己做笔记、提出和回答技术问题，以及从作者和其他用户那里获得帮助都非常简单。要访问论坛，请访问[`livebook.manning.com/book/fullstack-graphql-applications/discussion`](https://livebook.manning.com/book/fullstack-graphql-applications/discussion)。您还可以在[`livebook.manning.com/discussion`](https://livebook.manning.com/discussion)了解更多关于 Manning 的论坛和行为准则。

Manning 对我们读者的承诺是提供一个场所，在那里个人读者之间以及读者与作者之间可以进行有意义的对话。这不是对作者参与特定数量承诺的承诺，作者对论坛的贡献仍然是自愿的（且未付费）。我们建议您尝试向作者提出一些挑战性的问题，以免他的兴趣转移！只要书籍在印刷中，论坛和先前讨论的存档将从出版社的网站提供访问。

### 其他在线资源

请务必查阅 Neo4j GraphQL 库的文档，链接为[`neo4j.com/docs/graphql-manual/current/`](https://neo4j.com/docs/graphql-manual/current/)。其他可能有所帮助的在线资源包括 GraphAcademy 提供的免费在线课程（[`graphacademy.neo4j.com/`](https://graphacademy.neo4j.com/)）和 Neo4j 社区网站（[`community.neo4j.com/`](https://community.neo4j.com/)）。

## 关于作者

![Lyon_author.photo](img/Lyon_author.photo.png)

William Lyon 是 Neo4j 的员工开发者倡导者，在那里他帮助开发者成功构建使用图形的应用程序。加入 Neo4j 之前，他在初创公司担任软件工程师，从事定量金融系统、房地产行业的移动应用程序和预测 API 服务。他拥有蒙大拿大学计算机科学硕士学位，并在[lyonwj.com](https://lyonwj.com/)上发布博客。

## 关于封面插图

《全栈 GraphQL 应用》的封面图题为“Tinne 岛的女神”，或称“Tinne 岛的女郎”，取自 Jacques Grasset de Saint-Sauveur 的收藏，该收藏于 1797 年出版。每一幅插图都是手工精心绘制和着色的。

在那些日子里，人们通过他们的服饰很容易就能识别出他们住在哪里，以及他们的职业或社会地位。Manning 通过基于几个世纪前丰富多样的地区文化的封面设计，以及此类收藏中的图片，来庆祝计算机行业的创新精神和主动性。
