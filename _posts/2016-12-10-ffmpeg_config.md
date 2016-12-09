---
layout:        post
title:         "ffmpeg转码参数"
subtitle:      "找个地方记录，备忘"
date:          2016-12-10
author:        "sven"
header-img:    "img/post-bg-2015.jpg"
tags:
    - 视频
---

## ffmpeg  
查看所有参数的方法，ffmpeg --help full

## ffmpeg分辨率转码的配置 
这里的参数配置算是比较靠谱的，因为有些ffmpeg运行起来以后，在**1分40秒**就自动断开，尚未明确什么原因，就先用其他的参数来实现ffmpeg的分辨率转码，效率方面还没有详细测试，但使用GPU能有更好的速度

```
exec ffmpeg -y -i rtmp://localhost/$app/$name -threads 20 -async 1 -vsync -1  
                               -c:v libx264 -flags +global_header -tune zerolatency -preset faster -vprofile main -level 4.0 -x264opts nal-hrd=cbr:force-cfr=1:keyint=60 -b:v 1500k -minrate 1500k -maxrate 1500k -bufsize 1500k -framerate 30 -g 60 -s 1280x720 -acodec copy -r 30 -f tee -map 0 "[f=flv]rtmp://localhost/dash/$name_high|[f=flv]rtmp://localhost/hls/$name_high"  
                               -c:v libx264 -flags +global_header -tune zerolatency -preset faster -vprofile main -level 3.1 -x264opts nal-hrd=cbr:force-cfr=1:keyint=60 -b:v 800k -minrate 800k -maxrate 800k -bufsize 800k -framerate 30 -g 60 -s 854x480 -acodec copy -r 30 -f tee -map 0 "[f=flv]rtmp://localhost/dash/$name_mid|[f=flv]rtmp://localhost/hls/$name_mid"  
                               -c:v libx264 -flags +global_header -tune zerolatency -preset faster -vprofile main -level 3.1 -x264opts nal-hrd=cbr:force-cfr=1:keyint=60 -b:v 500k -minrate 500k -maxrate 500k -bufsize 500k -framerate 30 -g 60 -s 640x360 -acodec copy -r 30 -f tee -map 0 "[f=flv]rtmp://localhost/dash/$name_low|[f=flv]rtmp://localhost/hls/$name_low"
```