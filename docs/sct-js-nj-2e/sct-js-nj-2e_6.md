## 附录 C. 练习答案

### 第二章\. 运行时构建页面

> **1**
> 
> 客户端 Web 应用程序的生命周期中有两个阶段是什么？
> 
> **A:**
> 
> 客户端 Web 应用程序的生命周期中的两个阶段是页面构建和事件处理。在页面构建阶段，通过处理 HTML 代码和执行主线 JavaScript 代码来构建我们页面的用户界面。在处理最后一个 HTML 节点后，页面进入事件处理阶段，在该阶段中处理各种事件。
> 
> **2**
> 
> 使用 `addEventListener` 方法注册事件处理程序与将处理程序分配给特定元素属性相比，主要优势是什么？
> 
> **A:**
> 
> 当将事件处理程序分配给特定元素属性时，我们只能注册一个事件处理程序；另一方面，`addEventListener` 允许我们注册所需的所有事件处理程序。
> 
> **3**
> 
> 一次可以处理多少个事件？
> 
> **A:**
> 
> JavaScript 基于单线程执行模型，其中事件逐个处理。
> 
> **4**
> 
> 事件队列中的事件按什么顺序处理？
> 
> **A:**
> 
> 事件按照它们生成的顺序进行处理：先进先出。

### 第三章\. 为新手准备的顶级函数：定义和参数

> **1**
> 
> 在以下代码片段中，哪些是回调函数？
> 
> ```
> //sortAsc is a callback because the JavaScript engine
> //calls it to compare array items
> numbers.sort(function sortAsc(a,b){
>   return a – b;
> });
> 
> //Not a callback; ninja is called like a standard function
> function ninja(){}
> ninja();
> 
> var myButton = document.getElementById("myButton");
> //handleClick is a callback, the function is called
> //whenever myButton is clicked
> myButton.addEventListener("click", function handleClick(){
>   alert("Clicked");
> });
> ```
> 
> **2**
> 
> 在以下代码片段中，根据函数类型（函数声明、函数表达式或箭头函数）对函数进行分类。
> 
> ```
> //function expression as argument to another function
> numbers.sort(function sortAsc(a,b){
>   return a – b;
> });
> 
> //arrow function as argument to another function
> numbers.sort((a,b) => b – a);
> 
> //function expression as the callee in a call expression
> function(){})();
> 
> //function declaration
> function outer(){
>   //function declaration
>   function inner(){}
>   return inner;
> }
> 
> //function expression call wrapped in an expression
> (function(){}());
> 
> //arrow function as a callee
> (()=>"Yoshi")();
> ```
> 
> **3**
> 
> 执行以下代码片段后，变量 `samurai` 和 `ninja` 的值是什么？
> 
> ```
> //"Tomoe", the value of the expression body of the arrow function
> var samurai = (() => "Tomoe")();
> //undefined, in case an arrow function's body is a block statement
> //the value is the value of the return statement.
> //Because there's no return statement, the value is undefined.
> var ninja = (() => {"Yoshi"})();
> ```
> 
> **4**
> 
> 在 `test` 函数体内，对于两次函数调用，参数 `a`、`b` 和 `c` 的值是什么？
> 
> ```
> function test(a, b, ...c){ /*a, b, c*/}
> 
> // a = 1; b = 2; c = [3, 4, 5]
> test(1, 2, 3, 4, 5);
> // a = undefined; b = undefined; c = []
> test();
> ```
> 
> **5**
> 
> 执行以下代码片段后，`message1` 和 `message2` 变量的值是什么？
> 
> ```
> function getNinjaWieldingWeapon(ninja, weapon = "katana"){
>   return ninja + " " + katana;
> }
> 
> //"Yoshi katana" – there's only one argument in the call
> //so weapon defaults to "katana"
> var message1 = getNinjaWieldingWeapon("Yoshi");
> 
> //"Yoshi wakizashi" – we've sent in two arguments, the default
> //value is not taken into account
> var message2 = getNinjaWieldingWeapon("Yoshi", "wakizashi");
> ```

### 第四章\. 对于熟练工：理解函数调用

