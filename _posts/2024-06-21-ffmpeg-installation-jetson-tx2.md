---
layout: post
title: FFmpeg Installation on the Jetson TX2
description: "Step-by-step guide to install FFmpeg on Nvidia Jetson TX2."
date: 2024-06-21 09:00:00-0400
categories: [Tutorial, Streaming, FFmpeg, Nvidia]
tags: [FFmpeg, Jetson TX2, Nvidia, Installation]
thumbnail: assets/img/blog/ffmpeg_jetson1.jpg
---

{% include figure.liquid loading="eager" path="assets/img/blog/ffmpeg_jetson.jpg" class="img-fluid rounded z-depth-1" %}

While Nvidia does not officially support FFmpeg on Jetson devices (Nano, TX2), it's worth noting that FFmpeg can leverage Jetson's powerful hardware resources as an encoder and decoder. This unique capability makes it a compelling choice for your projects. Here's a detailed guide to installing FFmpeg on the Jetson TX2 using JetPack SDK 4.3.

### Prerequisites

Since FFmpeg is built from source, we first need to install the necessary dependencies:

```bash
$ sudo apt build-dep ffmpeg
```

If you encounter the error E: You must put some 'source' URIs in your sources.list, uncomment the source lines in /etc/apt/sources.list and update the package lists:

```bash
$ sudo vi /etc/apt/sources.list
```

Uncomment the line starting with `deb-src`, save (`:wq`), and then update the repository:

```bash
$ sudo apt update
$ sudo apt build-dep ffmpeg
```

## Build dependent libraries
Clone the Jetson-optimized FFmpeg repository and build it:
```bash
$ git clone https://github.com/jocover/jetson-ffmpeg.git
$ cd jetson-ffmpeg
$ mkdir build
$ cd build
$ cmake ..
$ make
$ sudo make install
$ sudo ldconfig
```

## FFmpeg patch and build
Clone FFmpeg repository and apply necessary patches:
```bash
$ git clone git://source.ffmpeg.org/ffmpeg.git -b release/4.2 --depth=1
$ cd ffmpeg
$ wget https://github.com/jocover/jetson-ffmpeg/raw/master/ffmpeg_nvmpi.patch
$ git apply ffmpeg_nvmpi.patch
$ ./configure --enable-nvmpi
$ make -j4 2>&1 | tee make.log
$ sudo make install
```

Confirm installation:
```bash
$ which ffmpeg
/usr/local/bin/ffmpeg
$ ffmpeg -codecs | grep 264
```
You should see the output confirming that `h264_nvmpi` is included in the H.264 decoder and encoder.
```bash
ffmpeg version 3eedf15 Copyright (c) 2000-2019 the FFmpeg developers
  built with gcc 7 (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04)
  configuration: --enable-nvmpi
  libavutil 56. 31.100 / 56. 31.100
  libavcodec     58. 54.100 / 58. 54.100
  libavformat    58. 29.100 / 58. 29.100
  libavdevice    58.  8.100 / 58.  8.100
  libavfilter     7. 57.100 /  7. 57.100
  libswscale      5.  5.100 /  5.  5.100
  libswresample   3.  5.100 /  3.  5.100
 DEV.LS h264                 H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10 (decoders: h264 h264_v4l2m2m h264_nvmpi ) (encoders: h264_nvmpi h264_v4l2m2m h264_vaapi )
```
Once you've followed these steps, you can confirm the successful installation by checking if `h264_nvmpi` is included in both the H.264 decoder and encoder. This confirmation will give you the satisfaction of a job well done.

The first version of the blog is available [here](https://iamgmujtaba.medium.com/ffmpegs-installation-on-the-jetson-tx2-66b5a3f21d02).