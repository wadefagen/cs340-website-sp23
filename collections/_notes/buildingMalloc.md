---
title: "Building Malloc"

description: When creating a memory allocator, such as an implementation of malloc, there are many different approaches to efficiently store memory. This guide explores the use of per-allocation metadata with free lists and various optimizations.
---


# Memory Allocation

When creating a memory allocator, such as an implementation of `malloc`, there are many different approaches to efficiently store memory.  This guide explores the use of per-allocation **metadata** and ends with ideas for optimization of the allocation process.


## Memory Allocator Overview

Consider a simple program that makes a series of heap memory allocations using `malloc` and some calls to `free`:

```
void *a = malloc(256);  // 256 == 0x 100 bytes
void *b = malloc(256);
void *c = malloc(128);
void *d = malloc(256);
void *e = malloc(128);
/* Line 6 */
free(a);
free(b);
free(d);
/* Line 10 */
```

There are a few things to note about creating a memory allocator:

- Initially, the heap is 0 bytes in size.  To store data on the heap, we must increase the size of the heap.
- We increase the size of the heap by using the library call `sbrk`.  For example, `sbrk(100)` increases the heap by 100 bytes.

In this implementation, we will store information about each allocation **immediately before** the requested memory.  We can organize all of our metadata in a C `struct`.  Initially, this structure has only two members:

```
typedef struct _metadata_t {
  unsigned int size;     // The size of the memory block.
  unsigned char isUsed;  // 0 if the block is free; 1 if the block is used.
} metadata_t;
```

Looking at the allocation required for `a`, we know we need 256 bytes of memory on the heap for the program (the amount provided to malloc) **plus** enough memory to store the metadata.  A simple implementation of `malloc` might now be:

```
void *malloc(size_t size) {
  // Allocate heap memory for the metadata structure and populate the variables:
  metadata_t *meta = sbrk( sizeof(metadata_t) );
  meta->size = size;
  meta->isUsed = 1;

  // Allocate heap memory for the requested memory:
  void *ptr = sbrk( size );

  // Return the pointer for the requested memory:
  return ptr;
}
```

### Line 1

Visually, we can view our heap memory allocation after allocating `a` (Line 1):

<table class="alloc">
  <tr><td class="s2 inUse">256 bytes</td><td>← a</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td></td></tr>
</table>

In this visualization, there are several interesting bits:

- Notice that the 256 bytes in **dark red** are now allocated to the program that requested the allocation of memory.  We cannot edit, change, move, or do anything with this memory.
- The pointer returned to `a` is pointing to the beginning fo the allocated memory to the program and the metadata is located immediately before that pointer.
- Finally, as seen before, heap memory is growing **UP** from the start of the heap.


### Lines 2-5

Continuing the allocation, we can visually show the allocation after running through Line 5:

<table class="alloc">
  <tr><td class="s1 inUse">128 bytes</td><td>← e</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 inUse">256 bytes</td><td>← d</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="s1 inUse">128 bytes</td><td>← c</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 inUse">256 bytes</td><td>← b</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="s2 inUse">256 bytes</td><td>← a</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td></td></tr>
</table>


### Lines 7-9

At Line 6, we begin to free previously allocated memory.  To do this, our free implementation needs to find the metadata and change `isUsed` to `0` to denote the memory is free and can be reused.  A simple C program that modifies metadata located before the allocation:

```
void free (void *ptr) {
  // Find the metadata located immediately before `ptr`:
  metadata_t *meta = ptr - sizeof( metadata_t );
  
  // Mark the allocation is free:
  meta->isUsed = 0;
}
```

Visually, after running `free(a)` (Line 7), we see that the structure data is updated and the block is freed:

<table class="alloc">
  <tr><td class="s1 inUse">128 bytes</td><td>← e</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 inUse">256 bytes</td><td>← d</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="s1 inUse">128 bytes</td><td>← c</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 inUse">256 bytes</td><td>← b</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← a</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td></td></tr>
</table>

This continues repeats with `free(b)` (Line 8) and `free(d)` (Line 9):

