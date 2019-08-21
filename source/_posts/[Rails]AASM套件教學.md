---
title: '[Rails]AASM套件教學'
date: 2018-04-14 09:05:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - AASM
---
參考文件：

[官方文件](https://github.com/aasm/aasm)
[為你自己學](Ruby on Rails https://railsbook.tw/chapters/30-order.html )

## 教學開始
---
在自己的model引入AASM
``` javascript
class Job
  include AASM

  aasm do
    state :sleeping, :initial => true
    state :running, :cleaning

    event :run do
      transitions :from => :sleeping, :to => :running
    end

    event :clean do
      transitions :from => :running, :to => :cleaning
    end

    event :sleep do
      transitions :from => [:running, :cleaning], :to => :sleeping
    end
  end

end
```
`state`統整所有可能的狀態-initial為初始狀態
`event`各個事件method名稱
`transitions`從(from)什麼狀態改為(to)什麼狀態

此時我們在rails console也有了下面的methods可以呼叫
```
job = Job.new
job.sleeping? # => true
job.may_run?  # => true
job.run
job.running?  # => true
job.sleeping? # => false
job.may_run?  # => false
job.run       # => raises AASM::InvalidTransition
```

這時候在controller裡面可以呼叫
```
…
    @job.run!
...
```
執行結果:
`job.aasm_state從原本的"sleeping"更新為"running"`

注意:
1.transitions from可以是一個陣列，但如果呼叫了此event但值不包含在from裡面則會報錯！
ex:
在job.aasm_state還是sleeping的時候執行@job.clean!

2.aasm do預設執會去找aasm_state這個欄位，如要設定哪個欄位請使用aasm(xxx) do
ex:
想要他去偵測一個location欄位需要寫成aasm(:location) do

3.我們可以宣告多個欄位，但有重複的event後面的會覆蓋到前面的event，請善用namespace！
ex:
``` javascript
class NamespacedMultipleExample
  include AASM
  aasm(:status) do
    state :unapproved, :initial => true
    state :approved

    event :approve do
      transitions :from => :unapproved, :to => :approved
    end

    event :unapprove do
      transitions :from => :approved, :to => :unapproved
    end
  end

  aasm(:review_status, namespace: :review) do
    state :unapproved, :initial => true
    state :approved

    event :approve do
      transitions :from => :unapproved, :to => :approved
    end

    event :unapprove do
      transitions :from => :approved, :to => :unapproved
    end
  end
end
```
在rails console呼叫:
```
namespaced = NamespacedMultipleExample.new

namespaced.aasm(:status).current_state
# => :unapproved
namespaced.aasm(:review_status).current_state
# => :unapproved
namespaced.approve_review
namespaced.aasm(:review_status).current_state
# => :approved
```

以上我認為已經很夠用，如果想了解更多再去翻文件吧！
[官方文件](https://github.com/aasm/aasm)
