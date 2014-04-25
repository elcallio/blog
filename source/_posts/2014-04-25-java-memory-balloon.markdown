---
layout: post
title: "Comparing memory ballooning and swapping"
date: FIXME FINAL DATE HERE
comments: true
published: false
---

FIXME MEMORY BALLOON INTRO HERE

Just as the [virtio balloon device](http://rwmj.wordpress.com/2010/07/17/virtio-balloon/) is a way for the guest OS to share its memory with the host OS, 
OSv's memory balloon for Java is a way for the JVM to give up some of its memory to the guest OS.

It sounds counterintuitive to give the JVM more memory by making it give up memory--but with ballooning in place we can give all the memory on the system to the JVM, and balance its requirements with those of the kernel.

FIXME BENCHMARK DESCRIPTION AND CODE HERE

Linux (SLES11)  / FIXME JAVA VERSION
real    3m28.282s
user    0m3.720s
sys    0m6.764s

OSv / FIXME JAVA VERSION
real    0m48.038s
user    0m17.618s
sys    0m4.311s

## Understanding the results

Why such a big difference?

Can't we rely on the operating system's memory management?

After all, Linux already manages memory for the JVM, by taking pages out when we need to page in other things.

The problem is the disconnect between a conventional operating system's msmory mangement and the memory management within the JVM.  They're working on the same problem, but only sharing one tiny piece of information about each memory page: the last access time.  

Swapping in Linux generates expensive disk I/O.  Unfortunately, the OS has limited ability to  *which* page to swap, because the fact that the Java GC touches a lot of unrelated pages make the hint of "last access" that Linux uses disconnected from the actual Java usage.

Also, at this point, Linux need to make a decision about how many pages to swap out, versus how many to use for page cache. This is a hard decision to take if you are blind about the internal state of the JVM.

The problem with the swapping approach is that the system component with the least information about memory usage (the kernel) is required to make decisions about the most expensive operation (swapping).  With ballooning, the JVM is fully in control of its memory management, and then delegates the management of the kernel's memory needs back to the kernel.

FIXME DETAIL ON JVM MEMORY POLICY?

FIXME LINK TO CODE

FIXME CALL TO ACTION
