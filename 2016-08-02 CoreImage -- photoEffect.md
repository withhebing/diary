## Core Image 的简单使用之一 -- 图片滤镜(以CIPhotoEffect..为例)

### Core Image 概述
> Core Image is an image processing and analysis technology designed to provide near real-time processing for still and video images. It operates on image data types from the Core Graphics, Core Video, and Image I/O frameworks, using either a GPU or CPU rendering path. Core Image hides the details of low-level graphics processing by providing an easy-to-use application programming interface (API). You don’t need to know the details of OpenGL or OpenGL ES to leverage the power of the GPU, nor do you need to know anything about Grand Central Dispatch (GCD) to get the benefit of multicore processing. Core Image handles the details for you.[1]

CoreImage是iOS5新加入的一个图像处理框架, 提供了强大高效的图像处理功能, 用来对基于像素的图像进行操作与分析.  
内置了很多强大的滤镜(Filter), 这些Filter提供了各种各样的效果, 并且还可以通过`滤镜链`将各种效果的Filter`叠加`起来形成强大的自定义效果.

### Core Image 架构 [2]
Core Image 有一个插件架构, 这意味着它允许用户编写自定义的滤镜并与系统提供的滤镜集成来扩展其功能. 此处不会用到 Core Image 的可扩展性, 提到它只是因为它影响到了框架的 API.  

Core Image 是用来最大化利用其所运行之上的硬件的. 每个滤镜实际上的实现, 即内核, 是由一个 GLSL (即 OpenGL 的着色语言) 的子集来书写的. 当多个滤镜连接成一个滤镜图表, Core Image 便把内核串在一起来构建一个可在 GPU 上运行的高效程序.  

只要有可能, Core Image 都会把工作延迟. 通常情况下, 直到滤镜图表的最后一个滤镜的输出被请求之前都不会发生分配或处理.  

为了完成工作, Core Image 需要一个称为上下文 (context) 的对象. 这个上下文是框架真正工作的地方, 它需要分配必要的内存, 并编译和运行滤镜内核来执行图像处理. 建立一个上下文是非常耗费性能的的, 所以要尽可能考虑到重用.

### 图片滤镜(以CIPhotoEffect..为例)

思路: originalUIImage -> inputCIImage -> (CIFilter) -> outputCIImage -> (CIContext) -> CGImage -> UIImage

三个主要对象:
* CIImage -- 保存图像数据. 它可以从UIImage, 图像文件, 或像素数据中构造.
* CIFilter -- 包含的字典对各种滤镜定义了各自的属性. 滤镜有很多种，比如裁剪/色彩/模糊/像素等.
  - `由于CoreImage的插件结构, 大多数滤镜属性并不是直接设置的, 而是通过键值编码(KVC)设置`
* CIContext -- 图像处理都是在CIContext中完成的.
为了完成工作, CoreImage需要一个称为上下文(context)的对象. 这个上下文是框架真正工作的地方, 它需要分配必要的内存, 并编译和运行滤镜内核来执行图像处理.
  - `创建一个上下文(context)是非常耗性能的, 应重复使用.`

```objc
/// 传入滤镜名称(e.g. @"CIPhotoEffectFade"), 输出处理后的图片
- (UIImage *)outputImageWithFilterName:(NSString *)filterName {
    // 1.
    // 将UIImage转换成CIImage
//    CIImage *ciImage = self.originalImage.CIImage; // 未分配内存空间
    CIImage *ciImage = [[CIImage alloc] initWithImage:self.originalImage];
    // 创建滤镜
    self.filter = [CIFilter filterWithName:filterName keysAndValues:kCIInputImageKey, ciImage, nil];

    // 2.
    // 渲染并输出CIImage
    CIImage *outputImage = [self.filter outputImage];

    // 3.
    // 获取绘制上下文
    self.context = [CIContext contextWithOptions:nil];
    // 创建CGImage
    CGImageRef cgImage = [self.context createCGImage:outputImage fromRect:[outputImage extent]];
    // 获取图片 (不要直接`imageWithCIImage:`, CIContext重复, 性能)
    UIImage *image = [UIImage imageWithCGImage:cgImage];
    // 释放CGImage
    CGImageRelease(cgImage);

    return image;
}
```

CoreImage提供的滤镜很多, 不便记忆. 可通过`打印`或`po filter.attributes`方式查看相应信息.
```objc
// 打印滤镜名称
// `kCICategoryBuiltIn`内置; `kCICategoryColorEffect`色彩
- (void)showFilters {
    NSArray *filterNames = [CIFilter filterNamesInCategory:kCICategoryColorEffect];
    for (NSString *filterName in filterNames) {
        NSLog(@"%@", filterName);
        // CIFilter *filter = [CIFilter filterWithName:filterName];
        // NSDictionary *attributes = filter.attributes;
        // NSLog(@"%@", attributes); // 查看属性
    }
}
```

```objc
// 自动调整样式
- (UIImage *)photoEffectAutoAdjust {
    CIImage *ciImage = [[CIImage alloc] initWithImage:self.filterlessImage];
    NSArray *filters = [ciImage autoAdjustmentFiltersWithOptions:nil];
    CIImage *outputImage;
    for (CIFilter *filter in filters) {
        [filter setValue:ciImage forKey:kCIInputImageKey];
        [filter setDefaults];
        outputImage = filter.outputImage;
    }

    self.context = [CIContext contextWithOptions:nil];
    CGImageRef cgImage = [self.context createCGImage:outputImage fromRect:[outputImage extent]];
    UIImage *image = [UIImage imageWithCGImage:cgImage];
    CGImageRelease(cgImage);

    return image;
}
```

### 效果图如下:

###### original 原图

![original](http://ob9xd51py.bkt.clouddn.com/image_original.png)

##### photoEffect 效果图
对应滤镜 |
:----: | :----: | :----:
AutoAdjust 自动 | Instant 怀旧 | Process  冲印
Chrome     铬黄 | Mono    单色 | Tonal    色调
Fade       褪色 | Noir    黑白 | Transfer 岁月

![Filter](http://ob9xd51py.bkt.clouddn.com/image_filters.png)

附: [demo](https://github.com/withhebing/ImageFilter)

---
###### 分享:  

* 我一直在用这个: [developer.xamarin.com/api/type/CoreImage](https://developer.xamarin.com/api/type/CoreImage.CIPhotoEffectNoir/), 查看起来比较方便, 除了Core Image还有很多其它框架.  
* [ObjC 中国 - 期刊](https://objccn.io/issues/), 讲得都很精当, @onevcat大神等组建; 或者直接去[objc.io](https://www.objc.io/issues/), 不需搭梯子的哦.  
* [Core Image Reference Collection](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171) 和 [Core Image Programming Guide](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/CoreImaging/ci_intro/ci_intro.html#//apple_ref/doc/uid/TP30001185) 是Core Image的官方文档集.  
* [Core Image Filter Reference](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/index.html#//apple_ref/occ/cl/CIFilter) 包含了Core Image所提供图像滤镜的完整列表以及用法示例.

---
[1]:https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/CoreImaging/ci_intro/ci_intro.html#//apple_ref/doc/uid/TP30001185 "Core Image Programming Guide"
[2]:https://objccn.io/issue-21-6/ "Core Image 介绍"


ThanksTo:
```
[1]:https://developer.apple.com/library/ios/documentation/GraphicsImaging/Conceptual/CoreImaging/ci_intro/ci_intro.html#//apple_ref/doc/uid/TP30001185 "Core Image Programming Guide"
[2]:https://objccn.io/issue-21-6/ "Core Image 介绍"
```
