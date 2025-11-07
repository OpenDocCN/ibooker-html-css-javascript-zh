# 附录 A. 配置 Babel

Babel 是一个负责将高级 JavaScript 或未来 JavaScript 转换为在您的平台（无论是浏览器还是 Node.js）上标准运行的 JavaScript 版本的 JavaScript 到 JavaScript 转译器。本书介绍了一些仍处于起步阶段的提案。为了使这种新语法能够工作，我们首先必须使用 Babel 将其转换为 Docker 容器内运行的版本的标准 JavaScript（Node.js 14）。您也可以将其转译为您的平台版本。代码遵循的标准（如 ECMAScript 2020）与您的浏览器或服务器支持的标准之间的差距越大，Babel 转译代码时需要做的工作就越多。

要在您的项目中配置 Babel，您必须安装必要的依赖项。本书使用 Babel 7。以下是项目 package.json 的一部分：

```
  "devDependencies": {
    "@babel/cli": "⁷.10.1",
    "@babel/core": "⁷.10.2",
    "@babel/node": "⁷.10.1",
    "@babel/plugin-proposal-class-properties": "⁷.10.1",
    "@babel/plugin-proposal-function-bind": "⁷.10.1",
    "@babel/plugin-proposal-numeric-separator": "⁷.10.1",
    "@babel/plugin-proposal-pipeline-operator": "⁷.10.1",
    "@babel/plugin-proposal-throw-expressions": "⁷.10.1",
    "@babel/preset-env": "⁷.10.2",
    "@babel/preset-flow": "⁷.10.1",
    "@babel/register": "⁷.10.1",
  }
```

在安装必要的依赖项之后，配置 Babel 最简单的方法是通过项目级别的 .babelrc 文件：

```
{
  "presets": [
    [
      "@babel/preset-env",
      {        
        "modules": false,
        "targets": {
          "node": "current"
        },
        "debug": true
      }      
    ]
  ],
  "plugins": [
    "@babel/plugin-proposal-class-properties",
    "@babel/plugin-proposal-numeric-separator",
    "@babel/plugin-proposal-function-bind",
    "@babel/plugin-proposal-throw-expressions",
    ["@babel/plugin-proposal-pipeline-operator", { "proposal": "minimal" }]
  ],
  "sourceMaps": "both",
  "comments": true,
  "highlightCode": true
}
```

当 Babel 配置完成后，您可以通过使用 `babel-cli` 来运行它。例如，要将所有文件转译到 dist 目录，您可以运行

```
babel src --out-dir dist --keep-file-extension --copy-files
```
