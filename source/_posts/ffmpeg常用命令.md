---
title: ffmpeg common commands
date: 2023-07-23 22:29:00
categories:
  - Miscellaneous
tags: 
  - Video Processing
  - Video Compression
  - ffmpeg
  - Video Splitting
  - Video Merge
  - Graphics card support
description: This article summarizes and demonstrates the common functions and commands of ffmpeg, a powerful media processing tool. The article lists the specific commands for ffmpeg to perform these operations, such as video capture camera commands, audio and video conversion commands, commands to take screenshots of the video, commands to add watermarks to the video, etc. All the ffmpeg commands in the article are summarized and shown. All the ffmpeg commands in the article are exemplified and explained, which is easy for readers to understand and memorize. Finally, the article gives some advanced usage of ffmpeg, such as using filters to process videos, processing GIF motion pictures, etc.
cover: https://s2.loli.net/2023/07/24/uxe2mkvdq64o5EH.webp
---

### Audio Video Merge

```shell
ffmpeg -i video filename -i audio filename -codec copy output MP4 filename
```

### mp4 video stitching

```shell
ffmpeg -i 1.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 1.ts ffmpeg -i 2.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb 2.ts ffmpeg -i "concat:1.ts|2.ts" -acodec copy -vcodec copy -absf aac_adtstoasc output.mp4
```



### Enable Graphics Support

```bash
ffmpge -hwaccel cuvid
##### GPU:
  .\ffmpeg-win64-v4.2.2.exe -c:v h264_cuvid -i "E:\\Downloads\\0112\\The Tai-chi Maste qq.mp4"
      -i "E:\\GitSpace\\DeWaterMark\\watermark\\watermark1.png"
      -i "E:\\GitSpace\\DeWaterMark\\watermark\\watermark2.png"
      -filter_complex "overlay=enable='lte(t,20)':x=(W-w)/2:y=H-h:,overlay=enable='gt(mod(t,31),20)':x=W-w:y=0"
      -c:v h264_nvenc -b:v 1500k -bufsize 1500k "D:\\Downloads\\output\\output.mp4" -y

##### CPU:
 .\ffmpeg-win64-v4.2.2.exe  -i "E:\\Downloads\\0112\\The Tai-chi Maste aa.mp4"
      -i "E:\\GitSpace\\DeWaterMark\\watermark\\watermark1.png"
      -i "E:\\GitSpace\\DeWaterMark\\watermark\\watermark2.png"
      -filter_complex "overlay=enable='lte(t,20)':x=(W-w)/2:y=H-h:,overlay=enable='gt(mod(t,31),20)':x=W-w:y=0"
      -b:v 1500k -bufsize 1500k "D:\\Downloads\\output\\output.mp4" -y

```

### Add Watermark

* Movie filters in detail

  ```bash
    ffmpeg -i inputfile -vf  "movie=masklogo,scale= 60: 30[watermask]; [in] [watermask] overlay=30:10 [out]" outfile
  Parameter description:
  marklogo: the image of the watermark to be added;
  scale: the size of the watermark, the length of the watermark * the height of the watermark;
  overlay: location of the watermark, distance from the left side of the screen * distance from the top side of the screen; mainW main video width, mainH main video height, overlayW watermark width, overlayH watermark height
  　　Upper left corner overlay parameter is overlay=0:0
  　　Upper right corner is overlay= main_w-overlay_w:0
  　　Bottom right corner is overlay= main_w-overlay_w:main_h-overlay_h
  　　Bottom left is overlay=0: main_h-overlay_h
      The 0 on the face can be changed to 5, or 10 pixels to allow for more white space.
  ```

* Timed Text Watermark

  ```bash
  ffmpeg -re -i test.mp4 -vf "drawtext=fontsize=60:fontfile=lazy.ttf:text='{localtime\:%Y\-%m-%d %H-%M-%S}':fontcolor=green:box=1:boxcolor=yellow:enable=lt(mod(t\, 3)\, 1)" out.mp4
  ```

* Add an image watermark

  ```bash
  ffmpeg -i test.mp4 -vf "movie=logo.jpg[wm];[in][wm]overlay=30:10[out]" image_out.mp4
  ```

* Add four image watermarks

  ```bash
  ffmpeg -i in.mp4 -i logo.png -i logo.png -filter_complex "overlay=5:5, overlay=x=W-w:y=5" in_out_mul_watermark.mp4
  ```
