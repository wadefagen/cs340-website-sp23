---
title: Versioned State Server

date: 2022-03-25
due: 2022-04-04 23:59:59

points: 50

number: 8
published: false
---

# Overview

In lecture, you learned how cloud-based applications will use data stores that are not just in their local memory.  In this MP, you will write two solutions to the same problem:

- One solution you will store your data locally in memory (ex: any Python data structure you want),
- The other solution will store your data remotely in a MongoDB data store.

You will utilize a Docker container to launch a MongoDB instance and connect to it without installing it locally.  Finally, you will run this on your CS 240 VM.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp8 -m "Merging initial files"
```


## MP Overview Session

An MP Overview was held by Eunice on Monday, March 28 at 11:00am.

- Recording: [https://mediaspace.illinois.edu/media/t/1_szxeavd2](https://mediaspace.illinois.edu/media/t/1_szxeavd2)
- Slide: [https://courses.grainger.illinois.edu/cs240/sp2022/static/mp-overviews/MP8 Overview Session.pdf]({{site.baseurl}}/static/mp-overviews/MP8 Overview Session.pdf)


## Machine Problem

### Overview

Your web server must store key-value pairs of data and maintain a version number for each key-value.  An example of a possible use-case for this MP is as follows:

- You add the key/value pair `date="2021-11-05"` to the server via a `PUT /date` request with the content `2021-11-05`.
- Since this is the first time the key `date` has been added, it is version `1`.
- You now add the key/value `date="2021-11-12"` to the server via another `PUT /date` request with the content `2021-11-12`.
- Since this is the second time the key `date` has been added, it is version `2`.
- You request the value contained in `date` via a `GET /date` request.  The data returned is a JSON with the latest version, which is: `{ "value": "2021-11-12", "version": 2 }`.
- You request "Version 1" of the content via a `GET /date/1` request.  The data returned is: `{ "value": "2021-11-05", version: 1 }`.
- You request "Version 3" of the content via a `GET /date/3` request.  Since there is no version 3, the server returns an `HTTP/404 Not Found` response.
- You delete the `date` key, and all versions of it, from the server via a `DELETE /date` request.  (Future versions of `date` must begin back at 1, there should be no history of `date` ever existing after being deleted.)

### Requirements

In this MP, a nearly blank Python file is provided for `app.py`. You need to populate it to create a server that supports the following four API(s):

#### 1. `PUT /<key>` -- Adds a versioned object

- Request: `PUT /<key>`
- Action: Adds the content of the request as a versioned value for the key `<key>`.  You should assume the content is a UTF-8 string.  The first version of the data is version `1`, second version is `2`, and so-on.
- Returns: `HTTP/200` if successful.
- Notes:
  - In `flask`, you can get the contents of the request as a UTF-8 string with the following code: `value = request.data.decode("utf-8")`.

#### 2. `GET /<key>` -- Retrieves the latest version of a key

- Request: `GET /<key>`
- Action: Returns a JSON containing the latest value stored for the key as `value` (string) and the version number as `version` (number).
- Returns: `HTTP/200` if successful, or `HTTP/404` if the key was not found.
- Example JSON: `{ "value": "2021-11-12", "version": 2 }`

#### 3. `GET /<key>/<version>` -- Retrieves a specific version of a key

- Request: `GET /<key>/<version>`
- Action: Same as `GET /<key>`, except requesting a specific version instead of the latest.
- Returns: `HTTP/200` if successful, or `HTTP/404` if the key or version was not found.

#### 4. `DELETE /<key>` -- Completely deletes a key

- Request: `DELETE /<key>`
- Action: Deletes a key and all versions of the key. No record of the key ever existing should remain. Future versions of the key begin again at `1`.
- Returns: `HTTP/200` if successful.


### Implementation
The MP is divided into two parts. In part 1, you'll implement a versioned state store by using in-memory variables. In part 2, you'll use a `mongodb` data store to store your key and data.


## Part 1: Implementing a Versioned State Store
In this part, you'll complete the `app.py` in `local-store/` to implement a versioned state server.

### Running Your Server
As usual, launch the `flask` app in your `local-store` directory using the standard command:
```
python -m flask run
```

### Testing Your Code

You can debug your code by running your server and using `curl` commands in the terminal:

- PUT: `curl -X PUT localhost:5000/<Key Name> -d '<Key Value>' -H "Content-Type: text/plain"`
- GET: `curl localhost:5000/<Key Name>`
- GET (with version): `curl localhost:5000/<Key Name>/<Version>`
- DELETE: `curl -X DELETE localhost:5000/<Key Name>`

Alternatively, you can also use applications like Postman for sending HTTP requests.

You can run the test suite we provide by running `python -m pytest test_local.py`. 

<!--
The autograder tests can be run by running `pytest test_local.py` in `local-store` directory. Before running the tests, make sure your microservice is running and listening at http://localhost:5000/. As part of the autograder, we will execute the `launch-docker.sh` script to initially launch the docker container and then run the tests. You can update the commands in the `launch-docker.sh`, but make sure the script builds and runs the container correctly.
-->


## Part 2: Using MongoDB NoSQL Data Store

In this part, you'll complete the `app.py` in `mongodb-nosql/` to implement a versioned state server using MongoDB as your data store.

To transition your code to a `mongodb` data store, you will want to install the `mongodb` Python library using either:
- `python -m pip install pymongo` or `pip install pymongo`

Additionally, you will want to run a `mongodb` server to connect to.  The quickest way to get one running is to use docker:

- `docker run --rm -it -p 27017:27017 mongo`

The documentation for `mongodb` can be found here: [https://pymongo.readthedocs.io/en/stable/](https://pymongo.readthedocs.io/en/stable/)

- Every database is made up of "collections".  Similarly, you will need to "create" a collection.  Note, just by using a collection will create it!
- Your database **MUST** be named `mp8-state-server`.  This is important to allow us to verify the contents of the database. (The `app.py` already sets this value in `db = mongo["mp8-state-server"]`)
- You can use just one collection for the entire MP, or one collection per key, and both designs are great.

<!--
Note: Do not change the database name from `mp8-state-server`. We use this database to check if you are using the MongoDB collection.

Once you have completed this implementation, you'll package this as a docker container and complete the `Dockerfile`. Note that, the `mongodb` server would be running separately from your `server` and this dependency need not be part of your `Dockerfile`.
-->

### Testing Your Code

The tests for this part can be run using `curl` in the same way as the tests for the previous part.  However, you must now:
- Ensure the `mongodb` server is running as a Docker container,
- Launch your app in your `mongodb-nosql` directory.
- Run your curl tests.

You can run the test suite we provide by running `python -m pytest test_mongo.py`. 

<!--
The autograder tests can be run by running `pytest test_mongo.py` in `mongodb-nosql` directory. Before running the autograder tests, make sure your microservice is running and listening at http://localhost:5000/. As part of the autograder, we will execute the `launch-docker.sh` script to initially launch the docker container and then run the tests. You can update the commands in the `launch-docker.sh`, but make sure the script builds and runs the container correctly.
-->


## Submit

When you have completed your program, double-check that your server runs as expected. When you are ready, submit the code via the following git commands:

```
git add -u
git commit -m "MP submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there.