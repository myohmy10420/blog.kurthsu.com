---
title: '[Disqus]簡易留言板'
date: 2018-04-21 13:48:00
categories:
 - [Disqus]
tags:
 - Disqus
---
首先我們先到[Disqus](https://disqus.com/)官方網站點選`GET STARTED` > `I want to install Disqus on my site`辦好一個網站在這個平台裡面，當詢問到What platform is your site on?時請選擇最下面的`Universal Code`，這時候就會看到他們提供了直接可以使用的程式碼。

P.S.這個平台容易讓人誤會的是他也是個社群平台，但我們只要他簡單的留言板功能而已

基本上只要貼上他的第一個提供的code到自己的view就行了！他連該利用這個網站的shortname去抓哪一隻js都幫忙列出了。

``` html show.html.erb
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://your-shortname.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>

```
