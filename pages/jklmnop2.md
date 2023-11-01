---
title: "JKLMNOP #2 - Redis"

layout: content

---

# JKLMNOP #2

<hr>

### Books on Redis

On a redis server, I have uploaded the entire text of "Alice in Wonderland".  Inside of the text, there are a few hints on how to earn some extra credit.  **Go find it! :)**

- The redis server is `fa22-cs340-adm.cs.illinois.edu` (using the default redis port `6379`).

- You must authenticate to access the server:

    - Your username is your **NetID** (lowercase)
    - Your password will be the **MD5 hash of your username** (lowercase)

- The keys you have access to with your username will **ALWAYS** be prefixed with `username:`.

- The first page of your book is called `start`.  If you were `waf`, the key to the first page is: `waf:start` (yours will be different, do not use `waf`).

- Read the contents of each key to find out more.  The lecture code from Lecture #19, or the official <a href="https://redis-py.readthedocs.io/en/stable/">redis-py documentation</a>, or any guides you find online will help you access the redis datastore.

Have fun!!