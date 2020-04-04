---
title: '[Ruby] 物件導向'
date: 2020-04-03 23:39:38
categories:
 - [Ruby]
tags:
 - Ruby
---

這幾天發現我對於原生的 Ruby 並不是這麼的熟悉, 物件導向的概念也不是到真正理解, 會用不代表真正理解原理, 看了泰安大大推薦的[The Ruby Object Model by Dave Thomas](https://www.youtube.com/watch?v=X2sgQ38UDVY&t=3s)才真正更進一步的理解 Ruby 物件導向的宇宙觀

## 什麼是物件導向

關鍵字是 Object-oriented programming(簡稱 OOP), 網路上有許多說法, 但不如先說為什麼 coding 需要物件導向呢?

先敘述在沒有物件導向時遇到的問題, 假設有一個庫存系統可以純粹的寫入和讀取資料, 我們必須對每一種物品都各自定義商業邏輯再寫入和讀取, 如果有100種物品再乘上10種商業邏輯我們可能就要細心的寫出1000種程式碼了, 所以有幾個問題:
1. 程式碼重複性很高
2. 程式碼將以倍數成長
3. 程式碼的擴充十分沒有彈性
4. 程式碼將難以維護

人是會出錯的, 如果我們能讓每一種物品更智能, 可以有方法可以計算自己呢? 所以有了物件導向的概念出現了!

這個概念就是每一個 object(物品) 都有自己的 state(狀態), 以及自己的 behaviours(行為)
在 Ruby 的世界就是每一個 object(物品) 都有自己的 instance variables(實例變數) 和 methods(方法)

可以看作是每一筆資料(object)都是完全獨立的, 每個資料各自運作, 每個資料各自更新自己的 state

再來物件導向的世界有 class(類別) 的概念, 他可以製做出各式各樣的 object, 且 class 有繼承的效果, 簡單的 Ruby 程式碼範例如下:
``` ruby
class Animal
  def eat
    puts 'I need foods.'
  end
end

class Dog < Animal
  def speak
    puts 'Woof'
  end
end

class Cat < Animal
  def speak
    puts 'Meow'
  end
end

dog = Dog.new
dog.eat # I need foods.
dog.speak # Woof
cat = Cat.new
cat.eat # I need foods.
cat.speak # Meow
```
我們解決了剛剛那四個問題, 物件導向的概念改變了世界!
接下來繼續探討 Ruby 的世界

## self

self 在 Ruby 是極其重要的概念, 跟 JavaScript 的 this 概念很像,他代表了三件事情:
1. 當前是哪個 object
2. object 裡找到了哪些 instance variables
3. 定義了 receiver 是 object 讓 methods 去執行它

而可以改變 self 的只有兩件事情:
1. 呼叫 methods
2. 定義 class/module

可以寫一個簡單的 ruby 檔案並執行它試試看
``` ruby ruby.rb
puts self # main
class Duck
  puts self # Duck
  def speak
    puts self # <Duck:0x00007fe2229284a0>
    'QUACK'
  end
end

duck = Duck.new
duck.speak.upcase # QUACK
```
`$ ruby ruby.rb`
當呼叫了 duck.speak.upcase 時 self = duck.speak = 'QUACK'
而 'QUACK' 這個 receiver 可以 call upcase

再來一個範例更清楚:
``` ruby
class Duck
  puts self # Duck
  @name = 'Duck'
  def self.name
    puts self # Duck
    puts @name
  end

  def name
    puts self # <Duck:0x00007ff960981a00>
    puts @name
  end
end
Duck.name # 'Duck'
Duck.new.name # nil
```
永遠要確定當前(self)呼叫的 instance variables 是哪一個 object

## 繼承鍊

物件導向可以繼承所有狀態和行為:
``` ruby
class Duck
  def name=(name)
    @name = name
  end

  def name
    puts @name
  end

  def speak
    puts 'Quack'
  end
end
duck1 = Duck.new
duck1.name = 'Duck'
duck1.name # Duck
duck1.speak # Quack
duck2 = duck1.clone
duck2.name # Duck
duck2.speak # Quack
puts duck2.object_id # 70335517768660
```
咦! 為什麼有 object_id 這個方法可以用呢? 其實他是繼承於 BasicObject 這個 class 而來的

``` ruby
puts duck2.class # Duck
puts duck2.class.superclass # Object
puts duck2.class.superclass.superclass # BasicObject
puts duck2.class.superclass.superclass.superclass # nil
```

繼承的特性是當前的 class 找不到 method 的話就會往 superclass(父類別) 去找, 而到最上層的父層(BasicObject)都找不到的話就會回傳 undefined method

P.S. 當呼叫 `BasicObject.superclass` 會回傳 nil 是因為再上去關聯不到東西了所以才回傳 NilClass 的 object

可以測試 object_id 這個 method 是否真的來自於 BasicObject
``` ruby
BasicObject.instance_methods.respond_to?(:object_id) # true
```

所謂的 instance_methods 是由這個 class 實例出來的 object(instance) 可以使用的方法(methods), 所以也可以看到
``` ruby
Object.instance_methods.respond_to?(:object_id) #true
```

## singleton class

singleton class 也可以稱呼--匿名class, 他是一個蠻抽象的概念, 甚至 call superclass 也找不到他, 延續 Duck 例子:

``` ruby
duck1 = Duck.new
duck2 = Duck.new
def duck1.run
  puts 'I am running'
end
duck1.run # I am running
duck2.run # NoMethodError
```

剛剛提到的繼承要找尋 run 這個 method 的順序應該是
Duck > Object > BasicObject
那 run 呢??

找尋 method 的順序沒有特例也沒有例外, 只是 ruby 會新增一個 singleton class 來放這個 method, 所以其實順序如下:
Duck > `Singleton` > Object > BasicObject

這個 singleton 屬於每一個 object, 他們是完全獨立的一層 class, 但模式沒有特例也沒有例外, singleton 也跟任何 class 一樣是可以繼承的
``` ruby
duck1 = Duck.new
def duck1.run
  puts 'I am running'
end
duck2 = duck1.clone
duck1.run # I am running
duck2.run # I am running
```

## everything is object

第一個主題提到 `物件導向的世界有 class(類別) 的概念, 他可以製做出各式各樣的 object`, 白話的來說, object 就是 class 的產物, 包括所有 class

聽起來很玄對吧! 可以來玩玩看, 物件導向提到最初的概念是, 每個 object 都有自己的 instance variables(實例變數) 和 methods(方法), 我們來對 Object 加點料

``` ruby
class Object
  @name = 'HeyHeyHey'

  def self.name
    @name
  end
end

puts Object.name # HeyHeyHey
```
所以其實沒有 class method 這種東西, def self.name 會讓 Object 這個 object 新增了一層自己的 singleton, 不過現在這種稱呼方法已經很普遍了, 在溝通上知道在講什麼就好

最後還有一個比較語意話的證明方法:
``` ruby
String.superclass # Object
Hash.superclass # Object
Array.superclass # Object
Time.superclass # Object
Numeric.superclass # Object
class Dog; end
Dog.superclass # Object

Object.superclass # BasicObject
```
所以萬物的最上層 class 都是 BasicObject, 所以所有東西都可以呼叫 object_id

## include vs extend

再來回顧找尋 methods 的順序
``` ruby
class A
  def self.speak
    puts 'A hello'
  end

  def self.run
    puts 'A run'
  end

  def sleep
    puts 'A sleep'
  end
end

class B < A
  def self.run
    puts 'B run'
  end
end
B.run # B run
B.speak # A Hello
B.new.sleep # A sleep
B.new.class.superclass.speak # A Hello
```
其實 B < A 這個動作即是在 B 的找尋順序上加上了 A 的順序, 現在 B 的找尋順序如下:
B > B singleton > A > A singleton > Object > BasicObject
不管是 instance methods 還是俗稱的 class methods都是一樣, 只是 instance methods 只會去找 instance methods, 反之 class 只會去找 class 的

再來看 include vs extend, 其實就也只是在找尋順序上多插了一條路線而已, 只不過 include 是安插在 instance methods 上, extend 是安插在 class methods 上, 如果程式碼改成這樣:
``` ruby
class A; end

module C
  def speak
    puts 'C Hello'
  end
end

class B < A
  include C
end
B.new.speak # C Hello
```
這時候找尋順序為
B > B singleton > C > A > A singleton > Object > BasicObject

## open object

我們可以重複宣告相同的 class name, 整個 class 不會被覆蓋掉而是再疊上去, 可以把它當作是一個打開同一個 object 在賦予新的東西的概念
``` ruby
class A
  def self.speak
    puts 'Hello'
  end
end

class A
  def self.run
    puts 'Running'
  end
end

A.speak # Hello
A.run # Running
```

所以只要名稱正確, 基本上可以在任何地方打開正確的 object 再給予新東西
``` ruby
class A; end

class B
  class ::A
    def self.speak
      puts 'Hello'
    end
  end

  class A
    def self.run
      puts 'Running'
    end
  end
end

A.speak # Hello
Object::A.speak # Hello
puts A == Object::A # true
B::A.run # Running
puts A == B::A # flase
```
雙冒號(::)會從最外層開始找尋, 其實最外層的 namesapce 就是 Object
如果沒有雙冒號的話在哪個 Object 底下打開的 Object 的 class name 則會被灌上 namespace

## 參考資源
[The Ruby Object Model by Dave Thomas](https://www.youtube.com/watch?v=X2sgQ38UDVY&t=3s)
[Ruby 使用手冊](https://guides.ruby.tw/ruby/index.html)
[Ruby 的繼承鍊 (1) - 如何實踐物件導向](https://www.spreered.com/ruby-object-model-1/)
