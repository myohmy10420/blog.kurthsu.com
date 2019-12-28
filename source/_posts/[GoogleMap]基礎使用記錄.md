---
title: '[GoogleMap]基礎使用記錄'
date: 2017-06-04 12:52:00
categories:
 - [GoogleMap]
tags:
 - GoogleMap
comments: true
---
最近要研究google map真的是挫折百般，網上一堆說很簡單操作的可是我用起來就是一直覺得怪怪的ＸＤ，也許我沒有寫程式的天賦，但至少要知道怎麼努力。

其實簡單來說Google map就做兩件事情：
`你給他地址會得到經緯度並定位，你給他經緯度可以得到地址並定位！`

這就是最基本的使用方式，在HTML的程式碼裡面只需要一行：
``` html
<div id="map_canvas" style="height: 300px; width: 100%;"></div>
```
當然大小樣式是可以自己決定的，那就來做使用吧！

# 給予經緯度定位
如果你真的有經緯度的話是最輕鬆定位的哦！而且只要寫：
``` javascript
var map = new google.maps.Map(document.getElementById('map_canvas'), {
    center: { lat: 25.037522, lng: 121.563501 },
    zoom: 17
});
```
其實HTML的div tag裡面就會出現最基本的畫面了，這個座標會出現市政府附近的地圖。
`center是輸入座標的地方，需要是一個物件。`
`zoom是地圖的縮放大小數字越小則可以看到的地方更多，17是最基本的。`

以使用者角度來看沒有標記點只給我一張地圖感覺很吝嗇糟糕，所以我們在來改變一下：

``` javascript
//這邊我把座標拉出來處理，map 跟 maker是共用的也會更好讀懂
var position = '25.037522,121.563501';
var latlng = {
    lat: parseFloat(position.split(',')[0]),
    lng: parseFloat(position.split(',')[1])
}
var map = new google.maps.Map(document.getElementById('map_canvas'), {
    center: latlng,
    zoom: 17
});
//我們的標記點登場～
var marker = new google.maps.Marker({
    map: map,
    position: latlng,
    icon: 'https://maps.google.com/mapfiles/ms/icons/blue-dot.png'
});
```

這樣有沒有很像Google map了呢？但對使用者來說我們不可能會知道座標在哪，就像坐計程車的時候我們不會跟司機說『我要去25.037522,121.563501』他應該只會載你去醫院，必須要說我要去臺北市政府，就像我們要輸入到google map收尋一樣！

# 給予地址定位
所以我們先來做個簡易的收尋欄位吧！
``` html
<input class="userinput" type="text">
<div style="width: 60px; height: 20px; border: 1px solid" onclick="search();">Search</div>
<div id="googlemap"></div>
```
再來新增一個getLatlngByAddress用中文地址得到座標的函示：
``` javascript
function search () {
  var address = $('.userinput').val();//得到剛剛輸入的地址字串
  var position = "'',''";
  var latlng = {
      lat: parseFloat(position.split(',')[0]),
      lng: parseFloat(position.split(',')[1])
  }
  var map = new google.maps.Map(document.getElementById('googlemap'), {
      center: latlng,
      zoom: 17
  });
  var marker = new google.maps.Marker({
      map: map,
      position: latlng,
      icon: 'https://maps.google.com/mapfiles/ms/icons/blue-dot.png'
  });

  getLatlngByAddress(map, marker, address);

  function getLatlngByAddress(map, marker, address) {//重新定位地圖位置與標記點位置
    var geocoder = new google.maps.Geocoder();
    geocoder.geocode({
      'address': address
    }, function (results, status) {
      if ( status === google.maps.GeocoderStatus.OK ) {
        var getLatLng = results[0].geometry.location;

        map.setCenter(getLatLng);  //將地圖中心定位到查詢結果
        marker.setPosition(getLatLng); //將標記點定位到查詢結果
        marker.setTitle(address); //重新設定標記點的title
      } else {
        alert('找不到哦！');
        $("#geo-UpdateDemand").text("");
      }
    });
  }
}
```

Google map的geocoder也可以用經緯度定位取得新地址和地址名稱，這樣可以用來做移動座標的動作，這邊簡單記錄一下基礎的東西，很多資源網路上查也會有，再來Google map存在著一些地雷，像是畫面顯示很容易遇到破圖，這時候可以試著用setTimeout或者Google map不知道哪一版有提供resize的事件去做刷新Google map的div tag，希望下次不要再卡關這麼久囉XD！
