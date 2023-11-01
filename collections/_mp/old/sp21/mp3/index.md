---
title: malloc

date: 2021-02-15
due: 2021-03-01 23:59:59

points: 50

number: 3
published: false
---

# Overview

Your third MP in CS 240 is writing your own memory allocator! You should write your implementations of `calloc`, `malloc`, `realloc`, and `free` in `alloc.c`.

Memory allocation seems like a mystery, but in actuality, we are making a wrapper around the system call [`sbrk`](http://linux.die.net/man/2/sbrk). Here's a really simple implementation of `malloc`:

```c
void *malloc(size_t size) {
    return sbrk(size);
}
```

As you can see, when we request `size` bytes of memory, we call `sbrk(size)` to increase the heap by `size` bytes. Then, we return a pointer to this memory, and we're done. Simple!

Here is our implementation of `free`:

```c
void free(void *ptr) {
}
```

This is a "correct" way to implement `free`. However, the obvious drawback with our implementation is that we can't reuse memory after we are done with it. Also, we have not checked for errors when we call `sbrk`, and we have not implemented `realloc` or `calloc`.

Despite all of this, this is still a "working" implementation of `malloc`. So, the job of `malloc` is not really to allocate memory, but to keep track of the memory we've allocated so that we can reuse it.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp3 -m "Merging initial files"
```


## Machine Problem

#### Writing `malloc`

Using [`sbrk`](http://linux.die.net/man/2/sbrk), write your `malloc` implementation! Your malloc should allocate a block of `size` bytes of memory, returning a pointer to the beginning of the block. The contents of the newly allocated block of memory are not initialized.

#### Writing `free`

Write your implementation of `free`! This function will take a pointer to a block of previously allocated memory and make it available again for future allocations.

#### Writing `calloc`

Write your implementation of `calloc`! This function will take in two parameters, `num` and `size`. It will allocate a block of memory for an array of `num` elements, each of them being `size` bytes long, and initialize all of its bits to zero.

#### Writing `realloc`

Write your implementation of `realloc`! This function will take in two parameters: a pointer to a previously allocated block of memory, and the desired size of the returned memory. You must return a pointer to a block of allocated memory of at least `size`, with this block of memory containing the same contents as the passed in pointer.

## Getting Started Guide

Not sure how to get started or test this program?  [We've made a "Building Malloc" guide to help you get started!]({{site.baseurl}}/notes/buildingMalloc.html)


## Testing Your Code

In order to run your solution on the testers, run `./mstats` with the tester you want. You __must__ do this, or your code will be run with the glibc implementation!

Example:

```
./mstats testers_exe/tester-1
Memory failed to allocate!
[mstats]: STATUS: FAILED=(256)
[mstats]: MAX: 0
[mstats]: AVG: 0.000000
[mstats]: TIME: 0.000000
```

Here is what some of the error codes you may encounter mean:

```
11: (SIGSEGV) Segmentation fault
15: (SIGTERM) Timed out
65, 66: Dynamic linking error
67: Failed to collect memory info
68: Exceeded memory limit
91: Data allocated outside of heap
92: Data allocated exceeds heap limit
```


### Adding Print Statements

I highly encourage you to add a `print_heap` function to your code, if you have not done so already.  [Our "Building Malloc" has code to print your heap as part of malloc and you just need to make it into a function.]({{site.baseurl}}/notes/buildingMalloc.html)

When you want to run your program without the print output, you can simple comment out the contents of your `print_heap` function.  You will find for all non-trivial tests the print statements will slow down your code far, far too much.


### Running Samples

Several sample programs are provided and can be built using `make`.  Specifically, six samples are part of `mp3`.  In each of these descriptions, the below values are the MAX values expected when running `./mstats` (ex: `./mstats ./samples/sample1`).

- `./samples/sample1` is the sample from [the "Building Malloc" guide.]({{site.baseurl}}/notes/buildingMalloc.html) and is a great sample to test block splitting.

    - **Without** block splitting, `sample1` should MAX around 1,600 bytes of memory.
    - With block splitting, `sample1` should MAX around 1,100 bytes of memory.<br><br>


- `./samples/sample2` is a great sample to debug memory coalescing.

    - **Without** memory coalescing, `sample2` should MAX around  9,000 bytes of memory.
    - With memory coalescing, `sample2` should MAX around 6,500 bytes of memory.<br><br>

- `./samples/sample3` tests memory coalescing in reverse order *(this one is trickier)*.

    - **Without** memory coalescing, `sample3` should MAX around 9,000 bytes of memory.
    - With memory coalescing, `sample3` should MAX around 6,500 bytes of memory.<br><br>

- `./samples/sample4` tests memory coalescing with extra free blocks around that cannot be coalesced.

    - **Without** memory coalescing, `sample4` should MAX around 11,000 bytes of memory.
    - With memory coalescing, `sample4` should MAX around 9,000 bytes of memory.<br><br>

- `./samples/sample5` tests a "free list" by having allocated blocks before/after the freed element.  This is a simple version of `sample6`.

- `./samples/sample6` tests your "free list" by having **LOTS** of allocated blocks before/after your free block.

    - If you are **NOT** reusing blocks, `sample6` will run quickly but will use over 3,000,000 bytes of memory.
    - If you are re-using blocks, `sample6` should run quickly when a linked list of free nodes are maintained (ex: `TIME` less than about 0.1s).
    - If you are re-using blocks but are **NOT** maintaining a linked list of free nodes, `sample6` will run slower (`TIME` greater than 1s).
    - (The speed of your system will impact the speed, but there will be a significant speed difference.)<br><br>



### Real Programs

Both `mstats` and `mreplace` can be used to launch "real" programs (not just the testers). For example:

```
# ignore the warning about an invalid terminal, if you get it
./mreplace /usr/bin/less alloc.c
```

or

```
./mstats /bin/ls
```

You can even run your version of MP2 using your `malloc`! For example:

```
./mstats ../mp2/png-extractGIF PATH_TO_PNG
```



## Grading

The MP is worth a total of 50 points, and the points are split in the following way:

- Implement a basic memory allocator, 10 points
- Implement **block splitting**, 15 points  (see `sample1`)
- Implement **memory coalescing**, 15 points  (see `sample{2,3,4}`)
- Implement **free lists**, 10 points  (see `sample{5,6}`)


### Extra Credit

You can earn extra credit in two different ways for this MP!

1. **Submitting Early**: We will grade the files you submit by Monday, Feb. 22 at 11:59pm for extra credit:

    - If your program is a working basic memory allocator, earn +1 EC point
    - If your program implements memory coalescing, earn +2 EC points
    - If your program implements block splitting, earn +2 EC points

2. **Passing Advanced Tests**: On your final submission (Mar. 1), we will run some advanced tests on your code:

    - The `testers` directory contains many advanced tests that do really mean things to your memory allocator in terms of the number of requests, the sizes of the requests, and more.  Earn +1 EC for each of `tester{1-5}` that successfully run using `./mstats` within 30 seconds.



## Submit

When you have completed your program, double-check all four functions run without errors and gets the result your expect.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)
