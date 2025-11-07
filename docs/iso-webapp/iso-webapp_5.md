## 附录 B. 服务器端 React Router

从同构应用的角度来看，迁移到 React Router 4 的难点之一是维护者和整个社区尚未就数据预取模式达成一致的最佳实践。你会在文档中反复看到这种观点；例如，React Router Config 的 README 文件中提到：“有很多人实现带有数据和待处理导航的服务器端渲染的方法，我们还没有确定一个。”

第七章 教你如何在服务器上预取数据，以便在渲染时可用。本附录展示了如何使用 React Router 4 来实现这一点。示例代码可以在 [`mng.bz/S3N0`](http://mng.bz/S3N0) 的仓库中找到，在分支 chapter-7-complete-react-router-4 (`git checkout chapter-7-complete-react-router-4`)。

React Router 4 允许你在渲染之前在服务器上预取数据。在我建议的附录 A 中，你使用 React Router Config 的 `matchRoutes` 来在浏览器中预取数据。你同样在服务器上做同样的事情！这允许你在服务器和浏览器上保持一致性，因为你通过库中提供的实用函数使用相同的方法。这个函数是同步的，与 React Router 3 中的异步路由匹配函数不同。（如果你想要你的 Node.js 应用保持一致性，你可以总是将其包装以返回异步。）以下列表显示了如何更新代码以与 React Router 4 一起工作。

##### 列表 B.1\. 服务器端数据获取——中间件/renderView.jsx

```
//...other code
import { StaticRouter } from 'react-router';                              *1*
import {
  matchRoutes, renderRoutes
} from 'react-router-config';                                             *2*
import routes from '../shared/sharedRoutesv4';                            *2*

export default function renderView(req, res, next) {
  const matches = matchRoutes(routes, req.path);                          *3*
  const context = {};                                                     *4*

  if (matches) {
    const store = initRedux();
    let actions = [];
    matches.map(({match, route}) => {                                     *5*
      const component = route.component;
      if (component) {
        if (component.displayName &&
            component.displayName.toLowerCase().indexOf('connect') > -1
        ) {
          let parentComponent = component.WrappedComponent;
          if (parentComponent.prefetchActions) {
            actions.push(parentComponent.prefetchActions());
          } else if (parentComponent.wrappedComponent &&
     parentComponent.wrappedComponent().prefetchActions) {                *6*
        actions.push(parentComponent.wrappedComponent().prefetchActions());
          }
        } else if (component.prefetchActions) {
          actions.push(component.prefetchActions());
        }
      }
    });
    actions = actions.reduce((flat, toFlatten) => {
      return flat.concat(toFlatten);
    }, []);

    const promises = actions.map((initialAction) => {
      return store.dispatch(initialAction());
    });
    Promise.all(promises).then(() => {
      const app = renderToString(
        <Provider store={store}>
          <StaticRouter
            location={req.url}
            context={context}>                                            *7*
            { renderRoutes(routes) }                                      *8*
          </StaticRouter>
        </Provider>
      );

      if (!context.url) {                                                 *9*
        const html = renderToString(<HTML renderedToStringComponents={app} /
     >);
        res.send(`<!DOCTYPE html>${html}`);
      }
    });
  } else {
    next();
  }
}
```

+   ***1*** 导入依赖项

+   ***2*** 导入依赖项

+   ***3*** 调用 matchRoutes 以找出正在加载哪些组件

+   ***4*** 创建一个上下文对象，用于确定是否存在路由重定向

+   ***5*** 遍历匹配项以便你可以调用预取动作函数。这个方法的大部分内容与第七章相同。第七章。

+   ***6*** 这是代码中的主要区别。因为 App 被两个高阶组件（HOCs）包裹，所以你必须向下检查两级才能访问根组件。

+   ***7*** 在服务器上使用 StaticRouter 而不是 BrowserRouter，因为你不需要历史记录。传递位置和上下文对象。

+   ***8*** 正如 main.jsx 中的那样调用 renderRoutes

+   ***9*** 检查上下文以确保没有重定向
