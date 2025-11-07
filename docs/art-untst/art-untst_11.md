# 8 可维护性

本章涵盖

+   失败测试的根本原因

+   常见的可避免的测试代码更改

+   提高当前未失败的测试的可维护性

测试可以使我们开发得更快，除非它们因为所有必要的更改而使我们放慢速度。如果我们能在更改生产代码时避免更改现有的测试，我们就可以开始希望我们的测试是在帮助而不是伤害我们的底线。在本章中，我们将重点关注测试的可维护性。

不可维护的测试会破坏项目进度，当项目被安排在一个更积极的进度表上时，它们通常会被搁置。开发者会简单地停止维护和修复那些更改时间过长或由于非常小的生产代码更改而需要经常更改的测试。

如果可维护性是衡量我们被迫更改测试的频率的指标，我们希望最小化这种情况发生的次数。这迫使我们提出这些问题，如果我们想深入了解根本原因的话：

+   我们何时注意到测试失败，因此可能需要更改？

+   为什么测试会失败？

+   哪些测试失败迫使我们更改测试？

+   当我们没有被迫改变测试时，我们选择何时改变测试？

本章介绍了一系列与可维护性相关的实践，您可以在进行测试审查时使用。

## 8.1 由失败测试引起的更改

一个失败的测试通常是维护性潜在问题的第一个迹象。当然，我们可能在生产代码中找到了一个真正的错误，但如果没有这种情况，测试还有哪些其他原因会导致失败？我将真正的失败称为*真实失败*，将除在底层生产代码中找到错误之外的其他原因导致的失败称为*虚假失败*。

如果我们想要衡量测试的可维护性，我们可以从测量随着时间的推移中错误的测试失败次数以及每次失败的原因开始。我们在第七章中已经讨论了一个这样的原因：当一个测试包含一个错误时。现在让我们讨论其他可能导致错误失败的可能原因。

### 8.1.1 测试不再相关或与另一个测试冲突

当生产代码引入一个与一个或多个现有测试直接冲突的新功能时，可能会出现冲突。而不是测试发现一个错误，它可能会发现冲突或新的需求。也可能有一个通过测试，它针对生产代码应该如何工作的新期望。

要么现有的失败测试不再相关，要么新的需求是错误的。假设需求是正确的，你可能会继续删除不再相关的测试。

注意，有一个常见的例外是“删除测试”规则：当你与*功能开关*一起工作时。当我们讨论测试策略时，在第十章中我们将涉及到功能开关。

### 8.1.2 生产代码 API 的变化

如果被测试的生产代码发生变化，使得现在需要以不同的方式使用正在测试的函数或对象，即使它可能仍然具有相同的功能，测试可能会失败。这种错误失败属于“我们应尽可能避免这种情况”的范畴。

考虑列表 8.1 中的`PasswordVerifier`类，它需要两个构造函数参数：

+   一个`rules`数组（每个都是一个接收输入并返回布尔值的函数）

+   一个`ILogger`接口

列表 8.1 具有两个构造函数参数的密码验证器

```
export class PasswordVerifier {
    ...
    constructor(rules: ((input) => boolean)[], logger: ILogger) {
        this._rules = rules;
        this._logger = logger;
    }

    ...
}
```

我们可以编写一些像下面的测试。

列表 8.2 无工厂函数的测试

```
describe("password verifier 1", () => {
  it("passes with zero rules", () => {
    const verifier = new PasswordVerifier([], { info: jest.fn() }); ❶
    const result = verifier.verify("any input");
    expect(result).toBe(true);
  });

  it("fails with single failing rule", () => {
    const failingRule = (input) => false;
    const verifier = 
      new PasswordVerifier([failingRule], { info: jest.fn() }); ❶
    const result = verifier.verify("any input");
    expect(result).toBe(false);
  });
});
```

❶ 使用代码现有 API 进行测试

如果从可维护性的角度来看这些测试，我们预计未来可能需要做出几个潜在的改变。

代码通常会有很长的生命周期

考虑到你正在编写的代码将在代码库中至少存在 4-6 年，有时甚至十年。在这段时间里，`PasswordVerifier`的设计发生变化的概率有多大？即使是简单的事情，比如构造函数接受更多的参数，或者参数类型发生变化，在更长的时间内变得更有可能。

让我们列出一些未来可能发生到我们的密码验证器上的更改：

+   我们可能会为`PasswordVerifier`的构造函数添加或删除参数。