> **1**
> 
> 以下函数使用 `arguments` 对象计算传入参数的总和。通过使用上一章中引入的剩余参数，重写 `sum` 函数，使其不使用 `arguments` 对象。
> 
> ```
> function sum(){
>   var sum = 0;
>   for(var i = 0; i < arguments.length; i++){
>      sum += arguments[i];
>   }
>   return sum;
> }
> 
> assert(sum(1, 2, 3) === 6, 'Sum of first three numbers is 6');
> assert(sum(1, 2, 3, 4) === 10, 'Sum of first four numbers is 10');
> ```
> 
> **A:**
> 
> 在函数定义中添加一个剩余参数，并稍微调整函数体：
> 
> ```
> function sum(... numbers){
>   var sum = 0;
>   for(var i = 0; i < numbers.length; i++){
>      sum += numbers[i];
>   }
>   return sum;
> }
> 
> assert(sum(1, 2, 3) === 6, 'Sum of first three numbers is 6');
> assert(sum(1, 2, 3, 4) === 10, 'Sum of first four numbers is 10');
> ```
> 
> **2**
> 
> 在浏览器中运行以下代码后，变量 `ninja` 和 `samurai` 的值是什么？
> 
> ```
> function getSamurai(samurai){
>   "use strict"
> 
>   arguments[0] = "Ishida";
> 
>   return samurai;
> }
> 
> function getNinja(ninja){
>   arguments[0] = "Fuma";
>   return ninja;
> }
> 
> var samurai = getSamurai("Toyotomi");
> var ninja = getNinja("Yoshi");
> ```
> 
> **A:**
> 
> `samurai` 将具有值 `Toyotomi`，而 `ninja` 将具有值 `Fuma`。因为 `getSamurai` 函数处于严格模式，所以 `arguments` 参数不会别名函数参数，因此更改第一个参数的值不会更改 `samurai` 参数的值。因为 `getNinja` 函数处于非严格模式，对 `arguments` 参数所做的任何更改都将反映在函数参数中。
> 
> **3**
> 
> 当运行以下代码时，哪个断言会通过？
> 
> ```
> function whoAmI1(){
>   "use strict";
>   return this;
> }
> 
> function whoAmI2(){
>   return this;
> }
> 
> assert(whoAmI1() === window, "Window?"); //fail
> assert(whoAmI2() === window, "Window?"); //pass
> ```
> 
> **A:**
> 
> `whoAmI1`函数处于严格模式；当它作为函数被调用时，`this`参数的值将是`undefined`（而不是`window`）。第二个断言会通过：如果非严格模式下的函数作为函数被调用，`this`将指向全局对象（在浏览器中运行代码时是`window`对象）。
> 
> **4**
> 
> 当运行以下代码时，哪个断言会通过？
> 
> ```
> var ninja1 = {
>    whoAmI: function(){
>      return this;
>    }
> };
> 
> var ninja2 = {
>   whoAmI: ninja1.whoAmI
> };
> 
> var identify = ninja2.whoAmI;
> 
> //pass: whoAmI called as a method of ninja1
> assert(ninja1.whoAmI() === ninja1, "ninja1?");
> 
> //fail: whoAmI called as a method of ninja2
> assert(ninja2.whoAmI() === ninja1, " ninja1 again?");
> 
> //fail: identify calls the function as a function
> //because we are in non-strict mode, this refers to the window
> assert(identify() === ninja1, "ninja1 again?");
> 
> //pass: Using call to supply the function context
> //this refers to ninja2
> assert(ninja1.whoAmI.call(ninja2) === ninja2, "ninja2 here?");
> ```
> 
> **5**
> 
> 当运行以下代码时，哪个断言会通过？
> 
> ```
> function Ninja(){
>   this.whoAmI = () => this;
> }
> 
> var ninja1 = new Ninja();
> var ninja2 = {
>   whoAmI: ninja1.whoAmI
> };
> 
> //pass: whoAmI is an arrow function inherits the function context
> //from the context in which it was created.
> //Because it was created during the construction of ninja1
> //this will always point to ninja1
> assert(ninja1.whoAmI() === ninja1, "ninja1 here?");
> 
> //false: this always refers to ninja1
> assert(ninja2.whoAmI() === ninja2, "ninja2 here?");
> ```
> 
> **6**
> 
> 以下哪个断言会通过？
> 
> ```
> function Ninja(){
>   this.whoAmI = function(){
>     return this;
>   }.bind(this);
> }
> 
> var ninja1 = new Ninja();
> var ninja2 = {
>   whoAmI: ninja1.whoAmI
> };
> //pass: the function assigned to whoAmI is a function bound
> //to ninja1 (the value of this when the constructor was invoked)
> //this will always refer to ninja1
> assert(ninja1.whoAmI() === ninja1, "ninja1 here?");
> //fail: this in whoAmI always refers to ninja1
> //because whoAmI is a bound function.
> assert(ninja2.whoAmI() === ninja2, "ninja2 here?");
> ```

### 第五章\. 精通者的函数：闭包和作用域

> **1**
> 
> 闭包允许函数
> 
> **A:**
> 
> 访问在函数定义时作用域内的外部变量（选项 a）
> 
> **2**
> 
> 闭包带有
> 
> **A:**
> 
> 内存成本（闭包在函数定义时保持作用域内的变量）(选项 b)
> 
> **3**
> 
> 在以下代码示例中，标记通过闭包访问的标识符：
> 
> ```
> function Samurai(name) {
>   var weapon = "katana";
> 
>   this.getWeapon = function(){
>     //accesses the local variable: weapon
>     return weapon;
>   };
> 
>   this.getName = function(){
>     //accesses the function parameter: name
>     return name;
>   }
> 
>   this.message = name + " wielding a " + weapon;
> 
>   this.getMessage = function(){
>     //this.message is not accessed through a closure
>    //it is an object property (and not a variable)
>     return this.message;
>   }
> }
> 
> var samurai = new Samurai("Hattori");
> 
> samurai.getWeapon();
> samurai.getName();
> samurai.getMessage();
> ```
> 
> **4**
> 
> 在以下代码中，创建了多少执行上下文，以及执行上下文栈的最大大小？
> 
> ```
> function perfom(ninja) {
>   sneak(ninja);
>   infiltrate(ninja);
> }
> 
> function sneak(ninja) {
>   return ninja + " skulking";
> }
> 
> function infiltrate(ninja) {
>   return ninja + " infiltrating";
> }
> 
> perfom("Kuma");
> ```
> 
> **A:**
> 
> 最大的栈大小是 3，在以下情况下：
> 
> +   全局代码 -> `perform` -> `sneak`
> +   
> +   全局代码 -> `perform` -> `infiltrate`
> +   
> **5**
> 
> JavaScript 中哪个关键字允许我们定义不能重新分配为完全新值的变量？
> 
> **A:**
> 
> `const`变量不能重新分配为新值。
> 
> **6**
> 
> `var`和`let`之间的区别是什么？
> 
> **A:**
> 
> 关键字`var`用于定义仅函数或全局作用域的变量，而`let`使我们能够定义块作用域、函数作用域和全局作用域的变量。
> 
> **7**
> 
> 以下代码将在哪里以及为什么抛出异常？
> 
> ```
> getNinja();
> getSamurai();  //throws an exception
> 
> function getNinja() {
>   return "Yoshi";
> }
> 
> var getSamurai = () => "Hattori";
> ```
> 
> **A:**
> 
> 当尝试调用`getSamurai`函数时，将会抛出异常。`getNinja`函数使用函数声明定义，将在任何代码执行之前创建；我们可以称它为“在代码中达到其声明之前”的“之前”。另一方面，`getSamurai`函数是一个箭头函数，它在执行到达它时创建，所以当我们尝试调用它时，它将是未定义的。

