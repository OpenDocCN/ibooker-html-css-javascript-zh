# 7 添加授权和认证

本章涵盖

+   将身份验证和授权添加到我们的应用程序中，包括 GraphQL API 和我们的前端 React 应用程序

+   使用 JSON Web 令牌 (JWT) 来编码用户身份和权限

+   使用 @auth GraphQL 模式指令在我们的 GraphQL 模式中表达和强制执行授权规则

+   使用 Auth0 作为 JWT 提供者以及 Auth0 React SDK 来为我们的应用程序添加 Auth0 支持

*身份验证*（验证用户的身份）和 *授权*（验证用户可以访问的资源）对于任何应用程序的安全性都是必要的——确保用户拥有他们应有的权限，并保护应用程序的数据和操作，包括前端和后端。到目前为止，我们的前端 React 应用程序和 GraphQL API 对任何人开放，任何人都可以访问所有功能和功能，包括修改、创建和删除数据。

GraphQL 本身对授权没有意见，将其留给开发者选择在他们的应用程序中实施的最合适的方法。在本章中，我们展示了如何使用 JWT、GraphQL 模式指令和 Auth0 在我们的应用程序中实现授权和认证功能。首先，我们将看看在解析器中添加授权检查的简单方法来添加授权到我们的 GraphQL API。然后，我们将探讨如何使用 @auth GraphQL 模式指令与 Neo4j GraphQL 库一起保护我们的 GraphQL API，在模式中添加授权规则。然后，我们添加对 Auth0 授权服务的支持，并看看我们如何可以在我们的应用程序中使用 JSON Web 令牌来编码用户身份和权限。

## 7.1 GraphQL 中的授权：一个简单的方法

让我们首先看看在列表 7.1 中添加授权到 GraphQL API 的一个简单方法，作为起点，仅使用单个静态授权令牌。当 GraphQL 服务器接收到请求时，我们将检查请求授权头中包含的令牌。我们将此令牌传递到 GraphQL 解析器，在那里我们将检查令牌的特定值以确定请求是否正确认证，并且只有在令牌有效时才发送适当的响应。请注意，此示例旨在传达概念，并不代表最佳实践！

列表 7.1 api/naive.js：一个简单的 GraphQL 授权实现

```
const { ApolloServer } = require("apollo-server");

const peopleArray = [
  {
    name: "Bob",
  },
  {
    name: "Lindsey",
  },
];

const typeDefs = /* GraphQL */ `
  type Query {
    people: [Person]
  }

  type Person {
    name: String
  }
`;

const resolvers = {
  Query: {
    people: (obj, args, context, info) => {
      if (
        context &&
        context.headers &&
        context.headers.authorization === "Bearer authorized123"    ❶
      ) {
        return peopleArray;
      } else {
        throw new Error("You are not authorized.");
      }
    },
  },
};

const server = new ApolloServer({
  resolvers,
  typeDefs,
  context: ({ req }) => {
    return { headers: req.headers };                                ❷
  },
});

server.listen().then(({ url }) => {
  console.log(`GraphQL server ready at ${url}`);
});
```

❶ 检查特定的认证令牌值

❷ 将 HTTP 请求头添加到 GraphQL 上下文对象中

我们的 GraphQL 服务器有一个单一的解析器，Query.people，它包括检查授权令牌值的逻辑，该令牌通过上下文对象传递。此令牌来自请求头，并在查询时传递到上下文对象中（见图 7.1）。

![CH07_F01_Lyon](img/CH07_F01_Lyon.png)

图 7.1 我们简单 GraphQL 授权实现的授权流程

让我们试一试。现在我们可以启动 GraphQL 服务器：

```
node naive.js
```

在 Apollo Studio 中，让我们发出一个 GraphQL 查询以查找所有 Person 对象并返回每个对象的名字字段：

```
{
    people {
        name
    }
}
```

由于我们没有包括适当的授权令牌，我们的请求被拒绝，并且我们的结果是错误消息：*您未授权.* 让我们在 GraphQL 请求中添加适当的授权头和我们的授权令牌。我们可以在 Apollo Studio 中通过点击左下角的 *Headers* 并选择 *New header*（键为 *Authorization*，值为 *Bearer authorized123*）来完成此操作：

```
{
    "Authorization": "Bearer authorized123"
}
```

现在，当我们执行相同的 GraphQL 操作——这次将授权令牌附加为请求中的头信息——我们看到我们预期的结果：

```
{
  "data": {
    "people": [
     {
      name: "Bob",
     },
     {
      name: "Lindsey",
     },
    ]
  }
}
```

这种天真方法展示了几个重要概念，例如如何从请求中获取授权头并将其传递到 GraphQL 解析器的上下文对象，以及如何在 Apollo Studio 中添加授权头。然而，这种方法有几个问题，我们不会在现实世界的应用程序中实现：

1.  *我们不验证令牌。* 我们如何知道发起请求的用户是他们所说的那个人，以及他们是否真的拥有令牌中声明的权限？我们只是在相信他们的话！

1.  *我们的授权规则与 GraphQL 解析器中的数据获取逻辑混合在一起。* 这可能看起来像是一个简单的例子可以工作，但想象一下当我们添加更多类型和授权规则时会发生什么——这将很难跟踪和维护。

我们将通过使用加密签名的 JWT 来编码和验证授权头中表达的用户身份和权限来解决第一个问题。我们将通过使用 Neo4j GraphQL 库中的 @auth GraphQL 模式指令来解决第二个问题；通过在我们的模式中添加声明性授权规则，我们有一个单一的真相来源，用于我们的授权规则。

## 7.2 JSON Web Tokens

