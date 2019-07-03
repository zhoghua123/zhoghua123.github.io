---
layout: post
title: CollectionView的详细使用(四)
category: iOS开发
tags: CollectionView
description: CollectionView的详细使用
---


## 自定义布局-瀑布流
1. 实现瀑布流常见的3中方案:  
    1. `view`上面添加一个`scrollView`,接着在添加3列`tableView`,分别禁止`tableView`的滚动   
    2. `view`上面添加一个`scrollView`,在一个一个往`scrollView`上面添加    
    3. 用`UIConllectionView `  
2. 分析可得:瀑布流总是找最短的那个添加,因此不是流水布局,那么自定义布局就要继承自根布局    

3. 控制器代码如下:

    ```
    #import "ZHWaterLayout.h"
    #import "ViewController3.h"
    #import "ZHShopViewCell.h"
    #import "MJRefresh.h"
    #import "ZHShop.h"
    #import "MJExtension.h"
    @interface ViewController3 ()<UICollectionViewDataSource,ZHWaterLayoutDelegate>
    @property (nonatomic,strong) NSMutableArray *dataArray;
    @property (nonatomic,weak) UICollectionView *collectionView;
    @end
    static NSString * const cellID = @"shopcell";
    
    @implementation ViewController3
    -(NSMutableArray *)dataArray{
        if (_dataArray == nil) {
            _dataArray = [NSMutableArray array];
        }
        return _dataArray ;
    }
    - (void)viewDidLoad {
        [super viewDidLoad];
        [self setupLayout];
        [self setupRefresh];
    }
    -(void)setupRefresh{
        self.collectionView.header = [MJRefreshNormalHeader headerWithRefreshingTarget:self refreshingAction:@selector(newData)];
        [self.collectionView.header beginRefreshing];
        self.collectionView.footer = [MJRefreshAutoNormalFooter footerWithRefreshingTarget:self refreshingAction:@selector(moreData)];
        self.collectionView.footer.hidden = YES;
    }
    -(void)newData{
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            NSArray *aray = [ZHShop objectArrayWithFilename:@"112.plist"];
            [self.dataArray removeAllObjects];
            [self.dataArray addObjectsFromArray:aray];
            [self.collectionView reloadData];
            [self.collectionView.header endRefreshing];
        });
       
    }
    -(void)moreData{
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            NSArray *aray = [ZHShop objectArrayWithFilename:@"112.plist"];
            [self.dataArray addObjectsFromArray:aray];
            [self.collectionView reloadData];
            [self.collectionView.footer endRefreshing];
        });
        
    }
    -(void)setupLayout{
        ZHWaterLayout *layout = [[ZHWaterLayout alloc] init];
        layout.delegate = self;
        UICollectionView *collection = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:layout];
        //只能注册
        [collection registerNib:[UINib nibWithNibName:@"ZHShopViewCell" bundle:nil] forCellWithReuseIdentifier:cellID];
        collection.backgroundColor = [UIColor whiteColor];
        collection.dataSource = self;
        self.collectionView = collection;
        [self.view addSubview:collection];
    }
    
    #pragma mark - UICollectionViewDataSource
    //必须实现@required:
    //每个section里面有多少个item
    - (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section{
        self.collectionView.footer.hidden = self.dataArray.count == 0;
        return self.dataArray.count;
    }
    
    //每个cell
    - (__kindof UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath{
        ZHShopViewCell *item = [collectionView dequeueReusableCellWithReuseIdentifier:cellID forIndexPath:indexPath];
        item.shop = self.dataArray[indexPath.item];
        return item;
    }
    #pragma mark - ZHWaterLayoutDelegate
    -(CGFloat)waterLayout:(ZHWaterLayout *)waterLayout heightForItemAtIndex:(NSInteger )index andItemWidth:(CGFloat)itemWidth{
        ZHShop *shop = self.dataArray[index];
        return itemWidth * shop.h/shop.w;
    }
    -(NSInteger)columCountWaterLayout:(ZHWaterLayout *)waterLayout{
        return 4;
    }
    -(CGFloat)columMarginWaterLayout:(ZHWaterLayout *)waterLayout{
        return 10;
    }
    -(CGFloat)rowMarginWaterLayout:(ZHWaterLayout *)waterLayout{
        return 10;
    }
    -(UIEdgeInsets)edgeInsetsWaterLayout:(ZHWaterLayout *)waterLayout{
        return UIEdgeInsetsMake(10, 10, 10, 10);
    }
    @end
    ```

