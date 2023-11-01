---
title: "Virtual Memory - Heap and Stack Memory"

description: 
---


# Virtual Memory - Heap and Stack Memory

At the end of last lecture, we discussed the idea of **virtual memory** and a process's **page tale**.  Specifically:

- Every single process has its own **private virtual memory** that gives the process the perception of contiguous memory,
- This virtual memory is translated to its location in physical memory through each process's **page table**.

This week we will explore **how** each process will utilize its virtual memory space -- next week, we will talk about the page table itself and how we move this virtual memory around in the system.


# Data Storage

In all C and C++ code, nearly all of your data is stored in only one of two types of memory storage:

1. All variables allocated by `malloc` (or `new` in C++) is stored in **heap memory**.  When `malloc` is called, the pointer that returns from malloc will always be a pointer to "heap memory".

2. All variables used as local variables -- for example, when you just do `int i = 0;` -- is stored on a function's stack.  We refer to this memory as being stored in **stack memory**.


Your operating system will organize the processes' memory and follows three important principles:

- The beginning of the **heap memory** for every process will start at a low address (ex: `0x100000`) and grow "up".
- The beginning of the **stack memory** for every process will start at a high address (ex: `0x7ffeafeaa8e4`) and grow "down".
- The heap and stack **grow towards each other** and the process will run out of memory when the heap reaches the stack -- if you allocated more on the heap, you'd overwrite the stack; if you allocate more on the stack, you'd override the heap.


## Example 1: heapAndStack.c

Consider a simple program using heap and stack memory:

```
int val;
printf("&val: %p\n", &val);     /* Line 6 */

int *ptr = malloc(sizeof(int));
printf("&ptr: %p\n", &ptr);     /* Line 9 */
printf(" ptr: %p\n", ptr);      /* Line 10 */

int *ptr2 = malloc(sizeof(int));
printf("&ptr2: %p\n", &ptr2);   /* Line 13 */
printf(" ptr2: %p\n", ptr2);    /* Line 14 */
```

**Question**: What are reasonable possible values for all print statements?

Exploring each line one at a time, we can understand what type of memory is stored in each variable:

- On Line 6, we print `&val` or memory address where `val` is stored.  Since `val` is a local variable, it's stored on the stack and will have a large virtual memory address.  For example, `val` might be stored at `0x700fffc` if our stack started at `0x700ffff`:

<table class="alloc">
  <tr><td class="startOfStack">Start of Stack (<code>0x700ffff</code>)</td><td></td></tr>

  <tr><td><code>val</code></td><td>← &val = <code>0x700fffc</code></td></tr>
  <tr><td class="s3" style="background-color: #ccc">...empty...</td><td></td></tr>
  <tr><td><i>nothing on the heap</i></td><td></td></tr>

  <tr><td class="startOfHeap">Start of Heap (<code>0x100000</code>)</td><td></td></tr>
</table>


- On Line 9, we print `&ptr` or the memory address where `ptr` is stored.  Since `ptr` is a local variable (just like `int val`, except its type is `int *`) it's also stored on stack and also have a large virtual address.
- One Line 10, we print `ptr` or the contents of the pointer `ptr`.  Since the contents of `ptr` is the return value from `malloc`, we know is memory stored on the heap and will have a small virtual address.
- After Line 10, we can update our visualization and add one variable to both our heap and our stack:

<table class="alloc">
  <tr><td class="startOfStack">Start of Stack (<code>0x700ffff</code>)</td><td></td></tr>

  <tr><td><code>val</code></td><td>← &val = <code>0x700fffc</code></td></tr>
  <tr><td><code>ptr</code> = 0x100000</td><td>← &ptr = <code>0x700fff4</code></td></tr>

  <tr><td class="s3" style="background-color: #ccc">...empty...</td><td></td></tr>

  <tr><td>storage for <code>ptr</code></td><td>← ptr = <code>0x100000</code></td></tr>

  <tr><td class="startOfHeap">Start of Heap (<code>0x100000</code>)</td><td></td></tr>
</table>


- Lines 13 and 14 are identical, growing our heap and our stack respectively:

<table class="alloc">
  <tr><td class="startOfStack">Start of Stack (<code>0x700ffff</code>)</td><td></td></tr>

  <tr><td><code>val</code></td><td>← &val = <code>0x700fffc</code></td></tr>
  <tr><td><code>ptr</code> = 0x100000</td><td>← &ptr = <code>0x700fff4</code></td></tr>
  <tr><td><code>ptr2</code> = 0x100004</td><td>← &ptr = <code>0x700ffec</code></td></tr>

  <tr><td class="s3" style="background-color: #ccc">...empty...</td><td></td></tr>

  <tr><td>storage for <code>ptr2</code></td><td>← ptr2 = <code>0x100004</code></td></tr>
  <tr><td>storage for <code>ptr</code></td><td>← ptr = <code>0x100000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0x100000</code>)</td><td></td></tr>
</table>


# Managing Heap Memory

One of the greatest things we can control as a systems designer is how to **most effectively make use of the heap memory**.  Throughout the execution of any process, there may be thousands or millions of calls to `malloc` and `free`:

- `malloc` allocates memory to the heap to the program for it's use,
- `free` releases the memory from use back to the heap (and can be reused by a future call to `malloc`)

We can explore a program that makes just calls to `malloc` and `free`:

