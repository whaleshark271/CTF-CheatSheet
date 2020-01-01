# Forensic

# Table of Contents
* [Tools](#Tools)
* [File Carving](#File-Carving)

## Tools
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