---
title: Final Project - Week 3 (Art)

date: 2021-05-03
due: 2021-05-10 13:00:00

points: 70

layout: project

number: Week 3
published: false
---

# Final Project - Week 3

So far, you have explored two uses of MP8 beyond the scope of a single MP:

- You used MP8 to connect with real data stores, including redis, mongodb, and SQL.
- You communicated with an MP8 server that stored data about music and used the `music21` library to generate music files.
- In this final week, you will extend MP8 to store a history of artwork created by your server.

This final week of the project is **due at 1:00pm on Monday** instead of 11:59pm.  Your server must be running live by **1:30pm on Monday, May 10th** for the project showcase that will happen during CS 240's final exam period (Monday, May 10 at 11:59pm).


## Learning Objectives

- Explore using `flask` Blueprints to create a multi-file `flask` server.
- Extend an MP8 solution to include an artwork generating `POST` route.
- Choose between different design decisions and ensure you server works within your VM environment.


## Initial Files

In your CS 240 directory (same location you used for the github-dev MPs), merge the initial starting files with the following commands:

```
git fetch release
git merge release/project-part3 -m "Merging initial files"
```


## Overview

In this MP, you have been provided a complete MP8 server.  You can find this server in `mp8.py`.  As part of this file, notice the use of a `Blueprint` to allow the `app.py` file to use routes from several files:

```
mp8_server = Blueprint("mp8_server", __name__)
...
@mp8_server.route('/<key>', methods=["PUT"])
def put(key):
  ...
```

You will complete the `artwork_server` Blueprint found in `artwork.py`.  This Blueprint must:

1. Create a route to handle a `POST` request to `/<key>` containing an PNG image file as the content of the `POST`.  (The boilerplate is provided in `artwork.py` for you already.)
2. Modify the image in a visible way.
3. Store the image as a new version of the `/<key>` route, so that a future `GET` will return the PNG data.
4. Return the modified image, as a PNG file, as the response to the `POST` request.


### Step 1 - `POST` Route

See `artwork.py`.


### Step 2 -- Modify the Image

This is your first design decision that is completely up to you.  Modify the image using any Python library you want, in any way you want, making sure that the original image is at least somewhat still recognizable.

Here are a few examples of simple transformations -- but be creative and have fun!  Think of some of your favorite things you've seen or done with images both in the creative MPs in CS 225 and around the Internet:

<hr>
<table class="text-center">
  <tr>
    <td><img src="../1.jpg" class="img-fluid"><br>Original Image (<a href="https://www.instagram.com/p/CNTMzl4JKhH/">@illinois1867</a>)</td>
    <td><img src="../2.jpg" class="img-fluid"><br>Transform Orange</td>
    <td><img src="../3.jpg" class="img-fluid"><br>Gaussian Blur</td>
    <td><img src="../4.jpg" class="img-fluid"><br>Saturation</td>
  </tr>
</table>
<hr>

### Step 3 - Store the PNG image

Once you have modified the image, you need to store it in your MP8 server.  This is the second design decision on how you want to accomplish this, and there are at least two different ways:

- Make a `PUT` request to your own server (`localhost:5000`), using the `requests` library, sending the image as the data.  This will use your `PUT` route to capture this data.
- Modify `mp8.py` file to add an `manual_put` function (or similar) that receives the image as an argument to the function.
- ...or any other way!

A future `GET` request to the `<key>` needs to be able to retrieve the image data.

<div class="alert alert-primary">
  You can modify the `mp8.py` code in any way you want.  The `GET` request must return **some sort of response** response (it could return the image itself, or the image encoded in JSON, or other response) but we did not specify the exact format of the response.
</div>


### Step 4 - Return the Modified Image

In addition to storing the image, return the image as the response to the `POST` request.


## Running Your Program

This program **MUST run on your virtual machine**.  To test your program:

1. Run your program MP on your CS 240 virtual machine.

2. While your server is running and you are either on campus or connected via the VPN, visit <a href="http://sp21-cs240-adm.cs.illinois.edu:24001/">http://sp21-cs240-adm.cs.illinois.edu:24001/</a>

    - On the web form, add your server name (ex: `cs240-sp21-##`), choose an image from your computer, and (optionally) change the key used.
    - Submit the form and our server will make a `POST` request and show the returned image.
    - Use your web browser to make a `GET` request to ensure the image is added to the history.



## Submit

When you have completed your program, double-check that your server runs as expected.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)


### Showcase + Grading

For the course final exam period (on May 10 at 1:30pm), we will showcase all of your servers live -- and also send images through successively large batches of servers -- culminating with a single image passed through every single server.

Your grade for this project week will be based on your server functioning at the showcase on Monday, May 10th.