---
title: "Page Eviction Algorithms"

layout: content
---

<style>
.pt-eviction {
  width: 100%;
  margin-bottom: 35px;
  font-size: 14px;
  text-align: center;
  background-color: transparent;
}

.pt-eviction tr:first-of-type {
  border-bottom: solid 3px #888;
  
}

.pt-eviction tr td:first-of-type {
  border-right: solid 1px #888;
  background-color: #eee;
  width: 100px;
  text-align: right;
}

.pt-eviction tr td {
  border: solid 1px #888;
  width: 9%;
}


.pt-eviction tr td.fault {
  border: solid 3px #000;
}

.pt-eviction tr td.new {
  background-color: #ffc;
}


</style>


# Page Eviction Algorithms

When a page fault occurs (data that is not in RAM needs to be loaded into RAM) and our RAM is full, we must decide on a page to evict from RAM to make room for the new page. There are several approaches to how we might want to evict pages.



## Page Eviction Strategy #1: First In, First Out

The most basic strategy for evicting a page is simple: **evict the page that has been in memory the longest**.  More generally, we call this a **first in, first out** or **FIFO** page eviction strategy.

To illustrate how this works, consider a series of page accesses:

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
</table>


At each step, we check if the page is already loaded into RAM.  If the page is not already loaded into RAM, a **page fault** will occur and the data will be loaded into an available spot in RAM.  If needed, the oldest page will be evicted.

- Page 17 is requested and is not in RAM.  A page fault occurs and its loaded into RAM[0].  (We will denote a page fault by making the square bold where is was loaded.)

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>



- Page 33 is requested and is not in RAM.  A page fault occurs and its loaded into RAM[1].

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>





- Page 40 is requested and is not in RAM.  A page fault occurs and its loaded into RAM[2].

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>



- Page 17 is already in RAM and can be accessed without a page fault.

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td class="new">17</td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>



- Page 43 is requested and is not in RAM.  A page fault occurs and its loaded into RAM[3].

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43</td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>





- Page 8 is requested and is not in RAM.  A page fault occurs and the oldest page is evicted -- Page 17 is evicted from RAM[0] and replaced with Page 8.

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="fault new">8</td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td>40</td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault">43</td><td>43</td><td></td><td></td><td></td><td></td>    
  </tr>
</table>




- Page 99 is requested and is not in RAM.  A page fault occurs and the oldest page is evicted -- Page 33 is evicted from RAM[1] and replaced with Page 99.

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="fault">8</td><td>8</td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="fault new">99</td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td>40</td><td>40</td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault">43</td><td>43</td><td>43</td><td></td><td></td><td></td>    
  </tr>
</table>




- Page 33 is requested and is not in RAM.  (*This is a really sad, as we **just evicted Page 33!***)  Since it's not in RAM, a page fault occurs and we evict the oldest page (Page 40) from RAM[2] and load in Page 33.

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="fault">8</td><td>8</td><td>8</td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="fault">99</td><td>99</td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td>40</td><td>40</td><td class="new fault">33</td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault">43</td><td>43</td><td>43</td><td>43</td><td></td><td></td>    
  </tr>
</table>




- Page 99 is already in RAM! :)

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="fault">8</td><td>8</td><td>8</td><td>8</td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="fault">99</td><td>99</td><td class="new">99</td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td>40</td><td>40</td><td class="fault">33</td><td>33</td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault">43</td><td>43</td><td>43</td><td>43</td><td>43</td><td></td>    
  </tr>
</table>





- Finally, Page 17 is now a RAM and one final page fault occurs.

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="fault">8</td><td>8</td><td>8</td><td>8</td><td>8</td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="fault">99</td><td>99</td><td>99</td><td>99</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault">40</td><td>40</td><td>40</td><td>40</td><td>40</td><td class="fault">33</td><td>33</td><td>33</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault">43</td><td>43</td><td>43</td><td>43</td><td>43</td><td class="fault new">17</td>
  </tr>
</table>


### Summary

After the ten page accesses, this is our full page table/RAM access diagram:

- A page faults occurred when the cell is outlined in a bold,
- A page access occurred when the cell is highlighted

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td>17</td><td>17</td><td class="new">17</td><td>17</td><td class="fault new">8</td><td>8</td><td>8</td><td>8</td><td>8</td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="fault new">99</td><td>99</td><td class="new">99</td><td>99</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40</td><td>40</td><td>40</td><td>40</td><td>40</td><td class="fault new">33</td><td>33</td><td>33</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43</td><td>43</td><td>43</td><td>43</td><td>43</td><td class="fault new">17</td>
  </tr>
</table>

### Analysis

When using the FIFO page eviction strategy, we saw we needed **eight** page faults when accessing the pages in this access pattern.  Since a page fault is an expensive operation (loading 4 KiB of data from storage into RAM), our goal is to **minimize the number of page faults**.




## Page Eviction Strategy #2: Least Recently Used (LRU)

Another second strategy to explore is to examine how **recently page page was used** (accessed) instead of simply how long the page has been in RAM.  By doing this, a frequently used page should be able to stay in RAM for a long time as long as it's continually used.

Using the same memory access sequence, we can fill in the table the same way:

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>

[...after all of the accesses...]

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td>17</td><td>17</td><td class="new">17</td><td>17</td><td>17</td><td>17</td><td class="fault new">33</td><td>33</td><td>33</td>
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33</td><td>33</td><td>33</td><td>33</td><td class="fault new">8</td><td>8</td><td>8</td><td>8</td><td>8</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40</td><td>40</td><td>40</td><td>40</td><td class="fault new">99</td><td>99</td><td class="new">99</td><td>99</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43</td><td>43</td><td>43</td><td>43</td><td>43</td><td class="fault new">17</td>    
  </tr>
