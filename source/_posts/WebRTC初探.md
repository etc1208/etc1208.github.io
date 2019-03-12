---
title: WebRTC初探
date: 2017-11-09 12:42:00
tags: [webrtc]
categories: 转载
---

# WebRTC

>WebRTC (Web Real-Time Communications) 是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。跨平台。
The WebRTC initiative is a project supported by Google, Mozilla and Opera, amongst others. This page is maintained by the Google Chrome team.

参见知乎上一个回答：https://www.zhihu.com/question/25497090/answer/43395462
> 自然这套API是带传输功能的。所以获取图像信源之后不应该用websocket发送图像数据，而是直接用WebRTC的通信相关API发送图像和声音（这套API是同时支持图像和声音的）数据。所以，正确的方法是什么呢？1、你得有一个实现了WebRTC相关协议的客户端。比如Chrome浏览器。2、架设一个类似MCU系统的服务器。（不知道MCU是什么？看这：MCU（视频会议系统中心控制设备））第一步，用你的客户端，比如Chrome浏览器，通过WebRTC相关的媒体API获取图像及声音信源，再用WebRTC中的通信API将图像和声音数据发送到MCU服务器。第二步，MCU服务器根据你的需求对图像和声音数据进行必要的处理，比如压缩、混音等。第三步，需要看直播的用户，通过他们的Chrome浏览器，链接上你的MCU服务器，并收取服务器转发来的图像和声音流。


### peer to peer

>“Peer”在英语里有“对等者”和“伙伴”的意义。因此，从字面上，P2P可以理解为对等互联网。国内的媒体一般将P2P翻译成“点对点”或者“端对端”，学术界则统一称为对等计算。P2P可以定义为：网络的参与者共享他们所拥有的一部分硬件资源（处理能力、存储能力、网络连接能力、打印机等），这些共享资源通过网络提供服务和内容，能被其它对等节点(Peer)直接访问而无需经过中间实体。在此网络中的参与者既是资源（服务和内容）提供者（Server），又是资源获取者（Client）。
> ----  [<p2p 综述>](http://www.intsci.ac.cn/users/luojw/P2P/ch01.html)


## protocols 协议

![](/images/webrtc/rtc.png)

### ICE (Interactive Connectivity Establishment) 
**互动式连接建立**是由IETF的MMUSIC工作组开发出来的一种framework，可整合各种NAT穿透技术，如STUN、TURN（Traversal Using Relay NAT，中继NAT实现的穿透）、RSIP（Realm Specific IP，特定域IP）等。该framework可以让SIP的客户端利用各种NAT穿透方式打穿远程的防火墙。

### NAT (Network Address Translation)
在计算机网络中，网络地址转换（Network Address Translation，缩写为NAT），也叫做网络掩蔽或者IP掩蔽（IP masquerading），是一种在IP数据包通过路由器或防火墙时重写来源IP地址或目的IP地址的技术。这种技术普遍使用在有多台主机但只通过一个公有IP地址访问因特网的私有网络中。

### STUN（Session Traversal Utilities for NAT）

NAT会话穿越应用程序是一种网络协议，它允许位于NAT（或多重NAT）后的客户端找出自己的公网地址，查出自己位于哪种类型的NAT之后以及NAT为某一个本地端口所绑定的Internet端端口。这些信息被用来在两个同时处于NAT路由器之后的主机之间创建UDP通信。该协议由RFC 5389定义。


### TURN（Traversal Using Relay NAT）

TURN是一个client-server协议。TURN的NAT穿透方法与STUN类似，都是通过取得应用层中的公有地址达到NAT穿透。但实现TURN client的终端必须在通讯开始前与TURN server进行交互，并要求TURN server产生"relay port"，也就是relayed-transport-address。这时TURN server会建立peer，即远端端点（remote endpoints），开始进行中继（relay）的动作，TURN client利用relay port将资料传送至peer，再由peer转传到另一方的TURN client。

### SDP (Session Description Protocol)
会话描述协议（Session Description Protocol或简写SDP）描述的是流媒体的初始化参数。此协议由IETF发表为 RFC 2327。

## API

由于兼容性问题，请现加载 adapter https://github.com/webrtc/adapter/

### getUserMedia() 
https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia

```
navigator.getUserMedia(constraints, successCallback, errorCallback);
```
* constranits 一个包含了video 和 audio两个成员的MediaStreamConstraints 对象，用于说明请求的媒体类型。
* 回调拿到的是一个 stream 对象，参见[MediaStream Interface](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)

### MediaRecorder()
### RTCPeerConnection
#### method:
+ RTCPeerConnection(): RTCPeerConnection的初始化函数
+ createOffer(): 生成一个offer，它是一个带有特定的配置信息寻找远端匹配机器（peer）的请求

```
pc1.createOffer(
	offerOptions
).then(
	onCreateOfferSuccess,
	onCreateSessionDescriptionError
)
```
+ createAnswer(): 
在协调一条连接中的两端offer/answers时，根据从远端发来的offer生成一个answer。
+ setLocalDescription(): 改变与连接相关的本地描述。
+ setRemoteDescription(): 改变与连接相关的远端描述。
+ createDataChannel(): 在一条连接上建立一个新的RTCDataChannel（用于数据发送）。这个方法把一个数据对象作为参数，数据对象中包含必要的配置信息。
	+ dataConstraint: https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/createDataChannel  传输基于 SCTP 

 


WebRTC的通讯终端需要获取和交换本地及远程的视频音频信息，例如分辨率等；这一类 metadata 数据在onCreateAnswerSuccess等方法接收到的参数中以 SDP 的格式传送。

#### event: 
+ onicecandidate: 当一个 RTCICECandidate 对象被添加时，这个事件被触发。
+ oniceconnectionstatechange:  当iceConnectionState 改变时，这个事件被触发。
+ onaddstream: 当MediaStream 被远端机器添加到这条连接时，该事件会被触发。 



## how to establish a p2p connection

>1. 每个端创建一个RTCPeerConnection, 添加从getUserMedia()获取到的 stream
>2. 获取网络信息，找到潜在的连接点，也就是ICE candidates.
>3. 获取和分享本地及远程描述, metadata about local media in SDP format.

demo 中没有 server, 实际参考 [WebRTC in the real world: STUN, TURN and signaling](https://www.html5rocks.com/en/tutorials/webrtc/infrastructure/)
 

## Reference

+ https://webrtc.org/ 
+ https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API 

+ Peer to Peer 综述 http://www.intsci.ac.cn/users/luojw/P2P/index.html