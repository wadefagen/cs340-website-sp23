---
title: Telephone (Bad Translator)

date: 2022-04-01
due: 2022-04-11 23:59:59

points: 50

number: 9
published: false
---

# Overview

Cloud-based services offer a means of carrying out complex computational tasks without needing to write large amounts of code or spending excessive time and disk space configuring services locally.
In this MP, you will use cloud service providers to finish implementing functionality for a simple browser-based game of [telephone](https://en.wikipedia.org/wiki/Telephone_game). To do this, you will:

  - Complete a translation function by using calls to the API of a cloud translation service
  - Complete a transcription function by using calls to the API of a separate cloud transcription service
  - Implement an in-memory cache for the data returned by each of these cloud APIs
  - Interact with the provided frontend to play a game of telephone!

## Initial Files

In your CS 240 directory, merge the initial starting files with the following commands:

```
git fetch release
git merge release/mp9 -m "Merging initial files"
```
## MP Overview Session

An MP Overview was held by Patrick on Monday, April 4 at 11:00am.

- Recording Link: [https://mediaspace.illinois.edu/media/t/1_2v2tu1dw](https://mediaspace.illinois.edu/media/t/1_2v2tu1dw)

## Machine Problem

### Requirements

In this MP, an `app.py` Python file is provided with two endpoints setup for `/telephone` and `/transcribe`.
These endpoints respectively call the `translate()` and `transcribe()` functions, which you will need to finish implementing in parts 1 and 2 of this MP to get the Telephone server fully functioning.
You will also need to implement an in-memory cache for cloud translations and transcriptions to speed up your server in part 3 of this MP.

*NOTE*: For calls to the translation and transcription APIs, **you will need to be on campus or connected to the campus VPN**.

### Part 1 - Implementing `translate()`

The `translate()` function takes a string `s`, source language `src`, and destination language `dest` as input, and returns `s` translated from `src` to `dest` as output.
To complete this function, you will need to make a POST request to a cloud translation API, which can be found at the following URL: [http://sp22-cs240-adm.cs.illinois.edu:5000/translate](http://sp22-cs240-adm.cs.illinois.edu:5000/translate).
This URL is passed to the `translate()` function automatically as the `endpoint` parameter.

The API takes a POST request where the POST body is a JSON object with the following fields:

- `"q"     ` : the query string to be translated
- `"source"` : the language code (defined at the top of `app.py`) for the language being translated from
- `"target"` : the language code for the language being translated to
- `"format"` : the format of the returned data (should always be "text")

On success, the translation API returns a JSON of the format `{"translatedText": "<your translated string>"}`.
On any type of failure, the API returns a JSON of the format `{"error": "<some error message>"}`.
Your `translate()` function should return `None` on any kind of error or whenever the translated string is empty (e.g., `''`).
Otherwise, your `translate()` function should just return the translated string.

To test your `translate()` function, run your Flask application in a console and navigate to [http://127.0.0.1:5000/](http://127.0.0.1:5000/).
Enter whatever text you'd like in the bottom text box and click "Start the Telephone!" to watch your bad translator in action. 🙂

### Part 2 - Implementing `transcribe()`

The `transcribe()` function takes a filename `fin` as an input, and returns a text transcription of `fin` as output.
To complete this function, you will need to make a POST request to a cloud transcription API, which can be found at the following URL: [http://sp22-cs240-adm.cs.illinois.edu:8080/stt](http://sp22-cs240-adm.cs.illinois.edu:8080/stt).
This URL is passed to the `transcribe()` function automatically as the `endpoint` parameter.

The API takes a POST request where the POST body is raw binary audio data (e.g., the contents of `fin`) in `.wav` format.
To send this data, you will need to set the `data` attribute of your POST request to the contents of `fin`, and set the `Content-Type` header of the POST request to `'application/octet-stream'`.

On success, the transcription API returns a plain text string (NOT a JSON) representing the transcribed audio file.
On failure, the API returns the plain text string `"Speech to text error"`.
Your `transcribe()` function should return `None` on any kind of error, including invalid filenames, invalid audio files, and speech to text errors.
Otherwise, your `transcribe()` function should just return the transcribed string.

To test your `transcribe()` function, run your Flask application in a console and navigate to [http://127.0.0.1:5000/](http://127.0.0.1:5000/).
Upload one of the provided ".wav" files in the `test-audio` folder and click the "Transcribe!" button; after a few seconds, a transcription of the audio file should autofill the text box below, which you can then use to start a new game of telephone.
Feel free to use your own (small) audio files as well -- there's no guarantee they'll be transcribed correctly, but that might just make it more interesting!

### Part 3 - Caching Your Results

Although cloud computation can be helpful by allowing us to treat functions as black boxes, repeated calls to cloud APIs can be both time-consuming and, for paid services, expensive.
In this last part of the MP, you should modify your `translate()` and `transcribe()` functions to use an in-memory cache to store outputs for a given set of inputs.
Subsequent calls to either of these functions with inputs that have already been seen before should immediately return the translation / transcription respectively from the in-memory cache.

For the `translate()` function, your cache should consider inputs identical when the combination of (query string + source language + destination language) has been seen before.

For the `transcribe()` function, your cache should consider inputs identical if the contents of the input file have been seen before, independent of the file name.

You may find the provided `hashString()` and `hashFile()` functions useful, though you are welcome to implement the cache using any Python standard libraries you see fit!

### Testing Your Code

You can run the test suite we provide by running `python -m pytest test_telephone.py`.

## Submit

When you have completed your program, double-check that your server runs as expected. When you are ready, submit the code via the following git commands:

```
git add -u
git commit -m "MP submission"
git push
```

You can verify your code was successfully submitted by viewing your git repo on your github account: [https://github.com](https://github.com). You should also be able to see if your code passed our test cases from there.
