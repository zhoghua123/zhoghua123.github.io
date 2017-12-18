---
layout: post
title: Foundation框架之-NSDate/NSCalendar
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
        
## NSCalendar
1. 获取当前时间的年月日时分秒
    
    ```
     // 获取当前时间
    NSDate *now = [NSDate date];
    NSLog(@"now = %@", now);
    // 日历类
    NSCalendar *calendar1 = [NSCalendar currentCalendar];
    // 利用日历类从当前时间对象中获取 年月日时分秒(单独获取出来)
    // components: 参数的含义是, 问你需要获取什么?
    // 一般情况下如果一个方法接收一个参数, 这个参数是是一个枚举 , 那么可以通过|符号, 连接多个枚举值
    NSCalendarUnit type = NSCalendarUnitYear |
                          NSCalendarUnitMonth |
                          NSCalendarUnitDay |
                          NSCalendarUnitHour |
                         NSCalendarUnitMinute |
                        NSCalendarUnitSecond;
    NSDateComponents *cmps = [calendar1 components:type fromDate:now];
    NSLog(@"year = %ld", cmps.year);
    NSLog(@"month = %ld", cmps.month);
    NSLog(@"day = %ld", cmps.day);
    NSLog(@"hour = %ld", cmps.hour);
    NSLog(@"minute = %ld", cmps.minute);
    NSLog(@"second = %ld", cmps.second);
    ```
2. 比较两个时间之间的差值, 比较相差多少年多少月多少日多少小时多少分钟多少秒
    
    ```
     // 2.1过去的一个时间
    NSString *str = @"2015-06-29 07:05:26 +0000";
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    formatter.dateFormat = @"yyyy-MM-dd HH:mm:ss Z";
    NSDate *date = [formatter dateFromString:str];
    // 2.2当前的时间
    NSDate *now = [NSDate date];
    NSLog(@"date = %@", date);
    NSLog(@"now = %@", now);
    // 2.3比较两个时间
    NSCalendar *calendar = [NSCalendar currentCalendar];
    NSCalendarUnit type = NSCalendarUnitYear |
    NSCalendarUnitMonth |
    NSCalendarUnitDay |
    NSCalendarUnitHour |
    NSCalendarUnitMinute |
    NSCalendarUnitSecond;
    NSDateComponents *cmps = [calendar components:type fromDate:date toDate:now options:0];
    NSLog(@"%ld年%ld月%ld日%ld小时%ld分钟%ld秒钟", cmps.year, cmps.month, cmps.day, cmps.hour, cmps.minute, cmps.second);
    
    ```

