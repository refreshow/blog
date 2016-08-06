title: Redux Saga介紹
date: 2016-08-02 08:13:55
Author: Rae
tags: javascript
categories: [Javascript, React.js]
---
# 前言

Async Action是實際應用常會發生的情況，它可以用來處理非同步的事件，例如對伺服器發
出request。而支援非同步Action，其實不只Saga，這類型的處理也包含Redux-promise，
Redux-thunk..等等，那為什麼選擇Saga?這理由很多，其中我個人認為包含

<!--more-->

1. Generator function的特性帶來更強大的非同步操作(ex:long lived transaction)
2. 支援平行化的處理，thread-like的行為
3. 非常方便測試
4. 讓你的ActionCreator是Pure的(若用redux-thunk則action creator會有side effect的問題)
..等

下面有兩個有趣的教材，推薦想要更進一步了解saga的朋友可以看看。

1. [2016 React-europe: Manage side-effects efficiently with Redux Saga](https://www.youtube.com/watch?v=QJVdcIlqGwc)
2. [Master Complex Redux Workflows with Sagas](http://konkle.us/master-complex-redux-workflows-with-sagas/)

接下來，用一個普遍call遠端api的例子，來說明redux-saga的用法。在開始之前，有幾個概念需要了解:

1. 須了解redux middleware的概念
2. saga利用middleware的概念，它介於ActionCreator跟reducer間
3. generator function須事先了解

# 第一步:引入redux-saga
```
import "babel-polyfill"
import React from 'react'
import ReactDOM from 'react-dom'
import Gallery from './Gallery';
import reducer from './reducer';
import {Provider} from 'react-redux';


import { createStore, applyMiddleware } from 'redux';
import createSagaMiddleware from 'redux-saga';
import {loadImages} from './saga';
const store = createStore(reducer, applyMiddleware(createSagaMiddleware(loadImages)));

ReactDOM.render(
  <Provider store={store}>
    <Gallery/>
  </Provider>
  ,
  document.getElementById('root')
);

```
觀察一下第9行到12行。
我們從redux中取出applyMiddleware，前面說過saga本身就是一個Middleware的概念，
所以從redux-saga中取出createSagaMiddleware這個方法，最後將我們寫的saga檔丟進
createSagaMiddleware這方法中。

### Gallery Component

```javascript
import React, {Component} from 'react'
import {connect} from 'react-redux';
import {bindActionCreators} from 'redux';
import * as GalleryAction from './action';


export class Gallery extends Component {
  componentDidMount() {
    this.props.loadImages();
  }
  render() {
    const {images, selectedImage, selectImage } = this.props;
    return (
      <div className="image-gallery">
        <div className="gallery-image">
          <div>
          <img src={selectedImage} />
          </div>
          </div>
          <div className="image-scroller">
            {images.map((image, index) => (
              <div key={index} onClick={() => selectImage(image)}>
                <img src={image}/>
              </div>
              ))
            }
          </div>
      </div>
      )
      }
    }

function mapStateToProps(state) {
  return {
    images: state.images,
    selectedImage: state.selectedImage,
  }
}
function mapActionCreatorsToProps(dispatch) {
  return bindActionCreators(GalleryAction,dispatch);
}
export default connect(mapStateToProps,mapActionCreatorsToProps)(Gallery);

```




# 第二步：寫Saga
Saga即是generator function，saga檔如下：
```javascript
import {fetchImages} from './flickr'; //從flickr提供的API抓Images回來
import {put, take, fork} from 'redux-saga/effects'; // saga本身提供的方法

export function* loadImages() {
  const images = yield fetchImages();// fetchImage返回promise
  yield put({type: 'IMAGES_LOAD', images});
  yield put({type: 'IMAGE_SELECTED', image: images[0]});
}

//用來監測ActionCreator是否有發出我們想偵測的Action 
export function* watchForLoadImages() {
  while(true) {
    yield take('LOAD_IMAGES') // 偵測是否有type為'LOAD_IMAGES'的action發出
    yield fork(loadImages);   //若有的話，則進行這行
  }
}
```
提醒一下，redux-saga是介於actionCreator與reducer之間的middleware，所以他會去監聽
ActionCreator發了什麼Action，這邊我們寫一個監聽的GF，watchForLoadImages就是這個用途。
我們來看看此function的內部，第一個take表示他會『主動』去監聽有LOAD_IMAGES這個type的Action，
一旦ActionCreator發出此Action，就會被catch到，此時就會執行下一個敘述句
```
yield fork(loadImages);
```
當然你也可以寫成
```
yield loadImages();
```
但如果這樣寫就變成blocking的行為，即表示一次只能執行一次LOAD_IMAGES這個workflow，
如果寫成fork就可以同時執行這workflow。接著就會執行loadImages這個GF
```javascript
export function* loadImages() {
  const images = yield fetchImages();
  yield put({type: 'IMAGES_LOAD', images});
  yield put({type: 'IMAGE_SELECTED', image: images[0]});
}
```
這邊要特別提醒一下，Generator function是用很像同步的寫法去表達非同步。
而put的功用類似redux中的dispatch。

整體的流程:
1. 使用者在Gallery這個Component按下onClick
2. 因為mapActionCreatorsToProps當作connect的參數的關係，所以可以直接在此Component呼叫
   事先在ActionCreator寫好的pure function，在此Component就是selectImage
3. 接著流程走到ActionCreator中的function，會丟出Action
4. 若丟出的Action是我們假定要用saga處理的，則必須在saga檔中寫一個watch(監視)去不斷的看看系統是否有丟出此Action
5. 若丟出只是一般的Action，則走一般redux的流程，也就是此Action會去找reducer
6. 接著4，假設異步的Action發出，被watch這函數偵測到，則進行watch函數中的指定的步驟
7. 執行完指定的步驟後(通常這裡是我們寫的generator function)，則用put通知reducer可以動作了

特別提醒，這裡的yield，並不會停住，假設以下列的程式碼為例子
```
export function* watchForLoadImages() {
  while(true) {
    yield take('LOAD_IMAGES') // 偵測是否有type為'LOAD_IMAGES'的action發出
    yield fork(loadImages);   //若有的話，則進行這行
  }
}
```
假設我現在在ActionCreator那邊發出一個Action type為LOAD_IMAGES的Action，則會被
saga裡面的watch函數偵測到，於是就會執行
```
yield take('LOAD_IMGAES');
```
這時候，你會有疑問，是不是要某個地方下next()才會繼續往下跑一行。
但其實不會，他會直接繼續執行。這邊其實筆者也不是很懂，反正把這些code當作yield不存在是我現在的做法XD
感覺只有在測試的時候才比較可以看得出generator function的精神。

# 測試
寫到這邊，其實Saga已經寫完了，剩下則是Testing的部分。
在saga中，非同步的測試碼很好撰寫的原因，歸功於generator function，在saga中，比較常用的
好像是[tape](https://github.com/substack/tape)，所以我們用tape來撰寫test碼

我們先新增一個test.js，上面有寫好一些基本的程式碼
```javascript
import test from 'tape';
import {put, take} from 'redux-saga/effects'
import {watchForLoadImages, loadImages} from './sagas';
import {fetchImages} from './flickr';

test('watchForLoadImages', assert => {
  const generator = watchForLoadImages();
  //寫你要測試的程式碼
  assert.end();
});
```
接著我們使用deepEqual這個方法，他接受兩個參數，並確認第一個參數的結果是否與第二個相等，
```
import test from 'tape';
import {put, take} from 'redux-saga/effects'
import {watchForLoadImages, loadImages} from './sagas';
import {fetchImages} from './flickr';

test('watchForLoadImages', assert => {
  const generator = watchForLoadImages();

  assert.deepEqual(
    generator.next().value, //next是GF的語法
    false,
    'watchForLoadImages should be waiting for LOAD_IMAGES action'
  );

  assert.end();
});
```
如果這時候你下npm test，則會得到下面的錯誤訊息
```
✖ watchLoadImages should be waiting for LOAD_IMAGES action
-----------------------------------------------------------
  operator: deepEqual
  expected: |-
    false
  actual: |-
    { TAKE: 'LOAD_IMAGES' }
```
從上面的訊息得知，他期待的是得到
```
{ TAKE: 'LOAD_IMAGES' }
```
接著，我們在原本的測試檔中，增加下面的code
```
 assert.deepEqual(
   gen.next().value,
    false,
    'watchForLoadImages should call loadImages after LOAD_IMAGES action is received'
  );
```
因為GF的next()，所以就會從上一次執行yield那一行在開始往下執行

如果在下npm test就會得到
```
✖ watchForLoadImages should call loadImages after LOAD_IMAGES action is received
---------------------------------------------------------------------------------
  operator: deepEqual
  expected: |-
    false
  actual: |-
    { FORK: { args: [], context: null, fn: [Function: loadImages] } }

```
我們可以很清楚看到，我們期待的是得到
```
{ FORK: { args: [], context: null, fn: [Function: loadImages] } }
```
同時我們也要了解
> 我們不會真的去執行loadImages這個function，取而代之的是我們只是測試他函數名是否有正確

# 結論
以上只是Saga的簡短介紹，但Saga其實不只如此，有興趣可以去[初級教程 Redux-Saga](http://leonshi.com/redux-saga-in-chinese/docs/introduction/BeginnerTutorial.html)
研究看看，還有如果有講的不對的地方，也請告知。


### 參考資料[Build an Image Gallery Using React, Redux and redux-saga](http://joelhooks.com/blog/2016/03/20/build-an-image-gallery-using-redux-saga)
