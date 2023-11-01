---
title: Midnight Rain

date: 2023-03-07
due: 2023-03-21 23:59:59

points: 40

number: 6
published: true
---

# Overview

As the Spring rains arrive, what if there was a way to check the weather for the next time your class meets?  Or the next Midnight Rain?  In this MP, you will build a microservice architecture application where you will build the middleware piece of the application that returns the weather the next time your class meets.

To do this, you will:

- Explore and interact with a provided flask application, `courses_microservice`, via an HTTP API,
- Use the `requests` library to access the National Weather Service's web API to get the weather forecast, and
- Respond to front-end requests to view the weather for the next meeting of any class at Illinois


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp6 -m "Merging initial files"
```

### Python Libraries

This MP uses several Python libraries.  You can install all required libraries by running:

- **Windows**: `py -m pip install -r requirements.txt`
- **macOS**: `python3 -m pip install -r requirements.txt`

(Note the commands are identical, except that Windows requires the use of `py` to invoke Python while macOS requires `python3`.)


## Courses Microservice

To help you on get started with your first Python application, the `courses_microservice` directory provides a **complete and fully functional microservice**.  On a `GET` request to the path `/<subject>/<number>/`, the microservice will respond with a JSON object with three keys: `course`, `Start Time` and `Days of Week`.  For example, the following is the result of a request to `/CS/340/`:

```
{
  "course": "CS 340",
  "Days of Week": "TR",
  "Start Time": "12:30 PM"
}
```

If the microservice is unable to find the course, it will return a `404` and JSON with an `error` key:

```
{
  "course": "CS 300",
  "error": "No course data available for CS 300"
}
```

The provided `.env` already sets up the courses microservice to run on `:34000`.  You should explore running this microservice now by navigating to the `courses_microservice` folder inside of `mp6` and running:

- **Windows**: `py -m flask run`
- **macOS**: `python3 -m flask run`


Once you launch the `courses_microservice`, you can visit it with your web browser to view results.  For example:

- [http://127.0.0.1:34000/CS/225/](http://127.0.0.1:34000/CS/225/) to view when CS 225 meets,
- [http://127.0.0.1:34000/CS/340/](http://127.0.0.1:34000/CS/340/) to view when CS 340 meets,
- ...etc...


## Machine Problem

Your task for this MP is to complete the flask `app.py` route for `POST /weather` that will respond with the weather forecast for the next meeting of the class provided in the request.  Specifically:

- You MUST accept any reasonable course identification (`CS 340`, `cs340`, or other such combinations).  This will be provided to your microservice as via a form submission field `course`.

- You MUST use the provided `courses_microservice` via web API requests to get the course meeting days and time.  You must use the environmental variable `COURSES_MICROSERVICE_URL` to find the location of the `courses_microservice`.
    * You can find `COURSES_MICROSERVICE_URL` via the following Python code: `server_url = os.getenv('COURSES_MICROSERVICE_URL')`
    * The value of this variable will be `http://127.0.0.1:34000` (no trailing slash).

- You MUST use the National Weather Service API to get the weather forecast [https://www.weather.gov/documentation/services-web-api](https://www.weather.gov/documentation/services-web-api).
    * Use (40.1125,-88.2284) as the GPS coordinates for all locations.
    * You must get the forecast **for the specific hour when the course begins to meet** (not the high temperature for the day).
    * You muse use the `requests` library to make a request to the API.

- <span style="color: red; font-weight: bold">IMPORTANT</span> -- You **MUST** cache the response from the National Weather Service.  That means that, once you receive a response once, your program should not make another request to the National Weather Service API until the current request has expired.
    * If you fail to do this, the test suite will spam the National Weather Service API and **may result in your IP being blocked for a period of time** and prevent you from testing/grading your MP for several hours.
    
- Your cached data must be accessed by `GET /weatherCache`.
    * If there is nothing in your cache, `/weatherCache` must return `{}`.
    * Otherwise, your cache should be returned as a JSON.  The specific contents is implementation dependent and up to you, but the cache must be a non-empty dictionary.

- You must report the data back in a response JSON with the following structure with a `200` response:

    ```
    {
      "course": "CS 340",
      "nextCourseMeeting": "2022-10-25 02:00:00",
      "forecastTime": "2022-10-25 02:00:00",
      "temperature": 60,
      "shortForecast": "Mostly Sunny"
    }
    ```

- Due to limitations of the weather API, hourly forecast information is only available up to 156 hours (6.5 days) in the future. To address this, if a course's next meeting time is more than 144 hours (6 days) in the future, you must set the `"temperature"` and `"shortForecast"` items to the string `"forecast unavailable"`:

    ```
    {
      "course": "CS 340",
      "nextCourseMeeting": "2022-10-19 02:00:00",
      "forecastTime": "2022-10-19 02:00:00",
      "temperature": "forecast unavailable",
      "shortForecast": "forecast unavailable"
    }
    ```

- On any other sort of error, respond with a `400` response (ex: when the course cannot be found).

- You will need to write logic to calculate the next course meeting relative to the current datetime.  The Python command [`datetime.now()`](https://docs.python.org/3/library/datetime.html#datetime.datetime.now) will provide you with a `datetime` object with the current time.

    - You should NOT account for holidays, the end of semester, spring break, or anything else.
    - That means you should always assume the next course meeting is the next datetime that occurs after the current datetime when the course would meet.



## Testing Your Application

A frontend has been provided for you to test your web application.  To run your service, you must do three things in **three different windows**:

- In one terminal, launch the `courses_microservice` from the `courses_microservice` directory.
- In a second terminal, launch your `mp6` microservice from the `mp6` directory.
- Finally, on a browser, connect to [http://127.0.0.1:5000/](http://127.0.0.1:5000/) to view your app!


### Testing Suggestions

Ensure that your server works for courses many days in the future **AND** for courses later the same day.  (Ex: If you check your service at 9:00am for your 1:00pm class, it should report weather back for the 1:00pm class that same day.)

Test several different courses:

- CS 225 is a good test case since it starts at 11:00am and uses the forecast for the exact time the class starts (11:00am).
- CS 340 is another good test case since it starts at 12:30pm and the forecasts are for a one hour period (ex: 12:00pm - 1:00pm).
- TEST 999 is a special test-case course we have hard-coded to always be 6 days and 23 hours in the future.


### Automated Tests via `pytest`

Only once you have tested it in the web browser and **ensured your caching works**, you can run automated testing.  <span style="color: red; font-weight: bold">IMPORTANT: If you run this test suite without caching the NWS forecast</span>, you may be automatically timed out by the NWS server and won't be able to run tests for several hours.

Once you have made absolutely certain you are using your cached weather forecast for each request after the first, you can run the automated tests:

- On Windows, run `py -m pytest`
- On macOS, run `python3 -m pytest`



## Submission

Once you have locally passed all the tests, you will need to submit and grade your code.  First commit using the standard git commands:

```
git add -A
git commit -m "MP submission"
git push
```


### Grading

The initial grading is done via a manual GitHub Action.  You **MUST** complete this step before the deadline to earn any points for the MP:

- Navigate to your repository on [https://github.com/cs340-illinois](https://github.com/cs340-illinois).
- Click on the "Action" Tab
- Choose "mp6 autograding"
- Click the green "Run Workflow" button (located on the blue bar)
- Press "Run Workflow"
- You will trigger a GitHub Action that will complete the grading run!



### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| autograding |  40 (scaled from 15 in the GitHub Action) |