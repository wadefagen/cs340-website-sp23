---
title: My Resource in your Wallet

date: 2023-02-21
due: 2023-02-28 23:59:59

points: 40

number: 4
published: true
---

# Overview

A classic game of the Internet-era is the game "Cookie Clicker".  Cookie Clicker is the "genre-defining" game of "incremental games", where you have something that generates a resource in the background.  With tasks being done in the background, this sounds like a perfect fit for CS 340!

For the first part of this MP, you will program a wallet that will hold a collection of resources.  A user will interact with your wallet by adding or subtracting resources from it, except that you must not allow the wallet to ever go negative -- **debt is not allowed**!

Once you have a wallet, you want to allow others to use your wallet as a service!  You will develop a multi-threaded server that uses a simple protocol to interact with your wallet over the network! 🎉

Let's get started! :)


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp4 --allow-unrelated-histories -m "Merging initial files"
```


## MP Requirements (Parts 1 and 2) - Wallet Library

### Part 1: The Wallet

For the first two parts of this MP, you will be working in the `lib` directory -- `lib/wallet.c` and `lib/wallet.h`.  You will implement the "wallet library", which has only a few basic requirements:

1. Your wallet initially starts empty, with 0 of all resources.
2. Your wallet can accept any resource (identified by any string).  The user of your library will call `wallet_change_resource` and supply a `delta` to change your resource by a certain `delta`.
3. You must **NEVER** let your wallet go negative.  You must NOT return from `wallet_change_resource` until you can satisfy the request (another thread must add to your wallet).
4. You may have multiple wallets at once.  All state of your wallet must be maintained in your `wallet_t`.
5. Finally, and most importantly, many threads will be accessing your wallet(s) at the same time.  You must ensure the data in your wallet is correct by ensuring accesses to your wallet are properly synchronized.

You will find your four functions -- `wallet_init`, `wallet_get`, `wallet_change_resource`, and `wallet_destroy` -- in `lib/wallet.c`.  Additionally, the `wallet_t` struct is defined in `lib/wallet.h`.

### Part 2: `wallet_change_resource` Must Block

In addition to correctly maintaining resources correctly, your wallet must move to the blocking state when a resource request cannot be immediately satisfied.  Your wallet must NOT busy wait and must NOT sleep wait.  Both busy and sleep waiting causes your CPU to make unnecessary checks and, in the case of sleeping, possibly waits an unnecessary extra amount of time.

```
// Example of "busy waiting" (wasting CPU cycles), which is NOT allowed:
while ( resource_count < delta ) {
    /* do nothing */
}
```

```
// Example of "sleep waiting" (wasting CPU cycles or time, or a bit of both), which is NOT allowed:
while ( resource_count < delta ) {
    usleep(10);
}
```

Instead, your wallet **MUST** move to the blocked state when the resource delta cannot be satisfied by using a mutex and conditional variables.


## Running Sample Implementations of the Wallet Library

A simple implementation using a wallet is provided for you as `hedgehog-simple.c`, `degree.c`, `gacha.c` and `ping-pong.c`.


### Simple Case: Hedgehogs

When you run `./hedgehog-simple`, you will see two threads run that do the following:
- A hedgehog food generator that constantly generates 🐛,
- A hedgehog generator that consumes 🐛 to generate 🦔!


### Complex Case: Degree

A more complex implementation (provided in `degree.c` and can be run with `./degree`), builds your degree by a series of worker threads including:
- A clover field that constantly produces ☘️ (and an occasional 🍀),
- A workshop that constantly produces 🧰 (and an occasional 💎),
- An orchard that constantly produces 🍏,
- A research job that consumes 🧰 and 🧬 to create a 📙,
- And a bunch of other jobs (you can check out the `degree.c`) to eventually create just a single 🎓!


### Fun Case: Gacha

Gacha is a system where a resource can be spent to open a "loot box" that contains random items.  In Genshin Impact, the gacha system allows players to earn three star, four star, or five star items with a pity system that guarantees an item of a certain star-level.  *(This sort of gacha system exists in almost all mobile microtransaction-based games, and most include some sort of pity.)*

When you run `./gacha` (compiled from `gacha.c`), the program will simulate grinding out earning the currency for 90 draws and the distribution of items -- how lucky can you get?


### Edge Case: Ping-Pong

When you run `./ping-pong` (compiled from `ping-pong.c`), you will constantly be bouncing resources back and forth.  This has proven useful for debugging some edge cases.


## Running Tests

- Run `make test` to compile and build the test suite.
- Run `./test "[part=1]"` to test the correctness of your solution.
- Run `./test "[part=2]"` to test that your solution does not use busy-waiting and properly synchronizes your threads.


## MP Requirements (Parts 3) - Wallet Server

You will implement a server for your wallet in the `create_wallet_server` function of `wallet_server.c`, which has the following requirements:

1. Your server has **exactly one wallet that is shared among all threads/connections** made to your server for as long as your server is running.

2. Your server must listen incoming connections on the provided `port`, and each incoming connection must be then handled by a dedicated thread.

3. The protocol for the wallet server has three different types of messages.  All messages are terminated with a newline (`\n`):

   * `GET <resource>\n` returns the current storage of `<resource>` in the wallet.

   * `MOD <resource> <delta>\n` modifies the `<resource>` by `<delta>`.
       * We guarantee that the resource name will never have a space character in it; that might be useful for parsing. 

   * `EXIT\n` closes the socket connection.

4. As a response to both `GET` and `MOD` messages, you server must return the resources in the wallet after the request is satisfied as a plain text number followed by a new line (`\n`).  For example, the application flow may be:

```
> GET cookie\n
0\n