+   `PasswordVerifier`的一个参数可能改变为不同的类型。

+   `ILogger`函数的数量或它们的签名可能会随时间变化。

+   使用模式发生了变化，所以我们不需要实例化一个新的`PasswordVerifier`，而是直接使用其中的函数。

如果发生任何这些事情，我们需要更改多少测试？目前，我们需要更改所有实例化`PasswordVerifier`的测试。我们能否防止需要一些这些更改？

让我们假设未来已经到来，我们的担忧已经成真——有人改变了生产代码的 API。让我们说构造函数签名已经更改，现在使用`IComplicatedLogger`而不是`ILogger`，如下所示。

列表 8.3 构造函数中的破坏性更改

```
export class PasswordVerifier2 {
  private _rules: ((input: string) => boolean)[];
  private _logger: IComplicatedLogger;

  constructor(rules: ((input) => boolean)[], 
      logger: IComplicatedLogger) {
    this._rules = rules;
    this._logger = logger;
  }
...
}
```

按照现状，我们可能需要更改任何直接实例化`PasswordVerifier`的测试。

工厂函数解耦测试对象的创建

避免未来这种痛苦的一个简单方法是将测试代码的创建解耦或抽象出来，这样对构造函数的更改只需要在集中位置处理。一个唯一目的是创建和预配置对象实例的函数通常被称为*工厂*函数或方法。这种更高级的版本（我们在这里不会涉及）是 Object Mother 模式。

工厂函数可以帮助我们减轻这个问题。接下来的两个列表显示了在签名更改之前我们如何最初编写测试，以及在这种情况下如何轻松适应签名更改。在列表 8.4 中，`PasswordVerifier`的创建已经被提取到它自己的集中化工厂函数中。我也为`fakeLogger`做了同样的事情——现在它也是通过它自己的单独工厂函数创建的。如果未来发生我们之前列出的任何更改，我们只需要更改我们的工厂函数；通常不需要触摸测试。

列表 8.4 重构为工厂函数

```
describe("password verifier 1", () => {
  const makeFakeLogger = () => {
    return { info: jest.fn() };                          ❶
  };

  const makePasswordVerifier = (
    rules: ((input) => boolean)[],
    fakeLogger: ILogger = makeFakeLogger()) => {
      return new PasswordVerifier(rules, fakeLogger); ❷
  };

  it("passes with zero rules", () => {
    const verifier = makePasswordVerifier([]);           ❸

    const result = verifier.verify("any input");

    expect(result).toBe(true);
  });
```

❶ 创建 fakeLogger 的集中点

❷ 创建 PasswordVerifier 的集中点

❸ 使用工厂函数创建 PasswordVerifier

在下面的列表中，我根据签名更改重构了测试。请注意，这个更改并不涉及更改测试，而只是工厂函数。这就是我可以在实际项目中接受的那种可管理的更改。

列表 8.5 重构工厂方法以适应新签名

```
describe("password verifier (ctor change)", () => {
  const makeFakeLogger = () => {
    return Substitute.for<IComplicatedLogger>();
  };

  const makePasswordVerifier = (
    rules: ((input) => boolean)[],
    fakeLogger: IComplicatedLogger = makeFakeLogger()) => {
    return new PasswordVerifier2(rules, fakeLogger);
  };

  // the tests remain the same
});
```

### 8.1.3 其他测试中的更改

测试隔离不足是测试阻塞的一个主要原因——我在咨询和编写单元测试时见过这种情况。你应该记住的基本概念是，一个测试应该始终在自己的小世界中运行，即使它们验证相同的功能，也应该与其他测试隔离。

哭着“失败”的测试

我参与的一个项目中，单元测试表现得很奇怪，随着时间的推移，它们变得更加奇怪。一个测试会失败，然后突然连续几天通过。一天后，它会随机失败，有时即使代码被更改以删除或改变其行为，它也会通过。到了开发者会告诉彼此，“哦，没关系。如果它有时通过，那就意味着它通过了。”的地步。

经过适当调查，我们发现测试在代码中调用了一个不同的（且不可靠的）测试，当其他测试失败时，它会破坏第一个测试。

在尝试了各种解决方案一个月后，我们花了三天时间理清了混乱，当我们最终使测试正常工作时，我们发现我们代码中有一堆我们因为测试有自身的错误和问题而忽略的真正错误。狼来了的故事即使在开发中也是成立的。

