---
title: '[Javascript]原生 JS functional default params'
date: 2020-05-25 17:16:33
categories:
 - [JavaScript]
tags:
 - JavaScript
---

想要實作出有 default params 的 functional
意思是希望能有這樣的效果
```javascript
myFunction({
  name: 'Kurt',
  age: 30
})
//=>
//name is Kurt
//age is 30
//sex is male
```

需要使用 ES6 的語法, 先來理解
```javascript
let hash = {
  name: 'Kurt',
  age: 30
}

// 我們可以直接拿 hash 裡面的值宣告變數
let { name, age } = hash
console.log(name)// => 'Kurt'
console.log(age) // => 30
```

回到一開始想做的範例, 搭配這個特性定義 default params 的 function 即可完成, 也可以寫一些基本 error condition 的狀況
```javascript
const myFunction = ({
  name = 'Adam',
  age = 10,
  sex = 'male'
}={}) => {
  if (typeof name !== 'string' ||
      typeof age !== 'number' ||
      typeof sex !== 'string') {
    throw 'Params typo invalid'
  }
  console.log(name)
  console.log(age)
  console.log(sex)
}

myFunction({
  name: 'Kurt',
  age: 30
})
//=>
//name is Kurt
//age is 30
//sex is male
```

有幾個點注意:
1. 不用照順序給參數
2. 參數少給或多給都沒關係
3. 多給的 params key 不存在會被省略

```javascript
myFunction({ sex: 'female', somekey: 'bla' })
//=>
//name is Adam
//age is 10
//sex is female
```
