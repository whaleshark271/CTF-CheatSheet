# Forensics

# Table of Contents
* [Tools](#Tools)
* [File Carving](#File-Carving)
* [LSB Steganography](#LSB-Steganography)
* [Event Log](#Event-Log)

## Tools
* [Security Onion](https://docs.securityonion.net/en/2.3/about.html) : A Linux distribution for threat hunting, enterprise monitoring, and log management.
  * Includes security tools such as TheHive, CyberChef, Elasticsearch, Kibana, Zeek, Wazuh, Wireshark, Networkminer, etc.
* [VirusTotal](https://www.virustotal.com/gui/home/upload) : Analyze suspicious files and URLs to detect types of malware
* [Binwalk](https://github.com/ReFirmLabs/binwalk) : Search for embedded files or data.
  * `binwalk file` : Displays the embedded data in given file.
  * `binwalk -e file` : Displays and extract the embedded data.
* [foremost](https://tools.kali.org/forensics/foremost) : A forensic program to recover lost files based on their headers, footers, and internal data structures.
* [Kali Linux: John the Ripper(JtR)](https://tools.kali.org/password-attacks/john) : A password cracking tool.
  * For encrypted .rar file
    * `rar2john secret.rar > hash.txt` : Writes hashed password to hash.txt
    * `john hash.txt` : Cracks the password
* [Stegsolve](https://github.com/eugenekolo/sec-tools/tree/master/stego/stegsolve/stegsolve) : Apply color filters on images and find hidden texts/messages.
* [Zsteg](https://github.com/zed-0xff/zsteg) : Detect hidden data in png and bmp files.
  * Install : `gem install zsteg`
  * `zsteg -a file` : Runs all the methods on given file.
  * `zsteg -E file` : Extracts data from given payload. ex. `zsteg -E b4,bgr,mb,yX file`

## File Carving
* File carving is the process of reconstructing files by scanning the raw bytes of the disk and reassembling them. This is usually done by examining the header (the first few bytes) and footer (the last few bytes) of a file.
* [Info](https://resources.infosecinstitute.com/file-carving/#gref)

## LSB Steganography
* [Introduction](https://itnext.io/steganography-101-lsb-introduction-with-python-4c4803e08041)
* Script from [CSY54 of WXN CTF](https://hackmd.io/@CSY54/WXN_writeup#Forensics-LSB-268-pts-19-solves)
* What if we don't know the order of the colors?
  * Use [zsteg](https://github.com/zed-0xff/zsteg)

## Event Log
* [Windows Event Log](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-vista/cc722385(v=ws.10)?redirectedfrom=MSDN)
  * Event Viewer
* [rsyslog](https://en.wikipedia.org/wiki/Rsyslog)
