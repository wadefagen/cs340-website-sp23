---
title: Midterm 1 Rubric
layout: content
---

# Rubric for Midterm 1 Grading of the Programming Question

I examined every solution and awarded credit based on the following rubric.  You can find the categories where points were lost by viewing your "Midterm 1" category in Canvas.

We will go over a solution in Tuesday's lecture!


## Midterm 1 - CV Not Defined *(-1 point)*

Does your code define and initialize a conditional variable (or semaphore) for synchronization?


## Midterm 1 - CV Not Used in Reader *(-1 point)*

Does your code use a conditional variable (or other synchronization tool) in the **reader** thread for synchronization?  When it is used, is the mutex locked?


## Midterm 1 - CV Not Correct in Reader *(-1 point)*

Is your synchronization only used when blocking is needed?  Is the conditional correct?  If `broadcast` is used without concern for who wakes up, is the conditional checked repeatedly?


## Midterm 1 - doRead not in CS *(-1 point)*

Is your `doRead` function called outside of the critical section so that multiple threads can read at the same time?


## Midterm 1 - CV Not Used in Writer *(-1 point)*

Does your code use a conditional variable (or other synchronization tool) in the **writer** thread for synchronization?  When it is used, is the mutex locked?


## Midterm 1 - CV Not Correct in Writer *(-1 point)*

Is your synchronization only used when blocking is needed?  Is the conditional correct?  If `broadcast` is used without concern for who wakes up, is the conditional checked repeatedly?

*(Note: A correct conditional must block when anyone is writing or reading.)*


## Midterm 1 - Globals Incorrect/Missing *(-1 point)*

Do you have globals defined to track if there is an active reader/writer?  Are they updated within a critical section?


## Midterm 1 - Implementation is not a Working Solution *(-4 points)*

Does your implementation provide synchronization to the readers-writers problem?  Only nearly perfect solutions will earn these points and lose a few points elsewhere.


## Midterm 1 - Correct except Race Condition in Reader *(-2 points)*

Is your program otherwise correct, except for a race condition in the reader?  The most common race condition in the reader is updating global variable counters outside of the critical section.


## Midterm 1 - Correct except Race Condition in Writer *(-2 points)*

Is your program otherwise correct, except for a race condition in the writer?  The most common race condition is only checking the that no writers are writing, when you must check **BOTH** that no readers and reading and no writers are writing.


## Midterm 1 - Correct except Infinite Loop or Deadlock *(-2 points)*

Checking that **both** no writers are writing **AND** no readers are reading before entering into `doWrite`.  (Many solutions that lose points here and nowhere else forgot to check that no one is reading.)