</table>


### Analysis

On this memory sequence, LRU resulted in a different layout of pages within RAM but still had 8 page faults.  We would need to explore more samples, and longer access patterns, to understand if LRU would outperform FIFO on this system.

However, what's the best we can do?



## Page Eviction Strategy #3: Optimal (OPT)

As we explore more strategies, we should explore what the optimal scenario would look like.  Consider what would be the optimal eviction strategy:

- We want to evict the page that is **used furthest in the future** among all of the pages in RAM.
- This requires us know the future (impossible in the real world!) but will provide us the ability to find the best-case solution.

Using the same memory access sequence, we can fill in the table the same way:

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>


[...after all of the accesses...]

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td>17</td><td>17</td><td class="new">17</td><td>17</td><td>17</td><td>17</td><td>17</td><td>17</td><td class="new">17</td>
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33</td><td>33</td><td>33</td><td>33</td><td>33</td><td>33</td><td class="new">33</td><td>33</td><td>33</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40</td><td>40</td><td>40</td><td class="fault new">8*</td><td class="fault new">99*</td><td>99</td><td class="new">99</td><td>99</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43</td><td>43</td><td>43</td><td>43</td><td>43</td><td>43</td>    
  </tr>
</table>

<div class="small" style="margin-top: -35px; padding-left: 20%; text-align: right;">
*: When deciding which page to evict to load in Page 8 and Page 99, we could have used either RAM[2] or RAM[3] since pages stored in both RAM[2] and RAM[3] are never accessed again.  I choose to use the earliest index in my tie-breaking.
</div>

### Analysis

Using an optimal approach, we find we require **six page faults** even if we can know the future.






## Page Eviction Strategy #4: Not Recently Used (NRU)

A new strategy we can explore begins to add more complexity.  We want to capture the idea of **how recently used is the page?**  To do that:

- When a page is used (accessed), set the `recentlyUsed` bit to 1.
- When a page needs to be evicted, evict a page least recently used page that has the `recentlyUsed` bit set to 0.  If no pages have the `recentlyUsed` bit set to 0, set all pages `recentlyUsed` bit to 0 and evict the least recently used page.


Using the same memory access sequence, we can fill in the table the same way.  Except that we will add an **!** when the `recentlyUsed` bit is `0` and the page is in danger of being evicted:

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>

[...after all of the accesses...]

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17</td><td>17</td><td>17</td><td class="new">17</td><td>17</td><td>17 !</td><td>17 !</td><td class="fault new">33</td><td>33</td><td>33</td>
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33</td><td>33</td><td>33</td><td>33</td><td class="fault new">8</td><td>8</td><td>8</td><td>8</td><td>8</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40</td><td>40</td><td>40</td><td>40 !</td><td class="fault new">99</td><td>99</td><td class="new">99</td><td>99</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43</td><td>43 !</td><td>43 !</td><td>43 !</td><td>43 !</td><td class="fault new">17</td>    
  </tr>
</table>

<div class="small" style="margin-top: -35px; padding-left: 20%; text-align: right;">
!: Recently used bit is equal to zero.
</div>






## Page Eviction Strategy #5: Least Frequently Used (LFU)

A final strategy we can explore captures the idea of **how frequently used is the page?**

In addition to storage the page, we will store the `useCount`.  When evicting a page, we will evict the page with minimal value of `useCount`; in the case of a tie, we will evict the page that was least recently used:

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17 (1)</td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td><td></td>    
  </tr>
</table>

[...after all of the accesses...]

<table class="pt-eviction">
  <tr>
    <td>Page Access:</td>
    <td>17</td><td>33</td><td>40</td><td>17</td><td>43</td><td>8</td><td>99</td><td>33</td><td>99</td><td>17</td>
  </tr>
  <tr>
    <td>RAM[0]:</td>
    <td class="fault new">17 (1)</td><td>17 (1)</td><td>17 (1)</td><td class="new">17 (2)</td><td>17 (2)</td><td>17 (2)</td><td>17 (2)</td><td>17 (2)</td><td>17 (2)</td><td class="new">17 (3)</td>
  </tr>
  <tr>
    <td>RAM[1]:</td>
    <td></td><td class="fault new">33 (1)</td><td>33 (1)</td><td>33 (1)</td><td>33 (1)</td><td class="new fault">8 (1)</td><td>8 (1)</td><td>8 (1)</td><td>8 (1)</td><td>8 (1)</td>    
  </tr>
  <tr>
    <td>RAM[2]:</td>
    <td></td><td></td><td class="fault new">40 (1)</td><td>40 (1)</td><td>40 (1)</td><td>40 (1)</td><td class="fault new">99 (1)</td><td>99 (1)</td><td class="new">99 (2)</td><td>99 (2)</td>    
  </tr>
  <tr>
    <td>RAM[3]:</td>
    <td></td><td></td><td></td><td></td><td class="fault new">43 (1)</td><td>43 (1)</td><td>43 (1)</td><td class="fault new">33 (1)</td><td>33 (1)</td><td>33 (1)</td>    
  </tr>
</table>


<div class="small" style="margin-top: -35px; padding-left: 20%; text-align: right;">
(x): Denotes the use count for the page in memory.
</div>



<div style="text-align: center">
  <a href="pageTableEntry.html" class="btn btn-primary">
    Next: Page Table Entry &gt;&gt;
  </a>
</div>