当测试没有很好地隔离时，它们可能会互相干扰，让你后悔决定尝试单元测试并承诺自己再也不这样做。我见过这种情况发生。开发者懒得在测试中寻找问题，所以当有问题时，找出问题可能需要花费很多时间。最容易的症状就是我所说的“受限测试顺序”。

受限测试顺序

当一个测试假设先执行了之前的测试，或者没有先执行，因为它依赖于其他测试设置或重置的某些共享状态时，就会发生 *受限的测试顺序*。例如，如果一个测试更改了内存中的共享变量或某些外部资源（如数据库），而另一个测试在第一个测试执行后依赖于该变量的值，那么我们就有了基于顺序的测试之间的依赖关系。

结合这样一个事实，即大多数测试运行器不会（也不会，也许不应该！）保证测试将按特定顺序运行。这意味着如果你今天运行了所有测试，一周后使用测试运行器的新版本再次运行所有测试，测试可能不会按之前的顺序运行。

![08-01](img/08-01.png)

图 8.1 一个共享的 `UserCache` 实例

为了说明问题，让我们看看一个简单的场景。图 8.1 展示了一个使用 `UserCache` 对象的 `SpecialApp` 对象。用户缓存持有单个实例（一个单例），作为应用程序的缓存机制，顺便说一句，也是测试的缓存机制。列表 8.6 展示了 `SpecialApp`、用户缓存和 `IUserDetails` 接口的实现。

列表 8.6 一个共享的用户缓存及其相关接口

```
export interface IUserDetails {
  key: string;
  password: string;
}

export interface IUserCache {
  addUser(user: IUserDetails): void;
  getUser(key: string);
  reset(): void;
}
export class UserCache implements IUserCache {
  users: object = {};
  addUser(user: IUserDetails): void {
    if (this.users[user.key] !== undefined) {
      throw new Error("user already exists");
    }
    this.users[user.key] = user;
  }

  getUser(key: string) {
    return this.users[key];
  }

  reset(): void {
    this.users = {};
  }
}
let _cache: IUserCache;
export function getUserCache() {
  if (_cache === undefined) {
    _cache = new UserCache();
  }
  return _cache;
} 
```

以下列表展示了 `SpecialApp` 的实现。

列表 8.7 `SpecialApp` 的实现

```
export class SpecialApp {
  loginUser(key: string, pass: string): boolean {
    const cache: IUserCache = getUserCache();
    const foundUser: IUserDetails = cache.getUser(key);
    if (foundUser?.password === pass) {
      return true;
    }
    return false;
  }
}
```

这只是一个简单的示例实现，所以不必太担心 `SpecialApp`。让我们看看测试。

列表 8.8 需要按特定顺序运行的测试

```
describe("Test Dependence", () => {
  describe("loginUser with loggedInUser", () => {
    test("no user, login fails", () => {
      const app = new SpecialApp();
      const result = app.loginUser("a", "abc");    ❶
      expect(result).toBe(false);                  ❶
    });

    test("can only cache each user once", () => {
      getUserCache().addUser({                     ❷
        key: "a",
        password: "abc",
      });

      expect(() =>
        getUserCache().addUser({
          key: "a",
          password: "abc",
        })
      ).toThrowError("already exists");
    });

    test("user exists, login succeeds", () => {
      const app = new SpecialApp();
      const result = app.loginUser("a", "abc");    ❸
      expect(result).toBe(true);                   ❸
    });
  });
});
```

❶ 需要用户缓存为空

❷ 将用户添加到缓存中

❸ 需要缓存包含用户

注意，第一个和第三个测试都依赖于第二个测试。第一个测试要求第二个测试尚未执行，因为它需要用户缓存为空。另一方面，第三个测试依赖于第二个测试用预期的用户填充缓存。如果我们只使用 Jest 的 `test.only` 关键字运行第三个测试，该测试将失败：

```
test.only("user exists, login succeeds", () => {
   const app = new SpecialApp();
   const result = app.loginUser("a", "abc");
   expect(result).toBe(true); 
 });
```

这种反模式通常发生在我们尝试重用测试的一部分而不提取辅助函数时。我们最终期望不同的测试先运行，从而避免进行一些设置。这可以工作，直到它不行了。

我们可以分几个步骤进行重构：

+   提取一个用于添加用户的辅助函数。

+   在多个测试中重用此函数。

+   在测试之间重置用户缓存。

