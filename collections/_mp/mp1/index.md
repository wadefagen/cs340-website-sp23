---
title: Best Believe I'm Still 💎💎💎
image: mp/mp1/img.png

date: 2023-01-24
due: 2023-01-31 23:59:59

points: 40

number: 1
published: true
---

# Overview

MP1 in CS 340 is all about getting conformable with memory allocation, pointers, and character encodings in C.  You will complete several different sects of functions to build up your C programming skills.

- You will explore some key differences between C and C++ programs.
- You will create data structures in C.
- You will work at the bit- and byte-level to manipulate multi-byte UTF-8 characters in C-strings.
- You will read files and manipulate data from files in memory.
- You will share with us your favorite emoji! 🎉


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp1 --allow-unrelated-histories -m "Merging release repository"
```


## Machine Problem

Throughout this MP, you will be working with emojis!  You will begin with some simple functions and then advance to building a small, expandable emoji translator!


### Part 1: Simple Emoji Functions 🎉

In `emoji.c`, you will find your final five functions where you will combine everything you know into working with UTF-8 encoded strings.

- In `emoji_favorite`, we just want to know your favorite emoji.  

  There are many ways to specify byte sequences in C code.  For example, 💙 is the emoji `U+1F499`.  However, C does not understand the formal UTF-8 notation.  Instead, you need to specify it either by:

  - Using the values for each byte.  For 💙 (U+1F499), the byte sequence is `0xF0 0x9F 0x92 0x99`.
  - The **bytes** can be set one by one as numbers in hex form (ex: `str[0] = 0xF0`) or in decimal form (ex: `str[0] = 240`).
  - The **bytes** can be set one by one as characters, which the character escape `'\x##'`.  Even though this is four bytes, when read by C it will only be one byte in memory.  The first byte is set with `str[0] = '\xF0'`.
  - A **string** can be created using multiple individual character escapes (ex: `str = "\xF0\x9F\x92\x99"`).  Even though the example string appears to be a 16 byte string, the string contains only four bytes.
  - A **string** can be created using the character escape `\u` or `\U` (ex: `str = "\U0001F499"`). Note: `\u` escape sequence takes 4 hexadecimal digits i.e. \uhhhh where h is a hexadecimal digit. `\U` escape sequence takes 8 hexadecimal digits i.e. \Uhhhhhhhh where h is a hexadecimal digit.
  - A **string** can be created using the emoji itself (ex: `str = "💙"`).

  You will want to use different forms at different times.


- In `emoji_count`, you will count the emojis in a provided string.

  For the purpose of this MP, we will consider an emoji to be anything in the inclusive range `U+1F000` - `U+1FAFF`.  *(There are some invalid characters in this range and a few early emojis outside of this range, but we want to keep it simple.  Feel free to be more accurate, we will only test your code on real emoji within this range and your solution won't break if you program a more correct solution.)*


- In `emoji_random_alloc`, you will generate a random emoji each time the function is called!  The [C documentation for rand](http://www.cplusplus.com/reference/cstdlib/rand/) outlines how to create a random integer in C, which you'll need to use for your emoji.  Make sure that you return a different emoji each time `emoji_random_alloc` is called.

  Just like in `emoji_count`, you can assume the inclusive range `U+1F000` - `U+1FAFF` or be more specific.


- In `emoji_invertChar`, you will be inverting an emoji. You are provided with a string in this function. You have to invert only the first character of this string (which may be up to 4 bytes) only if it is an emoji. The inversion is a **semantic**, so you should invert the meaning of the emoji (**NOT** flipping or "inverting" the bits). You will have to invert "😊" (`U+1F60A`) to some sort of sad face (your choice!). In addition to "😊", you need to invert at least five other emojis of your choice (and it's okay to do more than just five).


- In `emoji_invertAll`, you will invert all the characters in the string using your `emoji_invertChar` function.


- In `emoji_invertFile_alloc`, you will read in the contents of a provided file name and invert all the emojis in that file, and return the inverted string.


### Part 2: An Emoji Translator

Sometimes, we may write articles (or entire books) with emojis!  For the second part of this MP, you will begin to work with basic C data structures to create a "emoji translation" utility.

In `emoji-translate.c`, you will find four functions:

- `void emoji_init(emoji_t *emoji)` is called to initialize an `emoji` object.

- `void emoji_add_translation(emoji_t *emoji, const unsigned char *source, const unsigned char *translation)` adds a translation to the `emoji` object.  For example, "💎" might translate to "diamond".

- `const unsigned char *emoji_translate_file_alloc(emoji_t *emoji, const char *fileName)` must translate the contents of the file specified by `fileName` using all translation rules added so far.  When matching rules, always choose the **longest matching rule** based on the length of the `source`.

  - For example, only the rule "💎" => "diamond" has been added: `"💎s are forever!"` would translate to `"diamonds are forever!"`.
  - If the rule "💎" => "diamond" and "💎💎💎" => "Bejeweled", `"Best Believe I'm Still 💎💎💎"` would translate to `"Best Believe I'm Still Bejeweled"` since the rule using two faces is the longer than one face.

- `void emoji_destroy(emoji_t *emoji)` is called to destroy the `emoji` object and any memory allocated by your emoji library.

#### Example Usage

```
emoji_t emoji;
emoji_init(&emoji);

