**问题描述**：用ButterKnife进行BindView时候，如果在xml中使用<include\>标签，View绑定行为不确定

**问题经过**：

	- 在写UI的时候，经常使用<include>标签，在用ButterKnife处理这种情况的时候，一般都会定义内部类，然后根据layout的id在内部类进行绑定
	- 尝试直接根据layout内部View的id进行绑定，绑定成功
	- 在<include>标签外定义同id的View进行绑定，会绑定第一个id匹配的View，后续View会被忽略
	
**结论**：ButterKnife在View绑定的实现中，会将<include\>中的view和根xml文件中的view进行同等处理，在多数情况下，可以直接对layout内部view进行绑定，**但是，为了保持更好的代码可维护性，最好还是用内部类的方式，根据layout的id进行区分绑定**。 否则哪天另一个小伙伴加了一个同id的view，就可以看各种奇妙的bug了...