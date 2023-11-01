---
title: "Project Week #2 - Image Pixelation and Intelligent PGs"

date: 2022-11-17
due: 2022-11-29 23:59:59

points: 20

layout: project

number: Week 2
published: false
---

# Overview

In Week #1, you created and documented a design for the pixel board service.  This week, you will make your personal pixel board a little more interesting!

While you are working on this, we're reviewing your API.md from Week #1 to create a shared, class-wide design that will be introduced after break.


## Part I: Image Pixelation

It would be nice to have pixels that look amazing!  Create a file called `pixelation.py` in your `project-pixel` folder, and complete the `pixelation` function.  This function has four required parameters:

- `filename : string`, containing the path to an image file,
- `width : int` and `height : int`, containing the size of the final dimensions of the result
- `palette : string[]`, containing a list of ["hex triplet"](https://en.wikipedia.org/wiki/Web_colors#Hex_triplet) RGB values.

For example, a call to this function may be:

```
pixelation("taylor.jpg", 200, 200, ["#FFFFFF", "#C0C0C0", "#808080", "#000000"])
```

The `pixelation` function must **return** a 2D array of color that represent a pixelation of the `filename` (similar to the `/pixels` route).  For example, a 2x2 image with a row of white pixels (`#ffffff`, index=0) following by a row of black pixels (`#000000`, index=3) returns the following result:

```
[ [ 0, 0 ],
  [ 3, 3 ] ]
```

We leave it up to you to decide the technical details on both the "pixelation" process and the process to convert the image colors into the palette colors.  The only requirement is the result is reasonable.


## Part II: An Intelligent Pixel Generator

Using your `pixelation` function, create a new PG that works with your middleware to paint the pixelated image to your pixel board.  Since you have designed how your PGs communicate with your middleware, every implementation will be different.

There are two technical requirements:

- Your new PG **must** look at the state of the pixel board and only add new pixels when the current desired pixel is not already the color needed for the image.  (Ex: If pixel `(4, 4)` needs to be white, and it's already white, it should not make the pixel white again.)

- Your PG **must** be a PG that communicates with your middleware; you cannot directly have the functionality built into your middleware.


## Submission

Your middleware and PG design will differ from everyone else's design, so we will inspect your code and rely on a screenshot to see your image on the pixel board.  There are three deliverables required:

1. A new pixel generator (PG) in your `project-pixel` folder on git,

2. The new pixel generator must include a `pixelation.py` file that includes `pixelation` function, **AND**

3. A screenshot **submitted on Canvas** of your pixel board running with the content from your new PG.
