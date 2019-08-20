---
title: '[socket.io]初探socket client端使用與設置'
date: 2017-05-22 02:40:00
categories:
 - [socket.io]
tags:
 - socket.io
---
工作上要實作一個聊天室，網路上最簡單的套件就是socket了，我是負責客戶端的程式碼，所以伺服器端的我不熟XD，但是socket的文件寫的不是很完整讓我摸了兩天才大概知道怎麼用囧，所以在這邊做一下記錄。

先在這邊放上[socket的網站](https://socket.io/)
還有[socket的Github](https://github.com/socketio/socket.io)

### 初始連線
我們先假設你的socket已經安裝好，而且親愛的後端已經把伺服器寫好了，這時候我們要先初始化socket的連線：
``` javascript
var socket = new io.connect('伺服器地址和port');
```

此時我們來看看[Github上socket裡doc的Client API文件](https://github.com/socketio/socket.io-client/blob/master/docs/API.md)，裡面是關於客戶端的設定還有操作，可以看到中分類有兩個Class，一個是`io.Manager是關於socket初始化的管理`還有` io.Socket是關於socket的動作`，剛剛我們那行程式碼已經是初始化完socket了所以我們看動作的部分有內建的`Event`。

文件裡的Event是內建的，像是`Event: 'connect'`代表連線後要做的事情，使用方法：
``` javascript
socket.on('connect', function(){
    console.log('Socket connect!);
});
```
伺服器收接到連線成功之後會自動發射一個connect給客戶端，這樣就完成最簡單的連線囉！當然也可以把錯誤的連線設定好：
``` javascript
socket.on('connect_error', function(){
    console.log('Socket has something wrong!);
});
```

### emit和on的動作
>socket伺服器端和客戶端的聯繫全程是使用emit(發射)和on(接收)去溝通的

`不論哪一端只要emit(發射)一個命令，另一端就一定要有on(接收)去接收他`，除非是內建的Event，就像是剛剛的連線一樣，伺服器就不用特地再寫socket.emit('connect');發射給客戶端說連線成功。

如果連線成功，接下來就可以很自由的玩基本的即時聊天功能囉！例如：
簡單的伺服器端
``` javascript sever端
socket.on('New chat', function () {
    socket.emit('Reflash');
});
```
搭配客戶端
``` javascript client端
function submit () {
    socket.emit('New chat');
}
socket.on('Reflash', function () {
    reflashView();
});
```
這樣就是一個簡易的聊天互動了！

### Manager(初始化管理)
這邊真的是研究了一段時間才知道怎麼用，可以先看[網頁版的socket](https://socket.io/docs/client-api/)，他的敘述大概是一個新的[Manager](https://socket.io/docs/client-api/#new-manager-url-options)可以重複給sockit使用，其實我不太懂意思，但是如果你一個頁面只需要一個sockit的話應該是不用理這個，不過重要的是`如果你的聊天頁面網址是http://localhost:3000/users，在連線指令只要下：`
``` javascript
new io.connect('http://localhost:3000');
```
`就可以了！`

此時再來看看[Github上socket裡doc的Client API文件](https://github.com/socketio/socket.io-client/blob/master/docs/API.md)，`Class: io.Manager`有很多`manager.something`的東西，譬如說設定最多連線次數的東西`manager.reconnectionAttempts([value])`，使用方法如下：

``` javascript
var socket = new io.connect('伺服器地址和port',{
    'reconnectionAttempts': 5
});
```
這樣就行了！

### 經驗談
接下來稍微講一下socket特性的地雷，所以我才會覺得他的文件不是寫得很完整XD。

剛剛manager的範例是設定最大連線次數5次，但是如果沒有設定`'autoConnect': false`的話他還是會繼續嘗試連線然後不斷報錯喔囧，但要記得！！如果設定了false要主動使用`socket.open();`去打開連線。

再來`'reconnection': true是代表連線中如果斷線了要不要自動重新連線`。

`'reconnectionDelay': 2000,'reconnectionDelayMax': 5000,'randomizationFactor': 0.8似乎是一國的，是調整每次重新連線的時間`，但是要怎麼切確調整我不太熟悉，不過以上是我的設定。

`'timeout': 5000是連線五秒還沒有成功的話就跳connect_error`，預設似乎是20秒。

我會更希望不斷提醒使用者scoket在做什麼，譬如說每次重新連線我都顯示一個提示，使用它內建的Event：
``` javascript
var connectTime = 0;
socket.on('connect_error', function () {
    console.log('connect_error');
    if ( connectTime < 5) {
        connectTime ++;
    } else {
        socket.close();
        console.log('Failed too many times, please check internet!');
    }
});
```
為什麼要再下`socket.close();`呢？我們不是設定`reconnectionAttempts: 5了嗎`？

這是真的把socket的連線全部斷開，一定要再使用`socket.open();`才能重新開啟連線，這是比較保險的做法，因為不知道為什麼有時候他會不理我的`reconnectionAttempts`限定次數繼續爆衝連線啊囧。

以上是小弟跌跌撞撞使用socket的經驗XD，希望能幫助大家也更希望有高手指點迷津。
