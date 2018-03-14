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
    ![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/tableView1.png)
3. 然后将tableView的cell类型设置为Static Cells。
 ![图2](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/tableView2.png)
4. 详细设置
    1. 点击tableView,可以设置类型(plain/group)
    2. 点击selection,可以设置该section下cell的行数row/header/footer标题 
    3. 点击cell可以设置cell的属性,以及搭建内部子控件
    ![图2](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/tableView3.png)
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
        
        ![图2](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/tableView4.png)



