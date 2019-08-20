---
title: '[Vue2]使用semantic modal 遇到 at HTMLDivElement.invoker'
date: 2017-07-25 08:49:00
categories:
 - [Vue2]
tags:
 - Vue2
---
我用的用法有點特別，算是硬改的所以引爆了一顆地雷，但是亂搞又自己救回來了XD

首先我們知道正常的方式是可行的：

``` html
<div id="clickme" @click="openModal">
    click me!
</div>
<div id="modal_signin" class="ui basic modal">
    Show modal
</div>
```

``` javascript
    new Vue({
        el: '#clickme',
        methods: {
            openModal () {
                 $('#modal_signin').modal('show');
            }
        }
    });
```

## 錯誤fns.apply is not a function at HTMLDivElement.invoker
因某些因素所以我用的方法很奇耙，大概類似這樣：

``` html
<div id="clickme" @click="test">
    click me!
</div>
```

``` javascript
    var obj = {
        el: '#clickme',
        data: {
            openModal: 'openModal'
            //注意一下這個data名稱，與第二個錯誤有關
        },
        methods {
            openModal () {
                 $('#modal_signin').modal('show');
            }
        }
    }
    var temp =
        '\<div>'+
            '\<div id="clickme" @click="openModal">'+
                'click me!'+
            '\</div>'+
            '\<div id="modal_signin" class="ui basic modal">'+
                'Show modal'+
            '\</div>'+
        '\</div>'

    obj.template = temp;

    new Vue(obj);
```

此時如果click`則會報fns.apply is not a function at HTMLDivElement.invoker`






## 錯誤openModal is not a function at click at HTMLDivElement.invoker
照著上面的範例，如果我把click改成`@click="openModal()"`則會報這個錯誤，`但其實是method的名稱不能跟data的變數名稱重複`，我也不知道發生了什麼事，總之趕快把問題紀錄下來～






## 最後解決方法
所以上面的代碼完全不動複製下來，只要改動一下：

``` html
<div id="clickme" @click="test">
    click me!
</div>
```

``` javascript
    var obj = {
        el: '#clickme',
        data: {
            openModal: 'openModal'
            //注意一下這個data名稱，與第二個錯誤有關
        },
        methods {
            PleaseOpenModal () {
                 $('#modal_signin').modal('show');
            }
        }
    }
    var temp =
        '\<div>'+
            '\<div id="clickme" @click="PleaseOpenModal">'+
                'click me!'+
            '\</div>'+
            '\<div id="modal_signin" class="ui basic modal">'+
                'Show modal'+
            '\</div>'+
        '\</div>'

    obj.template = temp;

    new Vue(obj);
```
應該就行摟！