> MOD cookie 5\n
5\n

> MOD cookie -10\n
(no response, until another thread adds to cookie since we cannot have debt)
```

Finally, your server **SHOULD** ignore all `\r` characters.  Just skip them.  (This will help you debug, but is not a hard requirement and is not tested.  However, telnet will add `\r` characters on some systems as part of the newline sequence.)


<div class="alert alert-info">
<h4>macOS Specific - Docker required</h4>

<p>
The test cases for "Part 3" do not work correctly on some versions of macOS.  You need to use docker to test your program (see below).
</p>

<p>
Specifically, we use <code>lsof</code> to detect when you server is up and running.  In Linux, the program works great.  macOS's <code>lsof</code> utility program does not return when we check to ensure your server is running in our test cases (the test case gets stuck at <code>tests/test-wallet-server.c:137</code>).
</p>

<p>
If you find a fix for it natively on macOS, please let us know! :)  Otherwise, docker is verified to work (see docker details below).
</p>

</div>


### Debugging

To test your implementation, run `make` to create the  `wallet-server` executable. Run it to spin up an instance of your wallet server by running `./wallet-server -p34000` (this launches it on port `34000`, no space between `p` and the port number). You can then use telnet to communicate with the server with the following command `telnet localhost 34000`. This will put you in a telnet environment, which allows you to communicate with your server.

Once you run `telnet localhost 34000`, try the following sequence (`>` denotes the telnet prompt, and you type only what appears after the `>`):

```
> GET midnights
0
> MOD midnights 5
5
> GET midnights
5
> MOD midnights 5
10
EXIT
```

To test if you are multithreading correctly, you can open two terminal instances, connect both using the telnet command and then modify a resource negatively in one, and positively in another!  If you have any more questions about debugging, come and chat with us in the discord or office hours!

Note: to exit telnet you need to type `CTRL-[`, which exits the connection and places you into telnet mode which you can then exit by entering `quit`. If you have `netcat` on your machine, you can use that instead by using `nc localhost 34000`, where you can exit via a normal `CTRL-C`. 


### Running Tests

- Run `make test` to compile and build the test suite.
- Run `./test "[part=3]"` to run the `wallet-server` tests.



## Modifiable Files

In your solution, you must only modify the following files.  Modifications of other files may break things:
- `wallet.c`
- `wallet.h`
- `wallet_server.c`
- `wallet_server.h`



## Submission and Grading


### Memory Correctness

For full credit, your MP must run "valgrind clean".

- On Windows/WSL, you can directly run valgrind with `valgrind ./test`.
- On macOS, you can run valgrind via a docker container.

<div class="alert alert-info">
<h4>Running valgrind via Docker (required on macOS)</h4>

The first time only, you need to build the docker when you're in your <b>mp4 directory</b>:
<pre>
docker build -t cs340  .
</pre>

After the the container is built, run the container <b>in your mp4 directory</b>.  This new container now provides you a bash prompt.
<pre>
docker run -p 34000:34000 --rm -it -v "`pwd`:/mp4" cs340
</pre>

Once you're into the container, you will need to `cd mp4` and then you can run the terminal commands directly! :)
<pre>
cd mp4
make clean
make
make test
./test
</pre>

</div>

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
- Choose "mp4 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!



### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  26.67 (scaled from 100 in the GitHub Action) |
| valgrind    |  13.33 (scaled from 50 in the GitHub Action) |
