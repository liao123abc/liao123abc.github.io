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



### 2.2 Segmentation in Hardware

- logical address = a segment identifier[16-bit field called Segment Selector]  + an offset[32-bit field]

- processor provides segmentation registers to hold Segment Selectors
- CS:code segment register, points to a segment containing program instructions

- SS:stack segment register, points to a segment containing the current program stack

- DS:data segment register, points to a segment containing static and external data

- ES,FS,GS:->three segmentation registers are general purpose and may refer to arbitrary segments.


### 2.5 paging in Linux
Linux handling of processes relyis heavily on paging.
