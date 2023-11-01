---
title: Versioned State Server

date: 2020-11-12
due: 2020-11-19 08:00:00

points: 25

number: 6.2
published: false
---

# Overview

In this MP, you will expand on MP6 to use external data stores including redis, mongodb, and (optionally) mysql.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp6-part2 -m "Merging initial files"
```


## Machine Problem

### Overview

In this MP, you will transition your code from using a local data store to an external data store:

- In the `redis-kv` directory, implement your server using a redis data store.
- In the `mongodb-nosql` directory, implement your server using a MongoDB data store.
- For extra credit, in the `mysql-sql` directory,  implement your server using a MySQL data store.

For each of these, you may want to copy/paste your `mp6` solution into the directory as a starting point.  The flask routes should remain the same, but the functionality will almost certainly change significantly.


### Redis Key-Value Store

To transition your code to a `redis` data store, you will want to install the `redis` Python library using either:

- `pip install redis` or `conda install redis`


Additionally, you will want to run a `redis` server to connect to.  The quickest way to get one running is to use docker:

- `docker run --rm -it -p 6379:6379 redis`


The documentation for `redis` can be found here: [https://github.com/andymccurdy/redis-py](https://github.com/andymccurdy/redis-py).

- I found `.get`, `.set` and `.delete` as useful functions.


### MongoDB NoSQL Data Store

To transition your code to a `mongodb` data store, you will want to install the `mongodb` Python library using either:

- `pip install pymongo` or `conda install pymongo`


Additionally, you will want to run a `mongodb` server to connect to.  The quickest way to get one running is to use docker:

- `docker run --rm -it -p 27017:27017 mongo`


The documentation for `mongodb` can be found here: [https://api.mongodb.com/python/current/api/pymongo/index.html#module-pymongo](https://api.mongodb.com/python/current/api/pymongo/index.html#module-pymongo)

- When using a NoSQL database, you do not need to "create" a database.
- Every database is made up of "collections".  Similarly, you will need to "create" a collection.  Using a collection will create it!
- You can use just one collection for the entire MP, or one collection per key, and both designs are great.


### Extra Credit: SQL Data Store

If you want even more challenge, create a SQL data store for this MP!  This is the most complex and more difficult route.  I'll leave the rest for you! :)



### Running Your Server (Same as MP6)

For each server, as usual, launch the `flask` app using the standard command:

```
python -m flask run
```

For each server, we have also provided the same test script, `test.py`, which you can run while the server is running:

```
python test.py
```

If all tests pass successfully, you will see no output.  If any assertion errors occur, you can inspect the test case to find out what was tested.



## Submit

When you have completed your program, double-check that your server runs as expected.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-fa20](https://github-dev.cs.illinois.edu/cs240-fa20)