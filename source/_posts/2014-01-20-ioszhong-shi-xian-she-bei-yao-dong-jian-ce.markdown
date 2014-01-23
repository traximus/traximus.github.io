---
layout: post
title: "iOS中实现设备摇动检测"
date: 2014-01-20 15:01:09 +0800
comments: true
categories: iOS_App
---
要检测iOS设备的摇动事件，网上有一些资料。我自己测试过的是下面这2种方式(其实算作一种，只是第二种方式可以在vc里面直接控制是否响应shake事件)<!--more-->

1. UIWndow
2. UIViewController(继承自UIResponder)

-------------

###1.UIWindow

```
-(void) motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
	//开始
}

-(void) motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
	if (motion == UIEventSubtypeMotionShake)
	{
		//触发结束
		//
		//这里可以促发系统震动，或者发送一个通知，进行相应的处理
		//
		//例子： 添加系统震动
		//  AudioToolBox.framework
		//  #import<AudioToolbox/AudioToolbox.h>
		//  AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
	}
}

-(void) motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
	//取消
}
```

###2.UIViewController

```
#pragma mark - 
#pragma mark - viewController methods

-(BOOL) canBecomeFirstResponder
{
	//这里设定一个变量，来控制是否支持shake事件
	return self.shakeDetectionOn;
}

-(void) viewDidAppear:(BOOL)animated
{
	[super viewDidAppear:animated];
	
	if ([self canBecomeFirstResponder])
	{
		//下面这行代码，也可以写到viewDidLoad或者applicationDidFinishLaunch中
		[[UIApplication sharedApplication] setApplicationSupportShakeToEdit:YES];
		
		[self becomFirstResponder];
	}
	else
	{
		NSLog(@"NOT supported");
	}
}


#pragma mark -
#pragma mark - UIResponder support motion

-(void) motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event
{
	NSLog(@"======== shake begins ==========");
}

-(void) motionCancelled:(UIEventSybtype)motion withEvent:(UIEvent *)event
{
	NSLog(@"======== shake cancelled ==========");
}

-(void) motionEnd：（UIEventSubtype)motion withEvent:(UIEvent *)event
{
	if (motion == UIEventSubtypeMotionShake)
	{
		NSLog(@"======== shake ended ========");
		//这里可以添加震动反馈，或者发送notification
	}
}
```

======
####说实话，在实际应用中，很少有合适的场景会用到这个事件；
而且上面的方式检测shake事件，我自己测试的时候，发现响应有一点延迟，肯定可以再改进；
找机会再慢慢研究一下。
