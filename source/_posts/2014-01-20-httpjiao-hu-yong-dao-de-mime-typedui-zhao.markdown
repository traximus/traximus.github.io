---
layout: post
title: "HTTP交互用到的MIME-Type对照"
date: 2014-01-20 14:28:06 +0800
comments: true
categories: HTTP  MIME XML
---
这是我自己的app中，需要解析xml文档，然后针对不同类型的文件，进行相应的操作。所以必须知道文件对应的mime-type（http返回的xml文档中有相应的描述）；<!--more-->

-----------------------
**下面是我自己根据wiki和测试后得到的数据，作为备忘：**


**音频 - Audio**

```
.mp3		audio/mpeg
.aac    	audio/x-aac
.m4a		audio/mp4a-latm
.aiff		audio/x-aiff
.wav		audio/x-wav
```

**视频 - Video**

```
.mov		video/quicktime
.mp4		video/mp4
.3gp		video/3gpp
.m4v		video/x-m4v
```

**图片 - Image**

```
.tiff		image/tiff
.tif		image/tiff
.jpg		image/jpeg
.jpeg		image/jpeg
.gif		image/gif
.png		image/png
.bmp		image/bmp
.bmpf		image/bmp
.ico		image/x-icon
/xbm		image/x-bitmap
```

**其他 - Other**

```
.pdf		application/pdf
.txt		text/plain
.zip		application/zip
.tar		application/tar
.rar		application/rar
```


###如果不清楚文件的MIME-Type，可以用下面的方式获取，打印查看：
```
-(NSString*)mimetypeForFile:(NSString*)_filepath {
	NSURL* fileUrl = [NSURL fileURLWithPath:_filepath];
	NSURLRequest* fileUrlRequest = [NSURLRequest requestWithURL:fileUrl];
	NSError* error = nil;
	NSURLResponse* response = nil;
	[NSURLConnection sendSynchronousRequest:fileUrlRequest returningResponse:&response error:&error];
	return [response MIMEType];
}
```

