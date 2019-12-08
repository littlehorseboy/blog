---
title: videojs-ima 實作筆記
date: 2019-12-03 17:51:04
tags:
- google
- video
---

# videojs-ima 是用來讓播放器能方便引入 google 圖片或影片的廣告的函式庫

## 首先用 npm 來下載該載的東西

```bash
npm i video.js
npm i videojs-contrib.ads
npm i videojs-ima
```

## css 引入

```html
<link rel="stylesheet" href="./node_modules/video.js/dist/video-js.css">
<link rel="stylesheet" href="./node_modules/videojs-contrib-ads/dist/videojs.ads.css">
<link rel="stylesheet" href="./node_modules/videojs-ima/dist/videojs.ima.css">
```

## js 引入

```html
<script src="https://imasdk.googleapis.com/js/sdkloader/ima3.js"></script>
<script src="./node_modules/video.js/dist/video.js"></script>
<script src="./node_modules/videojs-contrib-ads/dist/videojs.ads.js"></script>
<script src="./node_modules/videojs-ima/dist/videojs.ima.js"></script>
```

## html 全部裝起來的部分

最後寫上 `<video>` 並指定 source

script 初始化 videojs 套上 ima 廣告

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>videojs.ima</title>
  <link rel="stylesheet" href="./node_modules/video.js/dist/video-js.css">
  <link rel="stylesheet" href="./node_modules/videojs-contrib-ads/dist/videojs.ads.css">
  <link rel="stylesheet" href="./node_modules/videojs-ima/dist/videojs.ima.css">
</head>

<body>
  <video id="content_video" class="video-js vjs-default-skin" controls preload="auto" width="640" height="360">
    <source src="https://storage.googleapis.com/gvabox/media/samples/android.mp4" type="video/mp4" />
  </video>

  <script src="https://imasdk.googleapis.com/js/sdkloader/ima3.js"></script>
  <script src="./node_modules/video.js/dist/video.js"></script>
  <script src="./node_modules/videojs-contrib-ads/dist/videojs.ads.js"></script>
  <script src="./node_modules/videojs-ima/dist/videojs.ima.js"></script>

  <script>
    var player = videojs('content_video');

    var options = {
      id: 'content_video',
      adTagUrl: 'http://pubads.g.doubleclick.net/gampad/ads?sz=640x480&iu=/124319096/external/ad_rule_samples&ciu_szs=300x250&ad_rule=1&impl=s&gdfp_req=1&env=vp&output=xml_vmap1&unviewed_position_start=1&cust_params=sample_ar%3Dpremidpostpod%26deployment%3Dgmf-js&cmsid=496&vid=short_onecue&correlator=',
    };

    player.ima(options);

    player.on('ads-manager', function (response) {
      console.dir(response);
    });
  </script>
</body>

</html>
```

## demo

![](https://i.imgur.com/NPJ1f2k.gif)
