---
title: "Heap Memory Allocation: First-fit and Best-fit Approaches"
---


# Heap Memory Allocation - First-fit and Best-fit Approaches

When allocating heap memory, there are multiple strategies we can deploy to use the allocated memory.  This guide explores two popular approaches: **first-fit** and **best-fit** allocation.


## Memory Overview

Below is a simple program that makes a series of heap memory allocations using `malloc`, and some calls to `free`:

```
void *a = malloc(128);  // 128 == 0x 80 bytes
void *b = malloc(384);  // 384 == 0x180 bytes
void *c = malloc(128);
void *d = malloc(128);
void *e = malloc(256);  // 256 == 0x100 bytes
void *f = malloc(128);
/* Line 7 */
free(b);
free(c);
free(e);
```

With sequential allocation of memory and no overhead, at 'Line 7` a total of **900** bytes are allocated:

<style>
.alloc tr td:nth-of-type(1) {
  border: solid 1px black;
  background-color: #ccc;
  font-size: 12px;
}

.alloc tr td.startOfHeap:nth-of-type(1) {
  border-width: 0px;
  border-top: solid 2px black;
  background-color: inherit;
}

.alloc tr td:nth-of-type(2) {
  position: relative;
  top: 12px;

  vertical-align: bottom;
}

.alloc {
  margin: 0px auto;
  text-align: center;
  margin-bottom: 20px;
  font-size: 14px;
}

td.free { background-color: #efe !important; }
td.new {  background-color: hsla(60, 100%, 40%, 1) !important; font-weight: bold; }

.s2 { height: 36px; }
.s3 { height: 54px; }
.s4 { height: 72px; }
.s0_5 { height: 10px; font-size: 8px !important; }

</style>

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2">256 bytes</td><td>← e = <code>0x1300</code></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td>128 bytes</td><td>← c = <code>0x1200</code></td></tr>
  <tr><td class="s3">384 bytes</td><td>← b = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

After we run the remaining code, we introduce several holes into our heap memory:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← <strike>e = <code>0x1300</code></strike></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="free">128 bytes (FREE)</td><td>← <strike>c = <code>0x1200</code></strike></td></tr>
  <tr><td class="s3 free">384 bytes (FREE)</td><td>← <strike>b = <code>0x1080</code></strike></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Since we have two free blocks next to each other, we can **coalesce** these two small free blocks (128 + 384 = 512 bytes) together:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s4 free">512 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

<b>Question</b>: What strategies are best to find how to fill these holes when new allocations arrive?

## Strategy #1: First-Fit

The **First-fit Allocation Strategy** traverses the system's list of free blocks to find the **first** free block that is large enough that can be used to fulfill the request.  For example, consider three new requests:

```
void *x = malloc(128);
void *y = malloc(500);
void *z = malloc(100);
```

Using first fit, the first allocation request, `x` for 128 bytes, finds that it can placed in the very first free heap block and uses a small part of the block:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s3 free">384 bytes (FREE)</td><td></td></tr>
  <tr><td class="s1 new">128 bytes</td><td>← x = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Using first fit, the second allocation request, `y` for 500 bytes, cannot fit in either the first hole (448 bytes) nor the second hole (256 bytes).  To facilitate this heap request, new heap memory must be allocated:

<table class="alloc">
  <tr><td class="s4 new">500 bytes</td><td>← y = <code>0x1480</code></td></tr>
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s3 free">384 bytes (FREE)</td><td></td></tr>
  <tr><td class="s1 new">128 bytes</td><td>← x = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Finally, the final allocation request, `z` for 128 bytes, is able to fit immediately after `x` in our first hole of 448 bytes:

<table class="alloc">
  <tr><td class="s4 new">500 bytes</td><td>← y = <code>0x1480</code></td></tr>
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s2 free">284 bytes (FREE)</td><td></td></tr>
  <tr><td class="s1 new">100 bytes</td><td>← z = <code>0x1100</code></td></tr>
  <tr><td class="s1 new">128 bytes</td><td>← x = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

## Strategy #2: Best-Fit

The **Best-fit Allocation Strategy** traverses **all** of your free blocks to find the free block that **best fits** the memory request.  A best fit is defined to be the block that leaves as small of a resultant block as possible.

Using the same memory as we had before the first strategy:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s4 free">512 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Consider three new requests:

```
void *x = malloc(128);
void *y = malloc(500);
void *z = malloc(100);
```

Using best fit, the available free blocks are 512 bytes (at `0x1080`) and 256 bytes (at `0x1300`).  Since the 256 byte block best fits a request for 128 bytes, `x` is allocated within the 256 byte block:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="free">128 bytes (FREE)</td><td></td></tr>
  <tr><td class="s1 new">128 bytes</td><td>← x = <code>0x1300</code></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s4 free">512 bytes (FREE)</td><td></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Next, the allocation request for `y` of 500 bytes can only be allocated in the 512-byte hole and is a nearly perfect fit:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="free">128 bytes (FREE)</td><td></td></tr>
  <tr><td class="new">128 bytes</td><td>← x = <code>0x1300</code></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s0_5 free">12 bytes (FREE)</td><td></td></tr>
  <tr><td class="s4 new">500 bytes</td><td>← y = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

Finally, the final allocation request -- `z` of 100 bytes -- fits nearly perfectly in the hole of 128 bytes:

<table class="alloc">
  <tr><td>128 bytes</td><td>← f = <code>0x1400</code></td></tr>
  <tr><td class="s0_5 free">28 bytes (FREE)</td><td></td></tr>
  <tr><td class="new">100 bytes</td><td>← z = <code>0x1380</code></td></tr>
  <tr><td class="new">128 bytes</td><td>← x = <code>0x1300</code></td></tr>
  <tr><td>128 bytes</td><td>← d = <code>0x1280</code></td></tr>
  <tr><td class="s0_5 free">12 bytes (FREE)</td><td></td></tr>
  <tr><td class="s4 new">500 bytes</td><td>← y = <code>0x1080</code></td></tr>
  <tr><td>128 bytes</td><td>← a = <code>0x1000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x1000</code>)</td><td></td></tr>
</table>

## First-Fit vs. Best-Fit Analysis

Both first-fit and best-fit strategies are good memory allocation strategies that allow the re-use of memory.  Both strategies have different strengths and weaknesses.

Running Time:

- First-fit is faster, allowing the searching for memory to stop **immediately** after finding a free-block of large enough size.
- Best-fit is slow, requiring the search of **every free block** in memory.

Allocation Patterns:

- First-fit can **use up valuable large blocks of free space** for tiny requests, resulting in the need to grow the heap more often and using more memory than a best-fit algorithm.  *(You saw this in our example above where first-fit needed to allocate more heap memory for `y`.)*
- Best-fit will result in **many small holes** that may never be useful resulting in increased memory fragmentation.  *(You saw this in our example above where best-fit created a hole of 12 and 28 bytes -- smaller than any request our program ever made.)*


[Next: Building Malloc](buildingMalloc.html)