4. 自定义xib的cell代码如下:

    ```
    #import <UIKit/UIKit.h>
    @class ZHShop;
    @interface ZHShopViewCell : UICollectionViewCell
    @property (nonatomic, strong) ZHShop *shop;
    @end
    #import "ZHShopViewCell.h"
    #import "ZHShop.h"
    #import "UIImageView+WebCache.h"
    @interface ZHShopViewCell()
    @property (weak, nonatomic) IBOutlet UIImageView *imageView;
    @property (weak, nonatomic) IBOutlet UILabel *priceLabel;
    
    @end
    @implementation ZHShopViewCell
    
    - (void)setShop:(ZHShop *)shop
    {
        _shop = shop;
        // 1.图片
        [self.imageView sd_setImageWithURL:[NSURL URLWithString:shop.img] placeholderImage:[UIImage imageNamed:@"loading"]];
        // 2.价格
        self.priceLabel.text = shop.price;
    }
    @end
    ```
    
5. 模型ZHShop如下:

    ```
    #import <UIKit/UIKit.h>
    
    @interface ZHShop : NSObject
    @property (nonatomic, assign) CGFloat w;
    @property (nonatomic, assign) CGFloat h;
    @property (nonatomic, copy) NSString *img;
    @property (nonatomic, copy) NSString *price;
    @end
    ```
