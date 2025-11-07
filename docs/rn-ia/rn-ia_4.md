# 附录

安装和运行 React Native

## 为 iOS 设备开发

在撰写本文时，如果您想为 iOS 开发，您必须有一台 Mac，因为 Linux 和 Windows 不支持为 iOS 平台开发。

### 入门

要开始，您必须有一台 Mac，并且需要在上面安装以下内容：

+   Xcode

+   Node.js

+   Watchman

+   React Native 命令行界面

按照以下步骤操作：

1.  安装 Xcode，它可通过 App Store 获取。

1.  React Native 文档以及我都推荐通过 Homebrew 安装 Node 和 Watchman。如果您还没有安装 Homebrew，请访问 [`brew.sh`](http://brew.sh) 并在您的机器上安装它。

1.  打开命令行，使用 Homebrew 安装 Node 和 Watchman：`brew install node`

    ```
    `brew install watchman` 
    ```

1.  一旦安装了 Node.js，请通过命令行运行以下命令来安装 React Native 命令行工具：

    ```
    `npm install –g react-native-cli` 
    ```

如果您遇到权限错误，请尝试使用 sudo 再次运行：

```
sudo npm install -g react-native-cli 
```

### 在 iOS 上测试安装

通过创建新项目来检查 React Native 是否已正确安装。在终端或您选择的命令行中，运行以下命令，将 *MyProjectName* 替换为项目名称：

`react-native init` `*MyProjectName*`

`cd` `*MyProjectName*`

现在您已经创建了项目并切换到了新目录，您可以通过几种不同的方式运行项目：

+   在 MyProjectName 目录内，运行命令 `react-native run-ios``.`。

+   通过打开位于 MyProjectName/ios/MyProjectName.xcodeproj 的 MyProjectName.xcodeproj 文件在 Xcode 中打开项目。

## 为 Android 设备开发

您可以使用 Mac、Linux 或 Windows 环境开发 React Native for Android。

### Mac 和 Android

要在 Mac 上开始，您需要在您的机器上安装以下内容：

+   Node.js

+   React Native 命令行工具

+   Watchman

+   Android Studio

按照以下步骤操作：

1.  React Native 文档以及我都推荐通过 Homebrew 安装 Node 和 Watchman。如果您还没有安装 Homebrew，请访问 [`brew.sh`](http://brew.sh) 并在您的机器上安装它。

1.  打开命令行，使用 Homebrew 安装 Node 和 Watchman：

    ```
    brew install node
    brew install watchman 
    ```

1.  一旦安装了 Node.js，请通过命令行运行以下命令来安装 React Native 命令行工具：

    ```
    npm install -g react-native-cli 
    ```

1.  在 [`developer.android.com/studio/install.html`](https://developer.android.com/studio/install.html) 安装 Android Studio。

当所有内容都安装完毕后，转到 A.2.4 节以创建您的第一个项目。

### Windows 和 Android

以下内容必须在您的机器上安装：

+   Node.js

+   Python2

+   React Native 命令行工具

+   Watchman

+   Android Studio

按照以下步骤操作：

1.  Watchman 在 Windows 上处于 alpha 阶段，但根据我的经验，到目前为止运行良好。要安装 Watchman，请访问 [`github.com/facebook/watchman/issues/19`](https://github.com/facebook/watchman/issues/19) 并通过第一个评论中的链接下载 alpha 版本。

1.  React Native 推荐通过 Chocolatey（Windows 的包管理器）安装 Node.js 和 Python2。为此，请安装 Chocolatey ([`chocolatey.org`](https://chocolatey.org))，以管理员身份打开命令行，然后运行以下命令：

    ```
    choco install nodejs.install
    choco install python2
    Install the React Native command-line interface:
    npm install –g react-native-cli 
    ```

1.  从.下载并安装 Android Studio。

1.  当所有安装完成后，前往 A.2.4 节创建您的第一个项目。

### Linux 和 Android

以下必须在您的机器上安装：

+   Node.js

+   React Native 命令行工具

+   Watchman

+   Android Studio

按照以下步骤操作：

1.  如果您尚未安装 Node.js，请访问[`nodejs.org/en/download/package-manager`](https://nodejs.org/en/download/package-manager)并按照您 Linux 发行版的说明进行操作。

1.  运行以下命令来安装 React Native 命令行工具：

    ```
    npm install -g react-native-cli 
    ```

1.  从[`developer.android.com/studio/install.html`](https://developer.android.com/studio/install.html)下载并安装 Android Studio。

1.  从.下载并安装 Watchman。

一切安装完成后，继续到下一节创建您的第一个项目。

### 创建新项目（Mac/Windows/Linux）

一旦您的开发环境设置完成并且安装了 react-native-cli，您就可以从命令行创建新的 React Native 项目。导航到您想要创建项目的文件夹，并输入以下命令，将*MyProjectName*替换为项目名称：

```
react-native init `*MyProjectName*` 
```

### 运行项目（Mac/Windows/Linux）

要运行 React Native 项目，请在命令行中切换到项目目录，并运行以下 Android 命令：

```
react-native run-android 
```
