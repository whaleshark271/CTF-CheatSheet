# PWN

# Table of Contents
* [Tools](#Tools)
* [Security Options](#Security-Options)
  * [Lazy Binding](#Lazy-Binding)
  * [GOT & PLT](#GOT-&-PLT)
  * [RELRO](#RELRO)
  * [Stack Canary](#Stack-Canary)
  * [NX](#NX)
  * [PIE](#PIE)
  * [ASLR](#ASLR)

## Tools
* nc/ncat
  * 創立連線 : `ncat -vc $binary -kl $ip $port`
  * 連線 : `nc $ip $port`
* objdump
  * `objdump -M intel -d $binary | less`
* gdb
* checksec : 查看某個程式的安全性保護
  * `checksec $binary`
* gdb-vmmap : 查看目前程式的記憶體分佈，以及rwx權限設定
  * `vmmap`

## Security Options
### Lazy Binding
* Dynamic linking的程式在執行過程中，有些library的函式可能到結束都不會執行
* ELF採取Lazy binding的機制，在第一次call library函式時，才會去尋找函式真正的位置進行binding

### GOT & PLT
* Global Offset Table
* GOT 為一個函式指標陣列，存了其他library中function的位置，因為lazy binding的機制，所以一開始只會填上一段plt位置的code
* 第一次執行時，plt會呼叫_dl_fixup()，才會去尋找真正的function並填入GOT
* 第二次以後執行時，直接透過GOT找到function位置

### RELRO
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