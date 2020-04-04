---
title: '[Ruby] include vs extend'
date: 2020-04-04 20:38:35
categories:
 - [Ruby]
tags:
 - Ruby
---

## 精簡版
include 給 instance object 用
extend 給 class object 用

範例:
``` ruby
module ForClass
  def speak
    puts 'Hello'
  end
end

module ForInstance
  def run
    puts 'Running'
  end
end

class Person
  extend ForInstance
  include ForInstance
end

Person.speak # Hello
Person.new.run # Running

Person.run # NoMethodError
Person.new.speak # NoMethodError
```
如果只想知道怎麼使用看到這邊就可以了, 如果想知道原理就繼續往下看

# 再來發生的情況其實如下
## methods 找尋順序

Ruby 世界如果當前 class 沒有該 method, 則會一層一層往上找, 直到最上層的 class 還找不到就會回傳 NoMethodError, 我們先 print 出所有父類(superclass)

``` ruby
puts Person.new.class # Person
puts Person.superclass # Object
puts Object.superclass # BasicObject
puts BasicObject.superclass # nil
```
由此可知最上層是父類是 BasicObject, 在 call superclass 會因為關聯不到父類而回傳 NilClass 了

所以可以說 methods 找尋順序的找尋順序如下:
Person > Object > BasicObject

但在剛剛的例子中 include 或 extend 了兩個 module 都會插入找尋順序, 且越晚插入的將會排序越前面, 所以剛剛的例子找尋順序如下:
Person > ForInstance > ForInstance > Object > BasicObject

## class object methods vs instance object methods

在 class object 中, 兩種 methods 繼承上是分離的
``` ruby
class A
  def self.speak
    puts 'class Hi'
  end

  def speak
    puts 'instance Hi'
  end
end

class B < A
end

B.speak # class Hi
B.new.speak # instance Hi
```

而在 module 則沒有分, 單純只對 method 名稱, 給予 self 反而找不到
``` ruby
module Action
  def self.speak
    puts 'class Hi'
  end

  def speak
    puts 'instance Hi'
  end
end

class Person
  extend Action
  include Action
end

Person.speak # instance Hi
Person.new.speak # instance Hi
```

其實只要把所有 self 都印出來就不難理解了
``` ruby
module Action
  puts "Self_1 is #{self}"
  def self.speak
    puts "Self_2 is #{self}"
    puts 'class Hi'
  end

  def speak
    puts "Self_3 is #{self}"
    puts 'instance Hi'
  end
end

class Person
  puts "Self_4 is #{self}"
  extend Action
end

Person.speak
# 執行結果如下:
# Self_1 is Action
# Self_4 is Person
# Self_3 is Person
# instance Hi
```
1. 可以看到程式碼由上往下跑的時候會先初始化 module Action 跟 class Person
> 此時先 print 出 Self_1 is Action 再來 Self_3 is Person
2. 再來執行 Person.speak, 因為 Person 沒有 speak 這個 method 所以照著 methods 找尋順序找到了 module Action 的 speak
> 此時 print 出 Self_3 is Person
3. 再來執行最後的 puts
> print 出 instance Hi

最後疑惑那我要怎麼呼叫 module Action 的 self.speak 呢?
其實很簡單就把 self 改為 Action 這個 object 就好
``` ruby
Action.speak
# Self_2 is Action
# class Hi
```

關於 self 以及 ruby 物件導向的觀念可以參考我另外寫的文章{% post_link '[Ruby] 物件導向' '[Ruby] 物件導向' %}
