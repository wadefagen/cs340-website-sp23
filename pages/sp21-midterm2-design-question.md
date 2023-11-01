---
title: Midterm 2 Design Question

layout: content
---

# Midterm 2 Design Question

You are designing a student registration system for The University of Illinois.

You know the following about course registration here at Illinois:
- Students are identified by their UIN, a unique identifier number that identifies the student.
- Courses are identified by their CRN, a unique identifier that identifies a specific section of a course.
- There is a `course-information microservice` that can translate CRNs into information about a course, including the course name, course subject, course number, the date/time of the course, etc.

A student registration system must allow a student to add a course to their schedule, remove a course from their schedule, or view all courses they're currently enrolled in.  The course registration system must also ensure students do not register for two courses that meet at the same time.  Finally, an instructor for a course must be able to list all student UINs enrolled in their course by specifying their course CRN.

Give a high-level overview of the system that could be used. Be specific about what routes might be useful, including the variables sent along in each request to each route. In your answer, you do not need to worry about security or access control.

## Grading Rubric

Your answer will be graded on three criteria:

- Routes (60%): Does your answer provide the routes so that all of the tests listed in the description can be accomplished?  Does each route clearly identify the variables needed?  Is your use of HTTP verbs constant with HTTP standards?
- Backend / Data Store (30%): Does your answer identify the resources needed to process, store, or retrieve the data?
- Completeness (10%): Is your answer a complete system that could be implemented to solve the problem?  Would you be able to program this given your description?

([You can view two sample submission to a similar design question here.](https://courses.grainger.illinois.edu/CS240/sp2021/pages/sample-design-question.html))

## Submission

Submit your answer as a PDF file to Compass 2g by 11:59pm (Thursday, April 29) under the assignment "Midterm #2 Design Document Submission".
