# PWN

# Table of Contents
* [Tools](#Tools)
* [Code Vulnerability](#Code-Vulnerability)
* [Security Options](#Security-Options)
  * [Lazy Binding](#Lazy-Binding)
  * [GOT & PLT](#GOT-&-PLT)
  * [RELRO](#RELRO)
  * [Stack Canary](#Stack-Canary)
  * [NX](#NX)
  * [PIE](#PIE)
  * [ASLR](#ASLR)
* [Shellcode](#Shellcode)

## Tools
* nc/ncat
  * 創立連線 : `ncat -vc $binary -kl $ip $port`
  * 連線 : `nc $ip $port`
* objdump
  * `objdump -M intel -d $binary | less`
* [gdb](https://github.com/whaleshark271/CTF-CheatSheet/tree/master/Reverse#GDB)
* checksec : 查看某個程式的安全性保護
  * `checksec -f $binary`
* gdb-vmmap : 查看目前程式的記憶體分佈，以及rwx權限設定
  * `vmmap`
* pwntools
  * ```python
    from pwn import *

    # connet to server
    r = process('./add')            # localhost binary
    r = remote('140.113.0.3', 8080) # remote binary

    s = r.recvuntil(':')  # receive from binary until ':'
    print '1: ' + s

    r.sendline('3 5')     # send to server

    r.interactive()       # switch to interactive mode
    ```
  * 建立通訊接口
    * 本地端 : r = process('./test')
    * 遠端 : r = remote('host', port)
  * r.interactive() : 通常在利用完成後去得到shell
  * 指定檔案的架構 : context.arch = 'i386' / 'amd64'
  * 傳送資料
    * 傳送字串 : send('deadbeef')
    * 在接收到':'後傳送字串 : sendafter(':', 'deadbeef')
    * 後面有換行 : sendline('deadbeef')
    * 在接收到':'後傳送 + '\n' : sendlineafter(':', 'deadbeef')
  * 接收資料
    * recv()
    * recvline(keepends=True) : 接收一行, drop '\n' based on keepends
    * recvuntil(':') : 接收直到':'
  * pack data (little endian)
    * p32(32 bit data) ex. `p32(0x12345678)` = \x78\x56\x34\x12
    * p64(64 bit data)
  * unpack
    * u32()
    * u64()
* readelf : 分析libc的工具
  * 先用 `ldd <binary>` 看libc在哪裡
  * `readelf -a $libc | less`
* ROPgadget
  * 列出binary中可以使用的ROP gadget
  * `ROPgadget --binar $binary`
* one_gadget : 在libc中尋找可以一步開啟shell的gadget
  * 限制
    * 需有libc base
    * 須滿足必要條件(constraints)
  * `one_gadget $libc`
* radare2 : 動態、靜態分析都可以用的工具
  * `r2 $binary`

## Code Vulnerability
* `gets()`
  * Vulnerable to buffer overflow
  * Doesn't check the length of input
* `strncmp()`
  * Check until null character ('\0') is reached
  * Use pwntools to send '\0' as one of the string and pass the comparison
* `abs()`
  * Vulnerable to integer overflow/underflow
  * INT_MIN(-2147483648) doesn't have 2's complement

## Security Options
### Lazy Binding
* Dynamic linking的程式在執行過程中，有些library的函式可能到結束都不會執行
* ELF採取Lazy binding的機制，在第一次call library函式時，才會去尋找函式真正的位置進行binding

### GOT & PLT
* [Shared Library 中 PLT 和 GOT 的使用機制](http://brandon-hy-lin.blogspot.com/2015/12/shared-library-plt-got.html)
* Global Offset Table
* GOT 為一個函式指標陣列，存了其他library中function的位置，因為lazy binding的機制，所以一開始只會填上一段plt位置的code
* 第一次執行時，plt會呼叫_dl_fixup()，才會去尋找真正的function並填入GOT
* 第二次以後執行時，直接透過GOT找到function位置

### RELRO
* [Hardening ELF binaries using Relocation Read-Only (RELRO)](https://www.redhat.com/en/blog/hardening-elf-binaries-using-relocation-read-only-relro)
* RELocation Read Only
* No/Partial/Full
  * No RELRO - link map 和 GOT 都可寫
  * Partial RELRO - link map 不可寫，GOT 可寫
  * Full RELRO - link map 和 GOT 都不可寫

### Stack Canary
* 在rbp之前塞一個random值，ret之前檢查是否相同，不同的話就會abort
* 有canary的話不能蓋到return address、rbp

### NX
* No eXecute
* 又稱 DEP (Data Execution Prevention)
* 可寫的不可執行，可執行的不可寫

### PIE
* Position Independent Executable
* 開啟時，data段以及code段位址隨機化
* 關閉時，data段以及code段位址固定

### ASLR
* Address Space Layout Randomization
* 記憶體位址隨機變化
* 每次執行時，stack、heap、library位置都不一樣
* **ASLR是系統設定，非程式的設定**
* 查看/設定ASLR
  * $cat /proc/sys/kernel/randomize_va_space
    * 0 : 關閉ASLR
    * 1 : 部分關閉ASLR
    * 2 : 完全開啟ASLR
  * $echo 0 > /proc/sys/kernel/randomize_va_space

## Shellcode
* C program to run shellcode
  ```c
  #include<stdio.h>
  #include<string.h>

  const char code[] = "/x48/x31/xF6..." // String Literal of shellcode

  int main(int argc, char **argv)
  {
    printf("Shellcode Length: %d\n", strlen(code));

    int (*func)();
    func = (int (*)()) code;
    (int)(*func)();
    return 0;
  }
  ```
  * compile : `gcc -z execstack -o shell shell.c`
* [x86 syscall table](https://syscalls.kernelgrok.com/)
  * run `int 0x80`
* [x64 syscall table](http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)
  * run `syscall`
* [Turn shellcode to string literal](https://defuse.ca/online-x86-assembler.htm#disassembly)
