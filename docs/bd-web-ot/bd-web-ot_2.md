## 附录 A. Arduino, BeagleBone, Intel Edison, 和物联网

如前所述，尽管我们在这本书中一直以 Raspberry Pi 作为参考设备，但您在这本书中学到的所有概念、架构和模式绝对不局限于 Pi：它们适用于任何嵌入式设备或任何其他事物。如果您决定选择 Pi 以外的设备，这个附录将帮助您设置它并构建必要的代码，以将您选择的设备集成到物联网中。我们不会提供每个平台的详细信息，但会提供三个流行嵌入式系统的所有必要指针：BeagleBone，^([1]) Intel Edison，^([2]) 和 Arduino.^([3]) 对于每一个，我们将查看以下这些点：

> ¹
> 
> [BeagleBoard 官网](http://beagleboard.org/)
> 
> ²
> 
> [Intel Edison 官网](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html)
> 
> ³
> 
> [Arduino 官网](https://www.arduino.cc/)

+   安装软件

+   安装 Node.js

+   GPIO 布局和库

### A.1\. 将 BeagleBone 集成到物联网中

让我们从 Pi 最接近的亲戚开始：BeagleBone。这个设备由 BeagleBoard.org 制造，这是一家美国非营利性组织，提供嵌入式计算中的开源软件和硬件的教育和推广。该基金会与德州仪器等合作伙伴合作，发布易于使用的嵌入式设备，称为 BeagleBones。尽管在这个附录中我们专注于他们的最佳卖家 BeagleBone Black (BBB)，但本节的大部分内容也适用于所有 BeagleBone 型号。

#### A.1.1\. 认识 BeagleBone Black

BeagleBone Black 是 Linux 驱动的嵌入式设备家族的一员，其功能和尺寸与 Raspberry Pi B+类似：

+   ARM Cortex-A8

+   512 MB 的 RAM

+   板载 4 GB 闪存存储

+   板载以太网适配器

+   一个 3D 图形加速器

BeagleBone 因其坚固性和稳定性而闻名，这使得它在生产应用中也是一个极佳的选择。此外，BeagleBone 提供 SD 卡和基于闪存的存储，这使得从原型到实际试验的迁移变得简单（最好不要依赖 SD 卡，因为它们的寿命有限，它们会移动，而且它们通常速度较慢，以及其他问题）。BBB 的价格大约为 50 美元，这比 Pi 略高。如果您想购买一个，您可以在本书的网站上找到推荐卖家的列表.^([4])

> ⁴
> 
> [物联网之书](http://book.webofthings.io)

#### A.1.2\. 准备 BeagleBone Black 用于本书

如我们之前所说，BBB 的硬件和软件与 Pi 类似，因此这里展示的大多数设置步骤都将非常直接。

##### Linux

BeagleBone Black 预装了 Debian Linux，因此您几乎不需要做什么就可以让它启动运行。如果您想尝试冒险，BB 还可以支持其他 Linux 发行版，如 Ubuntu.^([5])

> ⁵
> 
> [`elinux.org/BeagleBoardUbuntu`](http://elinux.org/BeagleBoardUbuntu)

##### SSH

要通过 SSH 连接到您的 BBB，首先遵循在线入门指南，^([6]) 这将教您如何为 BBB 提供电以及如何访问默认的板载网络服务器。是的，BeagleBone 是一个非常友好的 WoT 设备！然后，使用以太网线将 BBB 连接到您的路由器，并按照维基百科中的步骤设置和使用 BBB 的 IP 地址，并通过 SSH 访问它.^([7])

> ⁶
> 
> [`beagleboard.org/getting-started`](http://beagleboard.org/getting-started)
> 
> ⁷
> 
> [`elinux.org/Beagleboard:Terminal_Shells`](http://elinux.org/Beagleboard:Terminal_Shells)

##### Node.js

在这里，BBB 与 WoT 的兼容性很好，因为 Node 是该设备的默认编程语言.^([8]) 如果您想升级 Node 的版本，可以按照 第 4.2.5 节 中列出的步骤进行，但请确保选择适合您的 BBB 处理器（ARMv7 架构）的 Node 版本。BeagleBone 还配备了 Cloud9 IDE，它允许您方便地在板上直接编辑 Node.js 程序.^([9])

> ⁸
> 
> [`beagleboard.org/support/bonescript`](http://beagleboard.org/support/bonescript)
> 
> ⁹
> 
> [`beagleboard.org/Support/bone101/`](http://beagleboard.org/Support/bone101/)

##### WoT 服务器代码

本书中的所有代码示例，包括完整的 WoT 服务器示例，都可以在 BBB 上直接使用，无需任何更改。就像 Pi 一样，BBB 支持 Git，因此您可以从 GitHub 上分叉本书的源代码（见 第七章）。

##### GPIOs

我们用来与 Pi 的 GPIO 交互的 `onoff` 库也与 BBB 兼容，因此您在代码中不需要做太多更改。唯一会不同的是 GPIO 的布局；因此，您需要更改代码中的 GPIO 编号。例如，对于 第四章 中的 blink.js 示例（列表 4.6），您需要将 `led = new Gpio(4, 'out')` 更改为使用有效的 GPIO 引脚，例如引脚 11：`led = new Gpio(11, 'out')`。这也意味着您必须将您的电路连接到 BBB 上的不同 GPIO、GND 或 PWR 引脚，因此请务必仔细研究您的设备布局.^([10])

> ¹⁰
> 
> 更多关于 BBB 引脚布局的详细信息，请参阅此处：[`beagleboard.org/support/bone101/`](http://beagleboard.org/support/bone101/).

### A.2\. 将英特尔爱迪生集成到 WoT 中

与 Pi 和 BeagleBone——以及大多数嵌入式平台——不同，Intel Edison 不是由 ARM 处理器供电，而是由 Intel 处理器供电（惊讶，惊讶！）。它主要是一个 Linux Yocto 设备，但它还有一个运行 Viper OS 的第二个微控制器。Edison 的尺寸不比邮票大多少，大约是 Pi Zero 的一半大小，使其成为本书中介绍的最小设备。尽管如此，它集成了令人印象深刻的功能集：

+   用于 Linux 的 500 MHz Intel Atom 双核 x86 CPU

+   用于 RTOS 的 100 MHz Intel Quark

+   1 GB 的 RAM

+   4 GB 板载闪存存储

+   板载 Wi-Fi a/b/g/n 模块

+   板载蓝牙 4.0 模块

所有这些功能都有一个代价；Edison 及其迷你断路板大约需要 70 美元。如果你想要购买，请查看本书网站上的推荐卖家列表。

#### A.2.1\. 为本书准备 Edison

因为 Edison 也是一个 Linux 设备，为 Web of Things 做准备与 Pi 或 BBB 并没有本质的不同。

##### Linux

Edison 预装了 Yocto Linux 版本，因此可以直接使用。

##### SSH

要通过 SSH 访问你的 Edison，你首先需要通过 USB 建立一个串行连接来配置板载 Wi-Fi。一旦建立了 Wi-Fi 连接，你就可以直接 SSH 到 Edison。这个过程在在线入门指南中有详细说明.^([11])

> ¹¹
> 
> [`software.intel.com/en-us/iot/library/edison-getting-started`](https://software.intel.com/en-us/iot/library/edison-getting-started)

##### Node.js

Edison 喜欢 Node.js，因为它预装了。就像 BeagleBone 一样，Edison 也有自己的 IDE，允许你编写在板上运行的 Node.js 代码。它被称为 Intel XDK IoT Edition，可以在 Mac OS、Linux 和 Windows 上安装.^([12])

> ¹²
> 
> [`software.intel.com/en-us/getting-started-with-the-intel-xdk-iot-edition`](https://software.intel.com/en-us/getting-started-with-the-intel-xdk-iot-edition)

##### WoT 服务器代码

这本书中你编写的完整 WoT 服务器将在 Edison 上运行，除了与 GPIOs 交互的部分；请参见下一项。就像 Pi 一样，Edison 支持 Git，因此你可以从 GitHub 上 fork 本书的代码；请参见第七章。

##### GPIOs

不幸的是，本书中使用`onoff`库访问 GPIO 的代码在 Edison 上无法直接运行。但不用担心；Edison 板也自带了一个名为`MRAA GPIO`的 Node GPIO 访问抽象库。^([13]) 你应该能够将`onoff`替换为 MRAA，并让不同的传感器和执行器正常运行。不过，就像 BBB 一样，Edison 上的 GPIO 布局与 Pi 不同，所以你必须确保连接正确的引脚.^([14])

> ¹³
> 
> [`github.com/intel-iot-devkit/mraa`](https://github.com/intel-iot-devkit/mraa)
> 
> ¹⁴
> 
> Intel Edison 扩展板的引脚布局：[`bit.ly/1Kjc7mj`](http://bit.ly/1Kjc7mj)

### A.3. 集成 Arduino 到 WoT

Arduino 是一个基于易于使用的硬件和软件的开源电子平台。它旨在为任何制作交互式项目的人提供服务，可能是最流行——也是最古老！——的硬件原型平台之一。Arduino 不仅仅是一个，而是有数十个 Arduino 设备，或者如 Arduino 世界中所称的板。价格从 80 美元到仅几美元不等，板从一直以来的最佳畅销品 Arduino Uno15 到美丽而简约的 LilyPad16 不等。

> 15
> 
> [`www.arduino.cc/en/Main/ArduinoBoardUno`](https://www.arduino.cc/en/Main/ArduinoBoardUno)
> 
> 16
> 
> [`www.arduino.cc/en/Main/ArduinoBoardLilyPadUSB`](https://www.arduino.cc/en/Main/ArduinoBoardLilyPadUSB)

与 Pi、BeagleBoard 和 Edison 平台不同，Arduino 板属于 RTOS 设备家族，而不是 Linux。Arduino 板也比我们迄今为止看到的其他平台资源受限得多。结果是，您将无法在 Arduino 板上运行 Node.js（除非是某些特殊板；见下一节）。这也意味着本书中的代码示例无法在 Arduino 上运行，您必须使用基于 C/C++语言的 Arduino 编程语言重写它们。

好消息是，本书中描述的所有概念都可以轻松地移植到 Arduino 平台，因为您的设备 API 将非常相似，如果不是完全相同。如果这是您在物联网世界的第一次游泳，并且直接在 Arduino 上学习这些概念并实现它们将很困难，我们建议您首先获取一个 Pi。但一旦您掌握了其中的精髓，您将能够进一步探索嵌入式系统世界。为此，Arduino 平台是一个很好的起点，尤其是如果您正在寻找低功耗设备。

#### A.3.1. Linux、SSH、Node.js

如前所述，Arduino 板运行在 RTOS 环境中，其中 C 编程语言占主导地位。在 Arduino 板上无法安装 Linux、SSH 或 Node。不过，有一个例外：Arduino Yún 支持 Linux 和 Arduino RTOS 环境，尽管这需要超出本书范围的多个步骤，但在 Yún 上安装 Node 是可能的。17

> 17
> 
> [`blog.arduino.cc/2014/05/06/time-to-expand-your-yun-disk-space-and-install-node-js/`](https://blog.arduino.cc/2014/05/06/time-to-expand-your-yun-disk-space-and-install-node-js/)

如果你不能通过 SSH 编程其他 Arduino 板，你将使用一个名为 Arduino Integrated Development Environment (Arduino IDE)的开发环境。这个 IDE 在你的电脑上运行，并允许你在机器上开发程序，然后再上传到 Arduino 板。如何做到这一点也超出了本书的范围，但你会在网上找到很多可以帮助你的资源。或者，你可能会想获取 Martin Evans 等人所著的*Arduino in Action*（Manning Publications，2013）的副本.^([18])

> ¹⁸
> 
> *Arduino in Action*, Manning Publications: [`www.manning.com/books/arduino-in-action?a_aid=wot`](https://www.manning.com/books/arduino-in-action?a_aid=wot)

##### WoT 服务器代码

尽管你无法重用本书中提供的代码示例，但一些优秀的 Arduino 库可以帮助你实现符合 WoT 的 Arduino。

尤其是使用`Webduino`^([19])库，你可以在 Arduino 板上实现 REST API。`ArdunioWebsocketServer`^([20])库可以用来实现设备 WoT API 的 WebSocket 部分。这应该让你能够理解从第四章到第八章中提出的所有概念。第九章有些复杂，因为 Arduino 平台对 TLS 的支持不是很好。这是因为底层加密算法资源消耗大，导致加密/解密消息需要显著的时间.^([21])

> ¹⁹
> 
> [`github.com/sirleech/Webduino`](https://github.com/sirleech/Webduino)
> 
> ²⁰
> 
> [`github.com/ejeklint/ArduinoWebsocketServer`](https://github.com/ejeklint/ArduinoWebsocketServer)
> 
> ²¹
> 
> 在这里可以找到关于在非常资源受限的设备上安全性的实际考虑的精彩阅读：[`tools.ietf.org/html/draft-aks-lwig-crypto-sensors-00`](https://tools.ietf.org/html/draft-aks-lwig-crypto-sensors-00)。

##### 网关模式

将 Arduino 板集成到物联网中还有另一种方法：使用我们在第七章中探讨的网关模式。例如，你可以在 Arduino 板上使用 MQTT 或 CoAP，然后使用更强大的嵌入式设备，如 Pi，作为网关。如果你想这样做，有一个优秀的`MQTT`^([22)) Arduino 库，还有一个用于 CoAP^([23))的库。

> ²²
> 
> [`github.com/knolleary/pubsubclient`](https://github.com/knolleary/pubsubclient)
> 
> ²³
> 
> [`github.com/1248/microcoap`](https://github.com/1248/microcoap)

##### GPIOs

Arduino 板旨在用于实验大量传感器和执行器，因此它们提供了许多 GPIO。好消息是 GPIO 直接标记在板上，所以不需要解释布局的图片。我们在第四章 chapter 4 中安装的所有传感器和执行器也可以与 Arduino 板一起使用，但再次强调，代码将会有很大不同。尽管描述 Arduino 代码超出了本书的范围，但我们为本书中使用的每个传感器和执行器提供了良好的链接： 

+   LED 灯^([24])

    > ²⁴
    > 
    > [`playground.arduino.cc/Code/LED`](http://playground.arduino.cc/Code/LED)

+   被动红外传感器 (PIR)^([25])

    > ²⁵
    > 
    > [`playground.arduino.cc/Code/PIRsense`](http://playground.arduino.cc/Code/PIRsense)

+   温湿度传感器 (DHT22)^([26])

    > ²⁶
    > 
    > [`playground.arduino.cc/Main/DHTLib`](http://playground.arduino.cc/Main/DHTLib)

### A.4\. 将其他嵌入式系统集成到物联网

实际上，任何嵌入式设备都可以通过遵循物联网架构集成到网络中。这只需要一点搜索或者选择你设备的优秀书籍！如果你还想尝试其他平台，Linux 设备的好起点是嵌入式 Linux Wiki^([27))，而对于 RTOS 设备，Element14 社区^([28))是一个不错的选择。如果你在其他平台上移植这些概念，请通过我们的 GitHub 联系我们，因为我们很乐意了解并从我们的书籍网站上链接到你的项目。

> ²⁷
> 
> [`elinux.org/Main_Page`](http://elinux.org/Main_Page)
> 
> ²⁸
> 
> [`www.element14.com/community`](https://www.element14.com/community)
