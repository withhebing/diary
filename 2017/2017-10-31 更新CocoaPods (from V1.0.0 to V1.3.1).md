(这次更新主要涉及到ruby源更新的问题)

1. 查看ruby源  <br/>
	 gem sources -l
2. 移除原有ruby源 (跟上一步查看的保持一致)  <br/>
	 gem sources --remove https://ruby.taobao.org/
3. 添加国内最新ruby源  <br/>
	 gem sources -a https://gems.ruby-china.org/
4. 查看是否添加成功  <br/>
	 gem sources -l
5. 安装CocoaPods  <br/>
	 sudo gem install -n /usr/local/bin cocoapods
6. 安装完成后查看pod版本  <br/>
	 pod --version
7. 更新Podspec索引文件, 创建本地索引库 (耗时可能较长)  <br/>
	 pod setup

<接下来就可正常使用啦>
