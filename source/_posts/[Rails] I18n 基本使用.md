---
title: '[Rails] I18n 基本使用'
date: 2018-05-14 13:30:00
categories:
 - [Rails, i18n]
tags:
 - Rails
 - i18n
---
首先安裝[rails-i18n](https://github.com/svenfuchs/rails-i18n)
``` ruby Gemfile
gem "rails-i18n"
```
`$ bundle install`

我們所有翻譯檔案都會在`config/locals`目錄下，都是為`.yml`的檔案，而檔名不重要，純粹是好分辨而已，這邊就創造兩個檔案：
`en.yml`代表英文翻譯
`zh-TW.yml`代表中文翻譯

自定義的話例如中文翻譯來說：
``` yaml
zh-TW:
  welcome: "嗨世界！#{username}"
  sport:
    ball: 籃球
```

而英文翻譯：
``` yaml
en:
  welcome: "Hello world! #{username}"
  sport:
    ball: basketball
```

使用方法如下：
``` html example.html.erb
t("sport.ball")
t(:sport, :scope => :ball )
t(:welcome, :username => "Kurt")
```

在application_controller.rb設定如下：
``` ruby application_controller.rb
before_action :set_locale

def set_locale
  # 可以將 ["en", "zh-TW"] 設定為 VALID_LANG 放到 config/environment.rb 中
  if params[:locale] && I18n.available_locales.include?( params[:locale].to_sym )
    session[:locale] = params[:locale]
  end

  I18n.locale = session[:locale] || I18n.default_locale
end
```

在view中切換版本：
``` html example.html.erb
<%= link_to "中文版", :controller => controller_name, :action => action_name, :locale => "zh-TW" %>
<%= link_to "English", :controller => controller_name, :action => action_name, :locale => "en" %>
```

### I18n 系列:
{% post_link '[Rails] I18n default scope' '[Rails] I18n default scope]' %}
