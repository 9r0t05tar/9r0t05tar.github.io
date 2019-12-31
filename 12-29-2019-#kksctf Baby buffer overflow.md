---
layout: post
title: "#kksctf Baby buffer overflow writeup"
tags: [protostar]
comments: false
---


challenge
```
We received new message from kackers. They laugh at us being sure that no one will ever be able to break them and even left a description of what needs to be done. Show them what happens to overly confident people!

nc tasks.open.kksctf.ru 10002

https://drive.google.com/open?id=1xSswzDDa0lhtGZ2zfhkRr_kNHD0pcdIy
```

We are given a binary for this challenge.

file
```
root@kali:~/Downloads/baby_bof_# file baby_bof
baby_bof: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=679ffb807feb7aef6982de068fe64bb6deb7fb0c, not stripped
```

Seems like a simple x86 buffer overflow challenge.

Looking at the functions, we can see that there's a win() function

```
void win(int param_1)

{
  char local_29 [29];
  FILE *local_c;
  
  local_c = fopen("flag.txt","r");
  if (local_c == (FILE *)0x0) {
    puts("flag not found");
  }
  else {
    fgets(local_29,0x1d,local_c);
    if (param_1 != -0x35014542) {
      puts("Almost there :)");
                    /* WARNING: Subroutine does not return */
      exit(0);
    }
    printf("Here it comes: %s\n",local_29);
  }
  return;
}
```

Which just prints out the flag.txt if it fits into the if statement. The if statement just wants us to modify the value to 0xcafebabe

And in the main function it uses a function read_wrapper() which bof happens 
```
undefined4 main(void)

{
  undefined local_104 [252];
  
  setbuf(stdin,(char *)0x0);
  setbuf(stdout,(char *)0x0);
  setbuf(stderr,(char *)0x0);
  puts("We have prepared a buffer overflow for you");
  puts("Can you get use of it?");
  printf("Enter your name: ");
  read_wrapper(local_104);
  printf("Hello, %s!\n",local_104);
  return 0;
}
```

So first we need to get the offset for local_104[].

```

```