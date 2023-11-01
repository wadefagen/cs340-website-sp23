---
title: Final Project - Week 2 (Generating Music)

date: 2021-04-26
due: 2021-05-03 23:59:59

points: 50

layout: project

number: Week 2
published: false
---

# Final Project - Week 2

One aspect of systems that often gets lost is the ability to create very high-level work from very simple systems.  This week, you will use the versioned state server in a different way than you expected: to create music.

## Learning Objectives

- Read command-line arguments in a Python program.
- Use an HTTP API you've programmed for a new purpose.
- Understand how Python can be used to create MIDI (music) files.


## Initial Files

Because we will be adding on to existing code each week, we will be switching back to github-dev for the final project. This will allow us to add on more code and minimize the amount of copy pasting.

In your CS 240 directory (same location you used for the github-dev MPs), merge the initial starting files with the following commands:

```
git fetch release
git merge release/project-part2 -m "Merging initial files"
```


## Overview

In this MP, you will use the versioned state server to create music.  Your MP must:

- Use the `--server ADDRESS` command line option to specify the server your program will connect to for music data.
- Use the `--song NAME` command line option to specify the song you program will ask the server to retrieve.

For example, `python music.py --server "localhost:5000" --song hello` must request the song `hello` from `localhost:5000`.


### Reading the Command Line

The standard way to parse Python command line arguments is to use `argparse`: [https://docs.python.org/3/library/argparse.html](https://docs.python.org/3/library/argparse.html).


### Requesting a Song

Each song is stored in the versioned state server as notes, where each note is an entry ("version") in your datastore.  The URL that you must request the song from will be the `server` appended with the `name`.  For example, `python3 music.py --server "localhost:5000" --song hello` would request the song `hello` from `localhost:5000`.

An example song `hello` might be comprised of three notes and will be stored as three entires in the datastore:

- `GET /hello/1`: `{"value": "{"note": "g3", "duration": 0.75}", "version": 1}` (HTTP/200)
- `GET /hello/2`: `{"value": "{"note": "g3", "duration": 0.25}", "version": 2}` (HTTP/200)
- `GET /hello/3`: `{"value": "{"note": "a3", "duration": 1}", "version": 3}` (HTTP/200)
- `GET /hello/4`: Not Found (HTTP/404)

Note that the value of each version is a JSON string with at least two keys: `note` and `duration`.  The song ends when there are no more notes.


### Constructing the Song

A song can be constructed by using the `music21` Python library ([https://web.mit.edu/music21/](https://web.mit.edu/music21/)).  This library can create a simple music file by appending `music21.note.Note` data structures together in a Python list.  Specifically:

- `new_note = note.Note("g3")`, creates a new `g3` note,
- `new_note.duration = duration.Duration(1)`, sets the duration to be `1` (in this library, durations are always measured in quarter notes),
- `new_note.offset = 0`, sets the offset to be `0` quarter notes from the start of the file,

*Note: In the songs you generate, the next note **must** always play immediately after the end of the previous note.  In the example `hello` song, the second note's `offset` must be 0.75 since it must begin as soon as the first note ends (which itself has a `duration` of 0.75).*

Finally, the following code can save your list of notes to a MIDI file (ensuring you save it as `SONG.mid`, where `SONG` is the song name specified by the command line option):

```
from music21 import stream

midi_stream = stream.Stream(list_of_notes)
midi_stream.write("midi", fp="SONG.mid")  # Ensure `SONG` is replaced with the name of the song from the command line.
```


### Additional Consideration

The state server may return a value with a JSON dictionary that contains **more** than `note` and `duration`.  Specifically, there are two additional features that **must** be implemented:

**[Consideration #1: Tempo]**: If the `tempo` value is present, you must add a metronome mark **before** the note specified.  For example, the response may be:

`GET /example/1`: `{"value": "{"note": "g3", "duration": 0.75, "tempo": 120}", "version": 1}` (HTTP/200)

To add a metronome mark, the following snippet of code for music21 will help you out:

```
metronome_mark = tempo.MetronomeMark(number=tempo)  # Create a new MetronomeMark
metronome_mark.offset = current_offset   # Set the offset of the note
list_of_notes.append(metronome_mark)   # Add the note to the list of notes
```


**[Consideration #2: Chords]**: If the `offset` value is present, you must use the offset specified and you should **NOT** implement the global offset.  (Remember, when the offset is not present you must play the next note after the previous note ends; the `offset` changes this behavior so that the note's duration should not modify the global offset.)  For example, a response to two requests might be:

- `GET /example2/1`: `{"value": "{"note": "g3", "duration": 0.75, "offset": 0}", "version": 1}` (HTTP/200)
- `GET /example2/2`: `{"value": "{"note": "e4", "duration": 0.75, "offset": 0}", "version": 2}` (HTTP/200)

This allows for multiple notes to be played at the same time, creating a musical chord.


**[Consideration #3: Everything Else]**: You can (and should!) feel free to implement more complex musical features as additional fields that may be present (ex: instruments, flags, etc).  Your program must not fail if additional fields are present, but can use them in any way you choose. :)


## Running Your Program

I have transcribed a song and hosted it on `sp21-cs240-adm.cs.illinois.edu:24000` and called it `mystery`.   While on the campus VPN, check out the following link to see the first note (since it's a response to the `GET` request, a web browser displays it):

[http://sp21-cs240-adm.cs.illinois.edu:24000/mystery/1](http://sp21-cs240-adm.cs.illinois.edu:24000/mystery/1)


To retrieve this song, run:

```
python music.py --server "sp21-cs240-adm.cs.illinois.edu:24000" --song "mystery"
```

Your program must produce `mystery.mid` and you will likely recognize this song.  (A correctly implemented solution will produce a MIDI that is 12 seconds in length.)



## Submit

When you have completed your program, double-check that your server runs as expected.  When you are ready, submit the code via the following git commands:

```
git add -A
git commit -m "submission"
git push origin master
```

You can verify your code was successfully submitted by viewing your git repo via the web interface here: [https://github-dev.cs.illinois.edu/cs240-sp21](https://github-dev.cs.illinois.edu/cs240-sp21)