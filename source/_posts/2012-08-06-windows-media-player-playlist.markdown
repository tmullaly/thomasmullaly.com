---
layout: post
title:  "Windows Media Player Playlist"
date:   2012-08-06
categories: windows
---
{% highlight bash %}
  <?wpl version="1.0"?>
  <smil>
    <head>
        <meta name="Generator" content="Microsoft Windows Media Player -- 12.0.7601.17514"/>
        <meta name="ItemCount" content="0"/>
        <title>MyStreams</title>
    </head>
    <body>
        <seq>
			<media src="http://wmbr.org/WMBR_live_128.m3u"/>
            <media src="http://jpr.streamguys.org:80/jpr-classics"/>
			<media src="http://amber.streamguys.com:4860/listen.pls"/>
			<media src="http://streams.wgbh.org:8000"/>
			<media src="http://npr.ic.llnwd.net/stream/npr_live24"/>
        </seq>
    </body>
  </smil>
{% endhighlight %}