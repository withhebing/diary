# UIStackView to resize/layout
(IBOutlet this time)

> 自适应、适配、布局这几个关键词一直伴随着iOS开发，从以前的单一尺寸屏幕，到现在的多尺寸屏幕，Apple一直致力于让开发人员尽可能少在这些事上耗费过多的精力，所以Apple在2012年推出了Auto Layout特性，2014年又推出了Adaptive Layout、Size Classes，2015年iOS 9中又增加了新的控件：UIStackView。这些无一不是我们开发者做适配的利器。

#### UIStackView简介

[UIStackView Class Reference](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/index.html)
> The UIStackView class provides a streamlined interface for laying out a collection of views in either a column or a row. Stack views let you leverage the power of Auto Layout, creating user interfaces that can dynamically adapt to the device’s orientation, screen size, and any changes in the available space. The stack view manages the layout of all the views in its arrangedSubviews property. These views are arranged along the stack view’s axis, based on their order in the arrangedSubviews array. The exact layout varies depending on the stack view’s axis, distribution, alignment, spacing, and other properties.
![workflow](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/Art/uistack_hero_2x.png)

UIStackView提供了一个高效的接口用于平铺一行或一列的视图组合。对于嵌入到StackView的视图，你不用再添加自动布局的约束了。Stack View管理这些子视图的布局，并帮你自动布局约束。

StackView其实一个视图容器，不过它会对它的子视图根据一定规则自动布局，将子视图按栈的排列方式进行布局。

#### UIStackView主要的属性

* Axis 方向   
StackView有水平和垂直两个方向的布局模式：
![axis](http://img2.tuicool.com/u2maa2m.png!web)

* Spacing 间隔   
StackView可以设置子视图之间的间隔：
![distribution](http://img2.tuicool.com/RRBJJfY.png!web)

* Alignment 对齐方式  
StackView可以设置子视图的对齐方式（水平方向和垂直方向的该属性值有所区别）：
![alignment](http://img1.tuicool.com/ZveMviB.png!web)

  - Fill：子视图填充StackView。
  - Leading：靠左对齐。
  - Trailing：靠右对齐。
  - Center：子视图以中线为基准对齐。
  - Top：靠顶部对齐。
  - Bottom：靠底部对齐。
  - First Baseline：按照第一个子视图中文字的第一行对齐。
  - Last Baseline：按照最后一个子视图中文字的最后一行对齐。

* Distribution 分布方式  
StackView可以设置子视图的分布方式：
![](http://img0.tuicool.com/N3YbIvf.png!web)
  - Fill：默认分布方式。
  - Fill Equally：子视图的高度或宽度保持一致。
  - Fill：Proportionally：按**比例**分布。
  - Equal Spacing：子视图保持同等间隔。
  - Equal Centering：每个子视图中心线之间保持一致。

* baselineRelativeArrangement  
determines whether the vertical spacing between views is measured from the baselines.

* layoutMarginsRelativeArrangement  
determines whether the stack view lays out its arranged views relative to its layout margins.

#### UIStackView的嵌套

> Typically, you use a single stack view to lay out a small number of items. You can build more complex view hierarchies by nesting stack views inside other stack views.

既然UIStackView继承了UIView，那么UIStackView也可以看做是一个UIView而被包含在UIStackView内，亦及嵌套使用。

![UIStackView的嵌套](http://img0.tuicool.com/ZfqYFfr.png!web)

###### 注意！

> Be careful to avoid introducing conflicts when adding constraints to views inside a stack view. As a general rule of thumb, if a view’s size defaults back to its intrinsic content size for a given dimension, you can safely add a constraint for that dimension.

```objc
1. Fill的方向垂直于Axis
2. 有时可能排版较乱，除了设置属性来布局之外，我们还可以手动添加属性，且"手动添加的享有更高的优先级"。
(Apple官方建议开发人员应优先采用StackView来设计用户界面，然后再根据实际需求来添加约束)
```

#### 后记

> The UIStackView is a nonrendering subclass of UIView; that is, it does not provide any user interface of its own. Instead, it just manages the position and size of its arranged views. As a result, some properties (like backgroundColor) have no effect on the stack view. Similarly, you cannot override layerClass, drawRect:, or drawLayer:inContext:.

诚然UIStackView不是万能的，但是无疑它可以在布局和自适应方面给开发者带来便利，在恰当的情形下使用StackView可以事半功倍。而且因为UIStackView是UIView的子类，所以也可以将动画效果作用于UIStackView上，在方便布局之余还能提高用户体验。让我们用起来吧。

> 隔桌安卓小伙伴不满了，这不就是`LinearLayout`吗？
额 -_-!

ThanksTo:

UIStackView Class Reference -- https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/index.html#//apple_ref/occ/instp/UIStackView/distribution  
UIStackView小窥 -- http://www.devtalking.com/articles/uistackview/?utm_source=tuicool&utm_medium=referral  
iOS 9: UIStackView 浅析 -- http://www.jianshu.com/p/184e3fb94bd8

----

使用UIStackView可以很方便快捷的设置以下布局：

![lineLayout](http://upload-images.jianshu.io/upload_images/37334-581e8ce74a510501.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240/format/jpg)

![example](http://upload-images.jianshu.io/upload_images/37334-fde47d3da6775366.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240/format/jpg)

// 纯代码的下次再总结
