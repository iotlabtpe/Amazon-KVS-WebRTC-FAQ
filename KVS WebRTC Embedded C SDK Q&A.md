---
layout: default
categories: [Q&A]
tags: [KVS WebRTC]
excerpt_separator: <!--more-->
permalink: /q&a/embedded
name: /q&a/embedded.html
---

# KVS WebRTC Embedded C SDK Q&A/Development Guide

## Supported video/audio format?

Audio/Video Support:
- VP8
- H264
- Opus
- G.711 PCM (A-law)
- G.711 PCM (µ-law)

## Is there any limitation for KVS WebRTC?

Check our [official docs](https://docs.aws.amazon.com/kinesisvideostreams-webrtc-dg/latest/devguide/kvswebrtc-limits.html) for more details.

## How can I cross compile it to my platforms?

Check this [Workshop: Cross Compile For Other Platforms](https://iotlabtpe.github.io/Amazon-KVS-WebRTC-WorkShop/lab/la-5.html) provided by IoT Lab.

## How to optimize WebRTC C SDK memory(RAM/Flash)?

1. Using mbedTLS instead of OpenSSL. This replacement can reduce around 1M RAM usage with one viewer during our test.
2. Reducing Rolling buffer size. SDK itself will maintain a 3 seconds rolling buffer by default(`DEFAULT_ROLLING_BUFFER_DURATION_IN_SECONDS * HIGHEST_EXPECTED_BIT_RATE / 8 / DEFAULT_MTU_SIZE`). Reduce rolling buffer according to your user cases at [Rtp.h](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/blob/8b8b2bdf064f6cb2b6495339d31efc3518b12eb9/src/source/PeerConnection/Rtp.h#L13), i.e.:
   ```c
    #define DEFAULT_MTU_SIZE                           1200
    #define DEFAULT_ROLLING_BUFFER_DURATION_IN_SECONDS 1
    #define HIGHEST_EXPECTED_BIT_RATE                  (5 * 1024 * 1024)
    #define DEFAULT_SEQ_NUM_BUFFER_SIZE                1000
    #define DEFAULT_VALID_INDEX_BUFFER_SIZE            1000
    #define DEFAULT_PEER_FRAME_BUFFER_SIZE             (5 * 1024)
    #define SRTP_AUTH_TAG_OVERHEAD                     10
   ```
3. If your device don't need data channel, use CMake flag `ENABLE_DATA_CHANNEL=OFF` to disable it.
4. Build with gcc optimization flag and remove debug symbols.

## How to use x509 certificates to connect to KVS WebRTC?

Check this [Workshop: Connect Amazon KVS WebRTC with AWS IoT](https://iotlabtpe.github.io/Amazon-KVS-WebRTC-WorkShop/lab/lab-4.html) provided by IoT Lab.

## How can I troubleshoot runtime error on an IP camera?

1. Set log level to `LOG_LEVEL_VERBOSE`.
2. Use [KVS WebRTC Test Page](https://awslabs.github.io/amazon-kinesis-video-streams-webrtc-sdk-js/examples/index.html) as viewer, KVS WebRTC Embedded C SDK master sample as master to reproduce.
3. Get error code in log.
4. Locate your error code in [WebRTC client header](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/blob/master/src/include/com/amazonaws/kinesis/video/webrtcclient/Include.h) or [KVS common header](https://github.com/awslabs/amazon-kinesis-video-streams-producer-c/blob/master/src/include/com/amazonaws/kinesis/video/common/Include.h).
5. If it's not network/credential related issue, rise an issue at our [github repository](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/issues/new/choose) with your log attached.

## How to solve Error: "Signature not yet current: xxxxx is still later than xxxxxxxx + 5 min."?

This could happen on devices set with incorrect system time. You need to synchronize your system time with UTC via `date -s`.

## Does KVS WebRTC SDK support dynamic video bitrate/resolution?

KVS WebRTC SDK supports dynamic bitrate/resolution on the fly, since H.264 SPS contains video bitrate/resolution information. If both the encoder and decoder support dynamic video bitrate/resolution, change video bitrate/resolution will work on the fly.

## How can I troubleshoot latency issues?

In normal case KVS WebRTC can provide real-time video/audio communication with low latency, if you observed abnormal latency issues:

1. Make sure the video/audio frames provided to KVS WebRTC have correct timestamp.
2. Try to reduce `DEFAULT_ROLLING_BUFFER_DURATION_IN_SECONDS` at [Rtp.h](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/blob/8b8b2bdf064f6cb2b6495339d31efc3518b12eb9/src/source/PeerConnection/Rtp.h)
3. For some devices with limited cpu resources, log module might impact KVS WebRTC SDK performance. Try to set log level to `LOG_LEVEL_ERROR` or `LOG_LEVEL_SILENT`.
