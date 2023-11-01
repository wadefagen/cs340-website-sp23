---
title: "Segmentation Faults"
layout: content
---

<style>
.pageTable {
  margin: 0px auto;
  margin-bottom: 20px;
  font-size: 10px;
  text-align: center;
  background-color: transparent;
  width: 100%;
}

.pageTable tr {
  border: solid 1px black;
}

.pageTable tr.new {
  border: solid 2px black;
}


.pageTable tr td:first-of-type {
  border-right: solid 1px #888;
  background-color: #eee;
  font-family: monospace;
  width: 30px;
  text-align: right;
}

.pageTable .disk {
  background-color: hsla(26, 100%, 90%, 1);
}

.pageTable .disk2 {
  background-color: hsla(206, 100%, 90%, 1);
}

.pageTable .pt {
  background-color: hsla(46, 100%, 90%, 1);
}


.pageTable .heap {
  background-color: hsla(100, 100%, 90%, 1);
}
</style>

# Segmentation Faults

On the first set of notes for this week, we had a process running that motivated this week's notes.  We left it in the following state:

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr><td>[2]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[3]</td><td class="heap">(heap memory data)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td class="pt">==> ram[0], disk[42]</td></tr>
      <tr><td>[2]</td><td class="pt">==> ram[2], disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td class="pt">(heap memory) ==> ram[1]</td></tr>
      <tr><td>[9]</td><td class="pt">(heap memory) ==> ram[3]</td></tr>
      <tr><td>[10]</td><td class="pt">(heap memory)</td></tr>
      <tr><td>[11]</td><td class="pt">(heap memory)</td></tr>
      <tr><td>[12]</td><td>(empty)</td></tr>
      <tr><td>[13]</td><td>(empty)</td></tr>
      <tr><td>[14]</td><td>(empty)</td></tr>
      <tr><td>[15]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    Hard Drive:
    <table class="pageTable">
      <tr><td>...</td><td>(empty)</td></tr>
      <tr><td>[41]</td><td>(empty)</td></tr>
      <tr><td>[42]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[43]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[44]</td><td class="disk">programCode (3/5)</td></tr>
      <tr><td>[45]</td><td class="disk">programCode (4/5)</td></tr>
      <tr><td>[46]</td><td class="disk">programCode (5/5)</td></tr>
      <tr><td>[47]</td><td>(empty)</td></tr>
      <tr><td>...</td><td>(empty)</td></tr>
      <tr><td>[81]</td><td>(empty)</td></tr>
      <tr><td>[82]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[83]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[84]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[85]</td><td>(empty)</td></tr>
      <tr><td>...</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    Operations:<br>
    ...
    
  </div>
</div>


## Memory Accesses

**Q**: What happens if our program accesses the memory address `0xC123`?

- In our system, our pages are 4 KiB.  Therefore, page offset is `0x123` (12 bits) and the page number is the remaining bits (`0xC`).
- We know `0xC` == `12`.
- The contents of index 12, [12], in our page table is empty!


<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr><td>[2]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[3]</td><td class="heap">(heap memory data)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td class="pt">==> ram[0], disk[42]</td></tr>
      <tr><td>[2]</td><td class="pt">==> ram[2], disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td class="pt">(heap memory) ==> ram[1]</td></tr>
      <tr><td>[9]</td><td class="pt">(heap memory) ==> ram[3]</td></tr>
      <tr><td>[10]</td><td class="pt">(heap memory)</td></tr>
      <tr><td>[11]</td><td class="pt">(heap memory)</td></tr>
      <tr class="new"><td>[12]</td><td>(empty)</td></tr>
      <tr><td>[13]</td><td>(empty)</td></tr>
      <tr><td>[14]</td><td>(empty)</td></tr>
      <tr><td>[15]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    Hard Drive:
    <table class="pageTable">
      <tr><td>...</td><td>(empty)</td></tr>
      <tr><td>[41]</td><td>(empty)</td></tr>
      <tr><td>[42]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[43]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[44]</td><td class="disk">programCode (3/5)</td></tr>
      <tr><td>[45]</td><td class="disk">programCode (4/5)</td></tr>
      <tr><td>[46]</td><td class="disk">programCode (5/5)</td></tr>
      <tr><td>[47]</td><td>(empty)</td></tr>
      <tr><td>...</td><td>(empty)</td></tr>
      <tr><td>[81]</td><td>(empty)</td></tr>
      <tr><td>[82]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[83]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[84]</td><td class="disk2">hiddenImage.png</td></tr>
      <tr><td>[85]</td><td>(empty)</td></tr>
      <tr><td>...</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    Operations:<br>
    ...
    
  </div>
</div>


## Segmentation Fault

A segmentation fault occurs when you access memory that has not been mapped in your virtual memory!

- The example above results in a segmentation fault because [12] is an unused page table entry.
- Additionally, the operating system will **ALWAYS** leave the memory address `0x0` (and the whole page associated with `0000 ... 0000`) empty.  Due to this, access a `NULL` pointer (as `NULL` is simply `0`) will also always result in a segmentation fault.


<div style="text-align: center">
  <a href="/cs240/sp2021/homeworks/hw6/" class="btn btn-primary">
    Next: Homework #6 &gt;&gt;
  </a>
</div>
