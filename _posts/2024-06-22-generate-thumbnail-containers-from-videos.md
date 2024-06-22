---
layout: post
title: "Generate Thumbnail Containers from Videos like YouTube"
description: "Learn how to generate thumbnail containers from videos using Python and FFmpeg."
categories: [Tutorial, Streaming, YouTube]
tags: [Video, Thumbnails, YouTube, Python, FFmpeg]
thumbnail: assets/img/blog/thumb_sample.png
---


This repository explains the process to generate thumbnail containers from videos. In recent years, several methods have been designed to improve the demanding timeline manipulation for video browsing. One of the most popular methods for web-based video players is the use of lightweight thumbnail containers. Users can skim through the video player for efficiency and instantly preview a lengthy video, by watching the thumbnail preview in the web player interface. As the thumbnails are easy to integrate and navigate in web-based video players, several famous video-on-demand (VoD) streaming platforms adopt these in their end-user video players [ref1](https://arxiv.org/abs/2201.09049).


{% include figure.liquid loading="eager" path="assets/img/blog/thumb_sample.png" class="img-fluid rounded z-depth-1" %}

*Orientation of thumbnails on a single thumbnail container image (left), and the thumbnail usage for instant preview in the client web-based YouTube video player (right).*


### Installation

1.  Clone the repo by using the following command.
```shell
git clone https://github.com/iamgmujtaba/vid2tc.git
cd vid2tc
```
2. Create a conda environment and install packages.
```shell
conda create -n vid2tc python=3.8 -y
conda activate vid2tc
```
3. To install dependencies using pip, please type the command.
```shell
pip install -r requirements.txt
```

### Usage

```shell
python .\main.py -h

optional arguments:
  -h, --help            show this help message and exit
  -i INP_PATH, --inp_path INP_PATH   Input videos path
  -o OUT_PATH, --out_path OUT_PATH   Output videos path
  -s SEG_LEN,  --seg_len SEG_LEN     Segments length (seconds)
  -a AUDIO,    --audio AUDIO         Extract audio (True/False)
  -f FRAMES,   --frames FRAMES       Extract video frames (True/False)
  --thumb_width THUMB_WIDTH          Width of each thumbnail
  --thumb_height THUMB_HEIGHT        Height of each thumbnail
  --thumb_interval THUMB_INTERVAL    Extract the first frame as thumbnails at every 1 second
  --thumb_container THUMB_CONTAINER  5x5 (row,column) is default Thumbnail Containers
```

### Example
To generate thumbnail containers and prepare video segments, run the following script.
```shell
python .\main.py -i .\input\ -o .\output\
```

The main code is available at [GitHub vid2tc](https://github.com/iamgmujtaba/vid2tc).


##### Acknowledgement
The base code for this repository is borrowed from [Video Thumbnail Generator](https://github.com/flavioribeiro/video-thumbnail-generator).
