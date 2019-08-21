---
title: '[Rails]will_paginate 做分頁'
date: 2018-05-10 08:32:00
categories:
 - [Rails, Gem]
 - [will_paginate]
tags:
 - Rails
 - Gem
 - will_paginate
---
參考官方文件：
[will_paginate](https://github.com/mislav/will_paginate)

安裝：
``` ruby Gemfile
gem 'will_paginate', '~> 3.1.0'
```
`$ bundle install`
`$ rails s`

在controller裡面加入：
``` ruby post_controller.rb
Post.paginate(:page => params[:page], :per_page => 30)
```
`per_page`是一頁顯示幾筆

當然也可以搭配任何條件：
``` ruby post_controller.rb
Post.order("created_at DESC").paginate(:page => params[:page], :per_page => 30)
```
這樣就可以把最新的Post顯示在最前面

最後在view：
``` html post.html.erb
<%= will_paginate @posts %>
```

大功告成又很簡單～至於css的話也可以參考官方的：
[make those pagination links prettier](http://mislav.github.io/will_paginate/)
