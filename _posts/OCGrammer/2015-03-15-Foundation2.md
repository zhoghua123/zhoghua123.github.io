---
layout: post
title: Foundation框架之-集合类
category: OC语法
tags: OC语法
description: Foundation框架
---
##  数组
### NSArray
1. OC数组不能存放nil值
2. OC数组只能存放OC对象、不能存放非OC对象类型，比如int、struct、enum等,也不能存放nil
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
8. NSArray排序

    ```
       //方法一:
    NSArray *arr = @[@10, @20, @5, @7, @15];
    NSLog(@"排序前: %@", arr);
    // 注意: 想使用compare方法对数组中的元素进行排序, 那么数组中的元素必须是Foundation框架中的对象, 也就是说不能是自定义对象
    NSArray *newArr = [arr sortedArrayUsingSelector:@selector(compare:)];
    NSLog(@"排序后: %@", newArr);

    //方法2:自定义对象排序
    Person *p1 = [Person new];
    p1.age = 10;
    Person *p2 = [Person new];
    p2.age = 20;
    Person *p3 = [Person new];
    p3.age = 5;
    Person *p4 = [Person new];
    p4.age = 7;
    NSArray *arr = @[p1, p2, p3, p4];
    NSLog(@"排序前: %@", arr);
    // 按照人的年龄进行排序
    // 不能使用compare:方法对自定义对象进行排序
//    NSArray *newArr = [arr sortedArrayUsingSelector:@selector(compare:)];
    
    // 该方法默认会按照升序排序
    NSArray *newArr = [arr sortedArrayWithOptions:NSSortStable usingComparator:^NSComparisonResult(Person *obj1, Person *obj2) {
        // 每次调用该block都会取出数组中的两个元素给我们
        return obj1.age > obj2.age;
        //降序
//        return obj1.age < obj2.age;
    }];
    NSLog(@"排序后: %@", newArr);
    ```   
8. NSrray的读写
    1. 其实如果将一个数组写入到文件中之后, 本质是写入了一个XML文件
    2. 在iOS开发中一般情况下我们会将XML文件的扩展名保存为plist
    3. writeToFile只能写入数组中保存的元素都是Foundation框架中的类创建的对象, 如果保存的是自定义对象那么不能写入
    4. 应用:
        
        ```
        // 1.将数组写入到文件中
        NSArray *arr = @[@"lnj", @"lmj", @"jjj"];
        BOOL flag = [arr writeToFile:@"/Users/xiaomage/Desktop/abc.plist" atomically:YES];
        NSLog(@"flag = %i", flag);
        
         // 2.从文件中读取一个数组
        NSArray *newArray = [NSArray arrayWithContentsOfFile:@"/Users/xiaomage/Desktop/abc.plist"];
        NSLog(@"%@", newArray);
        ```
 
### NSMutableArray
1. NSArray的子类,继承自NSArray
2. NSMutableArray为可变数组,NSArray为不可变数组
3. 创建
    
    ```
     //创建空数组
    NSMutableArray *array0 = [NSMutableArray array];
    //创建2个元素的数组
    NSMutableArray *array = [NSMutableArray arrayWithObjects:@"rose", @"jim", nil];
    NSMutableArray *array1 = [NSMutableArray arrayWithArray:array];
    ```
4. 添加元素
    
    ```
     [array addObject:[[Person alloc] init]];
    [array addObject:@"jack"];
    ```
5. 删除元素
    
    ```
    // 删除指定的对象
     [array removeObject:@"jack"];
    [array removeObjectAtIndex:0];
    // 删除全部元素
    [array removeAllObjects];
    // 错误写法
    // [array addObject:nil];
    ```
6. 常见错误
    1. `@[]` 只创建不可变数组`NSArray`
    
        ```
        //NSMutableArray *array = @[@"jack", @"rose"];
        ```
    2. 不可变数组的添加
        
        ```
        //NSArray *array = @[@"jack", @"rose"];
        // [array addObject:@"jim"];
    
        ```

## 字典
### NSDictionary
1. 里面存储的东西都是键值对

    ```
     key ----> value
     索引 ----> 文字内容
    ``` 
2. 字典的value只能存放OC对象、不能存放非OC对象类型，比如int、struct、enum等,也不能存放nil
3. 创建

    ```
    //一个键值对
     NSDictionary *dict = [NSDictionary dictionaryWithObject:@"jack" forKey:@"name"];
    //由多个键值对创建一个字典
     NSArray *keys = @[@"name", @"address"];
     NSArray *objects = @[@"jack", @"北京"];
     NSDictionary *dict1 = [NSDictionary dictionaryWithObjects:objects forKeys:keys];
    //value,key
     NSDictionary *dict2 = [NSDictionary dictionaryWithObjectsAndKeys:
     @"jack", @"name",
     @"北京", @"address",
     @"32423434", @"qq", nil];
    //快捷创建方式
    NSDictionary *dict3 = @{@"name" : @"jack", @"address" : @"北京"};
    ```
4. 字典的取值
    
    ```
     id obj = [dict objectForKey:@"name"];
     id obj1 = dict[@"name"];
    ```
