---
title: Project Part II - Caching and Additional MGs

date: 2022-04-15
due: 2022-04-25 23:59:59

points: 30

layout: project

number: Week 2
published: false
---

<style>
.maze {
  font-weight: bold;
  /*
  font-size: 120%;
  line-height: 80%;
  height: 80%;
  background: -webkit-linear-gradient(#13294B, #FF552E, #13294B, #FF552E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  font-family: Montserrat;
  */
}
</style>

# Overview

This week, you will continue to work on your <span class="maze">Infinite Maze</span> and requires three additional deliverables:

1. Add an additional MG, for a total of at least ***2p*** total MGs, where at least one MG per person is static and at least one MG per person is dynamic,
2. Implement age-based caching in your middleware for static MGs,
3. Add at least one advanced feature of your choice to your <span class="mix">Infinite Maze</span> service


## Deliverable 1: An Additional MG

By the end of this week, you must have ***2p*** total MGs and a brief description must be documented in `MG.md`.

- Each person must have at least one **static** MG and at least one **dynamic** MG.

- A **static** MG returns the same maze every time.  In Deliverable #2, you must ensure the middleware caches your static MGs.

- A **dynamic** MG returns a different maze (with high probability).  This maze is unique, so it should never be cached.


## Deliverable 2: Age-based Cache Control

In lecture, you learned about the importance of caching and various cache policies.  For this week, you must implement age-based cache control between your middleware and your MGs with the following requirements:

- You must use the HTTP `Age` and `Cache-Control` headers.

    * The `Age` header documentation can be found in the [MDN Web Docs for Age](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Age)

    * The `Cache-Control` header documentation can be found in the [MDN Web Docs for Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)


- The only `Cache-Control` field you are required to implement is `max-age`.  Use a `max-age` of at least one hour.

- Your middleware must store the responses with a cache policy so that the IM is not contacted again if there is valid cached data that has not expired.  *You can assume that your middleware will have enough memory to cache all requests made (ex: you never need to worry about flushing things from your cache).*

To complete this deliverable you must update all of your static MGs to respond with their cache policy and for your middleware to cache these results.


## Deliverable 3: Add One Advanced Feature

In Week #1's description and in class, we spoke about "Advanced Features" that may make the <span class="mix">Infinite Maze</span> more interesting.  Some possible features include:

- How do you support multiple simultaneous users in the single infinite maze?  (Right now, each user has their own maze but we one big, shared maze.)
- How do we make exploring the maze more enjoyable?  (Should there be color?  Images?  Possibly only appearing once you complete a specific area of a block?)
- How do we add history to the maze?  (Should users leave little trails behind -- like footprints -- of where they have gone?)
- How do we support block sizes that are not square?  (Right now, there's an assumption that everything is 7x7.  We can do better.)
- ...or anything else...

Implement any one of them in your service and document it in your `API.md` under a new section titled "Implemented Advanced Features".


## Week 3 Preview

During this week, we will be reviewing all middleware designs.  A course-wide middleware will be decided upon during this week and Week #3 will be focused on adoption your MGs to the course-wide middleware design.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MIX submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

