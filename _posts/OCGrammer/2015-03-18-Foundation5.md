---
layout: post
title: Foundation框架之-NSFileManager/NSData
category: OC语法
tags: OC语法
description: Foundation框架
---

## NSFileManager
1. NSFileManager介绍
    1. 用来管理文件系统的
    2. 用来进行常见的文件/文件夹操作
    3. 使用了单粒模式
        
        ```
         NSFileManager *manager = [NSFileManager defaultManager];
        ```
2. 常见使用
    
    ```
     NSFileManager *manager = [NSFileManager defaultManager];
    // 1.判断一个文件或者文件夹是否存在
    BOOL flag = [manager fileExistsAtPath:@"/Users/xiaomage/Desktop/video/01-NSArray基本概念.mp4"];
    NSLog(@"flag = %i", flag);
    
    // 2.判断一个文件是否存在, 并且判断它是否是一个文件夹
    // 注意: 该方法的返回值是说明传入的路径对应的文件或文件夹是否存在
    //  第二个参数是用于保存判断结果的, 如果是一个目录(文件夹), 那么就会赋值为YES, 如果不是就赋值为NO(文件)
    BOOL dir = NO;
    BOOL flag = [manager fileExistsAtPath:@"/Users/xiaomage/Desktop/video/01-NSArray基本概念.mp4" isDirectory:&dir];
    NSLog(@"flag = %i, dir = %i", flag, dir);
    
    // 3.获取文件或文件夹的属性
    NSDictionary *info = [manager attributesOfItemAtPath:@"/Users/xiaomage/Desktop/video/01-NSArray基本概念.mp4" error:nil];
    NSLog(@"info = %@", info);
    //可以通过文件或者文件夹的属性,拿到其大小
    // 注意: 如果通过attributesOfItemAtPath方法直接获取文件夹大小, 那么获取到的文件夹的大小不准确
    // 要想实现计算一个文件夹中所有文件的大小必须先拿到所有的文件, 然后再获取所有文件的大小, 然后再相加
    
    // 4.获取文件夹中所有的文件
    // 注意:contentsOfDirectoryAtPath方法有一个弊端, 只能获取当前文件夹下所有的文件, 不能获取子文件夹下面的文件
    NSArray *res = [manager contentsOfDirectoryAtPath:@"/Users/xiaomage/Desktop/video" error:nil];
    NSLog(@"res = %@", res);
    //下面的两个方法可以获取当前文件子文件夹下面的文件
//    NSArray *res = [manager subpathsAtPath:@"/Users/xiaomage/Desktop/video"];
//    NSArray *res = [manager subpathsOfDirectoryAtPath:@"/Users/xiaomage/Desktop/video" error:nil];
 
    
    // 5.创建文件夹
    // createDirectoryAtPath: 告诉系统文件夹需要创建到什么位置
    // withIntermediateDirectories: 如果指定的文件中有一些文件夹不存在, 是否自动创建不存在的文件夹
    // attributes: 指定创建出来的文件夹的属性
    // error: 是否创建成功, 如果失败会给传入的参数赋值
    // 注意: 该方法只能用于创建文件夹, 不能用于创建文件
     BOOL flag = [manager createDirectoryAtPath:@"/Users/xiaomage/Desktop/abc/lnj" withIntermediateDirectories:YES attributes:nil error:nil];
    NSLog(@"%i", flag);
    
    
    // 6.创建文件
    // createFileAtPath: 指定文件创建出来的位置
    // contents : 文件中的内容
    // attributes: 创建出来的文件的属性
    
    // NSData : 二进制数据
    // 注意: 该方法只能用于创建文件, 不能用于创建文件夹
    NSString *str = @"12334444";
    NSData *data = [str dataUsingEncoding:NSUTF8StringEncoding];
    [manager createFileAtPath:@"/Users/xiaomage/Desktop/abc.txt" contents:data attributes:nil];
    ```
    
## NSData
1. 简介
    1. 当我们需要把一些信息写入到文件里，或者发送到网络上，我们需要把这些数据转换下，变成纯粹的0、1字符流    
    2. 使用于读写文件，而读写文件时需要一个缓冲区，而NSData就提供了一个缓冲区
    3. NSData遵守NSCopying、NSCoding协议，它提供将面向对象的数组存储为字节
    4. NSData与NSMutableData存储的都是二进制数据，在文件操作、网络、以及核心图形、图像中使用较广泛，NSData创建后不可再修改，而NSMutableData可以修改
2. NSString与NSData之间的转化
    
    ```
    //字符串---NSData
    NSString *str = @"123456";
    NSData *strData = [str dataUsingEncoding:NSUTF8StringEncoding];
    //NSData---字符串
    NSString *dataStr = [[NSString alloc] initWithData:strData encoding:NSUTF8StringEncoding];
    NSLog(@"%@",dataStr);
    ```
3. UIImage与NSData之间的转化
    
    ```
    //UIimage---NSData
    NSString *imgPath = [[NSBundle mainBundle] pathForResource:@"ic_cat_music.png" ofType:nil];
    NSLog(@"%@",imgPath);
    NSData *imageData = [NSData dataWithContentsOfFile:imgPath];
    UIImage *image = [UIImage imageWithData:imageData];
    //UIImage--data
    NSData *imgData = UIImagePNGRepresentation(image);
    ```
4. NSNumber/NSArray/NSDictionary与NSData之间的转化
    1. 数组转NSData是建立在归档的基础上，归档也称为序列化，OC中的NSSting、NSNumber、NSArray、NSDictionary对象、NSData。这些类需要进行数据保存时，可以通过NSKeyArchive类来实现。
    2. Foundation框架`<Foundation/NSKeyedArchiver.h>`提供归档和解档方式，也叫序列化和反序列化过程
    3. 举例使用
        
        ```
        // 序列化
        NSDictionary *dic = @{@"key":@"123",@"key":@"345"};
        //1. 获取归档路径
        NSString *dicPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject stringByAppendingPathComponent:@"dic.text"];
        //2. 将字典归档后直接保存到文件中
        [NSKeyedArchiver archiveRootObject:dic toFile:dicPath];
        
        //3. 把字典或数组转换为NSData，再保存到一个文件中
        // 转换成NSData
        NSData *dicData = [NSKeyedArchiver archivedDataWithRootObject:dic];
        //保存到文件中
        NSFileManager *file= [NSFileManager defaultManager];
        [file createFileAtPath:dicPath contents:dicData attributes:nil];
        
        //4. 解归档
        //直接从文件中解归档
        NSDictionary *undict = [NSKeyedUnarchiver unarchiveObjectWithFile:dicPath];
        NSLog(@"---dic2---%@",undict);
        
        //将文件转换为NSData，再从NSData接归档
        NSData *undicData = [file contentsAtPath:dicPath];
        NSDictionary *unxdict = [NSKeyedUnarchiver unarchiveObjectWithData:undicData];
        NSLog(@"---dic---%@",unxdict);
        ```
        

