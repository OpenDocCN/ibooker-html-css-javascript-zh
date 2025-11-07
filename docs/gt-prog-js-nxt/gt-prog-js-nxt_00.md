## 第 1 课. ECMAScript 规范和提案流程

在本课中，你将了解 JavaScript 的起源以及 JavaScript 与 ECMAScript 之间的区别。由于本书是关于从 ES2015 开始引入的新特性，在本课中，你将了解这些新特性是如何被提出来作为语言规范的，以及这些提案成为语言规范的过程。

### 1.1. ECMAScript 的简要历史

JavaScript 最初是在 1995 年由 Netscape 创建的。后来，该语言被提交给 Ecma International 进行标准化，并于 1997 年发布了 ECMAScript 第一版。Ecma International 曾经被称为欧洲计算机制造商协会（ECMA），但为了反映其全球地位，将其名称更改为“Ecma International”。尽管 Ecma 不再是缩写，但 ECMAScript 仍然使用大写的 ECMA。在 ECMAScript 发布后，接下来的两年每年都看到了更新版的发布，其中 ECMAScript 第三版，通常被称为*ES3*，于 1999 年 12 月发布。

*ECMAScript 第四版（ES4）*原本打算进行一次彻底的变革。它引入了许多新概念，包括类和接口，并且是静态类型。它也与 ES3 不向后兼容。这意味着如果实施，它有可能破坏野外的现有 JavaScript 应用程序。不用说，ES4 是有争议的，并分裂了 Ecma 技术委员会，导致成立了一个子委员会来工作于一个规模较小的更新，被称为*ECMAScript 3.1*。最终，ECMAScript 第四版被放弃，ECMAScript 3.1 被更名为*ES5（第五版）*并于 2009 年发布。

如果你一直在计算，那已经是 10 年，在发布语言新版本之前整整十年——尽管版本号跳了两级，但更新实际上相当小。

### 1.2. 为什么 ES2015 添加了这么多

*ECMAScript 第六版*于 2015 年 6 月最终确定。这是自 15 年前以来语言的第一次重大更新。网络景观以及网站和 Web 应用程序的构建方式发生了巨大变化，因此自然会有许多新想法，这导致了新的语法、运算符、原始类型和对象，以及对现有内容的增强，以及大量新概念。所有这些加起来，使得第六版成为了一次重大修订。

最初（并且通常仍然）被称为*ES6*的第六版，为了与每年发布一个新版本的原始策略相一致，被更名为*ES2015*。因此，*ECMAScript 第七版*最初被称为*ES7*，后来更名为*ES2016*，于 2016 年 6 月最终确定。

每年发布一个新版本的思路是，语言可以逐渐且持续地成熟，而无需经历像 2000 年代初那样的停滞期。这也应该使新版本更容易、更快地为开发者所采用。

### 1.3.谁决定添加什么？

Ecma International 内部的一个称为*TC39*的任务组([`www.ecma-international.org/memento/TC39.htm`](http://www.ecma-international.org/memento/TC39.htm))负责开发和维护 ECMAScript 规范。该小组的成员大多来自构建网络浏览器的公司，如 Mozilla、Google、Microsoft 和 Apple，因为他们对该规范有既得利益，并且必须实施该规范。您可以在[`tc39wiki.calculist.org/about/people/`](http://tc39wiki.calculist.org/about/people/)查看 TC39 成员的完整列表。ECMAScript 规范的增加要经过从阶段 0 到阶段 4 的五阶段流程。

#### 1.3.1.规范阶段

+   *第一阶段：草稿*—这个阶段是非正式的，可以以任何形式存在；它允许任何人将他们的意见添加到语言的进一步发展中。要添加您的意见，您必须是 TC39 的成员或已在 Ecma International 注册。您可以在[`tc39.github.io/agreements/contributor/`](https://tc39.github.io/agreements/contributor/)注册。一旦注册，您可以通过 esdiscuss 邮件列表提出您的想法。您也可以在这些讨论中查看[`esdiscuss.org/`](https://esdiscuss.org/)。

+   *第一阶段：提案*—在草稿完成后，TC39 的成员必须支持这一添加，以推进到下一阶段。这意味着 TC39 的成员必须解释为什么这一添加是有用的，并描述一旦实施，添加将如何表现和看起来。

+   *第二阶段：草案*—在这个阶段，添加被完全规范化，被视为实验性的。如果添加达到这个阶段，这意味着委员会期望该特性最终会被纳入语言中。

+   *第三阶段：候选*—在这个阶段，解决方案被认为是完整的，并得到了批准。此阶段之后的变更很少，通常是实施和重大使用后的关键发现。在适当的部署期后，添加可以安全地提升到第四阶段。

+   *第四阶段：完成*—这是最终阶段；如果一个添加达到这个阶段，它就可以被纳入正式的 ECMAScript 标准规范中。

关于这些特定阶段和其他关于 TC39 流程的信息，请参阅[`tc39.github.io/process-document/`](https://tc39.github.io/process-document/)。

#### 1.3.2.选择阶段

有像 Babel（见第 2 课）这样的项目，允许你使用今天的 JavaScript 新功能。如果你打算使用这样的工具，在项目开始时选择一个合适的阶段可能是个好主意。如果你只想使用保证将在下一个版本中包含的功能，阶段 4 就很合适。选择阶段 3 也被认为是安全的，因为很可能包含在阶段 3 的功能最终会保持不变，并且变化很少。选择低于该阶段的阶段，你面临的风险是功能在未来可能会改变甚至被撤销。你可能会发现某个功能足够有用，足以使这种风险变得值得承担。

你也可以根据你想要使用的功能来决定选择哪个阶段。当然，你可能不想使用尚未正式包含在 ECMAScript 规范中的任何功能，这是完全可以的。如果你确实想选择一个阶段，你可以在以下网址查看每个阶段包含的功能：

+   阶段 0—[`github.com/tc39/ecma262/blob/master/stage0.md`](https://github.com/tc39/ecma262/blob/master/stage0.md)

+   阶段 1–3—[`github.com/tc39/proposals/blob/master/README.md`](https://github.com/tc39/proposals/blob/master/README.md)

+   阶段 4—[`github.com/tc39/proposals/blob/master/finished-proposals.md`](https://github.com/tc39/proposals/blob/master/finished-proposals.md)

### 1.4\. 本书将涵盖的内容

本书旨在帮助现有的 JavaScript 开发者跟上最新版本的 JavaScript（包括 ES2015、ES2016 以及以后的版本）的步伐，并变得高效。本书重点介绍这些版本和提案中最重要和最广泛使用的功能。本书的目的不是教授 JavaScript 或编程基础。但你也无需成为 JavaScript 专家程序员就能从本书中受益。

由于本书涵盖了 ES2015、ES2016 以及提议/分阶段的功能的混合，我将定义一些术语以使跟踪所有这些内容更容易。从现在开始，我可能将 ES2015 与 ES6 互换使用，当我这样做时，我总是指同一件事：ECMAScript 的第六版。同样，我可能将 ES7 和 ES2016 互换使用。我可能使用术语 *ESNext* 作为泛指，以指代 ES2015 及以后的版本——基本上是 ES5 之后对 JavaScript 的新增内容。

### 摘要

在本课中，你学习了 ECMAScript 是 JavaScript 的官方规范以及提案流程是如何工作的。在下一课中，你将学习如何转换尚未实现的功能，以便你现在可以使用它们。