### 第六章\. 未来的函数：生成器和承诺

> **1**
> 
> 在运行以下代码后，变量`a1`到`a4`的值是什么？
> 
> ```
> function *EvenGenerator(){
>   let num = 2;
>   while(true){
>     yield num;
>     num = num + 2;
>   }
> }
> 
> let generator = EvenGenerator();
> 
> //2 the first value yielded
> let a1 = generator.next().value;
> 
> //4 the second value yielded
> let a2 = generator.next().value;
> //2, because we have started a new generator
> let a3 = EvenGenerator().next().value;
> //6, we go back to the first generator
> let a4 = generator.next().value;
> ```
> 
> **2**
> 
> 运行以下代码后，`ninjas`数组的内容是什么？（提示：考虑如何使用`while`循环实现`for-of`循环。）
> 
> ```
> function* NinjaGenerator(){
>   yield "Yoshi";
>   return "Hattori";
>   yield "Hanzo";
> }
> 
> var ninjas = [];
> for(let ninja of NinjaGenerator()){
>   ninjas.push(ninja);
> }
> 
> ninjas;
> ```
> 
> **A:**
> 
> `ninjas` 数组将只包含 `Yoshi`。这是因为 `for-of` 循环会遍历生成器直到生成器表示已完成（不包括与完成一起传递的值）。这发生在生成器中没有更多代码可执行时，或者遇到 `return` 语句时。
> 
> **3**
> 
> 执行以下代码后，变量 `a1` 和 `a2` 的值是什么？
> 
> ```
> function *Gen(val){
>   val = yield val * 2;
>   yield val;
> }
> 
> let generator = Gen(2);
> //4\. The value of the first value passed in through next: 3 is ignored
> //because the generator hasn't yet started its execution, and there
> //is no waiting yield expression.
> //Because the generator is created with val being 2
> //the first yield occurs for val * 2, i.e. 2*2 == 4
> let a1 = generator.next(3).value;
> //5: passing in 5 as a argument to next
> //means that the waiting yielded expression will get the value 5
> //(yield val * 2) == 5
> //because that value is then assigned to val, the next yield expression
> //yield val;
> //will return 5
> let a2 = generator.next(5).value;
> ```
> 
> **4**
> 
> 以下代码的输出是什么？
> 
> ```
> const promise = new Promise((resolve, reject) => {
>   reject("Hattori"); //the promise was explicitly rejected
> });
> 
> //the error handler will be invoked
> promise.then(val => alert("Success: " + val))
>        .catch(e => alert("Error: " + e));
> ```
> 
> **5**
> 
> 以下代码的输出是什么？
> 
> ```
> const promise = new Promise((resolve, reject) => {
>   //the promise was explicitly resolved
>   resolve("Hattori");
>   //once a promise has settled, it can't be changed
>   //rejecting it after 500ms will have no effect
>   setTimeout(()=> reject("Yoshi"), 500);
> });
> 
> //the success handler will be invoked
> promise.then(val => alert("Success: " + val))
>        .catch(e => alert("Error: " + e));
> ```

### 第七章\. 使用原型的面向对象

