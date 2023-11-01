---
title: Come Back... Be Cached (Mandelbrot's Version)

date: 2023-03-28
due: 2023-04-04 23:59:59

points: 40

number: 8
published: true
---

# Overview

In lecture, you learned about using Docker containers as a way to quickly build using new technologies and the usefulness of cloud-object storage.  In this MP, you will:

- Explore a graphical rendering of the [Mandelbrot set](https://en.wikipedia.org/wiki/Mandelbrot_set)
- Utilize Docker to launch an S3-compatible object storage (`MinIO`) via docker
- Use the AWS `boto3` library for accessing your object storage
- Use a provided backend Mandelbrot set microservice to complete a program to explore the Mandelbrot Set


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp8 --allow-unrelated-histories -m "Merging initial files"
```


## Machine Problem

### Part 1: The Mandelbrot Microservice

In `mp8/mandelbrot_microservice`, you will find a fully complete Mandelbrot microservice.  This microservice will render an image of the Mandelbrot set based on six parameters you provide:

- The [Matplotlib `colormap`](https://matplotlib.org/stable/tutorials/colors/colormaps.html),
- The center point (`real`, `imag`),
- The unit height (`height`),
- The render dimensions (`dim`), and
- The maximum iterations of the Mandelbrot set (`iter`)

Specifically, these parameters make up the endpoint of the image in the following format:

```
/mandelbrot/<colormap>/<real>:<imag>:<height>:<dim>:<iter>
```

<img src="img/0.0_0.0_3.0_512_64.png" style="float: right; border: solid 1px black; margin-left: 10px; margin-bottom: 10px; max-width: 20%;">

For example, a very zoomed out view (`height=3`) of the Mandelbrot set centered at `real=0` + `imag=0` (*0+0i*) and rendered as a `dim=256` (256px x 256px) using the (`colormap=twilight_shifted`) and `iter=64` iterations has the following endpoint:

```
/mandelbrot/twilight_shifted/0.0:0.0:3.0:512:64
```

<div style="clear: both"></div><img src="img/0.4_0.0_0.4_512_64.png" style="float: right; border: solid 1px black; margin-left: 10px; margin-bottom: 10px; max-width: 20%;">
By shifting the center to the right (`real=0.4`) and zooming in (`height=0.4`), we can see the detail in the boundary:

<pre><code>/mandelbrot/twilight_shifted/<u>0.4</u>:0.0:<u>0.4</u>:512:64</code></pre>

<div style="clear: both"></div>

It's your turn -- run the `mp8/mandelbrot_microservice` right now by going into the `mp8/mandelbrot_microservice` directory, start the flask app (`py -m flask run`), and continue the journey by viewing the following links:

<style>
li a u {
    background-color: #ffd;
}
</style>

- Shifting a bit more and zooming in even further:
    - [<tt>http://127.0.0.1:34000/mandelbrot/twilight_shifted/<u>0.36</u>:<u>-0.09</u>:<u>0.01</u>:512:64</tt>](http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.01:512:64)
- View more details by changing the iterations from 64 to `iter=128`:
    - [<tt>http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.01:512:<u>128</u></tt>](http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.01:512:128)
- Add even more detail by going to `iter=256`:
    - [<tt>http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.01:512:<u>256</u></tt>](http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.01:512:256)
- Zooming in by 10x to `height=0.001`:
    - [<tt>http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:<u>0.001</u>:512:256</tt>](http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.001:512:256)
- Zooming in another 10x to `height=0.0001`:
    - [<tt>http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:<u>0.0001</u>:512:256</tt>](http://127.0.0.1:34000/mandelbrot/twilight_shifted/0.36:-0.09:0.0001:512:256)
- Changing the colors to `colormap=inferno`:
    - [<tt>http://127.0.0.1:34000/mandelbrot/<u>inferno</u>/0.36:-0.09:0.0001:512:256</tt>](http://127.0.0.1:34000/mandelbrot/inferno/0.36:-0.09:0.0001:512:256)

You will notice that **loading each image is VERY slow**.  The rest of this MP will have you work with exploring this API via a simple frontend that has been provided for you and caching the images that have been previously rendered using an object storage system using `MinIO`.

There's one last thing to do with the provided Mandelbrot microservice:

- Play around with the URL until you find a starting point you find visually interesting!  The `height` should be less than or equal to `0.01`, but every other parameter is up to you! :)
- Make sure to record your starting point parameters.  You'll need them for Part 3!

You **MUST** have this microservice running **before running your MP**.  The test suite will launch this microservice automatically.


### Part 2: Launch a Docker Container using MinIO

`MinIO` is an open-source object storage system that maintains a API compatibility with S3.

- Since `MinIO` maintains API compatibility with S3, you will use the AWS S3 library `boto3`.
- To connect to your local `MinIO ` instance when using `boto3`, you'll use `endpoint_url="http://127.0.0.1:9000"` instead of the default value.  Otherwise everything else works identically as S3.

To launch `MinIO`, use the following `docker run` command:

```
docker run -it --rm -p 9000:9000 -p 9090:9090 --name minio -e "MINIO_ROOT_USER=ROOTNAME" -e "MINIO_ROOT_PASSWORD=CHANGEME123" quay.io/minio/minio server /data --console-address :9090
```

- This command will set your `aws_access_key_id` to `ROOTNAME`
- This command will set your `aws_secret_access_key` to `CHANGEME123`

You **MUST** have this docker running **before running or testing your MP**.


### Part 3: Complete the Mandelbrot Viewer Application

The final, and major part of this MP is the completion of the Mandelbrot Viewer application.  Unlike other applications you have built in CS 340, this application is a **stateful server**.  This means that your server must store the current location of your view of the Mandelbrot set internal to your app.  


#### Modifying Application State

Your applications starts at the initial position, height, and all other parameters discussed you found while exploring the Mandelbrot microservice in Part 1.  *(This will be different for everyone.)*  The following routes must be implemented to then dynamically modify the position:

- `POST /moveUp`, moves the center of the image up by 25% of the current `height`.
- `POST /moveDown`, moves the center of the image down by 25% of the current `height`.
- `POST /moveLeft`, moves the center of the image to the left by 25% of the current `height`.
- `POST /moveRight`, moves the center of the image to the right by 25% of the current `height`.
- `POST /zoomIn`, modifies the `height` by a factor of `1 / 1.4`.
- `POST /zoomOut`, modifies the `height` by a factor of `1.4`.
- `POST /smallerImage`, modifies the `dim` of the image by a factor of `1 / 1.25`.
- `POST /largerImage`, modifies the `dim` of the image by a factor of `1.25`.
- `POST /moreIterations`, modifies the `iter` of the image by a factor of `2`.
- `POST /lessIterations`, modifies the `iter` of the image by a factor of `1 / 2`.

Additionally, the `colormap` is changed by the following route:

- `POST /changeColorMap`, changes the `colormap` to be equal to the `colormap` value in the JSON in the request's body.  For example, a sample request's body to `POST /changeColorMap` is: `{"colormap": "inferno"}`.


#### Rendering the Mandelbrot image: `GET /mandelbrot`

The `GET /mandelbrot` route must render the an image of the Mandelbrot set based on the current state of your application.  In addition to returning the generated Mandelbrot image:

- Your application must use the MinIO object storage to cache all generated images.
- If the image has been cached, this function must return the image from the object storage instead of using the Mandelbrot microservice.
- This means the **ONLY** time the Mandelbrot microservice is called is if the request has never been made before since the MinIO instance has launched.

Since the Mandelbrot microservice takes some amount of time to render the image, the cached image will be much quicker than the Mandelbrot microservice.

This route must send the image as an `image/png` file (this is the same way the Mandelbrot microservice returns the image to your middleware) with a 200 response code.  If any errors occur, return a non-200 response and make certain **NOT** to cache the error in your object storage.  *(It should be very rare for a non-200 response to occur.)*

The boto3 guide on ["Uploading files"](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-uploading-files.html) may be helpful in working with MinIO.


#### Viewing All Stored Data: `GET /storage`

The `GET /storage` route must return a JSON of every image stored as a array of entries.  This must be done by querying your object storage (it must persist across restarting your application).  Each entry in the JSON this route returns must be a dictionary with two key-value pairs:

1. `key`, containing the unique route used for the Mandelbrot microservice route (ex: `inferno/0.36:-0.09:0.0001:512:256`) .
2. `image`, containing a base64-encoded PNG image binary data with the `data:image/png;base64,` prefix.

For example, a response to `GET /storage` may be:
```
[
  {"key": "twilight/-0.7405:0.1314:0.015:164:512", "image": "data:image/png;base64,iVBORw0K..." },
  {"key": "twilight/-0.7405:0.1314:0.0107:164:512", "image": "data:image/png;base64,iVBORw0K..." },
  {"key": "twilight/-0.7405:0.1314:0.0077:164:512", "image": "data:image/png;base64,iVBORw0K..." },
  ...
]
```

The boto3 guide on ["Downloading files"](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-example-download-file.html) may be helpful in working with MinIO.  The only other command you'll need, which we leave to you to discover, is how to list all files in your bucket. :)


#### Setting Default Values: `POST /resetTo`

The `POST /resetTo` must reset the parameters to the value specified by the JSON in the request's body.  The JSON will always have the following fields: `colormap`, `real`, `imag`, `height`, `dim`, `iter`.

For example, the body provided to `/resetTo` may be:
```
{
  "colormap": "twilight_shifted",
  "real": 0.0,
  "imag": 0.0,
  "height": 3.0,
  "dim": 512,
  "iter": 64
}
```


#### Retrieving the Server's State: `GET /getState`

The `GET /getState` must return the current state as a JSON in the same format as `/resetTo`.

For example, the response by `/getState` may be:
```
{
    "colormap": "cividis",
    "dim": 256,
    "height": 0.0003718688641637414,
    "imag": 0.126129,
    "iter": 512,
    "real": -0.7434051354938358
}
```


#### Clearing your Server's Image Cache: `GET /clearCache`

Finally, the `GET /clearCache` must completely clear the cache of images.  It must return `200 OK` on success.


## Testing Your Code

Run your application and visit it using a web browser.  This is the best way to test your code.  You will need three different services running to fully run your application:

1. Run the Mandelbrot microservice in `mp8/mandelbrot_microservice`,
2. Run the MinIO docker, and
3. Run the Mandelbrot Viewer application in `mp8` and visit [http://127.0.0.1:5000/](http://127.0.0.1:5000/)

### Testing Using `pytest`

An automated test suite is provided by running `py -m pytest`.

- You **MUST** have the MinIo docker running **BEFORE** running pytest.


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
- Choose "mp8 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!



### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  40 (scaled from the GitHub Action score) |