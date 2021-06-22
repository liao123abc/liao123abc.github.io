---
layout: post
title: "computer system"
subtitle: "note"
date: 2021-06-02
author: "thomasliao"
header-img: "img/post-bg-2015.jpg"
published: false
tags: []
---

## 视频播放器原理1

- 视频播放器播放一个互联网上的视频文件
    - 解协议，解封装，解码视音频，视音频同步
- 播放本地文件则不需要解协议
    - 解封装，解码视音频，视音频同步

![process](https://img-blog.csdn.net/20140201120523046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVpeGlhb2h1YTEwMjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 视频播放器原理2

### 解协议

- 流媒体协议数据 -> 标准的相应的封装格式数据
    - e.g.  采用RTMP协议传输的数据，经过解协议操作后，输出FLV格式的数据
- 各种流媒体协议，例如HTTP，RTMP，或是MMS等等


### 解封装

- 封装格式的数据 -> 音频流压缩编码数据 + 视频流压缩编码数据
- 封装格式:
    - MP4，MKV，RMVB，TS，FLV，AVI等等
    - 作用: 将已经压缩编码的视频数据和音频数据按照一定的格式放到一起
        - e.g. FLV格式的数据 -> H.264编码的视频码流 + AAC编码的音频码流


### 解码

- 视频/音频压缩编码数据 -> 非压缩的视频/音频原始数据
- 编码标准
    - 音频: AAC，MP3，AC-3等等
    - 视频: H.264，MPEG2，VC-1等等
- 解码是整个系统中最重要也是最复杂的一个环节
    - 压缩编码的视频数据->非压缩的颜色数据，例如YUV420P，RGB等等
    - 压缩编码的音频数据->非压缩的音频抽样数据，例如PCM数据

### 音视频同步
- 根据解封装模块处理过程中获取到的参数信息，同步解码出来的视频和音频数据
- 将视频音频数据送至系统的显卡和声卡播放出来


## RTMP

## h.264

## h.265