> **1**
> 
> 以下哪个属性指向一个对象，如果目标对象没有要搜索的属性，则会搜索该对象？
> 
> **A:**
> 
> `prototype`（选项 c）
> 
> **2**
> 
> 执行以下代码后，变量 `a1` 的值是什么？
> 
> ```
> function Ninja(){}
> Ninja.prototype.talk = function (){
>   return "Hello";
> };
> 
> const ninja = new Ninja();
> const a1 = ninja.talk(); //"Hello"
> ```
> 
> **A:**
> 
> 变量 `a1` 的值将是 `Hello`。即使对象 `ninja` 没有拥有 `talk` 方法，但其原型有。
> 
> **3**
> 
> 执行以下代码后，`a1` 的值是什么？
> 
> ```
> function Ninja(){}
> Ninja.message = "Hello";
> 
> const ninja = new Ninja();
> 
> const a1 = ninja.message;
> ```
> 
> **A:**
> 
> 变量 `a1` 的值将是未定义的。`message` 属性在构造函数 `Ninja` 中定义，并且不能通过 `ninja` 对象访问。
> 
> **4**
> 
> 解释这两个代码片段中 `getFullName` 方法的区别：
> 
> ```
> //First fragment
> function Person(firstName, lastName){
>   this.firstName = firstName;
>   this.lastName = lastName;
> 
>   this.getFullName = function () {
>     return this.firstName + " " + this.lastName;
>   }
> }
> 
> //Second fragment
> function Person(firstName, lastName) {
>   this.firstName = firstName;
>   this.lastName = lastName;
> }
> 
> Person.prototype.getFullName = function () {
>   return this.firstName + " " + this.lastName;
> }
> ```
> 
> **A:**
> 
> 在第一个片段中，`getFullName` 方法是在使用 `Person` 构造函数创建的实例上直接定义的。使用 `Person` 构造函数创建的每个对象都会获得自己的 `getFullName` 方法。在第二个片段中，`getFullName` 方法是在 `Person` 函数的原型上定义的。使用 `Person` 函数创建的所有实例都将能够访问这个单一的方法。
> 
> **5**
> 
> 执行以下代码后，`ninja.constructor` 将指向什么？
> 
> ```
> function Person() { }
> function Ninja() { }
> 
> const ninja = new Ninja();
> ```
> 
> **A:**
> 
> 当访问 `ninja.constructor` 时，构造函数属性位于 `ninja` 的原型上。因为 `ninja` 是用 `Ninja` 构造函数创建的，所以构造函数属性指向 `Ninja` 函数。
> 
> **6**
> 
> 执行以下代码后，`ninja.constructor` 将指向什么？
> 
> ```
> function Person() { }
> function Ninja() { }
> Ninja.prototype = new Person();
> const ninja = new Ninja();
> ```
> 
> **A:**
> 
> `constructor` 属性是由构造函数创建的原型对象的属性。在这个例子中，我们用一个新的 `Person` 对象覆盖了 `Ninja` 函数的内置原型。因此，当使用 `Ninja` 构造函数创建 `ninja` 对象时，其原型被设置为新的 `person` 对象。最后，当我们访问 `ninja` 对象上的 `constructor` 属性时，因为 `ninja` 对象没有自己的 `constructor` 属性，所以会咨询其原型，即新的 `person` 对象。`person` 对象也没有 `constructor` 属性，所以会咨询其原型，即 `Person.prototype` 对象。该对象有一个 `constructor` 属性，引用了 `Person` 函数。这个例子完美地说明了为什么在使用 `constructor` 属性时要小心：尽管我们的 `ninja` 对象是用 `Ninja` 函数创建的，但由于覆盖了默认的 `Ninja.prototype`，`constructor` 属性指向了 `Person` 函数。
> 
> **7**
> 
> 解释以下例子中 `instanceof` 操作符的工作原理。
> 
> ```
> function Warrior() { }
> 
> function Samurai() { }
> Samurai.prototype = new Warrior();
> 
> var samurai = new Samurai();
> 
> samurai instanceof Warrior; //Explain
> ```
> 
> **A:**
> 
> `instanceof` 操作符检查右侧函数的原型是否在左侧对象的原型链中。左侧的对象是用 `Samurai` 函数创建的，其原型有一个新的 `warrior` 对象，其原型是 `Warrior` 函数的原型 (`Warrior.prototype`)。在右侧我们有 `Warrior` 函数。所以在这个例子中，`instanceof` 操作符将返回 `true`，因为右侧函数的原型 `Warrior.prototype` 可以在左侧对象的原型链中找到。
> 
> **8**
> 
> 将以下 ES6 代码翻译成 ES5 代码。
> 
> ```
> class Warrior {
>   constructor(weapon){
>     this.weapon = weapon;
>   }
> 
>   wield() {
>     return "Wielding " + this.weapon;
>   }
> 
>   static duel(warrior1, warrior2){
>     return warrior1.wield() + " " + warrior2.wield();
>   }
> }
> ```
> 
> **A:**
> 
> 我们可以这样翻译以下代码：
> 
> ```
> function Warrior(weapon) {
>   this.weapon = weapon;
> }
> 
> Warrior.prototype.wield = function () {
>   return "Wielding " + this.weapon;
> };
> 
> Warrior.duel = function (warrior1, warrior2) {
>   return warrior1.wield() + " " + warrior2.wield();
> };
> ```

### 第八章\. 控制对象访问

