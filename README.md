# XZCarouselView

@[TOC](目录)

## 说明
轮播图在 iOS 开发中属于常见需求，虽然第三方实现很多，但是真正让人满意的并不多。因此，组件 `XZCarouselView` 是我很早以前就开始写的一个组件，在经历过数次的优化、重构之后，我终于觉得能拿得出手，分享给大家一起使用。以后可能会有更多的组件分享出来，为了方便管理，组件 `XZCarouselView` 现在是 `XZKit` 模块之一，无其它依赖，可以单独引用。

## 效果
![XZImageCarouselViews示例](https://github.com/mlibai/XZKit/raw/master/Documentation/CarouselView/1.gif) 
![XZImageViewer示例](https://github.com/mlibai/XZKit/raw/master/Documentation/CarouselView/2.gif)  
![XZCarouselViewController示例](https://github.com/mlibai/XZKit/raw/master/Documentation/CarouselView/3.gif)

## 安装
对于 iOS 第三方，支持 [CocoaPods](https://cocoapods.org/) 是必须的：
```ruby
pod 'XZKit/CarouselView'
```
`XZCarouselView` 现在是 [XZKit](https://github.com/mlibai/XZKit) 框架的子模块，如果没有安装 CocoaPods 那就得去 [GitHub](https://github.com/mlibai/XZKit) 下载源代码了。

## 特性
`XZCarouselView ` 为 `CarouselView` 模块中的基础类，提供轮播图机制，方便再封装和自定义，当然也可以直接使用。`CarouselView` 中的图片轮播图 `XZImageCarouselView` 组件、大图查看器 `XZImageViewer` 组件、多控制器容器 `XZCarouselViewController`  都是基于类 `XZCarouselView` 来实现的。
`XZCarouselView ` 除了支持无限轮播、自动轮播、轮播方向、缩放等常用功能外，还支持内容自适应、获取轮播进度、自适应布局方向（针对阿拉伯语等自右向左布局等系统）、重用机制、添加自定义切换动画等功能，基本能满足大部分业务中对轮播图的需求。
`XZCarouselView ` 最大的特点应该就是支持切换进度事件的获取，特别方便做联动效果，比如大部分资讯 App 首页的多栏目结构，内容列表与菜单的联动，用 `UIPageViewController` 做列表虽然好用，但是难以获取轮播进度，联动效果不方便处理。所以某些资讯 App 的首页，联动效果处理的就不够自然。比如比较知名 CSDN、IT之家的 App 首页，在栏目页面连续切换时，栏目菜单无法正常滚动，点击栏目菜单，栏目页面切换也没有动画效果。而使用 `XZCarouselView` 实现的 `XZCarouselViewController` 在[示例代码](https://github.com/mlibai/XZKit/tree/master/Projects/Example/CarouselViewExample)中，就以 IT之家 App 首页为例，实现了更自然的联动效果示例，即上面的效果图3。
另外 `XZCarouselView` 使用中文注释，大部分属性或方法都有详细的注释文案，更多特性请移步[阅读源代码](https://github.com/mlibai/XZKit/tree/master/XZKit/Code/CarouselView)。
## 如何使用
### 1. XZCarouselView
轮播视图组件 `XZCarouselView ` 是 UIView 子类，像 UIView 一样使用即可。因为属于 XZKit 框架，如果使用 CocoaPods 引用的话，在 Swift 中类名就不需要加前缀 `XZ` ，使用 `CarouselView` 或 `XZKit.CarouselView` 来代替它在 ObjectiveC 中 `XZCarouselView` 命名。
```swift
let carouselView = CarouselView.init(frame: UIScreen.main.bounds);
self.addSubview(carouselView)
```
通过属性可以控制轮播图的各种特性，下面是 `XZCarouselView ` 的主要属性说明。
```swift
/// 当前正在显示的视图的索引。
open var currentIndex: Int
/// 是否无限轮播。默认 YES 。
/// @note 更改此属性会刷新轮播图。
open var isWrapped: Bool
/// 最小缩放倍数，默认 1.0 。
open var minimumZoomScale: CGFloat
/// 最大缩放倍数，默认 1.0 。
open var maximumZoomScale: CGFloat
/// 当前缩放倍数。
open var zoomScale: CGFloat
/// 是否锁定缩放。缩放锁定时，左右滚动禁用。默认 YES 。
open var isZoomingLockEnabled: Bool
/// 自动轮播时间间隔，0 表示不自动轮播。
/// @note 建议时长大于 1 秒，自动轮播动画时间，不包括在此时间内。
/// @note 缩放或拖拽时，自动轮播会暂停。
open var timeInterval: TimeInterval
/// 自动轮播方向，默认 forward 。
/// @note 无限轮播时，设置此值会影响轮播方向。
/// @note 非无限轮播时，此属性会自动改变，即正向轮播到最后一个就会开始逆向轮播。
open var scrollDirection: CarouselView.ScrollDirection
```
### 2. XZImageCarouselView
图片轮播图 `XZImageCarouselView` 继承自 `XZCarouselView` ，主要提供了直接使用 UIImage 数组或者图片 URL 数组作为数据源的功能，不用重复的去实现数据源代理协议。
```swift
let carouselView = ImageCarouselView.init(frame: UIScreen.main.bounds);
// 以图片链接作为数据源。
carouselView.imageURLs = self.imageURLs 
// 以 UIImage 对象作为数据源，轮播图此数据源图片。
carouselView.images = self.images
// 如果同时设置两种数据源，推荐使用下面的方法。
carouselView.setImages(self.images, imageURLs: self.imageURLs)
```
### 3. XZImageViewer
图片大图查看器 `XZImageViewer` 在内部管理了一个 `XZCarouselView` ，用来展示图片，其自身主要是处理数据源及转场逻辑。为了简化逻辑，`XZImageViewer` 使用 `XZImageViewerDelegate` 同时作为数据源和事件代理。
```swift
let viewer = ImageViewer.init()
viewer.delegate = self
// 设置 imageVier 默认显示的图片。
viewer.currentIndex = indexPath.item
// 最大缩放比
viewer.maximumZoomScale = 3.0
// 适配模式。
viewer.contentMode = .scaleAspectFit
// 关闭缩放锁。
viewer.isZoomingLockEnabled = false
// 展示大图查看器。
self.present(viewer, animated: true, completion: nil);
```
为了实现类似微信大图查看的转场效果，代理协议 `XZImageViewerDelegate` 除了必须实现的方法外，需实现下面两个可选方法才能达到效果。
```swift
// 实现这个方法，XZImageViewer 才能知道原始视图在屏幕上位置，也就是转场动画的起点。
func imageViewer(_ imageViewer: ImageViewer, sourceRectForItemAt index: Int) -> CGRect {
    guard let cell = collectionView.cellForItem(at: IndexPath.init(item: index, section: 0)) as? Example2CollectionViewCell else { return .zero }
    return cell.convert(cell.bounds, to: self.view)
}							
// 这个方法主要是来优化转场效果的，告诉 XZImageViewer 原始视图的大小与图片展示方式，才能作出完美的转场动画。
func imageViewer(_ imageViewer: ImageViewer, sourceContentModeForItemAt index: Int) -> UIView.ContentMode {
    return .scaleAspectFill
}
```
### 4. XZCarouselViewController
使用 `XZCarouselView` 实现的多控制器容器，用来替代 `UIPageViewController` 的最佳选择。借助于 `XZCarouselView` 特性，可以方便的处理控制器与菜单的动画效果，让切换效果看起来更自然；也更容易掌控控制器的生命周期，方便处理重用，节省内存。如效果图3中，实现联动效果的代码。
```swift
// 代理方法。处理控制器转场进度与菜单之间的动画。
func carouselViewController(_ carouselViewController: CarouselViewController, didTransition transition: CGFloat, animated: Bool) {
   let newIndex = carouselViewController.currentIndex
   if menuIndex == newIndex { //
       if transition > 0 { // 滚往下一个。
           menuTranstion(to: menuIndex + 1, transition: transition) // 执行动画的方法。
       } else if transition < 0 {
           menuTranstion(to: menuIndex - 1, transition: -transition)
       } else { // 滚动取消
           menuTranstion(to: menuIndex, transition: 0)
       }
   } else { // 页面已跳转到新的 index 。
       if (transition == 0) { // 完成跳转
           menuIndex = newIndex
           menuTranstion(to: menuIndex, transition: 0)
       } else { // 跳转中。
           menuTranstion(to: newIndex, transition: 1.0 - abs(transition))
       }
   }
}
// 动画应该封装在 menu 视图中，示例中没有单独封装 menu 视图，而是直接使用原生的 UICollectionView 视图。
private func menuTranstion(to newIndex: Int, transition: CGFloat) {
    if menuIndex != newIndex, let targetMenuCell = collectionView.cellForItem(at: IndexPath(item: newIndex, section: 0)) as? Example3MenuCell {
        targetMenuCell.transition = transition
        if let currentMenuCell = collectionView.cellForItem(at: IndexPath(item: menuIndex, section: 0)) as? Example3MenuCell {
            currentMenuCell.transition = 1.0 - transition
            
            let p1 = currentMenuCell.center
            let p2 = targetMenuCell.center
            
            if transition < 0.5 {
                if p1.x < p2.x {
                    let width = (p2.x - p1.x) * transition * 2.0 + 10
                    indicatorView.frame = CGRect(x: p1.x - 5, y: 37, width: width, height: 3.0)
                } else {
                    let width = (p1.x - p2.x) * transition * 2.0 + 10.0
                    indicatorView.frame = CGRect(x: p1.x + 5.0 - width, y: 37, width: width, height: 3.0)
                }
            } else {
                if p1.x < p2.x {
                    let width = (p2.x - p1.x) * (1.0 - transition) * 2.0 + 10.0
                    indicatorView.frame = CGRect(x: p2.x + 5.0 - width, y: 37, width: width, height: 3.0)
                } else {
                    let width = (p1.x - p2.x) * (1.0 - transition) * 2.0 + 10.0
                    indicatorView.frame = CGRect(x: p2.x - 5.0, y: 37, width: width, height: 3.0)
                }
            }
        } else {
            let p2 = targetMenuCell.center
            indicatorView.frame = CGRect.init(x: p2.x - 5.0, y: 37, width: 10, height: 3.0)
        }
    } else if let currentMenuCell = collectionView.cellForItem(at: IndexPath(item: menuIndex, section: 0)) as? Example3MenuCell {
        currentMenuCell.transition = 1.0 - transition
        
        let p1 = currentMenuCell.center
        indicatorView.frame = CGRect.init(x: p1.x - 5.0, y: 37, width: 10, height: 3.0)
    } else {
        collectionView.performBatchUpdates({
            self.collectionView.reloadData()
        }, completion: { (_) in
            self.menuTranstion(to: newIndex, transition: 0)
        })
    }
}
```
## 实现机制
`XZCarouselView` 使用三图实现轮播，但是三图只是为了方便计算，一般情况下，轮播图上只有两图参与轮播（在 `keepsTransitioningViews` 属性为 `true` 时，因为要显示前后的图片，需要同时加载 4 张图片）。

## 示例代码
在  [XZKit](https://github.com/mlibai/XZKit) 框架开源项目中，就有完整的 [XZCarouselView](https://github.com/mlibai/XZKit/tree/master/Projects/Example/CarouselViewExample) 测试代码，分别就三种常用场景分别写了示例，欢迎前往 [Star&Fork](https://github.com/mlibai/XZKit) 。


