---
title: '[JavaScript]map取代push取得Json裡的資料問題紀錄'
date: 2017-03-22 08:03:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - map
---
# map取代push實例
今天跟一起寫code的群組討論到JS世界裡盡量不要去使用直接改變變數的function，例如這邊的push，雖然我還不太懂切確的觀念是什麼不過還是分享一下朋友教的寫法，這種方法似乎比較精簡也直觀。


先紀錄我原本的寫法，在接取api的資料經常會碰到這種Json格式，我想要取得每個物件的name：
``` javascript
var apiArray = [
{
    name: 'Kurt',
    age: 25
},
{
    name: 'Andy',
    age: 40
},
{
    name: 'Maggie',
    age: 20
},
{
    name: 'Mary',
    age: 35
}];
var nameArray = [];
for (var key in apiArray) {
    nameArray.push(apiArray[key].name);
}
console.log(nameArray); //["Kurt", "Andy", "Maggie", "Mary"]
```

朋友教我使用map去擷取：
``` javascript
var apiArray = [
{
    name: 'Kurt',
    age: 25
},
{
    name: 'Andy',
    age: 40
},
{
    name: 'Maggie',
    age: 20
},
{
    name: 'Mary',
    age: 35
}];
var nameArray = apiArray.map(function(obj){
    return obj.name;
});
console.log(nameArray); //["Kurt", "Andy", "Maggie", "Mary"]
```
兩種方法在這種小地方或許看不出來，但就直觀維護上這種方法看到`return obj.name`就大略知道在做什麼事情了。