> **1**
> 
> 在运行以下代码后，以下哪个表达式会抛出异常，原因是什么？
> 
> ```
> const ninja = {
>    get name() {
>      return "Akiyama";
>    }
> }
> ```
> 
> **A:**
> 
> 调用 `ninja.name()` 会抛出异常，因为 `ninja` 没有名字方法（选项 a）。在 `const name = ninja.name` 中访问 `ninja.name` 的工作方式很完美；获取器被激活，变量 `name` 获得了值 `Akiyama`。
> 
> **2**
> 
> 在以下代码中，哪种机制允许获取器访问私有对象变量？
> 
> ```
> function Samurai() {
>   const _weapon = "katana";
>   Object.defineProperty(this, "weapon", {
>     get: () => _weapon
>   });
> }
> const samurai = new Samurai();
> assert(samurai.weapon === "katana", "A katana wielding samurai");
> ```
> 
> **A:**
> 
> 闭包允许获取器访问私有对象变量。在这种情况下，`get` 方法在构造函数中定义的 `_weapon` 私有变量周围创建了一个闭包，这使 `_weapon` 变量保持活跃。
> 
> **3**
> 
> 以下哪个断言会通过？
> 
> ```
> const daimyo = { name: "Matsu", clan: "Takasu"};
> const proxy = new Proxy(daimyo, {
>   get: (target, key) => {
>     if(key === "clan"){
>       return "Tokugawa";
>     }
>   }
> });
> 
> assert(daimyo.clan === "Takasu", "Matsu of clan Takasu");     //pass
> assert(proxy.clan === "Tokugawa", "Matsu of clan Tokugawa?"); //pass
> 
> proxy.clan = "Tokugawa";
> 
> assert(daimyo.clan === "Takasu", "Matsu of clan Takasu");     //fail
> assert(proxy.clan === "Tokugawa", "Matsu of clan Tokugawa?"); //pass
> ```
> 
> **A:**
> 
> 第一个断言通过，因为 `daimyo` 有一个值为 `Takasu` 的 `clan` 属性。第二个断言通过，因为我们通过一个具有总是返回 `Tokugawa` 作为 `clan` 属性值的获取陷阱的代理访问 `clan` 属性。
> 
> 当表达式 `proxy.clan = "Tokugawa"` 被评估时，值 `Tokugawa` 被存储在 `daimyo` 的 `clan` 属性中，因为代理没有设置陷阱，所以设置属性的默认操作是在目标对象 `daimyo` 上执行。
> 
> 第三个断言失败，因为 `daimyo` 的族属性值为 `Tokugawa` 而不是 `Takasu`。
> 
> 第四个断言通过，因为代理总是返回 `Tokugawa`，无论目标对象 `clan` 属性中存储的值是什么。
> 
> **4**
> 
> 以下哪个断言会通过？
> 
> ```
> const daimyo = { name: "Matsu", clan: "Takasu", armySize: 10000};
> const proxy = new Proxy(daimyo, {
>   set: (target, key, value) => {
>     if(key === "armySize") {
>       const number = Number.parseInt(value);
>       if(!Number.isNaN(number)){
>         target[key] = number;
>       }
>     } else {
>         target[key] = value;
>     }
>   },
> });
> //pass
> assert(daimyo.armySize === 10000, "Matsu has 10 000 men at arms");
> //pass
> assert(proxy.armySize === 10000, "Matsu has 10 000 men at arms");
> 
> proxy.armySize = "large";
> assert(daimyo.armySize === "large", "Matsu has a large army"); //fail
> 
> daimyo.armySize = "large";
> assert(daimyo.armySize === "large", "Matsu has a large army");//pass
> ```
> 
> **A:**
> 
> 第一个断言通过；`daimyo` 的 `armySize` 属性值为 `10000`。第二个断言也通过；代理没有定义获取陷阱，所以返回目标 `daimyo` 的 `armySize` 属性的值。
> 
> 当表达式 `proxy.armySize = "large";` 被评估时，代理的设置陷阱被激活。设置器检查传入的值是否为数字，并且只有当它是数字时，值才被分配给目标属性。在这种情况下，传入的值不是数字，所以没有对 `armySize` 属性进行更改。因此，假设更改的第三个断言失败。
> 
> 表达式 `daimyo.armySize = "large";` 直接写入 `armySize` 属性，绕过了代理。因此，最后一个断言通过。

### 第九章。处理集合

