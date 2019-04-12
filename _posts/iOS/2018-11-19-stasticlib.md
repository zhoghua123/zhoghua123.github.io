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

### 直接制作.a静态库
1. 创建项目时,选择Framework&Library-->Static Library
    ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai02.png)
2. 编写核心代码
3. 编译静态库前,将头文件暴露出去
    1. TARGETS -> build Phases->copy Files
    2. 注意了，这一步其实没必要(很老的Xcode这里需要)，因为Xcode会默认将这个项目的所有.文件暴露在这里了。
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

### 静态库的架构问题
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
    3. `lipo -create 静态库1 静态库2 -output 合并的静态库`
    4. 如下：
        
        ```
        lipo -create /Users/mac/Desktop/合并静态库/MJExtension/libMJExtension.a /Users/mac/Desktop/合并静态库/MJExtension2/libMJExtension.a -output /Users/mac/Desktop/合并静态库/合并后的静态库/MJExtension
        ```
    5. 合成后文件路径如下：
        
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai03.png)
7. release 和debug静态库的区别
    1. 从上面可操作可以看出，我们是在debug环境下生成的静态库。如果要想打release的静态库，可以先把Xcode的环境设置为release（至于怎么设置，百度），然后在生成静态库
    2. 静态库有4种形式
        1. 真机Debug版本
        2. 真机Release版本
        3. 模拟器Debug版本
        4. 模拟器Release版
    3. release版本的代码更加简洁，因此最好打包release版本。      
8. **注意：**
    1. 如果静态库中包含了分类Category，默认是不会把分类的.m文件链接进来的，在使用静态库的工程中会报”方法找不到“的错误（unrecognized selector sent to instance）
        1. 解决办法：targets ->build settings ->other->other linker Flags->双击后边添加-ObjC即可
    2. 静态库是不能把资源文件bundle打进去的，制作静态库时，不能把.bundle拖进去，制作完成后，把.a/.h和bundle放到一个新的文件夹即可。
    3. 从上面我们知道，编译静态库时，每次编译只会生成一种架构
        1. 但是由于模拟器iPhone5 跟iPhone5s的CPU架构不同，一个是i386，一个是x86_64，那怎么办呢？ **难道模拟器也要编译2个静态库，在合并吗？ 这个是不需要的**
        2. 在编译静态库之前，target-> build settings -> 搜索build -> build Active architetcture Only -> Debug ->设置为NO
        3. 而且在设置之前我们可以看到，这个地方release默认为NO ，而Debug默认为YES
            ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai04.png)
    
###  在项目中直接制作静态库
1. 优点：边写项目，边可以调试静态库。
2. 实际开发中不是先在项目中写好文件，然后拖出来，制作静态库的，而是在项目中直接制作
3. 在项目中,按照下面步骤即可
    1. 点击项目-->左下角+号
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai05.png)    
    2. 选择希望开发的静态库类型(.a/.framework)
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai06.png)  
    3. 创建静态库后，项目会多出以下内容：（比如静态库的名字为ZHExtension）
        1. 项目target会多出一个ZHExtension，并且图标为一个小房子
        2. 项目中会多出一个ZHExtension文件夹，而且里面默认有ZHExtension.h/.m文件
        3. 文件Products目录下回多出一个libZHExtension.a红色，文件，红色说明还没有编译生成静态库
        4. 如下图
            ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai07.png)  
    4. 编写将要打包的代码
        1. 所有你需要打成静态库的代码，都写在ZHExtension那个文件中
        2. 写好代码后，你在项目中运行，会发现报错
        3. 那是因为，你写的ZHExtension文件中的代码，是一个静态库中，而当前这个项目并没有引用到这个静态库，需要我们手动把这个静态库加载到当前项目。
            ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai08.png) 
    5. 编译写好的静态库
        1. 将项目中的target选择为静态库ZHExtension，然后command +B编译即可。
        2. 至于生成通用架构的静态库，方法跟之前一样。
            ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai09.png) 

## .framework静态库的制作
###  直接制作.framework
1. 创建项目时,选择Framework&Library-->Framework
2. 默认创建的项目中有一个主头文件,可以将其他头文件在该头文件中import一下
3. 编写核心代码
    1. 刚建好这个静态库后，仅仅只有一个主头文件
    2. 需要自己添加需要打包的.m/.h文件
