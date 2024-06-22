---
layout: post
title: "Configure DASH Server on Windows 10/11"
description: "Learn how to configure Dynamic Adaptive Streaming over HTTP (DASH) server on Windows 10/11 using Internet Information Services (IIS) and FFmpeg."
categories: [Tutorial, Streaming, Windows]
tags: [DASH, MPEG-DASH, IIS, Streaming, FFmpeg, Windows]
thumbnail: assets/img/blog/dash_icon.jpg
---

Dynamic Adaptive Streaming over HTTP (DASH), or MPEG-DASH, is an adaptive bitrate streaming technique. It enables high-quality streaming of media content over the Internet delivered from conventional HTTP web servers. Like Apple's HTTP Live Streaming (HLS) solution, MPEG-DASH breaks the content into small segments served over HTTP. Each segment contains a short interval of playback time of content that is potentially many hours, such as a movie or the live broadcast of a sports event. The content is made available at a variety of different bit rates, i.e., alternative segments encoded at various bit rates covering aligned short intervals of playback time [REF](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP). Now, we will move forward towards the steps followed in the process:

## Step 1: Creation of the Web Server

Microsoft provides Internet Information Services (IIS) that can be easily used as a web server. To configure, follow the straightforward steps below:

1. Select and open Windows features (WindowsKey+S (Search box), then type **Turn Windows features on or off**).
2. Find Internet Information Services and tick the box (wait for the process to configure and then **Close**).
3. Test it by opening Chrome and navigating to your local internet address, **127.0.0.1**.
4. The default page should appear (if not, reboot your system).

{% include figure.liquid loading="eager" path="assets/img/blog/iis_home.jpg" class="img-fluid rounded z-depth-1" %}

## Step 2: Change Default Physical Path

You can skip this step if you don't want to change the default path. All the files can be located at the default path **C:\inetpub\wwwroot\\**.

1. Open IIS Manager (WindowsKey+S, then type IIS).
2. The **Default Site** stores its files in a particular directory. To expose this information, right-click on it, choose **Manage Website**, then **Advanced Settings**.
3. This will open a pop-up window with all of the default site information, such as files or document roots, as they are typically known, enabled protocols, and even bindings.
4. If you click on **Physical Path**, a button appears on its extreme right where you can choose a different document root. This is the location where your web server will look for files to serve to clients. It's important to set this correctly to ensure your server functions as expected.

{% include figure.liquid loading="eager" path="assets/img/blog/iis_path.jpg" class="img-fluid rounded z-depth-1" %}

## Step 3: Enabling Cross-Origin Resource Sharing (CORS)

To test streams, you need to allow other websites to access files on your web server. This practical task is done by enabling cross-origin resource sharing (CORS). Follow these practical steps:

1. Open the webserver (WindowsKey+S then type **IIS**).
2. Select **Default Web Site** and right-click or double-click **HTTP Response Headers**.
3. Select **Open Feature** from the Action pane. Then, click **Add** and type in **Access-Control-Allow-Origin** for Name and type "*" for Value.
4. Click OK to add the header. To add another value, type in **Access-Control-Allow-Headers** for Name and type **Range** for Value.

{% include figure.liquid loading="eager" path="assets/img/blog/hrs_page.jpg" class="img-fluid rounded z-depth-1" %}

## Step 4: Adding the DASH MIME Type

DASH requires statements to learn how to analyze video and audio files. DASH manifest files end in **.mpd**. Windows IIS does not know about this extension by default. Follow these steps to add the MIME type:

1. Under connections, click your server and double-click **MIME Types**.
2. Type **.mpd** for File name extension and type **application/dash+xml** for MIME-type. Press OK.
3. Type **.m4s** for File name extension and type **video/mp4** for MIME-type. Press OK.

{% include figure.liquid loading="eager" path="assets/img/blog/mime_dash.jpg" class="img-fluid rounded z-depth-1" %}

## Step 5: FFmpeg Installation

1. Download FFmpeg from the readily available source [here](https://www.ffmpeg.org/download.html#build-windows).
2. Extract the downloaded FFmpeg zip file to **C:\ffmpeg**.
3. Navigate to the **bin** folder under **C:\ffmpeg** and copy the address using Ctrl+C.
4. Open up the System information window and click on **Advanced System Settings**. Then click on **Environment Variables**.
5. Select the **Path** variable under System variables.
6. Click **Edit** and then click **New**.
7. Type Ctrl+V to paste in the address where you extracted FFmpeg earlier. Then press OK.

### Check Installation

Open cmd and type **ffmpeg** in the command prompt. If you see much text in the cmd, your FFmpeg is installed successfully.

## Step 6: Prepare Workspace

1. Download the sample video [BigBuckBunny](https://download.blender.org/demo/movies/BBB/bbb_sunflower_1080p_30fps_normal.mp4).
2. Rename the downloaded file to **input.mp4**.
3. Run the following command in the cmd in the same directory:

```shell
ffmpeg -re -i input.mp4 -map 0 -map 0 -c:a copy -c:v libx264 -b:v:0 800k -b:v:1 300k -s:v:1 320x170 -profile:v:1 baseline -profile:v:0 main -bf 1 -keyint_min 120 -g 120 -sc_threshold 0 -b_strategy 0 -ar:a:1 22050 -use_timeline 1 -use_template 1 -window_size 5 -adaptation_sets "id=0,streams=v id=1,streams=a" -f dash out.mpd
```

4. Once the process is completed, copy all the files excluding **input.mp4** into the default IIS physical path (i.e C:\inetpub\wwwroot\\) or the modified path.

To generate segments from multiple videos, clone the repository using the following command:

```shell
git clone https://github.com/iamgmujtaba/dash-server.git
```
Then run the following script:
```shell
python .\main.py -i .\input\ -o .\output\
```

## Step 7: Final Testing
### Check your IP Address
Open cmd and type **ipconfig**. Get IPv4 Address. It would be 192.XXX.XXX.XXX.
### Play Video on DASH Player

#### VLC
1. Install [VLC](https://www.videolan.org/vlc/download-windows.html). 
2. Open **Network Stream** by using **Ctrl+N**. 
3. Type the URL like this **http://192.XXX.XXX.XXX/bbb_dash/out.mpd**. **bbb_dash** is the directory of the processed BigBuckBunny video and **out.mpd** is the DASH text file we created using the above script. 
4. Finally, press play. 

If you can watch the video, it means you have successfully configured DASH on your Windows machine.

#### Ubuntu or Jetson Devices
Suppose you want to utilize a native DASH web player. In that case, you can clone [dash.js](https://github.com/Dash-Industry-Forum/dash.js) from GitHub and then follow the [installation](https://github.com/Dash-Industry-Forum/dash.js#getting-started) instructions.


The main code is available at [GitHub](https://github.com/iamgmujtaba/dash-server).

Raise an issue if you are facing any problems.

