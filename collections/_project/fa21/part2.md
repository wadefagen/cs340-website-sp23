---
title: MIX Project Part II - Caching and IMs

date: 2021-11-12
due: 2021-11-28 23:59:59

points: 50

layout: project

number: Week 2
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

Week two is continues the work on <span class="mix">MIX</span> and requires three additional deliverables:

1. Implement age-based caching control in your middlware are IMs.
2. Add two additional IMs /person, for a total of ***3p*** total IMs.  All of your IMs must be distinct.
3. Ensure at least one IM must depend on the result form another IM.


## Deliverable 1: Age-based Cache Control

In lecture, you learned about the importance of caching and various cache control policies.  For this week, you must implement age-based cache control between your middleware and your IMs with the following requirements:

- You must use the HTTP `Age` and `Cache-Control` headers.

    * The `Age` header documentation can be found in the [MDN Web Docs for Age](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Age)

    * The `Cache-Control` header documentation can be found in the [MDN Web Docs for Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)


- The only `Cache-Control` field you are required to implement is `max-age`.

- Your middleware must store the response from an IM for a given GPS coordinate so that the IM is not contacted again if there is valid cached data that has not expired.  *You can assume that your middleware will have enough memory to cache all requests made (ex: you never need to worry about flushing things from your cache).*


## Deliverable 2+3: Additional IMs

You must develop a total of at least ***3p*** IMs, where **p** is the size of your group.  Each member of the group must be the "owner" at least ***p*** IMs.  All new and existing IMs must be documented in `IMs.md` (same requirements as Week 1).

In addition, at least one IM depends on the result form another IM.  (Ex: The "country flag IM" may depend on knowing what country the location is being queried.  One IM would find the country name from GPS coordinates and that IM would then be passed on to your country flag IM.)  We do not specify how this dependency must be satisfied (ex: does the IM contact another IM, or does the middleware gather all requirements and then make the IM requests).  You can modify your middleware if needed.


## Week 3 Preview

In Week 3, you will be required to have at least ***2p - 1*** unique IMs across the entire course.  You should focus on your IMs being unique tasks rather than trivial tasks so you will not need to program additional IMs as part of Week 3.


## Submission

When you have completed your program, double-check that your server runs as expected by the specifications above.  **If you are working on a team, make sure this is in your team repo.**  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MIX submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com).