> **1**
> 
> 运行以下代码后，`samurai` 数组的内容是什么？
> 
> ```
> const samurai = ["Oda", "Tomoe"];
> samurai[3] = "Hattori";
> ```
> 
> **A:**
> 
> `samurai` 的值是 `["Oda", "Tomoe", undefined, "Hattori"]`。数组以 `Oda` 和 `Tomoe` 在索引 `0` 和 `1` 处开始。然后我们在索引 3 添加一个新的武士 `Hattori`，这“扩展”了数组，并在索引 2 留下 `undefined`。
> 
> **2**
> 
> 运行以下代码后，`ninjas` 数组的内容是什么？
> 
> ```
> const ninjas = [];
> 
> ninjas.push("Yoshi");
> ninjas.unshift("Hattori");
> 
> ninjas.length = 3;
> 
> ninjas.pop();
> ```
> 
> **A:**
> 
> `ninjas` 的值是 `["Hattori", "Yoshi"]`。我们从一个空数组开始，`push` 将 `Yoshi` 添加到末尾，而 `unshift` 将 `Hattori` 添加到开头。显式地将 `length` 设置为 `3` 通过在索引 `2` 处添加 `undefined` 来扩展数组。调用 `pop` 从数组中删除那个 `undefined`，留下只有 `["Hattori", "Yoshi"]`。
> 
> **3**
> 
> 运行以下代码后，`samurai` 数组的内容是什么？
> 
> ```
> const samurai = [];
> 
> samurai.push("Oda");
> samurai.unshift("Tomoe");
> samurai.splice(1, 0, "Hattori", "Takeda");
> samurai.pop();
> ```
> 
> **A:**
> 
> `samurai` 的值是 `["Tomoe", "Hattori", "Takeda"]`。数组最初为空；`push` 将 `Oda` 添加到末尾，而 `unshift` 将 `Tomoe` 添加到开头；`splice` 删除索引 `1` 的项目（`Oda`）并添加 `Hattori` 和 `Takeda`。
> 
> **4**
> 
> 运行以下代码后，变量 `first`、`second` 和 `third` 中存储了什么？
> 
> ```
> const ninjas = [{name:"Yoshi", age: 18},
>     {name:"Hattori", age: 19},
>     {name:"Yagyu", age: 20}];
> 
> const first = persons.map(ninja => ninja.age);
> const second = first.filter(age => age % 2 == 0);
> const third = first.reduce((aggregate, item) =>  aggregate + item, 0);
> ```
> 
> **A:**
> 
> 第一：[18, 19, 20]; 第二：[18, 20]; 第三：57
> 
> **5**
> 
> 执行以下代码后，变量 `first` 和 `second` 中存储了什么？
> 
> ```
> const ninjas = [{ name: "Yoshi", age: 18 },
>                { name: "Hattor", age: 19 },
>                { name: "Yagyu", age: 20 }];
> 
> const first = ninjas.some(ninja => ninja.age % 2 == 0);
> const second = ninjas.every(ninja => ninja.age % 2 == 0);
> ```
> 
> **A:**
> 
> 第一：true；第二：false
> 
> **6**
> 
> 以下哪个断言会通过？
> 
> ```
> const samuraiClanMap = new Map();
> 
> const samurai1 = { name: "Toyotomi"};
> const samurai2 = { name: "Takeda"};
> const samurai3 = { name: "Akiyama"};
> 
> const oda = { clan: "Oda"};
> const tokugawa = { clan: "Tokugawa"};
> const takeda ={clan: "Takeda"};
> samuraiClanMap.set(samurai1, oda);
> samuraiClanMap.set(samurai2, tokugawa);
> samuraiClanMap.set(samurai2, takeda);
> 
> assert(samuraiClanMap.size === 3, "There are three mappings");
> assert(samuraiClanMap.has(samurai1), "The first samurai has a mapping");
> assert(samuraiClanMap.has(samurai3), "The third samurai has a mapping");
> ```
> 
> **A:**
> 
> 第一个断言失败，因为为 `samurai2` 创建了两次映射。第二个断言通过，因为添加了 `samurai1` 的映射。第三个断言失败，因为从未创建 `samurai3` 的映射。
> 
> **7**
> 
> 以下哪个断言会通过？
> 
> ```
> const samurai = new Set("Toyotomi", "Takeda", "Akiyama", "Akiyama");
> assert(samurai.size === 4, "There are four samurai in the set");
> 
> samurai.add("Akiyama");
> assert(samurai.size === 5, "There are five samurai in the set");
> 
> assert(samurai.has("Toyotomi", "Toyotomi is in!");
> assert(samurai.has("Hattori", "Hattori is in!");
> ```
> 
> **A:**
> 
> 第一个断言失败，因为 `Akiyama` 只添加了一次到集合中。第二个断言也失败，因为再次尝试添加 `Akiyama` 不会改变集合（及其长度）。最后两个断言会通过。

### 第十章。正则表达式的处理

> **1**
> 
> 在 JavaScript 中，可以使用以下哪种方式创建正则表达式？
> 
> **A:**
> 
> 正则表达式字面量（选项 a）和通过使用内置的 `RegExp` 构造函数（选项 B）。答案 c 是错误的；没有内置的 `RegularExpression` 构造函数。
> 
> **2**
> 
> 以下哪个是正则表达式字面量？
> 
> **A:**
> 
> 在 JavaScript 中，正则表达式字面量用两个正斜杠括起来：`/test/`（选项 a）。
> 
> **3**
> 
> 选择正确的正则表达式标志。
> 
> **A:**
> 
> 使用正则表达式字面量时，表达式标志放在关闭的正斜杠之后：`/test/g`（选项 a）。使用 `RegExp` 构造函数时，它们作为第二个参数传递：`new RegExp ("test", "gi");`（选项 c）。
> 
> **4**
> 
> 正则表达式 `/def/` 匹配以下哪个字符串？
> 
> **A:**
> 
> 正则表达式 `/def/` 仅匹配 `def`：由 *d* 后跟 *e*，再跟 *f*（选项 b）。
> 
> **5**
> 
> 正则表达式 `/[^abc]/` 匹配以下哪个？
> 
> **A:**
> 
> 正则表达式 `/[^abc]/` 匹配以下字符串之一：`d`、`e`、`f`——一个不是 *a*、*b* 或 *c* 的字符（选项 b）。
> 
> **6**
> 
> 以下哪个正则表达式匹配字符串 `hello`？
> 
> **A:**
> 
> 选项 a、b 和 c 匹配。`/hello/` 仅匹配确切的字符串 `hello`。`/hell?o/` 匹配 `hello` 或 `helo`（第二个 `l` 是可选的）。`/hel*o/`，在第一个 `l` 之后，匹配任意数量的字母 `l`。
> 
> **7**
> 
> 正则表达式 `/(cd)+(de)*/` 匹配以下哪个字符串？
> 
> **A:**
> 
> 选项 a、c、d 和 f 是正确的。`/(cd)+(de)*/` 匹配一个或多个 `cd` 的出现，后跟任意数量的 `de` 的出现。
> 
> **8**
> 
> 在正则表达式中，我们可以用以下哪种方式表达选择？
> 
> **A:**
> 
> 我们使用管道字符`|`在正则表达式中表示替代（选项 c）。
> 
> **9**
> 
> 在正则表达式`/([0-9])2/`中，我们可以用以下哪个来引用第一个匹配的数字？
> 
> **A:**
> 
> `\1`（选项 d）
> 
> **10**
> 
> 正则表达式`/([0-5])6\1/`将匹配以下哪个？
> 
> **A:**
> 
> 在正则表达式`/([0-5])6\1/`中，第一个字符是 0 到 5 的数字，第二个字符是数字 6，第三个字符是第一个匹配的数字，所以`060`和`565`都匹配（选项 a 和 d）。
> 
> **11**
> 
> 正则表达式`/(?:ninja)-(trick)?-\1/`将匹配以下哪个？
> 
> **A:**
> 
> 在正则表达式`/(?:ninja)-(trick)?-\1/`中，第一个组`(?:ninja)`是一个非捕获组，而第二个组是一个可选的捕获组`(trick)?`。但如果找到了第二个组，最后我们将有一个对它的反向引用。因此，`ninja-`和`ninja-trick-trick`匹配（选项 a 和 c）。
> 
> **12**
> 
> 执行`"012675".replace(/0-5/g, "a")`的结果是什么？
> 
> **A:**
> 
> 代码将所有从 0 到 5 的数字替换为字母 a，因此结果是`aaa67a`（选项 a）。

