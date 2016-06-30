---
layout: post
title: Hello World - Vno
date: 2016-06-29 20:32:24.000000000 +09:00
---

#### What's iMessage Apps
![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3203.JPG?imageView2/2/w/600)
> iMessage Apps是iOS X新推出的一个概念，这种App作为消息的内容运行在iMessages应用当中。iMessage Apps可以提供三种类型的消息：

>1.Interactive Message,这种消息发出去后，用户点击这条消息就会运行对应的App。

>这里说的App不是我们之前做的App，而是iMessage App, 本质上还是Extension，相比Extension 存在一个比较大的区别，就是没必要存在containing application,也就是说iMessage App可以单独存在;

![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3200.JPG?imageView2/2/w/600)
![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3206.JPG?imageView2/2/w/600)

>2.Stickers,这种消息可以理解为贴纸。
>创建Sticker Pack Application 把Sticker中需要的图片拖进assets，如果需要的是动图，需要创建一个Sticker Sequence,然后把每一帧PNG拖进去，此外Apple 提醒，Sticker还应该在各种背景下测试，有可能在不同场景中被使用。

[移动端图片格式调研](http://blog.ibireme.com/2015/11/02/mobile_image_benchmark/)

[腾讯PNG格式转换APNG工具](https://isux.tencent.com/introduction-of-apng.html)
 [github地址](https://github.com/iSparta/iSparta)

![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3201.JPG?imageView2/2/w/400)

>关于 Custom Sticker App:
	
>1.可以定制UI,比如显示的Layout、background color等

>2.可以动态创建Sticker,比如,从Server上Download sticker,否则只能在Message AppStore

>3.可以使用摄像头创建Sticker
>4.可以做应用内支付，付费后激活某些Stickers
>3.Other Content(Photos,Videos,Text,Links)。Apple还提供了一种更容易让用户发现iMessage Apps的功能，就是如果你接收到一条消息，你的手机上又没有安装这个iMessage App，在消息下面会显示这条消息的出处，你点击后会直接启动Message AppStore。

>iMessage Apps只能运行在iOS X上，但是macOS和WatchOS都可以接收到iMessage Apps发送的消息。

![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3198.JPG?imageView2/2/w/600)

>MessagesFramework也是iOS X推出的东西，它作为Message应用和iMessage Apps直接的桥梁，通过MessagesFramework我们可以接收到Message应用的回调，也可以向Message应用发送消息。

![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3204.JPG?imageView2/2/w/600)

![Mou icon](http://o9lammkmn.bkt.clouddn.com/IMG_3205.JPG?imageView2/2/w/600)


#### License

Great thanks to [Dale Anthony](https://github.com/daleanthony) and his [Uno](https://github.com/daleanthony/uno). Vno Jekyll is based on Uno, and contains a lot of modification on page layout, animation, font and some more things I can not remember. Vno Jekyll is followed with Uno and be licensed as [Creative Commons Attribution 4.0 International](http://creativecommons.org/licenses/by/4.0/). See the link for more information.