以下列表展示了我们如何重构测试以避免这个问题。

列表 8.9 重构测试以消除顺序依赖

```
const addDefaultUser = () =>                              ❶
  getUserCache().addUser({
    key: "a",
    password: "abc",
  });
const makeSpecialApp = () => new SpecialApp();         ❷
describe("Test Dependence v2", () => {
  beforeEach(() => getUserCache().reset());               ❸
  describe("user cache", () => {                          ❹
    test("can only add cache use once", () => {
      addDefaultUser();                                   ❺

      expect(() => addDefaultUser())
        .toThrowError("already exists");
    });
  });

  describe("loginUser with loggedInUser", () => {         ❹
    test("user exists, login succeeds", () => {
      addDefaultUser(); ❺
      const app = makeSpecialApp();

      const result = app.loginUser("a", "abc");
      expect(result).toBe(true);
    });

    test("user missing, login fails", () => {
      const app = makeSpecialApp();

      const result = app.loginUser("a", "abc");
      expect(result).toBe(false);
    });
  });
});
```

❶ 提取的用户创建辅助函数

❷ 提取的工厂函数

❸ 在测试之间重置用户缓存

❹ 新的嵌套 describe 函数

❺ 调用可重用的辅助函数

这里有几个事情在进行中。首先，我们提取了两个辅助函数：一个`makeSpecialApp`工厂函数和一个`addDefaultUser`辅助函数，我们可以重用它。接下来，我们创建了一个非常重要的`beforeEach`函数，在每个测试之前重置用户缓存。每当我有这样的共享资源时，我几乎总是有一个`beforeEach`或`afterEach`函数，在测试运行前后将其重置到原始状态。

现在第一个和第三个测试现在运行在自己的小嵌套`describe`结构中。它们也都使用了`makeSpecialApp`工厂函数，其中一个使用了`addDefaultUser`来确保它不需要先运行其他任何测试。第二个测试也运行在自己的嵌套`describe`函数中，并重用了`addDefaultUser`函数。

## 8.2 重构以提高可维护性

到目前为止，我讨论了迫使我们必须做出更改的测试失败。现在让我们讨论我们*选择*做出的更改，以使测试随着时间的推移更容易维护。

### 8.2.1 避免测试私有或受保护的函数

本节更多地适用于面向对象的语言以及 TypeScript。在开发者的心目中，私有或受保护的函数通常出于良好的原因而保持私有。有时是为了隐藏实现细节，这样实现可以在不改变可观察行为的情况下进行更改。也可能是出于安全相关或知识产权相关的原因（例如，混淆）。

当你测试私有方法时，你是在测试系统内部的合同。内部合同是动态的，当你对系统进行重构时，它们可能会改变。当它们改变时，你的测试可能会失败，因为某些内部工作以不同的方式进行，尽管系统的整体功能保持不变。从测试的目的来看，公共合同（可观察的行为）是你需要关心的全部。测试私有方法的功能可能会导致测试中断，即使可观察的行为是正确的。

这样想：没有私有方法存在于真空中。在某个地方，必须有人调用它，否则它永远不会被触发。通常有一个公共方法最终调用这个私有方法，如果没有，在调用链中总有一个公共方法被调用。这意味着任何私有方法总是系统中的更大工作单元或用例的一部分，它从公共 API 开始，以三个结果之一结束：返回值、状态更改或第三方调用（或三者兼有）。

因此，如果你看到一个私有方法，找到系统中将使用它的公共用例。如果你只测试私有方法并且它工作正常，这并不意味着系统的其余部分正确地使用了这个私有方法或正确处理了它提供的结果。你可能有一个内部工作完美无缺的系统，但所有这些美好的内部东西都是从公共 API 中错误使用的。

有时，如果一个私有方法值得测试，那么将其公开、静态化或至少内部化，并定义一个公共契约，针对任何使用它的代码，可能是有价值的。在某些情况下，如果将方法放入不同的类中，设计可能会更简洁。我们稍后会探讨这些方法。

这是否意味着代码库中最终不应该有私有方法？不。在使用测试驱动设计的情况下，你通常会对公开的方法编写测试，并且这些公开的方法随后会被重构为调用更小、更私有的方法。在此过程中，对公开方法的测试仍然会通过。

将方法公开

将方法公开并不一定是坏事。在一个更功能化的世界中，这甚至不是一个问题。这种做法可能看起来与许多人都接受的面向对象原则相悖，但情况并不总是如此。

