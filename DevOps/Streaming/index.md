---
title: Streaming
author: Christian Külker
date: 2023-03-23
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Streaming
commands:
- vlc
tags:
- Vlc
- Stream Generator
- Streaming
- Streaming Server
- Streaming Client
- Streaming of Streams
- Icecast2
- Ices2
- Darkice
description: Streaming is basically to copy a long file over the network.

---

## VLC Streaming

The "poor man's" streaming solution is __vlc__. When you first look into what
is needed to stream a file, directory or media collection locally, you will
find a lot of tutorials suggesting to use a _stream generator_ like `ices2` or
`darkice` and connect it to a _streaming server_ like `icecast2`. While this
can be set up in maybe 15 minutes and can be very robust, it seems to have too
much complexity. The solution here is to simply use `vlc`. One command on the
server and one command on the client.

Scenario:

- Desktop aka _the server_
- Laptop A
- Laptop B

### Server

Browse to a directory that contains audio or video files, or use a file. Run
of the following commands in the directory or file.

```bash
# 1
vlc -vvv `pwd` --sout '#rtp{mux=ts,dst=239.255.12.42,sdp=sap,name="Stream"}'
# 2
vlc -vvv --sout '#rtp{mux=ts,dst=239.255.12.42,sdp=sap,name="Stream"}' .
# 3
export RTP="#rtp{mux=ts,dst=239.255.12.42,sdp=sap,name='Stream'}"
find -name "*.mp4" -print0|xargs -0 vlc --sout $RTP
```

It is also possible to stream a stream. This has the advantage that the
download bandwidth is used only once and that all clients play the stream via
the local network. This works in principle with `m3u8` and `mp3` `URL` types.

```bash
# 1
export URL=https://example.org/stream.m3u8
# 2
export URL=https://example.org/stream.mp3
vlc --sout '#rtp{mux=ts,dst=239.255.12.42,sdp=sap,name="Stream"}' $URL
```

The GUI can be used to steer the stream.

While it is possible to add a `dublicate` rule to `vlc` that would _display_
the video or play the sound on the local machine, the disadvantage of such a
solution is a delay in playback. If you use, say, the desktop in room A and the
laptop in room B, you would hear an unpleasant delay. To not hear a delay, just
run a `vlc` client on a second terminal.

```bash
vlc rtp://239.255.12.42
```

This also has the advantage of being a test to see if it works.

### Client

For each client (Laptop A, Laptop B), run a `vlc` that connects to this
multicast address.

```bash
vlc rtp://239.255.12.42
```

### Resources

- <https://wiki.videolan.org/Documentation:Streaming_HowTo/Command_Line_Examples/>
- <https://wiki.videolan.org/Documentation:Streaming_HowTo_New/>
- <https://wiki.videolan.org/Documentation:Streaming_HowTo/Receive_and_Save_a_Stream/>
- <https://wiki.videolan.org/Documentation:Streaming_HowTo/Advanced_Streaming_Using_the_Command_Line/>
- <https://wiki.videolan.org/Documentation:Streaming_HowTo/Streaming,_Muxers_and_Codecs/>
- <https://wiki.videolan.org/VLC_HowTo/Transcode_multiple_videos/>

Icecast examples:

- <https://blog.michael.franzl.name/2013/11/25/audio-streaming/>
- <https://www.linuxuprising.com/2019/09/how-to-create-your-own-internet-radio.html>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-03-23 | Streaming of streams                                 |
| 0.1.0   | 2023-03-09 | Initial release with vlc                             |

