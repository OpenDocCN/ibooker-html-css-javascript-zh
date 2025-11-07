# 附录 A：JavaScript 速查表

## 更新

您可以在[`jscheatsheet.the-data-wrangler.com`](http://jscheatsheet.the-data-wrangler.com)找到此速查表的更新和演变版本。

## 记录日志

记录日志是你的最佳朋友。这是检查和验证数据最简单的方式：

```
console.log("Your logging here"); // General text logging for debugging.

const arr = [1, 2, 3];            // Your data.
console.log(arr);

console.trace();                  // Show callstack for current function. 
```

## 对象

```
let o = { A: 1, B: 2 };                 // Your data

let v1 = o["A"];                        // Extract field value
let v2 = o.A;

o["A"] = 3;                             // Set field value
o.A = 3;

delete o["A"];                          // Delete a field value
delete o.A;

let c = Object.assign({}, o);           // Clone an object
let ovr = { /* ... */ };
let c = Object.assign({}, o, ovr);      // Clone and override fields 
```

## 数组

```
let a = [1, 2, 3, 4, 5, 6];             // Your data
a.forEach(element => {
    // Visit each element in the array.
});

let v = a[5];                           // Get value at index
a[12] = v;                              // Set value at index

a.push("new item");                     // Add to end of array

let last = a.pop();                     // Remove last element

a.unshift("new item");                  // Add to start of array

let first = a.shift();                  // Remove first element

let a1 = [1, 2, 3];
let a2 = [4, 5, 6];
let a = a1.concat(a2);                  // Concatenate arrays

let e = a.slice(0, 3);                  // Extract first 3 elements

let e = a.slice(5, 11);                 // Extract elements 5 to 10

let e = a.slice(-4, -1);                // Negative indices relative to end                                   // of the array

let e = a.slice(-3);                    // Extract last three elements

let c = a.slice();                      // Clone array

let i = a.indexOf(3);                   // Find index of item in array
if (i >= 0) {
    let v = a[i];                       // The value exists, extract it
}

a.sort();                               // Ascending alphabetical sort

a.sort((a, b) => a - b);                // Customize sort with a user-defined                                    // function

let f = a.filter(v => predicate(v));    // Filter array

let t = a.map(v => transform(v));       // Transform array

let t = a.reduce((a, b) => a + b, 0)    // Aggregate an array 
```

## 正则表达式

```
let re = /search pattern/;              // Define regular expression
let re = new RegExp("search pattern");

let re = /case insensitive/ig           // Case insensitive + global

let source = "your source data";
let match = re.exec(source);            // Find first match.

while ((match = re.exec(source)) !== null) {
    // Find all matches.
} 
```

## 读取和写入文本文件（Node.js，同步）

```
const fs = require(‘fs’);

const text = “My text data”;                // Data to write.

fs.writeFileSync(“my-file.txt”, text);      // Write the to file.

const loaded = 

    fs.readFileSync(“my-file.txt”, “utf8”); // Read from file.

console.log(loaded); 
```

## 读取和写入 JSON 文件（Node.js，同步）

```
const fs = require(‘fs’);

const data = [

    { item: “1” },

    { item: “2” },

    { item: “3” }

];

const json = JSON.stringify(data);        // Serialize to JSON

fs.writeFileSync(“my-file.json”, json);   // Write to file.

const loaded = fs.readFileSync(“my-file.json”, “utf8”); // Read file.

const deserialized = JSON.parse(loaded); // Deserialize JSON.

console.log(deserialized); 
```

## 读取和写入 CSV 文件（Node.js，同步）

```
const fs = require(‘fs’);

const Papa = require(‘papaparse’);

const data = [

    { item: “1”, val: 100 },

    { item: “2”, val: 200 },

    { item: “3”, val: 300 }

];

const csv = Papa.unparse(data);     // Serialize to CSV.

fs.writeFileSync(“my-file.csv”, csv);     // Write to file.

const loaded = fs.readFileSync(“my-file.csv”, “utf8”); // Read file.

const options = { dynamicTyping: true, header: true };

const deserialized = Papa.parse(loaded, options); // Deserialize CSV.

console.log(deserialized.data); 
```

```
 let source = "your source data";

let match = re.exec(source);            // Find first match. 
```
