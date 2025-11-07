# 附录 C：Vagrant 入门

Vagrant 是一个用于构建和运行虚拟机的开源软件产品。您可以使用它来模拟生产环境或在各种操作系统上测试您的代码。它也是在一个与您的开发工作站隔离的环境中尝试新软件的好方法。

### 更新

您可以在网上找到这个入门指南的更新和演变版本，网址为[`vagrant-getting-started.the-data-wrangler.com`](http://vagrant-getting-started.the-data-wrangler.com)。

### 安装 VirtualBox

首先，您必须安装 VirtualBox。这是在您的普通计算机（主机）内运行虚拟机的软件。您可以从 VirtualBox 下载页面下载它，网址为[`www.virtualbox.org/wiki/Downloads`](https://www.virtualbox.org/wiki/Downloads)。

下载并安装适合您主机操作系统的软件包。请遵循 VirtualBox 网页上的说明。

### 安装 Vagrant

现在您应该安装 Vagrant。这是 VirtualBox 之上的一个脚本层，允许您通过代码（Ruby 代码）管理虚拟机的设置。您可以从 Vagrant 下载页面下载它，网址为[`www.vagrantup.com/downloads.html`](https://www.vagrantup.com/downloads.html)。

下载并安装适合您主机操作系统的软件包。请遵循 Vagrant 网页上的说明。

### 创建虚拟机

在安装了 VirtualBox 和 Vagrant 之后，您现在可以创建虚拟机了。首先，您必须决定要使用哪个操作系统。如果您已经有一个生产系统，请选择相同的操作系统。如果没有，请选择一个*长期支持*（LTS）版本，该版本将长期得到支持。您可以在网页上搜索操作系统，网址为[`app.vagrantup.com/boxes/search`](https://app.vagrantup.com/boxes/search)。

我喜欢 Ubuntu Linux，所以在这个例子中，我们将使用 Ubuntu 18.04 LTS（Bionic Beaver）。我们将安装的*box*的名称是*ubuntu/bionic64*。**

**在创建 Vagrant 虚拟机之前，打开命令行并创建一个用于存储它的目录。切换到该目录；然后按照以下方式运行`vagrant init`命令：**

```
vagrant init ubuntu/bionic64 
```

这将在当前目录中创建一个基本的*Vagrantfile*。编辑此文件以更改虚拟机的配置和设置。

现在启动您的虚拟机：

```
vagrant up 
```

确保您在包含 Vagrantfile 的同一目录中运行此命令。

这可能需要一些时间，尤其是如果您还没有在本地缓存操作系统的镜像。请给它足够的时间完成。一旦完成，您将有一个全新的 Ubuntu 虚拟机可供使用。

### 在虚拟机上安装软件

在您的虚拟机运行时，您需要在上面安装软件。您可以使用以下命令*shell into*机器：

```
vagrant ssh 
```

要更新虚拟机上的操作系统并安装软件，你将使用特定于该操作系统的命令。在这个例子中，我们使用 Ubuntu，所以接下来的三个命令是针对 Ubuntu 的。如果你选择了不同的操作系统，你需要使用适合它的命令。

使用你的新虚拟机时，首先要做的是更新操作系统。你可以在 Ubuntu 上使用以下命令来完成此操作：

```
sudo apt-get update 
```

现在，你可以安装你需要的任何软件。例如，让我们安装 Git，这样我们就可以克隆我们的代码：

```
sudo apt-get install git 
```

在 Ubuntu 上安装大多数软件都遵循相同的模式。不幸的是，获取尚未由包管理器支持的最新版本的 Node.js 会稍微复杂一些。为此，最好遵循 Node.js 文档中的说明，网址为 [`nodejs.org/en/download/package-manager/`](https://nodejs.org/en/download/package-manager/)。

对于 Ubuntu，它说我们可以使用以下命令安装 Node.js 版本 8：

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs 
```

这比之前的例子复杂一些，但现在我们已经安装了 Node.js 的最新版本。你还可以通过遵循 Node.js 下载页面 [`nodejs.org/en/download/`](https://nodejs.org/en/download/) 上针对你的操作系统的说明来手动安装 Node.js（例如，不使用包管理器）。

### 在虚拟机上运行代码

在你的虚拟机上安装了 Node.js 后，你现在可以运行代码。这可以通过在你的开发工作站上，将你的代码复制到与你的 Vagrantfile 相同的目录中轻松实现。放置在这个目录中的文件将自动在虚拟机中的此目录下可用：

```
/vagrant 
```

如果你有一个 index.js 文件位于你的 Vagrantfile 旁边，当你进入虚拟机时，你可以这样运行它：

```
cd /vagrant
node index.js 
```

开发者将 Vagrantfile 提交到版本控制是一种常见的做法。这样，新开发者（或你在不同的工作站上）只需要克隆仓库，然后运行 `vagrant up` 来构建开发环境。

你甚至可以将自定义设置和代码放入 Vagrantfile 中，以便在虚拟机中安装依赖项并启动应用程序或服务器。你可能记得，在本书的几个章节中，我提供了启动虚拟机、安装数据库并填充数据的 Vagrantfile，创建了一种*即时数据库*。当虚拟机完成启动时，你就有了一个可以工作的系统。

### 关闭虚拟机

完全完成你的虚拟机后，你可以使用以下命令将其销毁：

```
vagrant destroy 
```

如果你只是暂时完成机器并希望以后再次使用它，可以使用以下命令将其挂起：

```
vagrant suspend 
```

可以通过运行 `vagrant resume` 命令在任何时间恢复挂起的机器。

请记住，当您不使用虚拟机时，请销毁或暂停它们；否则，它们将无端消耗您宝贵的系统资源。**
