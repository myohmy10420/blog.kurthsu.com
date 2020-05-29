---
title: '[Rails] I18n default scope'
date: 2020-05-26 21:59:15
categories:
 - [Rails, i18n]
tags:
 - Rails
 - i18n
---

在挖 deivse set_flash_message code 的時候研究了很久, 把一些心得記錄下來
這篇就不把 locale 檔案一一打出來了, 只記錄一下最終字串結果, 並且架設目前語系是 zh

## 基本使用和特性

```ruby
I18n.t(:hello)
I18n.t('hello')
I18n.t(:hello, :world)
I18n.t([:hello, :world])
I18n.t(['hello', 'world'])
# => zh.hello
I18n.t('hello.world')
# => zh.hello.world
```

## scope

scope 像是 namespace 的感覺, 要注意是加在最前面
```ruby
I18n.t(:cat, scope: :super)
I18n.t(:cat, scope: 'super')
# => zh.super.cat
I18n.t(:cat, scope: [:super, :power])
I18n.t(:cat, scope: [:super, 'power'])
# => zh.super.power.cat
```

## default

default 就是當找不到 i18n 翻譯時回傳的東西, 如果給予 array 則找到東西為止
```ruby
I18n.t(:man, default: 'I am Iron man')
# => 如果沒有找到 zh.man 的翻譯就回傳字串 'I am Iron man'
I18n.t(:man, default: [:super, :hero, 'I am Iron man'])
# => 依順序 zh.man > zh.super > zh.hero 的翻譯都沒有找到的話就回傳字串 'I am Iron man'
I18n.t(:man, default: [:super, :hero])
# => 同上依順序的翻譯都沒有找到的話就回傳字串 'translation missing: zh.man'
```

但要注意的是如果翻譯是有階層的話會回傳整個 hash, 例如:
```yaml
zh:
  man:
    super:
      hansome: '我是帥哥'
```

這時 I18n 的動作為:
```ruby
I18n.t(:man, default: [:super, :hansome])
# => {:hansome=>'我是帥哥'}
```
因為是 hash 不是 nil 就會整包回傳過來了

## scope 搭配 default

default 也會吃到 scope namespace
```ruby
I18n.t(:man, scope: [:super, :iron], default: [:woman, 'I am Iron man'])
# => 依順序 zh.super.iron.man > zh.super.iron.woman 的翻譯都沒有找到的話就回傳字串 'I am Iron man'
```

## 小結

我還是覺得非必要不要寫太多的 scope 和 default 其實挺通靈的, 如果沒有把 devise 的 find_message binding 看裡面發生了什麼事情其實挺難懂的, 還是用最基本的寫法比較好收尋和維護

## I18n 系列:
{% post_link '[Rails] I18n 基本使用' '[Rails] I18n 基本使用]' %}
{% post_link '[Rails] I18n 語系設定' '[Rails] I18n 語系設定]' %}
