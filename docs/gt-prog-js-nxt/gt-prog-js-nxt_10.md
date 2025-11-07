## 附录. 练习题答案

### 第 4 课

> **A1:**
> 
> ```
> {
>   let DEFAULT_START = 0;
>   let DEFAULT_STEP  = 1;
> 
>   window.mylib.range = function (start, stop, step) {
>     let arr = [];
> 
>     if (!step) {
>       step = DEFAULT_STEP;
>     }
> 
>     if (!stop) {
>       stop = start;
>       start = DEFAULT_START;
>     }
> 
>     if (stop < start) {
>       // reverse values
>       let tmp = start;
>       start = stop;
>       stop = tmp;
>     }
> 
>     for (let i = start; i < stop; i += step) {
>       arr.push(i);
>     }
> 
>     return arr;
>   }
> }
> ```

### 第 5 课

> **A1:**
> 
> ```
> {
>   const DEFAULT_START = 0;
>   const DEFAULT_STEP  = 1;
> 
>   window.mylib.range = function (start, stop, step) {
>     const arr = [];
> 
>     if (!step) {
>       step = DEFAULT_STEP;
>     }
> 
>     if (!stop) {
>       stop = start;
>       start = DEFAULT_START;
>     }
> 
>     if (stop < start) {
>       // reverse values
>       const tmp = start;
>       start = stop;
>       stop = tmp;
>     }
> 
>     for (let i = start; i < stop; i += step) {
>       arr.push(i);
>     }
> 
>     return arr;
>   }
> }
> ```

### 第 6 课

> **A1:**
> 
> ```
> function
> maskEmail(email, mask) {
>   if (!email.includes('@')) {
>     throw new Error('Invalid Email');
>   }
>   if (!mask) {
>     mask = '*';
>   }
>   const atIndex = email.indexOf('@');
>   const masked = mask.repeat(atIndex);
>   const tld = email.substr(atIndex);
> 
>   return masked + tld;
> }
> ```

### 第 7 课

> **A1:**
> 
> ```
> function
> withProps() {
>   let stringParts = arguments[0];
>   let values = [].slice.call(arguments, 1);
>   return stringParts.reduce(function(memo, nextPart) {
>     let nextValue = values.shift();
>     if (nextValue.constructor === Object) {
>       nextValue = Object.keys(nextValue).map(function(key) {
>         return `${key}="${nextValue[key]}"`;
>       }).join(' ');
>     }
>     return memo + String(nextValue) + nextPart;
>   });
> }
> 
> let props = {
>   src: 'http://fillmurray.com/100/100',
>   alt: 'Bill Murray'
> };
> 
> let img = withProps`<img ${props}>`;
> 
> console.log(img);
> // <img src="http://fillmurray.com/100/100" alt="Bill Murray">
> ```

### 第 9 课

> **A1:**
> 
> ```
> function $(query) {
>   let nodes = document.querySelectorAll(query);
>   return {
>     css: function(prop, value) {
>       Array.from(nodes).forEach(function(node) {
>         node.style[prop] = value;
>       });
>       return this;
>     }
>   }
> }
> ```

### 第 10 课

> **A1:**
> 
> ```
> // helper function (not required but removes a lot of boilerplate)
> function
> inherit() {
>   return Object.assign.apply(Object, [{}].concat(Array.from(arguments)))
> }
> 
> const PASSENGER_VEHICLE = {
>   board: function() {
>     // add passengers
>   },
>   disembark: function() {
>     // remove passengers
>   }
> }
> 
> const AUTOMOBILE = inherit(PASSENGER_VEHICLE, {
>   drive: function() {
>     // go somewhere
>   }
> })
> 
> const SEA_VESSEL = inherit(PASSENGER_VEHICLE, {
>   float: function() {
>     // go somewhere
>   }
> })
> 
> const AIRCRAFT = inherit(PASSENGER_VEHICLE, {
>   fly: function() {
>     // go somewhere
>   }
> })
> 
> const AMPHIBIAN_AIRCRAFT = inherit(SEA_VESSEL, AIRCRAFT, {
>   // do some other stuff
> })
> ```

### 第 11 课

> **A1:**
> 
> ```
> let {
>   name: { first: firstName, last: lastName },
>   address: { street, region, zipcode }
> } = potus;
> 
> let [{
>   name: firstProductName,
>   price: firstProductPrice,
>   images: [firstProductFirstImage]
> },{
>   name: secondProductName,
>   price: secondProductPrice,
>   images: [secondProductFirstImage]
> }] = products
> ```

### 第 12 课

