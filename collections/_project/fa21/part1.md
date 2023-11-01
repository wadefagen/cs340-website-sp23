---
title: MIX Project Part I - Design and Middleware Implementation

date: 2021-11-04
due: 2021-11-14 23:59:59

points: 30

layout: project

number: Week 1
published: false
---

<style>
.mix {
  font-weight: bold;
  font-size: 120%;
  line-height: 80%;
  height: 80%;
  background: -webkit-linear-gradient(#13294B, #FF552E, #13294B, #FF552E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-family: Montserrat;
}
</style>

# Overview

Based on the class vote, we're building a new web service called <span class="mix">MIX</span> (**M**icroservice **I**nformation e**X**change) that will provide a wealth of information about any location based on freely available public APIs.

As a class, we will create the technical design for the entire project and use everyone's components together when we launch <span class="mix">MIX</span> during this course's final exam period on Friday, Dec. 10 at 7:00pm.

<div class="alert alert-info" role="alert">
  <h3>Optional Team-Based Project!</h3>
  <p>
    This part of the project can be done <b>with a team of up to 3 total people</b> (you and up to two others).
    The requirements of this project will scale based on the size of your team.  In this document,
    <b><i>p</i></b> describes the number of members in your team (if you're solo, p=1; a group of two
    has p=2, etc).
  </p>
  <p>
    If you are working in a team, <b>your submission must be made in a team repo instead of your own personal
    repo</b>.  You must agree on a team name and then <a href="https://docs.google.com/forms/d/e/1FAIpQLSdzAPa1jsYw_o7-E2r7C4OXWnFR4wQG4UBs0JYBA2sErMHlBQ/viewform?usp=sf_link" target="_blank">everyone on your team must submit this form to have a repo created
    for your team.</a>
  </p>
</div>


<h1><span class="mix">MIX</span> Application Vision</h1>

The <span class="mix">MIX</span> service is a scalable web service that provides a detailed suite of information about a user provided location.  For example, the location of our classroom is `40.1125,-88.2284`.  This input might yield any of the following from different microservices that are part of the <span class="mix">MIX</span> system:

- The city it's located within *(Urbana, IL)*,
- The state it's located within *(Illinois)*,
- The weather forecast,
- The most popular restaurant within 1 mile,
- The walkability of the city/area,
- The most played song within the city/area,
- The state bird,
- The sales tax rate,
- The nearest Starbucks coffee shop (or any coffee shop),
- The zip code,
- *...and any number of millions other things...*

<span class="mix">MIX</span> is designed to be scaled, so each piece of information is gathered in its own independent microservices called an "Information Microservice" or **IM**.  There number of IMs will vary over time as new IMs get developed, so you never know what information you might get when you query <span class="mix">MIX</span>!

There are three major components to <span class="mix">MIX</span>:

- A frontend (basic right now, but we can develop it out later),
- A middleware the interfaces between the frontend and backend (designed programmed in Part I),
- Many backend microservices that each fetch one piece of information (called "IMs" or "Information Microservices").

During the first two weeks of the project, you will develop your own private <span class="mix">MIX</span>.  Over Fall Break, everyone's design will be reviewed and a single course-wide <span class="mix">MIX</span> infrastructure will be chosen and all IMs will then be updated to match the specification of the course-wide <span class="mix">MIX</span> design.

Feel free to discuss high-level details of your <span class="mix">MIX</span> design between groups and as a class to come up with the best possible design.  <span class="mix">MIX</span> will eventually be comprised of code developed by every single person in this class.


## Part I: Design and Middleware Implementation

To develop <span class="mix">MIX</span>, you will design the system specification and middleware implementation within a group of **1-3 students**.

The only technical requirement that is fixed is that the request will be communicated to your service via a `POST` with the form field `gps` containing a GPS coordinate formatted as two numbers separated by a comma (ex: `40.1125,-88.2284`).  The frontned to submit the coordinates and the middleware logic to read that value is already provided for you in the provided code.

The response to that `POST` must contain all of the data available in <span class="mix">MIX</span> about the location in question.  Currently, the entire response is simply dumped into the webpage, but frontend processing can make that beautiful at a later stage.

There are a few functional requirements:

- Your <span class="mix">MIX</span> middleware must communicate to backend IMs that look up information about the request,
- Your <span class="mix">MIX</span> middleware must be able to connect to many IMs that may come and go over time, and
- Some IMs may be dependent on input from other IMs (ex: the IM that returns the state bird may need the state as input instead of GPS coordinates; the state bird IM will depend on the output of the IM that returns the state the GPS coordinate is located within).


### Initial Files

In your CS 240 directory (if you are solo) or in your team directory (if you are in a team), merge the initial starting files with the following commands:

```
git fetch release
git merge release/mix-week1 -m "Merging initial files"
```


### Deliverables

For Part I, you must deliver:

1. A technical specification describing your design in `API.md`.  This must include:

    * An high-level overview of how your implementation of <span class="mix">MIX</span> is designed,
    * Technical details on the request and response APIs between your middleware and the IMs,
    * Technical details on the response API between the frontend and the middleware,
    * Technical details on how IMs are added and removed from MIX,
    * Technical details on any dependencies needed to run MIX (ex: python commands to run, other services, docker, etc)


2. An implementation of your middleware in the `app.py` file provided that works with the provided frontend,

3. At least ***p*** IMs that includes at least one IM by each team member:

    * Each IM's functionality should be described in `IMs.md` and briefly describe what it does (ex: *"finds the current weather"*) **and** the name of who owns this IM (ex: *by Wade Fagen-Ulmschneider*).

    * Each IM must be implemented within a septate directory within the `project-MIX` folder.


### Part 2 Preview

For Part II (next week) update, each team member must develop at least ***3*** IMs (for a total of at least ***3p*** IMs) with at least one IM dependent on the result of another IM.  In addition, Part II will require you to add caching between the middleware and the IMs.

You may will to ensure your design this week will be able to scale into Week 2's challenges.


### Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MIX submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

