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
    7. 字符串比较
        
        ```
         NSString *str1 = @"abc";
    NSString *str2 = @"ABC";
    
    
        // 1.比较两个字符串的"内容"是否相同
        BOOL flag = [str1 isEqualToString:str2];
        NSLog(@"flag = %i", flag);
        
        // 2.下面这个方法, 是比较两个字符串的"地址"是否相同
        flag = (str1 == str2);
        NSLog(@"flag = %i", flag);
        
        // 3. 比较字符串的大小(比的是ASCI码值)
        // NSOrderedAscending  前面的小于后面的
        // NSOrderedSame,  两个字符串相等
        // NSOrderedDescending  前面的大于后面的
        switch ([str1 compare:str2]) {
            case NSOrderedAscending:
                NSLog(@"str1小于str2");
                break;
            case NSOrderedSame:
                NSLog(@"str1等于str2");
                break;
            case NSOrderedDescending:
                NSLog(@"str1大于str2");
                break;
            default:
                break;
        }
    
        //4. 忽略大小写进行比较
        switch ([str1 caseInsensitiveCompare:str2]) {
            case NSOrderedAscending:
                NSLog(@"str1小于str2");
                break;
            case NSOrderedSame:
                NSLog(@"str1等于str2");
                break;
            case NSOrderedDescending:
                NSLog(@"str1大于str2");
                break;
            default:
                break;
        }
    
        ```
    8. 字符串搜索
        
        ```
         NSString *str = @"http://www.520it.com/img/lnj.gif";
        // 1.判断是否以什么开头
        // 本质就是从字符串的第一个字符开始匹配, 只要不匹配就返回NO
        if ([str hasPrefix:@"http://"]) {
            NSLog(@"是一个URL");
        }else
        {
            NSLog(@"不是一个URL");
        }
        
        // 2.判断是否以什么结尾
    
        // 本质就是从字符串的最后一个字符开始匹配, 只要不匹配就返回NO
        if ([str hasSuffix:@".gif"]) {
            NSLog(@"动态图片");
        }else{
            NSLog(@"不是动态图片");
        }
        
        // 3.判断字符串中是否包含520it.com
        NSString *str1 = @"abcd";
        //方法一:
        // 只要str1中包含该字符串, 那么就会返回该字符串在str1中的起始位置以及该字符串的长度
        // location从0开始 , length从1开始
        // 如果str中没有需要查找的字符串, 那么返回的range的length=0, location = NSNotFound
        NSRange range =  [str1 rangeOfString:@"lnj"];
    //    if (range.location == NSNotFound) {
        if (range.length == 0){
            NSLog(@"str中没有需要查找的字符串");
        }else{
            NSLog(@"str中有需要查找的字符串");
            NSLog(@"location = %lu, length = %lu", range.location, range.length);
        }
        //方法二:
      BOOL isContain =  [str1 containsString:@"a"];
        ```
    9. 字符串截取
        
        ```
         //截取出"小码哥"
         NSString *str = @"<head>小码哥</head>";
        // 1.动态获取截取的起始位置
        NSUInteger location = [str rangeOfString:@">"].location + 1;
        // 2.动态获取截取的长度
        // 注意:rangeOfString是从左至右的开始查找, 只要找到就不找了
        //从右往左开始
    //    NSUInteger length = [str rangeOfString:@"<" options:NSBackwardsSearch].location - location;
        NSUInteger length = [str rangeOfString:@"</"].location - location;
        NSLog(@"location = %lu, length = %lu", location, length);
        // 3. 创建range
        NSRange range = NSMakeRange(location, length);
        //3. 截取
        NSString *newStr = [str substringWithRange:range];
        NSLog(@"str = %@", str);
        NSLog(@"newStr = %@", newStr);
        
        // 从什么地方开始截取, 一直截取到最后
        NSString *newStr = [str substringFromIndex:6];
        // 从开头开始截取, 一直截取到什么位置
        NSString *newStr = [str substringToIndex:6];
        
        //举例使用
        NSLog(@"str = %@", str);
        NSUInteger location = [str rangeOfString:@">"].location + 1;
        //小码哥</head>
        NSString *newStr = [str substringFromIndex:location];
        NSLog(@"newStr = %@", newStr);
        location = [newStr rangeOfString:@"</"].location;
        //小码哥
        // 改变了指针的指向, 并不是修改了原来的字符串
        newStr = [newStr substringToIndex:location];
        NSLog(@"newStr = %@", newStr);
        ```
   10. 字符串替换
   
        ```
         // OccurrencesOfString: 要替换谁
        // withString: 用谁替换
        // 1.去除空格  2.将&替换为/
        NSString *str = @"   http:   &&www.   520it.com   &img&lnj.gif   ";
        // 1.去除空格
        NSString *newStr = [str stringByReplacingOccurrencesOfString:@" " withString:@""];
        NSLog(@"newStr = |%@|", newStr);
        NSString *newStr2 = [newStr stringByReplacingOccurrencesOfString:@"&" withString:@"/"];
        NSLog(@"newStr2 = |%@|", newStr2);
        
        
        // 3.替换首尾
        //stringByTrimmingCharactersInSet专门用于取出字符串首尾的
        //3.1 去除首尾空格
        NSString *str = @"   http:&&www.520it.com&img&lnj.gif   ";
        //whitespaceCharacterSet 去除首尾空格
        NSCharacterSet *set = [NSCharacterSet whitespaceCharacterSet];
        NSString *newStr = [str stringByTrimmingCharactersInSet:set];
        
        //3.2 去除字符串首尾的大写
        NSString *str = @"HTTP://www.520it.com/img/LNJ.GIF";
        NSCharacterSet *set = [NSCharacterSet uppercaseLetterCharacterSet];
        NSString *newStr = [str stringByTrimmingCharactersInSet:set];
        NSLog(@"newStr = |%@|", newStr);
        ```
    11. 字符串与路径
        
        ```
        NSString *str = @"User/lnj/Desktop/lnj.txt.jpg";
        // 1.判断是否是绝对路径
        // 其实本质就是判断字符串是否以/开头
        if([str isAbsolutePath])
        {
            NSLog(@"是绝对路径");
        }else{
            NSLog(@"不是绝对路径");
        }
        
        
        // 2.获取文件路径中的最后一个目录
        // 本质就是获取路径中最后一个/后面的内容
        NSString *newStr = [str lastPathComponent];
        NSLog(@"%@", newStr);
        
        
        // 3.删除文件路径中的最后一个目录
        // 本质就是删除最后一个/后面的内容, 包括/也会被删除
        NSString *newStr = [str stringByDeletingLastPathComponent];
        NSLog(@"%@", newStr);
        
        
        // 4.给文件路径添加一个目录
         // 本质就是在字符串的末尾加上一个/ 和指定的内容
         // 注意: 如果路径后面已经有了/, 那么就不会添加了
         // 如果路径后面有多个/, 那么会自动删除多余的/, 只保留一个
         NSString *newStr = [str stringByAppendingPathComponent:@"xmg"];
         NSLog(@"%@", newStr);
        
        
        // 5.获取路径中文件的扩展名
        // 本质就是从字符串的末尾开始查找., 截取第一个.后面的内容
        NSString *newStr = [str pathExtension];
        NSLog(@"%@", newStr);
        
        
        // 6.删除路径中文件的扩展名
        // 本质就是从字符串的末尾开始查找.,删除第一个.和.后面的内容
        NSString *newStr = [str stringByDeletingPathExtension];
        NSLog(@"%@", newStr);
        
        // 7.给文件路径添加一个扩展名
        // 本质就是在字符串的末尾加上一个.和指定的内容
        NSString *newStr = [str stringByAppendingPathExtension:@"jpg"];
        NSLog(@"%@", newStr);
        ```     
    12. 字符串的转换
        
        ```
         NSString *str = @"abc";
        // 1.将字符串转换为大写
        NSString *newStr = [str uppercaseString];
        NSLog(@"%@", newStr);
        // 2.将字符串转换为小写
        NSString *newStr2 = [newStr lowercaseString];
        NSLog(@"%@", newStr2);
        // 3.将字符串的首字符转换为大写
        NSString *newStr = [str capitalizedString];
        NSLog(@"%@", newStr);
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


