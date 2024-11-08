---
layout: post
category: Jekyll
date: 2024-05-15
nav_order: 3
title: Topic FreeRTOS - Heap Memory Management
author: Hoseok Lee
---

## 1. Overview
In this chapter, we will explore what 'Heap' means in terms of C programming and Microprocessor. Then, we will examine several features that FreeRTOS provides. In modern operating systems, numerous applications run concurrently, and each application should allocate a suitable Heap region owned by itself to manipulate data dynamically. Otherwise, the system will crash. As a quick aside, in Over-The-Air (OTA) systems, when deleting an old application and installing a new one on the operating system, managing the Heap (RAM) region reserved for them is crucial to prevent system malfunctions. Therefore, understanding the Heap will help you become a competent C programmer.

## 1-0. FreeRTOS Heap Basic

<div style="text-align: center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/o4RsFG_gTU8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>


## 2. Introduction
Heap memory is a scheme for accessing RAM in a CPU or Microprocessor dynamically. Here, I want to emphasize the terms <b>'Dynamic'</b> and <b>'RAM'</b>. Unlike the 'Stack', which is allocated statically as a memory region in RAM, the Heap is a memory region allocated dynamically during program runtime. If you have experience with using <b>'malloc()'</b> and <b>'free()'</b>, you'll understand this concept.

As for its advantages,
1. Whenever an application or you require it, you can reserve Heap memory with different sizes. This is one of the biggest advantages of using the Heap.
2. The Heap is shared by all software functions, but it requires the programmer to manage Heap memory, which can be complex.

Regarding its drawbacks,
1. The implementation of accessing Heap memory may vary based on different operating systems and CPUs.
2. Heap is prone to Memory Leakage (Memory Fragmentation) since the required Heap size will differ every time. This is one reason why your computer may slow down as you use the device. To address this problem, Java provides an 'Automatic Garbage Collection' function.
3. Heap has slower access times compared to the Stack.

## 3. Features provided by FreeRTOS
### 3.1 Concept
Kernel objects include <b>tasks</b>, <b>queues</b>, <b>semaphores</b>, and <b>event groups</b>. These kernel objects are stored in the heap memory of the RAM. One distinct feature is that FreeRTOS abstracts memory allocation from the core codebase. In other words, it treats memory allocation as a portable layer, providing users with flexibility. Therefore, users can implement their own memory allocation by replacing the FreeRTOS-provided memory allocation.

### 3.2 Statically allocated RAM (Heap)
1. FreeRTOS provides a statically allocated RAM feature. In the FreeRTOSConfig.h file, configSUPPORT_STATIC_ALLOCATION should be set to 1.
2. A statically allocated heap makes FreeRTOS appear to consume a lot of RAM because the heap becomes part of the FreeRTOS data.

### 3.3 Dynamically allocated RAM (Heap)
FreeRTOS has implemented <b>pvPortMalloc()</b> and <b>vPortFree()</b> for dynamic memory allocation, instead of using <b>malloc()</b> and <b>free()</b> from the C standard library. The main reasons are:
* The implementation of <b>malloc()</b> and <b>free()</b> is relatively large, making it unsuitable for the small size of embedded systems.
* Using the C library functions may cause memory fragmentation in the embedded system.
* The linker configuration and debugging may be more difficult with the C library functions.

