---
title: Versioned State Server

date: 2021-04-12
due: 2021-04-19 23:59:59

points: 25

number: 8
published: false
---

# Overview

Maintaining the state of a system is an important aspect of many web services.  For this MP, you will build a simple version-based state server using HTTP actions.


## Initial Files

To generate a new repo for this assignment, go to the following [link](https://classroom.github.com/a/n3WGMLY-) and accept the assignment. After a minute or so, refresh the page. You should see your new repo has been created. Click on the green button that says `Code` and copy the clone link.

In your CS 240 directory, clone your new directory with the following command, using the link you got from the step above:

```
git clone <link>
```

Once that is done, be sure to go to the `netid.txt` file and type in your netid. This is how we will associate your GitHub account with your school id. If you forget to do this step, you will see a test failing on GitHub classroom to remind you.


## Machine Problem

### Overview

Your web server must store key-value pairs of data and maintain a version number for each key-value.  An example of a possible use-case for this MP is as follows:

- You add the key/value pair `date="2020-11-05"` to the server via a `PUT /date` request with the content `2020-11-05`.
- Since this is the first time the key `date` has been added, it is version `1`.
- You now add the key/value `date="2020-11-12"` to the server via another `PUT /date` request with the content `2020-11-12`.
- Since this is the second time the key `date` has been added, it is version `2`.
- You request the value contained in `date` via a `GET /date` request.  The data returned is a JSON with the latest version, which is: `{ value: "2020-11-12", version: 2 }`.
- You request "Version 1" of the content via a `GET /date/1` request.  The data returned is: `{ value: "2020-11-05", version: 1 }`.
- You request "Version 3" of the content via a `GET /date/3` request.  Since there is no version 3, the server returns an `HTTP/404 Not Found` response.
- You delete the `date` key, and all versions of it, from the server via a `DELETE /date` request.  (Future versions of `date` must begin back at 1, there should be no history of `date` ever existing after being deleted.)


### Requirements

In this MP, a nearly blank Python file is provided for `app.py`.  Use your skills from MP6, MP7, or provided MP7 courses-microservice as a starting point if you're uncertain about creating a flask server from an empty file.


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



### Testing Your Code

You can test your code using `curl` commands in the terminal:

- PUT: `curl -X PUT localhost:5000/<Key Name> -d '<Key Value>' -H "Content-Type: text/plain"`
- GET: `curl localhost:5000/<Key Name>`
- GET (with version): `curl localhost:5000/<Key Name>/<Version>`
- DELETE: `curl -X DELETE localhost:5000/<Key Name>`



## Submit

When you have completed your program, double-check that your server runs as expected inside a Docker container.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there. 

Note: the way our test cases work for now is by storing them in a file in your repo, called `mp8-state-server-test.py`. If you modify this file, you may see incorrect results when we run our test cases on GitHub Classroom. At the assignment deadline, we will run our test cases on your repo using a fresh copy of `mp8-state-server-test.py`.