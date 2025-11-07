# 附录. 修补函数和模块

在第三章中，我介绍了各种我称之为“可接受”的模拟技术，因为它们通常被认为对代码的可维护性和可读性以及它们引导我们编写的测试都是安全的。在本附录中，我将描述一些不太被接受且不太安全的方法，我们可以用它们在测试中模拟整个模块。

## A.1 必须的警告

关于全局修补和模拟函数和模块，我有好消息和坏消息。是的，你可以做到这一点——我将向你展示几种实现这一目标的方法。这是一个好主意吗？我并不确信。根据我的经验，使用我将展示的技术来维护测试的成本往往比维护参数化良好或具有适当接口的代码的成本要高。

然而，可能存在一些特殊时刻，你需要使用这些技术。这些时刻包括但不限于，在无法拥有和更改的代码中模拟依赖项，有时在使用立即执行的函数或模块时，以及当模块只暴露函数而没有对象时，这大大限制了模拟选项。

尽可能避免使用我在附录中描述的技术。如果你能找到一种方法来编写测试或重构代码，使其不需要这些方法，请使用那种方法。如果所有其他方法都失败了，附录中的技术是一种必要的恶。如果你必须使用它们，尽量减少使用它们的程度。你的测试将受到影响，变得更为脆弱且难以阅读。

让我们深入探讨。

## A.2 修补函数、全局变量和可能的问题

Monkey-patching 指的是在运行时更改正在运行的程序实例的行为。我第一次遇到这个术语是在我从事 Ruby 开发工作时，在那里 monkey-patching 非常常见。在 JavaScript 中，在运行时“修补”一个函数同样简单。

在第三章中，我们探讨了测试和代码中的时间管理问题。通过 monkey-patching，我们可以查看任何函数，无论是全局的还是局部的，并将其（对于特定的 JavaScript 作用域）替换为不同的实现。如果我们想修补时间，我们可以修补全局的 `Date.now`，这样从那时起的所有代码都会受到影响，包括生产代码和测试代码。

列表 A.1 展示了一个测试，它为直接使用 `Date.now` 的原始生产代码执行了这一操作。它模拟全局 `Date.now` 函数以在测试期间控制时间。

列表 A.1 模拟全局 `Date.now()` 的问题

```
describe('v1 findRecentlyRebooted', () => {
  test('given 1 of 2 machines under threshold, it is found', () => {
    const originalNow = Date.now;             ❶
    const fromDate = new Date(2000,0,3);      ❷
    Date.now = () => fromDate.getTime(); ❷

    const rebootTwoDaysEarly = new Date(2000,0,1);
    const machines = [
      { lastBootTime: rebootTwoDaysEarly, name: 'ignored' },
      { lastBootTime: fromDate, name: 'found' }];

    const result = findRecentlyRebooted(machines, 1, fromDate);

    expect(result.length).toBe(1);
    expect(result[0].name).toContain('found');

    Date.now = originalNow;                   ❸
  });
}); 
```

❶ 保存原始的 Date.now

❷ 用自定义日期替换 Date.now

❸ 恢复原始的 Date.now

在这个列表中，我们正在用自定义日期替换全局 `Date.now`。由于这是一个全局函数，其他测试可能会受到影响，因此我们在测试结束时清理，将原始的 `Date.now` 恢复到其正确的位置。

这样的测试存在几个主要问题。首先，这些断言在失败时抛出异常，这意味着如果它们失败，原始 `Date.now` 的恢复可能永远不会执行，其他测试将遭受“脏”的全局时间，这可能会影响它们。

保存时间函数然后再将其恢复也很麻烦。这给测试留下了痕迹，使得测试变得更长、更难阅读，而且编写起来也更困难。很容易忘记重置全局状态。

最后，我们损害了并行性。Jest 似乎处理得很好，因为它为每个测试文件创建了一组独立的依赖关系，但与其他可能并行运行测试的框架相比，可能会出现竞争条件。多个测试可以更改或期望全局时间具有某个值。当并行运行时，这些测试可能会发生冲突，并在全局状态中创建竞争条件，相互影响。在我们的情况下这不是必需的，但如果你想消除不确定性，Jest 允许你使用额外的 `--runInBand` 命令行参数来避免并行性。

