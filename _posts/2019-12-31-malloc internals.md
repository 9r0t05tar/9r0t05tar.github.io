---
layout: post
title: "Malloc Internals"
tags: [protostar]
comments: false
---

First, the important definitions

```
Arena
A structure that is shared among one or more threads which contains references to one or more heaps, as well as linked lists of chunks within those heaps which are "free". Threads assigned to each arena will allocate memory from that arena's free lists.
Heap
A contiguous region of memory that is subdivided into chunks to be allocated. Each heap belongs to exactly one arena.
Chunk
A small range of memory that can be allocated (owned by the application), freed (owned by glibc), or combined with adjacent chunks into larger ranges. Note that a chunk is a wrapper around the block of memory that is given to the application. Each chunk exists in one heap and belongs to one arena.
Memory
A portion of the application's address space which is typically backed by RAM or swap.
Note that, in this document, we only refer to "memory" as a generic term. While there is some code in glibc's malloc to work with the Linux kernel (or other OS) to hint at what memory should be mapped/backed and what can be returned to the kernel, such discrimination between "real memory" and "virtual memory" is irrelevant to the discussion herein, unless explicitly called out.
```

# What is a Chunk

Glibc's Malloc divides a large region of memory which is called a heap into chunks. Each chunk includes an information on how big it is. 

chunk pointer(mchunkptr) does not point the beginning but the last word in the previous chunk.

