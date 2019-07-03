---
layout: post
title: CollectionView的详细使用(二)
category: iOS开发
tags: CollectionView
description: CollectionView的详细使用
---

## collectionView的基本使用-九宫格

CollectionViewController2.m文件如下:

```
#import "CollectionViewController2.h"
#import "CollectionViewCell.h"
#import "CollectionHeadView.h"
#import "CollectionFooterView.h"
@interface CollectionViewController2 ()

@end

@implementation CollectionViewController2

static NSString * const reuseIdentifier = @"Cell";
static NSString * const reuseIdentifierHeader = @"header";
static NSString * const reuseIdentifierFooter = @"footer";
- (void)viewDidLoad {
    [super viewDidLoad];
    self.view.backgroundColor = [UIColor redColor];
    self.collectionView.backgroundColor = [UIColor greenColor];
    //1.必须注册cell
    [self.collectionView registerClass:[CollectionViewCell class] forCellWithReuseIdentifier:reuseIdentifier];
    //注册自定义footer和header,必须是继承UICollectionReusableView的View,或者直接使用UICollectionReusableView系统自带类
    //注册header
    [self.collectionView registerClass:[CollectionHeadView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:reuseIdentifierHeader];
    //注册footer
    UINib *nib = [UINib nibWithNibName:@"CollectionFooterView" bundle:nil];
    [self.collectionView registerNib:nib forSupplementaryViewOfKind:UICollectionElementKindSectionFooter withReuseIdentifier:reuseIdentifierFooter];
}
//2.重写init方法,传值layout
// 初始化的时候必须设置布局参数，通常使用系统提供的流水布局UICollectionViewFlowLayout
-(instancetype)init{
    //注意:这里都是统一设置,如果要单独设置,那么就要实现UICollectionViewDelegateFlowLayout代理方法
    UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];
    // 设置cell的尺寸
    layout.itemSize = CGSizeMake(60, 60);
    // 设置cell之间间距
    layout.minimumInteritemSpacing = 15;
    // 设置行距
    layout.minimumLineSpacing = 15;
    //设置内边距
    layout.sectionInset = UIEdgeInsetsMake(0, 15, 0, 15);
    //置设置sectionheader的高度(只有设置了才会去到数据源方法中拿View)
//    layout.headerReferenceSize = CGSizeMake(100, 100);
    //设置sectionfooter的高度
//    layout.footerReferenceSize = CGSizeMake(100, 100);
    return [super initWithCollectionViewLayout:layout];
}
//3.实现代理数据源方法
#pragma mark <UICollectionViewDataSource>
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView {
    return 3;
}
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return 30;;
}
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    //自定义cell,在cell里面放一张图片即可(略)
    CollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:reuseIdentifier forIndexPath:indexPath];
    UIView *selectView = [[UIView alloc] init];
    selectView.backgroundColor = [UIColor yellowColor];
//    cell.selectedBackgroundView = selectView;
    return cell;
}
//用于设置头尾的View,复用使用,该View必须继承自UICollectionReusableView
- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath{
    UICollectionReusableView *view = nil;
    if (kind == UICollectionElementKindSectionHeader) {
        view = [collectionView dequeueReusableSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:reuseIdentifierHeader forIndexPath:indexPath];
    }else {
        view = [collectionView dequeueReusableSupplementaryViewOfKind:UICollectionElementKindSectionFooter withReuseIdentifier:reuseIdentifierFooter forIndexPath:indexPath];
    }
    return view;
}
//详细设置布局
#pragma mark <UICollectionViewDelegateFlowLayout>
//详细设置每个item的size
//- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath{
//
//}
//详细设置每个section的内边距
//- (UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout insetForSectionAtIndex:(NSInteger)section{
//
//}
////详细设置每个cell中cell的行距
//- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumLineSpacingForSectionAtIndex:(NSInteger)section{
//
//}
////详细设置每个section中cell的列距
//- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumInteritemSpacingForSectionAtIndex:(NSInteger)section{
//
//}
//详细设置每个secton的header的size
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section{
    if (section%2) {
        return CGSizeMake(60, 60);
    }else{
        return CGSizeMake(100, 100);
    }
}
//详细设置每个section的footer的size
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForFooterInSection:(NSInteger)section{
    if (section%2) {
        return CGSizeMake(60, 60);
    }else{
        return CGSizeMake(100, 100);
    }
}
#pragma mark <UICollectionViewDeletegate>
// 允许选中时，高亮
- (BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
    return YES;
}

// 高亮完成后回调
- (void)collectionView:(UICollectionView *)collectionView didHighlightItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
    //设置高亮背景色
    UICollectionViewCell *cell = [collectionView cellForItemAtIndexPath:indexPath];
    cell.backgroundColor = [UIColor purpleColor];
}

// 由高亮转成非高亮完成时的回调
- (void)collectionView:(UICollectionView *)collectionView didUnhighlightItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
    //高亮消失时还原背景色
    UICollectionViewCell *cell = [collectionView cellForItemAtIndexPath:indexPath];
    cell.backgroundColor = [UIColor redColor];
}

// 设置是否允许选中
- (BOOL)collectionView:(UICollectionView *)collectionView shouldSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
    return YES;
}

// 设置是否允许取消选中
- (BOOL)collectionView:(UICollectionView *)collectionView shouldDeselectItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
    return YES;
}
//选中操作
-(void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath{
     NSLog(@"%s", __FUNCTION__);
}
// 取消选中操作
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%s", __FUNCTION__);
}
@end
```
自定义的CollectionHeadView:

```javascript

#import <UIKit/UIKit.h>

@interface CollectionHeadView : UICollectionReusableView

@end
#import "CollectionHeadView.h"

@implementation CollectionHeadView

//-(instancetype)init{
//    if (self = [super init]) {
//        [self setup];
//    }
//    return self;
//}
//只会调用这个方法,不会调用init方法
-(instancetype)initWithFrame:(CGRect)frame{
    if (self = [super initWithFrame:frame]) {
        [self setup];
    }
    return self;
}
-(void)setup{

    self.backgroundColor = [UIColor blueColor];
    UILabel *label = [[UILabel alloc] init];;
    label.frame = CGRectMake(0, 0, 100, 30);
    label.text = @"我是头部标题";
    [self addSubview:label];
}
@end
```

自定义的CollectionHeadView:     
通过xib创建,直接继承自   UICollectionReusableView    

```javascript
//1.修改xib中的类为当前类 2.设置xib中的重用标识footer
#import <UIKit/UIKit.h>

@interface CollectionFooterView : UICollectionReusableView

@end
#import "CollectionFooterView.h"

@implementation CollectionFooterView

- (void)awakeFromNib {
    [super awakeFromNib];
    // Initialization code
}

@end
```

自定义的CollectionViewCell: 
    
```javascript
#import <UIKit/UIKit.h>

@interface CollectionViewCell : UICollectionViewCell

@end

#import "CollectionViewCell.h"

@implementation CollectionViewCell
//只会调用这个方法,不会调用init方法
-(instancetype)initWithFrame:(CGRect)frame{
    if (self = [super initWithFrame:frame]) {
        [self setup];
    }
    return self;
}
//
//-(instancetype)init{
//    if (self = [super init]) {
//        [self setup];
//    }
//    return self;
//}

-(void)setup{
    self.backgroundColor = [UIColor redColor];
    NSLog(@"调用了");
    //在这里添加子控件
}
@end
```
**效果图如下**

![图4](https://raw.githubusercontent.com/zhoghua123/imgsBed/master/coll4.gif)