### 第十一章。代码模块化技术

> **1**
> 
> 哪种机制使得模块模式中可以存在私有模块变量？
> 
> **A:**
> 
> 在模块模式中，闭包允许我们隐藏模块内部：模块公共 API 的方法保持了模块内部的生命（选项 b）。
> 
> **2**
> 
> 在以下使用 ES6 模块的代码中，如果导入模块，哪些标识符可以被访问？
> 
> ```
> const spy = "Yagyu";
> function command(){
>   return general + " commands you to wage war!";
> }
> export const general = "Minamoto";
> ```
> 
> **A:**
> 
> 从模块外部，我们只能访问`general`标识符，因为它是唯一一个被显式导出的标识符（选项 c）。
> 
> **3**
> 
> 在以下使用 ES6 模块的代码中，当导入模块时，哪些标识符可以被访问？
> 
> ```
> const ninja = "Yagyu";
> function command(){
>   return general + " commands you to wage war!";
> }
> const general  = "Minamoto";
> 
> export {ninja as spy};
> ```
> 
> **A:**
> 
> 从模块外部，我们只能访问`spy`标识符：这是唯一一个被导出为`ninja`变量别名的标识符（选项 a）。
> 
> **4**
> 
> 以下哪些导入是被允许的？
> 
> ```
> //File: personnel.js
> const ninja = "Yagyu";
> function command(){
>   return general + " commands you to wage war!";
> }
> const general  = "Minamoto";
> 
> export {ninja as spy};
> ```
> 
> **A:**
> 
> 第一个导入不被允许，因为`personnel`模块没有导出`ninja`和`general`标识符（选项 a）。第二个导入是允许的，因为我们导入了整个模块，可以通过`Personnel`对象访问（选项 b）。第三个导入也是允许的，因为我们导入了导出的`spy`标识符（选项 c）。
> 
> **5**
> 
> 如果我们有以下模块代码，哪个语句将导入`Ninja`类？
> 
> ```
> //Ninja.js
> export default class Ninja {
>   skulk(){ return "skulking"; }
> }
> ```
> 
> **A:**
> 
> 第一个导入是允许的：我们导入默认导出（选项 a）。第二个导入是允许的：我们导入整个模块（选项 b）。第三个导入是不允许的，因为它在语法上不正确（在`*`之后应该跟“as Name”部分）（选项 c）。

### 第十二章\. 操作 DOM

> **1**
> 
> 在以下代码中，以下哪个断言会通过？
> 
> ```
> <div id="samurai"></div>
> <script>
>   const element = document.querySelector("#samurai");
>   assert(element.id === "samurai", "property id is samurai");
>   assert(element.getAttribute("id") === "samurai",
>          "attribute id is samurai");
> 
>   element.id = "newSamurai";
> 
>   assert(element.id === "newSamurai", "property id is newSamurai");
>   assert(element.getAttribute("id") === "newSamurai",
>          "attribute id is newSamurai");
> </script>
> ```
> 
> **A:**
> 
> 在此代码中，所有断言都通过。`id`属性和`id`属性是链接的；对其中一个的更改会反映在另一个上。
> 
> **2**
> 
> 给定以下代码，我们如何访问元素的`border-width`样式属性？
> 
> ```
> <div id="element" style="border-width: 1px;
>                          border-style:solid; border-color: red">
> </div>
> <script>
>   const element = document.querySelector("#element");
> </script>
> ```
> 
> **A:**
> 
> `element.border-width 表达式`没有太多意义。它计算`element.border`和变量`width`之间的差异，这绝对不是我们想要的。下一个选项`element.getAttribute ("border-width");`获取 HTML 元素的属性，而不是样式属性。最后，最后两个选项给出`1px`的值（选项 c 和 d）。
> 
> **3**
> 
> 哪个内置方法可以获取应用到某个元素上的所有样式（包括浏览器提供的样式、通过样式表应用的样式以及通过样式属性设置的属性）？
> 
> **A:**
> 
> 只有最后一个选项，`getComputedStyle`，是一个内置方法，可以用来获取某个 HTML 元素的计算样式（选项 c）。其他三个方法不包括在标准 API 中。
> 
> **4**
> 
> 布局销毁何时发生？
> 
> **A:**
> 
> 当我们的代码对 DOM 执行一系列连续的读取和写入操作时，就会发生布局销毁。这会导致较慢、响应性较差的 Web 应用程序。

