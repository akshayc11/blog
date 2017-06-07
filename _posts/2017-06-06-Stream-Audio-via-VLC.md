---
layout: page
title: How to set up a VLC stream in command line
---

In this article, I describe how to set up a VLC stream in command line


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Scenario](#scenario)
  - [Requirements:](#requirements)
- [Procedure](#procedure)
  - [GUI](#gui)
  - [CLI:](#cli)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Scenario
I have a mp3 file that I want to stream across a network using http

### Requirements:
1. Stream the mp3 file
2. Has to be done via command line
3. mp3 file should play as a loop


## Procedure
Below, I detail the procedure to stream a loop of a single MP3 file, both using the GUI, and the CLI

### GUI
Now, if we had access to a GUI, it is very simple to set up a stream without loop:
1. Open the VLC media player
2. Add the mp3 file to the playlist
3. go to media-> Stream...
4. Add the files using the add button.
5. Click on Stream
6. In next window verify the path, and click next
7. Destination setup: select http and click add
8. Change IP and path if you wish, click next (I chose port: 8080 path: /stream)
9. Transcoding options: I chose transcode, Audio-MP3
10. Miscellaneous Options (It showed the following in the generated stream output string):

```:sout=#transcode{vcodec=none,acodec=mp3,ab=128,channels=2,samplerate=44100}:http{mux=mp3,dst=:8080/stream} :sout-keep
```

11. Click stream, and you are done

To loop, just click on the loop button in the GUI.

### CLI
1. Run the command:

```cvlc <path to mp3 file> --sout="#transcode{vcodec=none,acodec=mp3,ab=128,channels=2,samplerate=44100}:http{mux=mp3,dst=:8080/stream}" --sout-keep --loop
```

## Accessing the stream
In a VLC instance, or a web radio player, type out the address as http://<ipaddress>:8080/stream