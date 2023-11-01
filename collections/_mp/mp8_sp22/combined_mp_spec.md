---
title: MapReduce Server

date: 2022-03-25
due: 2022-04-04 23:59:59

points: 50

number: 8
published: false
---

<!-- Saving this file in case we will use the combined MP8 + 9 for future semesters -->

# Overview

In lecture, you have learned about MapReduce and how this programming model is useful for processing and generating data in a parallel, distributed manner. In this MP, you will implement MapReduce mappers and reducers to solve many classical problems using MapReduce.

Specifically:

- You will write several mapper functions to map files into key-value pairs that can be reduced,
- You will write several reducer functions to reduce key-values sets,
- You will solve different types of problems using MapReduce

Then, you will wrap your local MapReduce programs in a cloud-based API. Your service will accept input data through HTTP requests, process the data by running the MapReduce job specified by the user, and store the result in MongoDB for later retrieval. 


## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp8 -m "Merging initial files"
```




## Part 1: MapReduce

You will complete the code to accomplish the following MapReduce tasks:

- Count the number of letters in text, in `count_letters.py`
- Count the number of words in text, in `count_words.py`
- Count the length of words in text, in `count_word_length.py`
- Count the number of bigrams in text, in `count_bigrams.py`
- Find the set of mutual friends of pairs of friends, in `find_mutual_friends.py`. 

Clarification on `find_mutual_friends.py`
- You are expected to output mutual friends for pair of users who are already "friends". If your output contains mutual friends for those pair of users who are not friends - it is still fine. But apart from pair of users as the key and their mutual friends as the value (in the format specified in the MP release) you shouldn't output any other information.
- You can assume that the friendship relationship is symmetric i.e. if A has B as a friend, B will have A as friend too.
- Your implementation should run on both `data/graph0/*.txt` as well as `data/graphs/graph-0.txt` i.e. the input file doesn't need to always contain just one user's data. It can have multiple users' data.
- For the purpose of this MP, you can assume that a single line will represent the complete list of friends for that user. Note that the ideal solution doesn't need to rely on this assumption and can support multiple lines of input for a single user.

### Required Structure by Example: `count_letters.py`

Each of these tasks must be solved using a mapper and a reducer in MapReduce.  The code for `count_letters.py` has been provided and all functions should use an identical structure:

```py
if __name__ == '__main__':
  mr = MapReduce(map_letters.map, reduce_sum.reduce)
  mr( sys.argv[1:] )
```


### Mapper Functions

In `count_letters.py`, the code imports `mappers/map_letters.py`.  This, and all map functions, must implement a function named `map` that receives the name of the input file as its one and only parameter.  The `map` function must completely read the file and return a dictionary (native Python `dict`) representation of the file as key-value pairs **OR** an array of dictionaries (if the one file should create multiple summaries).

Your code for `mappers/map_letters.py` might have the following structure:

```py
# map function:
def map(filename):
  # Open the file and read the contents:
  with open(filename, "r", encoding="utf-8") as f:
    lines = f.read()

  # Accumulate the letter count in a dictionary, to create:
  #   {"a": 3, "b": 7, ...}
  # ...

  # Return the dictionary:
  return d
```

When writing the mappers:

- You **must** place your mappers in the `mappers` directory.  You will need to create new files.
- For mappers that deal with text, transform the text to all lower case and treat all non-letter characters as delimiters.  (Letters are only `a`-`z`.)
- For mappers that deal with other data, do not transform them in any way.


### Reducer Functions

In `count_letters.py`, the code imports `reducers/reduce_sum.py`.  This, and all reduce functions, must implement a function named `reduce` that receives two dictionaries (native Python `dict`) and return one reduced dictionary (also as a native `dict`).

Your code for `reducers/reduce_sum.py` might have the following structure:

```py
def reduce(left, right):
  # Merge `left` into `right`:
  # ...
    
  return right
```

When writing your reducers:

- You **must** place your reducers in the `reducers` directory.  You will need to create new files.


### Testing Part 1

Each MapReduce program can be run by calling Python with arguments for the input files.  A number of example input files have been provided for you in `data`.  For example:

- `python count_letters.py data/books/*.txt` will count the letters in the files contained in `data/books/*.txt` files.  A subset of the expected output is provided at the top of `count_letters.py`.  *(The text from the books come from Gutenberg Project texts.)*

- `python count_bigrams.py data/taylor/*.txt` will count the letters in the files contained in `data/taylor/*.txt` files.  A subset of the expected output is provided at the top of `count_bigrams.py`.  *(The lyrics are from Taylor Swift's "Blank Space" song.)*

- ...etc...

You can find the expected output of the MapReduce programs in `expected_output`. 

We have also provided a test suite to test the correctness of your MapReduce logic: 

- `python -m pytest test_mapreduce.py`


## Part 2: Cloud-based API


You will then wrap your local MapReduce programs in a web-based service. Your web server will accept `PUT`, `GET` and `DELETE` requests to create, retrieve and delete MapReduce jobs with input data supplied by the client. You will use MongoDB to store the result of the MapReduce job. 


### Requirements

For part 2, a nearly blank Python file is provided for you in `microservice/app.py`. You need to populate it to create a server that supports the following four API(s):

#### 1. `PUT /<mapreduce_job>/<dataset_identifier>`

- Action: Create a MapReduce job to process the data sent in the request body. You need to do the following 
  - Save the request data to a temporary file
  - Then run the MapReduce job specified by `mapreduce_job` on the data to generate an output file
  - Store the MapReduce output to MongoDB so that the user can retrieve them later. 
    - You should store the `<key>: <value>` pair in each line of the output file separately instead of storing the whole file as one entry in the database for faster retrieval.
    - The dataset is identified by `dataset_identifier` that you should use to distinguish between different datasets (e.g. `/count_bigrams/books` and `/count_bigrams/taylor`). 
- Example: `PUT /count_words/taylor`
- Returns: `HTTP/200` if successful.
- Notes:
  - You should assume the request content is a UTF-8 string. In `flask`, you can get the data of the request as a UTF-8 string with the following code: `value = request.data.decode("utf-8")`.

#### 2. `GET /<mapreduce_job>/<dataset_identifier>/<key>`

- Action: Return a JSON containing the key-value pair from the output of the `mapreduce_job` for `dataset_identifier`.
- Example: `GET /count_words/taylor/you`
- Returns: `HTTP/200` if successful, or `HTTP/404` if the key was not found.
- Example JSON: `{ "dataset_identifier": "taylor", "key": "you", "value": 31 }`

#### 3. `DELETE /<mapreduce_job>`

- Action: Delete all data associated with a `mapreduce_job`.
- Example: `DELETE /count_letters`
- Returns: `HTTP/200` if successful.

#### 4. `DELETE /<mapreduce_job>/<dataset_identifier>`

- Action: Delete all data associated with a `mapreduce_job` and `dataset_identifier`
- Example: `DELETE /count_letters/taylor`
- Returns: `HTTP/200` if successful.


### Using MongoDB

You need to install the `mongodb` Python library using either:
- `python -m pip install pymongo` or `pip install pymongo`

Additionally, you will want to run a `mongodb` server to connect to.  The quickest way to get one running is to use docker:

- `docker run --rm -it -p 27017:27017 mongo`

The documentation for `mongodb` can be found here: [https://pymongo.readthedocs.io/en/stable/](https://pymongo.readthedocs.io/en/stable/)

- Every database is made up of "collections".  Similarly, you will need to "create" a collection.  Note, just by using a collection will create it!
- Your database **MUST** be named `mp8-state-server`.  This is important to allow us to verify the contents of the database. (The `app.py` already sets this value in `db = mongo["mp8-state-server"]`)



### Testing Part 2

You can debug your code by running your server and sending requests to your server. As usual, navigate into the `microservice` directory and launch the `flask` app using the standard command:

```
python -m flask run
```

There are a variety of tools available for sending HTTP requests:

- using `curl` commands in the terminal
- using application such as Postman

You can also run the test suite we provide by running `python -m pytest test_microservice`. 

Note: Before you can test your flask application, make sure the `mongodb` server is running. 


## Submit

When you have completed your program, double-check that your MapReduce logic is correct and your server runs as expected.  When you are ready, submit the code via the following git commands:

```
git add -u
git commit -m "MP submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there.
