---
title: "Project Week #1 - Design and Middleware Implementation"

date: 2022-11-10
due: 2022-11-17 23:59:59

points: 20

layout: project

number: Week 1
published: false
---

# Overview

The final project in CS 340 is all about creating something as a course, together, that all of our work contributes to the final deliverable and runs simultaneously at our final exam meeting time.  You will make all the major design decisions and decide how we build this app together.

Two weeks ago, we discussed many ideas for this project.  Based off the discussion, this semester we're going to build a <b>shared pixel board</b>.

- The shared pixel board is one, giant shared image of pixels.
- There will three primary interactions to the pixel board:
    * **Viewer**: Anyone viewing the pixel board must constantly get an updated view of the current image.
    * **User Additions**: A viewer of the pixel board should be able to modify a pixel to a new color.
    * **Automated Addition**: A pixel generator microservice should be able to modify a pixel to a new color at a programmable rate.
- You will program the middleware and backend services for this pixel board.  You can also extend the frontend, but a basic functional frontend is provided.


## Software Architecture

There are three major components to our pixel board:

- A <b>frontend</b> (basic proof of concept right now, but we can develop it out later),
- A <b>middleware</b> the interfaces between the frontend and backend (designed programmed in Part I),
- Many <b>backend microservices</b> that generate pixels (called "pixel generators" or PGs)

During the first week of the project, you will develop your own private pixel board by designing your own middleware and backend services.  During Fall Break, we will take the best implementation(s) of the middleware and share that middleware as a course-wide middleware that will integrate with the PGs from the entire course.

<b>Absolutely discuss high-level details of your pixel board server design between each other and as a class to come up with the best possible design.</b>  There is a Discord channel #project-design just for design-related discussions.  The complete pixel board will eventually be comprised of code developed by every single person in this class.


### Initial Files

Merge the initial starting files with the following commands:

```
git fetch release
git merge release/project-pixel -m "Merging initial files"
```


## Requirements Overview

The goal of Week 1 is to provide you with minimal technical requirements to allow you to design as much of the project as possible. There are a few requirements that must be met. Some of these requirements are technical (where we specify the functionality and design) but most of them are functional (where we specify the functionality, but you create the technical design).

### Technical Requirements

You must implement middleware for the pixel board in `project-pixel/app.py`.

Your middleware must contain a `GET /settings` route that returns the current server's settings. This route must return JSON containing **at least** the following keys:
- `width`: The width of the pixel board.
- `height` The height of the pixel board.
- `palette` A list of strings where each string is an [HTML/CSS color hex triplet](https://en.wikipedia.org/wiki/Web_colors#Hex_triplet). This list specifies the colors that users can pick on the pixel board.

For example, the `GET /settings` route may return:

```
{
  "width": 400,
  "height": 200,
  "palette": ["#FFFFFF", "#C0C0C0", "#808080", "#000000"]
}
```

Additionally, your middleware must contain a `GET /pixels` route that returns the current pixels of the pixel board.  This must return JSON containing **at least** a key called `pixels`.  The value of the `pixels` key must be a 2D array of color indexes into the `palette`.

For example, say we want to return a 3x3 board using the same palette as the example above, where the first two rows are only white pixels (`#ffffff`, index=0) and the final row is only black pixels (`#000000`, index=3). In this case, we would return the following JSON:

```
{
  "pixels": [
    [ 0, 0, 0 ],
    [ 0, 0, 0 ],
    [ 3, 3, 3 ]
  ]
}
```

In both the above routes, feel free to improve the design by adding additional keys or data.  These technical requirements were a minimal set of technical decisions to get a working prototype frontend and will almost certainly be incomplete. You will need to add additional routes and logic to the middleware, but you will design the technical details of those routes yourself!

### Functional Requirement

In addition to your middleware, you must create at least one "pixel generator" (PG) in the `PGs` directory this week.  Your pixel board must change over time based on data received from your PGs.  Functionally, you must:

- Create a "pixel generator" microservice, or simply a "PG", that communicates new pixels to your middleware.
- Your PGs and middleware must communicate with each other so that the PG modifies the pixel board.  (These modifications must then be passed along to all viewers when each viewer gets an updated set of pixels from `/pixels`.)
- You must support many PGs being connected to the middleware.
- Finally, your PG should communicate pixels in a rate-limited way so that it's visible that the board in changing.

A simple PG might simply fill the pixel board with one color, and then another, and so on.  There are no requirements for what your PG does this week,  but it must do something.


## Deliverables

For this week, you must deliver:

1. A technical specification describing your design in `API.md`.  This file must be in Markdown (`md`) format and display on github.com's web browser interface in a readable way.  Your `API.md` must include:

    * A high-level overview of your implementation of the pixel board design (including middleware and PGs),
    * Technical details on the APIs between your middleware and the PGs,
    * Technical details on any changes to the API between the frontend and the middleware,
    * Technical details on how PGs are added and removed from the middleware,
    * Technical details on any dependencies needed to run your pixel board (ex: python commands to run, other services, docker, etc), and
    * Any additional details of the ability for your design to incorporate the Week #2 challenges (see Week #2 preview below).

2. An implementation of your middleware in the `app.py` file provided that works with the provided frontend,

3. An implementation of at least one PG that works with your middleware,

    * Your PG(s) must be implemented inside of the `PGs` directory.
    * Your PG must be a separate process from the middleware and communicating using the `requests` library, you cannot simply call a function in another directory.

4. A file called `PGs.md` that briefly describe the what your PG(s) do.  (This should be in the root project directory along with `API.md`.)

    * This file must also be in Markdown (`md`) format and display on github.com's web browser interface in a readable way. 


### Grading

You will be able to earn 20 points for completing all of this week's deliverables (there will be **no partial credit**). This week comprises only a portion of the 120 total points for the final project, since this project builds on itself each week. Failure to complete this week will impact your ability to earn points in future weeks. 


### Course-Wide Design Extra Credit

During Fall Break, we will review all of the designs you created for Week #1 (mostly via reading `API.md`, so document it well).  We'll discuss several of the best designs in lecture and, as a class, choose one as the shared middleware for everyone to use.  The designs used for the course-wide middleware will earn EC. :)


### Week 2: Preview

For Week 2, there will be additional features that we need to design solutions to make a great pixel board.  As you design your `API.md`, consider some possible features and how you may add some/all of these to your design:

- How do we limit how often a PG can update the pixel board?  (Ex: When we have 100 users, we may only want any one user to update once every 30 seconds.)
- How do we allow a human-user to update the pixel board via the frontend, simultaneously with the PGs?  How do we limit how often human-users can update the pixel board?
- How do we store/show the history of the pixel board?  Can we "replay" the pixel board?
- How do PGs know the state of the pixel board?  In future PGs, you may need to "defend" your part of the board from others overwriting it and must know if a PG need to change a pixel they've previously set.
- How do we do more efficient communication about the state of the pixel board?  (Does the frontend always need the entire state of the pixel board sent every time?)
- What additional information would be useful to display on the frontend?  (Ex: How many times a pixel has changed?  The most recent person who changed the pixel?)
- What is a good set of colors for the palette?
- How do we ensure the image persists across restarts of the app?  (What data store(s) should we use instead of variables stored in memory?)

You should ensure your design this week will be able to scale into Week 2's challenges.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.

```
git add -A
git commit -m "submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

