---
title: Alloc Too Well

date: 2023-02-07
due: 2023-02-21 23:59:59

points: 80
jklmnop: ../mp3-jklmnop/

number: 3
published: true
---

# Overview

The very best way to understand an interface is to implement it yourself.  This MP is as simple as it gets: implement `calloc`, `malloc`, `realloc`, and `free` in `alloc.c`.  That's it! :)


## Detailed Overview

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

In this MP:

- You will implement the full suite of allocation functions `malloc`, `calloc`, `free`, and `realloc` using only the `sbrk` system call.
- You will understand foundational ideas on how heap memory is managed.
- You will gain an enormous amount of experience with pointers.
- You will have completed one of the longest-running MPs in the history of the University of Illinois! :)


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp3 --allow-unrelated-histories -m "Merging initial files"
```


<div class="alert alert-info">
<h4>New Docker Setup (required on macOS)</h4>

MP3 unfortunately does not work natively on MacOS, since it does not allow us to load your library so that it can actually be called instead of C's version of <tt>malloc</tt>. If you are a Mac user, you will need to run this MP inside of Docker.
<br><br>
For this MP (and future MPs), <b>we have made a change to docker that will streamline it's use</b>.  Instead of running each command in docker, the `docker run` will launch a bash shell for your use.
<br><br>
The first time only, you need to build the docker when you're in your <b>mp3 directory</b>:
<pre>
docker build -t cs340  .
</pre>

After the the container is built, run the container <b>in your mp3 directory</b>.  This new container now provides you a bash prompt.
<pre>
docker run --rm -it -v "`pwd`:/mp3" cs340
</pre>

Once you're into the container, you will need to `cd mp3` and then you can run the terminal commands directly! :)
<pre>
cd mp3
make clean
make
make test
./test
</pre>

</div>

## Machine Problem

Implement the `malloc`, `free`, `calloc` and `realloc` functions described in the Linux documentation manual [https://man7.org/linux/man-pages/man3/free.3.html](https://man7.org/linux/man-pages/man3/free.3.html) in `alloc.c` by using the `sbrk` system call.

You implementation must make some re-use of free'd memory by using block splitting, free memory coalescing, and free lists (all described below).

You have two weeks to fully complete this MP, but you must complete the initial work in the first week:

- Week #1 Deadline (Feb. 14 by 11:59pm): You must complete parts 1-4.  This implements an efficient memory allocator.
- Week #2 Deadline (Feb. 21 by 11:59pm): You must complete the advanced tests (Part 5).


## Modifiable Files

In your solution, you must only modify the following file.  Modifications of other files may break things:

- `alloc.c`


## Part 1: Implementing Basic Memory Allocator

Not sure how to get started or test this program?  [We've made a "Building Malloc" guide to help you get started!]({{site.baseurl}}/notes/buildingMalloc.html)

### Running Your Program

- In your terminal, type `make` to compile your `alloc.c` code.
- Run your program with a simple allocator using `./mreplace tests/samples_exe/00-simple`
- You can inspect what this code is doing by taking a look at `tests/samples/00-simple.c` to help you debug.

### Testing Your Program

- In your terminal, type `make test` to compile the test suite.
- Run `./test "[part=1]"` to run the tests that have been tagged with `"[part=1]"`.
- To evaluate your `malloc` implementation on a specific `sample1` file, run `./mstats tests/samples_exe/sample1`

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


## Memory Allocator Design Ideas

At this point, there are a lot of ways to be creative to make a good memory allocator. As a minimum, we need to make sure to reuse the free memory when available. Additionally, we need to optimize our use of heap memory in a few ways. Here’s a few ideas:

1. **[Block Splitting]**: When memory is re-used, it is very easy to simply mark the block isUsed to be 1 again even if the block is not a perfect fit. Could you split a block if there’s enough space left over to leave some free space for a future allocation?

	<i>With successful block splitting, you should see the r1 allocation in the sample1 program split one of the large free blocks into a block of size 10 for r1 and a large remaining free block for future allocations.</i>

2. **[Memory Coalescing]**: Currently, when memory is freed, two blocks of free memory may appear next to one-another. In our example, this happened with a and b. Could you combine the blocks together to create one larger block?

	<i>With successful memory coalescing, you should see the free block of a and b in the sample1 program join together for a free block of over 500 bytes.</i>

3. **[Free Lists]**: Right now, we have to traverse both used and free blocks when walking through our memory. Could we create a linked list or other data structure to allow us to traverse only the free blocks?

	<i>With successful free lists, your list of memory should skip over all blocks that are in use and list only the free blocks.</i>



## Part 2: Block Splitting

Implement **block splitting** in your memory allocator.



## Part 3: Memory Coalescing

Implement **free memory coalescing** in your memory allocator.



## Part 4: Free Lists

Implement **free lists** in your memory allocator.



## Part 5: Advanced Testers

### Running Your Program
- Run `./test "[part=5]"` to run the tests related to advanced testers. The source files for these advanced testers and their purposes are as follows:
  - mp0-gif - MP0, but with your malloc! :)
  - tester1.c - repeatedly `malloc()`s, assigns, and `free()`s a single int
  - tester2.c - several ints `malloc()`d and assigned in succession, all `free()`d at the end
  - tester3.c - several `malloc()`s, `memsets()`, and `free()`s of a megabyte of data at a time
  - tester4.c - `malloc()` a large amount of memory, recursively `realloc()` into smaller chunks, coalesce back together
  - tester5.c - repeatedly `calloc()`s, assigns, and `free()`s a single int

### Passing Advanced Tests

The `tests/testers` directory contains many advanced tests that do really stretch your memory allocator in terms of the number of requests, the sizes of the requests, and more.


## Submission and Grading


### Submission

Once you have locally passed all the tests, you will need to submit and grade your code.  First commit using the standard git commands:

```
git add -A
git commit -m "MP submission"
git push
```


### Grading

The initial grading is done via a manual GitHub Action.  You **MUST** complete this step before the deadline to earn any points for the MP:

- Navigate to your repository on [https://github.com/cs340-illinois](https://github.com/cs340-illinois).
- Click on the "Action" Tab
- Choose "mp3 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The MP is worth a total of 80 points, and the points are split in the following way:

Week 1 Tasks:

- Implement a basic memory allocator, 25% (`[part=1]`)
- Implement **block splitting**, 25%  (`[part=2]`)
- Implement **memory coalescing**, 25%  (`[part=3]`)
- Implement **free lists**, 25%  (`[part=4]`)

Week 2 Tasks:

- Pass all Week 1 tasks
- Pass all advanced tests (`[part=5]`)
