# Activity详解
作为4大组件中最重要， 我们使用最多的一个组件。下面跟我来逐步熟悉，掌握她。
Activity可以呈现界面， 可以交互等等。
先看生命周期
## 生命周期
- onCreate
- onRestart
- onStart
- onResume
- onPause
- onStop
- onDestroy

来图更清晰
![](http://i2.piimg.com/567571/75a197c753928d46.png)
上图的生命周期是一对一对的，onCreate对应onDestroy,onStart对应onStop其他类推。onStart方法执行时，界面可见。onResume方法执行时，已经获得焦点。即用户出门屏幕或者按键时，事件会传递给该Activity。

## Activity切换时生命周期过程
ActivityA跳转到ActivityB。
A.onPause--> B.onCreate--> B.onStart--> B.onResume--> A.onStop
如果按back键，则B.onPause--> A.onRestart--> A.onStart--> A.onResume--> B.onStop--> B.onDestroy

## 横竖屏切换时生命周期
1. 切换为横屏时
onSaveInstanceState
onPause
onStop
onDestroy
onCreate
onStart
onRestoreInstanceState
onResume
2. 再切换未竖屏时
onSaveInstanceState
onPause
onStop
onDestroy
onCreate
onStart
onRestoreInstanceState
onResume
onSaveInstanceState
onPause  第2遍
onStop
onDestroy
onCreate
onStart
onRestoreInstanceState
onResume
## 在manifest文件中设置了该Activity节点的 android：configChange="orientation"时
切换为横屏同上， 但切换未竖屏时， 只执行1遍生命周期，+ onConfigChanged方法

## android：configChange="orientation|keyboardHidden"时.
1. 切换未横屏时，执行onConfigChanged
2. 切换为竖屏时  执行2次, 即onConfigChanged, onConfigChanged
## **注意：有时还得在android:configChange=后加上screenSize**即"orientation|keyboardHidden|screenSize"

## 数据的保存与恢复
- onSaveInstanceState
此方法执行的时机是 当该activity失去焦点，但不是关闭时。如用户按Home键或者启动另一个activity，但自身并没有finish。根据Android优先级。有可能在内存不足时回收非前台的组件。因此避免被回收，且重新回到该activity时能恢复原貌，需要做好数据保存工作，以应对这种被动回收后重回的情况。
- onRestoreInstanceState
当activity被动onDestroy了后， 又重新回到该activity。 则此方法会执行。 但如果该activity没有被destroy，则不需要恢复原貌， 因为根本不需要恢复。没有被destroy嘛。直接再显示onRestart，onStart,onResume就行了。

## startActivityForResult
当需要唤起的activity传递会一个结果值时，使用此方法。

## onActivityResult
当使用startActivityForResult方法后，回到该activity时，会执行此方法。能收到传递会的数据

## FragmentActivity中startActivityForResult特点
参考：[](http://www.diycode.cc/topics/431)
1. 各自发起的startActivityForResult, 各自的onActivityResult都能接收到。
2. FragmentActivity发起的startActivityForResult， fragment接受不到。因为被屏蔽了。
3. Fragment发起的forResult， activity能接收到， 但是requestCode已经变了。 因为做了一个<<16的映射。
4. requetCode都必须小于0xffff。 但由于最终调用的都是activity的forResult，Fragment发起的forResult。在最终传递给activity的forResult时是大于0xffff的， 因为有一个(requestIndex+1)<<16 + requestCode&0xffff的映射. 前半部分必定大于0xffff

## 启动模式
- standard
默认启动模式，每一次启动一个相同的activity都会在activity栈顶新建一个activity
- singleTop
当被启动的activity已经有一个实例在栈顶时，不会新建activity实例，只会调用栈顶实例的onNewIntent方法
- singleInstance
当任务栈中已经有一个待启动的Acivity的实例时，不会新建实例，而是把已经存在的实例之上的activity全部出栈，即finish掉。让已经存在的activity实例到栈顶。执行onNewIntent方法
- singleTask
此模式的activity会单独享用一个任务栈。该任务栈只会有该activity一个activity对象。示例说明该模式的特殊性
A，B，C3个acitivy，A，C都standard模式。B为singleTask模式。当A启动B， B又启动C，在C时，按back键返回。 会返回到A， 而不是B。
**出栈操作永远只是出同一个任务栈的activity，而不是依据启动顺序**

##其他方法
- onNewIntent
当启动模式是除了standard外的另外一种时都有可能执行到此方法。

## 易忽略的的问题
当执行了onRestart方法，则一定会执行onStart方法，即使在onRestart中调用finish(), 仍然会执行onStart方法。