考虑到想要测试一个方法可能意味着该方法对调用代码有一个已知的行为或契约。通过将其公开，你使这成为官方的。通过保持方法私有，你告诉所有在你之后到来的开发者，他们可以更改方法的实现，而无需担心使用它的未知代码。

将方法提取到新的类或模块中

如果你的方法包含大量可以独立存在的逻辑，或者它使用了与特定方法相关的特定状态变量，那么将方法提取到新的类或具有特定系统角色的模块中可能是个好主意。然后你可以单独测试这个类。Michael Feathers 的《与遗留代码有效工作》（Pearson，2004）中有一些关于这种技术的良好示例，Robert Martin 的《代码整洁之道》（Pearson，2008）可以帮助你确定何时这样做是有益的。

将无状态私有方法公开和静态化

如果你的方法完全无状态，有些人会选择通过将其改为静态（在支持此特性的语言中）来重构该方法。这使得它更容易测试，但也表明该方法是一种具有已知公共契约的实用方法，该契约由其名称指定。

### 8.2.2 保持测试 DRY（不要重复自己）

单元测试中的重复可能会对你造成伤害，就像在生产代码中的重复一样，甚至可能更多。这是因为任何有重复的代码片段的更改都会迫使你更改所有重复的部分。当你处理测试时，开发者可能会避免这种麻烦，删除或忽略测试而不是修复它们的风险更大。

DRY（不要重复自己）原则应该在测试代码中生效，就像在生产代码中一样。重复的代码意味着当你测试的一个方面发生变化时，有更多的代码需要更改。更改构造函数或更改类使用的语义可能会对有大量重复代码的测试产生重大影响。

正如我们在本章前面的示例中看到的那样，使用辅助函数可以帮助减少测试中的重复。

警告：消除重复也可能做得太过分，从而损害可读性。我们将在下一章，关于可读性的章节中讨论这个问题。

### 8.2.3 避免使用设置方法

我不是`beforeEach`函数（也称为*设置*函数）的粉丝，这个函数在每个测试之前只发生一次，通常用于消除重复。我更倾向于使用辅助函数。设置函数很容易被滥用。开发者倾向于将它们用于它们本不应用于的事情，结果测试的可读性和可维护性会降低。

许多开发者以几种方式滥用设置方法：

+   在设置方法中初始化仅用于文件中某些测试的对象

+   拥有冗长且难以理解的设置代码

+   在设置方法内设置模拟和假对象

此外，设置方法也有局限性，你可以通过使用简单的辅助方法来克服这些局限性：

+   设置方法只有在需要初始化事物时才能提供帮助。

+   设置方法并不总是消除重复的最佳候选者。消除重复并不总是关于创建和初始化对象的新实例。有时它关于消除断言逻辑中的重复或以特定方式调用代码。

+   设置方法不能有参数或返回值。

+   设置方法不能用作返回值的工厂方法。它们在测试执行之前运行，因此它们的工作方式必须更通用。测试有时需要请求特定的事物或使用参数调用特定测试的共享代码（例如，检索一个对象并将其属性设置为特定值）。

+   设置方法应仅包含适用于当前测试类中所有测试的代码，否则方法将更难以阅读和理解。

我几乎完全停止使用设置方法来编写测试。测试代码应该既整洁又清晰，就像生产代码一样，但如果你的生产代码看起来很糟糕，请不要将其作为编写难以阅读的测试的借口。使用工厂和辅助方法，并为未来 5 年或 10 年将不得不维护你的代码的开发者创造一个更好的世界。

注意：我们在 8.2.3 节（列表 8.9）和第二章中查看了一个从使用`beforeEach`到辅助函数的示例。

### 8.2.4 使用参数化测试来消除重复

如果所有测试看起来都一样，另一个替换设置方法的绝佳选择是使用参数化测试。不同语言的测试框架支持不同的参数化测试——如果你使用 Jest，你可以使用内置的`test.each`或`it.each`函数。

参数化有助于将原本可能重复或位于`beforeEach`块中的设置逻辑移动到测试的安排部分。它还有助于避免断言逻辑的重复，如下面的列表所示。

列表 8.10 使用 Jest 的参数化测试

