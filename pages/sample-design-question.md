---
title: Sample Midterm 2 Design Question

layout: content
---

# Sample Midterm 2 Design Question

You are designing a backend system for COVID-19 testing at The University of Illinois.

You know the following about the existing infrastructure at The University of Illinois:
- Users will swipe into a testing center.  This provides the user’s UIN, a unique identifier that uniquely identifies the student.  An existing microservice, called the `student-record microservice`, can translate the UIN into a name, NetID, and other data about the person.
- At the testing center, users will provide a sample to be tested for COVID-19.  This test will be labeled with the user’s UIN as well.
- At the University of Illinois testing lab, samples are tested and are either “COVID-19 positive” or “COVID-19 negative”. (For example, the UIN 1234 tested negative and UIN 2345 tested positive.)

When the testing lab finishes the test, this result needs to be processed and stored. As part of the processing, the user should be notified about the result.  The user must be able to view all of their results, as well as specific details on an individual result (ex: positive/negative, as well as any other data that might be stored along with the result). Finally, the school should be able to create a dashboard with the total number of daily positive and negative results.

Give a high-level overview of the system that could be used. Be specific about what routes might be useful, including the variables sent along in each request to each route. In your answer, you do not need to worry about security or access control.

## Grading Rubric

Your answer will be graded on three criteria:

- Routes (60%): Does your answer provide the routes so that all of the tests listed in the description can be accomplished?  Does each route clearly identify the variables needed?  Is your use of HTTP verbs constant with HTTP standards?
- Backend / Data Store (30%): Does your answer identify the resources needed to process, store, or retrieve the data?
- Completeness (10%): Is your answer a complete system that could be implemented to solve the problem?  Would you be able to program this given your description?

## Sample Answers

*You should attempt this problem before looking at sample answers, as it will be hard to "un-see" the answer.*

- [Sample Answer #1](https://docs.google.com/document/d/1084Np3uwC-U3UaVxQk663PV413oZ51BkQWMfqT8ejaM/edit?usp=sharing)
- [Sample Answer #2](https://docs.google.com/document/d/1GDniPFgr2A6oKhINI4JTiOW2gBtIYTTDJ7WYw0h0TpY/edit?usp=sharing)

