# Service详解
Service可以理解为没有界面的Activity，其通常的作用是提供服务。也是执行在主线程中。（很多人会因为其没有界面，第一印象是在后台工作，会误以为是在子线程中执行。）

## 生命周期方法
- onCreate
当Service实例不存在时，会创建一个实例，创建时会执行此方法 。
- onStartCommand
当以startService方式执行服务时，Service会执行此方法。
- onBind
当以bindService方式执行服务时， Service会执行此方法。
- onUnbind
当
- onStop
当被stopService时， Serviece会执行此方法。
- onDestroy
当Service实例被销毁时，执行此方法。
- onStart 已废弃

## 两种不同的启动方式的异同
1. startService
此方式启动的Service，Service不受控制， 只负责执行onStartCommand代码，两者无法再有联系。
例子1
2. bindService
此方式启动Service，会存在调用者和Service生命周期关联。即调用者存活，则Service也存活，调用者被销毁，则Service也销毁。
并且，调用者和Service还可以交互。即调用者可以调用Service执行提供的各种操作。此种方式是通过bindService时返回一个binder对象，调用这持有该binder对象来执行相应的方法。
例子2
```
ServiceConnection connection = new ServiceConnection() {...};
bindService(bindIntent, connection, BIND_AUTO_CREATE);
```

## 如果想要Service存活时间长，还能交互
常常使用startService，再bindService方式。注意在想要销毁Service时，要对应着先stopService，再unBindService。

## Service与Thread的关系
我们常说服务是运行在后台， 因此特别容易与Thread关联起来。那么两者有啥关系呢？其实两者一点关系都没有。 这里需要大家搞清楚后台的概念， 这里说的后台指的是Service没有界面。 实际上Service是和Activity一样运行在主线程中的。
那么大家又会有疑问了，那岂不是也很容易发生ANR， 对，没错，但是我们使用Service时，常常是会新建一个子线程来处理耗时操作的。

进一步疑问， 为什么不直接使用子线程的方式来执行耗时操作。 这是因为Service是没有界面的。 因此不会像Activity一样有前台，后台的切换。

并且只要Service实例没有销毁， 则可以永远持有该工作线程，这样我们能够自有的控制工作线程。但如果是直接在Activity中新建工作线程，如果Acitvity被销毁后重建时无法再获得直线创建的工作线程的控制了。因此我们使用Service来执行这些需要长期运行或在后台运行的工作。


## ForegroundService和IntentService的使用
由于Service也是执行在主线程， 因此执行逻辑不能太耗时。往往是在Service中new Thread的方式执行耗时代码。为了免除这种繁琐，创造了一个类IntentService。此类已经替我们生成了一个任务线程， 且我们通过handler传递消息的方式来控制任务线程执行代码。
```
IntentService intentService = new IntentService() {
	public void handleIntent(Intent intent) {...}
}
```

前台服务， 在Service的onCreate方法中调用startForeground(...)来让自己成为前台进程。
前台进程会在状态栏中有一个驻守标志

## 线程优先级，由高到低
当系统的内存不足时, android系统将根据进程优先级选择杀死一些不太重要的进程. 进程优先级从高到低分别为:

##### 前台进程，以下的进程为前台进程：
a. 进程中包含处于前台的正与用户交互的activity;
b. 进程中包含与前台activity绑定的service;
c. 进程中包含调用了startForeground()方法的service;
d. 进程中包含正在执行onCreate(), onStart(), 或onDestroy()方法的service;
e. 进程中包含正在执行onReceive()方法的BroadcastReceiver.

##### 可视进程： 
a. 进程中包含未处于前台但仍然可见的activity(调用了activity的onPause()方法, 但没有调用onStop()方法). 典型的情况是运行activity时弹出对话框, 此时的activity虽然不是前台activity, 但其仍然可见.

b. 进程中包含与可见activity绑定的service.

##### 服务进程. 
进程中包含已启动的service
##### 后台进程. 
进程中包含不可见的activity(onStop()方法调用后的activity). 后台进程不会直接影响用户体验, 为了保证前台进程/可视进程/服务进程的运行, 系统随时都有可能杀死一个后台进程. 一个正确的实现了生命周期方法的activity处于后台时被系统杀死, 可以在用户重新启动它时恢复之前的运行状态.
##### 空进程. 
不包含任何处于活动状态的进程是一个空进程. 系统经常杀死空进程, 这不会造成任何影响. 空进程存在的唯一理由是为了缓存一些启动数据, 以便下次可以更快的启动.

## 跨进程Service
跨进程Service需要在manifest文件中在该Service就节点下添加android:process=":remote"。
然后该Service被启动时会在另一个进程中。但不能像普通的Service那样去bindService。 否则会报错。
应该使用aidl方式。
```
@Override  
public void onServiceConnected(ComponentName name, IBinder service) {  
    myAIDLService = MyAIDLService.Stub.asInterface(service);  
    try {  
        int result = myAIDLService.plus(3, 5);  
        String upperStr = myAIDLService.toUpperCase("hello world");  
        Log.d("TAG", "result is " + result);  
        Log.d("TAG", "upperStr is " + upperStr);  
    } catch (RemoteException e) {  
        e.printStackTrace();  
    }  
} 
```