4. 编译库前,将头文件暴露出去
    1. Targets-> Build Phases ->Headers
    2. 会发现这里有3个类型
        1. public： 这个framework需要暴露的.h文件，默认只有主头文件
        2. Private：private，私有头文件，就是不需要暴露的
        3. project：这个库中所有的.h文件
    3. 如何将project中的.h添加到public中呢？ 直接拖拽即可。
5. 默认情况直接编译的库为动态库,可以修改配置改成静态库
    1. Framework默认为动态库，需要手动设置为静态库
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai10.png) 
6. 选择模拟器和真机分别编译
7. 打开Products，然后show in finder，即可看到.framework库
8. 合并真机模拟器通用静态库
    1. 将真机.framework中的二进制文件、模拟器.framework中的二进制文件合并成一个通用的二进制文件（合并方法，跟.a一样）
    2. 然后将这个通用的二进制文件，替换掉真机或者模拟器.framework中的二进制文件即可。

###  在项目中直接制作.framework静态库
1. 开始创建步骤和.a一样，只是选择framework方式
2. 创建后项目中新增的文件如下图:(ZHRefresh)
    ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai11.png) 
    
    1. 注意，默认ZHRefresh文件中只有ZHRefresh.h跟一个info.plist文件，没有.m文件。
    2. 这个.h文件主要是用于其他.h文件导入到这个文件中的
3. 编写将要打包的代码
     1. 所有你需要打成静态库的代码，都写在ZHRefresh那个文件夹中
     2. 而且将所有的.h文件都导入ZHRefresh.h
     3. 注意，framework，不需要手动导入项目库，Xcode会自动导入
     3. 设置需要暴露的文件
        1. 在Targets 中选择，ZHRefresh，然后设置暴露头文件。
         ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai12.png)
     4. 设置当前的frame为静态库
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai13.png)
4. 编译写好的静态库
    1. 将项目中的target选择为静态库ZHRefresh，然后command +B编译即可。
    2. 至于生成通用架构的静态库，方法跟之前一样。
5. 注意：
    1. .framework使用时，import导入必须用<>导入


## 静态库总结
1. .framework 为什么即时动态库又是静态库？
    1. 系统的.framework是动态库
    2. 我们自己建立的事静态库（默认是动态库，但是需要改成静态库）
2. 多文件处理
    1. 如果静态库需要暴露出来的 .h 比较多，可以考虑创建一个主头文件，一般 主头文件 和 静态库 同名),在主头文件中包含所有其他需要暴露出来的 .h 文件
3. 无论是 .a 静态库还是 .framework 静态库，最终需要的都是：二进制文件 + .h + 其它资源文件
4. .a 和 .framework 的使用区别
    1. .a 本身是一个二进制文件，需要配上 .h 和 其它资源文件 才能使用
    2. .framework 本身已经包含了 .h 和 其它资源文件，可以直接使用
5. 图片资源的处理
    1. 如果静态库中用到了图片资源，一般都放到一个bundle文件中，bundle名字一般跟 .a 或 .framework 名字一致
    2. bundle的创建：新建一个文件夹，修改扩展名为 .bundle 即可，右击bundle文件，显示包内容，就可以往bundle文件中放东西


## 实战问题整理：
1. 我明明已经设置了build Active architetcture Only -> Debug ->设置为NO了，但是我查看我打包的静态库，为何只支持arm64 没有armv7呢？ 或者模拟器只有x86_64，没有i386呢？
    1. 因为iOS11支持>iPhone 5s，你的静态库当前支持的系统，是>大于iOS11的，因此，静态库，只有arm64架构，同理模拟器只有x86_64架构
    2. 解决办法，在target中选择相应的静态库，然后在general中把最低支持系统设置的低一点，就行了
    3. 如下图：
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/jingtai14.png)
        
2. 如果当前打的静态库中使用了其他的静态库framework(注意不是.a),那么在你这个静态库`Target`下`（general->Linked Frameworks ...）`也要导入这几个使用的framework
    1. 注意：如果是`.a`的话，可以直接拖入到这个静态库文件中，如果不拖入，注意这里不能导入.a的静态库

