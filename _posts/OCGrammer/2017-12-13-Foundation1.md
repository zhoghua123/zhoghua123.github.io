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
     .......
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
        1. 指资源路径,互联网上每一个资源都有一个唯一的URL,它用于表示资源的位置.
        2. 组成: 协议头://域名或ip:port/路径
        3.  协议头:
            1. file(本地文件)
            2. ftp(ftp服务器上)文件传输协议
            3. http(s)(网络http服务器上)超文本传输协议
        4. 域名
            1. 相当于IP地址的别名,只是表现形式不一样
            2. 域名通过DNS解析成IP地址,然后去访问 
        5. 举例: `http://weibo.com/a.png`
    2.  NSUTF8StringEncoding:用到中文就可以用这种编码
    3. 创建URL的方法(**重要!!!!!**)
        1. `URLWithString`
            1. 如果加载的资源是本机上的资源,那么URL中的主机地址可以省略
            2. 虽然主机地址可以省略,但是需要注意,文件路劲中最前面的`/`不能省略,文件路径最前面的`/`代表根路径
            
            ```
            //URL : 协议头 + 主机地址 + 文件路径   
            //省略前
             NSString *path = @"file://192.168.199.199/Users/NJ-Lee/Desktop/lnj.txt";
            //省略后如下:
             NSString *path = @"file:///Users/NJ-Lee/Desktop/lnj.txt";
             NSURL *url = [NSURL URLWithString:path];
            ``` 
        2. `fileURLWithPath`
            1. 系统会自动给我们传入的字符串添加协议头(`file://`),所以字符串中不需要再写`file://`
            2. 开发中一 般情况下,如果是访问本机的资源,创建URL的时候,建议使用`fileURLWithPath`方法创建;因为url不支持中文,如果URL中包含中文,那么无法访问;但是如果是通过`fileURLWithPath`方法创建URL,哪怕URL中包含中文也可以进行访问,系统内部会自动对URL中包含的中文进行处理
            
            ```
            NSURL *url = [NSURL fileURLWithPath: @"/Users/NJ-Lee/Desktop/lnj.txt"];
            ```
            
    4. 但是如果通过`URLWithString`也想让他支持中文怎么办呢?解决方法:  
        1. 在创建URL之前先对字符串中的中文进行处理,进行百分号编码  
            
            ```
            
             NSString *path = @"file:///Users/NJ-Lee/Desktop/未命名文件夹/lnj.txt";
              NSLog(@"处理前:%@", path);
              //1.处理
             path = [path stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
             NSLog(@"处理后:%@", path);
             //2.创建URL
             NSURL *url = [NSURL URLWithString:path];
             NSLog(@"url = %@", url);
             //3.根据URL加载文件中的字符串
             NSString *str = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
             
             NSLog(@"str = %@", str);
            ```
    5. 从文件中读取字符串
        1. 注意: 以后在OC方法中但凡看到XXXofFile的方法, 传递的一定是全路径(绝对路径)
        2. 这个方法只支持File(只能读取文件,并且是file本地的)
        
            ```
            NSString *s5 = [[NSString alloc] initWithContentsOfFile:@"/Users/apple/Desktop/1.txt" encoding:NSUTF8StringEncoding error:nil];
        
            ```
        3. 通过url可以万能读取(不仅仅是文件,也可以是网页)
            
            ```
            //URL创建的两种方法:
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
    6. 将字符串写入文件
    
        ```
        // atomically 如果传入YES, 字符串写入文件的过程中如果没有写完, 那么不会生成文件
    //  如果传入NO, 字符串写入文件的过程中如果没有写完, 会生成文件
        //方法1:
        [@"Jack\nJack" writeToFile:@"/Users/apple/Desktop/my.txt" atomically:YES encoding:NSUTF8StringEncoding error:nil];
        
        //方法2:
        NSString *str = @"4234234";
        NSURL *url = [NSURL fileURLWithPath:@"/Users/apple/Desktop/my2.txt"];
        [str writeToURL:url atomically:YES encoding:NSUTF8StringEncoding error:nil];
        ```
        
## NSMutableString
1. `NSString`的子类,继承自`NSString`
2. `NSMutableString`为可变字符串,`NSString`为不可变字符串

```
 //1.创建
    NSMutableString *s1 = [NSMutableString stringWithFormat:@"my age is 10"];
    //2. 拼接内容到s1的后面
    [s1 appendString:@" 11 12"];
    //3.删除is
    // 获取is的范围
    NSRange range = [s1 rangeOfString:@"is"];
    [s1 deleteCharactersInRange:range];
    
    //不可变字符串
    NSString *s2 = [NSString stringWithFormat:@"age is 10"];
    NSString *s3 = [s2 stringByAppendingString:@" 11 12"];
    NSLog(@"s1=%@, s2=%@", s1, s2);
```


