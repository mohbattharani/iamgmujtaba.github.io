---
layout: post
title: "Configure HLS Server on Windows 10/11"
description: "Learn how to configure HTTP Live Streaming (HLS) server on Windows 10/11 using Internet Information Services (IIS) and FFmpeg."
categories: [Tutorial, Streaming, Windows]
tags: [HLS, Streaming, Windows, IIS, FFmpeg]
thumbnail: assets/img/blog/hls_icon.jpg
---

HTTP Live Streaming (also known as HLS) is an HTTP-based adaptive bitrate streaming communications protocol developed by Apple Inc. and released in 2009. Support for the protocol is widespread in media players, web browsers, mobile devices, and streaming media servers. In the recent past, Adobe's Flash video technology was the primary method of delivering video via the Internet. However, there's been a significant shift in online video streaming. Specifically, online video delivered by protocols like HLS streaming and played by HTML5 video players has increasingly replaced Adobe's Flash protocol. HLS resembles MPEG-DASH, breaking the overall stream into a sequence of small HTTP-based file downloads, each downloading one short chunk of an overall potentially unbounded transport stream. A list of available streams, encoded at different bit rates, is sent to the client using an extended M3U playlist [REF](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). One key benefit of this protocol relates to its compatibility features. Unlike other streaming formats, HLS is compatible with various devices and firewalls. However, latency (or lag time) tends to be in the 15-30 second range with HLS live streams. Now, we will move forward towards the steps followed in the process:

## Step 1: Creation of the Web Server

Microsoft's Internet Information Services (IIS) is a practical and widely-used web server that we'll be using for this setup. Don't worry, the configuration process is not as daunting as it may seem. Just follow these simple steps:

1. Start by selecting and opening Windows features (WindowsKey+S (Search box) then type **Turn Windows features on or off**).
2. Find Internet Information Services and Tick the box (Wait for the process to configure and then **Close**).
3. Test it by opening Chrome and navigating to your local internet address, **127.0.0.1**.


4. The default page should appear (If not, reboot your system).

{% include figure.liquid loading="eager" path="assets/img/blog/iis_home.jpg" class="img-fluid rounded z-depth-1" %}

## Step 2: Change Default Physical Path

You can skip this step if you don't want to change the default path. All the files can be located at the default path **C:\inetpub\wwwroot\\**

Open IIS Manager (WindowsKey+S, then type IIS). The **Default Site** stores its files in a particular directory. To expose this information, right-click on it, choose **Manage Website**, then **Advanced Settings**. This will open a pop-up window with all the default site information, such as files or document roots, as they are typically known, enabled protocols, and even bindings. If you click on **Physical Path**, a button will appear on its extreme right where you can choose a different document root.

{% include figure.liquid loading="eager" path="assets/img/blog/iis_path.jpg" class="img-fluid rounded z-depth-1" %}

## Step 3: Enabling Cross-origin resource sharing (CORS)

To test streams, you must allow other websites to access files on your web server. However, not all modern browsers allow this by default due to security concerns. To allow this, you must explicitly tell the browser that you agree to a website to read data from your server. This is called cross-source resource sharing (CORS). To enable CORS to follow the below steps:

1. Open the webserver (WindowsKey+S then type **IIS**).
2. Select **Default Web Site** and Right Click or Double click **HTTP Response Headers**.
3. Select **Open Feature** from the Action. Then, click Add and Type in **Access-Control-Allow-Origin** for Name and type "*" for Value.
4. Click OK to add the header to add another value: type in **Access-Control-Allow-Headers** for Name; type in **Range** for Value.

{% include figure.liquid loading="eager" path="assets/img/blog/hrs_page.jpg" class="img-fluid rounded z-depth-1" %}

## Step 4: Adding the HLS MIME Type

HLS requires statements to learn how to analyze video and audio files. HLS manifest file ends in **.m3u8**. Windows IIS does not know about this extension. So, for IIS to correctly send the file to the player, you need to add this extension to IIS.

