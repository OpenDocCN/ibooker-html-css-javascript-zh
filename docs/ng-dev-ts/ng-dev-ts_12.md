## 第十三章 使用 WebSocket 协议与服务器交互

*本章涵盖*

+   实现服务器数据推送到 Angular 客户端

+   从服务器向多个客户端广播数据

+   将 ngAuction 拆分为两个项目

+   在 ngAuction 中实现竞标

WebSocket 是一种由所有现代网络浏览器支持的低开销二进制协议（参见 [`en.wikipedia.org/wiki/WebSocket`](https://en.wikipedia.org/wiki/WebSocket)）。它允许浏览器和 Web 服务器之间双向的消息流式传输文本和二进制数据。与 HTTP 不同，WebSocket 不是一个基于请求/响应的协议，服务器应用程序和客户端应用程序可以在数据可用时立即向对方发起数据推送，实现实时通信。这使得 WebSocket 协议非常适合以下类型的应用程序：

+   实时交易/拍卖/体育通知

+   通过网络控制医疗设备

+   聊天应用程序

+   多玩家在线游戏

+   社交流中的实时更新

+   实时图表

所有这些应用程序都有一个共同点：存在一个服务器（或设备），可能需要立即向用户发送通知，因为某个重要事件发生在其他地方。这与用户决定向服务器发送请求以获取新数据的使用案例不同。例如，如果股票交易在证券交易所发生，通知必须立即发送给所有用户。

另一个例子是在线拍卖。如果用户 Joe 正在考虑对某个产品进行竞标，而用户 Mary（位于 1,000 英里之外）决定提高对该产品的出价，您最好立即将通知推送给 Joe，而不是等到 Joe 刷新窗口。

我们将从这个章节开始，简要比较 HTTP 和 WebSocket 协议，然后向您展示如何使用 Node 服务器将数据推送到普通网页和 Angular 应用程序。

在实践部分，您将继续在 ngAuction 上工作。您将从将 ngAuction 拆分为两个项目：客户端和服务器开始。服务器应用程序将启动两个服务器：HTTP 服务器将提供数据，WebSocket 服务器可以接收用户出价并推送实时出价通知，模拟多个用户可以对拍卖产品进行竞价的场景。Angular 客户端与这两个服务器进行交互。

### 13.1\. 比较 HTTP 和 WebSocket

基于请求的 HTTP 协议，客户端通过连接发送请求并等待响应返回，如图 13.1 所示。请求和响应都使用相同的浏览器-服务器连接。首先，请求发送出去，然后响应通过相同的“线路”返回。想象一下，河上的一座狭窄桥梁，两边的汽车必须轮流过桥。在 Web 领域，这种类型的通信被称为*半双工*。

##### 图 13.1\. 半双工通信

![](img/13fig01.jpg)

WebSocket 协议允许数据在同一连接上同时双向传输（*全双工*），如图 13.2 所示，任何一方都可以启动数据交换。这就像是一条双车道道路。另一个类比是电话通话，两个通话者可以同时说话并被听到。WebSocket 连接保持活跃，这有额外的益处：服务器和客户端之间交互的低延迟。

##### 图 13.2\. 全双工通信

![图片 13.2](img/13fig02.jpg)

一个典型的 HTTP 请求/响应会在应用数据上添加几百字节（HTTP 头部）。假设你想编写一个每秒报告最新股票价格的 Web 应用程序。使用 HTTP，这样的应用程序需要发送一个 HTTP 请求（大约 300 字节）并接收一个股票价格，该价格会附带额外的 300 字节的 HTTP 响应对象。

使用 WebSocket，开销低至几个字节。此外，无需每秒都发送请求以获取新的价格报价——这种股票可能一段时间内不会交易。只有当股票价格发生变化时，服务器才会将新的值推送到客户端。注意以下观察结果（见 goo.gl/zjj7Es）：

> *将千字节的数据减少到 2 字节不仅仅是“稍微节省一些字节”，将延迟从 150 毫秒（TCP 往返设置连接加上消息的包）减少到 50 毫秒（仅仅是消息的包）则远远超出了边际。实际上，这两个因素单独就足以使 WebSocket 对 Google 来说变得非常有趣。*
> 
> *伊恩·希克斯*

| |
| --- |

##### 备注

尽管大多数浏览器支持二进制协议 HTTP/2（见 [`http2.github.io`](https://http2.github.io)）——它比 HTTP 更高效，并且还允许服务器推送数据——但它并不是 WebSocket 协议的替代品。WebSocket 协议提供了一个 API，允许将 *数据* 推送到浏览器中运行的客户端应用程序，而 HTTP/2 推送 *静态资源* 到浏览器，主要用于更快的应用程序交付。

| |
| --- |

每个浏览器都支持一个 `WebSocket` 对象，用于创建和管理到服务器的套接字连接（见 [`mng.bz/1j4g`](http://mng.bz/1j4g)）。最初，浏览器会与服务器建立一个常规的 HTTP 连接，但随后你的应用程序请求升级连接，指定支持 WebSocket 连接的服务器 URL。之后，通信成功，无需 HTTP。WebSocket 端点的 URL 以 ws 开头而不是 http——例如，ws://localhost:8085。

WebSocket 协议基于事件和回调。例如，当您的浏览器应用与服务器建立连接时，它会接收到`connection`事件，并且您的应用调用回调来处理此事件。为了处理服务器可能通过此连接发送的数据，请期待提供相应回调的`message`事件。如果连接关闭，`close`事件会被触发，以便您的应用可以相应地做出反应。在发生错误的情况下，`WebSocket`对象会接收到`error`事件。

在服务器端，您将不得不处理类似的事件。它们的名称可能取决于您在服务器上使用的 WebSocket 软件。让我们编写一些代码，其中 Node 服务器将通过 WebSocket 向 Angular 应用发送数据。

### 13.2. 从 Node 服务器向普通客户端推送数据

大多数服务器端平台（Java、.NET、Python 等）都支持 WebSocket。在第十二章中，您开始使用 Node 服务器进行工作，您将继续使用 Node 来实现您的 WebSocket 服务器。在本节中，您将实现一个特定的用例：客户端连接到 socket 后，服务器立即向浏览器客户端推送数据。由于任一方都可以开始通过 WebSocket 连接发送数据，您会看到 WebSocket 并不涉及请求/响应通信。您的简单客户端不需要发送数据请求——服务器将发起通信。

几个 Node 包实现了 WebSocket 协议，您将使用名为`ws`的 npm 包([www.npmjs.com/package/ws](http://www.npmjs.com/package/ws))。您可以在项目目录中输入以下命令来安装此包及其类型定义：

```
npm install ws
npm install @types/ws --save-dev
```

需要类型定义，这样 TypeScript 编译器就不会在您使用`ws`包的 API 时发出警告。此外，此文件便于查看可用的 API 和类型。

| |
| --- |

##### 注意

本章附带代码中有一个名为`server`的目录，其中包含名为`package.json`的文件，列出了`ws`和`@types/ws`作为依赖项。您只需运行`npm install`命令。源代码可以在[`github.com/Farata/angulartypescript`](https://github.com/Farata/angulartypescript)和[www.manning.com/books/angular-development-with-typescript-second-edition](http://www.manning.com/books/angular-development-with-typescript-second-edition)找到。

| |
| --- |

您的第一个 WebSocket 服务器将会非常简单：一旦建立了 socket 连接，它就会将文本“此消息由 WebSocket 服务器推送”推送到 HTML/JavaScript 客户端（不使用 Angular）。我们故意不希望客户端向服务器发送任何请求，这样我们可以说明 socket 是双向的，服务器可以在没有任何请求仪式的情况下推送数据。

你的应用程序创建了两个服务器。HTTP 服务器在端口 8000 上运行，负责向浏览器发送 HTML 页面。当此页面加载时，它会立即连接到运行在端口 8085 上的 WebSocket 服务器。此服务器将在连接建立后立即推送带有问候的消息。此应用程序的代码位于 server/simple-websocket-server.ts 文件中，如下所示。

##### 列表 13.1\. simple-websocket-server.ts

```
import * as express from "express";
import * as path from "path";
import {Server} from "ws";                                                    *1*

const app = express();

// HTTP Server
app.get('/', (req, res) => res.sendFile(path.join(__dirname,
                             '../simple-websocket-client.html')));            *2*

const httpServer = app.listen(8000, "localhost", () => {                      *3*
     console.log(`HTTP server is listening on localhost:8000`);
});

// WebSocket Server
const wsServer = new Server({port: 8085});                                    *4*
 console.log('WebSocket server is listening on localhost:8085');

wsServer.on('connection',                                                     *5*
          wsClient => {
             wsClient.send('This message was pushed by the WebSocket server');*6*

             wsClient.onerror = (error) =>                                    *7*
                  console.log(`The server received: ${error['code']}`);
         }
);
```

+   ***1* 你将使用 ws 模块中的 Server 来实例化 WebSocket 服务器。**

+   ***2* 当 HTTP 客户端通过根路径连接时，HTTP 服务器将发送回此 HTML 文件。**

+   ***3* 在端口 8000 上启动 HTTP 服务器**

+   ***4* 在端口 8085 上启动 WebSocket 服务器**

+   ***5* 监听来自客户端的连接事件**

+   ***6* 将消息推送到新连接的客户端**

+   ***7* 处理连接错误**

一旦任何客户端通过端口 8085 连接到你的 WebSocket 服务器，服务器上就会触发连接事件，并且它还会收到代表此特定客户端连接的对象引用。使用 `send()` 方法，服务器向此客户端发送问候。如果另一个客户端连接到同一端口的套接字，它也会收到相同的问候。


##### 注意

一旦新客户端连接到服务器，此连接的引用就会被添加到 `wsServer.clients` 数组中，这样你就可以在需要时向所有已连接的客户端广播消息：`wsServer.clients.forEach (client => client.send('...'));`。


在你的应用程序中，HTTP 和 WebSocket 服务器运行在不同的端口上，但你可以通过将新创建的 `httpServer` 实例提供给 WebSocket 服务器的构造函数来重用相同的端口，如下所示。

##### 列表 13.2\. 两个服务器重用相同的端口

```
const httpServer = app.listen(8000, "localhost", () => {...});    *1*

const wsServer = new Server({server: httpServer});                *2*
```

+   ***1* 在端口 8000 上创建 HTTP 服务器实例**

+   ***2* 基于现有的 HTTP 服务器创建 WebSocket 服务器实例**


##### 注意

在动手实践部分，你将重用端口 8000 用于 HTTP 和 WebSocket 通信（请参阅 ng-auction/server/ws-auction.ts 文件）。


服务器/简单-websocket-client.html 文件的内容显示在下一条列表中。这是一个不使用任何框架的纯 HTML/JavaScript 客户端。

##### 列表 13.3\. simple-websocket-client.html

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
<span id="messageGoesHere"></span>

<script type="text/javascript">
    var ws = new WebSocket("ws://localhost:8085");                 *1*

    ws.onmessage = function(event) {                               *2*
         var mySpan = document.getElementById("messageGoesHere");
        mySpan.innerHTML = event.data;
    };

    ws.onerror = function(event) {                                 *3*
         console.log(`Error ${event}`);
    }
</script>
</body>
</html>
```

+   ***1* 建立套接字连接**

+   ***2* 当消息从套接字到达时，在 <span> 元素中显示其内容**

+   ***3* 如果发生错误，浏览器将在控制台记录错误消息。**

当浏览器下载此文件时，其脚本会连接到你的 WebSocket 服务器，地址为 ws://localhost:8085。在此点，服务器将协议从 HTTP 升级到 WebSocket。请注意，协议是 `ws` 而不是 `http`。对于安全的套接字连接，请使用 `wss` 协议。

要查看此示例的实际效果，请在服务器目录中运行 `npm install`，通过运行 `tsc` 命令编译代码，然后按照以下方式启动服务器：

```
node build/simple-websocket-server
```

你将在控制台看到以下消息：

```
WebSocket server is listening on port 8085
HTTP server is listening on 8000
```

打开 Chrome 浏览器并打开其开发者工具，访问 http://localhost:8000。你将看到消息，如图 13.3 所示的左侧。在右侧的“网络”标签页中，你可以看到对本地服务器的两个请求。第一个请求加载了 simple-websocket-client.html 文件，第二个请求是对服务器上 8085 端口上打开的 WebSocket 的请求。

##### 图 13.3\. 从套接字获取消息

![图片 13.3](img/13fig03_alt.jpg)

在这个例子中，仅使用 HTTP 协议最初加载 HTML 文件。然后客户端请求将协议升级到 WebSocket（状态码 101），从那时起，这个网页将不再使用 HTTP。你可以使用 Chrome 开发者工具中的帧标签页监控通过套接字传输的数据。在这个演示中，你使用浏览器原生的 `WebSocket` 对象编写了一个 WebSocket 客户端，但一个 Angular 应用如何通过 WebSocket 消费或发送消息到服务器呢？

### 13.3\. 在 Angular 客户端中使用 WebSocket

在 Angular 中，你通常将所有与服务器的通信包装到可注入的服务中。在 第十二章 中的一些应用程序中，你使用 `HttpClient` 做过这件事，你也将使用 `WebSocket` 对象。但这两个对象在 `HttpClient` 已经是一个 Angular 可注入服务，你会在应用程序的服务类中 *注入* 它，而 `WebSocket` 是一个原生浏览器对象，你将在服务类内部 *创建* 它。

`HttpClient` 和 `WebSocket` 之间还有一个主要区别。如果使用 `HttpClient` 发送 HTTP 请求会返回一个包含单个值的可观察对象，那么 `WebSocket` 对象提供了一个易于转换为多个值（如股票价格或产品竞标）的可观察流 API。

将 WebSocket 视为一个可以发出值的 数据生产者，而 `Observable` 对象可以将这些值传递给订阅者（例如 Angular 组件）。在 Angular 中，你可以手动创建一个从 WebSocket 连接生成可观察流的服务，或者使用 RxJS 提供的 `WebSocketSubject`。在本章中，你将看到在 Angular 客户端处理 WebSocket 消息的两种方式。

但首先，让我们看看如何将任何发出值的 `Observable` 包装到 Angular 服务中。

#### 13.3.1\. 将可观察流包装到服务中

在本节中，你将创建一个可观察服务，该服务会发出硬编码的值，而不连接到任何服务器。在 附录 D 的 13.5 节中，我们解释了如何使用 `Observable.create()` 方法，并提供一个观察者作为参数。如果你还没有阅读 附录 D，请现在阅读。

以下列表创建了一个服务，该服务有一个方法，该方法接受一个观察者作为参数，并每秒发出当前时间。

##### 列表 13.4\. observable.service.ts

```
import {Observable} from 'rxjs';

export class ObservableService {

  createObservableService(): Observable<Date> {       *1*

      return new Observable(                          *2*
           observer => {                              *3*
               setInterval(() =>
                  observer.next(new Date())           *4*
               , 1000);
          }
      );
  }
}
```

+   ***1* 返回日期的可观察流**

+   ***2* 创建可观察对象**

+   ***3* 提供观察者**

+   ***4* 每秒发射新的日期**

在此服务中，你创建一个`Observable`对象的实例，假设订阅者将提供一个知道如何处理发射数据的`Observer`。每当可观察对象在观察者上调用`next(new Date())`方法时，订阅者将接收到当前的日期和时间。你的数据流永远不会抛出错误，也永远不会完成。

你将`ObservableService`注入到`AppComponent`中，它调用`createObservableService()`方法并订阅其值流，创建一个知道如何处理数据的观察者。观察者只是将接收到的时间分配给渲染 UI 上时间的`currentTime`变量，如下列所示。

##### 列表 13.5\. observableservice/app.component.ts

```
import {Component} from "@angular/core";
import {ObservableService} from "./observable.service";

@Component({
  selector: 'app-root',
  providers: [ObservableService],
  template: `<h1>Custom observable service</h1>
       Current time: {{currentTime | date: 'mediumTime'}}       *1*
   `})
export class AppComponent {

  currentTime: Date;

  constructor(private observableService: ObservableService) {   *2*

    this.observableService.createObservableService()            *3*
       .subscribe(data => this.currentTime = data);             *4*
   }
}
```

+   ***1* 使用日期管道显示时间**

+   ***2* 注入封装可观察对象的服务**

+   ***3* 创建可观察对象并开始发射日期**

+   ***4* 订阅日期流**

此应用程序不使用任何服务器，你可以在以下位置看到它的运行情况。在客户端目录中运行以下命令来运行它（在`npm install`之后）：

```
ng serve --app observableservice -o
```

在浏览器窗口中，当前时间将每秒更新一次。你在这里使用`DatePipe`，格式为`'mediumTime'`，它只显示小时、分钟和秒（所有日期格式都在 Angular `DatePipe`文档中描述，见[`mng.bz/78lD`](http://mng.bz/78lD))。

此简单示例演示了创建一个可注入服务的基本技术，该服务封装了一个可观察流，以便组件或用户服务可以订阅它。在这种情况下，你使用`setInterval()`，但你可以用任何特定于应用程序的代码替换它，该代码生成一个或多个值并将它们作为流发射出来。

不要忘记错误处理以及在必要时完成流。以下列表显示了一个向观察者发送一个元素的可观察对象，可能会抛出错误，并通知观察者流已完成的示例。

##### 列表 13.6\. 发送错误和完成事件

```
return new Observable(
    observer => {
      try {
        observer.next('Hello from observable');       *1*

        // throw("Got an error");                     *2*
         // some other code can be here
      } catch(err) {
         observer.error(err);                         *3*
       } finally {
         observer.complete();                         *4*
       }
    }
);
```

+   ***1* 将文本值发送给观察者**

+   ***2* 模拟错误情况**

+   ***3* 将错误发送给观察者**

+   ***4* 总是让观察者知道数据流已结束。**

如果你取消注释带有`throw`的行，前面的程序将跳过“某些其他代码”并继续在`catch`部分，在那里你调用`observer.error()`。这将导致如果有的话，在订阅者上调用错误处理程序。

你的可观察流的数据生产者是时间生成器，但它可以是生成一些有用值的 WebSocket 服务器。让我们创建一个与 WebSocket 服务器通信的 Angular 服务。

#### 13.3.2\. 角度与 WebSocket 服务器通信

在实际操作部分，您将实现一个 Angular 客户端通过 WebSockets 与服务器通信的实际情况。这就是 ngAuction 的用户如何进行竞标并接收其他用户竞标通知的方式。

在本节中，我们将向您展示一种非常基本的方法，将 `WebSocket` 包装到 Angular 客户端中。这将是一个相当简单的 `WebSocket` 对象包装器，但在实际操作部分，您将使用一个更健壮的 `WebSocketSubject`，它随 RxJS 一起提供。

您的下一个 Angular 应用程序将包括一个与 Node WebSocket 服务器交互的服务。服务器端层可以使用支持 WebSockets 的任何技术实现。图 13.4 展示了此类应用程序的架构（想象一下，竞标消息在客户端和服务器之间通过套接字连接进行交换）。

##### 图 13.4\. Angular 通过套接字与服务器交互

![](img/13fig04_alt.jpg)

列表 13.7 中的代码将浏览器的 `WebSocket` 对象包装成一个可观察的流。此服务根据提供的 URL 创建一个连接到 WebSocket 服务器的 `WebSocket` 对象实例，客户端实例处理从服务器接收到的消息。

您的 `WebSocketService` 还有一个 `sendMessage()` 方法，因此客户端也可以向服务器发送消息。在发送消息之前，该服务会检查连接是否仍然打开（`WebSocket.readyState === 1`），如下面的列表所示。

##### 列表 13.7\. wsservice/websocket.service.ts

```
import {Observable } from 'rxjs';

export class WebSocketService {

  ws: WebSocket;
  socketIsOpen = 1;                                                *1*

  createObservableSocket(url: string): Observable<any> {           *2*
     this.ws = new WebSocket(url);                                 *3*

    return new Observable(                                         *4*
       observer => {

        this.ws.onmessage = (event) =>
          observer.next(event.data);                               *5*

        this.ws.onerror = (event) => observer.error(event);        *6*

        this.ws.onclose = (event) => observer.complete();          *7*

        return () =>
            this.ws.close(1000, "The user disconnected");          *8*
       }
    );
  }

  sendMessage(message: string): string {
    if (this.ws.readyState === this.socketIsOpen) {                *9*
       this.ws.send(message);                                      *10*
       return `Sent to server ${message}`;
    } else {
      return 'Message was not sent - the socket is closed';        *11*
     }
  }
}
```

+   ***1* WebSocket 已打开。**

+   ***2* 此方法会发出从指定 URL 收到的消息。**

+   ***3* 连接到 WebSocket 服务器**

+   ***4* 创建一个 Observable 对象**

+   ***5* 将从服务器接收到的消息发送给订阅者**

+   ***6* 将从服务器接收到的错误发送给订阅者**

+   ***7* 如果服务器关闭套接字，则通知订阅者**

+   ***8* 返回一个回调，以便调用者可以取消订阅**

+   ***9* 检查连接是否打开**

+   ***10* 将消息发送到服务器**

+   ***11* 通知调用者连接已关闭**

注意，您的观察者返回一个回调，因此如果调用者调用 `unsubscribe()` 方法，则此回调将被调用。它将关闭连接，发送状态码 1000 和解释关闭原因的消息。您可以在 [`mng.bz/5V07`](http://mng.bz/5V07) 查看所有允许的关闭连接的状态码。

现在，让我们编写 `AppComponent`，它订阅 `WebSocketService`，该服务注入到 图 13.4 中所示的 `AppComponent` 中。此组件，如下面的列表所示，当用户点击发送消息到服务器按钮时，也可以向服务器发送消息。

##### 列表 13.8\. wsservice/app.component.ts

```
import {Component, OnDestroy} from "@angular/core";
import {WebSocketService} from "./websocket.service";
import {Subscription} from "rxjs";

@Component({
  selector: 'app-root',
  providers: [ WebSocketService ],
  template: `<h1>Angular client for a WebSocket server</h1>
  {{messageFromServer}}<br>
  <button (click)="sendMessageToServer()">Send Message to Server</button>
  <button (click)="closeSocket()">Disconnect</button>
  <div>{{status}}</div>
  `})
export class AppComponent implements OnDestroy {

  messageFromServer: string;
  wsSubscription: Subscription;                                         *1*
   status;

  constructor(private wsService: WebSocketService) {                    *2*

    this.wsSubscription =
      this.wsService.createObservableSocket("ws://localhost:8085")      *3*
       .subscribe(
        data => this.messageFromServer = data,                          *4*
         err => console.log( 'err'),
        () =>  console.log( 'The observable stream is complete')
      );
  }

  sendMessageToServer(){
    this.status = this.wsService.sendMessage("Hello from client");      *5*
   }

  closeSocket(){
    this.wsSubscription.unsubscribe();                                  *6*
     this.status = 'The socket is closed';
  }

  ngOnDestroy() {
    this.closeSocket();
  }
}
```

+   ***1* 此属性将保留订阅的引用。**

+   ***2* 注入服务**

+   ***3* 连接到服务器**

+   ***4* 处理从服务器接收到的数据**

+   ***5* 向服务器发送消息**

+   ***6* 关闭 WebSocket 连接**

注意，您将订阅的引用存储在 `wsSubscription` 属性中，当用户点击“断开连接”按钮时，此组件从可观察对象中取消订阅。这会调用观察者中定义的回调，关闭 WebSocket 连接。

客户端已准备好。现在我们将向您展示与该客户端通信的服务器代码。在连接事件上调用的回调函数向客户端发送问候，并将两个更多的事件处理函数添加到表示此特定客户端的对象中。

一个函数处理来自客户端的消息，另一个处理错误（您将记录错误代码）。此服务器在 two-way-websocket-server.ts 文件中实现。

##### 列表 13.9\. server/two-way-websocket-server.ts

```
import {Server} from "ws";

let wsServer = new Server({port:8085});                                  *1*

console.log('WebSocket server is listening on port 8085');

wsServer.on('connection',                                                *2*
    websocket => {

     websocket.send('Hello from the two-way WebSocket server');          *3*

     websocket.onmessage = (message) =>                                  *4*
              console.log(`The server received: ${message['data']}`);

     websocket.onerror = (error) =>                                      *5*
          console.log(`The server received: ${error['code']}`);

     websocket.onclose = (why) =>                                        *6*
          console.log(`The server received: ${why.code} ${why.reason}`);
   });
```

+   ***1* 启动 WebSocket 服务器**

+   ***2* 新客户端已连接**

+   ***3* 向新连接的客户端问候**

+   ***4* 监听来自此客户端的消息**

+   ***5* 记录此连接的错误，如果有**

+   ***6* 客户端已断开连接，因此您记录了原因。**

要查看此应用程序的实际运行情况，请从服务器目录运行以下命令以启动服务器：

```
node build/two-way-websocket-server
```

然后按照以下步骤从客户端目录构建并启动 Angular 应用程序：

```
ng serve --app wsservice
```

要模拟多个客户端连接到同一 WebSocket 服务器的场景，请同时在 http://localhost:4200 打开两个浏览器。每个应用程序都将收到服务器的问候，您可以通过点击“发送消息到服务器”按钮向服务器发送消息。

在按钮点击一次后（在 Chrome 开发者工具中，网络下打开了 WS 和 Frames 选项卡），我们拍摄了 图 13.5 中的截图。在右侧，您可以看到从服务器到达的问候消息和客户端发送到服务器的消息。

##### 图 13.5\. 从 Node 获取消息的 Angular

![图片 13.5](img/13fig05_alt.jpg)

图 13.6 展示了客户端点击“发送消息到服务器”按钮后，断开连接，然后再次发送消息到服务器的截图。

##### 图 13.6\. 发送、断开连接，然后再次发送

![图片 13.6](img/13fig06_alt.jpg)


##### 注意

浏览器不对 WebSocket 连接执行同源策略。这就是为什么您能够交换来自端口 4200 的客户端和运行在端口 8085 上的服务器之间的数据。请参阅您使用的任何服务器端技术文档，以了解 WebSocket 可用的保护措施。

|  |

**将 WebSocket 与服务器端消息系统集成**

假设你的服务器使用消息系统；让我们以 ActiveMQ 为例。假设你希望启用你的 JavaScript 客户端通过 WebSockets 与 ActiveMQ 交换数据。如果你决定从头开始编写这样的数据交换程序，你需要想出一个方法来通知服务器端点，客户端发送的数据应该被重定向到具有特定名称的 ActiveMQ 队列。然后服务器端代码需要格式化客户端的消息，使其符合 ActiveMQ 的内部协议。此外，服务器端应用程序需要跟踪所有已连接的客户端，并可能实现一些心跳来监控套接字连接的健康状况。这需要大量的编码工作。

好消息是 WebSockets 可以使用子协议与服务器端消息系统集成。例如，服务器端代码可以将 WebSocket 端点映射到 ActiveMQ 中的现有队列。这样，当服务器软件将消息放入队列时，它会自动推送到客户端。同样，当客户端向 WebSocket 端点发送消息时，它会被放置在服务器上的队列中。实现心跳功能只需提供一个配置选项。

STOMP 是用于通过 WebSockets 发送文本消息的流行子协议之一（见 [`mng.bz/PPsy`](http://mng.bz/PPsy)）。它描述了一个客户端消息代理，它与服务器端对等体进行通信。对于客户端 STOMP 支持，我们使用 ng2-stompjs，可在 [`mng.bz/KdIM`](http://mng.bz/KdIM) 获取。

服务器端管理员应为其消息服务器（ActiveMQ 具有本机 STOMP 支持）安装 STOMP 连接器。在这种配置中，客户端-服务器通信更加健壮，并且需要在应用程序级别上编写更少的代码。


在第十二章中，你学习了如何通过 HTTP 与 Web 服务器进行通信。在本章中，我们介绍了 WebSocket 协议。ngAuction 的下一个版本将使用这两种通信协议，但首先让我们看看本章涵盖的材料如何应用于你即将实施的 ngAuction 的新功能。

WebSocket 协议不是基于请求/响应模型，WebSocket 服务器可以在没有任何额外仪式的情况下主动与客户端通信。这对于 ngAuction 来说是一个非常有价值的特性，因为服务器首先知道任何用户在多用户应用程序中对每个拍卖产品的出价。由于服务器不需要等待客户端的数据请求，它可以实时将新出的价推送到所有连接到该 WebSocket 服务器的用户。这意味着服务器可以实时将最新的出价推送给所有用户。

### 13.4. 实践：支持 WebSockets 的 Node 服务器

在本节中，我们将回顾本章附带的重构版本的 ngAuction。在真实拍卖中，多个用户可以对产品进行竞标。当服务器收到用户的竞标时，竞标服务器应向所有观看选定产品的用户广播最新的竞标。本版本的 ngAuction 完成了以下主要任务：

+   将 ngAuction 分为两个独立的项目，客户端和服务器，并将产品数据和图像存储在服务器上。

+   修改客户端，使其使用 `HttpClient` 服务向服务器发送请求以获取产品数据。

+   在服务器端，实现 HTTP 和 WebSocket 服务器。HTTP 服务器将提供产品数据。

+   WebSocket 服务器将接受用户对选定产品的竞标，所有其他用户都可以看到服务器推送的最新竞标。

图 13.7 展示了渲染后的 `ProductDetailComponent`，其中包含一个按钮，允许用户以 $5 的增量进行竞标。如果用户点击此按钮一次，价格将在他们的 UI 上变为 $75，以及所有其他打开相同产品详细视图的用户。服务器将通过 WebSocket 连接向所有查看此产品的用户广播最新的竞标金额。

##### 图 13.7\. 带有竞标按钮的 `ProductDetailComponent`

![](img/13fig07_alt.jpg)

要实现此功能，您需要在服务器上添加 WebSocket 支持，并在客户端创建一个新的 `BidService`。图 13.8 展示了在本版本 ngAuction 中涉及客户端-服务器通信的主要参与者。

##### 图 13.8\. ngAuction 中的客户端-服务器通信

![](img/13fig08_alt.jpg)

图 13.8 中的 *DI* 代表 *依赖注入*。Angular 将 `HttpClient` 服务注入到 `ProductService` 中，后者反过来被注入到三个组件中：`CategoriesComponent`、`SearchComponent` 和 `ProductComponent`。`ProductService` 负责与服务器所有基于 HTTP 的通信。

`BidService` 包装了与服务器所有基于 WebSocket 的通信。它被注入到 `ProductDetailComponent` 中。当用户打开产品详细视图时，如果有新的竞标，将显示新的竞标。当用户放置新的竞标时，`BidService` 将消息推送到服务器。当 WebSocket 服务器推送新的竞标时，`BidService` 接收竞标，并 `ProductDetailComponent` 渲染它。

图 13.8 展示了两个额外的可注入值：`API_BASE_URL` 和 `WS_URL`。前者将包含 HTTP 服务器的 URL，后者，WebSocket 服务器的 URL。要注入这些值，您将使用 `InjectionToken`。这两个 URL 都是可配置的，并且它们的值存储在 Angular 项目的 environments/environment.ts 和 environments/environment.prod.ts 文件中。

environment.ts 文件用于开发模式，如下所示。

##### 列表 13.10\. environment.ts

```
export const environment = {
  production: false,
  apiBaseUrl: 'http://localhost:9090/api',
  wsUrl: 'ws://localhost:9090'
};
```

environment.prod.ts 文件用于生产模式，由于 Angular 应用预计将部署在提供数据的服务器上，因此不需要指定 HTTP 通信的完整 URL，如下面的列表所示。

##### 列表 13.11\. environment.prod.ts

```
export const environment = {
  production: true,
  apiBaseUrl: '/api',
  wsUrl: 'ws://localhost:9090'
};
```

#### 13.4.1\. 以 dev 模式运行 ngAuction

ngAuction 现在由两个项目组成，因此你需要在每个项目中运行 `npm install`，然后分别启动服务器和客户端。要启动服务器，切换到服务器目录，通过运行 `tsc` 将所有 TypeScript 代码编译成 JavaScript，然后按照以下方式启动服务器：

```
node build/main
```

要启动 Angular 应用，转到客户端目录并运行以下命令：

```
ng serve
```

你将看到与你在第十一章中创建的 ngAuction 相同的 UI，但现在产品数据和图片通过 HTTP 连接从服务器获取。打开你的 Chrome 浏览器，访问 http://localhost:4200，选择一个产品，并点击竞标按钮。你会看到价格以 $5 的幅度增加。现在打开另一个浏览器（例如 Firefox），访问 http://localhost:4200，选择相同的产品，你会看到最新的价格。在第二个浏览器中提交新的竞标，新的竞标将在两个浏览器中显示。服务器将新的竞标推送到所有已连接的客户端。

在阅读了第十二章中关于同源限制和代理客户端请求的内容后，你可能想知道从端口 4200 加载的应用如何在不配置客户端代理的情况下访问运行在端口 9090 上的 HTTP 服务器。这是因为这次，你在 Node 服务器上使用了特殊的 CORS 包，以实现来自任何客户端的无限制访问。你将在下一节中看到如何做到这一点。

#### 13.4.2\. 回顾 ngAuction 服务器代码

到目前为止，你已经知道如何使用 Node.js、Express 和 `ws` 包创建和启动 HTTP 和 WebSocket 服务器，这部分内容将不再重复。在本节中，我们将回顾与 ngAuction 新功能相关的服务器代码片段。你将把服务器代码拆分为四个 TypeScript 文件。图 13.9 展示了本章提供的 ngAuction 服务器目录结构。

##### 图 13.9\. ngAuction 服务器结构

![](img/13fig09_alt.jpg)

在第十一章中的 ngAuction，数据文件夹位于 Angular 项目中；现在，你将数据移动到服务器。在第十一章，读取 products.json 的代码和获取所有产品或按 ID 获取产品的函数位于 product .service.ts 文件中，现在位于 db-auction.ts 文件中。main.ts 文件包含启动 HTTP 和 WebSocket 服务器的代码。ws-auction.ts 文件包含支持与 ngAuction 客户端进行 WebSocket 通信的代码。

##### 启动 HTTP 和 WebSocket 服务器

让我们从用于启动服务器的 main.ts 文件开始代码审查。这个文件中的代码与第 13.2 节中的 simple-websocket-server.ts 类似，但这次你不需要在不同的端口上启动 HTTP 和 WebSocket 服务器——它们都使用端口 9090。另一个区别是，你使用 Node 的一个接口`createServer()`创建 HTTP 服务器的实例，正如以下列表所示。

##### 列表 13.12\. main.ts

```
import * as express from 'express';
import {createServer} from 'http';
import {createBidServer} from './ws-auction';
import {router} from './rest-auction';

const app = express();
app.use('/api', router);                               *1*

const server = createServer(app);                      *2*
 createBidServer(server);                              *3*

server.listen(9090, "localhost", () => {               *4*
   const {address, port} = server.address();
  console.log(`Listening on  ${address} ${port}`);
});
```

+   ***1* 将包含/api 的请求转发到 Express 路由器**

+   ***2* 创建 http.Server 对象实例**

+   ***3* 使用其 HTTP 对等实例创建 BidServer**

+   ***4* 启动两个服务器**

这段代码使用 Node 的`createServer()`创建 HTTP 服务器的实例，并将 Express 作为回调函数传递以处理所有 HTTP 请求。要启动你的 WebSocket 服务器，你从 ws-auction.ts 中调用`createBidServer()`函数。但首先，让我们审查你的 RESTful HTTP 服务器。

##### HTTP 服务器

在第十二章中，12.2.2 节，你创建了一个简单的 Node/Express 服务器，用于处理产品请求。在本节中，你将看到这样一个服务器的更高级版本。在这里，你将使用 Express 的`Router`来路由 HTTP 请求。你还将使用 CORS 模块来允许所有浏览器忽略同源限制。这就是为什么你可以使用`ng serve`启动客户端而不需要配置代理。

最后，产品数据不会硬编码——你将数据处理部分移动到了 db-auction.ts 脚本中。你的 HTTP REST 服务器在 rest-auction.ts 文件中实现，如下列表所示。

##### 列表 13.13\. rest-auction.ts

```
import * as cors from 'cors';                                     *1*
 import * as express from 'express';
import {
  getAllCategories,
  getProducts,
  getProductById,
  getProductsByCategory
} from './db-auction';                                            *2*

export const router = express.Router();                           *3*

router.use(cors());                                               *4*

router.get('/products',
  async (req: express.Request, res: express.Response) => {        *5*
   res.json(await getProducts(req.query));                        *6*
 });

router.get('/products/:productId', async (req: express.Request,
res: express.Response) => {
  const productId = parseInt(req.params.productId, 10) || -1;
  res.json(await getProductById(productId));
});

router.get('/categories', async (_, res: express.Response) => {
  res.json(await getAllCategories());
});

router.get('/categories/:category', async (req: express.Request,
res: express.Response) => {
  res.json(await getProductsByCategory(req.params.category));
});
```

+   ***1* 导入 CORS 模块**

+   ***2* 导入数据处理函数**

+   ***3* 创建并导出 Express Router 实例**

+   ***4* 使用 CORS 允许所有客户端请求**

+   ***5* 使用 async 关键字标记函数为异步**

+   ***6* 使用 await 关键字避免在 then()回调中嵌套代码**

在第 13.4.1 节中，我们提到客户端的开发服务器将在端口 4200 上运行，而 REST 服务器将在端口 9090 上运行。为了克服同源限制，你使用 Express 包 CORS 来启用所有来源的访问（见[`mng.bz/aNxM`](http://mng.bz/aNxM)）。如果你在服务器目录中打开 package.json，你会在其中找到依赖项`"cors": "².8.4"`。

在这个服务器中，你创建了一个 Express `Router`对象的实例，并使用它根据提供的路径路由 HTTP GET 请求。

注意 `async` 和 `await` 关键字的使用。在第十二章的第 12.2.2 节中，您没有使用它们来检索产品，因为产品数据存储在数组中，并且那里的 `getProducts()` 等函数是同步的。现在您使用 db-auction.ts 中的数据处理函数，它们从文件中读取数据，这是一个异步操作。

使用 `async` 和 `await` 关键字可以使异步代码看起来像是同步的（有关更多详细信息，请参阅附录 A 的 A.12.4 节）。

##### 数据处理脚本

您的 HTTP 服务器使用 db-auction.ts 脚本进行所有数据处理操作。此脚本包含从 products.json 文件中读取产品以及根据提供的搜索条件搜索产品的函数。我们不会审查 db-auction.ts 脚本的全部代码，但我们将讨论与 ngAuction 第十一章中包含的版本的产品.service.ts 相比所做的代码更改，如下所示。

##### 列表 13.14\. db-auction.ts（部分列表）

```
import * as fs from 'fs';
import * as util from 'util';

type DB = Product[];                                                  *1*

const readFile = util.promisify(fs.readFile);                         *2*
 const db$: Promise<DB> =                                             *3*
    readFile('./data/products.json', 'utf8')                          *4*
   .then(JSON.parse, console.error);

export async function getAllCategories(): Promise<string[]> {
  const allCategories = (await db$)                                   *5*
    .map(p => p.categories)
    .reduce((all, current) => all.concat(current), []);

  return [...new Set(allCategories)];                                 *6*
 }
...

export async function updateProductBidAmount(productId: number,       *7*
                                       price: number): Promise<any> {
  const products = await db$;
  const product = products.find(p => p.id === productId);
  if (product) {
    product.price = price;
  }
}
```

+   ***1* 定义一个新的类型来存储产品数组**

+   ***2* 使 fs.readFile 返回一个 promise**

+   ***3* 声明读取 products.json 的 promise**

+   ***4* 读取 products.json**

+   ***5* 此函数获取每个产品的分类名称。**

+   ***6* 移除重复的分类**

+   ***7* 此函数根据最新的出价更新产品价格。**

在第十一章中，products.json 文件位于客户端，`ProductService` 使用 `HttpClient` 服务读取此文件，如下所示：

```
http.get<Product[]>('/data/products.json');
```

现在这个文件位于服务器上，您使用 Node 的 `fs` 模块来读取它，该模块包含用于处理文件系统的函数（有关更多详细信息，请参阅[`nodejs.org/api/fs.html`](https://nodejs.org/api/fs.html)）。您还使用另一个 Node 模块 `util`，它包含许多有用的实用工具，并使用 `util.promisify()` 将文件读取操作返回的数据作为 promise（请参阅[`mng.bz/Z009`](http://mng.bz/Z009)），而不是向 `fs.readFile` 提供回调。

在 db-auction.ts 的几个地方，您会看到 `await db$`，这意味着“执行 `db$` promise 并等待其解析或拒绝。”`db$` promise 知道如何读取 products.json 文件。

既然我们已经讨论了您的 RESTful 服务器的工作方式，那么让我们熟悉 WebSocket 服务器的代码。

##### WebSocket 服务器

ws-auction.ts 脚本实现了您的 WebSocket 服务器，该服务器可以接收用户的出价并通知用户有关新出价的信息。出价由包含产品 ID 和出价金额（价格）的 `BidMessage` 类型表示，如下所示。

##### 列表 13.15\. ws-auction.ts 中的 `BidMessage`

```
interface BidMessage {
  productId: number;
  price: number;
}
```

`createBidServer()` 函数使用提供的 `http.Server` 实例创建 `BidServer` 类的实例，如下所示。

##### 列表 13.16\. ws-auction.ts 中的 `createBidServer()`

```
export function createBidServer(httpServer: http.Server): BidServer {
  return new BidServer(httpServer);
}
```

`BidServer` 类包含标准的 WebSocket 回调 `onConnection()`、`onMessage()`、`onClose()` 和 `onError()`。这个类的构造函数创建了一个 `ws.Server` 实例（在那里你使用 `ws` 包），并将 `onConnection()` 回调方法定义为 WebSocket 的 `connection` 事件。`BidServer` 类的轮廓如下面的列表所示。

##### 列表 13.17\. `BidServer` 的结构

```
export class BidServer {
  private readonly wsServer: ws.Server;

  constructor(server: http.Server) {}

  private onConnection(ws: ws): void {...}           *1*

  private onMessage(message: string): void {...}     *2*

  private onClose(): void {...}                      *3*

  private onError(error: Error): void {...}          *4*
 }
```

+   ***1* WebSocket 连接事件处理器**

+   ***2* 消息事件处理器**

+   ***3* 关闭事件处理器**

+   ***4* 错误事件处理器**

现在我们来回顾每个方法的实现，从 `constructor()` 开始。

##### 列表 13.18\. 构造函数

```
constructor(server: http.Server) {
  this.wsServer = new ws.Server({ server });                 *1*
   this.wsServer.on('connection',                            *2*
      (userSocket: ws) => this.onConnection(userSocket));
}
```

+   ***1* 使用 HTTP 服务器实例实例化 WebSocket 服务器**

+   ***2* 定义连接事件处理器**

当你的 ngAuction 客户端连接到 `BidServer` 时，会调用 `onConnection()` 回调。这个回调的参数是一个 `WebSocket` 对象，代表单个客户端的连接。当客户端发起从 HTTP 协议切换到 `WebSocket` 协议的初始请求时，它会调用 `onConnection()` 回调，如下面的列表所示。

##### 列表 13.19\. 处理连接事件

```
private onConnection(ws: ws): void {
  ws.on('message', (message: string) => this.onMessage(message));      *1*
   ws.on('error', (error: Error) => this.onError(error));              *2*
   ws.on('close', () => this.onClose());                               *3*

  console.log(`Connections count: ${this.wsServer.clients.size}`);     *4*
 }
```

+   ***1* 监听消息事件**

+   ***2* 监听错误事件**

+   ***3* 监听关闭事件**

+   ***4* 报告连接客户端的数量**

`onConnection()` 方法为 WebSocket 事件 `message`、`close` 和 `error` 分配回调方法。当 `ws` 模块创建 WebSocket 服务器实例时，它将连接客户端的引用存储在 `wsServer.clients` 属性中。每次连接时，你会在控制台上打印连接客户端的数量。下一个列表将逐个回顾回调方法，从 `onMessage()` 开始。

##### 列表 13.20\. 处理客户端消息

```
import { updateProductBidAmount} from './db-auction';
...
  private onMessage(message: string): void {
    const bid: BidMessage = JSON.parse(message);                           *1*
     updateProductBidAmount(bid.productId, bid.price);                     *2*

    // Broadcast the new bid
    this.wsServer.clients.forEach(ws => ws.send(JSON.stringify(bid)));     *3*

    console.log(`Bid ${bid.price} is placed on product ${bid.productId}`);
  }
```

+   ***1* 解析客户端的 BidMessage**

+   ***2* 更新内存数据库中的竞标金额**

+   ***3* 向所有订阅者发送新产品竞标信息**

`onMessage()` 回调获取用户对产品的竞标并更新 db-auction.ts 脚本中实现的简单内存数据库中的金额。如果用户打开产品详情视图，他们将成为所有用户竞标通知的订阅者，因此你将新的竞标通过套接字推送到每个订阅者。

接下来，我们将回顾 `close` 事件的回调。当用户关闭产品详情视图时，WebSocket 连接也会关闭。在这种情况下，关闭的连接将从 `wsServer.clients` 中移除，因此不会向不存在的连接发送竞标通知，如下面的列表所示。

##### 列表 13.21\. 处理关闭连接

```
private onClose(): void {
  console.log(`Connections count: ${this.wsServer.clients.size}`);
}
```

在 `onError()` 回调中，你从提供的 `Error` 对象中提取错误消息并在控制台上记录错误。

##### 列表 13.22\. 处理 WebSocket 错误

```
private onError(error: Error): void {
    console.error(`WebSocket error: "${error.message}"`);
  }
}
```

图 13.10 显示了在 Chrome 和 Firefox 浏览器中打开的相同产品详细信息视图。用户在任一浏览器中点击投标按钮后，最新投标立即在两个视图中同步。

##### 图 13.10\. 两个浏览器中的同步投标

![](img/13fig10_alt.jpg)


**作业**

您的 ngAuction 不是一个生产级别的拍卖，您可能会发现一些边缘情况没有得到妥善处理。我们将描述其中之一，以防您想改进这个应用程序。

假设当前产品的投标金额是$70，Joe 点击投标按钮进行$75 的投标。同时，Mary 也看到了$70 作为最新的产品投标，她也点击了投标按钮。可能存在这样的情况：Joe 的请求将投标金额在服务器上更改为$75，几毫秒后，Mary 的$75 投标到达服务器。目前，`BidServer`将只是用 Mary 的$75 替换 Joe 的$75，他们每个人都会假设自己投了$75 的标。

为了防止这种情况发生，请修改`BidServer`中的代码，以拒绝投标，除非投标金额大于现有金额。在这种情况下，向输掉的用户发送一条包含新最低投标金额的消息。


我们已经涵盖了 ngAuction 的服务器端代码；让我们看看与第十一章版本相比客户端发生了什么变化。

#### 13.4.3\. ngAuction 客户端代码中的变化

如前所述，ngAuction 的 Angular 代码的主要变化是将包含产品数据和图像的文件以及读取这些文件的代码移到了服务器上。相应地，您在`ProductService`中添加了代码，以使用第十二章中介绍的`HttpClient`服务与 HTTP 服务器进行交互。

记住，`ProductComponent`负责渲染包含`ProductDetailComponent`和`ProductSuggestionComponent`（带有建议产品网格）的产品视图，并且您修改了这两个组件的代码。

此外，您添加了`bid.service.ts`文件以与 WebSocket 服务通信，并修改了`product-detail.component.ts`中的代码，以便用户可以对产品进行投标并查看其他用户的投标。让我们回顾与产品视图相关的更改。

##### ProductComponent 中的两个观察者

首先，如果用户查看一个产品的详细信息然后从建议产品网格中选择另一个产品，产品视图会发生变化。路由不会改变（用户仍在查看产品视图），`ProductComponent`订阅了来自`ActivatedRoute`的观察者，该观察者发出新选中的`productId`并检索相应的产品详细信息，如下所示。

##### 列表 13.23\. 处理来自`ActivatedRoute`的更改参数

```
this.product$ = this.route.paramMap
  .pipe(
    map(params => parseInt(params.get('productId') || '', 10)),        *1*
     filter(productId => Boolean(productId)),                          *2*
     switchMap(productId => this.productService.getById(productId))    *3*
   );
```

+   ***1* 获取新的 productId**

+   ***2* 处理可能无效的 productId。**

+   ***3* 获取所选产品的详细信息**

在 列表 13.23 中，您从 `ActivatedRoute` 中检索 `productId` 并使用 `switchMap` 操作符将其传递给 `ProductService`。`filter` 操作符仅作为预防措施，以排除虚假的产品 ID。例如，用户可以手动输入一个错误的 URL，如 http://localhost:4200/products/A23，您不希望请求不存在的产品详情。

产品组件的模板包括 `<nga-product-detail>` 组件，它通过其输入属性 `product` 获取选定的产品，如下所示。

##### 列表 13.24\. 将选定的产品传递给 `ProductDetailComponent`

```
<nga-product-detail
    fxFlex="auto"
    fxFlex.>-md="65%"
    *ngIf="product$ | async as product"         *1*
     [product]="product">                       *2*
 </nga-product-detail>
```

+   ***1* 使用异步管道提取产品对象**

+   ***2* 将选定的 Product 传递给 ProductDetailComponent**

您将提取产品数据的代码放在 `*ngIf` 指令内部，因为产品数据是异步检索的，并且您想确保 `product$` 可观察对象发出的数据与您绑定到 `ProductDetailComponent` 输入属性的属性数据相匹配。让我们看看 `ProductDetailComponent` 如何处理接收到的产品。

##### 在 ProductDetailComponent 中放置和监控出价

`ProductDetailComponent` 的 UI 如 图 13.10 所示。该组件通过其输入属性 `product` 获取要显示的产品。如果用户点击出价按钮，则使用 `BidService` 通过 WebSocket 连接发送新的出价（比当前出价高 5 美元），`BidService` 实现了与 `BidServer` 的所有通信。如果另一个连接到 `BidServer` 的用户对同一产品出价，产品详情视图中的出价金额将立即更新。

`ProductDetailComponent` 类具有私有的 RxJS 主题 `productChange$` 和可观察的 `latestBids$`，它合并了两个可观察对象的数据：

+   `productChange$` 处理用户打开产品详情视图并从“更多考虑的项目”列表中选择另一个产品的情况。当输入参数 `product` 的绑定发生变化时，生命周期钩子 `ngOnChanges()` 会拦截变化，并 `productChange$` 发出产品数据。

+   当 `productChange$` 或 `BidService` 推送从服务器接收的新出价时，`latestBids$` 发出新值。

您有两个数据源可以发出值，并且在任何发出时都需要更新视图。这就是为什么您使用 RxJS 操作符 `combineLatest` 将两个可观察对象组合在一起。product-detail.component.ts 文件的代码如下所示。

##### 列表 13.25\. product-detail.component.ts

```
// imports are omitted for brevity
@Component({
  selector: 'nga-product-detail',
  styleUrls: [ './product-detail.component.scss' ],
  templateUrl: './product-detail.component.html',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class ProductDetailComponent implements OnInit, OnChanges {
  private readonly productChange$ = new Subject<Product>();
  latestBids$: Observable<number>;
  @Input() product: Product;

  constructor(private bidService: BidService) {}                              *1*

  ngOnInit() {
    this.latestBids$ = combineLatest(                                         *2*
       this.productChange$.pipe(startWith(this.product)),                     *3*
       this.bidService.priceUpdates$.pipe(startWith<BidMessage | null>(null)),*4*
       (product, bid) =>  bid && bid.productId === product.id ?               *5*
                          bid.price: product.price                            *6*
     );
  }

  ngOnChanges({ product }: { product: SimpleChange }) {
    this.productChange$.next(product.currentValue);                           *7*
   }

  placeBid(price: number) {
    this.bidService.placeBid(this.product.id, price);                         *8*
   }
}
```

+   ***1* 注入 BidService**

+   ***2* 合并两个可观察对象的值**

+   ***3* 第一个可观察对象以当前显示的产品开始发出**

+   ***4* 第二个可观察对象发出出价。**

+   ***5* 检查到达的出价是否针对当前产品**

+   ***6* 如果放置了新的出价，则使用其值；否则，使用产品价格**

+   ***7* 发射新选择的产品**

+   ***8* 在此产品上放置出价**

RxJS 操作符 `combineLatest`（参见 [`mng.bz/Y28Y`](http://mng.bz/Y28Y)）订阅了两个可观察对象发出的值，并在任一可观察对象发出值时调用合并函数。在这种情况下，要么是 `productChange$` 发出的值，要么是 `bidService.priceUpdates$`（`BidService` 代码包含在下一节中）。以下是你的合并函数：

```
(product, bid) =>  bid && bid.productId === product.id ?
                         bid.price: product.price
```

这两个可观察对象发出的值表示为参数 `(product, bid)`，这个函数返回 `product.price` 或 `bid.price`，具体取决于哪个可观察对象发出了值。这个值将用于在产品详情视图中进行渲染。

因为 `combineLatest` 操作符需要两个可观察对象都发出值以最初调用合并函数，所以你应用了 `startWith` 操作符（参见 [`mng.bz/OL9z`](http://mng.bz/OL9z)），以确保在可观察对象开始进行常规发射之前，提供值有一个初始发射。对于初始值，你使用一个可观察对象的 `product`，另一个使用 `BidMessage` 或 null。当 `ProductDetailComponent` 首次渲染时，可观察对象 `bidService .priceUpdates$` 发出 null。

你的组合可观察对象在 `ngOnInit()` 生命周期钩子中声明，其值使用 `async` 管道在模板中渲染。你是在 `*ngIf` 指令内部完成的，这样就不会渲染假值：

```
*ngIf="latestBids$ | async as price"
```

当用户点击投标按钮时，你调用 `bidService.placeBid()`，该函数内部检查是否需要打开与 `BidServer` 的连接或连接已经打开。下一节中的 `product-detail.component.html` 展示了如何在模板中实现投标按钮。

##### 列表 13.26\. 模板中的投标按钮

```
<button class="info__bid-button"
         mat-raised-button
         color="accent"
         (click)="placeBid(price + 5)">                           *1*
    PLACE BID {{ (price + 5) | currency:'USD':'symbol':'.0' }}    *2*
  </button>
```

+   ***1*** 将投标金额定为比最新投标/价格高 $5**

+   ***2*** 在按钮上显示下一个投标金额**

现在我们来看看 `BidService` 类如何使用 WebSocket 协议与服务器通信。

##### 使用 RxJS 与 WebSocket 服务器通信

在 第 13.3.2 节，我们展示了编写与 WebSocket 服务器通信的客户端代码的一种非常基本的方法。在 ngAuction 中，你将使用 RxJS 包含的更健壮的 WebSocket 服务，这意味着你可以在任何 Angular 项目中使用它。

RxJS 提供了一个基于 `Subject` 的 WebSocket 服务实现，该 `Subject` 在 附录 D 中的 D.6 节 中进行了说明。RxJS 的 `Subject` 既是观察者又是可观察对象。换句话说，它可以接收和发出数据，这使得它非常适合处理 WebSocket 数据流。RxJS 的 `WebSocketSubject` 是标准浏览器 `WebSocket` 对象的包装器，位于 `rxjs/websocket` 文件中。


##### 小贴士

在 RxJS 6 之前，`WebSocketSubject` 类位于 `rxjs/observable/dom/WebSocketSubject` 文件中。


在其最简单形式中，`WebSocketSubject` 可以接受一个包含 WebSocket 端点 URL 的字符串或 `WebSocketSubjectConfig` 对象的实例，其中你可以提供额外的配置。当你的代码订阅 `WebSocketSubject` 时，它要么使用现有的连接，要么创建一个新的连接。从 `WebSocketSubject` 取消订阅如果没有任何其他订阅者监听相同的 WebSocket 端点，则会关闭连接。

当服务器将数据推送到 socket 时，`WebSocketSubject` 会将数据作为可观察值发出。在发生错误的情况下，`WebSocketSubject` 会像任何其他可观察对象一样发出错误。如果服务器推送数据但没有订阅者，值将被缓冲，并在新客户端订阅时立即发出。


##### 注意

虽然处理消息的方式在常规 RxJS `Subject` 和 `WebSocketSubject` 之间有所不同。如果你在 `Subject` 上调用 `next()`，它会向所有订阅者发出数据，但如果你在 `WebSocketSubject` 上调用 `next()`，则不会。记住，在可观察对象和订阅者之间有一个服务器，何时发出值由服务器决定。


本章附带 ngAuction 客户端包括 shared/services/bid.service.ts 文件，该文件使用 `WebSocketSubject`。`BidService` 是一个单例，仅由 `ProductDetailComponent` 使用，它使用 `async` 管道订阅它。当用户关闭产品详情视图时，组件被销毁，`async` 管道取消订阅，关闭 WebSocket 连接。让我们回顾一下 bid.service.ts 脚本的代码。

##### 列表 13.27\. bid.service.ts

```
import { WebSocketSubject } from 'rxjs/websocket';
...

export interface BidMessage {
  productId: number;
  price: number;
}

@Injectable()
export class BidService {
  private _wsSubject: WebSocketSubject<any>;
  private get wsSubject(): WebSocketSubject<any> {                   *1*

    const closed = !this._wsSubject || this._wsSubject.closed;       *2*
     if (closed) {
      this._wsSubject = new WebSocketSubject(this.wsUrl);            *3*
     }
    return this._wsSubject;
  }

  get priceUpdates$(): Observable<BidMessage> {
    return this.wsSubject.asObservable();                            *4*
   }

  constructor(@Inject(WS_URL) private readonly wsUrl: string) {}     *5*

  placeBid(productId: number, price: number): void {
    this.wsSubject.next(JSON.stringify({ productId, price }));       *6*
   }
}
```

+   ***1* 私有属性 _wsSubject 的 getter**

+   ***2* WebSocket-Subject 永远没有被创建或已经断开连接。**

+   ***3* 连接到 BidServer**

+   ***4* 获取主题的可观察对象的引用**

+   ***5* 注入 WebSocket 服务器 URL**

+   ***6* 将新出价推送到 BidServer**

`BidService` 单例包含 `priceUpdates$` getter，它返回可观察对象。`ProductDetailComponent` 在 `ngOnInit()` 中使用此 getter。这意味着 `priceUpdates$` 一旦 `ProductDetailComponent` 渲染，就会打开 WebSocket 连接（通过 `this.wsSubject` getter），并且 `async` 管道是该组件模板中的订阅者。

`BidService` 还有一个私有属性 `_wsSubject` 和内部使用的 `wsSubject` getter。当从 `priceUpdates$` 第一次访问 getter 时，`_wsSubject` 变量不存在，并且会创建一个新的 `WebSocketSubject` 实例，与 WebSocket 服务器建立连接。

如果用户离开产品详情视图，连接将被关闭。因为 `BidService` 是一个单例，如果用户关闭并重新打开产品详情视图，`BidService` 的实例不会重新创建，但由于连接状态已关闭 (`_wsSubject.closed`)，它将被重新建立。

WebSocket 服务器的 URL（`WS_URL`）在开发环境中存储在`environment.ts`中，在生产环境中存储在`environment.prod.ts`中。此值通过`@Inject`指令注入到`wsUrl`变量中。

这完成了 ngAuction 更新代码审查，这些更新实现了 Angular 客户端与两个服务器之间的通信。按照第 13.4.1 节所述运行 ngAuction，ngAuction 将变为可用状态。

### 摘要

+   WebSocket 协议提供了 HTTP 所不具备的独特功能，这使得它在某些用例中成为更好的选择。客户端和服务器都可以发起通信。

+   WebSocket 协议不使用请求/响应模型。

+   你可以创建一个 Angular 服务，将 WebSocket 事件转换为可观察的流。

+   RxJS 库在`WebSocketSubject`类中包含了基于`Subject`的 WebSocket 支持实现，你可以在任何 Angular 应用中使用它。
