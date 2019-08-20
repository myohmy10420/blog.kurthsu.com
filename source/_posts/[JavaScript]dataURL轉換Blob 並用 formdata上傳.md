---
title: '[JavaScript]dataURL轉換Blob 並用 formdata上傳'
date: 2017-09-18 10:11:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - dataURL
 - Blob
 - formdata
---
繼上一篇{% post_link '[Vue2]使用vue-avatar-editor套件裁減照片做出大頭貼(Resize image) '[Vue2]使用vue-avatar-editor套件裁減照片做出大頭貼(Resize image)' %}，這次我要做的是用form-data把我的出圖傳上去，但裁剪後得出圖格式是dataURL，而要上傳的格式要是Blob，該如何實現呢？

注意事項：
1.有import jQuery
2.有import axios

## dataURL to Blob
``` javascript
var imgURL = $('.someCanvas').toDataURL("image/png");
//直接複製國外的function
dataURItoBlob: function (dataURI) {
	// convert base64 to raw binary data held in a string
	// doesn't handle URLEncoded DataURIs - see SO answer #6850276 for code that does this
	var byteString = atob(dataURI.split(',')[1]);

	// separate out the mime component
	var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]

	// write the bytes of the string to an ArrayBuffer
	var ab = new ArrayBuffer(byteString.length);

	// create a view into the buffer
	var ia = new Uint8Array(ab);

	// set the bytes of the buffer to the correct values
	for (var i = 0; i < byteString.length; i++) {
		ia[i] = byteString.charCodeAt(i);
	}

	// write the ArrayBuffer to a blob, and you're done
	var blob = new Blob([ab], {type: mimeString});
	return blob;

}
var imgBlob = this.dataURItoBlob(imgURL);
//到這邊我們已經製造好blob，開始製造formdata
var postData =  new FormData();
postData.append('key', imgBlob, 'fileName.jpg' );
//最後一個參數是可以自行設定檔案名稱
axios.post('url', postData, {'content-type': 'multipart/form-data'})
	.then( function (response) {
    //handle succeed
	}).catch(function (error) {
	 //handle error
	})
```
這樣就能利用dataURL上傳Blob格式囉！

## 參考文件
[image 各種型態轉換(blob, dataURL, canvas) in JavaScript](https://chiayilai.com/image-%E5%90%84%E7%A8%AE%E5%9E%8B%E6%85%8B%E8%BD%89%E6%8F%9Bblob-dataurl-canvas-in-javascript/)

[stackoverflow-Blob from DataURL?](https://stackoverflow.com/questions/12168909/blob-from-dataurl)

[stackoverflow-How to give a Blob uploaded as FormData a file name?](https://stackoverflow.com/questions/6664967/how-to-give-a-blob-uploaded-as-formdata-a-file-name)
