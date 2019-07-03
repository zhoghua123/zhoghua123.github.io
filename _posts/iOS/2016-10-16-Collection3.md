---
layout: post
title: CollectionView的详细使用(三)
category: iOS开发
tags: CollectionView
description: CollectionView的详细使用
---

## collectionView的基本使用-自定义布局
1. 自定义布局有两种方式:
    1. 继承自`UICollectionViewLayout`
    2. 继承自`UICollectionViewFlowLayout`
2. 使用选择：
    1. 如果使用的布局包含了流水布局的功能，那就选择`UICollectionViewFlowLayout`
    2. 如果想完全自定义布局，选择：`UICollectionViewLayout`
3. 流水布局`UICollectionViewFlowLayout`有哪些特性？ 
    1. 根据属性可以看出：
        1. 滚动方向（横、竖）
        2. 头/尾部section，以及悬浮
        3. 每个item大小
        4. 当前行满，自动布局下一行（minimumLineSpacing、minimumInteritemSpacing）
4. 控制器代码如下:

    ```
    #import "ZHCircleLayout.h"
    #import "ViewController.h"
    #import "ZHLineLayout.h"
    #import "ZHDefineLayout.h"
    #import "ZHCollectionViewCell.h"
    #import "CollectionViewController2.h"
    @interface ViewController ()<UICollectionViewDataSource,UICollectionViewDelegate>
    @property (nonatomic,weak) UICollectionView *collectionView;
    @property (nonatomic,strong) NSMutableArray *imageArray;
    @end
    static NSString *cellID = @"itemcell";
    @implementation ViewController
    -(NSMutableArray *)imageArray{
        if (_imageArray == nil) {
            _imageArray = [NSMutableArray array];
            for (int i = 0 ;i<20; i++) {
                [_imageArray addObject:[NSString stringWithFormat:@"%d",i+1]];
            }
        }
        return _imageArray ;
    }
    - (void)viewDidLoad {
        [super viewDidLoad];
        //1. 初始化自定义布局：ZHLineLayout
        CGFloat collectionWH = [UIScreen mainScreen].bounds.size.width;
        CGRect frame = CGRectMake(0, 200, collectionWH, 300);
        ZHLineLayout *layout = [[ZHLineLayout alloc] init];
        layout.itemSize = CGSizeMake(150, 150);//设置item的尺寸
        //水平滚动
        layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
        
        //2. 初始化UICollectionView
        UICollectionView *collection = [[UICollectionView alloc] initWithFrame:frame collectionViewLayout:layout];
        //只能注册
    //    [collection registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:cellID];
        [collection registerNib:[UINib nibWithNibName:@"ZHCollectionViewCell" bundle:nil] forCellWithReuseIdentifier:cellID];
        collection.dataSource = self;
        collection.delegate = self;
        [self.view addSubview:collection];
        self.collectionView = collection;
       
    }
    //布局间的切换
    -(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    //    CollectionViewController2 *collectVC = [[CollectionViewController2 alloc] init];
    //    [self presentViewController:collectVC animated:YES completion:nil];
        //看完自定义布局3之后在看下面这个!!切换布局
        if ([self.collectionView.collectionViewLayout isKindOfClass:[ZHLineLayout class]]) {
            [self.collectionView setCollectionViewLayout:[[ZHCircleLayout alloc] init] animated:YES];
        }else if([self.collectionView.collectionViewLayout isKindOfClass:[ZHCircleLayout class]]) {
             [self.collectionView setCollectionViewLayout:[[ZHDefineLayout alloc] init] animated:YES];
        }
        else if([self.collectionView.collectionViewLayout isKindOfClass:[ZHDefineLayout class]]) {
            ZHLineLayout *layout = [[ZHLineLayout alloc] init];
            layout.itemSize = CGSizeMake(150, 150);
            layout.scrollDirection = UICollectionViewScrollDirectionHorizontal;
            [self.collectionView setCollectionViewLayout:layout animated:YES];
        }
    }
    
    #pragma mark - UICollectionViewDataSource
    //必须实现@required:
    //每个section里面有多少个item
    - (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section{
        return self.imageArray.count;
    }
    
    //每个cell
    - (__kindof UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath{
        ZHCollectionViewCell *item = [collectionView dequeueReusableCellWithReuseIdentifier:cellID forIndexPath:indexPath];
        item.imageName = self.imageArray[indexPath.item];
    //    item.backgroundColor = [UIColor redColor];
        //没有该方法,只能使用注册(sb不需要写)
    //    if (!item) {
    //        item = [UICollectionViewCell alloc] init...
    //    }
        //精辟!!!防止重复添加!!!!
    //    NSInteger  tag = 10;
    //    UILabel *label = (UILabel *)[item.contentView viewWithTag:tag];
    //    if (!label) {
    //        label = [[UILabel alloc] init];
    //        label.tag = tag;
    //        [item.contentView addSubview:label];
    //    }
    //    label.text = [NSString stringWithFormat:@"%zd",indexPath.item];
    //    [label sizeToFit];
        return item;
    }
    #pragma mark - UICollectionViewDeletegate
    //点击图片删除图片
    -(void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath{
        [self.imageArray removeObjectAtIndex:indexPath.item];
        [self.collectionView deleteItemsAtIndexPaths:@[indexPath]];
    }
    @end
    ```
    
