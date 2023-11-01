---
title: Courses Microservice

date: 2020-10-20
due: 2020-10-27 08:00:00

points: 25

number: 5
published: false
---

# Overview

Cloud-based infrastructure is commonly organized as micro-services that preform a functionality for a single task.  In this MP, you will create a web service that calculates the average expected GPA of a provided set of UIUC courses.


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp5 -m "Merging initial files"
```

### Python Libraries

This MP uses a few new Python libraries, specifically: `pandas`, `python-dotenv` and `requests`.  You will need to use either `conda install <library>`  or `pip install <library>` if you get an error that the specifically library is not found.


## Machine Problem

Consider the [sample schedule provided as part of the "Curriculum Flow Chart" from the CS Advising webpage](https://ws.engr.illinois.edu/sitemanager/getfile.asp?id=385) where, in "Semester 3", you might have a schedule of the following courses:

- MATH 241: Calculus III (4 credit hours)
- PHYS 212: E&M Physics (4 credit hours)
- CS 225: Data Structures (4 credit hours)
- SOC 100: Introduction to Sociology (4 credit hours)

Using the provided courses microservice (more on this later), you retrieve the following information about each of the courses:

- MATH 241 is 4 credit hours and has a recent average GPA of 2.757
- PHYS 212 is 4 credit hours and has a recent average GPA of 2.767
- CS 225 is 4 credit hours and has a recent average GPA of 3.096
- SOC 100 is 4 credit hours and has a recent average GPA of 3.364

Using the information above, the average expected overall GPA for the provided schedule would be:

- <code>&nbsp; (4 * 2.757)</code>, for MATH 241
- `+ (4 * 2.767)`, for PHYS 212
- `+ (4 * 3.096)`, for CS 225
- `+ (4 * 3.364)`, for SOC 100
- `    = 47.936 / 16 credit hours` = **2.996** average expected GPA


### Courses Microservice

To help you on this task, the `courses-microservice` directory provides a complete and fully functional microservice.  For a `GET` request to the path `/<subject>/<number>/`, the microservice will respond with a JSON object with two keys: `course` and `gpa`.  For example, the following is the result of a request to `/CS/225/`:

```
{
  "course": "CS 225",
  "gpa": 3.47,
  "creditHours": 4
}
```

If the microservice is unable to retrieve either the GPA or the number of credit hours, it will return a JSON with an `error` key:

```
{
  "course": "CS 300",
  "error": "No GPA Data Available for CS 300"
}
```

In Python, this JSON can be retrieved as a Python dictionary using the following code using the `requests` library:

```
# Ensure `subject` contains the subject (ex: "CS") and `number` contains the course number (ex: 240).
server_url = os.getenv('COURSES_MICROSERVICE_URL')
r = requests.get(f'{server_url}/{subject}/{number}/')
course_gpa_data = r.json()

# Use the result in your code (I'm just printing to console here, but you'll use it to calculate):
print(course_gpa_data)
```

The provided `.env` already sets up the courses microservice to run on `:24000` and the `COURSES_MICROSERVICE_URL` contains the location of that server (`127.0.0.1:24000`).



### Your Microservice

Your microservice must respond to a `POST` request containing course fields (`course1` - `course5`) that will either contain an course or an empty string.  All non-empty strings will contain a single course, which consists of a subject and a number with up to one space between the subject and the course.  Therefore, all of the following are valid strings to identify CS 240:

- `CS 240`
- `cs 240`, capitalization should not matter
- `cs240`, the space may or may not be present
- `Cs240`, mixed-case is okay as well

Your server must respond with one of the following two responses:

- An `HTTP/200 OK` response if the an average GPA can be successfully calculated.  The contents should be a JSON object containing the key `gpa` with the value of the average GPA.

- An `HTTP/400 Bad Request` response a course the average GPA cannot be calculated (ex: the microservice does not have complete data for one or more of the courses).  The contents should be a JSON object containing the key `message` with information informing the user of the course that was not valid.  (Be sure to include the `error` string returned from the courses microservice -- this will help you in debugging.)

In all of these responses, you can add additional data to the JSON response.  However, the specified key must exist.


#### Running Your Microservice

Since you microservice depends on the course microservice, you must **first start the courses microservice in another terminal/console**.  To do this, open up a new console/terminal and, in the `courses-microservice` directory, run the follwoing:

```
python -m flask run
```

Ensure that you see that the service is running on `:24000`.  You can visit `http://127.0.0.1:24000/CS/225/` to ensure it is working correctly on the correct port.

Once the courses microservice is running, you can run your microservice by launching a second flask server (now inside of just your `mp5` directory):

```
python -m flask run
```

Visit http://localhost:5000/ to test and debug your service.


#### Extra Credit: Beyond CS 240

**Extra Credit**: Want to dive into a bit of front-end we development?  Modify the MP5 flask HTML to send the `POST` request via JavaScript and display the formatted results in a webpage instead of the raw JSON displayed in the browser.


## Submit

When you have completed your program, double-check that your server runs as expected.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-fa20](https://github-dev.cs.illinois.edu/cs240-fa20)