色彩的三要素 —— 色相、明度、纯度
* 色相（表，表现）：即色彩的相貌和特征。自然界中色彩的种类很多，色相指色彩的种类和名称。如；红、橙、黄、绿、青、蓝、紫等等颜色的种类变化就叫色相。  

* 明度（表，面子）：指色彩的亮度或明度，也叫**明亮度**。颜色有深浅、明暗的变化。比如，深黄、中黄、淡黄、柠檬黄等黄颜色在明度上就不一样，紫红、深红、玫瑰红、大红、朱红、桔红等红颜色在亮度上也不尽相同。这些颜色在明暗、深浅上的不同变化，也就是色彩的又一重要特征一一明度变化。   
色彩的明度变化有许多种情况，一是不同色相之间的明度变化。如：白比黄亮、黄比橙亮、橙比红亮、红比紫亮、紫比黑亮；二是在某种颜色中加白色，亮度就会逐渐提高，加黑色亮度就会变暗，但同时它们的纯度(颜色的饱和度)就会降低，三是相同的颜色，因光线照射的强弱不同也会产生不同的明暗变化。。     

* 纯度（里，里子）：指色彩的鲜艳程度，也叫**饱和度**。原色是纯度最高的色彩。颜色混合的次数越多，纯度越低，反之，纯度则高。原色中混入补色，纯度会立即降低、变灰。物体本身的色彩，也有纯度高低之分，西红柿与苹果相比，西红柿的纯度高些，苹果的纯度低些。   
有好的表现，必然有一定的面子跟里子做基础。  

关于水的色彩观念 —— 水，首先是无色的；其次是白色的（可以降低纯度，提高明度；最后是任意色的（经光照反色多彩或折射七彩）。

---
前面也有知友推荐过的: 

[色生心中：人性化的HSL模型](http://cdc.tencent.com/2011/05/09/%E8%89%B2%E7%94%9F%E5%BF%83%E4%B8%AD%EF%BC%9A%E4%BA%BA%E6%80%A7%E5%8C%96%E7%9A%84hsl%E6%A8%A1%E5%9E%8B/#)

  > HSL色彩模型又是什么？  
  HSL同样使用了3个分量来描述色彩，与RGB使用的三色光不同，HSL色彩的表述方式是：H(hue)色相，S(saturation)饱和度，以及L(lightness)亮度。  
  听起来一样复杂？稍后你就会发现，与“反人类”的RGB模型相比，HSL是多么的友好。

* HSL的H(hue)分量，代表的是人眼所能感知的颜色范围，这些颜色分布在一个平面的色相环上，取值范围是0°到360°的圆心角，每个角度可以代表一种颜色。色相值的意义在于，我们可以在不改变光感的情况下，通过旋转色相环来改变颜色。在实际应用中，我们需要记住色相环上的六大主色，用作基本参照：360°/0°红、60°黄、120°绿、180°青、240°蓝、300°洋红，它们在色相环上按照60°圆心角的间隔排列。

  ![HSL之色相](http://cdc.tencent.com/wp-content/uploads/2011/05/31.png)

* HSL的S(saturation)分量，指的是色彩的饱和度，它用0%至100%的值描述了相同色相、明度下色彩纯度的变化。数值越大，颜色中的灰色越少，颜色越鲜艳，呈现一种从理性(灰度)到感性(纯色)的变化。

  ![HSL之饱和度](http://cdc.tencent.com/wp-content/uploads/2011/05/41.png)

* HSL的L(lightness)分量，指的是色彩的明度，作用是控制色彩的明暗变化。它同样使用了0%至100%的取值范围。数值越小，色彩越暗，越接近于黑色；数值越大，色彩越亮，越接近于白色。

  ![HSL之明度](http://cdc.tencent.com/wp-content/uploads/2011/05/51.png)

---

采用HSL颜色体系后, 更能便捷地选取自己偏好或当前合适的颜色.   
特别对于随机色的表示, 采用RGB可能不太友好(有时偏暗, 外观不佳), 可试试HSL. 如下所示(仅示范, 细心调试后更佳): 

````objc
    CGFloat hue = ( arc4random() % 256 / 256.0 );               //  0.0 to 1.0
    CGFloat saturation = ( arc4random() % 128 / 256.0 ) + 0.5;  //  0.5 to 1.0, away from white
    CGFloat brightness = ( arc4random() % 128 / 256.0 ) + 0.5;  //  0.5 to 1.0, away from black
    UIColor *color = [UIColor colorWithHue:hue saturation:saturation brightness:brightness alpha:1.0];
````