---
layout: post
title: "Basics of pwn: Calling Convention"
tags: [basics]
comments: false
---

I already have made a post about Calling Convention but this is for the basics posts.


## 32 bit

As it was mentioned in the previosu post, Stack, 32-bit binaries on Linux passess arguments by pushing and if there's more than one argument, it pushes it backwards following to the LIFO(Last in First out) method. 

So for example, if there was a function like this.

```
int sum(int a, int b, int c){
    return a+b+c;
}
```

in the assembly, it will look like.

```
push c
push b
push a
```

## 64 bit

In 64 bit binaries, function arguments are passed in certain registers 

<pre>
    1. RDI
    2. RSI
    3. RDX
    4. RCX
    5. R8
    6. R9
</pre>

This information is really useful in binary exploitation because of how simple it is that you just need to find a gadget that pops the register and if you put a value in that value will go into the register.s




###### The posts of 'basics of pwn' is a summary of mine of the posts on https://ctf101.org/binary-exploitation/