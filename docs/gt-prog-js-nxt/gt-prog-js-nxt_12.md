## 这里是使用承诺和异步函数从第 7 单元的预览

```
[source,js]
----
async function fetchImage(url) {
  const resp = await fetch(url);
  const blob = await resp.blob();
  return createImageBitmap(blob);
};

fetchImage('my-image.png').then(image => {
  // do something with image
});
----
```

要在不使用承诺或异步函数的情况下实现这一点，你需要编写更多晦涩难懂的代码。

```
[source,js]
----
function fetchImage(url, cb) {
  fetch(url, function(resp) {
    resp.blob(function(blob) {
      createImageBitmap(blob, cb);
    })
  })
};

fetchImage('my-image.png', function(image) {
  // do something with image
});
----
```
