---
layout: default
categories: [Q&A]
tags: [KVS WebRTC]
excerpt_separator: <!--more-->
permalink: /q&a/mobile
name: /q&a/mobile.html
---

# Mobile App Q&A/Development Guide

## Is Cognito mandatory for KVS WebRTC App?

The best practice for AWS credential management on App side is using Cognito, but user can have their own credential management services instead.

## Why App can't connect to signaling channel?/Why App need to spend a long time to connect to signaling channel?

This normally caused by bad network. Try to use nearest AWS region/switch to another network.

## Why App can't do streaming on some specific Android phone model?

WebRTC native Android SDK requires usable hardware codec for video/audio decoding. If your Android phone model doesn't have supported hardware codec, stream will not be established. In this case, you need to modify WebRTC native SDK and add a software codec in, or disable self-review at app side when you connect to a H.264 only IP camera.

## How can I implement video/audio recording features?

The original WebRTC native SDK doesn't support recording features completely. To add video/audio recording features in, user need to modify WebRTC native SDK and recompile it. IoT Lab provided an [Android implementation with video/audio recording features for reference](https://github.com/codingspirit/amazon-kinesis-video-streams-webrtc-sdk-android/tree/record).

## How can I troubleshoot runtime error when connect to an IP camera?

1. Use [KVS WebRTC Test Page](https://awslabs.github.io/amazon-kinesis-video-streams-webrtc-sdk-js/examples/index.html) as master, app as viewer to reproduce. If issue is reproducible and not network related, rise an issue at our [Android sdk Github repository](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-android/issues/new). If issue is not reproducible, go to step 2.
2. Use KVS WebRTC Embedded C SDK master sample as master, app as viewer to reproduce issues. If issue is reproducible, go to step 3.
3. Print out the [SDP offer](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-android/blob/4d1b0ed8dac338c0a8dddfda923a0b176cc50fb8/src/main/java/com/amazonaws/kinesisvideo/demoapp/activity/WebRtcActivity.java#L182) and the [SDP answer](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-android/blob/4d1b0ed8dac338c0a8dddfda923a0b176cc50fb8/src/main/java/com/amazonaws/kinesisvideo/demoapp/activity/WebRtcActivity.java#L198) at app side and gathering the logs from KVS WebRTC Embedded C SDK master sample.
4. Rise an issue at [Embedded C SDK Github repository](https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c/issues/new/choose) with the SDP offer/answer and logs from KVS WebRTC Embedded C SDK master sample attached.
