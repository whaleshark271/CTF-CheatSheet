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