> **A1:**
> 
> ```
> function
> createStateManager() {
>   const state = {};
>   const handlers = [];
>   return {
>     onChange(handler) {
>       if (handlers.includes(handler)) {
>         return;
>       }
>       handlers.push(handler);
>       return handler;
>     },
>     offChange(handler) {
>       if (!handlers.includes(handler)) {
>         return;
>       }
>       handlers.splice(handlers.indexOf(handler), 1);
>       return handler;
>     },
>     update(changes) {
>       Object.assign(state, changes);
>       handlers.forEach(function(cb) {
>         cb(Object.assign({}, changes));
>       });
>     },
>     getState() {
>       return Object.assign({}, state);
>     }
>   }
> }
> ```

### 第 13 课

> **A1:**
> 
> ```
> function
> makeSerializableCopy(obj) {
>   return Object.assign({}, obj, {
>     [Symbol.toPrimitive]() {
>       return Object.keys(this).map(function(key) {
>         const encodedKey = encodeURIComponent(key);
>         const encodedVal = encodeURIComponent(this[key].toString());
>         return `${encodedKey}=${encodedVal}`;
>       }, this).join('&');
>     }
>   });
> }
> 
> const myObj = makeSerializableCopy({
>   total: 1307,
>   page: 3,
>   per_page: 24,
>   title: 'My Stuff'
> });
> 
> const url = `http://example.com?${myObj}`;
> ```

### 第 15 课

> **A1:**
> 
> ```
> function car(name, seats = 4) {
>   return {
>     name,
>     seats,
>     board(driver, ...passengers) {
>       console.log(driver, 'is driving the', this.name)
>       const passengersThatFit = passengers.slice(0, this.seats - 1)
>       const passengersThatDidntFit = passengers.slice(this.seats - 1)
>       if (passengersThatFit.length === 0) {
>         console.log(driver, 'is alone')
>       } else if (passengersThatFit.length === 1) {
>         console.log(passengersThatFit[0], 'is with', driver)
>       } else {
>         console.log(passengersThatFit.join(' & '), 'are with', driver)
>       }
>       if (passengersThatDidntFit.length) {
>         console.log(passengersThatDidntFit.join(' & '), 'got left behind')
>       }
>     }
>   }
> }
> ```

### 第 16 课

> **A1:**
> 
> ```
> function
> updateMap({ zoom, center, coords = center, bounds }) {
>   if (zoom) {
>     _privateMapObject.setZoom(zoom);
>   }
>   if (coords) {
>     _privateMapObject.setCenter(coords);
>   }
>   if (bounds) {
>     _privateMapObject.setBounds(bounds);
>   }
> }
> ```

### 第 17 课

> **A1:**
> 
> ```
> const translator = lang => (strs, ...vals) => strs.reduce(
>   (all, str) => all + TRANSLATE(vals.shift(), lang) + str
> );
> ```

### 第 18 课

> **A1:**
> 
> ```
> function* dates(date = new Date()) {
>   for (;;) {
>     yield date;
>     // clone the date
>     date = new Date( date.getTime() );
>     // increment the clone
>     date.setDate( date.getDate() + 1 );
>   }
> }
> ```

### 第 20 课

> **A1:**
> 
> ```
> export let luckyNumber = Math.round(Math.random()*10)
> export function guessLuckyNumber(guess) {
>   return guess === luckyNumber
> }
> ```

### 第 21 课

> **A1:**
> 
> ```
> import guessLuckyNumber from './luck_numbery'
> 
> let foundNumber = false
> 
> for (let i = 1; i <= 50 i++) {
>   if (guessLuckyNumber(i)) {
>     console.log('Guessed the correct number in ', i, 'attempts!')
>     foundNumber = true
>     break
>   }
> }
> 
> if (!foundNumber) {
>   console.log('Could not guess number.')
> }
> ```

### 第 23 课

> **A1:**
> 
> ```
> function
> take(n, iterable) {
>   i = 0
>   const items = []
>   for (const val of iterable) {
>     items.push(val)
>     i++
>     if (i === n) break
>   }
>   return items
> }
> 
> function* fibonacci() {
>   let prev = 0
>   let curr = 1
>   while(true) {
>     yield curr;
>     [prev, curr] = [curr, prev + curr]
>   }
> }
> 
> take(10, fibonacci()) // [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
> 
> function* two() {
>   yield 1
>   yield 2
> }
> 
> take(10, two()) // [1, 2]
> ```

### 第 24 课

> **A1:**
> 
> ```
> function
> union(a, b) {
>   return new Set([ ...a, ...b ]);
> }
> 
> function intersection(a, b) {
>   const inBoth = new Set();
>   for (const item of union(a, b)) {
>     if (a.has(item) && b.has(item)) {
>       inBoth.add(item);
>     }
>   }
>   return inBoth;
> }
> 
> function subtract(a, b) {
>   const subtracted = new Set(a);
>   for (const item of b) {
>     subtracted.delete(item);
>   }
>   return subtracted;
> }
> 
> function difference(a, b) {
>   return subtract(
>     union(a, b),
>     intersection(a, b)
>   );
> }
> ```

### 第 25 课

> **A1:**
> 
> ```
> function
> sortMapByKeys(map) {
>   const sorted = new Map();
>   const keys = [ ...map.keys() ].sort();
>   for (const key of keys) {
>     sorted.set(key, map.get(key));
>   }
>   return sorted;
> }
> 
> function invertMap(map) {
>   const inverted = new Map();
>   for (const [ key, val] of map) {
>     inverted.set(val, key);
>   }
>   return inverted;
> }
> 
> function sortMapByValues(map) {
>   return invertMap(sortMapByKeys(invertMap(map)))
> }
> ```

### 第 27 课

> **A1:**
> 
> ```
> class Fish {
>   hunger = 1;
>   dead = false;
>   born = new Date();
> 
>   constructor(name) {
>     this.name = name;
>   }
> 
>   eat(amount=1) {
>     if (this.dead) {
>       console.log(`${this.name} is dead and can no longer eat.`);
>       return;
>     }
>     this.hunger -= amount;
>     if (this.hunger < 0) {
>       this.dead = true;
>       console.log(`${this.name} has died from over eating.`)
>       return
>     }
>   }
> 
>   sleep() {
>     this.hunger++;
>     if (this.hunger >= 5) {
>       this.dead = true;
>       console.log(`${this.name} has starved.`)
>     }
>   }
> 
>   isHungry() {
>     return this.hunger > 0;
>   }
> }
> ```

### 第 28 课

> **A1:**
> 
> ```
> class Cruiser extends Car {
>   drive(miles=1) {
>     const destination = this.milage + miles;
>     while(this.milage < destination) {
>       if (!this.hasGas()) this.fuel();
>       super.drive();
>     }
>   }
> 
>   fuel() {
>     this.gas = 50;
>   }
> }
> ```

### 第 30 课

> **A1:**
> 
> ```
> function loadCreditAndScore(userId) {
>   return Promise.all(
>     ajax(`/user/${userId}/credit_availability`),
>     Promise.race(
>       ajax(`/transunion/credit_score?user=${userId}`),
>       ajax(`/equifax/credit_score?user=${userId}`)
>     )
>   )
> }
> 
> loadCreditAndScore('4XJ').then(([creditAvailability, creditScore]) => {
>   // Do something with credit availability and credit score
> })
> ```

### 第 31 课

> **A1:**
> 
> ```
> function
> loadAsync(url) {
>   return new Promise((resolve, reject) => {
>     load(url, (error, data) => {
>       if (error) {
>         reject(error);
>       } else {
>         resolve(data);
>       }
>     })
>   });
> }
> 
> function getArticle(id) {
>   return Promise.all([
>     loadAsync(`/articles/${id}`),
>     loadAsync(`/articles/${id}/comments`)
>   ]).then(([article, comments]) => Promise.all([
>     article,
>     comments,
>     loadAsync(`/authors/${article.author_id}`)
>   ]));
> }
> 
> getArticle(57).then(
>   results => {
>     // render article
>   },
>   error => {
>     // show error
>   }
> );
> ```

### 第 32 课

> **A1:**
> 
> ```
> async function
> getArticle(id) {
>   const article = await loadAsync(`/articles/${id}`);
>   const comments = await loadAsync(`/articles/${id}/comments`);
>   const author = await loadAsync(`/authors/${article.author_id}`);
>   return [ article, comments, author ];
> }
> ```

### 第 33 课

> **A1:**
> 
> ```
> function
> collect(obs$) {
>   const values = [];
>   return new Observable(observer => obs$.subscribe({
>     next(val) {
>       values.push(val);
>       observer.next(values);
>     }
>   }));
> }
> 
> function sum(obs$) {
>   return new Observable(observer => obs$.subscribe({
>     next(arr) {
>       observer.next(arr.reduce((a, b) => a + b))
>     }
>   }));
> }
> 
> sum(collect(Observable.of(1, 2, 3, 4))).subscribe({
>   next(val) {
>     console.log('sum:', val);
>   }
> });
> ```
