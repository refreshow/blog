title: ES6-New Variable
date: 2016-08-06 20:51:23
tags: javascript
Author: Rae
categories: [Javascript]
---

隨著ES6的普及，在Javascript中，宣告一個新的變數有三種方式
1. var
2. let
3. const

# function scoping vs block scoping
第一種大家不陌生，但要特別注意它是**function scoping**，舉一些例子：
第一個例子，暖暖身，這是很單純的用法，不再解釋
```
var width = 500;
function hello(){
  console.log(width);
}

//---console---
//500
```
第二個例子
```javascript
function f(){
  var width = 500;
}
```
若在console中輸入
```
width or window.width
```
答案是會是
> 500
why? 因為var是function scoping，我們若把var換成let和const，則會出現
```
Uncaught ReferenceError: width is not defined(…)
```
因為let和const是block scoping。

再來一個經典的問題
```
for(var i=0;i<10; i++){
  setTimeout( function() { console.log('The number is ' + i) },1000);
}
```
如果你將上面的這串丟到console就會得到一個結果
> (10) The number is 10 <--跑10次

如果你將上面的var改成let
就會跑
> The number is 0
> The number is 1
> ...跑到底

why? 如果你用第一種寫法，因為i是function scoping，而且setTimeout其實是一種非同步的
函數，所以當1秒後，i早就跑到10了，才開始跑console.log('The number is' + i )，所以
這時候全部都是The number is 10

但如果用let為什麼會正常呢？ 因為let是block scoping所以，你可以把每一次重複繞的環圈的
{ }視作不同的block，而這些不同的{ }裡面有let宣告的變數，雖然名稱一樣，但是{ }不同
所以值都會保留下來

# let vs const
let是ES6宣告變數的新方式，由上面知道它除了是block scoping外，它還有一些特色，如:
```javascript
let points = 50;
let points = 60;
```
會出現
> VM473:1 Uncaught TypeError: Identifier 'points' has already been declared(…)
也就是let宣告的『變數』(非常數) **在同一個block中**，無法在宣告第二次，而var就不會有此限制，這算是一種
保護機制，避免變數在專案越來越大，協同越來越多的情況下的措施。

再來看看下面例子
```
let abc = false;
let math = 50;
if(math>30) {
  let abc = true;
}
```
若在console中輸入abc，則答案是false，why? 因為let是block scoping，第一個abc是屬於
window也就全域這個block，而第二個abc是屬於if這個block，雖然名稱相同，但是不同block
是不會出錯的。

接下來我們來介紹const，顧名思義，就是常數，除了一定要初始化外，要注意的事情是
```
const person = {
  name: 'tom',
  age: 10,
}
```
我們依然可以用下列方式來改變其值
```
person.name = 'john';
person.age = 12;
```
如果想讓物件無法被修改，可以使用
```
const newPerson = Object.freeze(person);
```

# 結論
網路上不少文章有相關的比較: var, const, let，其中一些人認為var不應該存在於
ES6，但每個人想法不同，就讓各位去思考和評判。

留下一些文章，讓有興趣深入研究的人可以看看
> [JavaScript ES6+: var, let, or const?](https://medium.com/javascript-scene/javascript-es6-var-let-or-const-ba58b8dcde75#.jbsmngybz)
> [De Stijl: How necessary are var, let, and const?](http://raganwald.com/2015/05/30/de-stijl.html)