我们可以通过使用 `beforeEach()` 和 `afterEach()` 辅助函数来避免一些这些问题。

列表 A.2 使用 `beforeEach()` 和 `afterEach()`

```
describe('v2 findRecentlyRebooted', () => {
  let originalNow;
  beforeEach(() => originalNow = Date.now);     ❶
  afterEach(() => Date.now = originalNow);      ❷

  test('given 1 of 2 machines under threshold, it is found', () => {
    const fromDate = new Date(2000,0,3);
    Date.now = () => fromDate.getTime();

    const rebootTwoDaysEarly = new Date(2000,0,1);
    const machines = [
      { lastBootTime: rebootTwoDaysEarly, name: 'ignored' },
      { lastBootTime: fromDate, name: 'found' }];

    const result = findRecentlyRebooted(machines, 1, fromDate);

    expect(result.length).toBe(1);
    expect(result[0].name).toContain('found');
  });
});
```

❶ 保存原始的 Date.now

❷ 恢复原始的 Date.now

列表 A.2 解决了我们的一些问题，但不是全部。好处是，我们不再需要记住保存和重置 `Date.now`，因为 `beforeEach()` 和 `afterEach()` 会处理它。现在阅读测试也更简单了。

但是，我们仍然存在一个潜在的重大问题，即并行测试。Jest 足够智能，只按文件并行运行测试，这意味着这个规范文件中的测试将线性运行，但其他文件中的测试行为没有保证。任何一个并行测试都可能有自己的 `beforeEach()` 和 `afterEach()` 来重置全局状态，可能会在不经意间影响我们的测试。

当我能帮忙时，我不是很喜欢伪造全局对象（即在大多数类型语言中的“单例”）。总有附加条件——额外的编码、额外的维护、额外的测试脆弱性，或者间接影响其他测试并担心一直清理都是一些原因。大多数时候，当我把接口因素融入到待测试代码的设计中，而不是以隐式方式围绕它时，代码会更好。就像我们刚才做的那样。

尤其是考虑到越来越多的框架可能会开始复制 Jest 的功能并并行运行测试，全局伪造变得越来越危险。

### A.2.1 以 Jest 的方式猴子补丁一个函数

为了使画面更完整，Jest 还支持通过使用两个协同工作的函数来实现猴子补丁的想法：`spyOn` 和 `mockImplementation`。以下是 `spyOn`：

```
Date.now = jest.spyOn(Date, 'now')
```

`spyOn` 以作用域和需要跟踪的函数作为参数。请注意，我们在这里需要使用一个字符串作为参数，这并不真的有利于重构——如果我们重命名那个函数，很容易就会错过它。

### A.2.2 Jest 间谍

“spy” 这个词比我们在本书中遇到的其他术语要稍微有趣一些，这也是为什么如果可能的话，我不太喜欢过多（或根本不）使用它。不幸的是，这个词是 Jest API 的一个重要部分，所以让我们确保我们理解它。

*xUnit 测试模式*（Addison-Wesley，2007），由 Gerard Meszaros 编著，在其关于间谍的讨论中提到：“使用测试双用来捕获系统（SUT）对另一个组件进行的间接输出调用，以便稍后由测试进行验证。”间谍与存根或测试双重的唯一区别在于，间谍是调用函数底层的真实实现，并且它只跟踪该函数的输入和输出，我们可以通过测试稍后验证这些输入和输出。存根和测试双重不使用函数的真实实现。

我对“间谍”的改进定义非常接近：在测试期间，通过在“工作单元”的“入口点”和“出口点”上添加一个不可见的跟踪层，而不改变其底层功能，以跟踪其输入和输出。

### A.2.3 使用 `mockImplementation()` 的 `spyOn`

间谍固有的这种“跟踪而不改变功能”的行为也解释了为什么仅仅使用 `spyOn` 并不足以让我们伪造 `Date.now`。它仅用于跟踪，而不是伪造。

