---
title: PNG Microservice

date: 2020-10-06
due: 2020-10-13 08:00:00

points: 25

number: 4
published: false
---

# Overview

Our next MP has you beginning to explore Python and beginning to work towards cloud-based services.  Specifically, you will create a simple API on top of your `mp2` program to create a web-based microservice that will extract a hidden GIF from a PNG image.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp4 -m "Merging initial files"
```


## Machine Problem

Complete the `app.py` program using the Python `flask` library to create a web-based service accepts a PNG file (as `png` in the `POST` data) via a HTTP `POST` request `/extract`:

- If the PNG file has a hidden GIF image (as defined by mp2), the hidden GIF file is returned.
- If the PNG file does not have a hidden GIF image (or is an invalid PNG file), an `HTTP 500` response is returned with some useful status text.


### Installing Python

If you do not already have Python installed, make sure to install the latest version of Python (ex: 3.8).  Any distribution is fine; my personal favorite is [miniconda](https://docs.conda.io/en/latest/miniconda.html).  You may also need to install `flask` with `conda install flask` after installing miniconda.




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

- `python -m flask run` (or `python3 -m flask run` on some systems, or just `flask run`) to launch your `app.py` flask server.
- Follow the instructions on the console to view your microservice in your web browser.

Now you need to complete the `extract_hidden_gif` to make your microservice work! :)


### Using Your MP2

As part of the `extract_hidden_gif`, you will need to use your mp2 `png-extractGIF` program.  Relative to your flask server, this program should be located at `../mp2/png-extractGIF`.  You may want to verify that your `png-extractGIF` program is complete and functional by running the following commands from your mp4 directory:

- Testing using a PNG with a hidden GIF: `../mp2/png-extractGIF sample/waf.png taylor.gif`.  (*This should successfully save `taylor.gif`.*)
- Testing using a PNG without a `uiuc` chunk: `../mp2/png-extractGIF sample/no-uiuc-chunk.png nothing.gif`.  (*This should run, not crash, and probably return a non-zero value from `main`.*)

You may need to modify your MP2 program slightly to fix any bugs to ensure your program can work with your new microservice.


### Useful Python Functions

There will be a few tasks you will need to complete in Python:

- You will need to save the contents of the `POST` request (the `.png` file).  I **strongly recommended** you save it within the `temp` directory in your MP4 folder with a unique file name.  *(The `temp` directory has been configured to be ignored by git via the provided `.gitignore` file.)*

    - The contents of the data sent via `POST` is stored by flask in the variable `request.files['png']`.
    - You can find many examples of reading and writing files in Python via a search.

- You will need to programmatically run your `../mp2/png-extractGIF` program.  To do this, you will likely use the python [`os.system` function](https://docs.python.org/3/library/os.html#os.system).

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

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-fa20](https://github-dev.cs.illinois.edu/cs240-fa20)