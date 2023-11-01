---
title: Dancing with Our C Code

date: 2023-03-21
due: 2023-03-28 23:59:59

points: 40
jklmnop: ./jklmnop.html

number: 7
published: true
---

# Overview

One major aspect of systems programming is the integration of many pieces of software to work together as one "system".  In MP7: "Dancing with Our C Code", you will create a web service that "wraps" your `mp2` solution to create a web-based microservice that will extracts hidden GIFs from within PNGs.

To complete this VM, you will deploy your service to your CS 340 virtual machine.


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp7 --allow-unrelated-histories -m "Merging initial files"
```


## Part 1: Flask Implementation

### Machine Problem

Complete the `app.py` program using the Python `flask` library to create a web-based service that has two different routes. 


#### Extract Hidden GIF, `POST /extract`

Complete the `POST /extract` route to extract a hidden and return a hidden GIF.

- If the PNG file has a hidden GIF image (as defined by mp2), the hidden GIF file is returned.
- If the PNG file does not have a hidden GIF image, an `HTTP 415 Unsupported Media Type` status must be returned, along with some useful text for the end-user.
- If the PNG file is not a valid PNG file, an `HTTP 422 Unprocessable Content` must be returned, along with some useful text for the end-user.


#### Retrieve Previously Extracted GIF, `GET /extract/<image_num>`

Return a previously extracted hidden GIF, based on the `image_num` index.

- If the `<image_num>`th GIF exists in your local cache, the `<image_num>`th hidden GIF file is returned.
- If the `flask` program has not received `<image_num>` GIFs, a `HTTP 404: Not Found` response must be returned.
- The `<image_num>` cache must save the first extracted image as index `0`.


### Using Your MP2

As part of the `POST /extract` route, you will need to use your mp2 `png-extractGIF` program. You may want to verify that your `png-extractGIF` program is complete and functional and that you can run the following commands:

- We have provided a `Makefile` in the MP7 directory so you can use `make` in `mp7` to compile `png-extractGIF` into your `mp7` directory.  This uses your code from your `mp2` directory.
    * You should verify that your `./png-extractGIF` works before trying to use it in Python.
    * Here's a command to test that: `./png-extractGIF sample/waf.png taylor.gif`.  (*This should successfully save `taylor.gif`.*)

Additionally, you should verify your MP2 solution returns unique values for `main` for the different features you need to identify in Python:
- An successfully extracted GIF should return a `0` (the convention for successful execution),
- An invalid PNG should return a non-zero value, **AND**
- A valid PNG, but without a `uiuc` chunk, should return a different non-zero value.

You may need to modify your MP2 program slightly to fix any bugs to ensure your program can work with your new microservice.  


### Useful Python Functions

There will be a few tasks you will need to complete in Python:

- You will need to save the contents of the `POST` request (the `.png` file).  If you choose to make a directory, the provided `.gitignore` will ignore a directory called `temp` (and avoid it ever cluttering up your GitHub).  Some useful hints:
    - You can use the python [`os.makedirs` function](https://docs.python.org/3/library/os.html#os.makedirs).
    - The contents of the data sent via `POST` is stored by flask in the variable `request.files['png']`.

- You will need to programmatically run your `./png-extractGIF` program.  To do this, you will likely use the python [`os.system` function](https://docs.python.org/3/library/os.html#os.system).  This function will return the **exit value** from `png-extractGIF`.  If you followed our code style, the exit value should be `0` on success and non-zero on failure.

- Once you have the GIF file saved, you will likely want to use flask's [`send_file` function](https://flask.palletsprojects.com/en/1.1.x/api/#flask.send_file) to send the GIF file.

- If anything goes wrong, you should `return "Error Message", 500` to return an error (but change "Error Message" to something useful).


### Manually Testing Your Microservice

Launch your service by starting your flask application:

- Windows: `py -m flask run`
- macOS: `python3 -m flask run`


Then, you can test your program in two different ways:

1. By using your web browser and visiting your flask server (ex: `http://127.0.0.1:5000/`).

2. By the command line, using `curl` to make a request to your web server: `curl -f -o output.gif http://localhost:5000/extract -F "png=@sample/waf.png"`

    - You can replace `@sample/waf.png` with another file.  Ensure that you keep the `@` symbol to tell `curl` to send the contents of the file.
    - You should inspect `output.gif` to ensure the extraction was successful.
    - Make sure that you get an error when sending it an invalid PNG file.
    - Make sure you also get an error when sending it a PNG file without a hidden `uiuc` chunk.


### Pytest Test Suite

Finally, a `pytest` suite is provided for you.

- All of the `test_flask.py` test cases tests your code locally (it uses `make` to build your `mp2`, and requires your `mp2` to be working).
- All of the `test_vm.py` test cases tests your VM deployment.

To run only the Part 1 test cases, run:

- Windows: `py -m pytest test_flask.py`
- macOS: `python3 -m pytest test_flask.py`


## Part 2: Deploying your MicroService

You have a Virtual Machine (VM) provided for you as part of being in CS 340.  This VM is similar to an AWS EC2 instance, except that it's on our "private cloud" instead of the AWS "public cloud".

- The VM interface and your VM can only be accessed **either on campus (using IllinoisNet)** OR via the **UIUC VPN client**.  If you are not on campus, [information about the UIUC VPN client is available here](https://answers.uillinois.edu/98773).

- EngrIt has created a ["Power On / Reset VM"](https://answers.uillinois.edu/illinois.engineering/page.php?id=108475) guide that details how to turn on your CS 340 VM.

- The interface you visit to "Power On" will give you the hostname for the machine that you have been assigned.  (It will be in the format `sp23-cs340-###.cs.illinois.edu`).

- Once you have powered on the your box following the "Power On / Reset VM" guide, you can SSH into it with your NetID and password.

- You will need to install any/all necessary libraries.  The OS provided is Ubuntu, and we leave it to you to set up the system in any way you want.


### Identifying your VM

We identify your VM for testing by the `NETID.txt` file.

- Modify `NETID.txt` to have your University of Illinois NetID.  *(Include only the NetID portion, **NOT** your full e-mail.)*
- If you fail to include this, you'll find all test cases will fail with the message `The NETID.txt file contains the NetID "netid", but no such entry can be mapped to a VM.`

### Deployment Requirements

At the time you run your GitHub Action for grading, you server must be running on your VM using port `5000` (the default flask port).  Additionally, your service must be accessible to other machines than just localhost.

- To ensure that your service is visible to all connections, run your flask application using `--host 0.0.0.0`.  For example: `python3 -m flask run --host 0.0.0.0`.



## Submission

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
- Choose "mp7 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!



### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  40 (scaled from the GitHub Action score) |