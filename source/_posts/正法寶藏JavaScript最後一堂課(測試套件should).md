---
title: '正法寶藏JavaScript最後一堂課(測試套件should)'
date: 2016-10-13 12:10:00
categories:
 - [生活, 跨行學程式]
---
終於到最後一堂課了，這堂課的主題是用其中一款測試套件 ` should ` ，為什麼要使用測試工具呢？

- 人很粗心，總會犯錯。
- 方便修改程式，知道哪個環節出錯。
- 可以早點發現程式上的結構瑕疵。
- 利用測試訂出程式 spec。

那廢話不多說就下 ` $ npm install should --save ` 吧！

## 簡單的製作一個map.js檔案，裡面有一個使用map的function：
``` javascript
export default function map(arr, cb) {
  const length = arr.length;
  const newArr = [];
  let index = 0;
  while (index < length) {
    newArr.push(cb(arr[index], index));
    index++;
  }
  return newArr;
}
```

## 在同一個目錄裡創造一個資料夾裡面也創一個map.js的檔案用來測試：
``` javascript
import should from 'should';  //載入套件should(使用ES6語法)
import {map} from './../';    //載入上一層的map.js

describe('map', () => {  //第一個字串為敘述測試的東西，第二個function就是要如何進行測試。

  it('should have new reference', () => {

    const arr = ['apple', 'orange', 'banana'];
    const mappedArr = map(arr, (elem) => {
      return elem;
    });
    const res = (mappedArr === arr);
    res.should.equal(false);
  });

  //用法其實就跟if/else很相像，取名也很白話要讓測試者很清楚知道哪裡出錯。
  //有趣的是should have new reference的文字字串會接在map的文字字串後面，使用起來很像電腦再跟自己溝通。

  it('should have elem argument', () => {

    const arr = ['apple', 'orange', 'banana'];
    const elems = [];
    const mappedArr = map(arr, (elem, index) => {
      elems.push(elem);
      return elem;
    });

    elems.should.deepEqual(['apple', 'orange', 'banana']);
  });

  it('should have index argument', () => {

    const arr = ['apple', 'orange', 'banana'];
    const indices = [];
    const mappedArr = map(arr, (elem, index) => {
      indices.push(index);
      return elem;
    });

    indices.should.deepEqual([0, 1, 2]);
  });

  it('should use my callback function', () => {

    const arr = ['apple', 'orange', 'banana'];
    const mappedArr = map(arr, (elem) => ({fruit: elem}));

    mappedArr.should.deepEqual([
      {fruit: 'apple'},
      {fruit: 'orange'},
      {fruit: 'banana'}
    ]);
  });
});
```

## 課程總結

最後一堂課也結束了，一夥人去吳留手串燒店聚餐，這兩個月學習對我來說真的強度有點過大，不過我還是很感謝老師帶領我接觸了寫程式一整套流程所有可能會碰到的東西。

這一趟奇幻旅程對我來說很難用文字表達，從一開只會打幾個指令和程式碼，陸續碰到了Node.js,React框架,甚至還教我們如何製作爬蟲等等，兩個月這樣子真的是賺翻了，還想要繼續在編成的世界繼續冒險下去呀！
