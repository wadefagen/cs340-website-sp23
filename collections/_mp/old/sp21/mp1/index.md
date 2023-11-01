---
title: Emojis

date: 2021-02-01
due: 2021-02-08 23:59:59

points: 25

number: 1
published: false
---

# Overview

Your first MP in CS 240 is all about getting conformable with memory allocation, pointers, and character encodings in C.  You will complete several different sects of functions to build up your C programming skills.


## Virtual Machine

You have been provided an EngrIT virtual machine ("your CS 240 VM") for use in CS 240 MPs.  The VM is a computer that is entirely yours -- you control every bit of the system -- and provides you an environment to hack on.

- You can find your VPN hostname on Compass 2g -- it will be a URL that you will use SSH to connect to once you turn on your VPM.
- You must be connected to the campus VPN to access your CS 240 -- [VPN install guide](https://answers.uillinois.edu/98773).
- Your VM will be powered down each day at 5am (unless you're actively logged in).  EngrIt has created a ["Power On / Reset VM" guide](https://answers.uillinois.edu/illinois.engineering/page.php?id=108475) that details how to turn on your CS 240 VM.
- Once you have powered on your VPN, you can SSH into it with your NetID and password.


## Initial Files

You will download and turn in your code using the internal CS github server known as `github-dev`.

1. [Follow our guide to getting your github-dev set up]({{site.baseurl}}/resources/git/).
2. Once you have your github setup, run the following commands for your cs240/netid/ directory:
```
git fetch release
git merge release/mp1 -m "Merging initial files"
```



## Machine Problem

### Part 1: Adding Integers

In `add_int.c`, you will find four functions that require you to add two integers together.  Complete each function to return the sum of the two integers provided:

- In some functions, the integers are provided via an int pointer (`int *`).  You will need to access the contents of the memory pointed to by the pointer to find the integer value.  (You may need to review how to *dereference a pointer* from your C++ course.)

- In some functions, the function will require you to return your value in newly allocated memory.  In C, you can create new memory using the [`malloc`](https://en.cppreference.com/w/c/memory/malloc) call that will return a pointer to the newly allocated heap memory (similar to the `new` keyword in C++).  To allocate exactly enough memory to store an `int`, the following code allocates the exact memory required to store an `int` and stores that in the pointer named `result`:

```
int *result = malloc( sizeof(int) );
```

#### Running Your Program

We have provided a `make` file for you to run your code:

- In your terminal, type `make` to compile your program.
- To run your code, run `./mp1`.  You should just focus on the functions from Part 1 at this point.
- You can view/edit the `part1` function in `main.c` to see what test cases we have used.  *(Feel free to edit this file to add more test cases on your own!)*

<hr>

### Part 2: Capitalizing Strings

In `capitalizing.c`, you will find three functions where you will capitalize one or more letters in a provided string.

- Remember that strings in C are a pointer (`char *`) to the beginning of sequence of bytes that ends with a `NULL` byte (`0x00`).  This means that everything you know about pointers applies here and there is nothing "special" about a string (besides the termination condition).

- When detecting a lower-case letter, examine an [ASCII table](https://www.google.com/search?ei=LV1OX6m5EI63tQaf2b-gDg&q=ascii+table) to find the range of all possible lower case letters.  By using the table, you should also find a way to easily translate any lower-case letter to upper-case.  *Please do not write 26 if-statements for every possibly letter, you can do it with just math! :)*

- In completing these functions, try to use the earlier functions in your later code.  For example, can you call `capitalize` in `capitalizeAll` so you do not have to rewrite or copy/paste code?

#### Running Part 2

Compile your program with `make`, focusing on the output related to functions from Part 2.

<hr>

### Part 3: Emojis! 🎉

In `emoji.c`, you will find your final five functions where you will combine everything you know into working with UTF-8 encoded strings.

- In `emoji_favorite`, we just want to know your favorite emoji.  You will need to specify a string of bytes that encodes your favorite emoji.  In C, you can specify multiple hex-values in a string by using `\x` followed by two hex digits.  For example: `"\x45\x67"` is the string containing `0x45` (`E`) and `0x67` (`g`).  Almost all emojis will require **four bytes** for a single character.

- For the purpose of this MP, we will consider an emoji to be anything in the inclusive range `U+1F000` - `U+1F9FF`.  *(There are some invalid characters in this range and a few early emojis outside of this range, but we want to keep it simple.  Feel free to be more accurate, we will only test your code on real emoji within this range and your solution won't break if you program a more correct solution.)*

- In `emoji_invertChar`, you get to invert an emoji.  You must invert "😊" (`U+1F60A`) to some sort of sad face (your choice!).  The inversion is a **symantec**, so you should invert the meaning of the emoji (not necessarily flipping the bits).  In addition to "😊", you need to invert five other emojis of your choice (and it's okay to do more than just five).

- Finally, in `emoji_random_alloc`, you should generate a new random emoji!  The [C documentation for rand](http://www.cplusplus.com/reference/cstdlib/rand/) outlines how to create a random integer in C, which you'll need to use for your emoji.  Make sure that you return a different emoji each time `emoji_random_alloc` is called.


#### Running All Three Parts

You can test all three parts of your program together:

- To compile everything simply run `make`.
- To run your code, run `./mp1` and everything should look correct! 🎉


## Submit

When you have completed your program, double-check all three parts run without errors and gets the result your expect.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)