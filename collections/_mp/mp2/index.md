---
title: I Know Places (We Won't Be Found)

date: 2023-01-31
due: 2023-02-07 23:59:59

points: 40

number: 2
published: true
---

# Overview

Your second C program explores the implementation and use of a widely-used technical specification.  You will implement a small part of the PNG technical specification and use your implementation to hide a GIF within a PNG file.

- You will implement part of a published technical specification, *the Portable Network Graphics (PNG) Specification (Second Edition)*.
- You will read and write files with specification-defined binary data.
- You will explore cross-platform byte ordering (endianness).
- You will use your knowledge of the PNG specification to hide a GIF inside of a PNG.


<div class="row text-center">
  <div class="col-4">
    <img class="img-fluid" style="border: solid 1px black" src="340.png"><br>
    File: <code>340.png</code>
  </div>
  <div class="col-4">
    <img class="img-fluid" style="border: solid 1px black" src="natalia.png"><br>
    File: <code>sample/natalia.png</code>
  </div>
  <div class="col-4">
    <img class="img-fluid" style="border: solid 1px black" src="waf.png"><br>
    File: <code>sample/waf.png</code>
  </div>
</div>

<div class="text-center mt-3">
  <i>All of these images look the same, but two have a hidden GIF inside of them!</i>
</div>


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp2 --allow-unrelated-histories  -m "Merging initial files"
```


## Part 1: Implementing the PNG File Format

The World Wide Web Consortium (W3C) is "an international community where \[many\] work together to develop Web standards".  One of the web standards that the W3C published and maintains is the ["Portable Network Graphics (PNG) Specification (Second Edition)"](https://www.w3.org/TR/2003/REC-PNG-20031110/).  This specification fully and completely defines how PNG image files work.

To complete Part 1 of this MP, you must complete a small PNG library that reads the "PNG signature" and "chunks" found in PNG files:

- Read the PNG specification to understand the "PNG signature" and "chunks".
    - *Note*: integer fields within PNG chunks (specifically the data length and CRC fields) are stored in network byte order, but modern systems store integers in memory in various different byte orders (see [Endianness](https://en.wikipedia.org/wiki/Endianness#Overview) on Wikipedia). Your code will need to convert between host byte order and network byte order formats when reading and writing integer fields of PNG chunks.
    - You may find functions like [`htons`, `ntohs`, `htonl`, `ntohl`](https://linux.die.net/man/3/ntohl) helpful for converting values between host and network byte order. 
- Implement `lib/png.c` (and the `struct` in `lib/png.h`) to complete Part 1.
- We have included `lib/crc.{c,h}` that defines `crc32(const void *data, size_t n_bytes, uint32_t* crc)`.  This function takes in a pointer to `data` of `n_bytes` and writes the 32-bit CRC value into the address provided by `crc`. 
    - Your library **must** calculate and write the CRC when writing PNG chunks.

The design on the PNG library is a **file-stream based library** where you need only to return the next chunk in the file.  Therefore, it is highly recommended you only read the data necessary to complete the function and read the next part of the file the next time a library function is called.

- For example, `PNG_open` requires only that you verify the "PNG signature".  It is recommended you do not read any additional data beyond the signature.
- Similarly, `PNG_read` requires you to return data about the next PNG chunk.  It is recommended you only read the data for one chunk when this function is called.
- You **can** create a data structure to store the full PNG in memory as soon as the file is open but this is not recommended.

You will need to open a file for reading or writing in C.  The following C library calls may be helpful:

- [`fopen` (reference)](http://www.cplusplus.com/reference/cstdio/fopen/), to open a new file.  Use `"r"` or `"r+"` as the second parameter to open the file for reading and `"w"` as the second parameter to open the file for writing.
- [`fwrite` (reference)](http://www.cplusplus.com/reference/cstdio/fwrite/), to write to an open file.
- [`fclose` (reference)](http://www.cplusplus.com/reference/cstdio/fclose/), to close an open file.


### Running Your Program

- In your terminal, type `make test` to compile the test suite.
- Run `./test "[part=1]"` to run the tests that have been tagged with `[part=1]` (covering this portion of the MP).


## Part 2: Understanding the Provided Code

Oh! You’re here in the future?! [Fill out this Google Form to finish the treasure hunt](https://forms.gle/apnsR1GARTczivxw9) (@illinois login required).

In this MP, we have provided two programs that are already complete:

1. A complete program that analyzes a PNG file, `png-analyze.c`.
2. A complete program that reads and rewrites the PNG file using the provided PNG library, `png-rewrite.c`.

The `png-analyze.c` and `png-rewrite.c` files use your library from Part 1.  *If your library is not complete, these programs will not run correctly!*  Here are several example runs of the programs that you should run to explore what the provided code does for you:

- Run `make` to compile,
- Run `./png-analyze 340.png` to view the chunks in `340.png`
- Run `./png-analyze sample/natalia.png` to view the chunks in `sample/natalia.png`
- Run `./png-analyze sample/waf.png` to view the chunks in `sample/waf.png`
- Notice that `sample/natalia.png` and `sample/waf.png` both have a `uiuc` chunk -- this is our hidden GIF file!  You will need to extract it from the PNG file.


## Part 3: Extract the Hidden `uiuc` Chunk

Complete `png-extractGIF.c` to extract the `uiuc` chunk from the PNG file specified in the command line (`argv[1]`) and save the data from the `uiuc` chunk into the GIF file specified in the command line (`argv[2]`).  You will almost certainly find the provided `png-analyze.c` as a great starting point for your code.

The hidden GIFs in the files within the `sample` folder are valid GIF and should be able to be opened by any program that can open images if extracted correctly.

### Testing Your Code

Once you have finished `png-extractGIF.c`, make sure to test it before moving on to Part 3!  To do so:

- Run `make` to compile,
- Run `./png-extractGIF sample/natalia.png natalia.gif` to extract the hidden GIF from Natalia's file, 
- Run `./png-extractGIF sample/waf.png waf.gif` to extract the hidden GIF from Wade's file,
- View `natalia.gif` and `waf.gif` to see the hidden GIFs you extracted.  (If they don't appear as valid GIFs, something went wrong in your extraction.)


Finally, you can also run the test suite we have provided:

- Run `./test "[part=3]"` to run the tests that have been tagged with `[part=3]`.


## Part 4: Hide Your Own GIF

Complete `png-hideGIF.c` to hide a GIF file within a `uiuc` chunk in a provided PNG file. The first command line argument (`argv[1]`) is the normal PNG file that needs to be modified to hide an GIF image and the second argument (`argv[2]`) is the GIF to be hidden.

Your `png-hideGIF.c` file will be very similar to the provided `png-rewrite.c` program except that you need to add an additional `PNGChunk` somewhere between `IHDR` and `IEND` (it cannot be before the `IHDR` chunk or after the `IEND` chunk, but it otherwise can be anywhere in between the two chunks).

### Share in #mp2-showcase

Share your PNG with hidden GIF in Discord in #mp2-showcase for +2 EC!  I'll look at all of them and we'll showcase the best of them in lecture! :)


### Testing Your Code

Once complete, make sure to test your program:

- Use `png-analyze` to make sure the hidden `uiuc` chunk is present,
- Use your `png-extractGIF` program to ensure you can extract the GIF you hide in the file,
- Finally, post your PNG file to the class Discord and share your hidden image with the all of us! :)

As a final check, run `./test` to ensure you pass all test cases.

### Memory Correctness

For full credit, your MP must run "valgrind clean".

- On Windows/WSL, you can directly run valgrind with `valgrind ./test`.
- On macOS, you can run valgrind via a docker container:

    * Only once, you need to build your docker container using: `docker build -t cs340 .`

    * Then, you can run valgrind with the following commands:

```
docker run --rm -it -v `pwd`:/mp2 cs340 "make clean"
docker run --rm -it -v `pwd`:/mp2 cs340 "make test"
docker run --rm -it -v `pwd`:/mp2 cs340 "valgrind --leak-check=full --show-leak-kinds=all ./test"
```


## Modifiable Files

In your solution, you must only modify the following files.  Modifications of other files may break things:

- `png-extractGIF.c`
- `png-hideGIF.c`
- `lib/png.c`
- `lib/png.h`

## Submission and Grading

<div class="alert alert-info">
<h4>Just Keep Letting Me Nerd Out Please!</h4>

This MP has a JKLMNOP.  Once you have completed the MP, you can nerd out with the PNG specification a little bit more in <a href="../mp2-jklmnop/">MP2's JKLMNOP</a> project.

</div>

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
- Choose "mp2 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  26.67 (scaled from 100 in the GitHub Action) |
| valgrind    |  13.33 (scaled from 50 in the GitHub Action) |