5. 返回的是键值对的个数/返回字典的所有key
    
    ```
     // 返回的是键值对的个数
    NSLog(@"%ld", dict.count);
    //返回字典的所有key
    NSLog(@"%ld",dict.allKeys);
    ```

### NSMutableDictionary
1. NSDictionary的子类,继承自NSDictionary
2. NSMutableDictionary为可变字典,NSDictionary为不可变字典
3. 无序
4. key唯一,value可以不唯一
5. 应用

    ```
     //1.创建一个空的可变字典
    NSMutableDictionary *dict = [NSMutableDictionary dictionary];
    //2. 添加键值对
    [dict setObject:@"jack" forKey:@"name"];
    [dict setObject:@"北京" forKey:@"address"];
    //会覆盖jack
    [dict setObject:@"rose" forKey:@"name"];
    
    //3. 移除键值对
     [dict removeObjectForKey:@"name"];
    ```
6. 注意
    1. 可变字典不可以使用快速创建方法
        
        ```
       // NSMutableDictionary *dict = @{@"name" : @"jack"};
       //实际是不可变的,会报错
       [dict setObject:@"rose" forKey:@"name"];
        ```
    2. 字典不允许有相同的key，但允许有相同的value（Object）
7. 字典的遍历
    
    ```
    // 字典的无序的
    NSDictionary *dict = @{
    @"address" : @"北京",
    @"name" : @"jack",
    @"name2" : @"jack",
    @"name3" : @"jack",
    @"qq" : @"7657567765"};
    
    //第一种遍历
    NSArray *keys = [dict allKeys];
    for (int i = 0; i<dict.count; i++)
    {
        NSString *key = keys[i];
        NSString *object = dict[key];


        NSLog(@"%@ = %@", key, object);
    }

    //第二种遍历
     // 如何通过forin遍历字典, 会将所有的key赋值给前面的obj
    for (NSString *key in dict) {
//        NSLog(@"%@", key);
        NSString *value = dict[key];
        NSLog(@"key = %@, value = %@", key, value);

    }
    //第三种遍历
    [dict enumerateKeysAndObjectsUsingBlock:
     ^(id key, id obj, BOOL *stop) {
         NSLog(@"%@ - %@", key, obj);
         
         // *stop = YES;
     }];
    ```
8. 字典数组的混合使用
    
    ```
        NSArray *persons = @[
    @{@"name" : @"jack", @"qq" : @"432423423", @"books": @[@"5分钟突破iOS编程", @"5分钟突破android编程"]},
    @{@"name" : @"rose", @"qq" : @"767567"},
    @{@"name" : @"jim", @"qq" : @"423423"},
    @{@"name" : @"jake", @"qq" : @"123123213"}
    ];

     NSDictionary *jim = persons[2];
    NSString *bookName = persons[0][@"books"][1];
    NSLog(@"%@", bookName);
    NSArray *array = persons[0][@"books"];
    // 先取出1位置对应的字典
    // 再取出字典中qq这个key对应的数据
    NSLog(@"%@", persons[1][@"qq"]);
    
    ```
9. 字典文件读写

    ```
    NSDictionary *dict = @{@"name":@"lnj", @"age":@"30", @"height":@"1.75"};
    // XML 扩展名plist
    [dict writeToFile:@"/Users/xiaomage/Desktop/info.plist" atomically:YES];
    
    // 注意: 字典和数组不同, 字典中保存的数据是无序的
    NSDictionary *newDict = [NSDictionary dictionaryWithContentsOfFile:@"/Users/xiaomage/Desktop/info.plist"];
    NSLog(@"%@", newDict);
    
    
    NSArray *arr = @[@10, @20, @30, @5];
    [arr writeToFile:@"/Users/xiaomage/Desktop/abc.plist" atomically:YES];
    ```
10. OC与Java
    
    ```
     OC                Java
    NSArray --->      (Array)List
    NSSet --->        (Hash)Set
    NSDictionary ---> (Hash)Map
    ```
    
## Set
### NSSet
1. NSSet和NSArray的对比
    1. 共同点
        1. 都是集合，都能存放多个OC对象
        2. 只能存放OC对象，不能存放非OC对象类型（基本数据类型：int、char、float等，结构体，枚举）,也不能放nil
        3. 本身都不可变，都有一个可变的子类
    2. 不同点
        1. NSArray有顺序，NSSet没有顺序
2. 基本应用 
    
    ```
      //1. 创建
    //创建一个空的set
    NSSet *s = [NSSet set];
    //创建一个有元素的set
    NSSet *s2 = [NSSet setWithObjects:@"jack",@"rose", @"jack2",@"jack3",nil];
    
    //2. 随机拿出一个元素
    NSString *str =  [s2 anyObject];
    ```

### NSMutableSet

1. NSSet的子类,可变的
2. 基本应用
    
    ```
     NSMutableSet *s = [NSMutableSet set];
    // 添加元素
    [s addObject:@"hack"];
    // 删除元素
    // [s removeObject:<#(id)#>];
    ```


