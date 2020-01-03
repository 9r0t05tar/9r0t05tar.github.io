---
layout: post
title: "Linux x86-64 calling convention"
tags: [resources]
comments: false
---

Calling convention is how the function receives the parameters and how they return a result. This is important when solving pwnable CTF challenges because there's a lot of situations where you have to use these features.

#Registers

%rax -> Returns a value from a function
%rbx -> Optionally used by base pointer

%rdi -> Used to pass the 1st argument to a function
%rsi -> Used to pass the 2nd argument to a function
%rdx -> Used to pass the 3rd argument to a function
%rcx -> Used to pass 4th argumnet to a function

These are the important registers that you need to know when looking at calling convention