JSON Web Token，通常简称为 JWT，是一种用于对 JSON 对象进行加密签名的开放标准，可用于各方之间进行可信通信。通过使用公钥/私钥对生成并签名一个紧凑的令牌，以验证该令牌是由持有私钥的一方生成的，因此，可以通过解码它并使用用于签名的私钥的公钥对应物来对令牌中包含的信息的完整性进行加密验证。

JWT（负载）中编码的信息是一系列关于实体的 *声明*，通常是用户。JWT 中的标准声明包括

+   iss—令牌的发行者

+   exp—令牌的过期日期

+   sub—主题，通常是一些 ID，引用了这些声明适用的用户

+   aud—受众，通常在验证 API 时使用

我们还可以向 JWT 添加额外的声明来表示有关用户的信息，例如他们在应用程序中的角色（例如，用户是管理员还是编辑？）或更细粒度的权限，例如读取、创建、更新或删除应用程序中某些类型数据的权限。

许多身份和访问管理服务支持 JWT 标准。如果您选择提供自己的授权服务，它们甚至可以独立使用。在本章中，我们将使用 Auth0 服务。

首先，让我们创建一个 JWT 来编码关于用户的一些声明，然后我们将修改之前的简单 GraphQL API 以验证令牌并确保用户应该被允许访问 GraphQL API。为此，我们将使用在线 JWT 调试器[`jwt.io`](https://jwt.io)。

我们需要一个随机字符串作为签名密钥。稍后，我们将在我们的 GraphQL 服务器中使用这个密钥来验证传入的 JWT：

```
Dpwm9XXKqk809WXjCsOmRSZQ5i5fXw8N
```

在 JWT 调试器的“验证签名”部分输入此值。接下来，我们需要在我们的令牌的有效载荷中添加一些声明（见图 7.2）：

```
{
    "sub": "1234567890",
    "name": "William Lyon",
    "email": "will@grandstack.io",
    "iat": 1638331785
}
```

![CH07_F02_Lyon](img/CH07_F02_Lyon.png)

图 7.2 使用 jwt.io 创建已签名的 JWT

在创建我们的 JWT 后，让我们回到简单的 GraphQL 服务器并添加验证令牌的支持。首先，我们将安装 jsonwebtoken 包：

```
npm install jsonwebtoken
```

接下来，我们将更新解析器逻辑，使用我们的随机客户端密钥解码 JWT，如下所示。

列表 7.2 api/naive.js：在 GraphQL 服务器中验证 JWT

```
const { ApolloServer } = require("apollo-server");
const jwt = require("jsonwebtoken");

const peopleArray = [
  {
    name: "Bob",
  },
  {
    name: "Lindsey",
  },
];

const typeDefs = /* GraphQL */ `
  type Query {
    people: [Person]
  }

  type Person {
    name: String
  }
`;

const resolvers = {
  Query: {
    people: (obj, args, context, info) => {
      if (context.user) {
        return peopleArray;
      } else {
        throw new Error("You are not authorized");
      }
    },
  },
};

const server = new ApolloServer({
  resolvers,
  typeDefs,
  context: ({ req }) => {
    let decoded;
    if (req && req.headers && req.headers.authorization) {
      try {
        decoded = jwt.verify(                  ❶
          req.headers.authorization.slice(7),
          "Dpwm9XXKqk809WXjCsOmRSZQ5i5fXw8N"
        );
      } catch (e) {
        // token not valid
        console.log(e);
      }
    }
    return {
      user: decoded,
    };
  },
});

server.listen().then(({ url }) => {
  console.log(`GraphQL server ready at ${url}`);
});
```

❶ 使用我们的随机客户端密钥验证令牌

如果令牌可以验证，即它是由适当的密钥签名的，那么我们将继续在解析器中获取数据。如果令牌无效，则解析器抛出错误，并且不会获取任何数据（见图 7.3）。

![CH07_F03_Lyon](img/CH07_F03_Lyon.png)

图 7.3 将 JWT 引入我们的授权流程

此示例使用 HS256 算法，这意味着客户端和服务器共享相同的密钥。稍后，当我们切换到 Auth0 作为令牌提供者时，我们将使用更安全的 RS256 算法，其中使用公钥/私钥对代替。

在重启 GraphQL 服务器以应用我们的更改后，我们将打开 Apollo Studio 并将 JWT 令牌添加到授权头中。如果我们尝试不使用令牌或使用无效令牌进行请求，我们会收到以下错误：“您未授权”。这确保了 GraphQL 服务器只执行有效的请求——即包含使用对应公钥的私钥签名的 JWT（见图 7.4）。

![CH07_F04_Lyon](img/CH07_F04_Lyon.png)

图 7.4 在 Apollo Studio 中添加 JWT 作为授权头

之前，我们提到了我们原始授权方法中的两个问题。第一个问题是我们没有一种方式来验证授权令牌。我们通过使用和验证 JWT 解决了这个问题，所以现在，是时候解决我们的混合授权规则了。我们将在 GraphQL 模式中使用指令来声明我们的授权规则，并确保使用 Neo4j GraphQL 库强制执行这些规则。

## 7.3 The @auth GraphQL 模式指令

让我们放下简单的、原始的 GraphQL 服务器示例，回到我们的业务审查应用中，探索如何将授权规则添加到我们的 GraphQL 模式中。正如我们之前通过@cypher 模式指令所看到的，GraphQL 模式指令允许我们在解析 GraphQL 请求时应用一些自定义的服务端逻辑。

Neo4j GraphQL 库包括@auth GraphQL 模式指令，允许定义授权规则以保护 GraphQL 模式中的字段或类型。在我们能够使用@auth 模式指令之前，我们需要指定用于验证 JWT 的方法以及用于验证令牌的密钥。让我们设置一个环境变量，其值为我们的 JWT 密钥：

```
export JWT_SECRET=Dpwm9XXKqk809WXjCsOmRSZQ5i5fXw8N
```

现在，我们需要更新 Neo4j GraphQL 库的配置，指定在验证授权令牌时应使用此令牌，如列表 7.3 所示。为此，我们将读取我们刚刚设置的 JWT_SECRET 环境变量，并将其与我们的类型定义和解析器一起传递到插件对象中。我们还需要安装 graphql-plugin-auth 包以启用与 Neo4j GraphQL 库一起使用授权插件：

```
npm i @neo4j/graphql-plugin-auth

```

列表 7.3 api/index.js：配置 Neo4j GraphQL 库的授权

```
const {
  Neo4jGraphQLAuthJWTPlugin,
} = require("@neo4j/graphql-plugin-auth");

const neoSchema = new Neo4jGraphQL({
  typeDefs,
  resolvers,
  driver,
  plugins: {
    auth: new Neo4jGraphQLAuthJWTPlugin({
      secret: process.env.JWT_SECRET,       ❶
    }),
  },
});
```

❶ 使用密钥验证 JWT。

我们还可以使用 JSON Web Key Set (JWKS) URL 配置 JWT 解码和验证，这是一种比使用共享密钥更安全的方法。当我们使用 Auth0 时，我们将使用此方法配置 Neo4j GraphQL 库中的 JWT 验证，但就目前而言，使用共享密钥配置是可行的，如下一个列表所示。此外，我们还需要传递包含授权头和用户认证令牌的 HTTP 请求对象。

列表 7.4 api/index.js：传递带有认证令牌的请求对象

```
neoSchema.getSchema().then((schema) => {
  const server = new ApolloServer({
    schema,
    context: ({ req }) => ({ req }),   ❶
  });
  server.listen().then(({ url }) => {
    console.log(`GraphQL server ready at ${url}`);
  });
});
```

❶ 将 HTTP 请求对象传递给上下文函数，以便 Neo4j GraphQL 库生成的解析器可以解码 JWT。

### 7.3.1 规则和操作

当使用@auth GraphQL 模式指令时，我们需要考虑两个方面的内容：规则和操作。这两个都作为@auth 指令的参数指定。可以定义几种授权规则，具体取决于我们如何精确地保护字段和类型。也许我们只想让已登录的用户访问某些字段。或者，也许我们只想让我们的应用中的管理员能够编辑某些类型。或者，也许只有评论的作者才能更新它。这些都是可以使用@auth 指令指定的授权规则。以下是与@auth 模式指令一起可用的规则类型：

+   isAuthenticated 是我们能使用的最基本规则。访问受保护类型或字段的 GraphQL 请求必须包含有效的 JWT。

+   roles 规则指定一个或多个角色，这些角色必须包含在 JWT 负载中。

+   allow 规则将比较 JWT 负载中的值与数据库中的值，确保它们在有效请求中相等。

+   bind 规则用于确保在提交到数据库之前，JWT 负载中的值与 GraphQL 突变操作中的值相等。

+   Where 规则与 allow 规则类似，都使用 JWT 负载中的值；然而，它不是检查相等性，而是在生成的数据库查询中添加了一个谓词来过滤符合规则的数据。

当使用@auth 指令添加规则时，可以可选地指定一个或多个操作，指示规则应用于哪些操作。如果没有指定操作，则规则将应用于所有操作。以下操作可以使用：

+   创建

+   读取

+   更新

+   删除

+   连接

+   断开连接

让我们看看@auth 指令的实际应用，以帮助我们理解这些规则和操作如何在我们的业务审查应用中使用。

### 7.3.2 isAuthenticated 授权规则

isAuthenticated 规则可以在 GraphQL 类型或字段上使用，表示要访问该类型或字段，GraphQL 请求必须附加有效的 JWT。JWT 的有效性由是否可以使用 JWT 密钥值作为密钥进行验证来决定——这表明令牌是由私钥签署并由适当的权威机构创建的。isAuthenticated 逻辑用于控制应用中需要用户认证但不需要任何特定权限的区域——用户只需要是一个认证用户。

对于我们的业务审查应用，假设我们希望允许任何用户搜索企业，但只向认证用户显示 averageStars 字段，以鼓励用户使用我们的应用进行注册。让我们更新我们的 GraphQL 类型定义以包含此授权规则。

列表 7.5 api/index.js：更新 Business 类型

```
type Business {
  businessId: ID!
  waitTime: Int! @computed
  averageStars: Float
    @auth(rules: [{ isAuthenticated: true }])            ❶
    @cypher(
      statement: "MATCH (this)<-[:REVIEWS]-(r:Review) RETURN avg(r.stars)"
    )
  recommended(first: Int = 1): [Business]
    @cypher(
      statement: """
      MATCH (this)<-[:REVIEWS]-(:Review)<-[:WROTE]-(u:User)
      MATCH (u)-[:WROTE]->(:Review)-[:REVIEWS]->(rec:Business)
      WITH rec, COUNT(*) AS score
      RETURN rec ORDER BY score DESC LIMIT $first
      """
    )
  name: String!
  city: String!
  state: String!
  address: String!
  location: Point!
  reviews: [Review!]! @relationship(type: "REVIEWS", direction: IN)
  categories: [Category!]!
    @relationship(type: "IN_CATEGORY", direction: OUT)
}
```

❶ 我们使用@auth 模式指令添加 isAuthenticated 规则来保护 averageStars 字段。

我们现在已经保护了 averageStars 字段，这意味着我们需要在包含该字段的任何 GraphQL 请求的头部包含一个有效的 JWT，如下一个列表所示。

列表 7.6 在 GraphQL 查询中请求受保护的 averageStars 字段

```
{
  businesses {
    name
    categories {
      name
    }
    averageStars
  }
}
"errors": [
    {
      "message": "Unauthenticated",
```

如果我们不将 averageStars 字段包含在选择集中，我们的请求将返回预期的字段。尝试发送无效令牌和包含或不包含 averageStars 字段的请求。在这里，我们在请求的授权头中包含一个有效令牌，以便我们可以查看 averageStars 字段：

```
{
"Authorization":"Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxM
jM0NTY3ODkwIiwibmFtZSI6IldpbGxpYW0gTHlvbiIsImVtYWlsIjoid2lsbEBncmFuZHN0YWNr
LmlvIiwiaWF0IjoxNTE2MjM5MDIyfQ.Y37P8OF_qMamIcZldi89Nm0YQdF4v91iHQWrNu0jtBk"
}
```

### 7.3.3 角色授权规则

角色规则允许我们为一种或多种操作添加所需权限类型的必要条件。除了拥有一个有效的签名令牌外，要访问受角色规则保护的字段或类型，令牌必须包含在令牌中编码的角色声明中指定的一个角色。让我们在下一个列表中查看一个示例。

列表 7.7 api/index.js：使用角色授权规则保护用户类型

```
extend type User @auth(rules: [{roles: ["admin"]}])
```

在这里，我们使用 extend GraphQL 关键字在我们的类型定义中添加额外的指令或字段到已经定义在我们的类型定义中的类型。使用此语法等同于在首次定义类型时包含指令，但使用类型扩展允许我们将我们的类型定义分成多个文件，如果需要的话（见图 7.5）。

![CH07_F05_Lyon](img/CH07_F05_Lyon.png)

图 7.5 使用@auth GraphQL 架构指令的授权流程

现在，任何访问用户类型的 GraphQL 操作都必须具有管理员角色，包括任何遍历到用户的操作，如下一个列表所示。

列表 7.8 查询用户信息的 GraphQL

```
query  {
  businesses(where: {name: "Neo4j"}) {
    name
    categories {
      name
    }
    address
    reviews {
      text
      stars
      date
      user {
        name
      }
    }

  }
}
```

执行前面的查询将导致以下错误消息，因为我们的令牌不包括管理员角色：

```
"errors": [
  {
    "message": "Forbidden"
  }
]
```

我们需要在令牌中的声明中包含角色。返回到在线 JWT 构建器[`jwt.io`](https://jwt.io)，并将角色数组添加到声明中：

```
{
  "sub": "1234567890",
  "name": "William Lyon",
  "email": "will@grandstack.io",
  "iat": 1516239022,
  "roles": ["admin"]
}
```

现在，如果我们使用这个新的 JWT 更新 Apollo Studio 中用于授权头的令牌，并再次运行 GraphQL 查询，我们将能够访问用户信息。

记住，如果我们没有在添加授权规则时指定特定操作（例如，创建、读取和更新），那么该规则将适用于包含所讨论类型或字段的任何操作。如果我们想限制授权规则只应用于某些操作，我们必须在定义规则时明确指定它们，使用@auth 架构指令。

我们之前检查的前两个@auth 规则（isAuthenticated 和 roles）仅使用了 JWT 有效载荷中的值（或者，在 isAuthenticated 的情况下，简单地，有效令牌的存在）。接下来我们将探索的三个规则将使用数据库中的值（我们的应用程序数据）来执行授权规则。

### 7.3.4 允许授权规则

之前，我们创建了一个规则，通过要求认证用户具有管理员角色来保护用户类型。让我们添加一个额外的授权规则，允许用户读取他们自己的用户信息。

列表 7.9 api/index.js：允许用户访问他们自己的用户信息

```
extend type User
  @auth(
    rules: [
      { operations: [READ], allow: { userId: "$jwt.sub" } }
      { roles: ["admin"] }
    ]
  )
```

注意，我们已经将我们的新允许规则与现有的角色规则结合起来。由于规则参数接受一个规则数组，这些规则充当或逻辑。要访问用户类型，请求的 JWT 中的声明必须至少符合规则数组中定义的授权规则之一。在这种情况下，认证用户必须是管理员或与请求用户的 userId 匹配。为了测试我们的新规则，让我们为用户 Jenny 创建一个新的 JWT，其有效载荷如下：

```
{
  "sub": "u3",
  "name": "Jenny",
  "email": "jenny@grandstack.io",
  "iat": 1516239022,
  "roles": [
    "user"
  ]
}
```

我们可以使用 web 界面在 [jwt.io](https://jwt.io/) 中创建此 JWT；只需确保在签名令牌时使用相同的 JWT 密钥：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1MyIsIm5hbWUiOiJKZW5ueSIsIm
VtYWlsIjoiamVubnlAZ3JhbmRzdGFjay5pbyIsImlhdCI6MTUxNjIzOTAyMiwicm9sZXMiOlsid
XNlciJdfQ.ctal5qgshR4-hqchxsYxxHVGPsE0JNxydGy3Pga27nA
```

现在，使用此 JWT 以用户 Jenny 的身份执行 GraphQL 请求，我们可以在以下列表中查询此用户的详细信息。

列表 7.10 查询单个用户的详细信息

```
query {
  users(where: { name: "Jenny" }) {
    name
    userId
  }
}
```

由于我们的 JWT 中的 sub 声明与请求用户的 userId 匹配，我们看到了结果数据：

```
{
  "data": {
    "users": [
      {
        "name": "Jenny",
        "userId": "u3"
      }
    ]
  }
}
```

在这种情况下，我们的 GraphQL 查询正在使用 where 参数过滤用户，以确保我们只查询我们有权访问的数据。如果我们请求我们没有访问权限的用户数据会发生什么？例如，如果我们请求所有用户信息会怎样？

列表 7.11 查询所有用户详细信息

```
query {
  users {
    name
    userId
  }
}
```

由于我们的用户不是管理员，并且我们请求的用户对象中 userId 将不会与我们的 JWT 中的 sub 声明匹配，我们将看到禁止错误。

让我们看看如何通过自动过滤查询结果，只为认证用户有权访问的数据，来避免这些类型的错误。为了实现这一点，我们将使用一个 where 授权规则。这意味着客户端不必担心添加过滤器以避免请求认证用户无权访问的数据。

### 7.3.5 Where 授权规则

在上一节中，我们使用了一个允许授权规则来确保用户只能访问他们自己的数据。然而，这种方法存在问题，因为它将负担放在了客户端，需要客户端添加适当的过滤器以确保 GraphQL 请求没有请求用户无权查看的数据。让我们在下一个列表中使用一个 where 规则，这样我们就不必担心请求认证用户无权查看的数据。

列表 7.12 api/index.js：使用 where 授权规则

```
extend type User
  @auth(
    rules: [
      { operations: [READ], where: { userId: "$jwt.sub" } }
      { operations: [CREATE, UPDATE, DELETE], roles: ["admin"] }
    ]
  )
```

我们仍然需要确保只有管理员用户能够创建、更新或删除用户，因此我们将这些操作添加到角色规则中，如列表 7.13 所示。现在，每当执行对用户类型的读取请求时，生成的数据库查询中都会添加一个谓词，以过滤出当前认证的用户，将 JWT 的 sub 声明与数据库中 userId 节点属性值相匹配。

列表 7.13 GraphQL 查询请求用户信息

```
query {
  users {
    name
    userId
  }
}
{
  "data": {
    "users": [
      {
        "name": "Jenny",
        "userId": "u3"
      }
    ]
  }
}
```

如果我们检查发送到数据库的生成的 Cypher 查询，我们可以看到附加的谓词，确保数据库中节点的 userId 属性值与 JWT 的 sub 值相匹配，如下列所示。

列表 7.14 生成的 Cypher 查询

```
MATCH (this:User)
WHERE this.userId IS NOT NULL AND this.userId = "u3"
RETURN this { .name, .userId } as this
```

### 7.3.6 绑定授权规则

绑定规则用于在创建或更新数据时强制执行授权规则，也可以用于跨关系。在列表 7.15 中，让我们使用绑定规则来确保在创建或更新审查时，它们与当前认证的用户相关联。我们不希望允许用户伪造其他用户的审查！

列表 7.15 api/index.js：使用绑定授权规则

```
extend type Review
  @auth(
    rules: [
      {
        operations: [CREATE, UPDATE]
        bind: { user: { userId: "$jwt.sub" } }
      }
    ]
  )
```

让我们在下一个列表中编写一个 GraphQL 突变来创建一个新的商业审查。

列表 7.16 创建新的审查

```
mutation {
  createReviews(
    input: {
      business: { connect: { where: { node: { businessId: "b10" } } } }
      date: "2022-01-22"
      stars: 5.0
      text: "Love the Philtered Soul!"
      user: { connect: { where: { node: { userId: "u3" } } } }
    }
  ) {
    reviews {
      business {
        name
      }
      text
      stars
    }
  }
}
```

这没有问题执行，将审查节点添加到数据库中并建立适当的关系：

```
{
  "data": {
    "createReviews": {
      "reviews": [
        {
          "business": {
            "name": "Philz Coffee"
          },
          "text": "Love the Philtered Soul!",
          "stars": 5
        }
      ]
    }
  }
}
```

然而，如果连接审查与当前认证用户（在这种情况下，userId 为 u3 的用户）的操作失败，尝试连接到用户 u1 或没有任何用户，那么突变操作将失败，并返回一个禁止错误。

请务必参考文档以获取更多关于如何使用@auth GraphQL 模式指令将复杂授权规则添加到您的 GraphQL API 的示例：[neo4j.com/docs/graphql-manual/current/auth](https://neo4j.com/docs/graphql-manual/3.0/auth/)。

到目前为止，我们一直在使用 JWT Builder 网站创建我们的 JWT；这对于开发和测试来说是不错的，但我们还需要为生产环境准备更多。

## 7.4 Auth0：JWT as a service

Auth0 是一个认证和授权服务，可以使用多种方法认证用户，例如社交登录或电子邮件和密码。它还包括维护用户数据库的功能，我们可以用它来定义用户的规则和权限。我喜欢将 Auth0 视为 JWT-as-a-service 提供商。尽管 Auth0 有很多功能和服务，但最终，我通常只对获取用户的认证令牌（作为 JWT）并使用它来授权我的 API 和应用程序感兴趣。

Auth0 也是一个很好的学习和发展的服务，因为它提供免费层，无需信用卡即可注册。在本节中，我们将配置 Auth0 以保护我们的 API，然后使用 Auth0 React SDK 将 Auth0 支持添加到我们的应用程序中。您可以在[`auth0.com`](https://auth0.com)免费创建 Auth0 账户。

### 7.4.1 配置 Auth0

一旦我们登录到 Auth0，我们将在我们的 Auth0 租户中创建一个 API 和一个应用程序（见图 7.6）。首先，创建 API 并为其命名。

![CH07_F06_Lyon](img/CH07_F06_Lyon.png)

图 7.6 在 Auth0 中创建 API

我们不会在我们的应用程序中使用此功能，但我们可以选择性地为我们的 API 启用基于角色的访问控制（RBAC）（见图 7.7）。这将允许我们向 Auth0 生成的 JWT 添加细粒度的权限，这些权限可以用于基于角色的访问控制（@auth）模式指令规则。

![CH07_F07_Lyon](img/CH07_F07_Lyon.png)

图 7.7 在 Auth0 中为我们的 API 启用 RBAC

如果我们启用 RBAC，我们还需要定义我们 API 中可以使用的所有可能的权限。我已经为我们的 API 中创建、读取、更新和删除业务添加了必要的权限（见图 7.8）。

![CH07_F08_Lyon](img/CH07_F08_Lyon.png)

图 7.8 在 Auth0 中的 API 中添加权限

您可以在 Neo4j GraphQL 库文档中了解更多关于使用角色授权规则启用 RBAC 的信息：[`mng.bz/5Q5z`](http://mng.bz/5Q5z)。

现在，我们需要在 Auth0 控制台中创建我们的应用程序。选择 *创建应用程序*。我们需要为我们的应用程序选择一个名称——我使用了 *Business Reviews*。我们还被要求选择应用程序类型。由于我们正在构建一个 React 应用程序，请选择 *单页 Web 应用程序*，然后点击 *创建* 按钮。

我们将保留大多数默认设置，但我们必须更新 *允许回调 URL* 和 *允许注销 URL* 的条目。在我们的新应用程序的 *设置* 选项卡下，将 http://localhost:3000 添加到每个文本框中，然后选择 *保存更改*。

接下来，我们需要更新我们的 GraphQL API 中的配置，指定用于验证 Auth0 生成的 JWT 的方法，如图 7.17 列表所示。到目前为止，我们一直在使用存储在环境变量（JWT_SECRET）中的简单密钥来验证 JWT。这对于本地开发和测试来说是不错的，但现在我们使用 Auth0 并准备将我们的应用程序部署到网络上，我们希望使用更安全的方法。

导航到 *高级设置* 然后选择 *端点*。查找 JWKS URL，并复制此值。然后，在我们的 GraphQL API 代码中，将用于验证 JWT 的方法更改为使用 Auth0 应用程序的 URL 的 jwksEndpoint。这将允许我们的 GraphQL API 从 Auth0 获取公钥以验证令牌，这是一种比使用共享密钥更安全的验证方法。

列表 7.17 api/index.js：使用 Auth0 JSON Web Key Set (JWKS) 端点

```
const {
  Neo4jGraphQLAuthJWKSPlugin,                                             ❶
} = require("@neo4j/graphql-plugin-auth");

...

const neoSchema = new Neo4jGraphQL({
  typeDefs,
  resolvers,
  driver,
  plugins: {
    auth: new Neo4jGraphQLAuthJWKSPlugin({
      jwksEndpoint: "https://grandstack.auth0.com/.well-known/jwks.json", ❷
    }),
  },
});
```

❶ 现在我们使用 Neo4jGraphQLAuthJWKSPlugin 类。

❷ 请确保使用在 Auth0 高级设置中找到的端点。

我们现在准备好开始将 Auth0 集成到我们的 React 应用程序中。

### 7.4.2 Auth0 React

首先，让我们安装 Auth0 SDK for React。这个包包括为添加 Auth0 支持到任何 React 应用程序而设计的特定于 React 的集成。

我们将使用 npm 安装 auth0-react 库。首先，确保你处于 web-react 目录中：

```
npm install @auth0/auth0-react
```

现在，让我们在下一个列表中将初始 Auth0 设置添加到我们的 React 应用程序中。

列表 7.18 web-react/src/index.js：添加 Auth0 提供者组件

```
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import {
  ApolloClient,
  InMemoryCache,
  ApolloProvider,
  makeVar,
} from "@apollo/client";
import { Auth0Provider } from "@auth0/auth0-react";   ❶

export const starredVar = makeVar([]);

const client = new ApolloClient({
  uri: "http://localhost:4000",
  cache: new InMemoryCache({
    typePolicies: {
      Business: {
        fields: {
          isStarred: {
            read(_, { readField }) {
              return starredVar().includes(readField("businessId"));
            },
          },
        },
      },
    },
  }),
});

ReactDOM.render(
  <React.StrictMode>
    <Auth0Provider                                   ❷
      domain="grandstack.auth0.com"
      clientId="4xw3K3cjvw0hyT4Mjp4RuOVSxvVYcOFF"
      redirectUri={window.location.origin}
      audience="https://reviews.grandstack.io"
    >
      <ApolloProvider client={client}>
        <App />
      </ApolloProvider>
    </Auth0Provider>
  </React.StrictMode>,
  document.getElementById("root")
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```

❶ 导入 Auth0 Provider 组件。

❷ 将我们的 App 组件包裹在 Auth0Provider 组件中。

我们添加 Auth0Provider 组件，通过包裹我们的 ApolloProvider 和 App 组件将其注入到组件层次结构中。我们还包含了我们的 Auth0 租户、应用程序和 API 的域、客户端 ID 和受众信息，这些信息可以在 Auth0 仪表板中找到。

在接下来的列表中，我们将使用 Auth0 将登录和注销按钮添加到我们的应用程序中。点击登录按钮将引导用户通过 Auth0 的认证流程。

列表 7.19 web-react/src/App.js：添加登录和注销按钮

```
import React, { useState } from "react";
import BusinessResults from "./BusinessResults";
import { gql, useQuery } from "@apollo/client";
import { useAuth0 } from "@auth0/auth0-react";                        ❶

const GET_BUSINESSES_QUERY = gql`
  query BusinessesByCategory($selectedCategory: String!) {
    businesses(
      where: { categories_SOME: { name_CONTAINS: $selectedCategory } }
    ) {
      businessId
      name
      address
      categories {
        name
      }
      isStarred @client
    }
  }
`;

function App() {
  const [selectedCategory, setSelectedCategory] = useState("");
  const { loginWithRedirect, logout, isAuthenticated } = useAuth0();  ❷

  const { loading, error, data, refetch } = useQuery(
    GET_BUSINESSES_QUERY,
    {
      variables: { selectedCategory },
    }
  );

  if (error) return <p>Error</p>;
  if (loading) return <p>Loading...</p>;

  return (
    <div>
      {!isAuthenticated && (                                          ❸
        <button onClick={() => loginWithRedirect()}>Log In</button>
      )}
      {isAuthenticated && (
        <button onClick={() => logout()}>Log Out</button>
      )}
      <h1>Business Search</h1>
      <form>
        <label>
          Select Business Category:
          <select
            value={selectedCategory}
            onChange={(event) => setSelectedCategory(event.target.value)}
          >
            <option value="">All</option>
            <option value="Library">Library</option>
            <option value="Restaurant">Restaurant</option>
            <option value="Car Wash">Car Wash</option>
          </select>
        </label>
        <input type="button" value="Refetch" onClick={() => refetch()} />
      </form>

      <BusinessResults businesses={data.businesses} />
    </div>
  );
}

export default App;
```

❶ 导入 useAuth0 React 钩子。

❷ 访问函数以处理认证流程和用户数据。

❸ 添加登录和注销按钮。

Auth0 React 包包括一个 useAuth0 钩子，它为我们提供了可以触发认证流程、确定用户是否当前已认证以及访问用户信息的函数。现在，我们有一个带有登录选项的按钮，或者如果我们已经登录，我们就有注销的选项。

点击“登录”，我们会看到一系列登录选项，包括 GitHub、Google、Twitter 或电子邮件和密码认证（见图 7.9）。使用认证服务的优点之一是我们实际上不需要关心认证流程的具体细节，因为这是由 Auth0 处理的。

![CH07_F09_Lyon](img/CH07_F09_Lyon.png)

图 7.9 通过 Auth0 的登录选项

注意使用了由 useAuth0 钩子提供的 isAuthenticated 变量。一旦他们登录，我们也可以访问用户信息。现在，让我们添加一个配置文件组件，在用户登录后显示他们的姓名和头像图片。在 web-react/src 目录中创建一个新文件 Profile.js，如下所示。

列表 7.20 web-react/src/Profile.js：添加用户配置文件组件

```
import { useAuth0 } from "@auth0/auth0-react";

const Profile = () => {
  const { user, isAuthenticated } = useAuth0();
  return (
    isAuthenticated && (
      <div style={{ padding: "10px" }}>
        <img
          src={user.picture}
          alt="User avatar"
          style={{ width: "40px" }}
        />
        <strong>{user.name}</strong>
      </div>
    )
  );
};

export default Profile;
```

现在，让我们将这个配置文件组件包含到我们的主 App 组件中，以便在用户登录时显示配置文件。

列表 7.21 web-react/src/App.js：添加配置文件组件

```
import Profile from "./Profile";

...

      {!isAuthenticated && (
        <button onClick={() => loginWithRedirect()}>Log In</button>
      )}
      {isAuthenticated && <button onClick={() => logout()}>Log Out</button>}
      <Profile />                  ❶
      <h1>Business Search</h1>

...
```

❶ 添加配置文件组件

好的，我们现在可以让用户登录到我们的应用程序并显示他们的配置文件信息，如图 7.10 所示，但我们如何向我们的 GraphQL API 发送认证请求呢？当我们使用 Apollo Studio 时，我们看到了需要在 GraphQL 请求中附加授权令牌作为头部。

![CH07_F10_Lyon](img/CH07_F10_Lyon.png)

图 7.10 我们 React 应用程序的认证视图

为了访问令牌，我们将使用 auth0-react 库中的 getAccessTokenSilently 函数。然后，我们将把这个令牌附加到 Apollo Client 实例中，如下一列表所示。

列表 7.22 web-react/src/index.js：在我们的 GraphQL 请求中添加访问令牌

```
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import {
  ApolloClient,
  InMemoryCache,
  ApolloProvider,
  makeVar,
  createHttpLink,
} from "@apollo/client";
import { setContext } from "@apollo/client/link/context";
import { Auth0Provider, useAuth0 } from "@auth0/auth0-react";

export const starredVar = makeVar([]);

const AppWithApollo = () => {                                   ❶
  const { getAccessTokenSilently, isAuthenticated } = useAuth0();

  const httpLink = createHttpLink({
    uri: "http://localhost:4000",
  });

  const authLink = setContext(async (_, { headers }) => {       ❷
    // Only try to fetch access token if user is authenticated
    const accessToken = isAuthenticated
      ? await getAccessTokenSilently()
      : undefined;
    if (accessToken) {
      return {
        headers: {
          ...headers,
          authorization: accessToken ? `Bearer ${accessToken}` : "",
        },
      };
    } else {
      return {
        headers: {
          ...headers,
          // We could set additional headers here or a "default"
          // authorization header if needed
        },
      };
    }
  });

  const client = new ApolloClient({
    link: authLink.concat(httpLink),
    cache: new InMemoryCache({
      typePolicies: {
        Business: {
          fields: {
            isStarred: {
              read(_, { readField }) {
                return starredVar().includes(readField("businessId"));
              },
            },
          },
        },
      },
    }),
  });

  return (
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  );
};

ReactDOM.render(
  <React.StrictMode>
    <Auth0Provider
      domain="grandstack.auth0.com"
      clientId="4xw3K3cjvw0hyT4Mjp4RuOVSxvVYcOFF"
      redirectUri={window.location.origin}
      audience="https://reviews.grandstack.io"
    >
      <AppWithApollo />                                        ❸
    </Auth0Provider>
  </React.StrictMode>,
  document.getElementById("root")

reportWebVitals(); 
```

❶ 创建一个包装组件，该组件将负责添加授权令牌。

❷ 使用 Apollo Client 的 setContext 函数将 JWT 添加到 GraphQL 请求中。

❸ 将 AppWithApollo 组件注入到 React 组件层次结构中。

现在，如果用户经过身份验证，每次对 GraphQL API 的请求都会在头部包含授权令牌。我们可以通过打开浏览器开发者工具并检查 GraphQL 网络请求（见图 7.11）来验证这一点。

![CH07_F11_Lyon](img/CH07_F11_Lyon.png)

图 7.11 在浏览器开发者工具窗口中查看附加到 GraphQL 请求的授权头部

我们可以复制这个令牌并使用 jwt.io 来解码其有效载荷。以下是我的解码令牌的样子：

```
{
  "iss": "https://grandstack.auth0.com/",
  "sub": "github|1222454",
  "aud": [
    "https://reviews.grandstack.io",
    "https://grandstack.auth0.com/userinfo"
  ],
  "iat": 1599684745,
  "exp": 1599771145,
  "azp": "4xw3K3cjvw0hyT4Mjp4RuOVSxvVYcOFF",
  "scope": "openid profile email"
}
```

当然，我们的应用程序看起来没有任何不同，因为我们没有在我们的 GraphQL 查询中请求任何受保护的字段。让我们在用户登录时，将受 isAuthenticated 规则保护的 averageStars 字段添加到 GraphQL 查询中。

列表 7.23 web-react/src/App.js：在选择集中包含 averageStars 字段

```
function App() {
  const [selectedCategory, setSelectedCategory] = useState("");
  const { loginWithRedirect, logout, isAuthenticated } = useAuth0();

  const GET_BUSINESSES_QUERY = gql`
  query BusinessesByCategory($selectedCategory: String!) {
    businesses(
      where: { categories_SOME: { name_CONTAINS: $selectedCategory } }
    ) {
      businessId
      name
      address
      categories {
        name
      }
      ${isAuthenticated ? "averageStars" : ""}    ❶
      isStarred @client
    }
  }
`;

  const { loading, error, data, refetch } = useQuery(
    GET_BUSINESSES_QUERY,
    {
      variables: { selectedCategory },
    }
  );

  if (error) return <p>Error</p>;
  if (loading) return <p>Loading...</p>;
```

❶ 当用户经过身份验证时添加 averageStars 字段。

现在我们将更新 BusinessResults 组件，以便在用户经过身份验证时包含 averageStars。

列表 7.24 web-react/src/BusinessResults.js：显示 averageStars 字段

```
import React from "react";
import { starredVar } from "./index";
import { useAuth0 } from "@auth0/auth0-react";

function BusinessResults(props) {
  const { businesses } = props;
  const starredItems = starredVar();
  const { isAuthenticated } = useAuth0();

  return (
    <div>
      <h2>Results</h2>
      <table>
        <thead>
          <tr>
            <th>Star</th>
            <th>Name</th>
            <th>Address</th>
            <th>Category</th>
            {isAuthenticated ? <th>Average Stars</th> : null}        ❶
          </tr>
        </thead>
        <tbody>
          {businesses.map((b) => (
            <tr key={b.businessId}>
              <td>
                <button
                  onClick={() =>
                    starredVar([...starredItems, b.businessId])
                  }
                >
                  Star
                </button>
              </td>

              <td style={b.isStarred ? { fontWeight: "bold" } : null}>
                {b.name}
              </td>
              <td>{b.address}</td>
              <td>
                {b.categories.reduce(
                  (acc, c, i) => acc + (i === 0 ? " " : ", ") + c.name,
                  ""
                )}
              </td>
              {isAuthenticated ? <td>{b.averageStars}</td> : null}   ❷
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}

export default BusinessResults;
```

❶ 仅当用户经过身份验证时，才添加“平均星级”标题。

❷ 当用户经过身份验证时显示平均星级值。

现在，我们只有在用户经过身份验证时才会看到每个企业的平均星级。我们已经将身份验证和授权添加到我们的应用程序中，并添加了对 Auth0 的支持。现在，我们相信我们的应用程序是安全的，我们将在下一章中查看如何部署我们的应用程序和数据库。

## 7.5 练习

1.  创建一个新的查询字段 qualityBusinesses，它使用 @cypher 架构指令来返回每个企业至少有两个评论，每个评论至少有四个星级的业务。使用角色规则和 @auth 架构指令来保护此字段，要求分析师角色。创建一个包含此角色的声明的 JWT，并使用 Apollo Studio 来查询这个新的 qualityBusinesses 字段。

1.  在本章中，我们使用 GraphQL 变更来创建新的企业评论。更新 React 应用程序以包括一个表单，允许当前经过身份验证的用户创建新的企业评论。

## 摘要

+   可以使用 @auth GraphQL 架构指令在 GraphQL 模式中以声明方式表达授权规则。

+   JWT 是一种用于编码和传输 JSON 对象的标准，通常用于 Web 应用程序中的授权令牌，如 GraphQL API。

+   Auth0 是一种身份和访问管理服务，可用于处理 JWT 生成和用户认证。Auth0 可以集成到 React 应用程序中，使用 Auth0 React SDK。