要实际伪造 `Date.now` 函数并将其转换为存根，我们将使用令人困惑的命名 `mockImplementation` 来替换底层工作单元的功能：

```
jest.spyOn(Date, 'now').mockImplementation(() => /*return stub time*/);
```

“mock” 过多

如果我能决定为 `mockImplementation` 起一个新的名字，我会叫它 `fakeImplementation`，因为它可以很容易地用来创建返回数据的存根或验证传入参数的模拟。在我们的行业中，“mock” 这个词被过度使用，用来表示任何不真实的东西，而区分这一点可以帮助我们编写更稳健的测试。“mock” 在名称中立即暗示了这是我们稍后会验证的东西，至少当我看的时候，考虑到我在本书中对待模拟和存根的观点。

Jest 中“mock”这个词的使用过于频繁，尤其是在将其 API 与 Sinon.js 这样的隔离框架进行比较时，Sinon.js 使用的是不那么令人惊讶的命名方式，并且避免了在不必要的地方使用“mock”。

下面是如何在我们的代码中看到 `spyOn` 和 `mockImplementation` 组合的示例。

列表 A.3 使用 `jest.SpyOn()` 来 monkey-patch `Date.now()`

```
describe('v4 findRecentlyRebooted with jest spyOn', () => {
  afterEach(() => jest.restoreAllMocks());

  test('given 1 of 2 machines under threshold, it is found', () => {
    const fromDate = new Date(2000,0,3);
    Date.now = jest.spyOn(Date, 'now')
      .mockImplementation(() => fromDate.getTime());

    const rebootTwoDaysEarly = new Date(2000,0,1);
    const machines = [
      { lastBootTime: rebootTwoDaysEarly, name: 'ignored' },
      { lastBootTime: fromDate, name: 'found' }];
```

你可以看到代码中最后一部分是在`afterEach()`中。我们使用另一个名为`jest.restoreAllMocks`的函数，这是 Jest 重置任何被监视的全局状态到其原始实现的方式，没有围绕它的额外模拟层。

注意，尽管我们使用了监视器，但我们并没有验证函数是否实际上被调用。那样做意味着我们将其用作模拟对象，而我们并不是。我们只是将其用作存根。在 Jest 中，我们必须通过“监视器”来存根东西。

我之前列出的所有优缺点在这里仍然适用。当有道理时，我更喜欢使用参数，而不是使用全局函数或变量。

## A.3 使用 Jest 忽略整个模块很简单

在本附录中提到的所有技术中，这是最安全的，因为它不涉及被测试单元的内部工作原理。它只是以广泛的方式忽略事物。

如果我们在测试过程中根本不关心模块，我们只想让它从我们的场景中移除，而不从它那里获取任何模拟数据，那么在测试文件顶部简单调用`jest.mock('module path')`就足够了，无需太多麻烦。

下一个部分有助于你从模拟模块中模拟每个测试的定制数据，这让我们不得不跳过更多的圈子。

## A.4 在每个测试中模拟模块行为

模拟模块基本上意味着模拟一个全局对象，当被测试代码首次使用`import`或`require`时，该对象会被加载。根据我们使用的测试框架，模块可能通过内部缓存或通过标准的 Node.js `require.cache`机制进行缓存。由于这只会发生一次，当我们尝试在同一个文件中对不同测试进行不同行为或数据的模拟时，我们就会遇到一些问题。

为了模拟我们模拟模块的定制行为，我们需要在我们的测试中注意以下事项：从内存中清理所需的模块，替换它，重新导入它，并通过再次导入我们的被测试代码来让被测试代码使用新的模块而不是原始模块。这相当多。我称这种模式为 Clear-Fake-Require-Act (CFRA)：

1.  *清晰*—在每次测试之前，清除测试运行器内存中所有缓存的或必需的模块。

1.  在测试的安排部分：

    1.  *模拟*—模拟将被测试代码中`require`动作调用的模块。

    1.  *导入*—在调用之前导入被测试代码。

