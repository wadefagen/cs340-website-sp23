---
title: Versioned State Server

date: 2020-11-05
due: 2020-11-12 08:00:00

points: 25

number: 6
published: false
---

# Overview

Maintaining the state of a system is an important aspect of many web services.  For this MP, you will build a simple version-based state server using HTTP actions.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp6 -m "Merging initial files"
```


## Machine Problem

### Overview

Your web server must store key-value pairs of data and maintain a version number for each key-value.  An example of a possible use-case for this MP is as follows:

- You add the key/value pair `date="2020-11-05"` (the day the MP is released) to the server via a `PUT /date` request with the content `2020-11-05`.
- Since this is the first time the key `date` has been added, it is version `1`.
- You now add the key/value `date="2020-11-12"` (the day the MP is due) to the server via another `PUT /date` request with the content `2020-11-12`.
- Since this is the second time the key `date` has been added, it is version `2`.
- You request the value contained in `date` via a `GET /date` request.  The data returned is a JSON with the latest version, which is: `{ value: "2020-11-12", version: 2 }`.
- You request "Version 1" of the content via a `GET /date/1` request.  The data returned is: `{ value: "2020-11-05", version: 1 }`.
- You request "Version 3" of the content via a `GET /date/3` request.  Since there is no version 3, the server returns an `HTTP/404 Not Found` response.
- You delete the `date` key, and all versions of it, from the server via a `DELETE /date` request.  (Future versions of `date` must begin back at 1, there should be no history of `date` ever existing after being deleted.)


### Requirements

In this MP, a nearly blank Python file is provided for `app.py`.  Use your skills from MP4, MP5, or provided MP5 courses-microservice as a starting point if you're uncertain about creating a flask server from an empty file.


#### `PUT /<key>` -- Adds a versioned object

- Request: `PUT /<key>`
- Action: Adds the content of the request as a versioned value for the key `<key>`.  You should assume the content is a UTF-8 string.  The first version of the data is version `1`, second version is `2`, and so-on.
- Returns: `HTTP/200` if successful.
- Notes:
  - In `flask`, you can get the contents of the request as a UTF-8 string with the following code: `value = request.data.decode("utf-8")`.


#### `GET /<key>` -- Retrieves the latest version of a key

- Request: `GET /<key>`
- Action: Returns a JSON containing the latest value stored for the key as `value` (string) and the version number as `version` (number).
- Returns: `HTTP/200` if successful, or `HTTP/404` if the key was not found.
- Example JSON: `{ value: "2020-11-12", version: 2 }`


#### `GET /<key>/<version>` -- Retrieves a specific version of a key

- Request: `GET /<key>/<version>`
- Action: Same as `GET /<key>`, except requesting a specific version instead of the latest.
- Returns: `HTTP/200` if successful, or `HTTP/404` if the key or version was not found.


#### `DELETE /<key>` -- Completely deletes a key

- Request: `DELETE /<key>`
- Action: Deletes a key and all versions of the key. No record of the key ever existing should remain. Future versions of the key begin again at `1`.
- Returns: `HTTP/200` if successful.



### Running Your Server

As usual, launch the `flask` app using the standard command:

```
python -m flask run
```

We have provided a test script, `test.py`, which you can run while the server is running:

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