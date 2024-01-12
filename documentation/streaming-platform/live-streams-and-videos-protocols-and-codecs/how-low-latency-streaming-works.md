---
title: how-low-latency-streaming-works
displayName: Low Latency streaming
published: true
order: 40
toc:
   --1--What is latency, and why it exists: "what-is-streaming-latency-and-why-does-it-exist"
   --1--How Gcore provides Low Latency: "how-does-gcore-provide-low-latency"
   --1--How LL-HLS and LL-DASH work: "how-do-ll-hls-and-ll-dash-work-in-comparison-to-the-standard-approach"
   --1--Use Low Latency streaming: "use-low-latency-streaming"
   --1--Switch to legacy HLS modes: "switch-to-legacy-hls-modes"
   --2--Regular HLS: "get-regular-hls-with-cmaf-mode"
   --2--HLSv3: "get-legacy-hlsv3-mode"
pageTitle: Understanding Low Latency Streaming | Gcore 
pageDescription: A guide explains streaming latency, how Gcore reduces it with LL-HLS and LL-DASH protocols, and how to use them.
---
# How Low Latency streaming works

## What is streaming latency, and why does it exist?

Streaming latency is the timespan between the moment a frame is captured and that frame is displayed on the viewers' screens. Latency occurs because each stream is processed several times during broadcasting to be delivered worldwide:

1.  **Transcoding (and packaging).** In this step, the Streaming service retrieves your stream in any format, converts it into the format for delivery through CDN, and divides it into small fragments.
2.  **Transferring.** In this step, CDN servers pull the processed stream, cache it, and send it to the end users.
3.  **Receipt by players.** In this step, end-user players load the fragments and buffer them.

Each step affects latency, so the total timespan can increase to ±30 seconds, especially if the streaming processing isn't optimized. For some companies (such as sports or metaverse events, news releases, etc.), such latency is too large, and it's crucial to reduce it.

## How does Gcore provide low latency?

The Gcore Streaming Platform receives live streams in RTMP, RTMPS, and SRT protocols; transcodes to ABR (<a href="https://gcore.com/docs/streaming-platform/live-streams-and-videos-protocols-and-codecs/how-we-optimize-live-stream-and-video-performance-by-creating-different-bitrates" target="_blank">adaptive bitrate</a>) via CDN in LL-DASH, LL-HLS protocols.

- LL-HLS (Low Latency HTTP Live Streaming) is an adaptive protocol developed by Apple for live streaming via the Internet. This protocol is based on HTTP, which allows it to be cached on CDN servers and distributed via CDN as static content. 
- LL-DASH (Low Latency Dynamic Adaptive Streaming over HTTP) is a data streaming technology that optimizes media content delivery via the HTTP protocol.

Also, Gcore uses CMAF (Common Media Application Format) as a base for LL-HLS/DASH. CMAF allows dividing segments into chunks (video fragments) for faster delivery over HTTP networks.

LL-HLS and LL-DASH reduce latency to ±4 sec, depending on the network conditions.

<img src="https://assets.gcore.pro/docs/streaming-platform/live-streams-and-videos-protocols-and-codecs/how-low-latency-streaming-works/13544135637137.png" alt="How does Gcore provide low latency" width="80%">

Read more about how we produce Low Latency streams in Blog https://gcore.com/blog/jit-packager/ .

## How do LL-HLS and LL-DASH work in comparison to the standard approach?

The standard video delivery approach involves sending the entirely created segment to the CDN, and once the CDN receives the complete segment, it transmits it to the player. With this approach, video latency depends on segment length. Also player need to download and cache 2 more chunks to have data in buffer to play seamlessly. So player displays a frame that is 3 chunk of 6 seconds each (3*6=18 seconds) late compared to the actual time. If you decrease chunk size from 6 to 2 seconds, then final latency can not be less than 3*2=6 seconds. But in real life it will be the more than 7-18 seconds.

<img src="https://assets.gcore.pro/docs/streaming-platform/live-streams-and-videos-protocols-and-codecs/how-low-latency-streaming-works/13082039137553.png" alt="How do LL-HLS and LL-DASH work in comparison to the standard approach?" width="80%">

The Low Latency approach uses the CMAF, which helps divide and deliver live stream segments into small, non-overlapping, and independent fragments (chunks) with a length of ±0.5 seconds. And as an addition HTTP/1.1 CTE (Chunked Transfer-Encoding) extension and/or HTTP/2 Server Push method are used, which allow the server not to wait for the end of the complete loading of the segment but to send it to the CDN and the player in ready-made small fragments continuously. 

This approach helps eliminate the segment duration factor affecting video latency in standard video delivery methods.

The picture below shows the difference between delivering a) regular stream with reduced chunks length to 2 seconds (final latency is 7+ seconds), compared to b) delivering low latency:

<img src="https://assets.gcore.pro/blog/what-advanced-streaming-platforms-should-be-able-to-do-in-2021/1629454101.jpg" alt="Difference between regular and low latency delivery approach" width="80%">

## Use Low Latency streaming

We support <a href="https://gcore.com/news/low-latency-hls/" target="_blank">Low Latency streaming</a> by default. It means your live streams are automatically transcoded to LL-HLSv6+ or LL-DASH protocol when you <a href="https://gcore.com/docs/streaming-platform/live-streaming/create-a-live-stream" target="_blank">create a live stream</a>. Links for embedding the live stream to your own player contain the */cmaf/* part.


It's main and recommended way to get live streams.


Here are our demo streams:

- ```[https://demo-public.gvideo.io/cmaf/2675_19146/index.mpd](https://demo-public.gvideo.io/cmaf/2675_19146/index.mpd)``` (LL-DASH, which is supported by any device but does not work with iOS)
- ```[https://demo-public.gvideo.io/cmaf/2675_19146/master.m3u8](https://demo-public.gvideo.io/cmaf/2675_19146/master.m3u8)``` (LL HLS, which is supported by iOS (Safari browser) and other devices)

FOR EDITOR: Need to insert this HTML video player into iframe. So end-reader must see the real video player on the page. 
https://player.gvideo.co/streams/2675_19146

## Switch to legacy HLS mode

Some legacy devices or software may require MPEGTS (.ts) segments as a format for streaming, so we provide this options keeping backward compatibility with any of your existing workflows. Use this method for legacy only.

To use this mode:

- Use API to get the value of the "hls_mpegts_url" field instead of low latency "hls_cmaf_url";

- OR add special attribute "?noLL=1" in query string of the video player. I.e. https://<url>?noLL=1

Loot at our legacy HLS MPEGTS demo streams:

- ```[https://demo-public.gvideo.io/mpegts/2675_19146/index.m3u8](https://demo-public.gvideo.io/mpegts/2675_19146/index.m3u8)``` (Native manifest .m3u8)

- ```[https://player.gvideo.co/streams/2675_19146?noLL=1](https://player.gvideo.co/streams/2675_19146?noLL=1)``` (HTML web player)