### 继承自UICollectionViewFlowLayout的自定义布局
1. 需求
    1. 实现一个相册浏览功能
    2. 左右滑动可以滚动每张照片
    3. 停止滚动中间照片居中放大显示，左右2张小张显示
2. 选择布局的自定义方式
    1. 视图是左右滚动（横向滚动）--流水布局的特性
    2. 排列是每行一个，n列，规则布局--流水布局的特性
    3. 所以采用继承流水布局`UICollectionViewFlowLayout`的方式实现
3. 技术难点：
    1. cell的放大缩小  
    2. 停止滚动时:cell的居中 
4. 代码实现：
    
    ```
    #import <UIKit/UIKit.h>
    @interface ZHLineLayout : UICollectionViewFlowLayout   
    @end
    
    #import "ZHLineLayout.h"
    @implementation ZHLineLayout
    /**
     UICollectionViewLayout方法：
     当collectionView的显示范围发生改变的时候(就是contentsize显示那部分),是否需要重新布局
     一旦刷新布局,就会重新调用layoutAttributesForElementsInRect方法和prepareLayout方法
     即:只要滚动,就回改变contentsize的显示rect,那么就会调用layoutAttributesForElementsInRect重新布局
     */
    -(BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds{
        return YES;
    }
    
    /**
     UICollectionViewLayout方法
     准备布局
     所有的初始化操作都要放在这里面,不要放在上面init中,因为调用init方法时,self还没有添加到collectionView里面,因此self.collectionView为nil,itemsize也不能拿到,拿到的只是系统默认的50,50
     */
    -(void)prepareLayout{
        [super prepareLayout];
        //设置secton的内边距,为了让第一个和最后一个cell显示在最中间
        CGFloat margin = (self.collectionView.frame.size.width - self.itemSize.width)*0.5;
        self.sectionInset = UIEdgeInsetsMake(0, margin, 0, margin);
    }
    /**
     UICollectionViewLayout方法
     1. 通过这个方法返回所有的attributes,去展示cell
     2. 这个方法返回值是一个数组(数组里面存放着rect范围内所有元素的布局属性)
     3. 这个方法返回值决定了rect范围内所有元素的排布(frame)
     4. UICollectionViewLayoutAttributes-布局属性
        1. 一个cell就对应一个UICollectionViewLayoutAttributes对象
        2. UICollectionViewLayoutAttributes对象决定了cell的展示样式(frame等其他)
     5. 实现功能：cell的放大缩小
     */
    -(NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
        //1. 获得super已经计算好的布局属性
        NSArray *array = [super layoutAttributesForElementsInRect:rect];
        //2. 在原有布局属性基础上,进行微调，设置缩放
        //思路:当cell的中心点x距离collectionView的中心线越来越近时应该放大,重合时最大,越来越远时应该缩小,注意这个距离是相对于contentsize的!!
        //2.1 计算collectionView最中心点的x值,注意是相对于contentsize的! 偏移量+宽度的一半
        CGFloat centerX = self.collectionView.contentOffset.x + self.collectionView.frame.size.width * 0.5;
        for (UICollectionViewLayoutAttributes *attri in array) {
            //每一个cell中心点的x,注意是相对于collectionView的contentsize的
    //        NSLog(@"%f",attri.center.x );
            //2.2 拿到cell中心点x与cellectionview中心点的距离
            CGFloat delta = ABS(attri.center.x - centerX);
            //2.3 根据间距值计算出cell的缩放比例
            CGFloat scale = 1 - delta/self.collectionView.frame.size.width;
            attri.transform = CGAffineTransformMakeScale(scale, scale);
            NSLog(@"%f",delta);
        }
    //    NSLog(@"%zd",array.count );
    //    NSLog(@"%f",self.collectionView.frame.size.width );
        return array;
    }
    
    /**
     UICollectionViewLayout方法
     1. 实现功能：停止滚动时（不是最后一个cell时）,cell的居中
     2. 这个方法就决定了,collectionview停止滚动时的偏移量contentsize的offset(x,y)
     3. 换句话说,返回值point是啥,contenview就在哪地方停下来
     4. 注意,point是相对于contentsize而言的
     5. 调用时刻:手一松开就会调用,但是注意此时collectionView可能任然在滚动!
     6. 因此这几个参数
        1. proposedContentOffset:真正停止那一刻的偏移量
        2. self.collectionView.contentOffset.x:手停那一刻的偏移量
        2. 参数velocity:速度描述量,velocity.x水平方向上的速率,velocity.y竖直方向上的速率
        3. 注意:如果将下面的proposedContentOffset全部改为self.collectionView.contentOffset的话,结果是手一旦松开就回停止滚动,但是仍然会居中显示
     7. 思路:
        1.拿到哪个cell距离collectionView中心线最近距离
        2.改变这个contentView的偏移量即可
     */
    -(CGPoint)targetContentOffsetForProposedContentOffset:(CGPoint)proposedContentOffset withScrollingVelocity:(CGPoint)velocity{
        //1.拿到所有cell的中心点x
        //用super,否者会调用上面的那个方法
        //注意这里要传的当然也是真正停止后矩形的x
        CGRect rect;
        rect.origin.x = proposedContentOffset.x;
        rect.origin.y = 0;
        rect.size = self.collectionView.frame.size;
        NSArray *array = [super layoutAttributesForElementsInRect:rect];
       //计算collectionView最中心点的x值
       //self.collectionView.contentOffset.x此时就不能用这个了
       CGFloat centerX = proposedContentOffset.x + self.collectionView.frame.size.width * 0.5;
       CGFloat mindelta = MAXFLOAT;
       for (UICollectionViewLayoutAttributes *attri in array) {
           if (ABS(mindelta) > ABS(attri.center.x - centerX)) {
               mindelta = attri.center.x - centerX;
           } ;
        }
       NSLog(@"%f-------%f=======%f",self.collectionView.contentOffset.x,velocity.x,proposedContentOffset.x);
        //2.修改contentView原有的偏移量
        proposedContentOffset.x += mindelta;
       return proposedContentOffset;
    }
    @end
    ```
    