Under connections click your server and double Click MIME Types

1. Type **.m3u8** for File name extension; type **application/hls+xml** for MIME-type
2. Press okay.

{% include figure.liquid loading="eager" path="assets/img/blog/mime.jpg" class="img-fluid rounded z-depth-1" %}

## Step 5: FFmpeg Installation

You can skip Step 5 and Step 6, by downloading the processed video from [google drive](https://drive.google.com/drive/folders/1JS9lwJWr9pOibl9ZpOB6uAinh-PseZXG). After downloading the video place it into the default IIS physical path (i.e., C:\inetpub\wwwroot\) or the modified path.

1. Download FFmpeg from [here](https://www.ffmpeg.org/download.html#build-windows).
2. Extract the downloaded FFmpeg zip file to **C:\ffmpeg**.
3. Navigate to the **bin** folder under C:\ffmpeg and copy the address using Ctrl+C.
4. Open up the System information window and click on **Advanced System Settings**. Then click on **Environment Variables**.
5. Select the **Path** variable under System variables.
6. Click **Edit**. then click **New**.
7. Type Ctrl+V to paste in the address where you extracted FFmpeg earlier. Then press OK.

### Check Installation
Open cmd and type **ffmpeg** in the command prompt. If you see a lot of text in the cmd, your FFmpeg is installed successfully.

## Step 6: Prepare Workspace

Use [vid2tc](https://github.com/iamgmujtaba/vid2tc) to generate thumbnail containers and video segments from videos.

Or

1. Download sample video [BigBuckBunny](https://download.blender.org/demo/movies/BBB/bbb_sunflower_1080p_30fps_normal.mp4).
2. Rename the downloaded file to **input.mp4**
3. Run the following command in the cmd in the same directory.

```shell
ffmpeg -i input.mp4 -force_key_frames "expr:gte(t,n_forced*10)" -strict -2 -c:a aac -c:v libx264 -f segment -segment_list_type m3u8 -segment_list_size 0 -segment_time 10.0 -segment_time_delta 0.1 -segment_list out.m3u8 out%02d.ts
```

4. Once the process is completed, copy all the files excluding **input.mp4** into the default IIS physical path (i.e C:\inetpub\wwwroot\\) or the modified path.

To generate segments from multiple videos, clone the repository using the following command:

```shell
git clone https://github.com/iamgmujtaba/hls-server.git
```
Then run the following script:
```shell
python .\main.py -i .\input\ -o .\output\
```

## Step 7: Final Testing

### Check your IP Address
Open cmd and type **ipconfig**. Get IPv4 Address. It would be 192.XXX.XXX.XXX.
### Play Video on HLS Player
#### VLC
1. Install [VLC](https://www.videolan.org/vlc/download-windows.html). 
2. Open **Network Stream** by using **Ctrl+N**. 
3. Type the URL like this **http://192.XXX.XXX.XXX/bbb/out.m3u8**. **bbb** is the directory of the processed BigBuckBunny video and **out.m3u8** is the HLS text file we created using the above script. 
4. Finally, press play. If you can watch the video, it means you have configured HLS on your window machine.

#### iPhone/iPad (Safari Web Browser)
If you have iPhone, open safari and type like this **192.XXX.XXX.XXX/bbb/out.m3u8**. **bbb** is the directory of the processed BigBuckBunny video and **out.m3u8** is the HLS text file we created using the above script. Please note the iPhone should be connected to the same WiFi network.

#### Ubuntu or Jetson Devices
Suppose you want to use a native HLS web player. In that case, you can clone [hls.js](https://github.com/video-dev/hls.js/) from GitHub and follow the [installation](https://github.com/video-dev/hls.js/#getting-started-with-development) instructions.

The main code is available at [GitHub](https://github.com/iamgmujtaba/hls-server).

Raise an issue if you are facing any problems.
