---
title: Multi-threaded Merge Sort

date: 2021-03-01
due: 2021-03-08 23:59:59

points: 25

number: 4
published: false
---

# Overview

Recently, you have learned about the POSIX thread library, pthread, which allows a user to setup and run multiple threads of execution within a single process. In this MP, you will program a basic multithreaded merge sort algorithm using pthreads.

To help you with input data sets, we have provided a program that will generate random numbers within a sequence. Running the command `./gen --min 0 --max 50 --seed 0 25` will always generate the same data set, printed below:

```
10 49 9 34 32 37 37 48 45 19 5 31 20 19 29 22 45 30 40 31 35 8 36 39 14
```

As input to this MP, you will be given a single command line argument: the number of times that the data should be split up as part of the merge sort. For example, a `segment_count` of 5 indicates that the data should be split into 5 (roughly) equal-sized segments. If we consider running the MP with a `segment_count` of 5, the data will be split in the following way:

```
[0]: 10 49 9 34 32
[1]: 37 37 48 45 19
[2]: 5 31 20 19 29
[3]: 22 45 30 40 31
[4]: 35 8 36 39 14
```

The first part of the merge sort requires you to sort each individual split in parallel. In our example, this means that you will launch five threads -- one for each of the splits -- and each thread must sort only their segment of the input. (You don't have to write your own sort here; in fact, you should use the C command `qsort()` to run a really great quick sort.)

```
[0]: 9 10 32 34 49
[1]: 19 37 37 45 48
[2]: 5 19 20 29 31
[3]: 22 30 31 40 45
[4]: 8 14 35 36 39
```


Next, the merge sort will take adjoining pairs of splits and merge them together in parallel. This will be done in rounds, where each round, a number of parallel merges take place until there is only a single, completely sorted list remaining. (Remember, merging is an O(n) operation **NOT** O(n*lg(n))... so you **cannot** call qsort() here.)

```
[0] + [1]: 9 10 19 32 34 37 37 45 48 49	
[2] + [3]: 5 19 20 22 29 30 31 31 40 45	
[4]: 8 14 35 36 39
```


The second round consists of only one merge:

```
[0] + [1] + [2] + [3]: 5 9 10 19 19 20 22 29 30 31 31 32 34 37 37 40 45 45 48 49	
[4]: 8 14 35 36 39
```


And the final round also only consists of one merge:

```
[0] + [1] + [2] + [3] + [4]: 5 8 9 10 14 19 19 20 22 29 30 31 31 32 34 35 36 37 37 39 40 45 45 48 49
```


In this MP, you must always start with the "left most" split and merge it with its neighbor. As such, the "right most" split will often be the last to be merged in. To let us know what you're doing, you will be required to output a few different print statements. For this example, the output of the program would actually look like:

```
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Merged 5 and 5 elements with 0 duplicates.
Merged 5 and 5 elements with 1 duplicates.
Merged 10 and 10 elements with 2 duplicates.
Merged 20 and 5 elements with 0 duplicates.
5
8
9
[...]
```


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp4 -m "Merging initial files"
```



## Tasks

You are given an empty program, msort.c, where all the code for this MP should be contained. To complete this MP, you will need to do the following tasks:


### Task 1: Reading Numbers

Read the sequence of input numbers from stdin. There will be one number per line, in the exact format that is outputted by `./gen`.


### Task 2: Segment Count from `argv`

Find the number of segments, `segment_count` from above, by reading the command-line argument of `./msort`. For example, if `segment_count` is 12, the command line will be `./msort 12`.


### Task 3: Dividing Up the Input

Divide up the input into `segment_count` segments such that each segment is equal-sized (eg: 25 input numbers / 5 segments = 5 numbers / segment). If you are unable to achieve equal-sized segments, you should ensure that the first `segment_count - 1` segments are equal sized and the last segment is smaller than the first `segment_count - 1` segments.

If you have input_ct input numbers and segment_count segments, you can find the size of each of the first segment_count - 1 segments with the following code:

```
int values_per_segment; /**< Maximum number of values per segment. */

if (input_ct % segment_count == 0)
    values_per_segment = input_ct / segment_count;
else
    values_per_segment = (input_ct / segment_count) + 1;
```


### Task 4: Running `qsort()`

Launch one thread per segment to sort each segment using qsort(). Your program must block until all threads finish sorting.

After the threads finish the qsort() operation, you should print out a status line indicating the number of elements sorted. This should be printed in your sorter function, printed to stderr, and printed as the exact format shown below:

```
fprintf(stderr, "Sorted %d elements.\n", count);
```


### Task 5: Parallel Merge Sort

Launch several rounds of threads to merge the sorted data segments. Each round must block until all threads in the current round finishes before advancing.

The threads must be merged in a specific order, where the segment at the beginning of the data set is merged with the next segment in the data set. That is `segment[0]` must be merged with `segment[1]`. This pattern should follow such that `segment[i]` is merged with `segment[i + 1]` for all even values of `i`. If there is an odd number of segments, you should not merge the left over segment in the current round (simply advance it to the next round of merging).

After each round, you will have `(ct / 2)` or `(ct / 2) + 1` segments remaining, where ct it the number of segments you had the previous round. You must continue the merging process until only 1 segment remains.

In merge sort, the merge operation MUST NOT be an O(n * lg(n)) operation. Instead, think of how you can merge two sorted lists in O(n) time. While merging the two lists, you must also keep track of how many duplicates you've seen between the two lists. (You should count a duplicate ONLY when you find the same number in both lists -- not if your list contains the same number multiple times. Count each time you see a duplicate number in both lists, even if that means the same number is counted multiple times.)

To count duplicates, there are several different ways to do so that may get you different results:
- When you find a duplicate in both sorted lists, you advance the "left" list,
- When you find a duplicate in both sorted lists, you advance the "right" list,
- When you find a duplicate in both sorted lists you look forward and advance the list that has the duplicated number repeated in the list (if that's the case of either of the lists).

...any, and all, of these methods will result in a correct solution so long as your program is consistent. In the output given in this document, we made the choice to always advance the "left" list. If you want to test your program aganist the output given here, you should also always advance the "left" list.

When the merging has completed, the merging thread should print out a status line. As with the sorted thread, this line should be printed to stderr, printed in your merge function, and printed in the exact format shown below:

```
fprintf(stderr, "Merged %d and %d elements with %d duplicates.\n", count1, count2, dupes);
```


### Task 6: Sorted Output

Once you have a single sorted list, print out the list, one number per line, to stdout. This can be done by `printf("%d\n", value);`.



## Compiling and Running

As always, compile using:

```
$ make clean
$ make
```

To run this program, you will need to generate input data using `./gen`. You can use `./gen` to generate a random set of numbers by using, for example:
```
./gen 100 >file.txt
```
...to generate 100 numbers.


You can also use `./gen` to generate numbers within a range. For example:
```
./gen --min 0 --max 100 100 >file.txt
```
...will generate 100 random numbers in the range of [0, 100]. Since the numbers are random, there will almost certainly be duplicates.

You can specify a specific random sequence by supplying a seed. The data set that was generated for the introduction of this MP was done by on an EWS VM:
```
./gen --min 0 --max 50 --seed 0 25 >example1.txt
```

To run your program with a given input, supply the number of segments and the input file:
```
./msort 5 <example1.txt
```

If you generated the same example1.txt as we did, the full output of the command above should be:
```
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Sorted 5 elements.
Merged 5 and 5 elements with 1 duplicates.
Merged 5 and 5 elements with 0 duplicates.
Merged 10 and 10 elements with 2 duplicates.
Merged 20 and 5 elements with 0 duplicates.
5
8
9
10
14
19
19
20
22
29
30
31
31
32
34
35
36
37
37
39
40
45
45
48
49
```

### Testing with a large dataset

You should make sure to test your MP with large data sets as well. As an example, example2.txt can be generated by:

```
./gen --min 0 --max 1234567 --seed 0 1234567 >example2.txt
```

Since you will sorting over a million lines, it may be useful to write the sorted output to a file (instead of showing it to the user). Running your MP with the `exmaple2.txt` dataset:
```
./msort 9 <example2.txt >example2-sorted.txt
```
...should result in the following output:
```
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137175 elements.
Sorted 137167 elements.
Sorted 137175 elements.
Merged 137175 and 137175 elements with 14231 duplicates.
Merged 137175 and 137175 elements with 14420 duplicates.
Merged 137175 and 137175 elements with 14308 duplicates.
Merged 137175 and 137175 elements with 14284 duplicates.
Merged 274350 and 274350 elements with 54702 duplicates.
Merged 274350 and 274350 elements with 54710 duplicates.
Merged 548700 and 548700 elements with 197491 duplicates.
Merged 1097400 and 137167 elements with 115132 duplicates.
```


### Verifying Speedup

Finally, since we're using threads, we should see a speedup between using a single segment (where all the work is done by a single thread) and using many segments. This should be most clear in really large test cases, so we will generate a third example:
```
./gen --min 0 --max 12345678 --seed 0 12345678 >example3.txt
```

We can test the time our program takes to run by using the Linux `time` command.

With one large segment:
```
$ time ./msort 1 <example3.txt >/dev/null
Sorted 12345678 elements.

real 0m7.955s
user 0m7.879s
sys 0m0.069s
```

With eight smaller segments:
```
$ time ./msort 8 <example3.txt >/dev/null
Sorted 1543210 elements.
Sorted 1543208 elements.
Sorted 1543210 elements.
Sorted 1543210 elements.
Sorted 1543210 elements.
Sorted 1543210 elements.
Sorted 1543210 elements.
Sorted 1543210 elements.
Merged 1543210 and 1543210 elements with 181375 duplicates.
Merged 1543210 and 1543208 elements with 181771 duplicates.
Merged 1543210 and 1543210 elements with 181149 duplicates.
Merged 1543210 and 1543210 elements with 181261 duplicates.
Merged 3086420 and 3086418 elements with 680333 duplicates.
Merged 3086420 and 3086420 elements with 681849 duplicates.
Merged 6172840 and 6172838 elements with 2431211 duplicates.

real 0m4.702s
user 0m8.204s
sys 0m0.145s
```


## Submit

When you have completed your program, double-check your code works and submit it:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)