---
layout: post
title: Foundation框架之-NSDate
category: OC语法
tags: OC语法
description: Foundation框架
---
## NSDate
1. 基本使用

    ```
    // 创建一个时间对象,当前时间
    NSDate *date = [NSDate date];
    // 打印出的时候是0时区的时间（北京-东8区）
    NSLog(@"%@", date);
    //比当前时间晚多少秒
    NSDate *date2 = [NSDate dateWithTimeInterval:5 sinceDate:date];
    // 从1970开始走过的秒数,当前秒的时间戳
    NSTimeInterval seconds = [date2 timeIntervalSince1970];
    // [date2 timeIntervalSinceNow];
    ```
2. 日期的格式化转换
    1. 将日期对象(NSDate)转化为字符串
    
        ```
         //1. 创建日期对象
        NSDate *date = [NSDate date];
        // 2.设置日期的格式
        //2.1 创建日期格式化对象
        NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
        //2.2 设置格式
        // y 年  M 月  d 日
        // m 分 s 秒  H （24）时  h（12）时
        formatter.dateFormat = @"yyyy-MM-dd HH:mm:ss";
        //3.将NSDate转化为字符串
        NSString *str = [formatter stringFromDate:date];
        
        NSLog(@"%@", str);
        ```
    2. 将字符串转化为日期对象(NSDate)
        
        ```
        //将字符串转化为日期对象(NSDate)
        // 09/10/2011
        NSString *time = @"2011/09/10 18:56";
        //创建格式对象
        NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
        //设置格式
        formatter.dateFormat = @"yyyy/MM/dd HH:mm";
        //转化为日期对象
        NSDate *date = [formatter dateFromString:time];
        NSLog(@"%@", date);
        ```


