---
layout: post
title: iOS如何获取App的IPA
category: 工具
tags: IPA
description: IPA
---

1. 首先 去Mac上的App Store下载Apple Configurator 2。
2. 然后把iphone连接上Mac，点击Apple Configurator 2 菜单中->账户->登陆（用连接设备的Apple ID）
3. 所有设备->选中当前iPhone->添加->应用，找到您想要ipa的那个应用->添加
4. 因为你手机中已经存在了当前应用，所以会提示，该应用已经存在， 是否需要替换？**此时，不要点任何按钮！不要点任何按钮！不要点任何按钮！**
5. 不要操作Apple Configurator 2，让它保持上图的状态，然后打开Finder前往文件夹，或者直接快捷键`command+shift+G`,
    1. 并输入下面路径
        
        ```
        ~/Library/Group Containers/K36BKF7T3D.group.com.apple.configurator/Library/Caches/Assets/TemporaryItems/MobileApps/
        ```
    2. 点击前往，打开ipa包所在文件。将ipa文件copy出来。
6. 这时候别忘了点击Apple Configurator 2窗口中的停止，你会发现刚才目录下的文件也消失了
7. 拿到ipa文件后,拖入**iOS Images Extractor**(mac一款软件)即可