<table class="alloc">
  <tr><td class="s1 inUse">128 bytes</td><td>← e</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← d</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="s1 inUse">128 bytes</td><td>← c</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← b</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← a</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td></td></tr>
</table>



## Traversing to Find Free Memory

Suppose we now want to handle four new requests:

```
void *r1 = malloc(10);
void *r2 = malloc(10);
void *r3 = malloc(300);
void *r4 = malloc(250);
```

Our current implementation, as written, will simply keep adding to the heap by calling `sbrk` again and again.  However, I think we can do better -- we have free memory -- it's just hidden as holes within our heap!

To begin to traverse our memory, we need to remember where our heap begins.  To do this, we can modify `malloc` to record the start of our heap for us to use later in a global variable:

```
void *startOfHeap = NULL;

void *malloc(size_t size) {
  printf("Inside: malloc(%lu):\n", size);
  // If we have not recorded the start of the heap, record it:
  if (startOfHeap == NULL) {
    startOfHeap = sbrk(0);
    // sbrk(0) returns the current end of our heap, without increasing it.
  }

  // ...rest of malloc, same as before...
}
```

Now, and forever, we now know the memory address of the start of the heap -- which is also the location of our first metadata chunk:

<table class="alloc">
  <tr><td class="s1 inUse">128 bytes</td><td>← e</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← d</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="s1 inUse">128 bytes</td><td>← c</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← b</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← a</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td style="top: -13px;">← startOfHeap</td></tr>
</table>

Visually inspecting the memory:

- `startOfHeap` is the location of the first region of metadata.
- Since the metadata contains `size`, we can skip ahead by **exactly** `size` plus `sizeof(metadata_t)` to find the second metadata entry.


Specifically, it may be useful for `malloc` to print the contents of each metadata chunk:

```
void *startOfHeap = NULL;

void *malloc(size_t size) {
  printf("Inside: malloc(%lu):\n", size);
  // If we have not recorded the start of the heap, record it:
  if (startOfHeap == NULL) {
    startOfHeap = sbrk(0);
    // sbrk(0) returns the current end of our heap, without increasing it.
  }

  // Print out data about each metadata chunk:
  metadata_t *curMeta = startOfHeap;
  void *endOfHeap = sbrk(0);
  printf("-- Start of Heap (%p) --\n", startOfHeap);
  while ((void *)curMeta < endOfHeap) {   // While we're before the end of the heap...
    printf("metadata for memory %p: (%p, size=%d, isUsed=%d)\n", (void *)curMeta + sizeof(metadata_t), curMeta, curMeta->size, curMeta->isUsed);
    curMeta = (void *)curMeta + curMeta->size + sizeof(metadata_t);
  }
  printf("-- End of Heap (%p) --\n\n", endOfHeap);
  
  // ...rest of malloc, same as before...
}
```

## Running Our Allocator

Let's actually run this code!

- If you have not done so already, go start on the MP!  When you've downloaded all of the files and have your development environment set up, come back to this guide and get started with malloc.

### Creating `sample1`

Once you have a basic malloc implemented, create a new file called `sample1.c` with the following code:

```
#include <stdlib.h>

int main() {
  void *a = malloc(256);  // 256 == 0x 100 bytes
  void *b = malloc(256);
  void *c = malloc(128);
  void *d = malloc(256);
  void *e = malloc(128);
  /* Line 6 */
  free(a);
  free(b);
  free(d);
  /* Line 10 */
  void *r1 = malloc(10);
  void *r2 = malloc(10);
  void *r3 = malloc(300);
  void *r4 = malloc(250);  
}
```

Save it, and compile it with `gcc sample1.c -o sample1`.  We can now run this simple program with your allocator by running `./mreplace ./sample1` and we get a LOT of output.  Let's explore by starting at the top.


### Reading the Output

