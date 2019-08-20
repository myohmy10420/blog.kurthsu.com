---
title: '[Sql]認識數據庫'
date: 2017-11-18 08:17:00
categories:
 - [Sql]
tags: Sql
---
## 一、什麼是數據庫？

所有的檔案都會放在硬體裡面，但數據庫提供了方便的查詢(Query)，和可以直接對他操作的CRUD(Create, Read, Update, Delete)，成了一門學問。

數據庫的類型有很多種，最普遍的是「關係型數據庫」 (RDBMS: Relational Database Management System)，又為 SQLite、MySQL、PostgreSQL是開源且免費的三種。

SQLite是Rails 開發時的默認數據庫。這種數據庫不是單獨的伺服器，一個數據庫即為一個檔案，但不適合多人同時連線使用。

MySQL是目前使用最廣泛，效能好，容易上手，線上資源最多的一種關係型數據庫，數據庫為一個伺服器，需要帳號密碼。
brew install mysql即可開始操作。

PostgreSQL經常與MySQL比較，是Heroku的默認數據庫。

Oracle 和 MS SQL Server是常用的付費資料庫，有更好的自己優化功能。



## 二、什麼是Schema？

Schema是存在數據庫的綱要，跟excel表單很像，每個項目對應一個欄位，可以有好幾筆資料在一個Schema裡面，比較不一樣的是需要定義這個欄位是字串、數值或者是布林值等等，也可以限定此欄位不得為空，如果一筆其一欄位不符合規定則不能寫入到Schema裡面。


## 三、一些SQL的標準語法
寫入資料庫：
`INSERT INTO events VALUES ("RubyConf", 100);`
拿取所有資料：
`Event.create( :name => "RubyConf", :capacity => 100)`
or
`Event.all`
或者可以直接在rails寫：
`SELECT * FROM events;`
執行時rails會幫我們編譯好剛剛上述的語法


## 四、什麼是ACID

ACID就是Atomicity, Consistency, Isolation, Durability是資料庫裡Transaction四個特性，那`什麼是Transaction`？

為了保證資料庫兩個表單互相更新資料的保證性資料庫有Transaction這個動作，最簡單的例子是A轉帳給B其實是更動了資料庫裡的兩筆資料，但如果讓程式一行一行跑則有可能發生A扣款了但B要增加戶頭金額的時候失敗，所以`最好的做法是這些轉帳的動作要嘛全部成功要嘛至少一起失敗`，才不會發生嚴重的商業錯誤。

Transaction裡面的動作是如何呢？
```
BEGIN;
  INSERT INTO histories (user_id, amount) VALUES (1, -100);
  INSERT INTO histories (user_id, amount) VALUES (2, 100);
  UPDATE accounts SET balance=200 WHERE id=1;
  UPDATE accounts SET balance=300 WHERE id=2;
COMMIT;
```
 P.S:每个 SQL 句必须用分号 ; 代表结束。

這樣BEGIN到COMMIT之間的所有程式碼就可以達到我們想要的要嘛全部成功要嘛至少一起失敗了！

如果是跨資料庫(model)：
ActiveRecord::Base.transaction do
  A.save
  B.save
  C.save
end

接下來四個Transaction的特性可以先參考網上[維基百科](https://en.wikipedia.org/wiki/ACID)的解釋：
* 原子性：一個事務（transaction）中的所有操作，要麼全部完成，要麼全部不完成，不會結束在中間某個環節。事務在執行過程中發生錯誤，會被回滾（Rollback）到事務開始前的狀態，就像這個事務從來沒有執行過一樣。
* 一致性：在事務開始之前和事務結束以後，資料庫的完整性沒有被破壞。這表示寫入的資料必須完全符合所有的預設規則，這包含資料的精確度、串聯性以及後續資料庫可以自發性地完成預定的工作。
* 隔離性：資料庫允許多個並發事務同時對其數據進行讀寫和修改的能力，隔離性可以防止多個事務並發執行時由於交叉執行而導致數據的不一致。事務隔離分為不同級別，包括讀未提交（Read uncommitted）、讀提交（read committed）、可重複讀（repeatable read）和串行化（Serializable）。
* 持久性：事務處理結束後，對數據的修改就是永久的，即便系統故障也不會丟失。



 ## 五、SQL 语言: DDL

這裡先說一下所有關係型資料庫都是用 SQL(Structured Query Language) 語言，每一行程式碼都可以稱為SQL Query 或 SQL Statement。

 DDL(Data Definition)是可以直接定義Schema的語法，請注意編碼(Encoding)，PostgrSQL 可用 utf-8、MySQL 可用 utf8mb4。

#### 打開或產生、砍掉檔案或刪除資料庫的話：
SQLite3：
`sqlite3 your_db_name.db`
MySQL：
`mysql -u root -p`
PostgreSQL：
`psql <database_name>`

再來我們只介紹在rails裡的操作語法
建立 Table：
`CREATE TABLE events (name VARCHAR(50) NOT NULL, capacity INTEGER, date DATE);`
會產生新的events表單，並新增三個字段name、capacity、date，默認是允許null值(可不填入)，如有設NOT NULL則必須有值。

#### 修改 Table：
改名：`ALTER TABLE persons RENAME TO people;`
新增字串：`ALTER TABLE people ADD COLUMN status VARCHAR(50);`
P.S：修改和移除字段SQLite3 沒有支援，要直接開心的Table把資料複製過去。

關於操作資料庫也可以使用擁有人性化介面的GUI套件。

## 六、SQL 语言: DML

操作數據的 SQL 就是 DML(Data Manipulation Language)，也就是做 CRUD 的操作。

#### 新增數據：
SQL：`INSERT INTO events (capacity, name) VALUES (200, "JSConf");`
rails：`Event.create( :capacity => 200, :name => "JSConf")`
也可以新增多筆：`INSERT INTO events (capacity, name) VALUES (300, "COSCUP"), (300, "OSDC.TW");`

#### 查詢資料：
SQL：`SELECT * FROM events;`
rails：`Event.all`
也可以撈出指定字串：
SQL：`SELECT name, capacity FROM events;`
rails：`Event.select(:name, :capacity).all`
等同於：`SELECT events.name, events.capacity FROM events;`
也可以使用遞增排序：
`SELECT name, capacity FROM events ORDER BY id;`
or
`SELECT name, capacity FROM events ORDER BY id ASC;`
遞減排序：`SELECT name, capacity FROM events ORDER BY id DESC;`
分頁：`SELECT name, capacity FROM events ORDER BY capacity `

#### 修改資料：
SQL：`UPDATE events SET capacity=10;`
rails：`Event.update_all( :capacity => 10 )`
可以用WHERE指定欄位裡指定的內容作修改：
SQL：`UPDATE events SET capacity=100 WHERE name="RubyConf"; `
rails：`Event.where( :name => "RubyConf" ).update_all( :capacity => 100)`
也可以指定一個特定內容修改多個欄位：
SQL：`UPDATE events SET capacity=100, name="RubyConf 2015" WHERE name="RubyConf";`
rails：`Event.where( :name => "RubyConf" ).update_all( :capacity => 100, :name => "RubyConf 2015" )`

#### 删除數據：
全部刪除：
SQL：`DELETE FROM events;`
rails：`Event.delete_all`
指定刪除：
SQL：`DELETE FROM events WHERE name="RubyConf";`
rails：`Event.where( :name => "RubyConf" ).delete_all`
