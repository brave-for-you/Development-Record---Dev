---
title: Media Server
date: 2024-05-01
tags:
---
> 关于推流、拉流命令行控制及开发依赖
> // 后管地址 127.0.0.1:8000/admin/
> // 接口：http://127.0.0.1:8000/api/server // 性能线程监控
> // 接口：http://127.0.0.1:8000/api/streams // 查询推流列表



## ffmpeg(shell/cmd)
### 基础命令
```shell
    # dshow可以用来抓取摄像头、采集卡、麦克风等，vfwcap主要用来采集摄像头类设备，gdigrab则是抓取Windows窗口程序
    ffmpeg -list_devices true -f dshow -i dummy # 查询可用dshow设备

    -f vfwcap -i "0" # 启用摄像头
    -f gdigrab -i desktop 启用桌面录制 -f gdigrab -i title="" # 启用应用录制
    -f dshow -i video="" 指定video设备 -f dshow -i audio="" # 指定audio设备
    -re -stream_loop -1 # 重复
    -vcodec libx264 -preset ultrafast # video指定解码配置
    -acodec libmp3lame -ar 44100 -ac 1 # audio指定解码配置
    -c:v libx264 -preset ultrafast  # vfwcap摄像头使用video配置
```
### RTMP协议推流
```shell
    # 重复推流test.mp4至rtmp接口
    ffmpeg -re -stream_loop -1 -i test.mp4 -vcodec libx264 -preset ultrafast -f flv rtmp://localhost:1935/stream/test

    # 摄像头推流至rtmp接口（不包含acodec）
    ffmpeg -f vfwcap  -i "0" -c:v libx264 -preset ultrafast -f flv rtmp://localhost:1935/media/home

    # 桌面desktop推流至rtmp接口（不包含acodec）
    ffmpeg -f gdigrab -i desktop -vcodec libx264 -preset ultrafast -f flv rtmp://192.168.52.51:1935/media/home
```
### ffmpeg本地
```shell
    # 从视频中提取音频
    ffmpeg.exe -i aa.mp4 -vn -c:a copy output.aac
    # -vn 表示去掉视频， -c:a copy表示不改变音频编码，直接拷贝。

    # 进行指定时间截图
    ffmpeg.exe -ss 0:8:34 -i aa.mp4 -vframes 1 -q:v 2 output.jpg
    # -vframes1表示只截取一帧， -q:v2表示输出的图片质量，通常范围在1到5之间（1 为最高质量）

    # 为音频添加封面
    ffmpeg -loop 1 -i cover.jpg -i input.mp3 -c:v libx264 -c:a aac -b:a 192k -shortest output.mp4
    # 以上命令中，有两个输入文件，一个是封面图片 cover.jpg，另一个是音频文件 input.mp3。-loop1参数表示图片无限循环，  -shortest参数表示音频文件结束时，输出视频也随之结束。

    # MP4 转 M3U8
    ffmpeg -i input.mp4 -c:v libx264 -c:a aac -strict -2 -f hls -hlslistsize 2 -hls_time 15 output.m3u8
    # 该命令将 input.mp4 视频文件每15秒生成一个 ts 文件，并最后生成一个 m3u8 文件，m3u8 文件则作为 ts 的索引文件。

    # 屏幕录制并保存成文件
    ffmpeg -f gdigrab -i desktop eguid.mp4

    # 转流（rtsp转rtmp为例）
    ffmpeg -i rtsp://184.72.239.149/vod/mp4://BigBuckBunny_175k.mov -rtsp_transport tcp -vcodec h264 -acodec aac -f flv rtmp://localhost:1935/rtmp/eguid

    # 拉流
    ffmpeg -i rtmp://eguid.cc:1935/rtmp/eguid -vcodec h264 -f flv -acodec aac -ac 2 eguid.flv
```

## win命令控制
```shell
    # 命令前+start新开命令窗口执行
    tasklist # 查询所有进程
    tasklist /fi "imagename eq ffmpeg.exe" # 根据名字查询进程
    taskkill /f /im ffmpeg.exe # 根据进程名结束进程
    taskkill /pid 13044 -f # 根据pid值结束进程
```



