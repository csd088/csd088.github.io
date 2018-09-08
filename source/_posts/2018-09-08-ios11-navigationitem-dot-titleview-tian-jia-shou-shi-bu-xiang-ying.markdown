---
layout: post
title: "iOS11 navigationItem.titleView 添加手势不响应"
date: 2018-09-08 14:15:55 +0800
comments: true
categories: 
---

在iOS11 navigationItem.titleView = 自定义view，自定义view添加点击手势，事件不触发，在之前版本是没问题的。

<!--more-->

在iOS11 自定义titleview里有个button,或者点击手势,点击事件不触发了.的解决办法如下：

1：在自定义titleview 里重写 intrinsicContentSize 属性，代码如下：

	@property(nonatomic, assign) CGSize intrinsicContentSize;
	
2：然后在 self.navigationItem.titleView = _titleView; 之前加入下面的代码：

	_titleView.intrinsicContentSize = CGSizeMake(200, 40);
	
CGSizeMake(200, 40); 宽高 自己根据实际情况自己设置具体值就可以了。这样titleview 里的button 应该就可以点击了。
