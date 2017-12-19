---
layout: post
title: Foundation框架之-NSNumber/NSValue
category: OC语法
tags: OC语法
description: Foundation框架
---

## NSNumber
1. 作用: 我们知道OC的集合类(NSArray/NSSet/NSDictionary)是不允许存放非OC对象的,为了存放基本数据类型,就出现了NSNumber----将基本数据类型包装成OC对象
2. 基本使用
    
    ```
     //创建
    NSNumber *num = [NSNumber numberWithInt:10];
    //快速创建
    // 将各种基本数据类型包装成NSNumber对象
    //Int
    //包装
    @4;
    //还原
    [@4 intValue];
    //Double
    //包装
    @10.5;
    //还原
    [@10.5 doubleValue];
    //BOOL
    //包装
    @YES;
    //还原
    [@YES boolValue];
    //Char
    //包装
    @'A';
    //还原
    [@'A' charValue];
    
    //注意这个是: NSString对象
    @"A";
    
    //放入字典中
    NSDictionary *dict =  @{
    @"name" : @"jack",
    @"age" : num
    };
    //取出NSNumber
    NSNumber *num2 = dict[@"age"];
    //还原为基本数据类型
    int a = [num2 intValue];
    
    NSLog(@"%d" , a);
    ```

## NSValue
1. NSNumber之所以能够包装基本数据类型,就是因为继承了NSValue
2. NSNumber只能包装基本数据类型(int/double/char/BOOL)
3. NSValue可以包装任何数据类型,包括指针(对象)/结构体
4. 基本使用
    
    ```
      // 结构体--->OC对象
    CGPoint p = CGPointMake(10, 10);
    // 将结构体转为Value对象
    NSValue *value = [NSValue valueWithPoint:p];
    // 将value转为对应的结构体
     [value pointValue];
    NSArray *array = @[value ];
    
    ```

