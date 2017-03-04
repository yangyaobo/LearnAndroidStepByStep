# BroadcastReceiver详解
[《参考：Android 广播相关的都在这儿》](http://mp.weixin.qq.com/s/khUeEW0CRaSlWAt2ZNLJKw)
广播接收者，常常用来作为组件之间通知的之用。
## 注意点
在onReceive方法中不要执行耗时操作， 如果在onReceive方法中10s还没执行完毕， 则会ANR。
广播执行完后就会销毁。

onReceiver方法返回值不同，可以氛围粘性广播。非粘性广播。
STICKY。

## 使用方式
sendBroadcast(intent)
sendOrderedBroadcast
abortBroadcast

registerReceiver(intentFilter, receiver)
unregisterReceiver(receiver)
## 广播的分类
- 无序广播
无序广播无法控制接受着接收到广播的先后顺序。
- 有序广播
通过在manifest中配置Receiver节点的 priority="1000"来设置优先级。数字越大，优先级越高。
sendOrderedBroadcast(...)
有序广播可以取消，abortBroadcast。 可以改变后续接受者接收到的intent信息setResult(xxx)

## 注册广播方式
1. 静态注册
静态注册，就是在manifest文件中注册
2. 动态注册
代码注册， 使用registerBroadcast(...),切记要unregisterBroadcast()避免内存泄漏

## 最终接受者广播
有点类似与try catch finally中的finally。 该广播最后一定会被某个接受者接收到，哪怕之前已经被abort掉了。

## 本地广播
LocalBroadcastManager.getInstance(context).sendBroadcast(...)
LocalBroadcastManager.getInstance(context).registerReceiver(...)
LocalBroadcastManager.getInstance(context).unregisterReceiver(...)

## 延伸阅读
在某些场景下，完全可以使用**EventBus**来代替Broadcast。[《EventBus使用说明》]()
