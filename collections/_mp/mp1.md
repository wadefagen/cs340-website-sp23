---
title: Emojis

date: 2022-01-20
due: 2022-01-31 23:59:59

points: 50

number: 1
published: false
---

# Overview

Your first MP in CS 240 is all about getting conformable with memory allocation, pointers, and character encodings in C.  You will complete several different sects of functions to build up your C programming skills.

- You will explore some key differences between C and C++ programs.
- You will create data structures in C.
- You will work at the bit- and byte-level to manipulate multi-byte UTF-8 characters in C-strings.
- You will read files and manipulate data from files in memory.
- You will share with us your favorite emoji! 🎉


## MP Overview Session

An MP Overview was held by Eunice on Monday, Jan. 24 at 11:00am.

<!--
- Zoom Link: [https://illinois.zoom.us/j/86189674505?pwd=MWQrNlE3VU9sNTBtbm9US2FHUS9Ldz09](https://illinois.zoom.us/j/86189674505?pwd=MWQrNlE3VU9sNTBtbm9US2FHUS9Ldz09)

- Complete the `start` test suite before the overview session for +1 EC.
-->

The recording can be found at [https://mediaspace.illinois.edu/media/t/1_dv86s3n1](https://mediaspace.illinois.edu/media/t/1_dv86s3n1). 

The slide can be found at [https://courses.grainger.illinois.edu/cs240/sp2022/static/mp-overviews/MP1 Overview Session.pdf]({{site.baseurl}}/static/mp-overviews/MP1 Overview Session.pdf). 


## Programming Environment

You will need to set up an environment to program C code.

- [Follow our guide to getting your programming environment set up]({{site.baseurl}}/notes/programming-environment.html).


## Initial Files

A new exciting thing has happened at Illinois -- the University now has an enterprise license to github.com, giving us access to all the fanciest of features on "real github.com"!  **This is likely different than what you have used in previous courses, as most courses use the UIUC github-dev!**

- [Follow our guide to getting your github.com set up]({{site.baseurl}}/resources/git/).

Once you have set up and navigated into your git repository, you will `fetch` the MP 1 starter files from the `release` repository and `merge` them into your repository using the following commands: 

```
git fetch release
git merge release/mp1 --allow-unrelated-histories -m "Merging release repository"
```


## Machine Problem

Throughout this MP, you will be working with emojis!  You will begin with some simple functions and then advance to building a small, expandable emoji translator!

- Read the [CS 240 MP policy]({{site.baseurl}}/pages/mp-policy.html) about having a collaborator who you can share code with, the expectations for grading, and all other course-wide policies related to MPs. 



### Part 1: Simple Emoji Functions 🎉

In `emoji.c`, you will find your final five functions where you will combine everything you know into working with UTF-8 encoded strings.

- In `emoji_favorite`, we just want to know your favorite emoji.  

  There are many ways to specify byte sequences in C code.  For example, 💙 is the emoji `U+1F499`.  However, C does not understand the formal UTF-8 notation.  Instead, you need to specify it either by:

  - Using the values for each byte.  For 💙 (U+1F499), the byte sequence is `0xF0 0x9F 0x92 0x99`.
  - The **bytes** can be set one by one as numbers in hex form (ex: `str[0] = 0xF0`) or in decimal form (ex: `str[0] = 240`).
  - The **bytes** can be set one by one as characters, which the character escape `'\x##'`.  Even though this is four bytes, when read by C it will only be one byte in memory.  The first byte is set with `str[0] = '\xF0'`.
  - A **string** can be created using multiple individual character escapes (ex: `str = "\xF0\x9F\x92\x99"`).  Even though the example string appears to be a 16 byte string, the string contains only four bytes.
  - A **string** can be created using the character escape `\u` (ex: `str = "\u1F499"`).
  - A **string** can be created using the emoji itself (ex: `str = "💙"`).

  You will want to use different forms at different times.


- In `emoji_count`, you will count the emojis in a provided string.

  For the purpose of this MP, we will consider an emoji to be anything in the inclusive range `U+1F000` - `U+1FAFF`.  *(There are some invalid characters in this range and a few early emojis outside of this range, but we want to keep it simple.  Feel free to be more accurate, we will only test your code on real emoji within this range and your solution won't break if you program a more correct solution.)*


- In `emoji_random_alloc`, you will generate a random emoji each time the function is called!  The [C documentation for rand](http://www.cplusplus.com/reference/cstdlib/rand/) outlines how to create a random integer in C, which you'll need to use for your emoji.  Make sure that you return a different emoji each time `emoji_random_alloc` is called.

  Just like in `emoji_count`, you can assume the inclusive range `U+1F000` - `U+1FAFF` or be more specific.


- In `emoji_invertChar`, you get to invert an emoji.  You must invert "😊" (`U+1F60A`) to some sort of sad face (your choice!).  The inversion is a **semantic**, so you should invert the meaning of the emoji (**NOT** flipping or "inverting" the bits).  In addition to "😊", you need to invert at least five other emojis of your choice (and it's okay to do more than just five).  This function inverts only the first character in a provided string.


- In `emoji_invertAll`, you will invert all the characters in the string using your `emoji_invertChar` function.


- In `emoji_invertFile_alloc`, you will read in the contents of a provided file name and invert all the emojis in that file, and return the inverted string.


### Part 2: An Emoji Translator

Sometimes, we may write articles (or entire books) with emojis!  For the second part of this MP, you will begin to work with basic C data structures to create a "emoji translation" utility.

In `emoji-translate.c`, you will find four functions:

- `void emoji_init(emoji_t *emoji)` is called to initialize an `emoji` object.

- `void emoji_add_translation(emoji_t *emoji, const unsigned char *source, const unsigned char *translation)` adds a translation to the `emoji` object.  For example, "😊" might translate to "happy".

- `const unsigned char *emoji_translate_file_alloc(emoji_t *emoji, const char *fileName)` must translate the contents of the file specified by `fileName` all translation rules added so far.  When matching rules, always choose the longest matching rule based on the length of the `source`.

  - For example, only the rule "😊" => "happy" has been added: `"I am 😊!"` would translate to `"I am happy!"`.
  - If the rule "😊" => "happy" and "😊😊" => "very happy", `"I am 😊😊!"` would translate to `"I am very happy!"` since the rule using two faces is the longer than one face.

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

For full credit, your MP must run "valgrind clean".  This means that you must:

- Compile all test cases on the command line using `make test`,
- Run all test cases using `valgrind --leak-check=full --show-leak-kinds=all ./test`,
- This must report the following output: `All heap blocks were freed -- no leaks are possible`


## Modifiable Files

In your solution, you must only modify the following files.  Modifications of other files may break things:

- `emoji.c`
- `emoji.h`
- `emoji-translate.c`
- `emoji-translate.h`


## Testing Your Program

- To compile the test suite, run `make test`.
- To run your code, run `./test` and everything should pass! 🎉


## Submit

When you have completed your program, double-check all three parts run without errors and gets the result your expect.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on github.com.