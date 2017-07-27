---
layout: post
title:  "volatile in C language"
author: paulguo
date:   2017-07-27
published: true
---

- **Brief introduction**

   [WIKI](https://en.wikipedia.org/wiki/Volatile_(computer_programming))

- **Usage**

   1. Access to memory mapped IO (device driver or embedded system).
   2. Shared variables are used in multiple-thread/process, signal handler also, longjmp also.


- **volatile + const**

  It is legal. (e.g. For read-only io register). 

- **Key point**

  prevent compiler from doing some "smart" optimization. e.g.

  Example 1:

```C
	while (*p == 0) {
		usleep(1);
	}
```

```C
	/* Avoid being optimized to: */
	if (*p == 0) {
		while (1) {
			usleep(1);
		}
	}
```
  Example 2:

```C
	char *addr; *addr = 1; *addr = 0; // "*addr = 1" could be meaningful sometimes (e.g. IO register).
```

```C
	/* Avoid being optimized to: */
	char *addr; *addr = 0;
```

- **ordering realted to volatile in gcc optimization.**

  There is an intersting page. [gcc_volatile](https://gcc.gnu.org/onlinedocs/gcc/Volatiles.html)

- **atomic**

   volatile is not directly relevant to atomic operations, but if we need atomic operations
   (e.g. for userspace spinlock between processes), we need to apply volatile to the variable.

- **volatile-considered-harmful**

  There is a artcile from the Linux kernel community:
  [volatile-considered-harmful](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/plain/Documentation/process/volatile-considered-harmful.rst)

  The key point is that many primitive (e.g. lock, barrier) has provided mechanisms to prevent compiler optimization, but volatile is still needed for IO (which we do not care). Actually for some simple use case, volatile may be still useful.  (e.g. do not want to introduce those primitives).

For other language, e.g. C++ things are different.
