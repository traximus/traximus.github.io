---
layout: post
title: "iOS performance Tips&amp;Tricksks"
date: 2014-01-21 11:09:33 +0800
comments: true
categories: iOS_App
---
>原文：<http://www.raywenderlich.com/31166/25-ios-app-performance-tips-tricks>

app拥有良好的性能非常重要，这是保证良好用户体验的基石。这篇文章是基于原文，我自己的备忘记录。<!--more-->

----------------

##1.ARC
新项目现在一般都是基于ARC了，就算使用ARC，很多情况下，还是会有内存泄露的情况存在，所以使用instrument就很必要了。

**如何使用instrument：<http://www.raywenderlich.com/23037/how-to-use-instruments-in-xcode>**


---------

##2.Reuse

####1)reuseidentifier
* UITableViewCell
* UICollectionViewCell
* UITableViewHeaderFooterViews

####2)Resue && Lazy load
学习UITableView的方式，不要一次性创建所有的subView,你只在需要的时候创建subView,并把它添加到reuse Queue;这可以避免大量的消耗。想啊次使用的时候，你只需要reuse即可。

####3)reuse Expensive object
类似与NSDateFormatter, NSCalendar，这些object的初始化，设置效率比较低，代价比较大。针对这种情况，应该考虑重用。

**应对的做法有2种：**

* 添加为class的变量，进行reuse
* 创建一个static variable

对于第二种情况，你应该写一个类似与singleton的方法，来获取这个object

```
-(NSDateFormatter *)fotmatter
{
	static NSDateFormatter *formatter;
	static dispatch_once_t onceToken;
	dispatch_once(&onceToken, ^{
		_formatter = [[NSDateFormatter alloc] init];
		_formatter.dateFormat = @"EEE MMM dd HH:mm:ss Z yyyy";//twitter date format
	});
	
	return formatter;
}
```


-------





##3.set `opaque` to YES
default

----


##4.Avoid fat XIBs
因为在XIB初始化的时候，会直接加载所有内容到内存中，增加大量的开销。因此，尽量把XIB拆分开，只在需要的时候在初始化并进行加载，这样就可以避免一次性加载国语复杂的xib所导致的内存和cpu过量消耗；

当然，现在很多项目使用stroyboard在一定程度上已经缓解了这个问题。

-------


##5.DO NOT block Main Thread
* GCD
* [NSURLConnection sendAsynchronousRequest: completionHandler:]

----------


##6.`Size image` to imageVew's size
事实上，将一个image放入到imageview中，并进行sizeToFit是有很大开销的,而且这部分开销可以避免；
当我们获取到image后，最好先进行resize,然后再放入到合适的imageView中；

-------



##7. Choose right data format and structure

####1)choose `correct collection`
* NSArray：**ordered** list of values, **Quick lookup** by **index**, **Slow lookup** by **value**；**Slow** to insert&delete
* NSDictionary：key-value pairs, **Quick lookup** by **key**
* NSSet：unordered list of values, **Quick lookup**by **value**, **Quick** insert&delete



####2)choose `right data format`

* JSON - faster to parse, smaller than XML
* XML - soap advantage


####3)choose `corrent data storage option`
* NSUserDefaults	-	small data
* XML,JSON,Plist	-	expensive operation to store and parse
* NSCoding (archive)-	expensive as above
* sqlite			-	easy to use
* Core Data			-	like above

------------



##8.enable GZIP compression

-------



##9.cache
对于不会变化，并且经常使用到的object进行cache; 对于http cache, 基本的第三方库都已经实现了cache; 对于一般的object，可以使用NSCache:<http://nshipster.com/nscache/>

-----------



##10. Drawing performance
考虑不同的绘图机制的性能

* UIKit
* Core Animation
* Core Graphics(相对于其他而言，更加高效)

//绘制 Shadow Path

```
//CoreAnimation - 这种方式代价高昂
UIView *view= [[UIView alloc] init];
view.layer.shadowOffst = CGSizeMake(-1.0, 1.0);
view.layer.shadowRadius = 5.0;
view.layer.shadowOpacity = 0.6;
```

```
//effective way
view.layer.shadowPath = [[UIBezierPath bezierPathWithRect: view.bounds] CGPath];
```

----------


##11.处理Memory Warnings
常见有3中方式接收到memory warning

1. applicationDidReceiveMemoryWarning: appDelegate
2. didReceiveMemoryWarning：			UIViewController
3. UIApplicationDidReceiveMemoryWarningNotification：notificationCenter

----------


##12.Avoid Re-Processing Data
这主要是数据出库的逻辑需要规划好

--------




##13. background image

* full size image - [UIImage imageNamed:@"test.png"];
* pattern image - [UIColor colorWithPatternImage:[UIImage imageNamed:@"test.png"]];

----------

##14.reduce your web footprint
尽量减少javascript的使用

---------

##15.Optimize tableview
* reuse cell
* set subview opaque
* avoid gradients, image scale, offscreen drawing
* cache height if height is variable
* use asynchronously method for cell' contents
* use shadowPath to set shadow
* reduce te number of subViews
* do as little work possible in `cellForRowAtIndexPath:`
* use the appropriate data structure
* use `rowHeight`, `sectionFooterHeight`,`sectionHeaderHeight` to set constant height instead of delegte(效率上能理解，设计上我自己暂时不是很理解这条)

--------


##16.speed up Launch time
注1：异步获取数据，延迟加载，避免臃肿的xib

注2：测试Launch time最有效的方式是，断开Xcode,单独运行你的app

-------

##17.image load

* imageNamed:				-	首先从system cache里面查找该image，如果没有找到，再从文件加载image
* imageWithContentsOfFile:	-	直接从文件加载image,不检查system cache

注意：对于会多次使用到的image，选择imageNamed：更加合适，因为system cache会进行cache; 而对于单次使用的image，直接从文件加载的imageWithContentsOfFile:更加合适


----------------
上面的知识点只是针对我个人做备忘，每一个人对此的理解不一样，因此建议看看原文。