1.  *行动*—调用入口点。

如果我们忘记了这些步骤，或者以错误的顺序执行它们，或者在测试的生命周期中不正确的点执行，当我们执行测试时，会出现很多问号，事情似乎没有正确模拟。更糟糕的是，它们*有时*可能工作正确。令人毛骨悚然。

让我们来看一个真实示例，从以下代码开始。

列表 A.4 带有依赖关系的测试代码

```
const { getAllMachines } = require('./my-data-module');     ❶

const daysFrom = (from, to) => {
  const ms = from.getTime() - new Date(to).getTime();
  const diff = (ms / 1000) / 60 / 60 / 24; // secs * min * hrs
  console.log(diff);
  return diff;
};

const findRecentlyRebooted = (maxDays, fromDate) => {
  const machines = getAllMachines();
  return machines.filter(machine => {
    const daysDiff = daysFrom(fromDate, machine.lastBootTime);
    console.log(`${daysDiff} vs ${maxDays}`);
    return daysDiff < maxDays;
  });
};
```

❶ 模拟的依赖项

第一行包含我们在测试中需要断开依赖的函数`getAllMachines`，它是从`my-data-module`解构出来的。因为我们使用的是与其父模块分离的函数，所以我们不能仅仅伪造父模块上的函数并期望测试通过。我们必须在解构过程中获取解构的函数，以便在解构过程中获得伪造的函数，这就是难点所在。

### A.4.1 使用纯`require.cache`伪造模块

在我们使用 Jest 和其他框架伪造整个模块之前，让我们看看我们如何实现这种效果，并探索各种框架中发生的事情。

你可以直接使用`require.cache`而不使用任何框架来使用 CFRA 模式。

列表 A.5 使用`require.cache`伪造

```
const assert = require('assert');
const { check } = require('./custom-test-framework');

const dataModulePath = require.resolve('../my-data-module');

const fakeDataFromModule = fakeData => {
  delete require.cache[dataModulePath];                                ❶
  require.cache[dataModulePath] = {                                    ❷
    id: dataModulePath,
    filename: dataModulePath,
    loaded: true,
    exports: {
      getAllMachines: () => fakeData
    }
  };
  require(dataModulePath);
};

const requireAndCall_findRecentlyRebooted = (maxDays, fromDate) => {
  const { findRecentlyRebooted } = require('../machine-scanner4');     ❸
  return findRecentlyRebooted(maxDays, fromDate);                      ❹
};

check('given 1 of 2 machines under the threshold, it is found', () => {
  const rebootTwoDaysEarly = new Date(2000,0,1);
  const fromDate = new Date(2000,0,3);
 fakeDataFromModule([
 { lastBootTime: rebootTwoDaysEarly, name: 'ignored' },
 { lastBootTime: fromDate, name: 'found' }
 ]);

 const result = requireAndCall_findRecentlyRebooted(1, fromDate);
  assert(result.length === 1);
  assert(result[0].name.includes('found'));
});
```

❶ 清除

❷ 伪造

❸ 需要

❹ 执行

不幸的是，这段代码与 Jest 不兼容，因为 Jest 忽略`require.cache`并内部实现了自己的缓存算法。要执行此测试，请直接通过 Node.js 命令行运行它。你会看到我实现了一个小小的`check()`函数，这样我就不用使用 Jest 的 API。当使用 Jasmine 等框架时，这个测试将正常工作。

记住我们被测试代码中的这一行？

```
const { getAllMachines } = require('./my-data-module'); 
```

我们的测试需要在想要返回伪造值时执行这种解构。这意味着我们需要在我们的测试代码中执行被测试单元的 require 或 import，而不是在文件顶部，而是在测试执行的中间部分。你可以在 A.5 列表的以下部分看到这是如何发生的：

```
const requireAndCall_findRecentlyRebooted = (maxDays, fromDate) => {
  const { findRecentlyRebooted } = require('../machine-scanner4');
  return findRecentlyRebooted(maxDays, fromDate);
};
```

