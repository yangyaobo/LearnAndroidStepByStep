# Fragment的使用
Fragment是在3.0后开始引入的一个新特性，是为了解决多尺寸的问题而诞生的一个类。有点像一个功能弱化的activity， 因为其拥有自己的生命周期和接收、处理用户的事件，这样就不必在Activity写一堆控件的事件处理的代码了。

## 生命周期
见图
![](http://img.blog.csdn.net/20140719225005356?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG1qNjIzNTY1Nzkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
图片来自鸿洋
onAttach
onCreate
onCreateView
onActivityCreated
onStart
onResume
onPause
onStop
onDestroyView
onDestroy
onDetach
## 使用方式
静态使用：把Fragment当一个普通的空间，就像Button一样。
动态使用：使用一个Frame Layout做占位符，这个FrameLayout所占据的区域就是动态添加的Fragment可占据的最大区域。通过FragmentManager来进行增，删，添加， 因此，显示等操作。

## 动态使用Fragment时，各种方法的异同
remove， 会销毁Fragment实例
detach， 会销毁View，但不会销毁实例
hide，不会销毁实例， 也不会销毁View。因此常用在需要保存用户现场的操作

## 创建MenuItem以及处理MenuItem逻辑
在onCreate方法中，调用setHasOptionsMenu(true);
然后重写onCreateOptionsMenu()方法
如果想在Fragment中处理MenuItem的点击，则可以实现onoptionsItemSelected方法

## 注意点：重叠现象， getActivity控制针
参考[《Fragment全解析系列》](www.jianshu.com/p/d9143a92ad94/)
在我们使用过程中，往往会发生层叠的现象，那是因为当activity置于后台时， 被回收了，待重新回到activity时，由于恢复现场时，会重建activity， 因此在onCreate方法中创建Fragment。然后由于要恢复现场， 即执行onRestoreInstanceState,会把保存的Fragment恢复，这里就又1个Fragment了， 因此发送了重叠。解决办法是在onCreate方法中判断savedInstanceState==null, 未null才创建Fragment； 如果是使用hide， show来控制Fragment时，由于不会保存Fragment的hide还是show状态，当内存重启时，就都按照默认show状态恢复。因此是多个Fragment都显示的重叠情况。（support24版本已经修复此问题。）
2. Fragment中调用getActivity()，结果activity为null导致错误。没有注意时机。可以用一个成员变量来持有mActivity。

## 一些使用建议
1. 对Fragment传递数据，建议使用setArguments(Bundle args)，而后在onCreate中使用getArguments()取出，在 “内存重启”前，系统会帮你保存数据，不会造成数据的丢失。和Activity的Intent恢复机制类似。

2、使用newInstance(参数) 创建Fragment对象，优点是调用者只需要关系传递的哪些数据，而无需关心传递数据的Key是什么。

3、如果你需要在Fragment中用到宿主Activity对象，建议在你的基类Fragment定义一个Activity的全局变量，在onAttach中初始化，这不是最好的解决办法，但这可以有效避免一些意外Crash。

## getFragmentManager 还是getChildFragmentManager
在与ViewPager结合使用时，传给FragmentPagerAdapter的FragmentManager要根据ViewPager是Activity中的控件还是Fragment中的控件，而选择getFragmentManager还是getChildFragmentManager.
