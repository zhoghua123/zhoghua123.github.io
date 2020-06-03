---
layout: post
title: CocoaPods的所有问题
category: 工具
tags: CocoaPods
description: CocoaPods
---

> 这篇文章主要解决Cocoapods使用过程中出现的一些蛋疼问题,当然也会有安装过程

## Cocoapods的安装

1. 升级ruby环境

    ```
    终端输入：gem update --system

    此时若出现,没权限升级Ruby的提示
    
    这是因为你没有权限去升级Ruby
    
    这时应该输入： sudo gem update --system
    
    此时会出现
    
    输入密码
    
    接下来输入密码，注意：输入密码的时候没有任何反应，光标也不会移动，你尽管输入就是了，输完了直接回车。
    
    等一会如果出现
    
    升级Ruby成功
    
    恭喜你，升级Ruby成功了。
    
    查看gem版本命令: gem -v
    
    查看ruby版本: ruby -v
    ```
2. 更换Ruby镜像

    ```
    首先移除现有的Ruby镜像

    终端输入：gem sources --remove https://rubygems.org/
    
    然后添加国内最新镜像源（淘宝的Ruby镜像已经不更新了）
    
    终端输入：$ gem sources -a https://gems.ruby-china.org/
    
    执行完毕之后输入gem sources -l来查看当前镜像
    
    终端输入：$ gem sources -l
    
    如果结果是
    
    *** CURRENT SOURCES ***
    
    https://gems.ruby-china.org/
    
    说明添加成功

    ```
3. 安装CocoaPods
    
    ```
    接下来开始安装

    终端输入：$ sudo gem install cocoapods
    
    如果出现安装错误提示
    
    因为: 苹果系统升级 OS X EL Capitan 后(10.11后苹果升级了安全策略)改为: $ sudo gem install -n /usr/local/bin cocoapods
    
    安装成功如下：
    
    安装成功(此时cocoapods已经成功配置了)
    
    初始化第三方库信息
    
    就是将CocoaPods里面的第三方库全部下载到mac本地（PS：这个过程是漫长的，要有耐心）
    
    终端输入：$ pod setup
    
    然后你会看到出现了Setting up CocoaPods master repo，卡住不动了，说明Cocoapods在将它的信息下载到 ~/.cocoapods里。
    
    你可以command+n新建一个终端窗口，执行cd ~/.cocoapods/进入到该文件夹下，然后执行du -sh *来查看文件大小，每隔几分钟查看一次，这个目录最终大小是900多M（我的是930M）
    
    当出现Setup completed的时候说明已经完成了。
    
    其他常用命令: 
    
    查看当前CocoaPods的版本: pod --version
    
    升级CocoaPods的版本: gem install cocoapods --pre
    
    注意：以上所有的步骤都是为这台电脑配置CocoaPods环境，以下才是真正的把CocoaPods安装到你的工程里面。
    ```

## CocoaPods在项目中使用

1. 常用命令: 
    
    ```
    1. 更换repo镜像为国内服务器(原路径出现问题时才需要替换,基本不用)
    这个镜像源指的就是第三方库的下载资源路径,默认在:https://github.com/CocoaPods/Specs.git  
     
    //移除原来路径
    pod repo remove master
    
    //添加新的路径
    pod repo add master source https://gitcafe.com/akuandev/Specs.git
    
    2. 更新本地资源库
    在pod setup 之后,cocoapods会从https://github.com/CocoaPods/Specs.git 路径下载所有的第三方库到本地,然而如果长时间不更新,本地资源库中的第三方仍然是老版本的
    
    pod repo update

    3. 将项目中Podfile文件中声明的第三方库下载到项目中
    
    pod install
    
    4. 查找第三方库
    
        在终端中输入pod search +第三方框架名（或一部分），就能查看cocoapods中是否有这个第三方
        
    pod search ...
        
    ```
