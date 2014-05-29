---
layout: post
title: "Storyboard相关笔记"
date: 2014-05-27 19:08:52 +0800
comments: true
categories: iOS_App  storyboard
---

>####Storyboard出来这个么长时间了，我自己使用的却不是很多，也不够熟练，这里只是简单的记录一下相关笔记，主要是搞清楚segue，scene,以及他们之间的跳转关系，部分资料也是参考别人的整理。<!--more-->


#####首先简单看一下

![summary](/images/2014-05-27-01.png)

一个`Storyboard`管理多个`VC`，这多个`VC`就看做是多个`scene`,视图之间的跳转关系定义都可以在`Storyboard`中完成。

--------------



##segue类型介绍

这里的简介转载自<http://www.2cto.com/kf/201210/161737.html>
>在storyboard中，segue有几种不同的类型，且iPhone和iPad对应的segue也不尽相同；

>在iPhone中，segue有push， modal，custom三种不同类型，这些segue定义了跳转时分别对应的方式；

>在iPad中，segue有push， modal，replace, popover， custom五种方式；

- modal 模态视图，弹出的模态视图，用户只能在该View上响应，不再使用，需要dismiss
- push  配合导航栏NavigationController一起使用，从右侧划入的方式
- popover 浮动窗视图，iPad专用；
- replace  替换当前scene， iPad专用
- custom  自定义跳转方式



------------



##Storyboard的创建

1. 可以直接使用项目默认的storyboard，也可以自己添加多个storyboard;
2. 获取storyboard

   `+ (UIStoryboard *)storyboardWithName:(NSString *)name bundle:(NSBundle *)storyboardBundleOrNil`

3. 获取storyboard中的某个VC，然后对该VC进行相应的segue操作
	
   `- (id)instantiateInitialViewController;`
   
   `- (id)instantiateViewControllerWithIdentifier:(NSString *)identifier;`
4. 一个VC在被触发跳转之间，会调用下面的方法，一般进行数据传递

```	
	// In a storyboard-based application,  do a little preparation before navigation
	- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender
	{
    	// used to transfer some data to new VC
   		// Get the new view controller using [segue destinationViewController].
    	// Pass the selected object to the new view controller.
	}
	
```

5. 对于一个VC，可以使用下面的方法，来指定执行哪一个segue(如果该VC指定了多个segue)，或者查询segue是否可用
  
```
	
	- (void)performSegueWithIdentifier:(NSString *)identifier sender:(id)sender
	
	- (BOOL)shouldPerformSegueWithIdentifier:(NSString *)identifier sender:(id)sender 
	
	// Invoked immediately prior to initiating a segue. Return NO to prevent the segue from firing.
	// The default implementation returns YES. 
	// This method is not invoked when -performSegueWithIdentifier:sender: is used.
	
```

6. 对于一个VC，可以指定segue类型为modal时的style

```
	
	/*
  	Defines the transition style that will be used for this view controller when it is 
  	presented modally. Set this property on the view controller to be presented, not the presenter.
  	Defaults to UIModalTransitionStyleCoverVertical.
	*/
	
	@property(nonatomic,assign) UIModalTransitionStyle modalTransitionStyle NS_AVAILABLE_IOS(3_0);
	@property(nonatomic,assign) UIModalPresentationStyle modalPresentationStyle NS_AVAILABLE_IOS(3_2);
	
```
	
	**UIModalTransitionStyle && UIModalPresentationStyle 的定义为：**

```
	
	typedef NS_ENUM(NSInteger, UIModalTransitionStyle) {
    	UIModalTransitionStyleCoverVertical = 0,
    	UIModalTransitionStyleFlipHorizontal,
    	UIModalTransitionStyleCrossDissolve,
    	UIModalTransitionStylePartialCurl,
	};

	typedef NS_ENUM(NSInteger, UIModalPresentationStyle) {
    	UIModalPresentationFullScreen = 0,
    	UIModalPresentationPageSheet,
    	UIModalPresentationFormSheet,
    	UIModalPresentationCurrentContext,
    	UIModalPresentationCustom,
    	UIModalPresentationNone = -1,               
	};
	
```
	
	
	

-------------

##Storyboard的简单使用

1. 一般情况下，一个storyboard至少管理者一个scene，对于VC之间的跳转segue可以直接从能触发的控件，
  按住Control，然后连接到另一个你需要跳转的VC上，记住，你最好给segue设置一个identifier。
  
2. 同时，你可以不指定segue，只给某一个VC指定一个Storyboard ID, 在使用的时候，直接调用下面的方法来获取VC

	`- (id)instantiateViewControllerWithIdentifier:(NSString *)identifier;`

--------------