正是因为这种解构代码模式，模块不仅仅是具有属性的普通对象，可以使用正常的猴子补丁技术。我们需要跳过更多的障碍。

让我们将四个 CFRA 步骤映射到 A.5 列表中的代码：

+   *清除*—这是在测试期间调用的`fakeDataFromModule`函数的一部分。

+   *伪造*—我们正在告诉`require.cache`的字典条目返回一个看似代表模块外观的自定义对象，但实际上它有一个自定义实现，返回`fakeData`。

+   *需要*—我们在`requireAndCall_findRecentlyRebooted()`函数中作为部分要求被测试的代码，该函数在测试期间被调用。

+   *ACT*—这是由测试调用的同一个`requireAndCall_findRecentlyRebooted()`函数的一部分。

注意，我们没有在这个测试中使用`beforeEach()`。我们直接从测试中做所有事情，因为每个测试都会从模块中伪造自己的数据。

### A.4.2 使用 Jest 伪造自定义模块数据很复杂

我们已经看到了伪造自定义模块数据的“纯”方法。但如果你使用 Jest，这通常不是你会这样做的方式。Jest 包含几个名称相似且非常接近的函数，它们处理清除和伪造模块，包括`mock`、`doMock`、`genMockFromModule`、`resetAllMocks`、`clearAllMocks`、`restoreAllMocks`、`resetModules`等。太棒了！

