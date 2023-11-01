---
title: "1989 Mosaics - Final Project Presentation"

date: 2023-05-02
due: 2023-05-08 13:30:00

points: 130

layout: project

number: Week 4
published: true
---

<div class="alert alert-info" role="alert">
  <h3>Using v5+ Middleware</h3>

  <p>
    The middleware has been updated to v5 (or later) and these docs describe functionality that only works with v5+.  Make sure you are running the latest by running <code>git checkout main</code> (if you used the v4 tag) and <code>git pull</code> to grab the latest shared middleware.
  </p>
</div>


# Overview

In this final week, you will prepare for contributing to the course-wide 1989 mosaics on Monday, May 8 at 1:30pm in 1404 SC.

Before the session, you will need to update your code to work with the updated API discussed in lecture on Tuesday and detailed below, have at least 13 mosaic generators, and have everything running on your VM.

Just like in Week #3, you must use the shared middleware from [https://github.com/cs340-illinois/1989-shared-middleware](https://github.com/cs340-illinois/1989-shared-middleware) and must be running on your VM.

Optionally, for extra credit, you can continue to work with the class community to expand the shared middleware.  You must make code changes to the shared middleware via a [Pull Request](https://github.com/cs340-illinois/1989-shared-middleware/pulls) to the shared middleware repo.


## Part 1: API Updates

The v5 (and higher) middlewares now support the updated API based on the lecture conversation on Tuesday.  To work with the latest changes, you must make the following updates:

1. When connecting your MMG to the middleware via the `/addMMG` route, a new key `tileImageCount` must be provided.  The value of `tileImageCount` must be an integer and contain number of tiles that are used in that MMG.

2. When a mosaic is generated for the middleware, an additional parameter is now sent to the MMGs as part of the query string: `fileFormat`.  The `fileFormat` will always have the value of either `PNG` or `JPEG`.  *(This means you now get three parameters, `tilesAcross`, `renderedImageSize` and `fileFormat`).

3. Instead of returning a JSON list with a base64-encoded image when a mosaic is generated for the middleware, your MMGs must return the binary data of the image in the file format specified by `fileFormat`.  This can often be done by using the Flask `make_response(image)` to generate a response to return, where `image` is the binary buffer of your image data.

4. Your MMG must **NOT** return a non-mosaic if it fails for any reason.  Either return a mosaic with a `200` status code, or return a non-`200` code and an error message -- do not return some "default" image.

5. Finally, ensure your `author` name is the same across all your microservices (MMGs and reducer) and is something that we can identify you.

    - Important: Any MMGs that are exclusively made with Taylor Swift images, be sure to include `#taylor` in the title.  If you don't have any, that's okay -- but any/all that are 100% Taylor should have `#taylor`.

    - Feel free to start up other hash-tag themes on discord.  The plan is to have the ability to use just subsets of MMGs for some special themed mosaics!


## Part 2: MapReduce - Create your Mosaic `reduce` Function

The v5 (and higher) middlewares now support "reducing" two mosaics into one.  You must implement a "mosaic reducer" that connects to the shared middleware.

The reducer takes two mosaics {`mosaic1`, `mosaic2`} plus a `baseImage`.  With the three images, on a tile-by-tile basis, you must choose the best tile between the two mosaics to use on a new mosaic that you will return.

Each of the three images will come in a binary format (identical to how you get the original image).  The following code extracts all of the parameters provided to your `reduce` route:

```py
app = Flask(__name__)
@app.route("/reduceMosaic", methods=["POST"])
def reduce():
    baseImage = request.files["baseImage"]
    mosaic1 = request.files["mosaic1"]
    mosaic2 = request.files["mosaic2"]

    renderedTileSize = int(request.args.get("renderedTileSize"))
    tilesAcross = int(request.args.get("tilesAcross"))
    fileFormat = request.args.get("fileFormat")

    # Your reduce logic here:
    ...

    # Return a reduced mosaic that combines the best of mosaic1 and mosaic2:
    return make_response(mosaic_reduction)
```

### Register your Reducer with the Middleware

Once your reducer is completed and running, you must register your reducer using the `PUT /registerReducer` route.  This route requires a JSON containing the keys `name`, `url` to your reducer, and `author`.  *(This endpoint is very similar to the endpoint ot add an MMG.)*

Everyone is required to have one **one reducer running along with their 13 MMGs**.


## Part 3: Ensure You Have (at least) 13 MMGs

13 is Taylor's favorite number, so make sure to have at least 13 MMGs.  Each one must include at least 60 unique images.



## Part 4: Attend the Final Exam Project Session

On Monday, May 8 at 1;30pm in 1404 SC, we will begin the final exam project session.  You **MUST** have all 13 of your mosaics and your reducer running on your VM and connected to the shared middleware at 1:30pm.

At this session, we will:

1. Spend the first bit doing initial viewing of single-author mosaics and reductions -- ensuring that each person has a working set of MMGs and a working reducer.

2. Then, begin to nerd out with "themed" mosaics using hundreds of MMGs.

3. Then, really nerd out with mosaics using 1989 MGs!

4. Finally, we'll use increasingly large file sizes and probably end up crashing things by the very end.  Once we are sending extremely large parameters for mosaic generation, we expect to see some MMGs start to crash (but it shouldn't crash before then).


### MMG Testing Sessions

I am hosting daily testing sessions to test your MMGs.  These sessions will allow you to connect your MMGs and reducer to the shared middleware and ensure everything is working correctly and you are ready for the final exam.

**I HIGHLY RECOMMEND YOU COME TO AT LEAST ONE!**  If possible, if as many people as possible can attend the Saturday session, it's going to be a massive stress test with as many users as possible.

Here's the full schedule:

1. Wednesday, May 3 at 7:00pm on Discord (voice channel)
2. Thursday, May 4 at 5:00pm in 0224 SC (Office Hours)
3. Friday, May 5 at 3:40pm in 0224 SC
4. **Big Stress Test – Saturday, May 6 at 3:40pm on Discord (voice channel)**
5. Possibly one on Sunday?



## Submission

Make sure all source code is committed before the exam session in case we need to look back on it.


### Grading

The remaining points for the project are all associated with the final exam session.  You will earn points by being connected to the shared middleware.

- You were awarded points for your first nine mosaics in Week #1-#9.
- 80 points: Mosaics #10, #11, #12, and #13 (20 points each)
- 50 points: Reducer
