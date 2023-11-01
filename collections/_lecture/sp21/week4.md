---
date: 2021-02-15 23:00:01

title: "Heap Memory Management"
number: 4

image: static/stock-photos/exp-design.jpg
layout: lecture

videos:
- Virtual Memory - Heap and Stack Memory: /cs240/sp2021/notes/virtualMemory-heap-stack.html
- Heap Memory Allocation - First-fit and Best-fit Approaches: /cs240/sp2021/notes/heapMemoryAllocation.html
- Building Malloc: /cs240/sp2021/notes/buildingMalloc.html

published: false
---

### Course Meetup: Tuesday, Feb. 16

The next course meetup at on **Tuesday, Feb. 16 at 12:30pm on Zoom**.  We will use the same link all semester long (same as last week, and you can find it in Compass 2g as the only announcement for CS 240).

### Note: COVID-19 Spring Break Replacement Day Off

This Wednesday (Feb. 17) is the first of three COVID-19 "Spring Break Replacement Day Off". Since CS 423 does not have any scheduled activities on Wednesdays, this will not impact the course.

That said, the final "Spring Break Replacement Day Off" is on Tuesday, April 13.  Since Tuesday is a regular lecture day, we will observe the week of April 12 - April 16 as our "Spring Break Replacement" for CS 240.  Yay!! 


### Lecture Topics

In our previous course meetup, we chatted about exploring a hybrid approach to lecture notes.  This week is a first pass at this approach with three detailed articles:

<ol>
  {% for link in page.videos %}
  <li>
      <a href="{{ link.first[1] }}">{{ link.first[0] }}</a>
  </li>
  {% endfor %}
</ol>


##### Course Meetup

- [Zoom Recording](https://mediaspace.illinois.edu/media/t/1_jaj2dnpc)

### MPs and Homeworks

1. [Homework 5: Memory (PrairieLearn)](/cs240/sp2021/homeworks/hw5/), due Feb. 22 by 11;59pm

2. [MP3: malloc](/cs240/sp2021/mp/mp3/) - A two week MP to implement malloc!
    - Extra Credit: Due Feb. 22 by 11:59pm
    - Full MP: Due Mar. 1 by 11:59pm

