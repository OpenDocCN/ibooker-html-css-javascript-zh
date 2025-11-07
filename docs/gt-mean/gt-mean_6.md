## 附录 C. 处理所有视图

*本附录涵盖*

+   从所有视图（除了主页）中移除数据

+   将数据移动到控制器中

第四章 讨论了设置控制器和视图以创建静态、可点击的原型。该章节更详细地介绍了“如何”和“为什么”，因此本附录专注于结果。

## 将数据从视图移动到控制器

此过程的一部分包括将数据从视图移动回 MVC 流程，即从视图移动到控制器。第四章中的示例处理了 Loc8r 主页中的此任务，但还需要对其他页面执行此操作。从详情页面开始。

#### 详情页面

详情页面是页面中最大、最复杂的，数据需求也最多。第一步是设置控制器。

##### 设置控制器

此页面的控制器称为 `locationInfo`，位于 `app_server/controllers` 中的 `locations.js` 文件。当您在视图中分析数据并将其整理成 JavaScript 对象时，您的控制器将类似于以下列表。

##### 列表 C.1\. `locationInfo` 控制器

```
const locationInfo = function(req, res){
  res.render('location-info', {
    title: 'Starcups',
    pageHeader: {title: 'Starcups'},
    sidebar: {
      context: 'is on Loc8r because it has accessible wifi and space to sit
      down with your laptop and get some work done.',
      callToAction: 'If you\'ve been and you like it - or if you don\'t -
      please leave a review to help other people just like you.'
    },
    location: {
      name: 'Starcups',
      address: '125 High Street, Reading, RG6 1PS',
      rating: 3,
      facilities: ['Hot drinks', 'Food', 'Premium wifi'],
      coords: {lat: 51.455041, lng: -0.9690884},           *1*
      openingTimes: [{                                     *2*
        days: 'Monday - Friday',
        opening: '7:00am',
        closing: '7:00pm',
        closed: false
      },{
        days: 'Saturday',
        opening: '8:00am',
        closing: '5:00pm',
        closed: false
      },{
        days: 'Sunday',
        closed: true
      }],
      reviews: [{                                          *3*
        author: 'Simon Holmes',
        rating: 5,
        timestamp: '16 July 2013',
        reviewText: 'What a great place. I can\'t say enough good things
        about it.'
      },{
        author: 'Charlie Chaplin',
        rating: 3,
        timestamp: '16 June 2013',
        reviewText: 'It was okay. Coffee wasn\'t great, but the wifi was
        fast.'
      }]
    }
  });
};
```

+   ***1* 包含用于在 Google 地图图像中使用的纬度和经度坐标**

+   ***2* 添加了开放时间数组，允许不同日期有不同的数据**

+   ***3* 存储其他用户留下的评论的数组**

