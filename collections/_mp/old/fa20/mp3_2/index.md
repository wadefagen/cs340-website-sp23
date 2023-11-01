---
title: malloc, refined

date: 2020-09-29
due: 2020-10-06 08:00:00

points: 25

number: 3.2
published: false
---

# Overview

This week, we will refine your memory allocator!


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp3-part2 -m "Merging initial files"
```


## Note for Mac OS X Users

**For this MP only** and **for OS X users only**, you will need to program on a Linux system due to the differences in how Linux and OS X handles process/memory injection and the `sbrk()` library call.  The `linux.ews.illinois.edu` server is available for you to remotely connect to to work on a Linux environment to run the MP.  (These machine are already set up and ready to go!)

- If you are comfortable with the terminal, you can use `ssh` to access `linux.ews.illinois.edu`.
- If you want a graphical desktop, [EngrIT has details on going FastX to connect on their FastX guide here](https://answers.uillinois.edu/illinois.engineering/page.php?id=81727)


## Machine Problem

To begin this MP, copy/paste all of your work on `alloc.c` from MP3 over to `mp3-part2`.


### Adding Print Statements

I highly encourage you to add a `print_heap` function to your code, if you have not done so already.  [Our "Building Malloc" has code to print your heap as part of malloc.]({{site.baseurl}}/notes/buildingMalloc.html)

When you want to run your program without the print output, you can simple comment out the contents of your `print_heap`.


### Running Samples

Several sample programs are provided and can be built using `make`.  Specifically, six samples are part of `mp3-part2`.  In each of these descriptions, the below values are the MAX values expected when running `./mstats` (ex: `./mstats ./samples/sample1`).

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

    - If you are not reusing blocks, `sample6` will run quickly but will use over 300,000 bytes of memory.
    - If you are re-using blocks, `sample6` should run quickly when a linked list of free nodes are maintained (ex: `TIME` less than about 0.1s).
    - If you are re-using blocks but are **NOT** maintaining a linked list of free nodes, `sample6` will run slower (`TIME` greater than 1s).
    - (The speed of your system will impact the speed, but there will be a significant speed difference.)<br><br>



## Grading

Complete **memory coalescing** and **block splitting** (`sample1` - `sample4`) for full credit.  Earn +5 points for implementing free lists **AND** passing all five testers (`tester1` - `tester5`).


## Submit

When you have completed your program, double-check all four functions run without errors and gets the result your expect.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-fa20](https://github-dev.cs.illinois.edu/cs240-fa20)
