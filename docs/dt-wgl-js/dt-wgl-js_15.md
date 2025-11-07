# 附录 B：Data-Forge 快速参考表

## 更新

你可以在 [`dfcheatsheet.the-data-wrangler.com`](http://dfcheatsheet.the-data-wrangler.com) 找到这个快速参考表的更新和演变版本***。

## 将数据加载到 DataFrame 中

你可以将内存中的数据加载到 Data-Forge DataFrame 中：

```
let data = [ /* ... your data ... */ ];
let df = new dataForge.DataFrame(data);
console.log(df.toString()); 
```

## 加载 CSV 文件

将 CSV 文件中的数据加载到 DataFrame 中：

```
let df = dataForge
    .readFileSync("./example.csv", { dynamicTyping: true })
    .parseCSV();
console.log(df.head(5).toString()); // Preview first 5 rows 
```

## 加载 JSON 文件

同时将 JSON 文件加载到 DataFrame 中：

```
let df = dataForge
    .readFileSync("./example.json")
    .parseJSON(); 
```

```
console.log(df.tail(5).toString()); // Preview last 5 rows. 
```

## 数据转换

使用 `select` 函数转换或重写你的数据集：

```
df = df.select(row => transformRow(row)); 
```

## 数据过滤

使用 `where` 函数过滤数据：

```
df = df.where(row => predicate(row)); 
```

## 删除列

使用 `dropSeries` 函数删除数据列：

```
df = df.dropSeries("ColumnToRemove"); 
```

## 保存 CSV 文件

将修改后的数据保存到 CSV 文件中：

```
df.asCSV().writeFileSync("./transformed.csv"); 
```

## 保存 JSON 文件

将修改后的数据保存到 JSON 文件中：

```
df.asJSON().writeFileSync("./transformed.json"); 
```
