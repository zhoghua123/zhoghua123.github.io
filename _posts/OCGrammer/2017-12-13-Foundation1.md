---
layout: post
title: Foundation框架之-字符串
category: OC语法
tags: OC语法
description: Foundation框架
---
## NSString
1. 字符串的创建
    1. 常量创建
        
        ```
         NSString *s1 = @"jack";//常用
        ```
    2. 对象方法创建
        
        ```
        NSString *s2 = [[NSString alloc] initWithString:@"jack"];
        NSString *s3 = [[NSString alloc] initWithFormat:@"age is %d", 10];
        ```
    3. 类方法创建
        1. 一个对象方法一般都会有一个类方法配对
        
        ```
        [NSString stringWithString@""]
     [NSString stringWithFormat:@""];
        ```
2. OC字符串与C语言字符串
    1. C字符串 --> OC字符串
    
        ```
        NSString *s4 = [[NSString alloc] initWithUTF8String:"jack"];
        ```
    2. OC字符串 --> C字符串

        ```
        const char *cs = [s4 UTF8String];
        ```
3. 字符串的读写
    1. URL
        1. 指资源路径
        2. 组成: 协议头://路径
        3.  协议头:
            1. file(本地文件)
            2. ftp(ftp服务器上)
            3. http(s)(网络http服务器上)
        4. 举例: `http://weibo.com/a.png`
    2.  NSUTF8StringEncoding:用到中文就可以用这种编码
    
    3. 从文件中读取字符串
        1. 这个方法只支持File(只能读取文件)
        
            ```
            NSString *s5 = [[NSString alloc] initWithContentsOfFile:@"/Users/apple/Desktop/1.txt" encoding:NSUTF8StringEncoding error:nil];
        
            ```
        2. 通过url可以万能读取(不仅仅是文件,也可以是网页)
            
            ```
            //方法一:
            /*
             file:// : 固定格式,本地文件
             /Users/apple/Desktop/1.txt : 文件全路径
             */
            // NSURL *url = [[NSURL alloc] initWithString:@"file:///Users/apple/Desktop/1.txt"];
            //方法二:fileURLWithPath,直接告诉该协议头为file,因此后面可以省略(file://)了
            NSURL *url = [NSURL fileURLWithPath:@"/Users/apple/Desktop/1.txt"];
            NSString *s6 = [[NSString alloc] initWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
            NSLog(@"s6=\n%@", s6);
            ```
    4. 将字符串写入文件
    
        ```
        //方法1:
        [@"Jack\nJack" writeToFile:@"/Users/apple/Desktop/my.txt" atomically:YES encoding:NSUTF8StringEncoding error:nil];
        
        //方法2:
        NSString *str = @"4234234";
        NSURL *url = [NSURL fileURLWithPath:@"/Users/apple/Desktop/my2.txt"];
        [str writeToURL:url atomically:YES encoding:NSUTF8StringEncoding error:nil];
        ```


