---
layout: post
title: 静态库
category: iOS开发
tags: 静态库
description: 静态库
---

## 静态库的介绍
1. 什么是库?
    1. 库是程序代码的集合，是共享程序代码的一种方式
2. 根据源代码的公开情况，库可以分为2种类型
    1. 开源库 
        1. 公开源代码，能看到具体实现
        2. 比如SDWebImage、AFNetworking
    2. 闭源库
        1. 不公开源代码，是经过编译后的二进制文件，看不到具体实现
        2. 主要分为：静态库、动态库
3. 静态库和动态库
    1. 静态库和动态库的存在形式
        1. 静态库：.a 和 .framework
        2. 动态库：.dylib 和 .framework
    2. 静态库和动态库在使用上的区别
        1. 程序的运行
            1. 程序->编译->链接->运行
            2. 链接： 把所有的二进制文件组合在一起形成可执行性文件
        2. 静态库：
            1. 链接时，静态库会被完整地复制到可执行文件中，被多次使用就有多份冗余拷贝
        3. 动态库：
            1. 链接时不复制，程序运行时由系统动态加载到内存，供程序调用，系统只加载一次，多个程序共用，节省内存
            2. 需要注意的是：项目中如果使用了自制的动态库，不能被上传到AppStore
        4. 静态库与动态库加载如下图：
          
           ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai01.png) 

## .a静态库的制作

1. 直接制作.a静态库
    1. 创建项目时,选择Framework&Library-->Static Library
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai02.png)
    2. 编写核心代码
    3. 编译静态库前,将头文件暴露出去
        1. TARGETS -> build Phases->copy Files
        2. 注意了，这一步其实没必要，因为Xcode会默认将这个项目的所有.文件暴露在这里了。
    4. 编辑真机静态库
        1. 选择当前的设备为通用真机设备：Generic iOS Device
        2. command +B 编译一下即可
        3. 在改项目的products目录找到`lib静态库名.a`，然后右击show in finder 找到这个.a 文件，复制出来即可
            1. 此时会发现还有一个include文件，里面包含的就是这个.a对应的头文件.h
        4. 这个就是真机设备的.a静态库
    5. 编译模拟器静态库                
        1. 选择任一款模拟设备
        2. 其他同真机静态库一样，获取到模拟器静态库.a
    6. 静态库整理：
        1. 新建一个文件夹，命静态库的名字
        2. 将include的.h文件都复制到这个文件中
        3. 将.a文件也复制到这里
    7. 创建一个项目,测试使用静态库即可.
    8. 问题抛出：到这里，我们是制作了两个静态库，一个是真机环境下做的额，另一个是模拟器环境下做的，那么我们使用时会发现，真机设备下只能用真机.a ，模拟器下的只能用模拟器.a，否则就会报错，怎么办呢？
2. 静态库的架构问题
    1. 设备的CPU架构
        1. 不同的设备使用的CPU是不同的
        2. CPU的不同造成使用的CPU架构(指令集)不同
    2. 静态库有其支持的CPU架构
        1. 如果静态库在不支持的CPU架构上运行就会报错
    3. 模拟器使用的CPU架构
        1. iPhone4s-iPhone5 : i386
        2. iPhone5s-iPhone6plus : x86_64
    4. 真机使用的CPU架构
        1. iPhone3gs-iPhone4s : armv7
        2. iPhone5-iPhone5c : armv7s
        3. iPhone5s-最新 : arm64
        4. 唯一特殊:只要支持armv7的静态库可以在armv7s上正常运行
    5. 如何查看一个静态库到底支持哪些架构呢？ 
        1. 打开终端，cd 静态库文件目录
        2. `lipo -info 静态库` 
    6. 合并静态库
        1. 如果我们想要制作一个静态库，同时支持模拟器也支持真机，该怎么办呢？
        2. 那就需要将模拟器静态库跟真机静态库合并成一个静态库
        3. lipo -create 静态库1 静态库2 -output 合并的静态库
        4. 如下：
            
            ```
            lipo -create /Users/mac/Desktop/合并静态库/MJExtension/libMJExtension.a /Users/mac/Desktop/合并静态库/MJExtension2/libMJExtension.a -output /Users/mac/Desktop/合并静态库/合并后的静态库/MJExtension
            ```
        5. 合成后文件路径如下：
            
            ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai03.png)

