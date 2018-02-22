**问题简述** ： 如何判断App是在前台还是后台

**问题经过** ：
	
	- 需要在App仅在后台运行的时候发送push
	- 利用Activity生命周期的回调无法解决App内部Activity切换场景
	- 利用ActivityManager获取当前运行的Task内的Activity的包名是否和App包名相同，或者获取当前运行process的importance是否为foreground，但是这些方式都需要遍历查找，性能较差
	
**后续解决** ：

	Android 官方提供了App级别的Activity生命周期管理回调ActivityLifecycleCallbacks，可以通过在onActivityStopped和onActivityStarted方法中进行计数或者修改标记 ( 修改标记的话，需要注意Activity切换的时候不同回调的调用顺序，可能在不同的实现中会有坑，优先使用计数法 )， 当计数为0的时候App在后台，否则App在前台 （因为至少有一个Activity调用过onStart但是没有调用onStop）