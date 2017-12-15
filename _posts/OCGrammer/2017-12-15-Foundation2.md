---
layout: post
title: Foundation框架之-集合类
category: OC语法
tags: OC语法
description: Foundation框架
---
## NSArray
1. OC数组不能存放nil值
2. OC数组只能存放OC对象、不能存放非OC对象类型，比如int、struct、enum等
3. NSArray的创建
    
    ```
    // 这个array永远是空数组
    NSArray *array = [NSArray array]; // => @[];
    //创建一个数只有一个元素
    NSArray *array2 = [NSArray arrayWithObject:@"jack"];
    //创建一个数组有多个元素
    // nil是数组元素结束的标记
    NSArray *array3 = [NSArray arrayWithObjects:@"jack", @"rose", nil];
    // 快速创建一个NSArray对象
    NSArray *array4 = @[@"jack", @"rose", @"4324324"];
    
    ```
4. NSArray的元素个数
    
    ```
    NSLog(@"%ld", array3.count);
    ```
5. NSArray中元素的访问
    
    ```
     NSLog(@"%@", [array3 objectAtIndex:1]);
    //快速访问
    NSLog(@"%@", array3[0]);
    ```
7. 数组的遍历
    
    ```
    Person *p = [[Person alloc] init];
    NSArray *array = @[p, @"rose", @"jack"];
    
    //方式1:
    for (int i = 0; i<array.count; i++)
    {
        NSLog(@"%@", array[i]);
    }
    
    //方式2: for in
    // id obj代表着数组中的每一个元素
    for (id obj in array)
    {
        // 找出obj元素在数组中的位置
        NSUInteger i = [array indexOfObject:obj];
        NSLog(@"%ld - %@", i, obj);
        if (i==1)
        {
            break;
        }
    }
    
    //方式3:枚举
    // 每遍历到一个元素，就会调用一次block
    // 并且当前元素和索引位置当做参数传给block
    [array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop)
     {
         NSLog(@"%ld - %@", idx, obj);
         
         
         if (idx == 0)
         {
             // 停止遍历
             *stop = YES;
         }
         
     }];
    //原理如下:
    void (^myblock)(id, NSUInteger, BOOL *) = ^(id obj, NSUInteger idx, BOOL *stop)
    {
        NSLog(@"%ld - %@", idx, obj);


        if (idx == 0)
        {
            // 停止遍历
            *stop = YES;
        }
    };
    
    for (int i = 0; i<array.count; i++)
    {
        // 用来标记是否需要停止遍历
        BOOL isStop = NO;

        // 取出元素
        id obj = array[i];
        
        myblock(obj, i, &isStop);
        
        
        if (isStop)
        {
            break;
        }
    }
    ```

