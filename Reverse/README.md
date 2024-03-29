# Reverse

# Table of Contents
* [Resources](#Resources)
* [Commands](#Commands)
* [GDB](#GDB)
* [IDA PRO](#IDA-PRO)
* [Character Encoding](#Character-Encoding)
* [Assembly](#Assembly)
* [Decompile pyc](#Decompile-pyc)

## Resources
* [Reverse Engineering for Beginners by Ophir Harpaz](https://www.begin.re/)
* [GodBolt CE](https://godbolt.org/): An interactive online compiler which shows the assembly output of compiled code
* [Cutter](https://cutter.re/): Free and Open Source RE Platform powered by radare2
* [Eicar Anti Malware Test File](https://www.eicar.org/download-anti-malware-testfile/)

## Commands
* `file <something>` : 查看檔案類型
* `strings <something>` : 印出檔案中的可視字串
* `strings <something> | grep "..."`
* `strace ./<binary>` : 查看binary執行時的system call和signal
* `ltrace ./<binary>` : 查看binary執行時的library call
* `objdump -M intel -d ./<binary>`: 以intel格式顯示binary反組譯的結果(組合語言)
* `objdump -M intel -d <binary> | less` : 把輸出結果導向到less方便查詢閱讀，打"/"就可以把左下角的":"改成"/"，輸入字串搜尋
* `hexeditor ./<binary>` : Change the hex of a binary file
  * `^w` : search
  * `^x` : save and exit
  * nop : \x90

## GDB
* 使用方法 : `$gdb <binary>` or `$gdb` + `attach pid`
* 設定斷點 : `break <address>` or `b <address>`
  * `break *0x0000000004004d7`
* 執行程式 : `run` or `r`
* 執行下一個指令(會追進function) : `step`
* 執行下一個指令(不會追進function) : `next` or `n`
* 繼續執行 : `continue`
* 直行至function結束 : `finish`
* 跳轉 : `jump <address>`
  * `jump *0x0000000004004d7`
* 印出暫存器的值 : `print <register>`
  * `print $rax`
* 印出記憶體的值 : `x/wx <memory address>`
  * b/h/w/g 分別代表1, 2, 4, 8 bytes
  * /後可以設定要印出幾個位置 ex. `x/40gx`
  * x可以換成u/d/s/i
    * u = unsigned int
    * d = decimal
    * s = string
    * i = 指令
  * `x/6s 0x7fffffffe920`
* 改變暫存器的值 : `set <register>=<value>`
  * `set $rsp=0x7fffffffe800`
* 改變記憶體的值 : `set {<size>}<memory address>=<value>`
  * `set {int}0x7fffffffea00=2`
* 查看所有斷點 : `info breakpoint`
* 查看暫存器狀態 : `info registers`
* 顯示frame的相關資訊 : `info frame`
* 產生該長度的字串 : `pattc <number>`
* 查看該數值在產生字串的偏移量 : `pattern offset <數值>`

## IDA PRO
* 把 binary 反編譯回 C code
  1. 在 functions window 點選想看的 function
  2. 按下F5
* 列出可視字串表
  * View => Open subviews => Strings
  * shift + F12
* 標記變數名
  1. 先點擊要命名的變數
  2. 按下 n
  3. 輸入新的變數名
* 標記 function 參數
  1. 先點擊該 function
  2. 按下 y
  3. 輸入正確的 function 參數
* 標記 struct 結構
  1. 切到 Structures 頁面
  2. 看裡面的說明
  3. 新增 struct 並標記裡面的內容
* Graph View
  * Blue arrow - unconditional jump
  * Green arrow - conditional jump where the condition **holds**
  * Red arrow - conditional jump where the condition **does not hold**

## Character Encoding
> [source](http://sean1223.blogspot.com/2013/06/asciiansiunicodeutf-8.html)
* SBCS (Single Byte Character Set)
  * ASCII - 用1個byte來表示，共有128個字元
  * ANSI - 也是用1個byte來表示，比起ASCII少了一些控制字元，但多了一些重音字元
* Wide Characters
  * Unicode - 用2個bytes來表示，比ASCII多用了一倍的記憶體
  * UTF-8 - 針對Unicdoe的可變長度字元編碼，用1~3個bytes來表示一個字元，減少記憶體空間的使用量

## Assembly
* [x86 Assembly Guide](http://www.cs.virginia.edu/~evans/cs216/guides/x86.html)
* [x86 Registers](https://wiki.skullsecurity.org/index.php?title=Registers)
* [x86 and amd64 instructions](https://www.felixcloutier.com/x86/)
  * [syscall](http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)
* [Windows System Calls Table](https://github.com/j00ru/windows-syscalls)
* [Calling Convention](https://medium.com/@ktecv2000/%E7%B7%A9%E8%A1%9D%E5%8D%80%E6%BA%A2%E4%BD%8D%E6%94%BB%E6%93%8A%E4%B9%8B%E4%B8%80-buffer-overflow-83516aa80240)
* Function Prologue and Epilogue
  * ```assembly
    push ebp
    mov ebp, esp
    sub esp, N
    ```
  * ```assembly
    mov esp, ebp
    pop ebp
    ret # pop eip
    ```
* Stack frame : `Add(x,y)` as example
  * Function parameters are pushed in reverse order. y is pushed first, then x.
  ```
  |      ...       | <= high address
  |       y        | # function parameter, ebp + 0xC
  |       x        | # function parameter, ebp + 0x8
  | return address | 
  |   saved ebp    | <= ebp, fixed, used as an anchor
  ...
  <local variables>
  ...
  |                | <= esp, low address
  ```
* Main Memory
  ```
  |                | <= low address
  |----------------|
  |     Stack      | <= local variables and arguments
  |----------------|
  |     Heap       | <= dynamic memory (ex. malloc)
  |----------------|
  |     Code       | 
  |----------------|
  |     Data       | <= static/global variables
  |----------------| 
  |                | <= high address
  ```
## Decompile pyc
* `pip install uncompyle6`
* `uncompyle6 -o . <filename>.pyc`
