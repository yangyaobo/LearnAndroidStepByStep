# Intent详解
Intent在很多地方都使用到了， 可以理解未其是4大组件之间沟通，关联的信使。

## 使用方法
显式构造

	Intent intent = new Intent(context, ActivityB.class);
	// 方式二
	Intent intent2 = new Intent(context);
	intent2.setClass(ActivityB.class);
	
	// 方式三
	Intent intent3 = new Intent();
	Component comp = new Component(context, "packageName");
	intent3.setComponent(comp);

隐式构造法

	Intent intent = new Intent();
	intent.setAction(actionName);

## 携带数据
putExtra(name, value);
存储简单数据结构
putExtras(bundle);

addCategory(catgory);
setData(data)
setType
setDataAndType
getXXXextra(name,defVal);
getParcelable(name);
getSerialExtra(name);

createChooser(sendIntent,title); //存在多个相应该intent的应用时，建选择列表
## 注意点
setData与setType,都会清楚前面对方设置的数据。要想同时设置data和type，则调用setDataAndType
