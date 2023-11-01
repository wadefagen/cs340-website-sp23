---
title: "Project Week #4 - Final Project Presentation"

date: 2022-12-07
due: 2022-12-13 19:00:00

points: 70

layout: project

number: Week 4
published: false
---

# Overview

In this final week, you will prepare for contributing to the course-wide pixel board during the final exam period on Tuesday, Dec. 13 at 7:00pm in 1404 SC.  Before the exam, you will need to prepare one instance of the middleware and three PGs that are all running on your VM.  You must use the shared middleware from [https://github.com/cs340-illinois/pixel-board-shared-middleware](https://github.com/cs340-illinois/pixel-board-shared-middleware) and must be running on your VM.


## Requirement #1: Update to the new Shared Middleware API

Between Week #3 and Week #4, we made [two small changes to the API of the middleware](https://github.com/cs340-illinois/pixel-board-shared-middleware/pull/16).

1. The `pixel_rate` field is no longer communicated via `GET /settings` route.  Instead, each response from a successful `/update-pixel` will contain a `rate` field that informs your PG how long to delay before it can send the next pixel.  The rate may dynamically change throughout a single pixel board.
2. The `GET /pixels` route now required your PG's `id` to be passed as a JSON (identical to how it is sent in `/update-pixel`).  You can only request to the board state (`GET /pixels`) when your PG is able to write a new pixel.

The shared middleware will be kept up to date, and you should regularly `git pull` to get the latest middleware.



## Requirement #2: Running the Middleware and PGs


### Deliverable #1: Private Pixel Board

To complete the first deliverable, you must launch a private version of the shared middleware on your own course VM on port `3340`, have it be externally accessible outside of your VM (use `host=0.0.0.0`), and connect a PG to it that generates a pixelated image (requirement from Week #2).

- At the final exam, we will visit your VM by going to `http://fa22-cs340-###.cs.illinois.edu:3340/`, where `###` is replaced with your assigned VM number.
- Your middleware must have the following configuration:
    - The color pallette (it's Illini!): `c84113,13294b,ffa069,ffd0b4,ffffff,b34404,662702,0d1d35,717f93,b8bfc9,08101e`,
    - At least `100` rows, and
    - At least `50` columns
- When we visit your VM, your version of our shared middleware and your PG must already be running and show a pixelated image with many pixels drawn and **zero unnecessary pixel updates**.  (An "unnecessary pixel update" is reported on the frontend and counts calls to `/update-pixel` that updated a pixel to its own value; having an intelligent pixel generator that avoids this was a Week #2 requirement.)
- Finally, your PG should mostly fill up your pixel board (you don't need to distort your pixelated image, but you should NOT have just a 10x10 image on a 100x50 board).  If you have a square image, you would ideally make a 100x100 board for the image and display it at 100x100.


### Deliverable #2: Shared Pixel Board of Solo PGs

To complete the second deliverable, you must launch a second PG on your VM.  This second PG must be your **solo work** and connect to the `http://fa22-cs340-adm.cs.illinois.edu:34000` before the exam.

This PG can be same as "Deliverable #1" but it should be something small and configurable.  Ex: You may want to try a grab a 10x10 spot on the board just for you, and keep that spot updated, since there's no way you will be able to defend a 100x100 board with 100 other MGs also creating art.

Due to race conditions with many PGs running concurrently, a minimal number of unnecessary pixel update is allowed but it should be less than 1% of your pixel updates.  You can see your individual PG stats in the servers tab.

You must use your private secret to connect to this shared pixel board.  Your `/register-pg` will fail if your secret is no used.

You should set up your PG so that you can quickly edit and restart it to adjust your strategy -- your goal is to have either (1): an interesting feature (art, color, etc) on the shared board OR (2): contribute as many pixels that are not overwritten by other PGs.  It may be advantageous to quickly edit the offset or config values of your PG to use a space that others are not using.

It is **very highly recommended** you test your code before the final.  See the "PG Testing Sessions" below.


### Deliverable #3: Shared Pixel Board for Your Team PG

To complete the final deliverable, you must launch a third PG on your VM.  This third PG must your **team PG** and all members of your team will have each individual PG achieve the same objective.  The team server will be running at `http://fa22-cs340-adm.cs.illinois.edu:34340`.  Each member will run their own PG.

- The `author` name for your team PG **must be your team name**.  Make sure that your use the same capitalization between all team members.

All members of your team can run the same code on their own VMs if your code is intelligent enough to ensure each copy running is making useful progress.  Due to race conditions with many PGs running concurrently, a minimal number of unnecessary pixel update is allowed but it should be less than 1% of your pixel updates.  You can see your individual PG stats in the servers tab.

You must use your private secret to connect to this shared pixel board (it's the same secret as your solo PG).  Your `/register-pg` will fail if your secret is no used.

It is **very highly recommended** you test code before the final.  See the "PG Testing Sessions" below.


### PG Testing Sessions

There are many ways to test your PG:

- There will be three PG testing sessions.  I'll will launch a new server and "enable" it at a set time, and I'll will be available to help with basic debugging for an hour.  These sessions will may be the best way to get many users on a shared pixel board at the same time to see how your PG works with other PGs also contributing to the same pixel board:

    1. On Reading Day (Thursday, Dec. 8) at 4:00pm, in-person in 0224 SC (office hour space in basement of Siebel).  This is happening immediately after the department's "Celebrate CS" event.

    2. On Discord in the **General** voice channel (scroll to the bottom) on Sunday, Dec. 11 at 5:00pm.

    3. On Discord in the **General** voice channel (scroll to the bottom) on Monday, Dec. 12 at 5:00pm.

- Additionally, a test server will be running on `:34999` that you can connect to at any time.  This server will be restarted occasionally to run the latest middleware.



## Requirement #3: Attend the Final Exam Project Session

On Tuesday, Dec. 13 at 7:00pm in 1404 SC, we will begin the final exam project session.  At this session, we will:

1. Visit all private pixel boards.  You will need to briefly share what you created on your pixel board.

2. Run the shared solo pixel board.  You'll have your PGs contribute to it, and also use your browser to help your PG.

3. Run the shared team pixel board.  You'll have your PGs contribute to it, and also use your browser to help your PG.



## Submission

As always, make sure all source code is committed before 7:00pm.