```
const sum = numbers => {
    if (numbers.length > 0) {
        return parseInt(numbers);
    }
    return 0;
};

describe('sum with regular tests', () => {
    test('sum number 1', () => {
        const result = sum('1');                  ❶
        expect(result).toBe(1);                   ❶
    });
    test('sum number 2', () => {
        const result = sum('2');                  ❶
        expect(result).toBe(2);                   ❶
    });
});
describe('sum with parameterized tests', () => {
    test.each([
 ['1', 1],                              ❷
 ['2', 2] ❷
 ])('add ,for %s, returns that number', (input, expected) => {
            const result = sum(input);            ❸
            expect(result).toBe(expected);        ❸
        }
    )
});
```

❶ 重复的设置和断言逻辑

❷ 用于设置和断言的测试数据

❸ 无重复的设置和断言

在第一个`describe`块中，我们有两次测试，它们使用不同的输入值和预期输出重复。在第二个`describe`块中，我们使用`test.each`提供一个数组数组，其中每个子数组列出测试函数所需的全部值。

参数化测试可以帮助减少测试之间的许多重复，但我们应该小心只在重复完全相同的场景，并且只更改输入和输出的情况下使用这种技术。

## 8.3 避免过度指定

一个过度指定的测试是包含关于特定单元（生产代码）应该如何实现其内部行为的假设的测试，而不是只检查可观察的行为（退出点）是否正确。

下面是单元测试通常过度指定的方法：

+   测试断言在测试对象中纯粹的内状态。

+   一个测试使用多个模拟。

+   一个测试使用存根作为模拟。

+   一个测试在不需要时假设特定的顺序或精确的字符串匹配。

让我们看看一些过度指定测试的例子。

### 8.3.1 使用模拟的内部行为过度指定

一个非常常见的反模式是验证一个类或模块中的内部函数是否被调用，而不是检查工作单元的退出点。以下是一个密码验证器，它调用了一个内部函数，而测试不应该关心这一点。

列表 8.11 调用受保护函数的生产代码

```
export class PasswordVerifier4 {
  private _rules: ((input: string) => boolean)[];
  private _logger: IComplicatedLogger;

  constructor(rules: ((input) => boolean)[],
      logger: IComplicatedLogger) {
    this._rules = rules;
    this._logger = logger;
  }

  verify(input: string): boolean {
    const failed = this.findFailedRules(input); ❶

    if (failed.length === 0) {
      this._logger.info("PASSED");
      return true;
    }
    this._logger.info("FAIL");
    return false;
  }

  protected findFailedRules(input: string) {      ❷
    const failed = this._rules
      .map((rule) => rule(input))
      .filter((result) => result === false);
    return failed;
  }
}
```

❶ 调用内部函数

❷ 内部函数

注意我们调用了受保护的`findFailedRules`函数来从它获取结果，然后对这个结果进行计算。

这是我们的测试。

列表 8.12 验证调用受保护函数的过度指定测试

```
describe("verifier 4", () => {
  describe("overspecify protected function call", () => {
    test("checkfailedFules is called", () => {
      const pv4 = new PasswordVerifier4(
        [], Substitute.for<IComplicatedLogger>()
      ); 
      const failedMock = jest.fn(() => []);     ❶
   pv4["findFailedRules"] = failedMock; ❶

pv4.verify("abc");

     expect(failedMock).toHaveBeenCalled(); ❷
    });
  });
});
```

❶ 模拟内部函数

❷ 验证内部函数调用。不要这样做。

这里的反模式是我们证明了不是退出点的东西。我们检查代码是否调用了某个内部函数，但这实际上证明了什么呢？我们没有检查计算结果是否正确；我们只是在测试。

如果函数正在返回一个值，通常这是一个强烈的迹象表明我们不应该模拟该函数，因为函数调用本身并不代表退出点。退出点是`verify()`函数返回的值。我们不应该关心内部函数是否存在。

通过验证一个受保护函数的模拟（该函数不是退出点），我们将测试实现耦合到被测试代码的内部实现中，这并没有带来真正的益处。当内部调用发生变化（它们会的）时，我们也必须更改所有与这些调用相关的测试，这不会是一个愉快的体验。你可以在 Vladimir Khorikov 的《单元测试原则、实践和模式》（Manning, 2020）一书的第五章中了解更多关于模拟及其与测试脆弱性的关系。

我们应该做什么呢？

寻找退出点。真正的退出点取决于我们希望执行哪种类型的测试：