### 继承自UICollectionViewLayout的自定义布局

#### 普通布局
1. 本次使用布局完全用自定义布局`UICollectionViewLayout`,不再继承自流水布局`UICollectionViewFlowLayout`
2. 继承自最根本的布局`UICollectionViewLayout`注意以下几点:
    1. `cell`的所有的`attribute`对象需要自己创建
        1. 需要重写:`layoutAttributesForElementsInRect`方法
        2. 为了只计算一次,那么就可以把计算放在`prepareLayout`方法中
        3. 如果数据量非常大,那么可以把计算放在子线程中,当计算完成回到主线程刷新布局即可
    2. `collectionView`的`contentsize`需要你重新告诉他,否则不滚动
        1. 需要重写:`collectionViewContentSize`方法
    3. 重写返回每一个`cell`的布局属性`layoutAttributesForItemAtIndexPath`
        1. 要是切换布局的话这个方法必须实现,否则报错
        2. 不切换可以不实现
    4. 代码示例：
        
        ```
        #import <UIKit/UIKit.h>
        @interface ZHDefineLayout : UICollectionViewLayout
        @end
        #import "ZHDefineLayout.h"
        @interface ZHDefineLayout()
        @property (nonatomic,strong) NSMutableArray *attriArray;
        @end
        @implementation ZHDefineLayout
        -(NSMutableArray *)attriArray{
            if (_attriArray == nil) {
                _attriArray = [NSMutableArray array];
            }
            return _attriArray ;
        }
        -(void)prepareLayout{
            [super prepareLayout];
            //1. 计算出所有cell的UICollectionViewLayoutAttributes
            [self.attriArray removeAllObjects];
            //拿到collectionView的所有的cell个数,这里只考虑一个section,多组两重for循环
            //多少组
            NSInteger sectoncount = [self.collectionView numberOfSections];
            //那一组多少个
            NSInteger rowcount = [self.collectionView numberOfItemsInSection:0];
            for (int i = 0 ; i<rowcount; i++) {
                //创建UICollectionViewLayoutAttributes
                NSIndexPath *indexpath = [NSIndexPath indexPathForItem:i inSection:0];
                UICollectionViewLayoutAttributes *attri = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexpath];
                //设置布局属性
                CGFloat width = self.collectionView.frame.size.width * 0.5;
                CGFloat height = 0;
                CGFloat x = 0;
                CGFloat y = 0;
                //i%3==0时 height = width
                if (i%3 == 0) {
                    height = width;
                    x = 0;
                    y = i/3*width;
                }else if (i%3 == 1){
                    height = 0.5*width;
                    x = width;
                    y = i/3 * width;
                }else if (i%3 == 2){
                    height = 0.5 * width;
                    x = width;
                    y = i/3 * width + 0.5*width;
                }
                attri.frame = CGRectMake(x, y, width, height);
                //添加UICollectionViewLayoutAttributes到数组中
                [self.attriArray addObject:attri];
            }
        }
        -(NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
            //继承自最根本的布局,因此就不用调用该方法了
        //    NSArray *array = [super layoutAttributesForElementsInRect:rect];
            //既然没有,那就需要我们自己创建了
            return self.attriArray;
        }
        //2. 因为你是空白继承,需要告诉他collectionView的contentsize才能滚动
        -(CGSize)collectionViewContentSize{
            //那一组多少个
            NSInteger rowcount = [self.collectionView numberOfItemsInSection:0];
            CGSize size;
            size.width = self.collectionView.frame.size.width;
            CGFloat height =(rowcount + 2)/3 *self.collectionView.frame.size.width * 0.5 ;
            size.height = height;
            return size;
        }
        
        /**
         3. 返回每一个cell的布局属性
         要是切换布局的话这个方法必须实现,否则报错
         不切换可以不实现
         */
        -(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath{
            return self.attriArray[indexPath.item];
        }
        @end
        ```

