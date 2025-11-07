# A  其他有用的模块

在这本书中，我介绍了一些第三方 Node 模块，但还有许多第三方模块我没有涉及到。在本附录中，我将为你快速浏览一些在各种情况下我发现有用的模块。这个列表不会详尽无遗，也绝不是全面的，但我希望它能帮助你找到你喜欢的模块。

·   Sequelize 是一个 SQL 的 ORM。在这本书中，我们讨论了 Mongoose，它处理 Mogo 数据库；Sequelize 是 SQL 数据库的 Mongoose。它是一个 ORM，支持迁移，并与各种 SQL 数据库接口。在 http://sequelizejs.com/ 上查看。

·   Lodash 是一个实用库。你可能听说过 Underscore 库，Lodash 与其非常相似。它拥有更高的性能和一些额外的功能。你可以在 [`lodash.com/`](http://lodash.com/) 上了解更多。

·   Async 是另一个实用库，它使得处理各种异步编程模式变得更加容易。更多信息请查看 [`github.com/caolan/async`](https://github.com/caolan/async)。

·   Request 几乎是 Express 的反面。Express 允许你接受传入的 HTTP 请求，而 Request 允许你发起出去的 HTTP 请求。它有一个简单的 API，你可以在 https://www.npmjs.com/package/request 上找到更多信息。

·   Gulp 自称为“流式构建系统”。它是 Grunt 等工具的替代品，允许你编译资源、压缩代码、运行测试等。它使用 Node 的流来提高性能。更多信息请查看 http://gulpjs.com/。

· node-canvas 将 HTML5 Canvas API 端口到 Node，允许你在服务器上绘制图形。你可以在 [`github.com/Automattic/node-canvas`](https://github.com/Automattic/node-canvas) 上查看文档。

·   Sinon.js 在测试中非常有用。有时你想要测试一个函数是否被调用，以及更多。Sinon 允许你确保一个函数以特定的参数或特定的次数被调用。在 http://sinonjs.org/ 上查看。

·   Zombie.js 是一个无头浏览器。还有其他浏览器测试工具，如 Selenium 和 PhantomJS，它们可以启动“真实”的浏览器，你可以控制它们。当你需要与浏览器 100%兼容时，它们是个不错的选择。但它们可能有点慢且难以操控，这就是 Zombie 的作用所在。Zombie 是一个非常快速的无头浏览器，它使得在模拟浏览器中测试你的应用程序变得容易。它的文档位于 http://zombie.labnotes.org/。

· Supererror 覆盖了 console.error 并使其变得更好，它提供了行号、更多信息以及更好的格式化。在 [`github.com/nebulade/supererror`](https://github.com/nebulade/supererror) 上查看。

这是一个简短的列表，但我想要告诉你，我非常喜欢这些模块！要获取更多有用的 Node 资源和模块，你可以查看：

·   《Sindre Sorhus 的“Awesome Node.js”》（在 [`github.com/sindresorhus/awesome-nodejs`](https://github.com/sindresorhus/awesome-nodejs)）

·   Eduardo Rolim 同名列表（在 [`github.com/vndmtrx/awesome-nodejs`](https://github.com/vndmtrx/awesome-nodejs)）

·   Node Weekly（在 [`nodeweekly.com/`](http://nodeweekly.com/)）

·   DailyJS 的 Node Roundup 部分（在 [`dailyjs.com/`](http://dailyjs.com/)）