+   *基于值的测试*—对于基于值的测试，我强烈建议你在可能的情况下倾向于这种测试，我们寻找被调用函数的返回值。在这种情况下，`verify` 函数返回一个值，因此它是基于值测试的完美候选：`pv4.verify("abc")`。

+   *基于状态的测试*—对于基于状态的测试，我们寻找一个兄弟函数（一个与入口点处于相同作用域级别的函数）或一个受调用 `verify()` 函数影响的兄弟属性。例如，`firstname()` 和 `lastname()` 可以被认为是兄弟函数。这就是我们应该进行断言的地方。在这个代码库中，没有东西在相同级别上被 `verify()` 调用所影响，因此它不是一个好的基于状态测试的候选。

+   *第三方测试*—对于第三方测试，我们可能需要使用模拟，这将要求我们找出代码中“一次性”位置在哪里。`findFailedRules` 函数并不是这样的，因为它实际上是在向我们的 `verify()` 函数返回信息。在这种情况下，我们没有真正的第三方依赖需要接管。

### 8.3.2 精确输出和顺序过度指定

一个常见的反模式是当测试过度指定了返回值集合的顺序和结构。通常在断言中指定整个集合及其每个项目更容易，但采用这种方法，我们隐式地承担了在集合的任何细节发生变化时修复测试的负担。而不是使用单个大的断言，我们应该将验证的不同方面分离成更小、更明确的断言。

以下列表显示了一个 `verify()` 函数，它接受多个输入并返回一个结果对象列表。

列表 8.13 返回输出列表的验证器

```
interface IResult {
  result: boolean;
  input: string;
}

export class PasswordVerifier5 {
  private _rules: ((input: string) => boolean)[];

  constructor(rules: ((input) => boolean)[]) {
    this._rules = rules;
  }

  verify(inputs: string[]): IResult[] {
 const failedResults = 
 inputs.map((input) => this.checkSingleInput(input));
    return failedResults;
  }

  private checkSingleInput(input: string): IResult {
    const failed = this.findFailedRules(input);
    return {
      input,
      result: failed.length === 0,
    };
  }
```

这个 `verify()` 函数返回一个包含 `IResult` 对象的数组，每个对象都有一个 `input` 和 `result`。以下列表显示了一个测试，它隐式检查了结果的顺序和结构，以及检查结果值。

列表 8.14 过度指定结果的顺序和模式

```
test("overspecify order and schema", () => {
  const pv5 = 
    new PasswordVerifier5([input => input.includes("abc")]);

  const results = pv5.verify(["a", "ab", "abc", "abcd"]);

  expect(results).toEqual([           ❶
    { input: "a", result: false },    ❶
    { input: "ab", result: false },   ❶
    { input: "abc", result: true },   ❶
    { input: "abcd", result: true },  ❶
  ]);
});
```

❶ 单个大的断言

这个测试在未来可能会如何变化？这里有一些它可能需要变化的原因：

+   当 `results` 数组的长度发生变化时

+   当每个 `result` 对象增加或删除一个属性（即使测试不关心这些属性）

+   当结果顺序发生变化时（即使它可能对当前测试不重要）

如果将来发生这些变化中的任何一项，但你的测试只是关注检查验证器的逻辑和其输出的结构，那么维护这个测试将会涉及很多痛苦。

我们可以通过验证对我们来说重要的部分来减少一些这种痛苦。

列表 8.15 忽略结果的模式

```
test("overspecify order but ignore schema", () => {
  const pv5 = 
    new PasswordVerifier5([(input) => input.includes("abc")]);

  const results = pv5.verify(["a", "ab", "abc", "abcd"]);

  expect(results.length).toBe(4);
  expect(results[0].result).toBe(false);
  expect(results[1].result).toBe(false);
  expect(results[2].result).toBe(true);
  expect(results[3].result).toBe(true);
});
```

而不是提供完整的预期输出，我们可以简单地断言输出中特定属性的值。然而，如果结果顺序发生变化，我们仍然会遇到麻烦。如果我们不关心顺序，我们可以简单地检查输出是否包含特定的结果，如下所示。

列表 8.16 忽略顺序和模式

```
test("ignore order and schema", () => {
  const pv5 = 
    new PasswordVerifier5([(input) => input.includes("abc")]);

  const results = pv5.verify(["a", "ab", "abc", "abcd"]);

  expect(results.length).toBe(4);
  expect(findResultFor("a")).toBe(false);
  expect(findResultFor("ab")).toBe(false);
  expect(findResultFor("abc")).toBe(true);
  expect(findResultFor("abcd")).toBe(true);
});
```

