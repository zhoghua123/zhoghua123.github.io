---
layout: post
title: OC语法之-基本语法
category: OC语法
tags: OC语法
description: OC语法
---

## 如何快速创建代码练兵场
1. 创建一个macOS命令行工具项目
    1. 打开xcode->create->macOS->Command Line Tool即可
2. 在命令行工具项目中创建target
    1. 在当前的命令行项目下,点击xcode工具栏File->New->Target...->Application->Command Line Tool即可
3. 如何切换各个target云信
    1. 该命令行项目中有很多target,每个target之间互不影响
    2. 点击停止程序右边的target名,就会有target列表,选择你想要运行的那个target,然后运行即可
    
## OC简介
* C语言的基础上，增加了一层最小的面向对象语法
* 完全兼容C语言
* 可以在OC代码中混入C语言代码，甚至是C++代码
* 可以使用OC开发Mac OS X平台和iOS平台的应用程序

## oc语法预览
* 关键字
    * 基本上oc所有的关键字都已@开头
    * 举例   
     
```
@interface、@implementation、@end 
@public、@protected、@private、@selector 
@try、@catch、@throw、@finally  
@protocol、@optional、@required、@class
@property、@synthesize、@dynamic           
非@开头的关键字:
self、super、id、_cmd、__block、__strong、__weak、
```

* 字符串
    * 字符串以@开头:`@"字符串"`
* 数据类型
    * 基本数据类型: char/int/double仅仅比C语言多了一个BOOL(YES/NO) 
* nil 相当于C语言中的NULL,即0
* 基本语句:循环语句,条件语句,跟C一样   

## OC程序的开发过程    
* 跟C语言类似:.m文件(源文件)->编译.o文件(目标文件)->连接a.out(可执行文件)
* 指令执行
    * 编译: cc -c main.m
    * 连接: cc main.o –framework Foundation
    * 运行: ./a.out 
* NSLog与printf的区别   
    * NSLog接收OC字符串作为参数，printf接收C语言字符串作为参数
    * NSLog输出后会自动换行，printf输出后不会自动换行
    * 使用NSLog需要#import <Foundation/Foundation.h>
    * 使用printf需要#include <stdio.h>
* **#import**的作用    
    * 跟#include一样，用来拷贝某个文件的内容
    * 可以自动防止文件内容被拷贝多次，也就是头文件中不用加入下面的预处理指令了  
    
      ```
      #ifndef 	_STDIO_H_
      #define	_STDIO_H_
      #endif
      ```
* BOOL的使用
    * BOOL类型的本质`typedef signed char BOOL;`
    * BOOL类型的变量有2种取值：YES、NO
    
```
#define YES (BOOL)1
#define NO  (BOOL)0
```

* 多.m文件开发
    * 编写3个文件:main.m/one.h/one.m 
    * 终端指令:
        * 编译：cc –c main.m one.m
        * 链接：cc main.o one.o –framework Foundation
        * 运行：./a.out
        
```javascript
main.m文件

#import "one.h"
int main()
{
    test();
    return 0;
}

one.h文件
void test();

one.m文件
#import <Foundation/Foundation.h>
void test()
{
    NSLog(@"调用了test函数");
}
```






