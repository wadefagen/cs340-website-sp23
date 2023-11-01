---
title: Resource Manager

date: 2021-03-08
due: 2021-03-15 23:59:59

points: 25

number: 5
published: false
---

# Overview

A classic game of the Internet-era is the game "Cookie Clicker".  Cookie Clicker is the "genre-defining" game of "incremental games", where you have something that generates a resource in the background.  With tasks being done in the background, this sounds like a perfect fit for CS 240!

For this MP, you will program a wallet that will hold a collection of resources.  A user will interact with your wallet by adding or subtracting resources from it, except that you must not allow the wallet to ever go negative -- debt is not allowed!

We'll theme this wallet around building a degree (🎓), but these resources may be CPUs needed for a cluster job, available RAM for a large compute job, or other system resources.  However, it's both simpler and more fun with emojis!



## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp5 -m "Merging initial files"
```



## Requirements

You will implement the "wallet library", which has only a few basic requirements:

1. Your wallet initially starts empty, with 0 of all resources.
2. Your wallet can accept any resource (identified by a string).  The user of your library will call `wallet_change_resource` and supply a `delta` to change your resource by a certain `delta`.
3. You must **NEVER** let your wallet go negative.  You must not return from `wallet_change_resource` until you can satisfy the request (another thead must add to your wallet).
4. You may have multiple wallets at once.  All state of your wallet must be maintained in your `wallet_t`.
5. Finally, and most importantly, many threads will be accessing your wallet(s) at the same time.  You must ensure the data in your wallet is correct by ensuring accesses to your wallet are properly synchronized.


### Implementation

You will find your four functions -- `wallet_init`, `wallet_get`, `wallet_change_resource`, and `wallet_destroy` -- in `lib/wallet.c`.  Additionally, the `wallet_t` struct is defined in `lib/wallet.h`.

A simple tester is provided in `degree.c`, which builds your degree by a series of worker threads including:
- A clover field that constantly produces ☘️ (and an occasional 🍀),
- A workshop that constantly produces 🧰 (and an occasional 💎),
- An orchard that constantly produces 🍏,
- A research job that consumes 🧰 and 🧬 to create a 📙,
- And a bunch of other jobs (you can check out the `degree.c`) to eventually create just a single 🎓!

The degree tester is a simple test case.  If you want to nerd out with an additional emoji creation, **feel free** to share additional testers on Slack and have fun with your crazy creations! :)


## Submit

When you have completed your program, double-check your code works and submit it:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)