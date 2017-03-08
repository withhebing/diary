#  Xcode中查看方法的调用者列表 (Method callers in Xcode)

> reference: [In Xcode, how can I find all caller functions of a specific function?](http://stackoverflow.com/questions/2038257/xcode-find-caller-functions/11112091#11112091)

1> 这种方式可以直接查看**类和方法**的**继承及调用列表**
1. 在代码中点击(或选中)方法名
2. 点击代码编辑区域左上角的`”方块”图标`,会弹出一个弹出窗口。
3. 在弹出窗口中选择`Callers`,右侧就有列出所有调用此方法的位置;选择`Callees`则会显示此方法调用的所有其他方法。

或者 `Command + Enter`到辅助编辑器 -> Mannal中点选Callers/Callees

2> `双指单击`方法名(右击鼠标效果)弹出 pop-up menu -> `Find Call Hierachy`

3> 点击方法名 -> `Shift + Control + Command + H` (需双手操作)

4> `Command + 3`to Show the Find Navigator -> `find` -> `Call Hierachy` -> 输入方法名 -> Enter (太繁杂)
