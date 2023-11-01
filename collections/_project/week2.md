---
title: "1989 Mosaics - Week #2: Microservice Architectures"

date: 2023-04-18
due: 2023-04-25 23:59:59

points: 40

layout: project

number: Week 2
published: true
---

# Overview

In Week #1, you created a mosaic generator and proved that it could work with the provided front end.  Now time to make something that can integrate with other people's work as well!


## Part I: Mosaic Generator Specification from Lecture

Based on the in-lecture discussion, we have decided on a uniform standard for the translation of a base image into regions that will be replaced with a tile image.

<img src="../300px.png" style="margin-bottom: 20px; float: right; max-width: 200px; margin-left: 10px;">

The `tilesAcross` parameter will specify the number horizontal **tiles** in the final mosaic.  It will always be an integer.  For example, consider a `300px` by `300px` image.


<div style="clear: both;"></div>
<img src="../300px-1grid.png" style="margin-bottom: 20px; float: right; max-width: 200px; margin-left: 10px;">


If `tilesAcross` is `8`, then we will fit exactly eight tiles across the 300 pixels.  We can calculate `d`, or the dimensions of the tile on the base image, to be equal to exactly `width` / `tilesAcross`.

In this example, `d` = `300 / 8` = `37.5`.  Additionally, every tile MUST always be a square.


<div style="clear: both;"></div>
<img src="../300px-grid.png" style="margin-bottom: 20px; float: right; max-width: 200px; margin-left: 10px;">

You should always include as many **rows of tiles as possible**.  If you have remaining pixels but you cannot include an additional row of tiles, since have less than `d` pixels remaining, discard the unused pixels at the bottom of the image.  (In our example, if there were 10 more pixels below the grid, they would be discarded since 10 pixels is not enough to make another 37.5px x 37.5px tile.)


<div style="clear: both;"></div>

Your mosaic generator must make a new image that replaces each tile with a tile image.  The replacement tile image should be rendered at the size `renderedTileSize` x `renderedTileSize`.  *(This was previously called `tileSize`, but that was an ambiguous name.)*

If `renderedTileSize` = `32`, the final image would be `256px` x `256px` (8 tiles * 32 pixels /tile) for the example base images.


## Part II: Creating a Microservice Design

Modify your middleware to use a microservice design (if you did not already do this in Week #1).  To complete this:

- Your mosaic generators must be standalone microservices (which we'll call "Microservice Mosaic Generators" or MMGs) that connect to the middleware via HTTP requests.
- When a user uploads and submits a base image to the middleware, your must make the middleware communicate that image to each of your connected MMGs and return those mosaics to the frontend.  (This will be just two MMGs right now, but this will allow us to scale this application to hundreds of MMGs.)
- In addition to the base image, the `tilesAcross` and `renderedTileSize` parameters must be provided in the HTTP request to the MMG.

You **must** document these routes in a new file called `API.md` in your `project-1989` directory.  This `API.md` must include:

- A high-level overview of your implementation of the system and how to start running all the components.
- Technical details on an MMG connects to the middleware (how does the middleware know a new MMG has launched?).  This should include route verbs, endpoints, and any required/optional parameters.
- Technical details on how the middleware sends a request for a mosaic to the MMG when the user uploads a base image.  This should include route verbs, endpoints, and any required/optional parameters.
- Any additional details on your system design -- or any advanced features you added! :)

### Design Review

After the Week #2 deadline, we'll review the designs in `API.md` to choose designs to incorporate into the course-wide middleware which all mosaics will connect to during the final exam.


## Part III: Creating Additional Mosaic 

Create **three** additional MMGs for a total of five MMGs.  Each should be a different theme and should be given an individual name.

FInally, modify the middleware so that the middleware is aware of the name of each MMG.




## Submission

Your middleware and PG design will differ from everyone else's design, so we will inspect your code and rely on a screenshot of your five MMGs on the frontend.  There are three deliverables required:

1. Changes to your `project-1989` to meet the Week #2 technical requirements,

2. An `API.md` file documenting your design in your `project-1989` folder,

3. A screenshot **submitted on Canvas** of your frontend running with the content from your five MMGs.  This link should take you directly to the Canvas assignment to upload: [https://canvas.illinois.edu/courses/33908/assignments/699493](https://canvas.illinois.edu/courses/33908/assignments/699493)


### Grading

You will be able to earn 40 points for completing all of this week's deliverables (there will be **no partial credit for this week, this is already partial credit as part of the final project**). This week comprises only a portion of the 220 total points for the final project, since this project builds on itself each week. Failure to complete this week will make it more difficult to earn points in future weeks since you will need to complete this work to complete the next week's work. 


### Submission on Git

```
git add -A
git commit -m "MP submission"
git push
```

Remember the screenshot on Canvas! :)