FreeRTOS provides five types of heap memory, which I will explain briefly. Detailed explanations can be found at[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

#### 3.3.1 Heap_1
1. Heap_1 is a basic heap implementation. Heap_1 only creates tasks and other kernel objects <b>before starting the FreeRTOS scheduler</b>.
2. This means that memory is allocated only before the application starts, and the allocated memory remains for the application's lifetime.
3. <mark style="background-color: lightblue">It does not support vPortFree().</mark>
4. The purpose of Heap_1 is to be used in commercially critical and safety-critical systems, such as those using the Rust programming language, which prohibits dynamic memory allocation.

<p align="center">
    <img src="../../assets/postsAssets/FreeRTOS/Heap_1.png" width="500"/>
    <br><b>[Pic.1] Heap_1 Heap Memory Creation</b></p>

#### 3.3.2 Heap_2
1. Heap_2 is superseded by Heap_4, which is an enhanced version of Heap_2. FreeRTOS does not recommend using Heap_2 for new designs.
2. Heap_2 uses the <b>best-fit algorithm</b> to allocate memory. For example, if pvPortMalloc() requests 20 bytes of RAM, and the available RAM blocks are 5 bytes, 25 bytes, and 100 bytes, FreeRTOS will choose the 25-byte RAM block. It then divides it into a 20-byte block and a 5-byte block, returning the pointer to the 20-byte block. The remaining 5-byte block will be used in the future.
3. <mark style="background-color: lightblue">Note that Heap_2 does not combine adjacent free blocks into a single large block.</mark>

<p align="center">
    <img src="../../assets/postsAssets/FreeRTOS/Heap_2.png" width="500"/>
    <br><b>[Pic.2] Heap_2 Heap Memory Creation and Deletion</b></p>

#### 3.3.3 Heap_3
1. Heap_3 uses the stand library 'malloc()' and 'free()'
2. <b>configTOTAL_HEAP_SIZE</b> constant is not used.


#### 3.3.4 Heap_4
1. Heap_4 subdivides an array into smaller blocks, and the array should be statically allocated and dimensioned by configTOTAL_HEAP_SIZE.
2. Heap_4 uses the <b>first-fit algorithm</b> to allocate memory. For example, if pvPortMalloc() requests 20 bytes of RAM, and the available RAM blocks are 5 bytes, 200 bytes, and 100 bytes, FreeRTOS will choose the 200-byte RAM block. It then divides it into a 20-byte block and a 180-byte block, returning the pointer to the 20-byte block. The remaining 180-byte block will be used in the future.
3. <mark style="background-color: lightblue">Note that Heap_4 does combine adjacent free blocks into a single large block, which minimizes the risk of memory fragmentation.</mark>

<p align="center">
    <img src="../../assets/postsAssets/FreeRTOS/Heap_4.png" width="500"/>
    <br><b>[Pic.3] Heap_4 Heap Memory Creation and Deletion</b></p>

#### 3.3.5 Heap_5
1. Heap_5 uses the same allocation algorithm as Heap_4.
2. <mark style="background-color: lightblue">But Heap_5 can combine memory from multiple separate <b>physical</b> memory spaces into a single heap.</mark>
3. Heap_5 is useful when the RAM provided by the system on which FreeRTOS is running does not appear as a single contiguous block in the system's memory map.
4. Instructions on how to configure multiple RAM blocks as a single RAM can be found in [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0].

<p align="center">
    <img src="../../assets/postsAssets/FreeRTOS/Heap_5.png" width="500"/>
    <br><b>[Pic.5] Heap_5 for multiple separated memory space</b></p>

## 4. APIs for memory allocation
All APIs for memory allocation, which are supported by FreeRTOS, is elaborated at [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

## 5. Summary and Conclusion

|Heap Type|Summary|
|:--:|:---|
|Heap_1|- Primitive Memory Allocation Scheme <br> - Usefule to memory safe system <br> - Does not support <b>'vPortFree()'</b>|
|Heap_2|- Superseded by Heap_4 <br> - Uses <b>'Best-Fit algorithm'</b>|
|Heap_3|- Heap_3 uses the stand library 'malloc()' and 'free()'|
|Heap_4|- an array is subdivided into a smaller blocks <br> - Use <b>'First-Fit algorithm'</b> <br> - Not combine adjacent free blocks into a single large block|
|Heap_5|- Suitable for the modern MUCs Memory Scheme <br> - Use <b>'First-Fit algorithm'</b> <br> - Combine adjacent free blocks into a single large block|

<mark style="background-color: lightblue">In this FreeRTOS port project, Heap_5 would be the proper candidate.<mark>


## Reference
- [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]:https://github.com/FreeRTOS/FreeRTOS-Kernel-Book/blob/main/booktitle.md