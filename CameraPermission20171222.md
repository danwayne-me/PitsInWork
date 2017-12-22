**问题简述**：根据[Android Developer Training Best Practice for Permissions](https://developer.android.com/training/articles/user-data-permissions.html#avoid_requesting_unnecessary_permissions)的描述，在不需要定制相机的情形下，通过Intent添加MediaStore.ACTION_IMAGE_CAPTURE的方式来唤醒系统Camera App并完成交互是最佳实践，但是在Android 7.0通过该方式调用抛出SecurityException

**问题经过**：

	- 学习System Permission Best Practice过程中，利用业务App对Intent方式进行尝试，抛出Security Exception
	- 动态获取Permission之后可以正常运行
	- 建立demo App 单独尝试无权限唤醒， 可以正常运行
	- 后去掉业务App AndroidManifest.xml中Camera权限声明之后可正常运行
	
**后续总结**：在Android官方文档中，MediaStore.ACTION_IMAGE_CAPTURE在默认情况下不需要权限即可唤醒Camera App， 但是在Android 7.0之后，**声明使用Camera权限的( Declared as using android.Manifest.permission.CAMERA )** 必须动态申请权限，否则会抛出SecurityException异常

所以综上，Best Practice For System Permission应该是：

	- 尽量少申请权限 （不是核心功能尽量用Intent代替）
	- 决定用Intent代替的功能，请不要在AndroidManifest中声明权限
	- 对于需要权限的，在AndroidManifest中声明，并在所有使用的地方动态申请 （很重要，不能假定一次申请成功就会在整个App生命周期一直存在）
	- 利用shouldShowRequestPermissionRationale()方法（ContextCompt中的API）在用户拒绝权限之后给出合理的解释 
	
> 由于系统动态申请权限的粒度是权限组，所以一个简单的权限反映给用户时就会是申请很多危险权限（e.g. 自动登录检测账号需要Get_Account权限，然而给用户的提示框是整个Contacts权限组，包括读写联系人，读取通话记录等），在这样的情况下，用户很可能直接拒绝权限导致App功能受损，所以，在用户拒绝权限之后给出合理的解释非常重要