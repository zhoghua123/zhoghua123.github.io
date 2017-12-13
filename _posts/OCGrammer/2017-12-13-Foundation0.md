---
layout: post
title: Foundation框架之-结构体
category: OC语法
tags: OC语法
description: Foundation框架
---

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

