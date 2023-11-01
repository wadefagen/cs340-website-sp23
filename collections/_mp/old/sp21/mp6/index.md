---
title: PNG Microservice

date: 2021-03-22
due: 2021-03-29 23:59:59

points: 25

number: 6
published: false
---

# Overview

Our next MP has you beginning to explore Python and beginning to work towards cloud-based services.  Specifically, you will create a simple API on top of your `mp2` program to create a web-based microservice that will extract a hidden GIF from a PNG image.


## GitHub Classroom

For this MP and the remaining MPs of the class, we will again be trying out GitHub classroom. For the most part, your workflow will remain the same, with a few changes to how you get and submit your code.


## Initial Files

To generate a new repo for this assignment, go to the following [link](https://classroom.github.com/a/jWEyjzzR) and accept the assignment. After a minute or so, refresh the page. You should see your new repo has been created. Click on the green button that says `Code` and copy the clone link.

In your CS 240 directory, clone your new directory with the following command, using the link you got from the step above:

```
git clone <link>
```

Once that is done, be sure to go to the `netid.txt` file and type in your netid. This is how we will associate your GitHub account with your school id. If you forget to do this step, you will see a test failing on GitHub classroom to remind you.


## Machine Problem

Complete the `app.py` program using the Python `flask` library to create a web-based service accepts a PNG file (as `png` in the `POST` data) via a HTTP `POST` request `/extract`:

- If the PNG file has a hidden GIF image (as defined by mp2), the hidden GIF file is returned.
- If the PNG file does not have a hidden GIF image (or is an invalid PNG file), an `HTTP 500` response is returned with some useful status text.


### Installing Python

This MP requires a modern version of Python (ex: 3.8+).  If you are running on the VM, [follow our guide to getting your VM setup with Python]({{site.baseurl}}/resources/python/).

Once Python is installed, you will need to install the Python library.  Using native Python, this can be done via:
```
pip install flask
```


### Using `flask`

For this MP, the complete `flask` code is provided for you in the provided `app.py`:

```
from flask import Flask
app = Flask(__name__)

# Route for "/" for a web-based interface to this microservice:
@app.route('/')
def index():
  return render_template("index.html")

# Extract a hidden "uiuc" GIF from a PNG image:
@app.route('/extract', methods=["POST"])
def extract_hidden_gif():
  # ...your code here...
```

To run this program, run:

- `python3.9 -m flask run` (or `python3 -m flask run` on some systems, or `python -m flask run` or the shorthand `flask run`) to launch your `app.py` flask server.
- Follow the instructions on the console to view your microservice in your web browser.

Now you need to complete the `extract_hidden_gif` to make your microservice work! :)


### Using Your MP2

As part of the `extract_hidden_gif`, you will need to use your mp2 `png-extractGIF` program.  We have given you a new `png-extractGIF.c` file for this assignment. Copy over your code from mp2 into this file.  You may want to verify that your `png-extractGIF` program is complete and functional by running the following commands from your mp6 directory:

- Testing using a PNG with a hidden GIF: `./png-extractGIF sample/waf.png taylor.gif`.  (*This should successfully save `taylor.gif`.*)
- Testing using a PNG without a `uiuc` chunk: `./png-extractGIF sample/no-uiuc-chunk.png nothing.gif`.  (*This should run, not crash, and probably return a non-zero value from `main`.*)

You may need to modify your MP2 program slightly to fix any bugs to ensure your program can work with your new microservice.


### Useful Python Functions

There will be a few tasks you will need to complete in Python:

- You will need to save the contents of the `POST` request (the `.png` file).  We have given you a `temp` directory for this purpose, so you can save the contents in this directory with a unique filename.

    - The contents of the data sent via `POST` is stored by flask in the variable `request.files['png']`.
    - You can find many examples of reading and writing files in Python via a search.

- You will need to programmatically run your `./png-extractGIF` program.  To do this, you will likely use the python [`os.system` function](https://docs.python.org/3/library/os.html#os.system).

- Once you have the GIF file saved, you will likely want to use flask's [`send_file` function](https://flask.palletsprojects.com/en/1.1.x/api/#flask.send_file) to send the GIF file.

- If anything goes wrong, you can `return "Error Message", 500` to return an error (but change "Error Message" to something useful).


### Testing Your Microservice

You can test your program in two different ways:

1. By the command line, using `curl` to make a request to your web server: `curl -f -o output.gif http://localhost:5000/extract -F "png=@sample/waf.png"`

    - You can replace `@sample/waf.png` with another file.  Ensure that you keep the `@` symbol to tell `curl` to send the contents of the file.
    - You should inspect `output.gif` to ensure the extraction was successful.
    - Make sure that you get an error when sending it an invalid PNG file.
    - Make sure you also get an error when sending it a PNG file without a hidden `uiuc` chunk.

2. Or, by using your web browser and visiting your flask server (ex: `http://127.0.0.1:5000/`).



## Submit

When you have completed your program, double-check that your server runs as expected with a GIF image that has hidden data and a GIF image without hidden data.  When you are ready, submit the code via the following git commands:

Note: Please do not commit your compiled `extractGIF` file. Our tests will compile your code before running the tests, and we have seen that sometimes, including this file will actually make the tests fail because of permission errors. We will be sure to fix this issue with our tests before running the final grader!

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there. 

Note: the way our test cases work for now is by storing them in a file in your repo, called `mp6-flask-test.py`. If you modify this file, you may see incorrect results when we run our test cases on GitHub Classroom. At the assignment deadline, we will run our test cases on your repo using a fresh copy of `mp6-flask-test.py`.