### Swift 3

Swift 1 确立了语言的基线: 安全, 快速, 现代. Swift 2 展现了 Swift 应该是什么, 未来怎么走: 面向协议的编程, 开源. Swift 3 更多的是清扫和规范: 新的 API 设计简洁干净, 减少了歧义, 溢出了很多 C 风格语法使代码风格更加一致, 可读性更高.

从语言使用的角度上, 大致做了如下修改或新特性:

###### API 的重命名和规范

* 避免歧义  <br />
方法名中应该包含所有需要的单词以减少阅读时的困惑.

* 删除协议名字的 'Type' 后缀

* 删除冗余的词

* 根据 Side Effect 来决定命名
> 方法命单词词性. e.g. array.sort() 对 array 进行排序, array.sorted() 返回一个新的 array
> 方法名长度缩短. stringByAppendingString(aString: String) 改为 appending(_ aString: String)

###### 引用 Objective-C 和 C 代码

很多 Objective-C Constants 在 Swift 中成了 Enum (Swift 3 中 Enum 小写字母开头)
```swift
enum HKQuantityTypeIdentifier : String {  
        case bodyMassIndex
        case bodyFatPercentage
        case height
        case bodyMass
        case leanBodyMass
}
```

C 的 APIs 原来是全局的方法和变量, 现在引入为变量使用. e.g. CGContext
```swift
context.lineWidth = 1  
context.strokeColor = CGColor(gray: 0.5, alpha: 1.0)  
context.drawPath(mode: .Stroke)
```

dispatch 的 APIs 也更加 Swifty
```swift
let queue = DispatchQueue(label: "com.example.imagetransform")  
queue.async {  
        let smallImage = image.resize(to: rect)
        DispatchQueue.main.async {
                imageView.image = smallImage
        }
}

let delay = DispatchTime.now() + .seconds(60)  
queue.after(when: delay) {  
        // ...
}
```

###### 语法
* C 风格的语法  <br />
自增 `++` 和 自减 `--` 运算符被移除
for 循环 `for (int i = 0; i < array.count; i++)` 也不再使用

###### 函数
	* 函数参数中 `let` 的显示使用被移除
	* 函数参数中 var 被移除
	* inout 调整为类型修饰, 而不是参数名修饰. fouc double(input: inout Int)
	* 同 inout 类似, `@noescape` 和 `@autoclosure` 也转变为类型修饰. `fouc foo(f: @noescape () -> ()) {}`
	* Curry 函数的声明语法被移除. `func foo(x: Int)(y: Int)` 类似语法应该用 `fouc foo(x: Int) -> (y: Int) -> Int` 这种语法替换

###### Enum  <br />
Enum 所有成员都改为小写, 枚举中必须在成员前加上左前缀点
```
// 之前只是在枚举外使用需要加, 现在枚举内部对 self 进行  switch, 也一定要加, 确保代码的一致性
enum Season {  
        case spring
        case summer
        case fall
        case winter

        var foo: String {
                switch self {
                case .spring:  return ...
                case .summer: return ...
                ...
                }
        }
}
```

有关联值的 enum, 在 case 后可以声明多个成员
```
enum MyEnum {  
        case case1(Int, Float)
        case case2(Float, Int)
}

switch value {  
case let .case(x, 2), let .case2(2, x):  
        print(x)
case .case1, .case2:  
        break
}
```

###### 访问级别
在 Swift 3 之前, 有三种访问级别:
> 公开 (public)  <br />
内部 (internal)  <br />
私有 (private)  <br />

默认的是 internal, 成员只能在 moudule 内可见. 如果要在 moudule 外使用, 应设置为 public. private 辨识改私有成员只能在其所在文件内使用

Swift 3 中多了一种访问级别 fileprivate, fileprivate 等同于之前的 private, 而 private 修改为之在其对应的作用域中可见.

###### Where

* Generic 声明

在 Generic 声明中, where 语句被移到了最后
```swift
// Swift 3 之前, 原来的方法
func anyCommonElements<T : SequenceType, U : SequenceType where  
        T.Generator.Element: Equatable,
        T.Generator.Element == U.Generator.Element>(lhs: T, _ rhs: U) -> Bool {
    ...
}

// Swift 3 中, 正确语法应该是
func anyCommonElements<T : SequenceType, U : SequenceType>(lhs: T, _ rhs: U) -> Bool  
        where
                T.Generator.Element: Equatable,
                T.Generator.Element == U.Generator.Element {
        ...
}
```

* 条件判断  <br />

where 在条件判断语句中被删掉, 改用逗号表示.
```swift
// 原来的guard判断
guard let x = opt1, y = opt2 where x == y else {}

// Swift 3 中应该为
guard let x = opt1, y = opt2, x == y else {}
```

* Implicitly Unwrapped Optional

原来隐式解析 Optional 类型



---

[API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
