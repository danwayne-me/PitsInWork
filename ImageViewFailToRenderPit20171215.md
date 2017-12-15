**问题简述** ： ImageView设置scaleType为CENTER并关闭硬件加速导致绘制失败

**问题经过** ：
	
	- 由于App内存管理机制不够完善，为了暂时缓解OOM的情况，在父View层级设置setLayerType为LAYER_TYPE_SOFTWARE, 来缩减一部分与GPU共享的内存
	- 测试同学发现Image Picker页第一格相机图标不显示，但是所有用Image Loading Framework加载的照片全都正常显示
	- 写测试Project进行测试
		- 测试替换convertView机制，问题不复现
		- 测试替换原生ImageView为CustomImageView， 问题不复现
		- 测试替换scaleType，问题不复现
		- 测试setLayerType -> LAYER_TYPE_SOFTWAER，重复上一步，在scaleType为CENTER时， 问题复现
		- 将问题场景简化为单ImageView， 遍历scaleType，只有CENTER复现
		- 在单ImageView情况下setImageResource， setImageDrawable，setBackground×××， 问题均复现
		- 将第一格ImageView设置为centerInside保证发版
		
**原因分析** ： 

	目前还在调研中，可能需要深入研究一下Android View绘制机制，了解GPU在其中作用的部分，来协助定位问题
	
**后续解决** ：

	从优化内存角度来讲，全局打开硬件加速，对于图片列表这类交互比较简单，单比较消耗资源的View，在View层级关闭硬件加速来节省内存是一个思路
	
	关闭硬件加速可能会导致部分绘制出现非预期情况，尽量避免在父View中设置关闭，依照场景取叶节点View进行设置比较稳妥（这个项目中在下个版本也会把父View中的设置转移到各个自View中进行细粒度设置）
	
	scaleType中的CENTER对于原图不进行缩放，可能会在绘制的时候节省一些资源（待调研），但是在关闭硬件加速的情况下可能会出现绘制失败的情况，建议尽量少用，用CENTER_INSIDE来替代（CENTER_INSIDE会在保证绘制内容的宽高比的情况下让绘制内容全部落在View内） 