6. 自定义布局ZHWaterLayout如下:

    ```
    
    #import <UIKit/UIKit.h>
    @class ZHWaterLayout;
    //自定义协议
    @protocol ZHWaterLayoutDelegate<NSObject>
    @required
    /**
     返回item的高度
     */
    -(CGFloat)waterLayout:(ZHWaterLayout *)waterLayout heightForItemAtIndex:(NSInteger )index andItemWidth:(CGFloat)itemWidth;
    @optional
    /**
     有多少列
     */
    -(NSInteger)columCountWaterLayout:(ZHWaterLayout *)waterLayout;
    /**
    列边距
     */
    -(CGFloat)columMarginWaterLayout:(ZHWaterLayout *)waterLayout;
    /**
    行间距
     */
    -(CGFloat)rowMarginWaterLayout:(ZHWaterLayout *)waterLayout;
    /**
     内边距
     */
    -(UIEdgeInsets )edgeInsetsWaterLayout:(ZHWaterLayout *)waterLayout;
    @end
    @interface ZHWaterLayout : UICollectionViewLayout
    @property (nonatomic,weak) id<ZHWaterLayoutDelegate> delegate;
    @end
    #import "ZHWaterLayout.h"
    
    @interface ZHWaterLayout()
    @property (nonatomic,strong) NSMutableArray *attriArray;
    //用来存放所有列的高度
    @property (nonatomic,strong) NSMutableArray *colsHeght;
    //注意get方法要想提示写出,必须在这里先声明!!!!
    -(CGFloat)rowMargin;
    -(CGFloat)columMargin;
    -(NSInteger)columCount;
    -(UIEdgeInsets)edgeInsets;
    
    @end
    //默认值
    static const CGFloat rowMargin = 10;//行距
    static const CGFloat colMargin = 10;//列距
    static const NSInteger col = 3;//默认列数
    static const UIEdgeInsets edgeInsets = {10,10,10,10};//内边距
    
    @implementation ZHWaterLayout
    //数据处理get方法
    -(CGFloat)rowMargin{
        if ([self.delegate respondsToSelector:@selector(rowMarginWaterLayout:)]) {
            return [self.delegate rowMarginWaterLayout:self];
        }else{
            return rowMargin;
        }
    }
    -(CGFloat)columMargin{
        if ([self.delegate respondsToSelector:@selector(columMarginWaterLayout:)]) {
            return [self.delegate columMarginWaterLayout:self];
        }else{
            return colMargin;
        }
    }
    -(NSInteger)columCount{
        if ([self.delegate respondsToSelector:@selector(columCountWaterLayout:)]) {
            return [self.delegate columCountWaterLayout:self];
        }else{
            return col;
        }
    }
    -(UIEdgeInsets)edgeInsets{
        if ([self.delegate respondsToSelector:@selector(edgeInsetsWaterLayout:)]) {
            return [self.delegate edgeInsetsWaterLayout:self];
        }else{
           return edgeInsets;
        }
    }
    //记录各列的高度
    -(NSMutableArray *)colsHeght{
        if (_colsHeght == nil) {
            _colsHeght = [NSMutableArray array];
        }
        return _colsHeght ;
    }
    //存储所有cell的UICollectionViewLayoutAttributes
    -(NSMutableArray *)attriArray{
        if (_attriArray == nil) {
            _attriArray = [NSMutableArray array];
        }
        return _attriArray ;
    }
    //初始化操作
    -(void)prepareLayout{
        [super prepareLayout];
        //1.每次刷新就回重新布局一次,重新清理一下数据
        //1.1清除高度数组并初始化
        [self.colsHeght removeAllObjects];
        //初始化数组
        for (int i= 0; i<self.columCount; i++) {
            [self.colsHeght addObject:@(self.edgeInsets.top)];
        }
        //1.2清除布局属性数据
        [self.attriArray removeAllObjects];
        //2.计算出所有cell的UICollectionViewLayoutAttributes
        //共多少个item
        NSInteger rowcount = [self.collectionView numberOfItemsInSection:0];
        //遍历计算出每一个
        for (int i = 0 ; i<rowcount; i++) {
            //创建UICollectionViewLayoutAttributes
            NSIndexPath *indexpath = [NSIndexPath indexPathForItem:i inSection:0];
            UICollectionViewLayoutAttributes *attri = [self layoutAttributesForItemAtIndexPath:indexpath];
            //添加UICollectionViewLayoutAttributes到数组中
            [self.attriArray addObject:attri];
        }
    }
    //返回rect内的所有cell的UICollectionViewLayoutAttributes数组
    -(NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
        return self.attriArray;
    }
    
    //返回每一个cell的UICollectionViewLayoutAttributes
    -(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath{
        //1.创建UICollectionViewLayoutAttributes对象
        UICollectionViewLayoutAttributes *attri = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
        //2.设置UICollectionViewLayoutAttributes的frame属性
        CGFloat collectW = self.collectionView.frame.size.width;
        //2.1计算出宽度
        CGFloat width = (collectW-self.edgeInsets.left-self.edgeInsets.right-(self.columCount-1)*self.columMargin)/self.columCount;
        //2.2根据外部模型数据传进来的高度计算出高度
        CGFloat height = [self.delegate waterLayout:self heightForItemAtIndex:indexPath.item andItemWidth:width];
        //2.3 计算出x
        //找出高度最短的那一列
    //    __block NSUInteger desCol = 0;
    //   __block CGFloat minColHeight = MAXFLOAT;
    //    [self.colsHeght enumerateObjectsUsingBlock:^(NSNumber *  _Nonnull colHeight, NSUInteger idx, BOOL * _Nonnull stop) {
    //        if (colHeight.doubleValue < minColHeight) {
    //            minColHeight = colHeight.doubleValue;
    //            desCol = idx;
    //        }
    //    }];
        //这样遍历可以少算一列
        NSInteger desCol = 0;
        CGFloat minColHeight = [self.colsHeght[0] doubleValue];
        for (NSInteger i = 1; i<self.columCount; i++) {
            //获取第一列的高度
            CGFloat colH = [self.colsHeght[i] doubleValue];
            if (colH<minColHeight) {
                minColHeight = colH;
                desCol = i;
            }
        }
        CGFloat x = self.edgeInsets.left + desCol*(width + self.columMargin);
        //2.4计算出y值
        CGFloat y = minColHeight;//第一行时不加rowmargin
        if (y != self.edgeInsets.top) {
            y += self.rowMargin;
        }
        //3.赋值frame
        attri.frame = CGRectMake(x,y, width, height);
        //4.更新高度数组
        self.colsHeght[desCol] =@(CGRectGetMaxY(attri.frame)) ;
        return attri;
    }
    
    //返回collectionView的contentsize
    -(CGSize)collectionViewContentSize{
        CGFloat maxColHeight = [self.colsHeght[0] doubleValue];
        for (NSInteger i = 1; i<self.columCount
             ; i++) {
            //获取第一列的高度
            CGFloat colH = [self.colsHeght[i] doubleValue];
            if (colH>maxColHeight) {
                maxColHeight = colH;
            }
        }
        return CGSizeMake(0, maxColHeight+self.edgeInsets.bottom);
    }
    
    @end
    ```
    
    
*[demo的Github地址](https://github.com/zhoghua123/UICollectionView)*

效果图如下:

![图6](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/coll6.gif)


