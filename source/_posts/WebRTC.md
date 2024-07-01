---
title: WebRTC
date: 2024-05-01
tags:
---
> WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。WebRTC 包含的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。

## 媒体设备
MediaStream是用于获取音频和视频的对象。通过MediaStream可以访问摄像头、麦克风等设备。
```js
const constraints = {
    video: true,
    audio: true,
}
navigator.mediaDevices.getUserMedia(constraints).then((stream) => {
    // stream是获取到的音频或视频流
    const localStream = stream
}).catch((err) => {
    // 处理错误
})
```
## 核心对象 RTCPeerConnection
### 注册RTC
RTCPeerConnection 作为创建点对点连接的 API,是我们实现音视频实时通信的关键。
```js
const peerConnection = new RTCPeerConnection()
```
### 媒体协商方法
- createOffer
```js
peerConnection.createOffer({
    offerToReceiveAudio: 1,
    offerToReceiveVideo: 1,
}).then(offer => {
    // 获取offer
})
```
- createAnswer
```js
peerConnection.createAnswer().then(answer => {
    // 获取answer
})
```
- setLocalDescription
```js
// 设置远程offer
peerConnection.setLocalDescription(offer)
// 设置远程answer
peerConnection.setLocalDescription(answer)
```
- setRemoteDescription
```js
// 设置本地offer
peerConnection.setRemoteDescription(offer)
// 设置本地answer
peerConnection.setRemoteDescription(answer)
```
- addIceCandidate
```js
(candidate) => {
    // 设置candidate
    peerConnection.addIceCandidate(candidate)
}
```
- addStream
```js
// 设置视频流
peerConnection.addStream(localStream)
```
### 重要事件
- onicecandidate
```js
peerConnection.onicecandidate = (event) => {
    // event.candidate
}
```
- addstream(官方已不推荐事件，建议使用addTrack) / addTrack
```js
// 收到视频流
peerConnection.onaddstream = (event) => {
    // event.stream
}
```