* Add two watermarks and specify the location and start time of disappearance.
  ```bash
  # Add two watermarks, the first one is displayed in the lower center, showing time 0-20 seconds, and the second one is displayed in the upper right, showing time 20-31 seconds.
  ffmpeg -i intput.mp4 -i watermark1.png -i watermark2.png -filter_complex "overlay=enable='lte(t,20)':x=(W-w)/2:y=H-h:,overlay=enable='gt( mod(t,31),20)':x=W-w:y=0" output.mp4 -y
  # Same as above to add graphics card support
  ffmpeg -hwaccel cuvid -i intput.mp4 -i watermark1.png -i watermark2.png -filter_complex "overlay=enable='lte(t,20)':x=(W-w)/2:y=H-h:,overlay =enable='gt(mod(t,31),20)':x=W-w:y=0" -vcodec h264_nvenc output.mp4 -y
  ```
* Add two watermarks that alternate for 10 seconds
  ```bash
  ffmpeg -i /root/test.mp4 -i /root/videoProcessing/youtube/test.png -i /root/test.png 
    -filter_complex “overlay=x=if(lt(mod(t,20),10),10,NAN ):y=10,overlay=x=if(gt(mod(t,20),10),main_w-273,NAN ) :y=main_h-113,subtitles=/root/test.srt :force_style=‘Fontsize=14’” /root/test3.mp4
    
    Add two watermarks, overlay=x=if(lt(mod(t,20),10),10,NAN ):y=10,overlay=x=if(gt(mod(t,20),10),main_w-273,NAN ) These two use a function that represents that the watermarks are alternating.
    mod(t,20) represents that the current time is modeled against 20;
    lt(a,b) represents a<b, then true
    if(true,a,b) means if true, then return a, otherwise return b
    
    ps:If you need to do command line splicing in your program, you must remember to escape, otherwise you will report an error.
  ```
### Video Splitting

```bash
ffmpeg -v quiet -y -i S4.mp4 -vcodec copy -acodec copy -ss 00:00:00 -t 00:07:12 -sn S01E01.mp4 -ss denotes the start time of video segmentation, -t denotes the segmentation duration
```

### rm 转mp4

```bash
ffmpeg -i gdg.rmvb  -vcodec libx264 -c:v h264 -c:a aac gdg.mp4 
silent -an : no audio encoding -vcodec : set the encoding of the video, I'm using x264 here -b : this is the bitrate -f : force to use the format -y : auto lose y confirmation
```

### flv to MP4

```bash
ffmpeg -i aa！.flv  -vcodec libx264 -c:v h264 -c:a aac aa.mp4
```

##### Batch Processing Video

```bash
for %%G in (*.rmvb) do ffmpeg -i "%%~G" -c:v h264 -c:a aac "%%~nG.mp4" for %%G in (*.rm) do ffmpeg -i "%%~G" -c:v h264 -c:a aac "%%~nG.mp4"
```

### Other formats to MP3 scripts

```
@echo off & title
cd /d %~dp0
for %%a in (*.m4a) do (
 echo Conversion in progress：%%a
 ffmpeg -i "%%~sa" -y -acodec libmp3lame -aq 0 "output\%%~na.mp3"
)
pause
```

### MP4 Batch Convert Files

```shell
@echo off

::Set the video or audio format to be processed below, some of the major video formats are listed here
set Ext=*.avi,*.mp4,*.wmv,*.flv,*.mkv,*.rmvb,*.rm,*.3gp,*.ts

md output

echo Start video conversion

::Set the output format at the bottom, here the output is mp4, you can change it by yourself
for %%a in (%Ext%) do (
	echo Conversion in progress：%%a
	ffmpeg -loglevel quiet -i "%%a" -f mp4 "output\%%~na.mp4" -y
)

echo Conversion complete

pause
```

### video compression

1. ###### View Video Parameters

   * ![](https://s2.loli.net/2023/07/23/jiJu5TCVg28LvlH.png)

2. ```python
   ffmpeg -i 1274.mp4 -b 600k 12.mp4
   -b    Data bit rate, the size of data traffic transmitted per second (kb/s), the bit rate set in this command is 600k, which is one-eighth of the original video.
   ```

‍

