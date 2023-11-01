---
title: getaway.html

date: 2023-02-28
due: 2023-03-07 23:59:59

points: 40

number: 5
published: true
---

# Overview

The Hyper-Text Transfer Protocol (HTTP) -- [defined in RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616) and other later specifications -- is **the** fundamental protocol for transferring data on the World Wide Web (i.e. Internet). It is an application-level protocol which through the use of its request methods, error codes and headers is widely used for various different applications.

HTTP is a Client-Server protocol, where the Client sends a request to the Server which processes the request and returns appropriate response.  In this MP, you will:

- Dive into the HTTP protocol to understand the technical design of HTTP request and response packets.
- Write an HTTP server that responds to HTTP requests (from web browsers like Chrome, command line utilities like `curl`, and anything else that "speaks" HTTP).
- Parse HTTP headers into key-value pairs.
- Have a foundational understanding of how libraries parse HTTP requests for building web services in the future.


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp5 --allow-unrelated-histories -m "Merging initial files"
```

# Implementation

We've split up the implementation into three parts:

1. Parsing an HTTP request packet for the request and headers (as a string, without needing to worry about the socket code).  This will include all of `http.c` **except** `httprequest_read`.
2. Reading an HTTP request over a socket (the `httprequest_read` function).
3. Building a web server using your `httprequest` code that can be used with Chrome or other web browsers.


## Modifiable Files

In your solution, you must only modify the following files.  Modifications of other files may break things:

- `http.c`
- `http.h`
- `server.c`


## Part 1: Parsing an HTTP request

Implement the API provided in `http.{c,h}`, excluding `httprequest_read`.  The main function in this part is `httprequest_parse_headers`, which must populate the provided `HTTPRequest *req` data structure with the contents from `char *buffer`.

The other functions include:
- `httprequest_get_action`, to get the action verb (ex: `GET`) from the HTTP request,
- `httprequest_get_path`, to get the path (ex: `/`) from the HTTP request,
- `httprequest_get_header`, to get a value for a specific header (ex: `Host` -> `localhost`), and
- `httprequest_destroy`, to free any memory stored by an `HTTPRequest` struct

While working on this part:

- You can (and will have to) add to the `HTTPRequest` struct in `http.h` (possibly also creating other structs in there, too).
- You must populate the `action`, `path`, `version` fields in `HTTPRequest` while parsing the packet as part of `httprequest_parse_headers`.


### Testing Part 1

We have provided a simple test suite to test the correctness of your parsing logic:

- In your terminal, type `make test` to compile the test suite.
- Run `./test "[part=1]"` to run the tests that have been tagged with `[part=1]` (covering this portion of the MP).



## Part 2: Reading an HTTP request from a socket

Complete `httprequest_read`.  This function is called with a file descriptor where you must `read` to read the contents of the request.

- You should use your `httprequest_parse_headers` to parse the headers of the request.
- The `Content-Length` header is a special HTTP header that will help you out to read the `payload` of the request.

### Testing Part 2

We have provided a simple test suite to test the correctness of your parsing logic.  The first five tests are identical to part 1, except that they're now delivered via the `sockfd` file descriptor instead of as a string.  The final test tests if your code can read the payload of a requests.

- In your terminal, type `make test` to compile the test suite.
- Run `./test "[part=2]"` to run the tests that have been tagged with `[part=2]` (covering this portion of the MP).


## Part 3: Building a Web Server

In Lecture, you saw a simple socket-based web server.  We've extended the server to launch a new thread for each incoming connection (`client_thread`).

In the `client_thread` function in `server.c`:

- You must read an HTTP request from the `fd` (use your `httprequest_read`).
- You must create an HTTP response to respond to the request.
    * If the requested path is `/`, you should process that request as if the path is `/index.html`.
    * If the file does not exist in your `static` directory (excluding the `/`), you must respond with a `404 Not Found` response.
    * If the file requested does exist, you will respond with a `200 OK` packet and:
        - Return the contents of the file as the payload,
        - If the file name ends in `.png`, the `Content-Type` header must be set to `image/png`.
        - If the file name ends in `.html`, the `Content-Type` header must be set to `text/html`.
- You can (and probably should, to make it easier for you) `close(fd)` after responding to the request.  This will ensure the browser opens a new socket (and you will have a new thread) when making another request.  *(You can continue to re-use this same socket and keep the connection alive, but this is not required.)*

### Testing Part 3

You will test Part 3 **using your favorite web browser**.

- Compile your server with `make`
- Launch your server using `./server 34000` (or any other port number)
- Visit [http://localhost:34000/](http://localhost:34000/)
    - ...and [http://localhost:34000/getaway.html](http://localhost:34000/getaway.html)
    - ...and [http://localhost:34000/340.png](http://localhost:34000/340.png)

If you can see the pages and images, you just made your first static web server! 🎉

There are a few tests `./test "[part=3]"` to verify it works by code -- but that's not really the point of this part.



# Submission and Grading


## Memory Correctness

For full credit, your MP must run "valgrind clean".

- On Windows/WSL, you can directly run valgrind with `valgrind ./test`.
- On macOS, you can run valgrind via a docker container.

<div class="alert alert-info">
<h4>Running valgrind via Docker (required on macOS)</h4>

The first time only, you need to build the docker when you're in your <b>mp5 directory</b>:
<pre>
docker build -t cs340  .
</pre>

After the the container is built, run the container <b>in your mp5 directory</b>.  This new container now provides you a bash prompt.
<pre>
docker run -p 34000:34000 --rm -it -v "`pwd`:/mp5" cs340
</pre>

Once you're into the container, you will need to `cd mp5` and then you can run the terminal commands directly! :)
<pre>
cd mp5
make clean
make
make test
./test
</pre>

</div>

## Submission

Once you have locally passed all the tests, you will need to submit and grade your code.  First commit using the standard git commands:

```
git add -A
git commit -m "MP submission"
git push
```


## Grading

The initial grading is done via a manual GitHub Action.  You **MUST** complete this step before the deadline to earn any points for the MP:

- Navigate to your repository on [https://github.com/cs340-illinois](https://github.com/cs340-illinois).
- Click on the "Action" Tab
- Choose "mp5 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!



## Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  26.67 (scaled from 100 in the GitHub Action) |
| valgrind    |  13.33 (scaled from 50 in the GitHub Action) |