```
int *a = malloc(4096);  /* Line 5 */
printf("a = %p\n", a);
free(a);
  
int *b = malloc(4096);  /* Line 9 */
printf("b = %p\n", b);

int *c = malloc(4096);  /* Line 12 */
printf("c = %p\n", c);
  
int *d = malloc(4096);  /* Line 15 */
printf("d = %p\n", d);

free(b);  /* Line 18 */
free(c);

int *e = malloc(5000);  /* Line 21 */
printf("e = %p\n", e);

int *g = malloc(10);  /* Line 24 */
printf("g = %p\n", g);

int *g = malloc(10);  /* Line 27 */
printf("g = %p\n", g);
```

## Pass #1: Allocation without Re-using Memory

Let's first explore this program **without** re-using any free'd memory:

- Lines 5-7 allocates 4,096 bytes of heap memory for `a` (and immediately frees it):

<table class="alloc">
  <tr><td class="free">4096 B (FREE)</td><td>← a = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Lines 9-16 allocates 4,096 bytes for `b`, `c`, and `d`:

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf3000</code></td></tr>
  <tr><td class="">4096 B</td><td>← c = <code>0xf2000</code></td></tr>
  <tr><td class="">4096 B</td><td>← b = <code>0xf1000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← a = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Lines 18 and 19 frees `b` and `c`:

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf3000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← c = <code>0xf2000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← b = <code>0xf1000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← a = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Lines 21-28 allocates 5,000 bytes (`e`) and 10 bytes for `f` and `g`:

<table class="alloc">
  <tr><td class="s0_5">10 B</td><td>← g = <code>0xf5392</code></td></tr>
  <tr><td class="s0_5">10 B</td><td>← f = <code>0xf5388</code></td></tr>
  <tr><td class="">5000 B</td><td>← e = <code>0xf4000</code></td></tr>
  <tr><td class="">4096 B</td><td>← d = <code>0xf3000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← c = <code>0xf2000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← b = <code>0xf1000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← a = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

**Question**: Our current pass uses (4,096 * 4) + 5000 + 10 + 10 total bytes of heap memory -- can we do better?


## Pass #2: Allocation with Simple Re-Use of Memory

Suppose we want to re-use memory **as optimally as possible when the allocation is made**.  Running the **exact same program**:

- We begin with Lines 5-7 again, allocating and freeing memory:

```
int *a = malloc(4096);  /* Line 5 */
printf("a = %p\n", a);
free(a);
```

<table class="alloc">
  <tr><td class="free">4096 B (FREE)</td><td>← a = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>


- Next, when we run Lines 9-10, we can now **re-use** the memory previously freed by `a`:

```
int *b = malloc(4096);  /* Line 9 */
printf("b = %p\n", b);
```

<table class="alloc">
  <tr><td class="">4096 B</td><td>← b = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>


- Lines 12-16 require new heap memory, so we are required to grow our heap:

```
int *c = malloc(4096);  /* Line 12 */
printf("c = %p\n", c);
  
int *d = malloc(4096);  /* Line 15 */
printf("d = %p\n", d);
```

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf2000</code></td></tr>
  <tr><td class="">4096 B</td><td>← c = <code>0xf1000</code></td></tr>
  <tr><td class="">4096 B</td><td>← b = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Lines 18-19 makes calls to `free` to free memory used by `b` and `c`:

```
free(b);  /* Line 18 */
free(c);
```

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf2000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← c = <code>0xf1000</code></td></tr>
  <tr><td class="free">4096 B (FREE)</td><td>← b = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Since we are being **optimal** in our use of memory, we can **coalesce** the two free blocks together that are sequential since we really have one large hole of 8192 B:

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf2000</code></td></tr>
  <tr><td class="s2 free">8192 B (FREE)</td><td>← b = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>

- Line 21 now request 5,000 bytes of heap memory -- we can **split** the 8,192 byte block and use 5,000 of to satisfy the request for memory pointed to by `e`:

```
int *e = malloc(5000);  /* Line 21 */
printf("e = %p\n", e);
```


<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf2000</code></td></tr>
  <tr><td class="free">3192 B (FREE)</td><td></td></tr>
  <tr><td class="">5000 B</td><td>← e = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>


- Finally, the system can allocate the last two blocks of memory:

```
int *g = malloc(10);  /* Line 24 */
printf("g = %p\n", g);

int *h = malloc(10);  /* Line 27 */
printf("h = %p\n", h);
```

<table class="alloc">
  <tr><td class="">4096 B</td><td>← d = <code>0xf2000</code></td></tr>
  <tr><td class="free">3172 B (FREE)</td><td></td></tr>
  <tr><td class="s0_5">10 B</td><td>← h = <code>0xf1392</code></td></tr>
  <tr><td class="s0_5">10 B</td><td>← g = <code>0xf1388</code></td></tr>
  <tr><td class="">5000 B</td><td>← e = <code>0xf0000</code></td></tr>
  <tr><td class="startOfHeap">Start of Heap (<code>0xf0000</code>)</td><td></td></tr>
</table>


# Fragmentation in Memory

In the very last example, you saw the continual creation of **holes of free space in the memory**.  Once the system gives memory -- a virtual address -- for a process to use the address **CANNOT be changed** and must be maintained on the heap at the current location.  The only way that the system can re-use that virtual address in the process is:

1. The code calls `free` to free up access to the memory,
2. The code calls `realloc`, which is used to change the size of the allocation, and allows us to return a enw location for the memory

Due to this, over a series of hundreds to millions of allocations and frees, memory will become filled with holes of free space.  Therefore, we want to develop strategies for deciding **which free block should used for a request, if multiple free blocks are available**.

[Next: Heap Memory Allocation: First-fit and Best-fit Approaches](heapMemoryAllocation.html)






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

.alloc tr td.startOfStack:nth-of-type(1) {
  border-width: 0px;
  border-bottom: solid 2px black;
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