The first thing we need to see is the injection line, this ensures that **our** malloc is actually being used.  The first allocation shows that a call to `malloc(256)` was made and there is currently nothing on our heap before the allocation is made (remember, we explore the contents of the heap before we make the allocation so we don't expect to see our the new allocation yet):

```
[mstats-alloc]: Injecting your alloc library into the running process.
Inside: malloc(256):
-- Start of Heap (0x56009a9ec000) --
-- End of Heap (0x56009a9ec000) --
```

The second allocation is a second call to `malloc(256)`, except our heap now contains the data from the first allocation:

```
Inside: malloc(256):
-- Start of Heap (0x56009a9ec000) --
metadata for memory 0x56009a9ec008: (0x56009a9ec000, size=256, isUsed=1)
-- End of Heap (0x56009a9ec108) --
```

The third allocation is a call to `malloc(128)` on a heap with two previous allocations:

```
Inside: malloc(128):
-- Start of Heap (0x56009a9ec000) --
metadata for memory 0x56009a9ec008: (0x56009a9ec000, size=256, isUsed=1)
metadata for memory 0x56009a9ec110: (0x56009a9ec108, size=256, isUsed=1)
-- End of Heap (0x56009a9ec210) --
```

Skipping far ahead, the final allocation is for `malloc(250)` and we can observe the current heap:
```
Inside: malloc(250):
-- Start of Heap (0x56009a9ec000) --
metadata for memory 0x56009a9ec008: (0x56009a9ec000, size=256, isUsed=0)
metadata for memory 0x56009a9ec110: (0x56009a9ec108, size=256, isUsed=0)
metadata for memory 0x56009a9ec218: (0x56009a9ec210, size=128, isUsed=1)
metadata for memory 0x56009a9ec2a0: (0x56009a9ec298, size=256, isUsed=0)
metadata for memory 0x56009a9ec3a8: (0x56009a9ec3a0, size=128, isUsed=1)
metadata for memory 0x56009a9ec430: (0x56009a9ec428, size=10, isUsed=1)
metadata for memory 0x56009a9ec442: (0x56009a9ec43a, size=10, isUsed=1)
metadata for memory 0x56009a9ec454: (0x56009a9ec44c, size=300, isUsed=1)
-- End of Heap (0x56009a9ec580) --
```

Visually, this matches our diagram **exactly** (except that our printouts are in reverse order, where the start of the heap is first) and we see that our `metadata_t` is 8 bytes in size:

<table class="alloc">
  <tr><td class="s2 inUse">300 bytes</td><td>← r3 = 0x56009a9ec454</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=300, isUsed=1</td><td></td></tr>

  <tr><td class="s0_5 inUse">10 bytes</td><td>← r2 = 0x56009a9ec442</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=10, isUsed=1</td><td></td></tr>

  <tr><td class="s0_5 inUse">10 bytes</td><td>← r1 = 0x56009a9ec430</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=10, isUsed=1</td><td></td></tr>

  <tr><td class="s1 inUse">128 bytes</td><td>← e = 0x56009a9ec3a8</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← d = 0x56009a9ec2a0</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="s1 inUse">128 bytes</td><td>← c = 0x56009a9ec218</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=128, isUsed=1</td><td></td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← b = 0x56009a9ec110</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td>← metadata (for b) = 0x56009a9ec108</td></tr>
  <tr><td class="s2 free">256 bytes (FREE)</td><td>← a = 0x56009a9ec008</td></tr>
  <tr><td class="s0_5 meta">metadata_t: size=256, isUsed=0</td><td></td></tr>
  <tr><td class="startOfHeap">Start of Heap</td><td style="top: -13px;">← startOfHeap = 0x56009a9ec000</td></tr>
</table>

Look at this -- we've identified, in code, where free memory is located!  Sadly, **we passed right by it and did not reuse it for the new allocation**.  Now it's your turn to use best-fit, first-first, or any other idea to decide which free block to use for the allocation! :)

#### Note on Output

The output you are printing is extremely useful for debugging, but will make your allocator **very slow** due to printing the messages to the screen.  You should remove lines related to the output when running the more complex tester programs.


<style>
.alloc tr td:nth-of-type(1) {
  border: solid 1px black;
  font-size: 12px;
}

.alloc tr td:nth-of-type(2) {
  text-align: left;
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
td.meta { background-color: #ffc; }
td.inUse { background-color: #400; color: #fee;  }

.s2 { height: 36px; }
.s3 { height: 54px; }
.s4 { height: 72px; }
.s0_5 { height: 10px; font-size: 8px !important; }

</style>
