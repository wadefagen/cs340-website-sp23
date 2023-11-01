---
date: 2021-02-22 23:00:01

title: "Paging and Page Tables"
number: 5

image: static/stock-photos/exp-design.jpg
layout: lecture

videos:
- Memory Paging and Page Tables: /cs240/sp2021/notes/paging/pageTable.html
- Page Eviction Algorithms: /cs240/sp2021/notes/paging/pageEviction.html
- Page Table Entry and Mutli-level Page Tables: /cs240/sp2021/notes/paging/pageTableEntry.html
- Segmentation Fault: /cs240/sp2021/notes/paging/segFault.html


published: false
---

### Course Meetup: Tuesday, Feb. 23

The next course meetup at on **Tuesday, Feb. 23 at 12:30pm on Zoom**.  We will use the same link all semester long (same as last week, and you can find it in Compass 2g as the only announcement for CS 240).


### Lecture Handouts

- Lecture Handout for Week #5: [PDF](/cs240/sp2021/static/lectures/cs240_wk5_lectureHandout.pdf)
  
As you go through the lecture notes, you can print this out or use a tablet to fill in key ideas from the notes!  They're designed to cover the same sample problems you'll see in the lecture notes! :)


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

- [Zoom Recording](https://mediaspace.illinois.edu/media/t/1_k8dg9p7n)


### MPs and Homeworks

1. [Homework 6: Paging (PrairieLearn)](/cs240/sp2021/homeworks/hw6/), due Mar. 1 by 11:59pm

2. [MP3: malloc](/cs240/sp2021/mp/mp3/) - The second week of a two week MP to implement malloc!
    - Extra Credit: Due Feb. 22 by 11:59pm
    - Full MP: Due Mar. 1 by 11:59pm