我将推荐的代码在可读性和可维护性方面是 Jest 所有 API 中最干净、最简单的。我在 GitHub 仓库 [`github.com/royosherove/aout3-samples`](https://github.com/royosherove/aout3-samples) 以及在 “other-variations” 文件夹 [`mng.bz/Jddo`](http://mng.bz/Jddo) 中涵盖了它的其他变体。

这是使用 Jest 模拟模块的常见模式：

1.  在你的测试中引入你想要模拟的模块。

1.  在测试上方模块中使用 `jest.mock(modulename)` 熄灭。

在每个测试中，告诉 Jest 通过使用 `[modulename].function.mockImplementation()` 或 `mockImplementationOnce()` 来覆盖该模块中某个函数的行为。

以下是一个示例。

列表 A.6 使用 Jest 存根模块

```
const dataModule = require('../my-data-module');
const { findRecentlyRebooted } = require('../machine-scanner4');

const fakeDataFromModule = (fakeData) =>
    dataModule.getAllMachines.mockImplementation(() => fakeData);

jest.mock('../my-data-module');

describe('findRecentlyRebooted', () => {
  beforeEach(jest.resetAllMocks); //<- the cleanest way

  test('given no machines, returns empty results', () => {
    fakeDataFromModule([]);
    const someDate = new Date(2000,0,1);

    const result = findRecentlyRebooted(0, someDate);

    expect(result.length).toBe(0);
  });

  test('given 1 of 2 machines under threshold, it is found', () => {
    const fromDate = new Date(2000,0,3);
    const rebootTwoDaysEarly = new Date(2000,0,1);
 fakeDataFromModule([
 { lastBootTime: rebootTwoDaysEarly, name: 'ignored' },
 { lastBootTime: fromDate, name: 'found' }
 ]);
    const result = findRecentlyRebooted(1, fromDate);
    expect(result.length).toBe(1);
    expect(result[0].name).toContain('found');
  });
```

这是你可以如何使用 Jest 接近 CFRA 的每个部分的示例。

| 清除 | `jest.resetAllMocks` |
| --- | --- |
| 模拟 | `jest.mock()+[fake].mockImplementation()` |
| 需要 | 定期在文件顶部 |
| 行动 | 定期 |

`jest.mock` 和 `jest.resetAllMocks` 方法都是关于模拟模块并将模拟实现重置为空的。注意，在 `resetAllMocks` 之后模块仍然是模拟的。只有其行为被重置为默认的模拟实现。不指定返回值而调用它将产生奇怪的错误。

使用 `FromModule` 方法，我们在每个测试中用返回我们硬编码值的函数替换默认实现。

我们本可以使用 `mockImplementationOnce()` 来进行模拟，而不是使用 `fakeDataFromModule()` 方法，但我发现这可能会创建非常脆弱的测试。使用存根时，我们通常不需要关心它们返回模拟值多少次。如果我们关心它们被调用的次数，我们会将它们用作 *模拟* 对象，这是第四章的主题。

### A.4.3 避免使用 Jest 的手动模拟

Jest 包含了 *手动模拟* 的概念，但如果可能的话，不要使用它们。这项技术要求你在测试中放置一个特殊的 __mocks__ 文件夹，其中包含基于模块名称的硬编码的模拟模块代码。这会起作用，但当你想要控制模拟数据时，维护成本会很高。可读性成本也很高，因为它将滚动疲劳增加到不必要的水平，需要我们在多个文件之间切换以理解测试。你可以在 Jest 文档中了解更多关于手动模拟的信息：[`jestjs.io/docs/en/manual-mocks.html`](https://jestjs.io/docs/en/manual-mocks.html)。

### A.4.4 使用 Sinon.js 存根模块

为了比较，并且让你看到 CFRA 的模式在其他框架中重复出现，这里是一个使用 Sinon.js（一个专门用于创建存根的框架）实现相同测试的示例。

列表 A.7 使用 Sinon.js 存根模块

```
const sinon = require('sinon');
let dataModule;
const fakeDataFromModule = fakeData => {
  sinon.stub(dataModule, 'getAllMachines')
    .returns(fakeData);
};

const resetAndRequireModules = () => {
 jest.resetModules();
 dataModule = require('../my-data-module');
};

const requireAndCall_findRecentlyRebooted = (maxDays, someDate) => {
 const { findRecentlyRebooted } = require('../machine-scanner4');
 return findRecentlyRebooted(maxDays, someDate);
};

describe('4  sinon sandbox findRecentlyRebooted', () => {
  beforeEach(resetAndRequireModules);

  test('given no machines, returns empty results', () => {
    const someDate = new Date('01 01 2000');
    fakeDataFromModule([]);

    const result = requireAndCall_findRecentlyRebooted(2, someDate);

    expect(result.length).toBe(0);
  });
```

让我们用 Sinon 将相关部分映射出来。

| 清除 | 在每个测试之前：`jest.resetModules + 重新要求模拟模块` |
| --- | --- |
| 模拟 | 在每次测试前:`sinon.stub(module,'function')` `.returns(fakeData)` |
| 需求（待测试模块） | 在调用入口点之前 |
| 行动 | 在重新要求待测试模块后 |

### A.4.5 使用 testdouble 模拟模块

Testdouble 是另一个可以轻松用于模拟事物的隔离框架。由于之前的测试中已经完成的重构，代码更改很小。

列表 A.8 使用 testdouble 模拟模块

```
let td;

const resetAndRequireModules = () => {
  jest.resetModules();
  td = require('testdouble');
  require('testdouble-jest')(td, jest);
};
const fakeDataFromModule = fakeData => {
  td.replace('../my-data-module', {
    getAllMachines: () => fakeData
  });
};

const requireAndCall_findRecentlyRebooted = (maxDays, fromDate) => {
  const { findRecentlyRebooted } = require('../machine-scanner4');
  return findRecentlyRebooted(maxDays, fromDate);
};
```

这里列出了使用 testdouble 的重要部分。

| 清晰 | 在每次测试前:`jest.resetModules + require('testdouble');` `require('testdouble-jest')` `(td, jest);` |
| --- | --- |
| 模拟 | 在每次测试前:`Td.replace(module, fake object)` |
| 需求（待测试模块） | 在调用入口点之前 |
| 行动 | 在重新要求待测试模块后 |

测试实现与 Sinon 示例完全相同。我们也在使用`testdouble-jest`，因为它连接到 Jest 模块替换功能。如果我们使用不同的测试框架，则不需要这样做。

这些技术*会*起作用，但我建议除非绝对没有其他方法，否则请远离它们。几乎总是有另一种方法，你可以在第三章中看到其中许多。
