###1、 查看app的包名和actiity

	adb logcat | grep start
	
	adb logcat > log.txt

可以保存log信息到本地文件进行分析
###2、 启动时间
冷启动：app首次启动，进程首次创建
热启动：app通过home或back及应用切换等退到后台未被杀死，再次换起，
冷启动和热启动的命令相同：

	adb shell am start -W -n package/activity

冷启动android自带浏览器

	C:\Users\lo>adb shell am start -W -n com.android.browser/.BrowserActivity
	Starting: Intent { cmp=com.android.browser/.BrowserActivity }
	Status: ok
	Activity: com.android.browser/.BrowserActivity
	ThisTime: 808
	TotalTime: 808
	WaitTime: 823
	Complete

冷启动的停止app的命令：

	adb shell am force-stop package

热启动停止命令：
	
	adb shell input keyevent 3

###3、cpu使用率

	adb shell dumpsys cpuinfo | grey packagename

测试自带浏览器
	adb shell dumpsys cpuinfo | grey com.andoid.browser

###4 、内存
获取内存的命令：
adb shell top
注意两个值：Vss(虚拟耗用内存)
Rss（实际使用内存）