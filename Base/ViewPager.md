# ViewPager的使用
参考[《ViewPager 详解》](http://blog.csdn.net/harvic880925/article/details/38453725)
ViewPager，可滑动切换的类似于幻灯片的控件。
## 使用：ViewPagerAdapter
### instantiateItem
此方法返回值表示与生成的itemView一一映射的一个Object。我们常常是以生成的View作为其代表，其实可以使用任何Object，只需要满足能做到一一映射，不会出现一个Object对应多个不同的View的情况。
### destroyItem
因为ViewPager的加载机制是保持固定的加载个数，在切换时，销毁过时的，腾出空位，生成新的View。因此在此方法就是销毁过时的view的操作，container.remove(item);
### getCount
### isViewFromObject
此方法就是根据instantiateItem返回的对应view的object来判断是否是一一对应的view

## 注意的方法
有时候ViewPager关联的数据未改变时，但知识data内部的item的成员变换时，调用notifyDatasetChanged会发送界面无反应。 因为源码中会做一个初步判断，如果position未变换，则不会从新生成view。 需要我们重写ViewPagerAdapter的方法getItemPosition,返回POSITION_NONE;

## 常用的方法
	// 获取当前显示的index
	getCurrentItem()
	// 设置当前显示第几页，从0开始
	setCurrentItem(int index)
	// 参数2为是否平滑滑动
	setCurrentItem(int index, boolean smoothScroll)
	// 设置预加载数量， 默认为1，即当前显示页面的左右各偏移1个。
	setOffsetScreenLimit(int num)

