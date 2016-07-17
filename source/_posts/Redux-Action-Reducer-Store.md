title: Redux-Action/Reducer/Store
date: 2016-07-17 21:32:13
Author: Rae
tags: 
  -javascript
categories: [javascript, react.js]
---

## Action
在redux中，網站與使用者一些的互動行為，都需要發出Action。如要發出一個Action則需利用redux原生的method:
```
store.dispath(ActionCreator(action));
```
<!-- more -->

而Action是由物件來描述，如：
```
const ADD_SOMETHING = 'ADD_SOMETHING'
{
  type: ADD_SOMETHING,
  text: 'Hello world',
}
```
在redux中，Action Type傳統來看類型會是String。

ActionCreator則是一個pure function，即表示此fucntion不會改變任何傳進此函數的參數。
No surprises , No side effects. No API calls. No mutations. Just a caculation.

結合上面的想法，簡單的例子如下:
```
const addSomething = (text) => dispatch(addToDo(text));
```
當然，如果你懶得寫ActionCreator，也可以這樣寫
```
const addSomething = (text) => dispatch({
  type: ADD_SOMETHING,
  text: 'Hello world',		
});
```

## Reducer
如果說Action是工頭，那Reducer就是工人，也就是實際上去做事情的角色。
在此要強調Reducer是一個pure function，例子如下:
```
function addSomething(state = initialState, action) {
 switch(action.type) {
   case ADD:
     return Object.assign({}, state, {text:'33'});
   case MINUS:
     ....同上
   default:
     return state;
 }		
}
```

### Tip1
順便一提，assign會將原本的state(第二個參數)加上新的屬性質(第三個參數)，回傳一個全新的物件，並不會修改原本的物件。
特別注意的是，assign的第三個參數，如果跟原本的state裡面的屬性質有一樣時，會修改其值，如果原本物件
沒有此屬性，會新增。

### Tip2
在原生的設計，如果你沒有任何寫任何的Action，Reducer也是會全部執行，所以為什麼一定要寫default就是這原因。

## Store
Store顧名思義，就是一個儲存庫，其類型就是一個物件，即用一個物件來儲存資料，他能接收來源是Action，也就是一個物件。
它有三個重要的函數
(1)getState() : 得到目前的state
(2)dispatch() : 發出一個Action
(3)subcribe(listener) : 將store綁在一個你認為如果它變動，store必須跟著變動的函數

dan 網頁中的例子如下:
```
function select(state) {
  return state.some.deep.property
}

let currentValue
function handleChange() {

  let previousValue = currentValue
  currentValue = select(store.getState())
  if (previousValue !== currentValue) {
    console.log('Some deep nested property changed from', previousValue, 'to', currentValue)
  }
}

let unsubscribe = store.subscribe(handleChange)
handleChange()
```

更多想知道的部分，請參考
[redux](http://redux.js.org/index.html)
