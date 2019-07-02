---
layout: post
title: CollectionView的详细使用(一)
category: iOS开发
tags: CollectionView
description: CollectionView的详细使用
---

## tableView与collectionView比较
1. 相同点  
    1. 都是通过`datasource`和`delegate`驱动的，因此在使用的时候必须实现数据源与代理协议方法;  
    2. 性能上都实现了循环利用的优化。
2. 不同点  
    1. `UITableView`的`cell`是系统自动布局好的，不需要我们布局(垂直分布，一行一行排列)。但是`UICollectionView`的`cell`是需要自己布局的(每个`item`如何排布)。
        1. 因此在创建`UICollectionView`的时候必须传递一个布局参数，而且这个参数必须是`UICollectionViewLayout`的子类
            
            ```
            - (instancetype)initWithFrame:(CGRect)frame collectionViewLayout:(UICollectionViewLayout *)layout;
            ```
        2. 系统默认提供并实现了一个布局样式：流水布局(`UICollectionViewFlowLayout`)  
    2. `UITableViewController`的`self.view == self.tableview;`,但`UICollectionViewController`的`self.view != self.collectionView`
        1. `UICollectionViewController`层次结构：控制器`View `上面`UICollectionView`   
    3. `UITableView`的滚动方式只能是垂直方向， `UICollectionView`既可以垂直滚动，也可以水平滚动；    
    4. `UICollectionView`的`cell`只能通过注册来确定重用标识符。不能像`tableViewcell`一样`initWith...`    
    5. 并不能直接设置`cell`的高度/`section`之间距离的数据源方法,只能通过设置`layout`来实现  
3. **结论** : 换句话说，UITableView的布局是UICollectionView的flow layout布局的一种特殊情况，类比于同矩形与正方形的关系 
4. 代码举例： 
    
    ```
    CGRect frame = CGRectZero;
    UICollectionView *collection = [[UICollectionView alloc] init];
    collection.frame = frame;
    [self.view addSubview:collection];
    //运行报错:
    //reason: UICollectionView must be initialized with a non-nil layout parameter
    //意思:collctionView初始化时必须要传一个非空布局(layout)参数(parameter);
    // 因此初始化方法如下:
    UICollectionView *collection = [[UICollectionView alloc] initWithFrame:frame collectionViewLayout:[[UICollectionViewFlowLayout alloc] init]];
    ```

## UICollectionViewFlowLayout
1. 系统自定义布局,流水布局,即:像流水一样一行满了,排下一行
2. `UICollectionViewLayout`的子类
3. `CollectionViewcell`排布的样式是由`UICollectionViewLayout`决定的
4. 该类常用属性如下:     
//设置最小行间距   
 `minimumLineSpacing  `     
// 设置垂直间距   
 `minimumInteritemSpacing`       
**注意**:解析为何min ?    
情况1:    
    同一行有不同的size的cell.由于高度不同,那么最小行间距就是本行最高的cell距离下一行最高cell的间距    
   同理,同一列有不同size的cell,由于宽度不同,那么最大列间距就是本列最宽cell距离下一列最宽cell的间距    
![图1](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/coll1.png)  
情况2:    
cell的itemsize已经设置固定,行间距再固定,那么界面排版就冲突,因此设置最小间距   
![图2](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/collection2.png)  
//每个cell统一尺寸        
  `itemSize`    
     //预估cell的尺寸，ios8之后可以先去预估cell的尺寸，然后去自适应(与tableView相似)   
 `estimatedItemSize`   
    
``` 
   //一行代码足以,不需要向tableView2行代码
     layout.estimatedItemSize = CGSizeMake( 60, 60);
```
 //设置滚动方向（默认垂直滚动）   
   `scrollDirection `   
    //每一组头视图的尺寸。如果是垂直方向滑动，则只有高起作用；如果是水平方向滑动，则只有宽起作用。    
 `headerReferenceSize`      
  //每一组尾部视图的尺寸。如果是垂直方向滑动，则只有高起作用；如果是水平方向滑动，则只有宽起作用。     
`footerReferenceSize`       
   //每一组的内容缩进   
`sectionInset`    


## 代码举例:制作一个简单启动广告页

```   
AppDelegate中代码
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    UIWindow *window = [[UIWindow alloc] init];
    window.frame = [UIScreen mainScreen].bounds;
    window.rootViewController =[[CollectionViewController alloc] init];
    self.window = window;
    [window makeKeyAndVisible];
    return YES;
}
自定义的CollectionViewController控制器:
CollectionViewController.m文件
//  UICollection的基本使用-启动页制作
#import "ViewController.h"
#import "CollectionViewController.h"
@interface CollectionViewController ()
@end

@implementation CollectionViewController
static NSString * const reuseIdentifier = @"Cell";

- (void)viewDidLoad {
    [super viewDidLoad];
    //可以看到self.view != self.collectionView
    self.view.backgroundColor = [UIColor redColor];
    self.collectionView.backgroundColor = [UIColor greenColor];
    //取消弹簧效果
    self.collectionView.bounces = NO;
    self.collectionView.showsHorizontalScrollIndicator = NO;
    self.collectionView.pagingEnabled = YES;
    //1.必须通过注册cell
    [self.collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:reuseIdentifier];
}
//2.重写init方法,传值layout
// 初始化的时候必须设置布局参数，通常使用系统提供的流水布局UICollectionViewFlowLayout
-(instancetype)init{
    UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
    // 设置滚动的方向
    layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
    // 设置cell的尺寸
    layout.itemSize = [UIScreen mainScreen].bounds.size;
    // 设置cell之间间距
    layout.minimumInteritemSpacing = 0;
    // 设置行距
    layout.minimumLineSpacing = 0;
    // 设置每一组的内间距
    //    layout.sectionInset = UIEdgeInsetsMake(0, 10, 0, 10);
    return [super initWithCollectionViewLayout:layout];
}
//3.实现代理数据源方法
#pragma mark <UICollectionViewDataSource>
//返回多少组
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView {
    return 1;
}
//每组多少个cell
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return 4;;
}
//返回展示的cell
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    //自定义cell,在cell里面放一张图片即可(略)
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:reuseIdentifier forIndexPath:indexPath];
    [cell.contentView addSubview:[UISwitch new]];
    cell.backgroundColor = [UIColor blueColor];
    return cell;
}
#pragma mark <UICollectionViewDelegate>
//点击最后一张时替换掉keyWindow的rootViewcontroller
-(void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath{
    if (indexPath.item == 3) {
        UIWindow *keyWindow = [UIApplication sharedApplication].keyWindow;
        UIViewController *VC = [[UIStoryboard storyboardWithName:@"Main" bundle:nil] instantiateInitialViewController];
        keyWindow.rootViewController = VC;
    }
}
//在这里检测到,一旦替换掉,当前控制器就会被销毁
-(void)dealloc{
    NSLog(@"对象已经销毁");
}
@end
```
---
**效果图如下**

![图3-w100](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/collection1.gif) 


