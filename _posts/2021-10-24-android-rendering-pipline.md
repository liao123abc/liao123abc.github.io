---
layout: post
title: "android rendering pipline"
subtitle: "note"
date: 2021-10-24
author: "thomasliao"
published: false
header-img: "img/post-bg-2015.jpg"
tags: []
---

### android display pipeline

https://lwn.net/Articles/809545/

- software and hardware cooperating
- in the advancement of the application's execution -- 推进


### graphic overview
https://source.android.com/devices/graphics

draw images to the screen in three ways:
- canvas
- opengl es
- vulkan

everything is rendered onto a surface

producer:surface
consumer:surfaceflinger

bufferqueue in three mode:
- synchronous-like mode
- non-blocking mode
- discard mode