emoji_add_translation(&emoji, "🧡", "heart");

// The file on disk contains: "I 🧡💙 Illinois!"
unsigned char *translation = emoji_translate_file_alloc(&emoji, "tests/txt/simple.txt");

// Translation Output: "I heart💙 Illinois!"
printf("%s\n");

emoji_destroy(&emoji);
```


### Part 3: Memory Correctness

For a program to run correctly and consistently across many machines, the program must have **no memory errors**.  Since every CPU is slightly different, a memory error may result in a program running perfectly fine on one system (perhaps the system initialized the heap memory to `0x00` for you) while may seg fault on another system (perhaps the other system has random/garbage values in the heap memory).

To test for memory correctness, we use commonly used tool called `valgrind`.  The magic of `valgrind` is that it will track every memory access your program makes and report:

- Memory that was allocated (via `malloc`) but not released back the system (via `free`),
- Use of previously allocated memory (accessing memory you `free`'d),
- Any memory access beyond either your allocated region (for heap memory) or beyond your stack (for stack memory),
- ...and many, many more types of memory misuse.

You will earn 67% of the credit for the MP for completing the MP.  The other 33% of your grade can only be earned if your program passes all of the test cases and runs "valgrind clean".  To run **"valgrind clean"**, your must:

1. Compile all test cases on the command line using `make test`,
2. Run all test cases using `valgrind --leak-check=full --show-leak-kinds=all ./test`,
3. Your valgrind output must report BOTH of the following:
    - `All heap blocks were freed -- no leaks are possible`, showing you free'd all memory before existing, **AND**
    - `ERROR SUMMARY: 0 errors from 0 contexts`, showing you made no memory errors.

**Until you run valgrind clean, you should expect differences between the GitHub Action grader and your local run.**


<div class="alert alert-info">
<h4>macOS Specific Information</h4>

Sadly, <code>valgrind</code> support on macOS is limited.  However, Docker provides a Linux environment on macOS:

<pre>
# Build a light-weight docker:
docker build -t cs340  .

# Run `make clean` to clear any old compiled code that may be compiled outside of docker:
docker run --rm -it -v `pwd`:/mp1 cs340 "make clean"

# Run `make test` to compile the test case inside of docker:
docker run --rm -it -v `pwd`:/mp1 cs340 "make test"

# Run `valgrind ./test` to run the test case with valgrind inside of docker:
docker run --rm -it -v `pwd`:/mp1 cs340 "valgrind ./test"
</pre>
</div>


## Modifiable Files

In your solution, you must only modify the following files.  Modifications of other files may break things:

- `emoji.c`
- `emoji.h`
- `emoji-translate.c`
- `emoji-translate.h`


## Testing Your Program

- To compile the test suite, run `make test`.
- To run your code, run `./test` and everything should pass! 🎉



## Submission and Grading

### Testing Suite

When you are finished working on the MP, you can run a **local copy of the same test suite that you will use for grading**.  To run the test suite:

- To compile the test suite, run `make test`.
- To run your code, run `valgrind ./test` and everything should pass and run "valgrind clean"! 🎉


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
- Choose "mp1 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!


### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  26.67 (scaled from 100 in the GitHub Action) |
| valgrind    |  13.33 (scaled from 50 in the GitHub Action) |
