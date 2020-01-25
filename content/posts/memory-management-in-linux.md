---
title: "Memory Management in Linux"
date: 2020-01-02T17:20:51+01:00
draft: true
tags: ["linux", "kernel", "memory management", "cloud computing", "computer science"]
---

# Introduction

I hope this introduction serves well for anyone who needs a refresher about Operating systems in general, or people who wants to know how a Linux/Unix system works under the hood, after all the Cloud is made of Unix/Linux systems (huge part of it). Memory management is a complex subject, and I declared myself a rookie in the sense about how the Linux kernel works, however I will try to include as much details as I can possible explain or understand.


Take a deep breath because it's going to be a long ride

{{< youtube M7W2I9FGF9U >}}


---

# Why do we even need memory?

On brendan's book[2], the introduction to Chapter 7 says:

> System main memory stores application and kernel instructions, their working data, and file system caches. In many systems, the secondary storage for this data is the primary storage devices (disks), which operate orders of magnitude mode slowly. 

But this doesn't actually give you the answer.. the actual reason is because the `CPU` needs to execute a set of instructions which are copied from a storage device (hard drive) to the main memory aka `RAM` and here the `fetch-decode-execute`cycle shows up. To summarised things up please watch:

{{<  youtube jFDMZpkUWCw >}}

