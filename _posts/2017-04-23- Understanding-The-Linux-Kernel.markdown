---
layout: post
title:  "Understanding the linux kernel!"
date:   2017-04-23 20:41:22 +0800
categories: Linux
---

“When you bow deeply to the universe, it bows back; when you call out the name of God, it echoes inside you.”
―Morihei Ueshiba

## Description
这个是对<<Understanding the linux kernel>>的读书笔记,记录下了要点.持续更新


## Chapter 1
### terms
- symmetric multiprocessing (SMP)
- Journaling file system 日志型文件系统
- General Public License (GPL)
- Fiber Distributed Data Interface (FDDI)光纤分布式数据接口
- High Performance Parallel Interface (HIPPI)高性能并行接口
- cpu nonprivileged mode:User Mode
- cpu privileged mode:Kernel Mode
- TSS:Task State Segment



### 区分module和microkernel operating systems
- module [monolithic operating Systems]:
- does not run as a specific process.Instead it is executed in Kernel Mode on behalf of the current process ,like any other statically linked kernel function

- microkernel operating Systems:
- demand a very small set of functions from the kernel, generally including a few synchronization primitives, a simple scheduler, and an interprocess communication mechanism. Several system processes that run on top of the microkernel implement other operating system-layer functions, like memory allocators, device drivers, system call handlers, and so on.

### File
A Unix file is an information container structured as a sequence of bytes; the kernel does not interpret the contents of a file.

#### Files Types
- Regular file
- Directory
- Symbolic link
- Device file
- Block-oriented device file
- Character-oriented device file
- Device files are related to I/O devices and device drivers integrated into the kernel
- Pipe and named pipe (also called FIFO)
- Pipes and sockets are special files used for interprocess communication
- Socket

#### Inode, consist the information:
- File type (see previous section)
- Number of hard links associated with the file
- File length in bytes
- Device ID (i.e., an identifier of the device containing the file)
- Inode number that identifies the file within the filesystem
- User ID of the file owner
- Group ID of the file
- Several timestamps that specify the inode status change time, the last access time, and
  the last modify time
- Access rights and file mode (see next section)


### File Descriptor and Inode
- each file consists of a sequence of characters
- Information needed included in a data structure-inode

### 1.6.1 The Process/Kernel Model

he kernel itself is not a process but a process manager. 

Besides user processes, Unix systems include a few privileged processes called kernel threads:
- They run in Kernel Mode in the kernel address space.
- They do not interact with users, and thus do not require terminal devices.
- They are usually created during system startup and remain alive until the system is
  shut down.


Kernel routine invoded:
- process itself invokes a system call 
- process sinals an exception, cause the kernel invoke to handle  the exception
- device issues an interrupt signal
- kernel thread is executed

### 1.6.2 Process Implementation
#### Process Descriptor
- program counter and stack pointer
- general-purpose registers
- floating point registers
- processor control registers(Process Status Word-cpu state)
- memory manager registers keep track of RAM

### 1.6.3 Reentrant Kernels
- All Unix Kernels are reentrant


## Chapter 2.Memory Addressing
### 2.1 Memory address
- Logical address:
- Included in the machine language instructions to specify the address of an operand or of an instruction.
- segment + offset

- Linear addrss:
- A single 32-bit unsigned integer that can be used to address up to 4 GB
- range from 0x00000000 to 0xffffffff.

- Physical address:
- Used to address memory cells included in memory chips.


- sample:
  我们写个最简单的hello world程序，用gccs编译，再反编译后会看到以下指令：

···
mov    0x80495b0, %eax
···

这里的内存地址0x80495b0 就是一个逻辑地址，必须加上隐含的DS 数据段的基地址，才能构成线性地址。也就是说 0x80495b0 是当前任务的DS数据段内的偏移。






### 2.2 Segmentation in Hardware

- logical address = a segment identifier[16-bit field called Segment Selector]  + an offset[32-bit field]

- processor provides segmentation registers to hold Segment Selectors
- CS:code segment register, points to a segment containing program instructions

- SS:stack segment register, points to a segment containing the current program stack

- DS:data segment register, points to a segment containing static and external data

- ES,FS,GS:->three segmentation registers are general purpose and may refer to arbitrary segments.


### 2.5 paging in Linux
Linux handling of processes relyis heavily on paging.




####  Signals and Interprocess Communication
- Asynchronous notifications
- Synchronous errors or exceptions

The kernel implements these constructs as IPC resources: a process acquires a resource by
invoking a shmget( ), semget( ), or msgget( ) system call. Just like files, IPC resources
are persistent: they must be explicitly deallocated by the creator process, by the current
owner, or by a superuser process.

Shared memory provides the fastest way for processes to exchange and share data. 



1.6.7.1 Zombie processes
a process
remains in that state until its parent process executes a wait( ) system call on it. 



### Memory Management
#### virtual memory
- Several processes can be executed concurrently.
- It is possible to run applications whose memory needs are larger than the available
  physical memory.
- Processes can execute a program whose code is only partially loaded in memory.
- Each process is allowed to access a subset of the available physical memory.
- Processes can share a single memory image of a library or program.
- Programs can be relocatable, that is, they can be placed anywhere in physical memory.
- Programmers can write machine-independent code, since they do not need to be
  concerned about physical memory organization.

#### Random access memory usage
two portions of the random access memory(RAM).
- A few megabytes are dedicated to storing the kernel image (i.e., the kernel code and
  the kernel static data structures). 
- The remaining portion of RAM is used in three possible ways:
  • To satisfy kernel requests for buffers, descriptors, and other dynamic kernel data
  structures
  • To satisfy process requests for generic memory areas and for memory mapping of files
  • To get better performance from disks and other buffered devices by means of caches