注意发送的纬度和经度。您可以从 [`www.where-am-i.net`](https://www.where-am-i.net) 获取您的当前纬度和经度。您可以从 [`www.latlong.net/convert-address-to-lat-long.html`](https://www.latlong.net/convert-address-to-lat-long.html) 将地址地理编码——即获取其纬度和经度。您的视图将使用 `lat` 和 `lng` 来显示正确位置的 Google 地图图像，因此在原型阶段这样做是值得的。

##### 更新视图

由于这个页面是最复杂、数据最丰富的页面，因此可以合理地推断它将拥有最大的视图模板。您已经看到了主页布局中的大多数技术细节，例如循环数组、引入包含文件以及定义和调用混入。不过，在这个模板中还有几样额外的东西需要注意，这两者都已被注释并用粗体突出显示。

首先，此模板使用了一个 `if`-`else` 条件语句。这个语句看起来像是没有花括号的 JavaScript。其次，模板使用 JavaScript `replace` 函数将评论文本中的所有换行符替换为 `<br/>` 标签。您通过使用简单的正则表达式，查找文本中所有 `\n` 字符的出现来完成此操作。以下列表显示了 `location-info.pug` 视图模板的完整内容。

##### 列表 C.2\. `location-info.pug` 视图模板位于 `app_server/views`

```
extends layout
include _includes/sharedHTMLfunctions                                   *1*
block content
  .row.banner
    .col-12
      h1= pageHeader.title
  .row
    .col-12.col-lg-9
      .row
        .col-12.col-md-6
          p.rating
            +outputRating(location.rating)                              *2*
          p 125 High Street, Reading, RG6 1PS
          .card.card-primary
            .card-block
              h2.card-title Opening hours
              each time in location.openingTimes                        *3*
                p.card-text                                             *3*
                  | #{time.days} :                                      *3*
                  if time.closed                                        *3*
                    | closed                                            *3*
                  else                                                  *3*
                    | #{time.opening} - #{time.closing}
          .card.card-primary
            .card-block
              h2.card-title Facilities
              each facility in location.facilities
                span.badge.badge-warning
                  i.fa.fa-check
                  | &nbsp;#{facility}
                | &nbsp;
        .col-12.col-md-6.location-map
          .card.card-primary
            .card-block
              h2.card-title Location map
              img.img-fluid.rounded(src=`http://maps.googleapis.com/
              maps/api/staticmap?center=${location.coords.lat},
              ${location.coords.lng}&zoom=17&size=400x350&sensor=
              false&markers=${location.coords.lat},${location.coords.
               lng}&key={googleAPIKey}&scale=2`)                      *4*
      .row
        .col-12
          .card.card-primary.review-card
            .card-block
              a.btn.btn-primary.float-right(href='/location/review/new')
              Add review
              h2.card-title Customer reviews
              each review in location.reviews                           *5*
                .row.review
                  .col-12.no-gutters.review-header
                    span.rating
                      +outputRating(review.rating)                      *5*
                    span.reviewAuthor #{review.author}
                    small.reviewTimestamp #{review.timestamp}
                  .col-12
                    p !{(review.reviewText).replace(/\n/g, '<br/>')}    *6*
    .col-12.col-lg-3
      p.lead #{location.name} #{sidebar.context}
      p= sidebar.callToAction
```

+   ***1* 引入了包含 `sharedHTMLfunctions` 的文件，其中包含 `outputRating` 混入**

+   ***2* 调用`outputRating`混合，传递当前位置的评分**

+   ***3* 遍历开放时间数组，使用内联 if-else 语句检查位置是否关闭**

+   ***4* 构建 Google Maps 静态图像的 URL，使用 ES2015 模板字面量插入 lat 和 lng。请记住，你需要你的 Google Maps API 密钥。**

+   ***5* 遍历每个评论，再次调用`outputRating`混合以生成星级标记**

+   ***6* 将评论文本中的任何换行符替换为<br/>标签，以便按作者的意图渲染**

可能会出现的疑问是，为什么每次都要将换行符替换为`<br/>`标签？为什么不直接在保存数据时使用`<br/>`标签呢？那样的话，你只需要在数据保存时运行一次`replace`函数。答案是，HTML 只是渲染文本的一种方法；碰巧你在这里使用的是这种方法。将来，你可能希望将此信息拉入原生移动应用程序。你不想源数据被在该环境中不使用的 HTML 标记污染。处理这种情况的方法是保持数据干净。

#### 添加评论页面

目前“添加评论”页面很简单，其中只包含一条数据：页面标题中的标题。更新控制器不应该引起太多问题。请参阅以下列表，以获取`addReview`控制器在`app_server/controllers`文件夹中`locations.js`的完整代码。

##### 列表 C.3\. `addReview` 控制器

```
const addReview = function(req, res){
  res.render('location-review-form', {
    title: 'Review Starcups on Loc8r',
    pageHeader: { title: 'Review Starcups' }
  });
};
```

这里没有太多可说的；你已经更新了标题内的文本。以下列表显示了相应的视图，`location-review-form.pug`，在`app_server/views`。

##### 列表 C.4\. `location-review-form.pug` 模板

```
extends layout
block content
  .row.banner
    .col-12
      h1= pageHeader.title
  .row
    .col-12.col-md-8
      form(action="/location", method="get", role="form")
        .form-group.row
          label.col-10.col-sm-2.col-form-label(for="name") Name
          .col-12.col-sm-10
            input#name.form-control(name="name")
        .form-group.row
          label.col-10.col-sm-2.col-form-label(for="rating") Rating
          .col-12.col-sm-2
            select#rating.form-control.input-sm(name="rating")
              option 5
              option 4
              option 3
              option 2
              option 1
        .form-group.row
          label.col-sm-2.col-form-label(for="review") Review
          .col-sm-10
            textarea#review.form-control(name="review", rows="5")
        button.btn.btn-primary.float-right Add my review
    .col-12.col-md-4
```

再次强调，这里没有复杂或新的内容，所以你可以继续到“关于”页面。

#### 关于页面

“关于”页面也不包含大量的数据，只有标题和一些内容。将其从视图中提取到控制器中。请注意，视图中的内容目前包含一些`<br/>`标签，所以在将其放入控制器时，需要将每个`<br/>`标签替换为`\n`。这些标签在以下列表中用粗体突出显示。`about`控制器位于`app_server/controllers/others.js`。

##### 列表 C.5\. `about` 控制器

```
const about = function(req, res){
  res.render('generic-text', {
    title: 'About Loc8r',
    content: 'Loc8r was created to help people find places to sit down
    and get a bit of work done.<br/><br/>Lorem ipsum dolor sit
    amet, consectetur adipiscing elit. Nunc sed lorem ac nisi digni
    ssim accumsan. Nullam sit amet interdum magna. Morbi quis
    faucibus nisi. Vestibulum mollis purus quis eros adipiscing
    tristique. Proin posuere semper tellus, id placerat augue dapibus
    ornare. Aenean leo metus, tempus in nisl eget, accumsan interdum
    dui. Pellentesque sollicitudin volutpat ullamcorper.'
  });
};
```

除了从内容中移除 HTML 之外，这里没有太多的事情发生。快速查看视图，你就可以完成了。以下列表显示了用于`app_server/views`中“关于”页面的最终通用文本视图。视图必须使用与评论部分相同的代码来替换`\n`换行符为 HTML `<br/>`标签。

##### 列表 C.6\. `generic-text.pug` 模板

```
extends layout
  .row.banner
    .col-12
      h1= title
  .row
    .col-12.col-lg-8
      p !{(content).replace(/\n/g, '<br/>')}      *1*
```

+   ***1* 在渲染 HTML 时将所有换行符替换为<br/>标签**

这个模板是一个简单、小巧、可重复使用的模板，用于在页面上输出文本时使用。

## 从 Promise 切换到 Observables

在 第八章 中，我们简要讨论了 Observables 和 Promises，然后继续在应用程序中使用 Promises。不过，将应用程序更改为使用 Observables 并不难，本节简要介绍了基础知识，以给你一个完整的了解，了解这项任务可能如何完成。通常，SPA 会根据要解决的问题使用 Observables 和 Promises。

查看位于 loc8r-data.service.ts 中的 `getLocations()` 方法。

##### 列表 C.7\. loc8r-data.service.ts

```
public getLocations(lat: number, lng: number): Promise<Location[]> {
    const maxDistance: number = 20000;
    const url: string =
     `${this.apiBaseUrl}/locations?lng=${lng}&lat=${lat}&maxDistance=$
{maxDistance}`;
    return this.http
      .get(url)
      .toPromise()                                *1*
      .then(response => response as Location[])
      .catch(this.handleError);
  }
```

+   ***1* 将 Observable 转换为 Promise**

如你所见，你正在将 `HttpClient get()` 方法返回的 Observable 转换为 Promise。

要将此方法切换为返回 Observable，你首先需要从 `rxjs` 中导入 Observable，然后让函数直接返回 `get()` 方法的结果。

##### 列表 C.8\. 修改 loc8r-data.service.ts 以返回 Observables

```
import { Observable } from 'rxjs'
...
public getLocations(lat: number, lng: number) : Observable<Location
[]> {
    const maxDistance: number = 20000;
    const url: string = `${this.apiBaseUrl}/locations?lng=${lng}&lat=${lat}&maxDistance=
${maxDistance}`;
    return this.http.get<Location[]>(url);      *1*
}
```

+   ***1* 返回 Observable 类型转换为 Location[]**

在这一点上，你没有捕获响应（Observable）；为了做到这一点，你需要一个订阅者。这个函数用于 home-list 组件中。

##### 列表 C.9\. `getLocations()` 来自 home-list.component.ts

```
private getLocations(position: any): void {
    this.message = 'Searching for nearby places';
    const lat: number = position.coords.latitude;
    const lng: number = position.coords.longitude;
    this.loc8rDataService
      .getLocations(lat, lng)
        .then(foundLocations => {                          *1*
          this.message = foundLocations.length > 0 ? '' :
          'No locations found';
          this.locations = foundLocations;
        });
  }
```

+   ***1* 响应 Promise**

要使用 Observable，你需要修改之前的列表。

##### 列表 C.10\. 订阅 Observables 的更改

```
private getLocations(position: any): void {
    this.message = 'Searching for nearby places';
    const lat: number = position.coords.latitude;
    const lng: number = position.coords.longitude;
    this.loc8rDataService
        .getLocations(lat, lng)
            .subscribe(                               *1*
                (foundLocations: Location[]) => {
                this.message = foundLocations.length > 0 ? '' :
                'No locations found';
                this.locations = foundLocations;
           },
           error => this.handleError(error)           *2*
       );
```

+   ***1* Observable 订阅者**

+   ***2* 错误处理程序**

如你所见，切换方法并不困难。方法的选择取决于具体情况。不过，为了参考，使用 Observables 正在成为标准做法。
