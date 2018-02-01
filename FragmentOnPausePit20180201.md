**问题描述**

在用Viewpager和Fragment组合进行开发的时候，在Fragment的onPause回调中进行事件处理未达到预期效果

**问题经过**

	- 在项目Fragment中设置监听，在onResume开始监听，在onPause停止监听
	- 发现在切换Fragment或者在当前Fragment显示AlertDialog时监听并没有被禁用
	- 将所有可能导致当前Fragment不可见的原因单独进行监听处理，暂时解决了问题
	- 针对切换Fragment的操作，可以利用setUserVisibleHint(boolean isVisible)来进行识别，但是在Fragment中弹出全屏AlertDialog导致Fragment不可见的情况仍然没办法很好解决
	
**后续解决**

结合setUserVisibleHint和部分暴露接口来完整标记当前Fragment的可见状态