---
title: "JKLMNOP: Wonderland (Redis)"

date: 2023-04-01
due: 2023-04-05 23:59:59

points: "+2 EC"
ec: true

published: true
---

### JKLMNOP: Wonderland (Redis)

On a redis server, I have uploaded the entire text of "Alice in Wonderland" in keys within a redis data store.  Inside of the text, there are a few hints on how to earn some extra credit.  **Go find it! :)**

- The redis server is `sp23-cs340-adm.cs.illinois.edu` (using the default redis port `6379`).

- You must authenticate to access the server redis server:

    - Your username is your **NetID** (lowercase)
    - Your password will be the **MD5 hash of your username** (lowercase)

- The keys you have access to with your username will **ALWAYS** be prefixed with `username:`.

- The first page of your book is called `start`.  If you username was `waf`, the key to the first page is: `waf:start` (yours will be different, do not use `waf`).

- Read the contents of each key to find out more.  The lecture code from Lecture #19, or the official <a href="https://redis-py.readthedocs.io/en/stable/">redis-py documentation</a>, or any guides you find online will help you access the redis datastore.

Have fun!!