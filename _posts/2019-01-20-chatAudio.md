---
layout: post
title: "web聊天室即时语音功能"
subtitle: "发送图片、语音等"
author: "Jorce"
header-style: text
tags:
    - js
    - 聊天室
---

Update: 同上一篇还是在做聊天室,本篇主要讲述语音部分

---

在线传输主要使用了 webRTC,这块以后看多了会再细讲。
不同于 socket 的是:客户端端对客户端端通过服务端进行验证之后,端对端直接进行 stream 传输

# 1.创建 webRTC 连接

先上代码,等有空了再细谈 

```js
var isCaller = true;

// 与信令服务器的WebSocket连接
var socket = new WebSocket("ws://127.0.0.1:3000");

// stun和turn服务器
var iceServer = {
    iceServers: [
        {
            url: "stun:stun.l.google.com:19302"
        },
        {
            url: "turn:numb.viagenie.ca",
            username: "webrtc@live.com",
            credential: "muazkh"
        }
    ]
};

// 创建PeerConnection实例 (参数为null则没有iceserver，即使没有stunserver和turnserver，仍可在局域网下通讯)
var pc = new webkitRTCPeerConnection(iceServer);

// 发送ICE候选到其他客户端
pc.onicecandidate = function(event) {
    if (event.candidate !== null) {
        socket.send(
            JSON.stringify({
                event: "_ice_candidate",
                data: {
                    candidate: event.candidate
                }
            })
        );
    }
};

// 如果检测到媒体流连接到本地，将其绑定到一个video标签上输出
pc.onaddstream = function(event) {
    document.getElementById("remoteVideo").src = URL.createObjectURL(
        event.stream
    );
};

// 发送offer和answer的函数，发送本地session描述
var sendOfferFn = function(desc) {
        pc.setLocalDescription(desc);
        socket.send(
            JSON.stringify({
                event: "_offer",
                data: {
                    sdp: desc
                }
            })
        );
    },
    sendAnswerFn = function(desc) {
        pc.setLocalDescription(desc);
        socket.send(
            JSON.stringify({
                event: "_answer",
                data: {
                    sdp: desc
                }
            })
        );
    };

// 获取本地音频和视频流
navigator.webkitGetUserMedia(
    {
        audio: true,
        video: true
    },
    function(stream) {
        //绑定本地媒体流到video标签用于输出
        document.getElementById("localVideo").src = URL.createObjectURL(stream);
        //向PeerConnection中加入需要发送的流
        pc.addStream(stream);
        //如果是发起方则发送一个offer信令
        if (isCaller) {
            pc.createOffer(sendOfferFn, function(error) {
                console.log("Failure callback: " + error);
            });
        }
    },
    function(error) {
        //处理媒体流创建失败错误
        console.log("getUserMedia error: " + error);
    }
);

//处理到来的信令
socket.onmessage = function(event) {
    var json = JSON.parse(event.data);
    console.log("onmessage: ", json);
    //如果是一个ICE的候选，则将其加入到PeerConnection中，否则设定对方的session描述为传递过来的描述
    if (json.event === "_ice_candidate") {
        pc.addIceCandidate(new RTCIceCandidate(json.data.candidate));
    } else {
        pc.setRemoteDescription(new RTCSessionDescription(json.data.sdp));
        // 如果是一个offer，那么需要回复一个answer
        if (json.event === "_offer") {
            pc.createAnswer(sendAnswerFn, function(error) {
                console.log("Failure callback: " + error);
            });
        }
    }
};
```
