---
title: "Page Table Entries"
layout: content
---
<style>
.pt-eviction {
  width: 100%;
  margin-bottom: 20px;
  font-size: 14px;
  text-align: center;
  background-color: transparent;
}

.pt-eviction tr td {
  border: solid 1px black;
}
</style>


<style>

.memAddr {
  margin: 0px auto;
  margin-bottom: 20px;
  font-size: 18px;
  text-align: right;
  background-color: transparent;
}

.memAddr td {
  padding-left: 5px;
}

.memAddr code {
  background-color: transparent;
  color: black;
  font-weight: bold;
}

.memSplit {
  border-right: solid 3px black;
  padding-right: 5px;
  margin-right: 5px;
}

.memAddr .desc {
  font-size: 14px;
  text-align: center;
  border-top: solid 1px black;
}

.memAddr .highlight {
  background-color: #ffffcc;
}

</style>

# Page Table Entries

At this point, you have an understanding of two key ideas:

- What happens in a real system that causes the need for a page to be evicted, and
- How we choose to evict a page

The last piece of the puzzle to understanding paging is to look at the entry in the actual page table itself.  This entry, called the Page Table Entry (PTE) exists for every record within every page table on the system.  Each page table entry will have a set size, specific to the system.


## The Page Table Entry on x86 Machines

On an x86 machine, each page table entry is made up of 32 bits:

<table class="pt-eviction">
  <tr style="font-weight: bold">
    <td>Physical Page Address</td>
    <td>A[2]</td>
    <td>G</td>
    <td>&mdash;</td>
    <td>D</td>
    <td>A</td>
    <td>C</td>
    <td>W</td>
    <td>U</td>
    <td>R</td>
    <td>P</td>
  </tr>
  <tr style="background-color: #ccc; font-size: 10px;">
    <td>20 bits</td>
    <td>2 bits</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
    <td>1 bit</td>
  </tr>  
</table>

