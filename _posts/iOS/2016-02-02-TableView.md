---
layout: post
title: UITableView之静态单元格
category: iOS开发
tags: UITableView
description: UITableView
---

## 什么是静态单元格？
1. 顾名思义，就是静态的，写死的cell
2. UITableView中cell的两种类型
    1. Cynamic Prototypes (创建tableView默认的cell类型，这种是动态的)
    2. static cells (静态单元格)
3. 静态单元格的优势是：直接用Storyboard布局，不需要写代理方法。
4. 缺点就是布局固定。只常用于设置基本不需要动态修改的页面，如个人中心。

## 应用场景
1. 基本不需要动态修改的页面是静态单元格的主要应用场景
2. 这种界面一般都是需求变化是最少的了，可能几年的不变，撑死就在加几个cell而已。这种场景就特别适合静态单元格，它可以用来快速创建，减少不必要的代码。
3. 比如:设置/个人中心等固定页面

## 使用注意
1. 静态表格只能用在stroyBoard实现，xib中无法使用。

## 静态单元格操作步骤
1. 创建一个SB(Storyboard),在sb中拖入一个UITableViewController
2. 代码自定义一个继承自UITableViewController的控制器,并这这个类设置为SB中那个控制器的类,如下图:  
    ![图1](https://gitee.com/zhonghua123/blogimgs/raw/master/img/tableView1.png)
3. 然后将tableView的cell类型设置为Static Cells。
 ![图2](https://gitee.com/zhonghua123/blogimgs/raw/master/img/tableView2.png)
4. 详细设置
    1. 点击tableView,可以设置类型(plain/group)
    2. 点击selection,可以设置该section下cell的行数row/header/footer标题 
    3. 点击cell可以设置cell的属性,以及搭建内部子控件
    ![图2](https://gitee.com/zhonghua123/blogimgs/raw/master/img/tableView3.png)
5. 代码展示:
    1. 给SB中设置Storyboard ID 
    2. 代码加载
        
        ```javascript
        - (IBAction)selectAction:(id)sender {
        //1.在主资源包中拿到该sb
        UIStoryboard *sb = [UIStoryboard storyboardWithName:@"TestTableViewController" bundle:nil];
        //2. 拿到该sb中的控制器,根据设置的IdentifierID
        TestTableViewController *VC = [sb instantiateViewControllerWithIdentifier:@"Testvc"];
        [self presentViewController:VC animated:YES completion:nil];
        }
        ```
        
        ![图2](https://gitee.com/zhonghua123/blogimgs/raw/master/img/tableView4.png)


## 静态单元格的进阶使用
1. 场景：
    1. 一个界面面很简单我想使用静态单元格，但是呢，这个界面上有一个按钮或者其他View是悬浮的，那么此时怎么办呢？   
        ![图3](https://gitee.com/zhonghua123/blogimgs/raw/master/img/stasticx.png)
    2. 一般的解决方法如下： 
        1. 方法一： 不使用静态单元格了，用纯代码，在一个viewController的View上添加一个tableview，按钮添加在VC的view上，这样可以实现效果了
            1. 缺点： 不能使用静态单元格了，比较麻烦
        2. 方法二： 仍然使用静态单元格，但是呢，这个按钮呢，添加在keyWindow上，然后在`-(void)viewWillAppear:(BOOL)animated`和`-(void)viewWillDisappear:(BOOL)animated`这两个方法中控制它的隐藏和实现
            2. 缺点： 如果试一下就会发现，这样的话不友好，这个按钮出现的很突兀
2. 如何通过使用静态单元格实现上面的功能呢？ 
    1. 创建一个SB的ViewController，比如：TestViewController(TestViewController.sb/TestViewController.h/TestViewController.m)
    2. 在SB中拖入一个ViewController，然后勾选（instantiateViewController），设置类为TestViewController，接着可以创建一个类工厂创建方法
    3. 在这个SB中布局你的悬浮按钮
    4. 拖一个UIContainerView,布局，注意了这个View就是让你嵌入静态单元格的重要控件（！！！）
        1. 拖入这个控件后会自动segue一个控制器（ViewController）类型的，这说明：
            1. **这个控件会将一个控制器的View嵌入到这个UIContainerView所占的位置**
            2. **同时将这个控制器加入到被嵌入View对应控制器的childViewControllers栈中去**
            3. 说白了其实就是父子控制器：（代码实现）
                1. 创建一个VC控制器，在VC控制器上添加悬浮控件
                2. 将VC控制器的某一块view用某个子控制器的View来展示
                    1. 创建子控制器，将子控制器添加到VC控制器中（[self addChildViewController:]）
                    2. 将子控制器的View添加到当前VC的view中，然后布局
    5. 删除UIContainerView的segue自带的那个VC
    6. 在这个SB中拖入一个tableViewController，当然同时也创建对应的代码控制器跟上面讲的静态单元格的创建一样（TestTableViewController）
    7. 将TestViewController.SB中对应的tableViewController设置为TestTableViewController类型，注意了，这里要设置一个StoryboardID(TestTableViewController),因为一个SB中只能有一个控制器可以勾选箭头，其他的控制器，加载寻找都要通过StoryboardID找到。
    8. 将SB中对应的TestTableViewController设置为静态单元格，然后布局
    9. 此时右击之前那个布局的UIContainerView选择ViewDidLoad，脱线到TestTableViewController，选择Embed（嵌入）
    10. 点击那个segue设置一个identifier
    11. [demo地址](https://github.com/zhoghua123/staticAdviceUser)