2. 使用步骤
    
    ```
    进入终端：
    
    进入项目文件
    
    cd （将项目的文件夹拖过来得到地址）
    
    验证当前文件路径: ls -1
    
    创建profile文件
    
    vim Podfile（打开Podfile，目的是在这个文件中说清楚要用那些框架）
    
    点击键盘的i，进入insert模式，再往里面输入
    
    platform :ios, '8.0'
    target '你的项目名称' do
    pod 'AFNetworking', '~> 3.0'
    end
    
    点击esc 退出insert，输入 :wq 回车，即可
    
    此时可以发现在项目目录下多了一个Podfile的文件，请注意这个文件必须与.xcodeproj在同一目录下。


    输入pod install 回车

    ```

## 常见错误
1.  RuntimeError - [Xcodeproj] Unknown object version.
    
    ```
    RuntimeError - [Xcodeproj] Unknown object version.
    /Users/ocean/.rvm/gems/ruby-2.2.2@global/gems/xcodeproj-1.3.0/lib/xcodeproj/project.rb:217:in initialize_from_file' /Users/ocean/.rvm/gems/ruby-2.2.2@global/gems/xcodeproj-1.3.0/lib/xcodeproj/project.rb:102:inopen'
    /Users/ocean/.rvm/gems/ruby-2.2.2@global/gems/cocoapods-1.0.1/lib/cocoapods/installer/analyzer.rb:695:in block (2 levels) in inspect_targets_to_integrate' /Users/ocean/.rvm/gems/ruby-2.2.2@global/gems/cocoapods-1.0.1/lib/cocoapods/installer/analyzer.rb:694:ineach'
    ........
    ```
    
    1. 原因：
        1. 就是你的xcode版本和现在CocoaPods 的版本问题不匹配  比如你用xcode8 你的cocoaPods 版本是1.0.1 . 就有可能出现此类问题。因为xcode8需要cocoaPods1.1.0.
    2. 解决方法：
        1. 就是更新cocoaPods的版本 ，在终端执行如下命令: `gem install cocoapods --pre`，然后在相对应podfile文件所在路径下执行`pod install` 即可。
2. CocoaPods 到 1.8.4问题：
    1. 今天升级 CocoaPods 到 1.8.4 版本，但是随即问题就来了, 执行 pod install 下载库时，出现错误
        
        ```
        [!] CDN: trunk URL couldn't be downloaded: https://cdn.cocoapods.org/all_pods_versions_a_7_5.txt, error: execution expired
        ```
    2. 解决办法：
        1. 查看ropo源：`pod repo list`
            
            ```
            master
            - Type: git (master)
            - URL:  https://github.com/CocoaPods/Specs.git
            - Path: /Users/mac/.cocoapods/repos/master
            
            trunk
            - Type: CDN
            - URL:  https://cdn.cocoapods.org/
            - Path: /Users/mac/.cocoapods/repos/trunk
            
            2 repos
            ```
        2. 删除trunk源:`pod repo remove trunk`
        3. 就是在 Podfile 加上 `source ‘https://github.com/CocoaPods/Specs.git’`, 因为现在默认是trunk源
        
            ```
            source 'https://github.com/CocoaPods/Specs.git'
            target 'xxxxx' do
            use_frameworks!
            pod 'AMapLocation'
            pod 'AMapNavi'
            end
            ```
    3. 如何添加一个镜像源
        1. 使用场景：当别人的项目Podfile文件中的source不是`https://github.com/CocoaPods/Specs.git`怎么办呢？
        2. 添加对应的镜像源
            1. 我的镜像源列表
                
                ```
                macdeMacBook-Pro:~ mac$ pod repo list
                
                master
                - Type: git (master)
                - URL:  https://github.com/CocoaPods/Specs.git
                - Path: /Users/mac/.cocoapods/repos/master
                
                1 repo
                ```
            2. 别人项目Podfile的source：`https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git`
            3. 添加这个镜像源：
                
                ```
                pod repo add edu-git-cocoapods-specs https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git
                ```
            4. 添加后的repo list 如下
                
                ```
                macdeMacBook-Pro:~ mac$ pod repo list

                edu-git-cocoapods-specs
                - Type: git (unknown)
                - URL:  https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git
                - Path: /Users/mac/.cocoapods/repos/edu-git-cocoapods-specs
                
                master
                - Type: git (master)
                - URL:  https://github.com/CocoaPods/Specs.git
                - Path: /Users/mac/.cocoapods/repos/master
                
                2 repos
                ```

