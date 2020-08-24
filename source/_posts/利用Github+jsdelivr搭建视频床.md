---
title: 利用Github+jsdelivr搭建视频床
author: bilibili@[开心就好-_-](https://space.bilibili.com/327596612)
categories:
- 黑科技
---
了解Github能做图床的人，我相信你应该对github和jsdelivr应该有一定的了解，那么，废话不多说，开始！  
<!-- more -->
## 如何实现
jsdelivr能加载GitHub中单个20M以下的静态资源，所以能实现**图床**的功能。**视频床**也就是同样的道理，但通常视频的大小都超出了**20M**的大小限制。这时我们就可以将**视频切片**，用**m3u8**索引，整合播放。   
## 利用ffmpeg转码并切片
* 下载ffmpeg
<http://ffmpeg.org/download.html>，解压压缩包，找到ffmpeg.exe，解压出来，放到你想放的位置。
* 视频转码
1.对视频进行转码(转为 mp4)，将视频文件转为视频编码 h.264，音频编码 aac 格式的 mp4 文件，mp4 视频文件不是 h.264 编码到后面切片的时候可能会遇到很多莫名其妙的问题。（不会命令行的话用格式工厂吧。。。记得码率不要超过5200kbps）
2.在放置ffmpeg.exe和已转码的视频的文件夹中打开powershell（在文件夹中摁住shift，单击右键，找到“在此处打开Power Shell窗口”选项，单击左键打开）
3.输入命令`ffmpeg`测试环境，如果输出版本号即可使用。（如果提示脚本不可运行，请输入`.\ffmpeg`。）
* 开始切片
* MP4转ts
假设你的视频文件名为output.mp4
```ffmpeg -y -i output.mp4 -vcodec copy -acodec copy -vbsf h264_mp4toannexb output.ts```
* ts切片
用ffmpeg把output.ts文件切片并生成playlist.m3u8文件，5秒一个切片：
```ffmpeg -i output.ts -c copy -map 0 -f segment -segment_list playlist.m3u8 -segment_time 5 output%03d.ts```
( playlist.m3u8可以自行改成xxx.m3u8 ，如更改则最终组成链接时需要对应的更改文件名)
* 上传所有文件至GitHub
**记得放在同一个文件目录中！**
## jsDelivr+Dplayer实现切片视频播放
最终完成和我们的视频链接格式为：
https://cdn.jsdelive.net/gh/GitHub用户名/库名/文件目录/playlist.m3u8
Demo：
<div id="dplayer"></div>
<script src="https://cdn.jsdelivr.net/npm/hls.js@0.14.9/dist/hls.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/dplayer@1.26.0/dist/DPlayer.min.js"></script>
<script>
    const dp = new DPlayer({
    container: document.getElementById('dplayer'),
    video: {
        url: 'https://cdn.jsdelivr.net/gh/bilikxjh/m3u8/1/playlist.m3u8',
        type: 'hls',
    },
    pluginOptions: {
        hls: {
            // hls config
        },
    },
});
console.log(dp.plugins.hls);
</script>

* Demo源代码
```html
<div id="dplayer"></div>
<script src="https://cdn.jsdelivr.net/npm/hls.js@0.14.9/dist/hls.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/dplayer@1.26.0/dist/DPlayer.min.js"></script>
<script>
    const dp = new DPlayer({
    container: document.getElementById('dplayer'),
    video: {
        url: 'https://cdn.jsdelivr.net/gh/bilikxjh/m3u8/1/playlist.m3u8',
        type: 'hls',
    },
    pluginOptions: {
        hls: {
            // hls config
        },
    },
});
console.log(dp.plugins.hls);
</script>
```