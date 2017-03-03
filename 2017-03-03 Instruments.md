### Instruments

##### 功能
* 分析一个或多个进程的行为
* 记录一系列用户的动作并相应他们, 可靠地在线这些事件并收集多次运行的数据
* 创建自定义 DTrace instruments 来分析系统和应用程序的行为
* 保存用户界面记录和 instruments 的配置为模板, 并从 Xcode 访问

##### 可进行的操作
* 追查代码中难以重现的问题
* 对程序进行性能分析
* 自动化测试代码
* 对程序进行压力测试
* 进行一般的系统级故障诊断
* 对代码如何工作更深入了解

##### 运行环境
Xcode 3.0 和 Mac OS X 10.5 及其以后

> 跟踪文档(trace documents)

###### template

1. Blank
* Activity Monitor
* Allocations
* Cocoa Layout
* Core Animation
* Core Data
* Counters
* Energy Log
* File Activity
* Leaks
* Metal System Trace
* Network
* OpenGL ES Analysis
* System Trace
* System Usage
* Time Profiler
* Zombies

###### 关键特性 (Trace document key features)

| Feature | Description |
| :--: | :--: |
| Instruments pane | This pane holds the instruments you want to run. You can drag instruments into this pane or delete them. You can click the inspector button in an instrument to configure its data display and gathering parameters.
| Track pane | The track pane displays a graphical summary of the data returned by the current instruments. Each instrument has its own “track,” which provides a chart of the data collected by that instrument. The information in this pane is read-only. You do use this pane to select specific data points you want to examine more closely, however.
| Detail pane | The Detail pane shows the details of the data collected by each instrument. Typically, this pane displays the explicit set of “events” that were gathered and used to create the graphical view in the track pane. If the current instrument allows you to customize the way detailed data is displayed, those options are also listed in this pane.
| Extended Detail pane | The Extended Detail pane shows even more detailed information about the item currently selected in the Detail pane. Most commonly, this pane displays the complete stack trace, timestamp, and other instrument-specific data gathered for the given event.
| Navigation bar | The navigation bar shows you where you are and how you got there. It includes two menus—the active instrument menu and the detail view menu. You can click entries in the navigation bar to select the active instrument and the level and type of information in the detail view.
