---
layout: post
title: 使用CocoaPods集成Reveal
date: 2016-03-16 15:32:24.000000000 +09:00
---

![hello reveal](http://o9lammkmn.bkt.clouddn.com/reveal-hero-2.png)
#### 这种方式最简单集成进项目

CocoaPods提供了Podspec用于将Reveal集成到项目。

此说明要求您在之前已经在项目中配置好了CocoaPods，若不然，请先行配置Cocoapods。

1. 将下面内容加入你的Podfile中：

	```pod 'Reveal-iOS-SDK', :configurations => ['Debug']
	```
2. 在项目的根目录下执行 pod install 命令（如果之前已经在项目中使用了Cocoapods，请执行 pod update 命令）。

> 警告: 不要将连接了Reveal库文件的应用用于正式发布。下面的指南描述了一种使用构建配置来使Reveal静态库文件仅在调试构建中连接的方式。Reveal的检视机制，会将您应用的许多内部信息暴露出来，而这将很可能导致您的应用被Apple审查团队拒绝发布。

1. 在Podfile中移除或注释 pod 'Reveal-iOS-SDK', :configurations => ['Debug']

2. 在Xcode的 Project Navigator中选中您的工程，对于每一个集成了Reveal得target，请选择 Build Settings 标签页，将下面内容从 Debug 配置中的 Other Linked Flags 设置中移除：
-framework Reveal![remove reveal](http://o9lammkmn.bkt.clouddn.com/reveal_remove.png)