---
layout: post

title:  Advanced.Mac.OS.X.Programming
date:   2016-11-22 11:58:00 +0800

categories: OS.X
tag: [编程]
---

* content
{:toc}


Foreword
====================================


In 1989, the band Living Colour released the song Glamour Boy which explained that the world was filled with glamour boys who valued appearance over other, deeper properties. And the song proclaims “I ain't no glamour boy.”

Many programmers are glamour boys. They lack a deep understanding of what is happening under the surface, so they are pleased simply to ship an application that the client doesn't hate. If they had a better comprehension of how the operating system does its work, their code would be faster, more reliable, and more secure.

If you are a Mac OS X or iOS programmer, the knowledge that separates really good coders from glamour boys is in this book. It demystifies the plumbing of Mac OS X. It helps you interpret mysterious messages from the compiler and debugger. When you finish reading this book, you will be a better programmer than when you started.

I met Mark Dalrymple shortly after Mac OS X shipped. I was astonished by his deep knowledge of Mac OS X's Unix core and how Apple had extended and enhanced it. I begged him to write the first edition of this book. The result was even better than I had hoped for.

If you read this third edition, you will master:


{% highlight bash %}

+ Blocks  
+ Grand Central Dispatch  
+ Multithreading  
+ Operation queues  
+ Network programming  
+ Bonjour  
+ The run loop
+ File I/O and metadata  
+ Forking off tasks  
+ File system events  
+ Keychain access  
+ Performance tuning  
+ Instruments and DTrace  
+ Memory and the garbage collector


{% endhighlight %}



Introduction
====================================


### Mac OS X: Built to Evolve ###

Complex systems come into existence in only two ways: through careful planning or through evolution. An airport is an example of something that is planned carefully beforehand, built, and then undergoes only minor changes for the rest of its existence. Complex organisms (like humans) are an example of something that has evolved continually from something simple. In the end, organisms that
are well suited to evolution will always win out over organisms that are less suited to evolve.  


An operating system evolves. Of course, the programmer who creates a new operating system designs it carefully, but in the end, an operating system that is well suited to evolution will replace an operating system that is not. It is, then, an interesting exercise to think about what traits make an operating system capable of evolution.  


The first version of Unix was developed by Ken Thompson at Bell Laboratories in 1969. It was written in assembly language to run on a PDP-7. Dennis Ritchie, also at Bell Labs, invented the C programming language. Among computer languages, C is pretty low level, but it is still much more portable than assembly language. Together, Thompson and Ritchie completely rewrote Unix in C. By 1978, Unix was running on several different architectures. Portability, then, was the first indication that
Unix is well suited to evolution.  


In 1976, Bell Labs began giving the source code for Unix to research facilities. The Computer Systems Research Group at UC Berkeley got a copy and began tinkering with it. The design of Unix was exceedingly elegant and a perfect platform upon which to build two important technologies: virtual
memory and TCP/IP networking. By freely distributing the source code, Bell Labs was inviting people to extend Unix. Extensibility was the second indication that Unix is well suited to evolution.  



4.4BSD was the last release of Unix produced by Berkeley. It was used as the basis for FreeBSD,
OpenBSD, NetBSD, and Mac OS X. Today, Unix is used as an operating system for cellular phones
and supercomputers. It is the most popular operating system for web servers, mail servers, and
engineering workstations. The manner in which it has found a home in so many niches is yet another
indication that Unix is capable of evolving.  


Mac OS X is based upon a hybrid of Mach and 4.4BSD, but notice that this new niche, a desktop operating system that your grandmother will love as well as a mobile device operating system that your kids will love, is very different from Unix’s previous purposes. To reach this goal, Apple has made several important additions to its Unix core. The Unix part of Mac OS X is called Darwin. The large additions to Darwin that Apple has made are known as the core technologies. Apple, recognizing that Unix must continue to evolve, has released the source code to Darwin and most of the core technologies.




### This Book ###

>My thought is that things shouldn’t be “magic” in your field of expertise. I don’texpect someone to implement a compiler or operating system, but they shouldn’t bemystical black boxes.  —Paul Kim, Chief Noodler, Noodlesoft


When you finish this book, you will be able to:  

* Create applications that leverage the full power of the Unix APIs.

* Use advanced ideas like multithreading and task queues to increase the performance of your applications.

* Add networking capabilities to event-driven applications.

* Make networked applications Bonjour-aware.

* Use the keychain and authorization capabilities of the security framework.

* Understand and use gcc, the linker, the debugger, Instruments, and the occasional Xcode dark corner.

* Use performance tools to evaluate and improve the responsiveness of your existing applications.


### Typographical Conventions ###


To make the book easier to comprehend, we have used several typographical conventions.


### Online Materials ###

本书的代码可以从[这里][BookCode]下载。本书的PDF版本可以从[这里][BookPDF]下载。  

[BookCode]:<http://www.borkware.com/corebook/> "Advanced OS.X Programing Code"
[BookPDF]:<{{ "/downloads/books/Advanced.Mac.OS.X.Programming.pdf" | prepend:site.baseurl}}> "Advanced OS.X Programing PDF"