#### 环形布局
1. 示例代码
    
    ```
    #import <UIKit/UIKit.h>
    @interface ZHCircleLayout : UICollectionViewLayout
    @end
    //注意:这里不需要滚动,因此不用重写:-(CGSize)collectionViewContentSize
    #import "ZHCircleLayout.h"
    @interface ZHCircleLayout()
    @property (nonatomic,strong) NSMutableArray *attriArray;
    @end
    @implementation ZHCircleLayout
    -(NSMutableArray *)attriArray{
        if (_attriArray == nil) {
            _attriArray = [NSMutableArray array];
        }
        return _attriArray ;
    }
    -(void)prepareLayout{
        [super prepareLayout];
        //计算出所有cell的UICollectionViewLayoutAttributes
        [self.attriArray removeAllObjects];
        //那一组多少个
        NSInteger rowcount = [self.collectionView numberOfItemsInSection:0];
        //假设分布在半径100的圆上
        CGFloat radus = 100;
        //圆心的x,y
        CGFloat ocenterx = self.collectionView.frame.size.width * 0.5;
        CGFloat ocentery = self.collectionView.frame.size.height * 0.5;
        
        for (int i = 0 ; i<rowcount; i++) {
            //创建UICollectionViewLayoutAttributes
            NSIndexPath *indexpath = [NSIndexPath indexPathForItem:i inSection:0];
            UICollectionViewLayoutAttributes *attri = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexpath];
            //设置布局属性(将图片布局到一个圆上:只要center再一个圆上即可)
            if (rowcount == 1) {
                //只有一个
                attri.center = CGPointMake(ocenterx,ocentery);
                attri.size = CGSizeMake(150, 150);
            }else{
                //多个
                //每个图片的的夹角为
                CGFloat jiao = 2*M_PI/rowcount * i;
                attri.size = CGSizeMake(50, 50);
                CGFloat centerx = ocenterx + radus * sin(jiao);
                CGFloat centery = ocentery + radus * cos(jiao);
                attri.center = CGPointMake(centerx, centery);
                //每个图片旋转相应的度数
                attri.transform = CGAffineTransformMakeRotation(jiao);
            }
            
            //添加UICollectionViewLayoutAttributes到数组中
            [self.attriArray addObject:attri];
        }
    }
    -(NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
        return self.attriArray;
    }
    /**
     返回每一个cell的布局属性
     要是切换布局的话这个方法必须实现,否则报错
     不切换可以不实现
     */
    -(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath{
        return self.attriArray[indexPath.item];
    }
    @end
    ```


效果图如下:

![图5](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/coll5.gif)


