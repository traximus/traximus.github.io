---
layout: post
title: "ARC内存管理拾遗"
date: 2014-08-04 14:52:09 +0800
comments: true
categories: iOS_App, memory management
---

>这是我之前做的一些笔记，今天有空就又看了一下官方`ARC`的内存管理部分，算是对这部分进行简单的记录；<!--more-->

### heap && stack


  类别    |     内存管理   |     空间大小   |           碎片         |   分配方式     |      效率   
---------|:-------------:|:-------------:|:---------------------:|:-------------:|:-----------:
  heap     |   programmer  |          大       |     new和delete产生内存碎片 |           动态分配      |  低
  stack    |    编译器      |          小       |     FIFO，一一对应，无碎片   |           编译器静态分配 |  高


----------

### MRR (manual retain-release)

[官方文档](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)

####1）NSObject部分

* 通过`malloc`， `new`, `copy`获取的对象， `retainCount`+1
* 谁拥有，谁管理 （`ownership`）
* 主视图对子视图是强引用`strong`， 所以子视图只能对主视图弱引用weak，避免retain循环
* 应该在恰当的时机，remove `notification`，设置`delegate`为nil，即使在ARC下
* 集合类`NSArray`， `NSDictionary`， `NSSet`等，对包含的child object会进行管理，无需单独的内存管理；

####2）CoreFoundation部分

* `create`, `copy`会形成own关系，不需要时应该`CFRelease((CFTypeRef)obj)`
* `get`方式得到的object不形成own关系，不需要单独管理
* 一个object作为参数，传递到函数中后，receiver不与他形成own关系，也就是，可能在任何时间被`deallocate`掉，因此，需要`CFRetain(obj)`
* `CFIndex count = CFGetRetainCount(obj)`
* `myCFObj1 = myCFObj2` -->引用拷贝，不会生成2个对象，可以使用`CFStringCreateCopy`或者类似的方式，生成一个新对象
* CF对象，copy的时候，需要自己实现`deep copy`

--------

### ARC (Automatic Reference Counting)

[官方文档](https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226)

####1) 基本要点
* 可以根据需要，实现`dealloc`方法，来实现对`resource`的管理（比如，设置`delegate`为nil）
* `CoreFoundation`对象，仍然需要`CFRelease`，`CFRetain`
* `assigned`的变量，在`ARC`下是`strong`
* 不能使用c struct的指针，而应该创建一个对应的objC对象来管理数据
* 不能使用`retain`， `release`, `retainCount`，`[super dealloc]`,` NSAutoreleasePool`（使用`@autoreleasepool`代替）
* 为了兼容`MRR`,`ARC`对method的命名有一个约束：你不应该该一个`property`命名的时候，以`new`开头

```
   // Won't work:
   @property NSString *newTitle;
   
   // Works:
   @property (getter=theNewTitle) NSString *newTitle;
   
```

####2）变量修饰符

* `__strong`: is the default. An object remains “alive” as long as there is a strong pointer to it.
* `__weak`: A weak reference is set to nil when there are no strong references to the object.

   **注意：`outlets`最好申明为`weak`**
         
* `__unsafe_unretained`: a reference that does not keep the referenced object alive and is not set to nil when there are no strong references to the object - 简单来说，就是不安全
* `__autoreleasing`: is used to denote arguments that are passed by reference (id *) and are autoreleased on return     


####3） MRR和ARC的转换

* `__bridge`: 直接转换CF对象和objC对象，不修改ownership
* `__bridge_retained` && `CFBridgingRetain`: 将objC对象转换为CF对象，并同时修改`ownership`，之后，需要自己对该对象进行CFRelease()管理 
* `__bridge_transfer` && `CFBridgingRelease`: 将CF对象转换为objC对象，并修改`ownership`，之后的管理交给`ARC`
 
  **注意：id和void *不能直接转换**



-------
#####基本上就是这么多吧，还有一些知识点没有罗列，只是记录了我自己容易忽略的部分。