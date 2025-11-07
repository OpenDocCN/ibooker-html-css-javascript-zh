## 附录 C. React Router 4 的附加用法

*本附录涵盖*

+   在 React Router 4 中处理第三方库，如分析模块

+   在运行时动态添加路由

+   使用 webpack 和 React Router 4 进行代码拆分

第十章 和 第十一章 展示了如何执行一些会影响 React Router 代码和应用程序其余部分协同工作方式的事情。本附录通过 React Router 4 讲解了这些示例中的几个。

本附录中的所有示例都位于主示例仓库的 react-router-4 分支（`git checkout react-router-4`）中，[`mng.bz/S3N0`](http://mng.bz/S3N0)。此分支中的代码也已更新，以便您可以查看 React Router 4 与代码最终版本协同工作的完整示例。这基于附录 A（kindle_split_027_split_000.xhtml#app01）和附录 B（kindle_split_028.xhtml#app02）中展示的代码更改。

### C.1\. 将分析移动到 onRouteChange

您需要做的第一个更新是将分析代码移动到 `onRouteChange` HOC。之前，这由 React Router 3 的生命周期方法处理。现在您将像处理页面跟踪和预取数据一样在 附录 A 中处理它。以下列表演示了如何进行此操作。此更改来自 第十章，第 10.1 节。

##### 列表 C.1\. 在 `onRouteChange` 中添加分析—components/onRouteChange.jsx

```
import { sendData } from '../analytics.es6';           *1*

const onRouteChange = (WrappedComponent) => {
  return class extends React.PureComponent {
    trackPageView(location) {                          *2*
      this.sendAnalytics(location);                    *2*
      console.log('Tracked a pageview');
    };

    fetchData(nextProps) {}

    sendAnalytics(location) {                          *3*
      sendData({                                       *3*
        location: location && location.pathname,
        type: 'navigation'
      });
    }

    componentDidMount() {
      this.trackPageView(this.props.location);         *4*
    }

    componentWillReceiveProps(nextProps) {
      //...other code

      if (navigated) {
        this.trackPageView(nextProps.location);        *4*
      }
    }
  }
}
```

+   ***1* 从分析模块导入 sendData**

+   ***2* 将位置传递给函数。调用 sendAnalytics。**

+   ***3* 创建一个调用 sendData 的 sendAnalytics 函数。将位置传递给它。**

+   ***4* 每次路由更改时调用 trackPageView。确保传递正确的位置。**

从 `componentWillReceiveProps` 中调用 `sendAnalytics` 替换了 `sharedRoutes` 中的 `onEnter` 调用。此外，从 `componentDidMount` 中调用 `sendAnalytics` 也替换了 `sharedRoutes` 中的 `onEnter` 调用。

### C.2\. 添加动态路由

第十章 还演示了如何根据环境变量或其他标志添加动态路由。要在新的 sharedRoutesv4 文件中这样做，您需要在导出之前更新路由数组。以下列表显示了如何进行此操作。

##### 列表 C.2\. 添加动态路由—sharedRoutesv4.es6

```
const routes = [
  {
    component: App,
    routes: [
      //...other code
    ]
  }
]

if (process.env.NODE_ENV !== 'production') {      *1*
  routes[0].routes.push({                         *2*
    path: '/dev-test',
    component: Products
  });
}

export { routes };

export default routes;
```

+   ***1* 检查环境。如果不是生产环境，则添加此仅限开发的路由。**

+   ***2* 将新路由推入路由数组。记住，路由数组是路由对象中根 App 组件的子组件。**

现在您在开发中有一个额外的路由了！

### C.3\. 代码拆分：React Loadable

最后一个涉及 React Router 的例子是来自第十一章（kindle_split_023_split_000.xhtml#ch11）的代码拆分。好消息是，在 React Router 4 中这要容易一些。社区在这方面确实已经迎头赶上，并提供了一个库来处理您所有的代码拆分需求。这个库叫做 React Loadable，它易于使用且文档齐全。

React 社区考虑到了方方面面，包括处理同构应用。他们在 [`mng.bz/3hJg`](http://mng.bz/3hJg) 提供了良好的文档。我这里不会重复所有他们的例子。

关于 React Loadable 的一些有趣之处：

+   您可以提供一个在组件加载时显示的组件（[`mng.bz/518M`](http://mng.bz/518M)）。

+   该库提供了一种预加载组件的选项（这在服务器上总是被使用），并且可以在浏览器中进行乐观加载（[`mng.bz/081O`](http://mng.bz/081O)）。

+   而不是安装所有正确的 Babel 插件并正确设置 webpack，该库为您处理所有这些。它提供了一个 Babel 包供您使用（[`mng.bz/91mV`](http://mng.bz/91mV)）。它还附带了一个 webpack 插件和用于服务器端渲染的相关实用工具（[`mng.bz/34vL`](http://mng.bz/34vL)）。
