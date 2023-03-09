Kernel Rop is CTF challenge available at [hexCtf2020](https://2020.ctf.link/). The challenge provides you the vulnerable kernel module you will be exploiting this module to perform privilage escallation. If you are new to kernel exploitation better start with these write-ups

1. [Low Level Adventure](https://0x434b.dev/dabbling-with-linux-kernel-exploitation-ctf-challenges-to-learn-the-ropes/) 
2. [Ikmidas](https://lkmidas.github.io/posts/20210123-linux-kernel-pwn-part-1/#the-simplest-exploit---ret2usr)


#### swapgs_restore_regs_and_return_to_usermode issue

While practicing the second part we were jumping to swapgs_restore_regs_and_return_to_usermode + 22 and there were so many push and pops i was confused how ROP chain made by lkmidas was working

```asm
.text:FFFFFFFF81200F26                 mov     rdi, rsp
.text:FFFFFFFF81200F29                 mov     rsp, qword ptr gs:unk_6004
.text:FFFFFFFF81200F32                 push    qword ptr [rdi+30h]
.text:FFFFFFFF81200F35                 push    qword ptr [rdi+28h]
.text:FFFFFFFF81200F38                 push    qword ptr [rdi+20h]
.text:FFFFFFFF81200F3B                 push    qword ptr [rdi+18h]
.text:FFFFFFFF81200F3E                 push    qword ptr [rdi+10h]
.text:FFFFFFFF81200F41                 push    qword ptr [rdi]
.text:FFFFFFFF81200F43                 push    rax
.text:FFFFFFFF81200F44                 jmp     short loc_FFFFFFFF81200F89
```

look closely we are saving the current `rsp` into `rdi` and then moving some value into `rsp` i don't know yet what it is then using `rdi` we are building the frame on stack again requried by `iretq` 


**How is the signal_handler trick is working ?** 

The answer to this question lies in man page of signal do `man 7 signal` and read the section called `Execution of signal handlers`

**What the hell is swapgs_restore_regs_and_return_to_usermode exactly ?**
