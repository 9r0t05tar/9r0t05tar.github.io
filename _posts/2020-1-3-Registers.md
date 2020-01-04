---
layout: post
title: "Basics of pwn: Registers"
tags: [basics]
comments: false
---

There are many great papers explaining Registers and I wish when I was studying this, somebody told me that Registers are just like variables.

Registers can hold any values but some registers are reserved for what to do.

rip and rsp are reserved registers which hold the address of the next instruction and the address of the stack(top of the stack).

The same register can have different sized accesses. For example,

rax - full 64-bit

eax - low 32 bits of rax

ax - low 16 bits

al - low 8 bits

ah - high 8 bits

