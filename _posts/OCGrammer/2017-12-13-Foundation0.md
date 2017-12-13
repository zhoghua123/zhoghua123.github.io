---
layout: post
title: Foundation框架之-结构体
category: OC语法
tags: OC语法
description: Foundation框架
---

### 如何通过XCode编译器测试代码
1. 创建一个工程:File -> new ->project ->macOS ->command Line Tool -> 工程名字 ->选择路径即可
2. 创建target: File -> new -> target->macOS ->command Line Tool->target名称->创建
3. 运行指定的target: 点击"停止"右边的target,选择对应的target,然后编译运行即可

### NSRange
1. 定义

    ```
    typedef struct _NSRange {
        NSUInteger location;
        NSUInteger length;
    } NSRange;
    ```
    
    1. length表示长度
    2. location表示位置
2. 初始化方式
    
    ```
    @"i love oc"  ;// love的范围
    NSRange r1 = {2, 4}; // 不用
    NSRange r2 = {.location = 2, .length = 4};// 不用
    NSRange r3 = NSMakeRange(2, 4); // 掌握
    ``` 
3. 其他使用
    
    ```
     //查找某个字符串在str中的范围
    NSString *str = @"i love oc";
    // 如果找不到，length=0，location=NSNotFound==-1
    NSRange range = [str rangeOfString:@"java"];
    NSLog(@"loc = %ld, length=%ld", range.location, range.length);
    ```

### NSPoint\CGPoint
1. 定义
    
    ```
    struct CGPoint {
    CGFloat x;
    CGFloat y;
    };
    typedef CGPoint NSPoint;
    ```
    
    1. CGPoint(常用) 与 NSPoint等价
    2. 坐标点的x,y
2. 初始化方式
    
    ```
    CGPoint p1 = NSMakePoint(10, 10);
    NSPoint p2 = CGPointMake(20, 20);// 最常用
    ```
    
### NSSize\CGSize
1. 定义
    
    ```
    struct CGSize {
    CGFloat width;//宽
    CGFloat height;//高
    };
    typedef CGSize NSSize;
    ```
2. 初始化
    
    ```
    NSSize s1 = CGSizeMake(100, 50);
    NSSize s2 = NSMakeSize(100, 50);
    CGSize s3 = NSMakeSize(200, 60);
    ```
    
### NSRect\CGRect
1. 定义
    
    ```
    struct CGRect {
    CGPoint origin;
    CGSize size;
    };
    typedef CGRect NSRect;
    ```
2. 初始化

    ```
    CGRect r1 = CGRectMake(0, 0, 100, 50);
    
    CGRect r2 = { {0, 0}, {100, 90}};
    
    CGRect r3 = {p1, s2};
    
    // 使用CGPointZero等的前提是添加CoreGraphics框架
    CGRect r4 = {CGPointZero, CGSizeMake(100, 90)};
     // 表示原点
    // CGPointZero == CGPointMake(0, 0)
    ```
    
### 其他用法

1. 将结构体转为字符串
    
    ```
    NSString *str = NSStringFromPoint(p1);
    NSString *str = NSStringFromSize(s3);
    NSString *str = NSStringFromRect(r1);
    NSLog(@"%@", str);
    ```  
2. 结构体的比较
    
    ```
    // 使用这些CGPointEqualToPoint、CGRectContainsPoint等函数的前提是添加CoreGraphics框架

    // 比较两个点是否相同（x、y）
    BOOL b = CGPointEqualToPoint(CGPointMake(10, 10), CGPointMake(10, 10));
    //CGRectEqualToRect(<#CGRect rect1#>, <#CGRect rect2#>)
    //CGSizeEqualToSize(<#CGSize size1#>, <#CGSize size2#>)
    
    //包含(矩形包含某个点)
    // x (50, 150) y (40 , 90)
    BOOL b2 = CGRectContainsPoint(CGRectMake(50, 40, 100, 50), CGPointMake(60, 45));
    
    NSLog(@"%d", b2);
    ```