### 第十三章\. 生存事件

> **1**
> 
> 为什么将任务添加到任务队列发生在事件循环之外很重要？
> 
> **A:**
> 
> 如果将任务添加到任务队列的过程是事件循环的一部分，那么在 JavaScript 代码执行期间发生的任何事件都将被忽略。这绝对是一个糟糕的想法。
> 
> **2**
> 
> 为什么事件循环的每次迭代不超过大约 16 毫秒很重要？
> 
> **A:**
> 
> 为了实现运行流畅的应用程序，浏览器试图每秒渲染大约 60 次。因为渲染是在事件循环的末尾执行的，所以每次迭代不应该超过 16 毫秒，除非我们想要创建缓慢且参差不齐的应用程序。
> 
> **3**
> 
> 运行以下代码 2 秒后的输出是什么？
> 
> ```
> setTimeout(function(){
>   console.log("Timeout ");
> }, 1000);
> 
> setInterval(function(){
>   console.log("Interval ");
> }, 500);
> ```
> 
> **A:**
> 
> `Interval Timeout Interval Interval Interval` (选项 b)。`setInterval`方法以至少每次调用之间的固定延迟调用处理程序，直到显式清除间隔。另一方面，`setTimeout`方法仅在指定的延迟过去后调用一次回调。在这个例子中，首先在 500 毫秒后触发一次`setInterval`回调。然后，在 1000 毫秒后调用`setTimeout`回调，紧接着又立即调用另一个`setInterval`。我们的检查在另外两个`setInterval`回调调用结束时停止，一个在 1500 毫秒，另一个在 2000 毫秒。
> 
> **4**
> 
> 运行以下代码 2 秒后的输出是什么？
> 
> ```
> const timeoutId = setTimeout(function(){
>   console.log("Timeout ");
> }, 1000);
> 
> setInterval(function(){
>   console.log("Interval ");
> }, 500);
> 
> clearTimeout(timeoutId);
> ```
> 
> **A:**
> 
> `Interval Interval Interval Interval` (选项 c)。在`setTimeout`回调有机会触发之前，它被清除，所以在这种情况下，我们只有四次`setInterval`回调的执行。
> 
> **5**
> 
> 运行以下代码并点击具有 ID `inner`的元素会产生什么输出？
> 
> ```
> <body>
>   <div id="outer">
>     <div id="inner"></div>
>   </div>
>   <script>
>     const innerElement = document.querySelector("#inner");
>     const outerElement = document.querySelector("#outer");
>     const bodyElement = document.querySelector("body");
>     innerElement.addEventListener("click", function(){
>       console.log("Inner");
>     });
> 
>     outerElement.addEventListener("click", function(){
>       console.log("Outer");
>     }, true);
> 
>     bodyElement.addEventListener("click", function(){
>       console.log("Body");
>     });
>   <script>
> </body>
> ```
> 
> **A:**
> 
> `Outer Inner Body` (选项 c)。在`innerElement`和`bodyElement`上的点击处理程序以冒泡模式注册，而`outerElement`上的点击处理程序以捕获模式注册。在处理事件时，事件首先从顶部开始，并调用所有捕获模式的事件处理程序。第一条消息将是`Outer`。在事件目标被达到后，在我们的例子中是具有 ID `inner`的元素，事件冒泡发生，事件向上传播。因此，第二条消息将是`Inner`，第三条将是`Body`。

### 第十四章。开发跨浏览器策略

> **1**
> 
> 决定支持哪些浏览器时，我们应该考虑哪些因素？
> 
> **A:**
> 
> 在决定支持哪些浏览器时，我们至少应该考虑以下因素：
> 
> +   目标受众的期望和需求
> +   
> +   浏览器的市场份额
> +   
> +   支持浏览器所需的努力量
> +   
> **2**
> 
> 解释贪婪 ID 的问题。
> 
> **A:**
> 
> 当与表单元素一起工作时，浏览器为每个具有 ID 的子元素添加属性到表单元素，这样我们就可以通过表单元素轻松访问这些元素。不幸的是，这可能会覆盖一些内置的表单属性，如`action`或`submit`。
> 
> **3**
> 
> 特征检测是什么？
> 
> **A:**
> 
> 特征检测通过确定某个对象或对象属性是否存在，如果存在，则假定它提供了隐含的功能。我们不是测试用户是否使用特定的浏览器，然后根据该信息实现解决方案，而是测试某个功能是否按预期工作。
> 
> **4**
> 
> 浏览器 polyfill 是什么？
> 
> **A:**
> 
> 如果我们想要使用某些所有目标浏览器都不支持的功能，我们可以使用功能检测。如果一个当前浏览器不支持某个功能，我们提供自己的实现，这被称为 polyfilling。