- The **P** bit, located as PTE[0], is the **"Present" bit**.  If the present bit is set, the page is available in RAM.  If the present bit is not set, a page fault occurs and the operating system reads the data from storage into RAM before continuing.
- The **A[2]** bits, located at PTE[9] and PTE[10], are two **"Available" bits** used by the operating system to track if the page has been accessed.  These bits can be used to implements a LRU, LFU, or other page eviction algorithm.
- The **D** bit, located at PTE[6], is the **"Dirty" bit** used to describe if the page has been written to.  If the dirty bit is off, the page has not been modified since it was loaded into RAM and does not need to be saved (it's contents is the same as it was in storage, and may just be overwritten).
- The **U** bit, located at PTE[2], is the **"User" bit**.  If the user bit is off, only the operating system itself can access the page.  If a user process tries to access this page, an exception within the CPU will be thrown.
- The **R** bit, located at PTE[1], is the **"Read/Write" bit**.  If the read/write bit is set, the page can be written to.  If the read/write bit is not set, the page may only be read from (and not written to).  (This provides additional safety to ensure that the program's code is never changed once it's loaded into memory to prevent various security exploits.)

- Other bits primary deal with low-level optimizations and cache control.  The [OSDev.org page on Page Tables](https://wiki.osdev.org/Paging) describes the x86 page table layout in detail.


## Page Table Entries in Pages

Given that page table entries take up space -- and the page table itself has to be stored in RAM -- we need to fit our page table entries into pages! *(We're getting meta!)*


**Q**: Given that our "dream computer" has 64 GiB of RAM, how large would the page table need to be to address all 64 GiB (given a page size of 4 KiB and a PTE size of 4 B)?

- Our 64 GiB of RAM is divided up into pages of 4 KiB, so there's a total of (64 GiB) / (4 KiB) = 16 MiB pages in the system.
- To address each of these 16 MiB pages, each process would need a page table entry for all 16 MiB possible pages.
- At 4 B /PTE, every page table would 64 MiB!
- Since every process has its own page table -- that's an **extreme** amount of overhead.
- Can we do better?


**Q**: Given that our system has 32-bit (4 B) page table entires and 4 KiB pages, how many PTEs fit within a page?

- If each entry takes just 4 B, 4096 / 4 == 1024 PTEs fit within one page on this specific system.


**Q**: What if we made our entire page table the size of one page?

- Our page table would only be able to address 2<sup>10</sup> different values -- or 10 bits of an address.


**Q**: Can we use multiple page tables to resolve a memory address?

- Yes!


## Multi-Level Page Tables

If our system required us to have a single page table for every process, the "cost" of a process would be quite high due to the overhead of just the page table alone.  A trade-off that is used in modern systems is to fit every page table into a single page.

To access the page table entry specific to a process, a special page table is used to map portion of a virtual memory address into the next level page table.  Consider a 48-bit address: `0x 5678 9abc def0`

<table class="memAddr">
  <tr>
    <td class="memSplit"><code>0b</code></td>
    <td><code>0101</code></td>
    <td><code>0110</code></td>
    <td><code>0111</code></td>
    <td><code>1000</code></td>
    <td><code>1001</code></td>
    <td><code>1010</code></td>
    <td><code>1011</code></td>
    <td><code>1100</code></td>
    <td class="memSplit"><code>1101</code></td>
    <td class="highlight"><code>1110</code></td>
    <td class="highlight"><code>1111</code></td>
    <td class="highlight"><code>0000</code></td>
  </tr>

  <tr class="desc">
    <td class="memSplit"><code></code></td>
    <td class="memSplit" colspan="9">Page Number</td>
    <td class="highlight" colspan="3">
      Page Offset<br>
      (w/ pagesize=4096)
    </td>
  </tr>
</table>

Using a mutli-level page table where each page table entirely in one page, we can divide up this full page number into a multi-level page table based on our system parameters:

- In our system here, we calculated earlier than each page and fit 2<sup>10</sup> PTEs.  Therefore, it can address 10 bits of our memory.
- We divide up the page number into 10-bit or smaller chunks and assign each part of address a different level of page table:

<table class="memAddr">
  <tr>
    <td class="memSplit"><code>0b</code></td>
    <td><code>0101</code></td>
    <td class="memSplit"><code>01</code></td>
    <td><code>10</code></td>
    <td><code>0111</code></td>
    <td class="memSplit"><code>1000</code></td>
    <td><code>1001</code></td>
    <td><code>1010</code></td>
    <td class="memSplit"><code>10</code></td>
    <td><code>11</code></td>
    <td><code>1100</code></td>
    <td class="memSplit"><code>1101</code></td>
    <td class="highlight"><code>1110</code></td>
    <td class="highlight"><code>1111</code></td>
    <td class="highlight"><code>0000</code></td>
  </tr>

  <tr class="desc">
    <td class="memSplit"><code></code></td>
    <td class="memSplit" colspan="2">First Level<br>Page Table Index</td>
    <td class="memSplit" colspan="3">Second Level<br>Page Table Index</td>
    <td class="memSplit" colspan="3">Third Level<br>Page Table Index</td>
    <td class="memSplit" colspan="3">Page Number</td>
    <td class="highlight" colspan="3">
      Page Offset<br>
      (w/ pagesize=4096)
    </td>
  </tr>
</table>

To resolve this virtual address into its physical address, we would require four page table lookups:

- First, we find the **root page table** for our process and find entry `0b 01 0101` (the first level page table index).  This entry will give us the memory address for the second level page table.
- In the second level page table, we find the entry `0b 10 0111 1000` (the second level page table index).  This entry will give us the memory address for the third level page table.
- ...this process repeats until we find the final page table.

### Efficiency of Multi-Level Page Tables

A reasonable question: is this not insanely inefficient?

For most processes, the vast majority of the page table entries will be completely empty.  You do need a second/third/fourth-level page table in cases where there's no data mapped to that region of the page table.

Additionally, all heap memory, stack memory, and other memory used sequentially laid out within your virtual page table.  Even though a process may have a heap address starting at `0x7f552c8340`, we know **ALL** heap memory will be nearby that address and will likely only span a one or two levels of page tables.


<div style="text-align: center">
  <a href="segFault.html" class="btn btn-primary">
    Next: Segmentation Fault &gt;&gt;
  </a>
</div>
