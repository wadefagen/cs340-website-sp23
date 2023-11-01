---
title: "1989 Mosaics - Week #1: Middleware and Mosaic Implementation"

date: 2023-04-13
due: 2023-04-18 23:59:59

points: 40

layout: project

number: Week 1
published: true
---

# Overview

The final project in CS 340 is all about creating something as a course, together, that all of our work contributes to the final deliverable and runs simultaneously at our final exam meeting time.  You will make all the major design decisions and decide how we build this app together.

Two weeks ago, we discussed many ideas for this project.  In lecture on Thursday, you were introduced to the final project for Spring 2023, to build the ultimate mosaic generator <b>"1989"</b>.

- A user will upload an image.
- Many mosaic generators ("MGs") will generate mosaics of various themes from the uploaded image.
- We will combine the results of many MGs into one large ultimate mosaic made up of over 100,000 tile images.

You will program the middleware and backend services for "1989".  You can also extend the frontend, but a basic functional frontend is provided.


## Software Architecture

There are three major components "1989":

- A <b>frontend</b> (basic proof of concept right now, but we can develop it out later),
- A <b>middleware</b> the interfaces between the frontend and backend (designed programmed in Part I),
- Many <b>backend microservices</b> that generate mosaics (called "mosaic generators" or MGs)

During the first week of the project, you will develop your first mosaic generators.  During Week #2, we will design the middleware to share a common interface between everyone's mosaic generators while you continue to generate additional mosaic generators.  But we need mosaics first, before we can send them around! :)


### Initial Files

Merge the initial starting files with the following commands:

```
git fetch release
git merge release/project-1989 -m "Merging initial files"
```


## Requirements Overview

The goal of Week 1 is to provide you with as **minimal requirements as possible** for you to design your own mosaic generators.  However, there are a few requirements that must be met.  Some of these requirements are technical (where we specify the functionality and design) and some of them are functional (where we specify the functionality, but you create the technical design).


### Technical Requirements

Every mosaic generator must input four parameters:

- A **base image**, provided by the user of "1989".  The **base image** is the image that the user wants to create a mosaic from.
- A set of **tile images**, chosen by you for each of your MGs.  Each set of tile images should have a name and a theme (ex: "cats", "volleyball", etc).
- An integer value `tilesAcross`, which defines the number of mosaic tiles for the image (ex: `200` tiles).
- An integer value ~~`tileSize`~~ `renderedTileSize`, which defines the size of the mosaic tiles (ex: `32` pixels).

An image that is 200 tiles across, and has a tileSize of 32 pixels, will be 200 * 32 = 6,400 pixels across.  (That's a pretty big image!)

Additionally, each of your MGs must have a **set of at least 60 tile images**.


#### Mosaic Algorithm

The algorithm is very similar to the mosaic you created if you took other courses I have taught -- either CS 225 or CS 107.  This is the ultimate end of of an era!

Your mosaic must transform the **base image** into an image made up entirely of **tiles images** of `renderedTileSize`x`renderedTileSize` pixels (tile images are always squares).  There must always be `tileAcross` tiles across the image.

Each tile must correspond to an equal number of pixels, and correspond to as many pixels as possible:
- If a `700px` x `300px` base image is used for 200 `tilesAcross`, each tile will correspond to a `3`x`3` pixels in the base image.  The mosaic would be 200 tiles wide and 100 tiles tall, using the first `600px` x `300px`.  The right-most 100px in the base image would be unused and lost.
- If a `300px` x `700px` base image is used for 200 `tileAcross`, each tile would correspond to just a `1`x`1`x pixels in the base image.  The mosaic would be 200 tiles across and 700 tiles tall, using the first `200px` x `700px`.  The bottom-most 100px in the base image would be unused and lost.

Each tile must profile the best replacement image from your set of tile images.


### Functional Requirement

You must implement your mosaic in such a way that the file is retrieved from the provided middleware and returned via a JSON array where each element is a dictionary that contains the key `"image"` with an image as the value.

A working example of sending a file (which is not a mosaic, you'll need to replace it with your mosaic).

Your mosaic algorithm **must be fast and efficient**.  You should pre-calculate the average color of each tile image (or whatever metric you want to use) and use a kd-tree (or similar algorithm) to find the best match.  In future weeks, there may be a hard requirement for the runtime.


## Deliverables

For this week, you must deliver:

1. Two MGs, each with at least 60 images, that are both used and when a base image is uploaded to the middleware.
2. Changes to the middleware logic to return both images.


### Grading

You will be able to earn 40 points for completing all of this week's deliverables (there will be **no partial credit for this week, this is already partial credit as part of the final project**). This week comprises only a portion of the 220 total points for the final project, since this project builds on itself each week. Failure to complete this week will make it more difficult to earn points in future weeks since you will need to complete this work to complete the next week's work. 


## Submission

Once you have successfully completed this week's deliverables, you will need to submit your code.  Commit using the standard git commands:

```
git add -A
git commit -m "MP submission"
git push
```

### Points

The 40 points for this week are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| Two MGs and Middleware | 40 points |
