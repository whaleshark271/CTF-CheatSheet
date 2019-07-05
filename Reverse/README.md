# Reverse

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

## Assembly
* [x86 and amd64 instructions](https://www.felixcloutier.com/x86/)
  * [syscall](http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/)
* [Calling Convention](https://medium.com/@ktecv2000/%E7%B7%A9%E8%A1%9D%E5%8D%80%E6%BA%A2%E4%BD%8D%E6%94%BB%E6%93%8A%E4%B9%8B%E4%B8%80-buffer-overflow-83516aa80240)
* [Function Prologue](https://en.wikipedia.org/wiki/Function_prologue)
  * ```assembly
    push ebp
    mov ebp, esp
    sub esp, N
    ```
  * Stack frame :
    ```
    | return address | <= high address
    | saved ebp      |
    ...
    <local variables>
    ...
    |                | <= esp, low address
    ```