## bundle资源文件的应用
1. 如果有代码里用到了image和xib或者sb文件，那么此时就要用到bundle了
2. 创建bundle
    1. 如下面6步骤
        ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/bundle01.png)
        
        1. 为何选择macOS ? 因为iOS环境下没有
    2. 命名（假设为testBundle），然后create
    3. 项目目录会多一个testBundle文件
2. 配置bundle
    1. 因为当时选择的事macOS，所以现在要设置为iOS类型的
    2. 要把enable bitcode设置为NO
    
    ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/bundle02.png)

3. 设置COMBINE_HIDPI_IMAGES 为NO
    ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/bundle03.png)

4. 在General中设置当前最低支持的iOS系统
5. 在项目的testBundle中添加相应的图片、xib、sb，然后项目选择为当前的bundle的Target编译即可，就能在项目的products文件下找到相应的bundle文件。
6. **注意：**
    1. 一定要在testBundle这个target下的Copy Bundle Resource中看看，相应的资源是否已经添加进去了。同理在打xxx.framework静态库的时候一定也要看看Compile Sources 、Headers 、Copy Bundle Resource文件下是否都添加了相应的文件。
    2. **如果静态库xxx.framework用到了这个bundle，注意一定要将这个bundle添加到该静态库的Copy Bundle Resource 中，这个很重要！！！！**。
        1. 如果没有添加，最后打包成的静态库xxx.framework中就不含testBundle.bundle文件
        2. 如何添加呢？ 
            1. 在项目的TARGETS列表中选择xxx这个静态库Target
            2. 选择Build Phases-> Copy Bundle Resources
            3. 点击+，选择当前项目下的Products文件下的testBundle，添加即可
            4. 这样打出来的静态库xxx.framework中，就包含testBundle.bundle文件了
            5. **但是注意了：编译xxx.framework静态库之前，必须先编译testBundle.bundle，否则就会报错**
    3. **项目中**一定也要把这个bundle添加到Copy Bundle Resource文件下，否则下面的代码找不到bundle  !!!!
        
        ```
        NSBundle *bundle = [NSBundle bundleWithPath:[[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"CMZHReadIDCardBundle.bundle"]];
        ```

### 加载bundle中的资源
1. 图片资源
    1. 代码
        
        ```
        //方法1：
        NSString *path = [[NSBundle mainBundle] pathForResource:@"myBundle" ofType:@"bundle"];
        NSBundle *bundle = [NSBundle bundleWithPath:path];
        NSString *file = [bundle pathForResource:@"pic" ofType:@"png"];
        UIImage *img = [UIImage imageWithContentsOfFile:file];
        
        //方法2：
        NSString *file2 = [[NSBundle mainBundle] pathForResource:@"myBundle.bundle/pic" ofType:@"png"];
        UIImage *img2 = [UIImage imageWithContentsOfFile:file2];
        
        //方法3：
        UIImage *img3 = [UIImage imageNamed:@"myBundle.bundle/pic"];
        ```
    2. xib、sb中使用图片：直接设置即可，因为打包bundle的时候xib、SB文件和图片资源文件在同一目录下.
2. 代码加载xib/sb
    1. xib
        
        ```
        //重写要加载的view的init方法

        - (instancetype)init{
            NSBundle *bundle = [NSBundle bundleWithPath:[[[NSBundle mainBundle] resourcePath] stringByAppendingPathComponent:@"CMZHReadIDCardBundle.bundle"]];
            if (self = [super initWithNibName:@"ShowIDCardInfoViewController" bundle:bundle]) {
            }
            return self;
        }

        ```
    2. sb：
        
        ```
        //新增类工厂方法
        +(instancetype)faceRegisterVC{
            NSString* bundlePath = [[NSBundle mainBundle] pathForResource:@"xxbundle" ofType:@"bundle"];
            NSBundle* resourceBundle = [NSBundle bundleWithPath:bundlePath];
            UIStoryboard *sb = [UIStoryboard storyboardWithName:@"FaceRegisterStoryboard" bundle:resourceBundle];
            return [sb instantiateViewControllerWithIdentifier:@"FaceRegisterStoryboard"];
        }
        ```


