---
title: Bigger Than the Whole Maze

date: 2023-04-04
due: 2023-04-11 23:59:59

points: 40

number: 9
published: true
---

# Overview

This MP is all about creating something as a course, together, that all of our work contributes to the final deliverable. You will develop a small but integral part of a complex software system.  Unlike the other MPs, there is no "solution" to this MP -- at this point, the final deliverable does not exist and we are building it together.

This MP is "Bigger Than the Whole Maze" since we will be creating an **infinite maze**.  The maze will initially start with one small cell with many exits to other regions:
- When you reach an "exit", a new maze segment is generated to grow the maze with more exits.
- Everyone contributes multiple unique microservices to create maze segments.
- All of the microservice runs simultaneously during the deployment, and each microservice has a random chance to be used as the next generated maze segment.
- You can view an early [proof of concept right here](maze-poc.html).

To earn points for this MP, you must commit your code via git **AND** run your microservice on your VM during the course-wide deployment during lecture on Thursday, April 13 at 12:40pm.

Python libraries outside of the Python Standard Library are allowed for this MP. For example, you could use NumPy or pandas to help randomize your maze, as long as the libraries used don't do the assignment for you.


## Initial Files

In your CS 340 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp9 --allow-unrelated-histories -m "Merging initial files"
```


## Part 1: Running the Provided Code

The middleware and frontend are completed and provided for you.

- First, start a mongo server by running `docker run -p 27017:27017 mongo`, then
- Run the MP9 maze server by running `py -m flask run` (or `python3` on macOS).
- You should see a maze with only `Err 503` tiles in the maze when connecting to the server with a web browser.

You will implement the backend microservices called "Maze Generators" or MGs that generate maze segments.  You MGs will be combined with other students' MGs to create a global, course maze live in lecture on April 13.


## Part 2: Technical Requirement for MGs

For MP9, you are required to create at least two MGs: at least one **static** MG and at least one **dynamic** MG.  The MGs should be interesting and inspiring.

- A **static** MG returns the same maze every time.

- A **dynamic** MG returns a different maze (with high probability) that has some design element to it.  *A completely random mess of random walls do not satisfy this requirement.*

You must create each of these MGs inside of the `mp9/MGs` folder as new, individual subfolders.


### Generating a New Maze Tile: `GET /generate`

Your MG will respond to requests from the middleware via a response to the `GET /generate` route.  This route returns a JSON that must contain `geom` that contains the geometry of the maze segment.  The geometry is a array of strings, where each string is one row of the maze and each character is a single cell in the maze.  For example:

- `{"geom": ["988088c", "1000004", "1000004", "0000000", "1000004", "1000004", "3220226"]}` is the geometry of an "empty" 7x7 segment.

Each character is a hex digit that encodes the walls of that cell.

- The most significant bit is `1` if there's a wall on the north side of the cell,
- The 2nd most significant bit is `1` if there's a wall on the east side of the cell,
- The 3rd most significant bit is `1` if there's a wall on the south side of the cell, and
- The least significant bit is `1` if there's a wall on the west side of the cell

### Connecting your MG to the Maze Server via `PUT /addMG`

To add an MG to the maze server, you must inform the maze server that your MG has launched.  This is done via a `PUT /addMG` request sent to the maze server.  This request must include a JSON with keys `'name'`, `'url'`, and `'author'`. For example:

```
{
  "name": "generator1",
  "url": "http://127.0.0.1:34000/",
  "author": "Your Name"
}
```

- The `name` value is displayed in the API's list of available servers.

- The `url` value will be used the URL to communciate with your MG when sending a `GET /generate` request.

- The `author` must be your name if you want to get credit for the MP! :)

- The maze server `/addMG` endpoint will respond with an HTTP/400 if the JSON is malformed.


**Hint**: Configure your MG server to add itself to the API on startup.  If the MG server has already been added, sending a second identical request to `/addMG` will have no effect so it's okay to restart your maze and re-send the `/addMG`.


### Testing

Once you have your static MG, test it using your `localhost` server!  When your MG is connected, you will begin to see your tiles instead of `Err 503`! :)

Make sure to complete both your **static** and **dynamic** MG.  Your MGs must be creative and well-designed -- a randomized hex string will not earn credit.  Specifically unique designs may earn up to +5 EC.

Once you have a working local version, you'll join the whole class for one giant maze!



## Part 3: Running your MGs as part of Lecture on April 13

In this part, you will connect your MGs to the server that is running the course-wide shared middleware. 


### Requirement 1: Deploy your MG on your CS 340 VM

You did this for MP7, refer back to MP7 if you need details regarding your VM.


### Requirement 2: Connect with the Course-Wide Microservice

A course-wide infinite maze server will be hosted at `http://sp23-cs340-adm.cs.illinois.edu:34000/` on the morning of Thursday, April 13.  You must configure your MGs on your VM to connect to this server when connecting to the course-wide microservice.

- Your MGs must connect via your course-provided **VM**.

- I **highly** recommend that you use a `.env` or other config to easily switch between testing your microservice locally with localhost and connecting it to the course-wide microservice.

- I'll post in #announce when this server goes live on Thursday morning.


<div class="alert alert-primary">

<h4>Test Server Available</h4>

<p>A test server is currently available at <code>http://sp23-cs340-adm.cs.illinois.edu:34999/</code>.  Make sure to connect to this test server to verify the back/forth communication with your maze. <b>Make certain to test it.</b>  Once you verify you can connect to the test server, it is only a matter of adjusting to :34000 for the live deployment.</p>

<p>Since your server must to communicate with a server not on your same physical machine, you cannot just use <code>localhost</code>/<code>127.0.0.1</code>.  Instead, you must provide the address of your VM (<code>http://sp23-cs340-###.cs.illinois.edu:#####</code>) when connecting to the course-wide server.</p>

<p>In addition, since your flask server must receive connections from the course-wide server, you must have your flask app listen for all connections.  Make sure your MG is running with <code>--host=0.0.0.0</code> or configure your <code>.env</code> to include <code>FLASK_RUN_HOST="0.0.0.0"</code></p>

<!--
<p>Finally, there will be additional office hours to test your connection at 4:00pm-5:00pm on Wednesday.  See the Canvas announcement for more details on the extra office horus!</p>
-->

</div>



### Requirement 3: Participating in the Infinite Maze

We will launch the course-wide infinite maze on **Thursday, April. 13** in lecture!

- Your MGs must be connected to the course-wide service by 12:40pm (10 minutes after lecture starts), running on your VM.

- **EVERYONE** must run their own MGs on their own VMs.

- Be prepared to speak briefly about your MG.  We will go through several MG and see your MGs you have developed and see the maze generated live before we deploy the full maze.











## Submission

You must commit your MGs to GitHub before the Tuesday deadline using the standard git commit+push commands:

```
git add -A
git commit -m "MP submission"
git push
```

### Grading

To earn any points at all, your code must be committed by the Tuesday deadline.  If that requirement is met, your grading is based on your maze's participation in the course-wide maze on Thursday, April 13.


### Points

The 40 points for this MP are split in the following way:

| Description | CS 340 Course Points |
| ----------- | -------------------- |
| Required: MP9 Code on GitHub | Requirement for Any Points |
| Deployment and deployment of your static MG in lecture | 20 |
| Deployment and deployment of your dynamic MG in lecture | 20 |
