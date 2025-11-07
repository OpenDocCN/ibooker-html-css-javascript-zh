## 附录 A. React Router 4 基础知识

*本附录涵盖*

+   在您的组件中使用声明式路由

+   使用可配置的路由创建单一事实来源

+   将 React Router 3 的生命周期事件中的代码移动到高阶组件中

+   通过使用高阶组件在浏览器中预取数据

React Router 4 代表了从 React Router 3 到的一种重大思维转变。它从在单个文件中定义路由的标准静态路由实现，转变为在 React 组件内部创建路由的动态实现。这也允许您将大多数生命周期逻辑从 React Router 的生命周期方法中移出，并放入 React 的生命周期中。让我们首先回顾一下如何将您的组件切换到使用 React Router 的去中心化路由模式。本附录中的代码示例假设您已审查了 React Router 3 版本，并想查看差异。每个列表都假设您熟悉相关章节中展示的代码。我已将新代码以粗体列出。

### A.1\. 使用 React Router 4 进行仅浏览器路由

在本节中，我将向您展示如何在单页应用程序（SPA）架构中使路由工作，以便您理解 React Router 4 的基本原理。本节中示例的代码可以在[`mng.bz/zRGa`](http://mng.bz/zRGa)的完整同构示例仓库中找到。代码也可以在分支 chapter-4-react-router-basic-routes 中找到（`git checkout chapter-4-react-router-basic-routes`）。

首先，您需要安装 React Router 4 包，以便为本节提供正确的代码。React Router 现在遵循 React 的约定，将 DOM 相关的代码分离到其自己的包中。请确保升级 react-router 并安装 react-router-dom：

```
$ npm install react-router@4.2.0
$ npm install react-router-dom
```

在完成此操作后，您需要在 main.jsx 中设置 React Router。

#### A.1.1\. 创建应用程序

在 React Router 4 中实例化主路由的方式与版本 3 略有不同。不是将路由和历史传递给路由器，而是选择适合用例的路由器。在这种情况下，您想使用`BrowserRouter`以便在您的应用程序中利用浏览器历史 API。以下列表显示了如何更改 main.jsx 中的代码以与 React Router 4 一起工作。

##### 列表 A.1\. 设置路由器—main.jsx

```
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';    *1*
import App from './components/app';                  *2*

ReactDOM.render(
  <BrowserRouter>                                    *1*
    <App />                                          *2*
  </BrowserRouter>,                                  *1*
  document.getElementById('react-content')
);
```

+   ***1* 使用 BrowserRouter 组件作为根组件**

+   ***2* App 应该是 BrowserRouter 的子组件。您将把路由放入 App 组件中。**

尽管您已实例化了路由器，但您仍需要更新您的组件以处理路由。下一节将向您展示如何做到这一点。

#### A.1.2\. 组件中的路由

React Router 4 的基础知识与 React Router 3 类似。你使用 Route 组件来声明你的路由。你给它一个路径和一个要渲染的组件。最大的变化在于你声明路由的位置。在版本 3 中，你有一个单一的、集中的路由文件。在版本 4 中，你在适当的组件内部声明你的路由。以下列表显示了如何将 sharedRoutes.jsx 文件中的路由放入 app.jsx 中。

##### 列表 A.2\. 声明路由—components/app.jsx

```
// ... other code
import {
  Link, Route, withRouter
} from 'react-router-dom';                                      *1*
import Cart from '../components/cart';                          *2*
import Products from '../components/products';                  *2*
import Profile from '../components/profile';                    *2*
import Login from '../components/login';                        *2*

const App = (props) => {
  return (
    <div>
      <div className="ui fixed inverted menu">
        <h1 className="header item">All Things Westies</h1>
        <Link to="/products" className="item">Products</Link>
        <Link to="/cart" className="item">Cart</Link>
        <Link to="/profile" className="item">Profile</Link>
      </div>
      <div className="ui main text container">
        <Route path="/" exact component={Products} />           *3*
        <Route path="/products" component={Products} />         *4*
        <Route path="/cart" component={Cart} />                 *4*
        <Route path="/profile" component={Profile} />           *4*
        <Route path="/login" component={Login} />               *4*
      </div>
    </div>
  );
};

export default withRouter(App);                                 *5*
```

+   ***1* 导入 Route 组件和 withRouter 高阶组件**

+   ***2* 导入在路由中使用的各种应用组件**

+   ***3* 对于会导致多个匹配的路径，请确保使用精确选项。**

+   ***4* 每个 Route 都需要一个路径和一个组件。**

+   ***5* 使用 HOC withRouter 包装 App。这确保你的组件可以访问历史和位置属性。**

现在你已经将路由移动到正确的位置，你的应用就可以工作了！注意，Link 组件在两个版本中工作方式相同。你通过为 Link 组件提供一个 `to` 属性来声明一个链接。


##### 注意

在 React Router 3 中，你可以使用 `/` 定义一个父路由，然后定义子路由时不需要前面的斜杠（例如，`products`）。这不再被支持。相反，在父路由上，你需要使用 `exact` 选项。对于你的根路由 `/`，你希望设置 `exact: true`。


接下来，你将学习如何以适合同构应用的方式创建路由。

### A.2\. 创建单一的真实来源

现在你已经在应用中使用了 React Router 4，让我们应用你在 React Router 3 中应用过的相同原则；目标是让你的路由有一个单一的真实来源。本节中所有的代码都可以在分支 chapter-4-react-router-v4 中找到（`git checkout chapter-4-react-router-v4`）。

你需要一种方法来定义你的路由，这个路由可以在浏览器中立即使用，也可以在服务器端稍后使用。好消息是 React Router 的创建者已经创建了一个用于解决这个问题的测试版库：React Router Config ([`mng.bz/33Vu`](http://mng.bz/33Vu))。这是一个提供两个函数的实用库，你可以使用这些函数来简化服务器渲染和匹配：

+   **`matchRoutes(routes, pathname)`—** 确定当前路由是否与提供的配置中的某个路由匹配。

+   **`renderRoutes(routes, extraProps)`—** 将提供的路由渲染到函数被调用的组件中。此方法必须用于替代 Route 组件，以确保浏览器渲染与服务器端渲染匹配。

安装这个库，以便你可以导入它：

```
$ npm install react-router-config
```

接下来，你将使用配置对象设置你的路由，而不是直接在 App 组件中声明它们。

#### A.2.1\. 路由作为配置

与在 App 组件中将路由声明为子组件不同，你现在将创建一个代表路由的 JavaScript 对象。与 React Router 3 一样，你将在 sharedRoutes 文件中这样做。为了区分和比较，我称此文件为 sharedRoutesv4.jsx。

以下列表显示了构成此模块的代码。

##### 列表 A.3\. 路由配置—shared/sharedRoutesv4.es6

```
import App from '../components/app';                       *1*
import Cart from '../components/cart';                     *1*
import Products from '../components/products';             *1*
import Profile from '../components/profile';               *1*
import Login from '../components/login';                   *1*

const routes = [
  {
    component: App,                                        *2*
    routes: [
      {
        path: '/',                                         *3*
        exact: true,                                       *4*
        component: Products
      },
      {
        path: '/cart',                                     *5*
        component: Cart
      },
      {
        path: '/products',                                 *5*
        component: Products
      },
      {
        path: '/profile',                                  *6*
        component: Profile
      },
      {
        path: '/login',                                    *6*
        component: Login
      }
    ]
  }
]

export default routes;
```

+   ***1* 导入在路由中使用的组件**

+   ***2* 声明你的根组件**

+   ***3* 在 App 组件的路由数组中声明子路由**

+   ***4* 你仍然可以通过添加此属性来声明精确选项。**

+   ***5* 每个路由都有一个路径和一个组件。**

+   ***6* 每个路由都有一个路径和一个组件。**

现在你已经声明了你的路由，你需要使用这个路由配置而不是上一节中的 Route 组件。

#### A.2.2\. 在组件中配置路由

使用 React Router Config 库，你可以轻松地将你的应用程序配置为使用上一节中的路由。为了使一切正常工作，你需要在 main.jsx 和你的顶级 App 组件中调用此库中的 `renderRoutes` 函数。以下列表显示了如何在 main.jsx 中声明此函数。

##### 列表 A.4\. 使用配置的路由—main.jsx

```
import from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';     *1*
import routes from './shared/sharedRoutesv4.es6';       *2*

ReactDOM.render(
  <BrowserRouter>
    { renderRoutes(routes) }                            *3*
  </BrowserRouter>,
  document.getElementById('react-content')
);
```

+   ***1* 导入 renderRoutes**

+   ***2* 导入路由配置对象**

+   ***3* 调用 renderRoutes 并传入路由配置。这替换了 App 组件的声明。**

接下来，你还需要在 App 组件内部声明这些路由——每次你有具有子路由的组件时，你都需要声明路由。这替换了之前你做的 Route 组件声明。以下列表显示了如何更新代码。

##### 列表 A.5\. 使用配置的路由—components/app.jsx

```
import { Link } from 'react-router-dom';
import {
  renderRoutes
} from 'react-router-config';                        *1*

const App = (props) => {
  return (
    <div>
      // ...more code
      <div className="ui main text container">
        {
          renderRoutes(
            props.route.routes,                      *2*
            { history: props.history }               *3*
          )
        }
      </div>
    </div>
  );
};
```

+   ***1* 导入 renderRoutes**

+   ***2* 调用 renderRoutes。通过属性传入提供的路由对象，因为你在 main.jsx 中声明了它们。**

+   ***3* 将历史对象作为属性传递，以便子组件在需要时可以访问。**

到目前为止，你的路由应该按预期工作。接下来，我们将回顾 React 生命周期的更改。

### A.3\. 处理生命周期事件

React Router 3 和 4 之间的另一个主要变化是处理生命周期事件的方式。在 React Router 3 中，Router 有它自己的独立生命周期。你必须管理 React 的生命周期和 React Router 的生命周期，这通常变得很复杂。你在 sharedRoutes 文件中添加了代码来处理生命周期中的特定点的代码。现在你需要以不同的方式来做这件事。

在 React Router 4 中，你使用 React 的生命周期根据路由变化来进行更新。一种促进这种更新方式的好方法是创建一个高阶组件（HOC）。

#### A.3.1\. 使用高阶组件来管理路由更改

要创建自己的高阶组件，你创建一个返回 React 组件的函数。然后你可以挂钩到 React 生命周期并根据路由更改进行更新。以下列表展示了如何做这件事。它包括第四章的示例，其中在每次更新时添加页面跟踪。

##### 列表 A.6\. `onRouteChange` 高阶组件—components/onRouteChange.jsx

```
const onRouteChange = (WrappedComponent) => {                           *1*
  return class extends React.PureComponent {

    trackPageView() {                                                   *2*
      // In real life you would hook this up to your analytics tool of
      choice console.log('Tracked a pageview');
    };

    componentDidMount() {
      this.trackPageView();                                             *3*
    }

    componentWillReceiveProps(nextProps) {
      const navigated = nextProps.location !== this.props.location;     *4*

      if (navigated) {
        this.trackPageView();                                           *5*
      }
    }

    render() {
     return <WrappedComponent {...this.props} />;
    }
  }
}
```

+   ***1* 创建一个接受组件的函数**

+   ***2* 这个函数代表跟踪页面视图。**

+   ***3* 在 componentDidMount 中调用 trackPageView 函数。这替换了旧 sharedRoutes 文件中的 onEnter 调用。**

+   ***4* 检查路由是否已更改**

+   ***5* 如果路由已更改，则调用 trackPageView。在 componentWillReceiveProps 中调用它替换了旧 sharedRoutes 文件中的 onChange 调用。**

在创建了这个高阶组件（HOC）之后，你需要在根组件 App 中使用它。以下列表展示了如何导入和应用它。

##### 列表 A.7\. 使用配置的路由—components/app.jsx

```
// no longer using withRouter HOC
import onRouteChange from './onRouteChange';        *1*

const App = (props) => {};

// no longer using withRouter HOC
export default onRouteChange(App);                  *2*
```

+   ***1* 导入 onRouteChange**

+   ***2* 为了让 onRouteChange HOC 管理你的路由，你需要用它在根组件周围包裹。**

现在你在 `onRouteChange` 中包裹了根组件，你可以轻松地添加代码来在应用程序中导航时预取数据。

#### A.3.2\. 预取视图数据

第八章 介绍了如何在浏览器中导航网站时预取数据。随着 React Router 4 版本中生命周期事件的移除，你需要采取不同的方法。这和之前章节中处理页面跟踪事件的方式相同。

这个示例的代码可以在 GitHub 上找到（`git checkout chapter-8-complete-react-router-4`）。

以下列表展示了如何在导航不同路由之间时在浏览器中预取数据。它使用了之前章节中创建的 `onRouteChange` HOC。

##### 列表 A.8\. 预取数据—components/onRouteChange.jsx

```
import { matchRoutes } from 'react-router-config';                         *1*
import routes from '../shared/sharedRoutesv4.es6';                         *1*

const onRouteChange = (WrappedComponent) => {
  return class extends React.PureComponent {
    // ... other code

    fetchData(nextProps) {
      const { route, location } = nextProps;
      const { routes } = route;
      const matches = matchRoutes(
                                   routes,
                                   location.pathname
                                 );                                        *2*
      let results = matches.map(({match, route}) => {                      *3*
        const component = route.component;
        if (component) {                                                   *4*
          if (component.displayName &&
              component.displayName.toLowerCase().indexOf('connect') > -1
          ) {
            let parentComponent = component.WrappedComponent;
            if (parentComponent.prefetchActions) {
              return parentComponent.prefetchActions (
                location.pathname.substring(1)
              );
            } else if (parentComponent.wrappedComponent &&
     parentComponent.wrappedComponent().prefetchActions ) {                *5*
              return parentComponent.wrappedComponent().prefetchActions (
                location.pathname.substring(1)
              );
            }
          } else if (component.prefetchActions) {
            return component.prefetchActions (
              location.pathname.substring(1)
            )
          }
        }
        return [];
      });

      const actions = results.reduce((
                                       flat,
                                       toFlatten
                                       ) => {                              *6*
        return flat.concat(toFlatten);
      }, []);

      const promises = actions.map((initialAction) => {                    *7*
        return this.props.dispatch(initialAction());
      });
      Promise.all(promises);                                               *8*
    }

    componentDidMount() {}

    componentWillReceiveProps(nextProps) {
      this.fetchData(nextProps);                                           *9*
      //... other code
    }
```

+   ***1* 导入 matchRoutes 以提取当前路由中使用的所有组件。从 sharedRoutesv4 中配置的路由获取路由。**

+   ***2* 使用 props 中的路由和位置调用 matchRoutes**

+   ***3* 遍历 matches 数组中的每个项目。这代表在这个路由上渲染的每个组件——在 sharedRoutesv4 中声明的任何内容。**

+   ***4* 这个块几乎与 第七章 中的相同。你寻找基本组件，如果函数存在，则调用 prefetchActions。**

+   ***5* 主要区别是你必须处理将 App 包裹在两个 HOC 中的情况。**

+   ***6* 将获取动作的结果扁平化成一个单一数组**

+   ***7* 通过将它们传递到 Redux 的 dispatch 函数中，准备执行动作**

+   ***8* 调用动作**

+   ***9* 你在 componentWillReceiveProps 中调用 fetchData 函数，所以除了初始加载外，它将每次运行。**

因为你在前面的代码中使用了`dispatch`，所以你需要将 App 组件包裹在 connect 高阶组件（HOC）中。下面的列表显示了如何做到这一点。

##### 列表 A.9\. 使用配置的路由—组件/app.jsx

```
import { connect } from 'react-redux';             *1*

const App = (props) => {};

export default connect()(onRouteChange(App));      *2*
```

+   ***1* 导入 connect**

+   ***2* 要在 onRouteChange 中访问 dispatch 函数，你必须首先将其包裹在 connect 高阶组件（HOC）中。**

现在应用将运行！它会在需要时预取数据。尝试从/products 导航到/cart 来查看这一功能的效果。
