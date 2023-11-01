---
title: MIX Project Part III - Towards a Course-wide Service

date: 2021-11-29
due: 2021-12-05 23:59:59

points: 20

layout: project

number: Week 3
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

This week, you will work towards integration of your work to the course-wide <span class="mix">MIX</span> service.  Specifically, there are two key components:

1. Registering your design IM designs to ensure you have a unique set of IMs, and
1. Refactoring your IMs to work with the course-wide specification


## Deliverable 1: IM Registration

For the final project, we want a <span class="mix">MIX</span> service that finds out a lot of unique information -- not just the weather 50 different times.  Each team is required to have ***2p - 1*** unique IMs across the entire course.

To ensure there are unique IMs, you will complete an IM registration form that describes the function of each of your IMs in a single sentence in simple terms (ex: "finds the weather at the location") by this **Thursday, Dec. 2, at 11:59pm**.

[Complete the IM registration form now (only one member /team needs to complete this).](https://docs.google.com/forms/d/e/1FAIpQLScdVykKa5CVO0-P7M5O-R8U_85JDvDt1T4kqTXmEIFjk6IdLw/viewform?usp=sf_link)

To determine if a duplicate occurs, I will follow this algorithm:

- I will randomize the order of the list,
- I will start with your first IM and include it if it's unique;  if it is not unique, I will move down your list until you have your first unique IM,
- Every group will have their first unique IM chosen, and
- Only every group has their 1st unique IM the process repeats for the 2nd, 3rd, 4th, and 5th unique IMs (for teams groups of ***p=2*** or ***p=3***).

After this process is complete, the list will be published.  IF you do not have at least ***2p - 1*** unique IMs, your team will need to create unique IMs to achieve ***2p - 1*** unique IMs.


## Deliverable 2: IM Refactoring

Jackson design and code, along with some elements of Kevin's design, were selected as the base for the course-wide microservice.  Many individual designs contained many of these features, but Jackson's code did all of the following:

- Allowed IM registration while the microservice was running, without requiring to hard-code a new IM,
- Allowed IM registration with various IP addresses (not just a port on localhost),
- Provided a clear dependency chain to have multiple layers of IMs, and
- Documented the process in a clean and easy-to-read way

You can find the course-wide middleware here: [https://github.com/cs240-illinois/cs240-fa21-MIX_shared](https://github.com/cs240-illinois/cs240-fa21-MIX_shared)

If you find any bugs, please create a GitHub issue if one does not already exist.  PRs to fix open issues are welcome and worth EC. :)


### Refactor your IMs

For this week, you must refactor **ALL** of your IMs to work with this new middleware.  The two key areas where you likely need to change your code are:

- Your IMs must make a `PUT` request to the <span class="mix">MIX</span> middleware upon startup (see the documentation on the format). 

- The middleware will make a `GET` request to `/` with a JSON payload in the format `{ 'latitude' : float, 'longitude' : float }` to request data from your IM.


### Testing your IM Integration

To do integration testing:

- Start the course <span class="mix">MIX</span> server found here: [https://github.com/cs240-illinois/cs240-fa21-MIX_shared](https://github.com/cs240-illinois/cs240-fa21-MIX_shared)

- Start your IMs, which should each automatically be added as an IM when they start up.

- Run <span class="mix">MIX</span> to ensure your IMs work!



## Week 4 Preview

In Week 4, you will be required to run your IMs on your CS 240 virtual machine and communicate with the <span class="mix">MIX</span> microservice on the CS cloud (ex: not localhost)!

We will have a "dry run" for extra credit on the last day of lecture (Tuesday, Dec. 7) to give you a chance to test your IMs before the final exam.  You will present your project in lecture during the final exam period on Friday, Dec. 10 at 7:00pm.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MIX submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

