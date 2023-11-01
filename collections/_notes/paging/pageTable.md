---
title: "Page Table"
layout: content
---

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

# Memory Paging and Page Tables

Right now, you have a deep understanding of how memory works within a process.  You understand stack memory, heap memory, and you are working to build a memory allocator yourself in MP3!  However, **how does the memory work within the operating system**?


## Pages and Page Size

In a modern system, we have many types of storage (processor caches, RAM, hard drive, etc).  To help us organize moving data between different locations, **ALL** of the storage inside of our system will be organized into **pages**:

- Just like a page in a book, a page in a system is going to contain a small amount of data.
  - On a Linux system, you can run `getconf PAGESIZE` to find the size (in bytes) of your page, or
  - On a Windows system, you can run write a program that calls the [`GetSystemInfo`](https://docs.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-getsysteminfo?redirectedfrom=MSDN) system call and check the `dwPageSize` value

- On most systems, the page size will be `4096`.  This means that every page will contain exactly 4,096 bytes of data -- or 2<sup>12</sup> bytes.  However, a page can be any power of two -- `256`, `512`, `1024`, `2048`, `4096`, `8192`, and so on are all reasonable values for a page size.


## Memory Addresses and Pages

Once we know the size of a page, we can look at every memory address and divide the address into two sections:

- The **page table index** or **page number** (ex: where in the book do I find this specific page), and
- The **page offset** (ex: where on the page do I find the specific letter I'm looking for)

For a page size of 4,096 B (2<sup>12</sup>), the least significant 12 bits will describe **where** on the page the data is located.  Therefore, the last 12 bits is the **page offset** and all of the other bits describe the **page number**.

Using the page size of 4,096 B (2<sup>12</sup>), consider the memory address `0x 2c3af` == `0b 0010 1100 0011 1010 1111`:

<table class="memAddr">
  <tr>
    <td class="memSplit"><code>0x</code></td>
    <td><code>2</code></td>
    <td class="memSplit"><code>c</code></td>
    <td class="highlight"><code>3</code></td>
    <td class="highlight"><code>a</code></td>
    <td class="highlight"><code>f</code></td>
  </tr>

  <tr>
    <td class="memSplit"><code>0b</code></td>
    <td><code>0010</code></td>
    <td class="memSplit"><code>1100</code></td>
    <td class="highlight"><code>0011</code></td>
    <td class="highlight"><code>1010</code></td>
    <td class="highlight"><code>1111</code></td>
  </tr>

  <tr class="desc">
    <td class="memSplit"><code></code></td>
    <td class="memSplit" colspan="2">Page Number</td>
    <td class="highlight" colspan="3">
      Page Offset<br>
      (w/ pagesize=4096)
    </td>
  </tr>
</table>


Using a smaller page size of just 256 B (2<sup>8</sup>), consider the same memory address:

<table class="memAddr">
  <tr>
    <td class="memSplit"><code>0x</code></td>
    <td><code>2</code></td>
    <td><code>c</code></td>
    <td class="memSplit"><code>3</code></td>
    <td class="highlight"><code>a</code></td>
    <td class="highlight"><code>f</code></td>
  </tr>

  <tr>
    <td class="memSplit"><code>0b</code></td>
    <td><code>0010</code></td>
    <td><code>1100</code></td>
    <td class="memSplit"><code>0011</code></td>
    <td class="highlight"><code>1010</code></td>
    <td class="highlight"><code>1111</code></td>
  </tr>

  <tr class="desc">
    <td class="memSplit"><code></code></td>
    <td class="memSplit" colspan="3">Page Number</td>
    <td colspan="2" class="highlight" style="font-size: 10px;">
      Page Offset<br>
      (w/ pagesize=256)
    </td>
  </tr>
</table>


# Page Table

You can now identify the page number and page offset of any memory address.  However, the critical thing that an operating system does is help us manage the pages within our system.

The operating system maintains a **page table for every process on the system**.  The page table will serve many purposes:

- The page table will include a pointer to where the data is located in storage (ex: in RAM, on disk, etc)
- The page table will provide the translation form a virtual memory address to physical memory address
- The page table will maintain bits associated with the use of memory on the page (ex: has the page had any write operations done to the memory?)

To help us understand this, consider a simple system with a small amount of RAM (4 pages), a process P1 (with 16 virtual pages), and a hard drive.  The hard drive has the executable that we will run in (`programCode`) and a `hiddenImage.png` that contains a hidden GIF inside of the PNG:

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

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td>(empty)</td></tr>
      <tr><td>[2]</td><td>(empty)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td>(empty)</td></tr>
      <tr><td>[2]</td><td>(empty)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
      <tr><td>[4]</td><td>(empty)</td></tr>
      <tr><td>[5]</td><td>(empty)</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td>(empty)</td></tr>
      <tr><td>[9]</td><td>(empty)</td></tr>
      <tr><td>[10]</td><td>(empty)</td></tr>
      <tr><td>[11]</td><td>(empty)</td></tr>
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
      <tr><td>[42]</td><td class="disk">programCode</td></tr>
      <tr><td>[43]</td><td class="disk">programCode</td></tr>
      <tr><td>[44]</td><td class="disk">programCode</td></tr>
      <tr><td>[45]</td><td class="disk">programCode</td></tr>
      <tr><td>[46]</td><td class="disk">programCode</td></tr>
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
    Operations:
  </div>
</div>


## File Sizes and Pages

In the memory layout above, each page is 4 KiB (4,096 bytes).  We can learn a lot about the files on disk:

- We know that `programCode` requires **five pages** to store the data.  Therefore, we know that:

    * Four pages was not enough to store the file.  Therefore, the file must be at least (16 KiB + 1 byte) in size.
    * Six pages was not required.  Therefore, the file must be no larger than 20 KiB in size (a full five pages).


- Similarly, the same logic can be applied to `hiddenImage.png`.  The file requires 3 pages of storage and must have the size in the range: (8 KiB, 12 KiB].


## Step 1: Starting Our Program

The first step in starting a process is to map the program's executable code into memory.  It is not necessary to load the entire program into RAM (*that would take forever for a large program*). 

- When a process is created, we simply map the executable code to the process' virtual page table and point it to disk:

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td>(empty)</td></tr>
      <tr><td>[2]</td><td>(empty)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td class="pt">==> disk[42]</td></tr>
      <tr><td>[2]</td><td class="pt">==> disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td>(empty)</td></tr>
      <tr><td>[9]</td><td>(empty)</td></tr>
      <tr><td>[10]</td><td>(empty)</td></tr>
      <tr><td>[11]</td><td>(empty)</td></tr>
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
      <tr><td>[42]</td><td class="disk">programCode</td></tr>
      <tr><td>[43]</td><td class="disk">programCode</td></tr>
      <tr><td>[44]</td><td class="disk">programCode</td></tr>
      <tr><td>[45]</td><td class="disk">programCode</td></tr>
      <tr><td>[46]</td><td class="disk">programCode</td></tr>
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
    <b>1. Load Program</b>
  </div>
</div>


## Step 2: Running Our Program

Our program begins to execute.  We can only work with data that is in RAM, so a request to the process's code (located in the process's first page) will need to load the data into RAM.

- Anytime we need to load new data into RAM we call this a **page fault**.
- A **page fault** results in the operating system fetching data from external storage, loading into RAM, and continuing only once the load is completed.


As we begin to run our program, three things take place:

- We check if the data for `programCode (1/5)` is already in RAM.  It is not, so a **page fault** occurs.
- As part of resolving the page fault, `programCode (1/5)` is read form disk and written to RAM,
- Additionally, the page table is updated to note the mapping into RAM

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr class="new"><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td>(empty)</td></tr>
      <tr><td>[2]</td><td>(empty)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr class="new"><td>[1]</td><td class="pt">==> ram[0], disk[42]</td></tr>
      <tr><td>[2]</td><td class="pt">==> disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td>(empty)</td></tr>
      <tr><td>[9]</td><td>(empty)</td></tr>
      <tr><td>[10]</td><td>(empty)</td></tr>
      <tr><td>[11]</td><td>(empty)</td></tr>
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
      <tr class="new"><td>[42]</td><td class="disk">programCode (1/5)</td></tr>
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
    1. Load Program<br>
    <b>2. Run programCode (1/5)</b><br>
    - malloc(4000) + use<br>
    3. Run programCode (2/5)<br>
    - malloc(10000) + use<br>
    - open `hiddenImage`<br>
    - read `hiddenImage`<br>
    4. Run programCode (3/5)<br>
    - Access 4KB from #2<br>
    5. Program Exits
  </div>
</div>


### Step 2(b): Running malloc

As part of running the first part of our program, the program requests a memory allocation via `malloc(4000)`!

- We can satisfy this by allocating this memory to the process's heap
- Since the program also uses this memory, a page fault will occur and that data is loaded into RAM


<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr class="new"><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr><td>[2]</td><td>(empty)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td class="pt">==> ram[0], disk[42]</td></tr>
      <tr><td>[2]</td><td class="pt">==> disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr class="new"><td>[8]</td><td class="pt">(heap memory) ==> ram[1]</td></tr>
      <tr><td>[9]</td><td>(empty)</td></tr>
      <tr><td>[10]</td><td>(empty)</td></tr>
      <tr><td>[11]</td><td>(empty)</td></tr>
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
    1. Load Program<br>
    2. Run programCode (1/5)<br>
    <b>- malloc(4000) + use</b><br>
    3. Run programCode (2/5)<br>
    - malloc(10000) + use<br>
    - open `hiddenImage`<br>
    - read `hiddenImage`<br>
    4. Run programCode (3/5)<br>
    - Access 4KB from #2<br>
    5. Program Exits
  </div>
</div>


## Step 3: Running Our Program (but running out of RAM)

The next step is to continue to run our program, by running the second page of our program's data.

- `programCode (2/5)` is not in RAM, so a **page fault** occurs to load the data into RAM
- Just like before, we will update our page table to ensure the page table maps where the data is at during all times

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr class="new"><td>[2]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
    </table>
  </div>

  <div class="col-3">
    P1 Page Table:
    <table class="pageTable">
      <tr><td>[0]</td><td>(empty)</td></tr>
      <tr><td>[1]</td><td class="pt">==> ram[0], disk[42]</td></tr>
      <tr class="new"><td>[2]</td><td class="pt">==> ram[2], disk[43]</td></tr>
      <tr><td>[3]</td><td class="pt">==> disk[44]</td></tr>
      <tr><td>[4]</td><td class="pt">==> disk[45]</td></tr>
      <tr><td>[5]</td><td class="pt">==> disk[46]</td></tr>
      <tr><td>[6]</td><td>(empty)</td></tr>
      <tr><td>[7]</td><td>(empty)</td></tr>
      <tr><td>[8]</td><td class="pt">(heap memory) ==> ram[1]</td></tr>
      <tr><td>[9]</td><td>(empty)</td></tr>
      <tr><td>[10]</td><td>(empty)</td></tr>
      <tr><td>[11]</td><td>(empty)</td></tr>
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
      <tr class="new"><td>[43]</td><td class="disk">programCode (2/5)</td></tr>
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
    1. Load Program<br>
    2. Run programCode (1/5)<br>
    - malloc(4000) + use<br>
    <b>3. Run programCode (2/5)</b><br>
    - malloc(10000) + use<br>
    - open `hiddenImage`<br>
    - read `hiddenImage`<br>
    4. Run programCode (3/5)<br>
    - Access 4KB from #2<br>
    5. Program Exits
  </div>
</div>



### Step 3(b): A large allocation

The next command to be run is scary -- `malloc(10000)`.  A request for 10,000 bytes requires the use of 3 pages as each page can only contain 4 KiB (4,096 bytes).

- Initially, as malloc expands the heap, the heap grows on the virtual page table.  The heap now includes virtual page numbers 8, 9, 10, and 11:

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr><td>[2]</td><td class="disk">programCode (2/5)</td></tr>
      <tr><td>[3]</td><td>(empty)</td></tr>
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
      <tr class="new"><td>[9]</td><td class="pt">(heap memory)</td></tr>
      <tr class="new"><td>[10]</td><td class="pt">(heap memory)</td></tr>
      <tr class="new"><td>[11]</td><td class="pt">(heap memory)</td></tr>
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
    1. Load Program<br>
    2. Run programCode (1/5)<br>
    - malloc(4000) + use<br>
    3. Run programCode (2/5)<br>
    <b>- malloc(10000) + use</b><br>
    - open `hiddenImage`<br>
    - read `hiddenImage`<br>
    4. Run programCode (3/5)<br>
    - Access 4KB from #2<br>
    5. Program Exits
  </div>
</div>


- As we make use of the first page of the heap data, the first page fault occurs loading virtual page [9] into RAM:

<div class="row">
  <div class="col-3">
    RAM:
    <table class="pageTable">
      <tr><td>[0]</td><td class="disk">programCode (1/5)</td></tr>
      <tr><td>[1]</td><td class="heap">(heap memory data)</td></tr>
      <tr><td>[2]</td><td class="disk">programCode (2/5)</td></tr>
      <tr class="new"><td>[3]</td><td class="heap">(heap memory data)</td></tr>
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
      <tr class="new"><td>[9]</td><td class="pt">(heap memory) ==> ram[3]</td></tr>
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
    1. Load Program<br>
    2. Run programCode (1/5)<br>
    - malloc(4000) + use<br>
    3. Run programCode (2/5)<br>
    <b>- malloc(10000) + use</b><br>
    - open `hiddenImage`<br>
    - read `hiddenImage`<br>
    4. Run programCode (3/5)<br>
    - Access 4KB from #2<br>
    5. Program Exits
  </div>
</div>


- Now, we need to load our second page of heap memory in RAM.  However -- **we are out of empty RAM to use! :(**


# Page Eviction

When a page fault occurs (data that is not in RAM needs to be loaded into RAM) and our RAM is full, we must decide on a page to **evict** from RAM to make room for the new page.  Let's dive into page eviction policies and algorithms before we continue our example...


<div style="text-align: center">
  <a href="pageEviction.html" class="btn btn-primary">
    Next: Page Eviction Algorithms &gt;&gt;
  </a>
</div>