这里我们使用 `findResultFor()` 来找到给定输入的特定结果。现在结果的顺序可以改变，或添加额外的值，但我们的测试只有在计算真实或假结果发生变化时才会失败。

另一个人们倾向于重复的常见反模式是在单元的返回值或属性中针对硬编码的字符串进行断言，而只需要字符串的特定部分。问问自己，“我能否检查字符串*包含*某些内容，而不是*等于*某些内容？”以下是一个密码验证器，它给我们一条消息，描述在验证过程中违反了多少规则。

列表 8.17 返回字符串消息的验证器

```
export class PasswordVerifier6 {
  private _rules: ((input: string) => boolean)[];
  private _msg: string = "";

  constructor(rules: ((input) => boolean)[]) {
    this._rules = rules;
  }

  getMsg(): string {
    return this._msg;
  }

  verify(inputs: string[]): IResult[] {
    const allResults = 
      inputs.map((input) => this.checkSingleInput(input));
    this.setDescription(allResults);
    return allResults;
  }

  private setDescription(results: IResult[]) {
    const failed = results.filter((res) => !res.result);
    this._msg = `you have ${failed.length} failed rules.`;
  }
```

以下列表展示了两个使用 `getMsg()` 的测试。

列表 8.18 使用等式过度指定字符串

```
describe("verifier 6", () => {
  test("over specify string", () => {
    const pv5 = 
      new PasswordVerifier6([(input) => input.includes("abc")]);

    pv5.verify(["a", "ab", "abc", "abcd"]);

    const msg = pv5.getMsg();
    expect(msg).toBe("you have 2 failed rules.");   ❶
  });

  //Here's a better way to write this test
  test("more future proof string checking", () => {
    const pv5 = 
      new PasswordVerifier6([(input) => input.includes("abc")]);

    pv5.verify(["a", "ab", "abc", "abcd"]);

    const msg = pv5.getMsg();
    expect(msg).toMatch(/2 failed/);                ❷
  });
});
```

❶ 过于具体的字符串期望

❷ 更好地断言字符串的方法

第一个测试检查字符串是否与另一个字符串完全相等。这通常适得其反，因为字符串是一种用户界面。我们倾向于随着时间的推移对其进行轻微的修改和装饰。例如，我们关心字符串末尾是否有句号吗？我们的测试要求我们关心，但断言的核心是显示正确的数字（特别是由于字符串在不同计算机语言或文化中会发生变化，但数字通常保持不变）。

第二个测试简单地查找消息中的“2 failed”字符串。这使得测试更具未来性：字符串可能略有变化，但核心信息保持不变，而不需要我们修改测试。

## 摘要

+   测试会随着被测试的系统而增长和变化。如果我们不关注可维护性，我们的测试可能需要我们进行很多修改，这可能不值得修改它们。我们可能最终会删除它们，并丢弃所有投入创建它们的辛勤工作。为了使测试在长期内有用，它们应该只因为真正关心的原因而失败。

+   *真正失败* 是指测试失败是因为它发现了生产代码中的错误。*假失败* 是指测试因其他任何原因而失败。

+   为了估计测试的可维护性，我们可以随着时间的推移测量错误测试失败的数量和每次失败的原因。

+   测试可能由于多种原因而错误地失败：它与另一个测试冲突（在这种情况下，你应该只是移除它）；生产代码 API 的变化（这可以通过使用工厂和辅助方法来缓解）；其他测试的变化（这些测试应该相互解耦）。

+   避免测试私有方法。私有方法是实现细节，由此产生的测试将会很脆弱。测试应该验证*可观察的行为*——对最终用户相关的行为。有时，需要测试私有方法可能是一个缺少抽象的迹象，这意味着该方法应该公开，甚至可以提取到一个单独的类中。

+   保持测试 DRY。使用辅助方法来抽象安排和断言部分的非必要细节。这将简化你的测试，而不会使它们相互耦合。

+   避免使用如`beforeEach`函数这样的设置方法。再次强调，使用辅助方法代替。另一个选择是参数化你的测试，并将`beforeEach`块的内容移动到测试的安排部分。

+   避免过度指定。过度指定的例子包括断言被测试代码的私有状态、对存根的调用断言，或者在没有要求的情况下假设结果集合中元素的具体顺序或精确字符串匹配。