and follow-up with this [article](https://www.bbc.co.uk/bitesize/guides/z2342hv/revision/5). At this point you know why memory is needed. Before introducing the next topic I would like to quote what Linus[4] wrote on his thesis a while ago, in regards of the memory management process:

>Memory is one of the most fundamental resources in the system, and as such the performance of the memory management layer is critical to the system. Making memory management efficient is thus of primary importance: not only do the routines have to be fast, they have to be clever too, sharing physical pages aggressively in order to get the most out of a system.

furthermore...

>However, to make matters even worse, memory management is typically one of the areas where there are absolutely no hardware standards, and different CPU’s use very different means of mapping virtual addresses into physical memory pages. As such, memory management is one area where traditionally most of the code has been very architecture-dependent, and only very little high-level code has been shared across architectures even though we would like to share a lot more.

As a reminder, back in the 90's, Linus starting working on the kernel on the **Intel-80386**:

>The original Linux was not only extremely PC-centric, it wallowed in features available on PC’s and was totally unconcerned with most portability issues other than at a user level. The original Intel 80386 architecture that Linux was written for is perhaps the example of current CISC design, and has high-level support for features other current CPU’s would not even dream about implementing (see for example [CG87]).

Linus mentioned on his thesis that everything related to `memory management` belongs under the directory [mm/](https://github.com/torvalds/linux/tree/master/mm), as so:

>The basic Linux kernel is directly organized around the primary services it provides: process handling, memory management, file system management, network access and the drivers for the hardware. These areas correspond to the kernel source directories kernel, mm, fs, net and drivers respectively. 


but also, in The magic Garden explained[3], Chapter 3, it's been stated that:

>A major concern for any operating system is the method by which it manages a process given the restrictions imposed by the amount of physical memory installed in the computer hardware. It must be determined where the process will be placed in main memory, and how memory is allocated to it as it grows and shrinks during its execution. One problem that is familiar to **all** operating systems is what do when memory is **scarce**.

>To overcome these and other memory management problems, UNIX System V Release 4 has adopted a concept of **virtual memory** (Moran 1988).

---

## Virtual Memory

Brendan's book mentioned this briefly, and after getting that reference from [Peter J. Denning](https://dl.acm.org/doi/10.1145/234313.234403):

>The designers of the Atlas computer at the University of Manchester invented virtual memory in the `1950s` to eliminate a looming programming problem: **planning** and **scheduling data** transfers between main and secondary memory and **recompiling** programs for each change of **size** of main memory.


Imagine you were in the 1970s-1980s and processors like the `Intel 8080` only provided an address space of 64kbytes (you may have in your laptop at least `8192000` / `8GB` RAM), but back those days processors used direct physical memory address to access code and data. There is something in particular mentioned in the book, programmers had to arrange that the program code and data avoided any **holes** in the given address space. It is much convenient to ignore the physical layout of a machine's memory and work instead with an idealised or **virtual machine**. A program/process can then reference the virtual memory addresses for both code and data and a **hole** in memory can be **ignored** since the restrictions imposed by physical memory are **invisible** to both the program and programmer.

>A virtual address schema gives the illussion that there is more memory available than is physically installed in the machine. This allows the OS to run a program that is larger than physical memory, however a program reside in physical memory and it must generate real addresses for both its code and data. Therefore, a **translation** mecanism is needed to convert virtual memory to physical memory addresses at run time and must not provide any significant overhead.

From the previous quote, it is referring to the Memory Management Unit / **MMU**. [Translation Lookaside Buffer](https://en.wikipedia.org/wiki/Translation_lookaside_buffer) or **TLB**, is a memory cache use to reduce the time taken to access a memory location and it stores the recent translations of virtual memory to physical memory.

A process address space has its own virtual address space, which of course is mapped to physical memory by the operating system (main memory and swap, if exists). Another important definition to remember is a `page`. A page is a unit of memory, historically has been `4kb` or `8kb` but depends of the architecture, and its how the physical memory has been divided (also known as `page frames`). The selection of the size is defined when the kernel is built. On the latest Ubuntu `19.10` release the page size is still set to `4k`:

```bash
$root@testing:~# uname -r
5.3.0-26-generic

$root@testing:~# getconf PAGE_SIZE
4096
```

For now, consider the following example (From the awesome "Writing an OS in Rust"), running the same process twice in parallel:


![allocating virtual memory](https://os.phil-opp.com/paging-introduction/segmentation-same-program-twice.svg)

>Here the same program runs twice, but with different translation functions. The first instance has an segment offset of 100, so that its virtual addresses 0–150 are translated to the physical addresses 100–250. The second instance has offset 300, which translates its virtual addresses 0–150 to physical addresses 300–450. This allows both programs to run the same code and use the same virtual addresses without interfering with each other.

# Segmentation

One of the main tasks of the operating system is to isolate a process from each other, so in order to achieve this the system uses hardware capability to protect memory areas. The approach differs from hardware and the implementation on the OS. On `x86`, the two ways to protect memory are `segmentation` and `paging`. 

The support for segmentation has been removed on `x86` for 64 bits and this concept applies mostly in legacy systems aka `32` bits.

# Fragmentation



This is more clear after reviewing the following case:

![memory fragmentation](https://os.phil-opp.com/paging-introduction/segmentation-fragmentation.svg)

>There is no way to map the third instance of the program to virtual memory without overlapping, even though there is more than enough free memory available. The problem is that we need continuous memory and can't use the small free chunks.
>One way to combat this fragmentation is to pause execution, move the used parts of the memory closer together, update the translation, and then resume execution:

![memory fragmentation fixed](https://os.phil-opp.com/paging-introduction/segmentation-fragmentation-compacted.svg)

>The disadvantage of this defragmentation process is that is needs to copy large amounts of memory which decreases performance. It also needs to be done regularly before the memory becomes too fragmented. This makes performance unpredictable, since programs are paused at random times and might become unresponsive.
>The fragmentation problem is one of the reasons that segmentation is no longer used by most systems. In fact, segmentation is not even supported in 64-bit mode on x86 anymore. Instead paging is used, which completely avoids the fragmentation problem.

# References

1. https://www.kernel.org/doc/html/latest/admin-guide/mm/concepts.html#mm-concepts
2. [Systems Performance in the cloud and enterprise](https://www.amazon.com/Systems-Performance-Enterprise-Brendan-Gregg/dp/0133390098/ref=sr_1_1?keywords=systems+performance&qid=1577983140&s=books&sr=1-1)
3. [The Magic Garden explained](https://www.amazon.com/Magic-Garden-Explained-Internals-Release/dp/0130981389)
4. [Linux: A portable operating system by Linus Torvals](https://www.cs.helsinki.fi/u/kutvonen/index_files/linus.pdf)
5. https://www.thegeekstuff.com/2012/02/linux-memory-management/
6. https://os.phil-opp.com/paging-introduction/