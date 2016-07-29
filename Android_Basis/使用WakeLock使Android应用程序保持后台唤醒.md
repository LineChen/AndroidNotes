   在使用一些产品列如微信、QQ之类的，如果有新消息来时，手机屏幕即使在锁屏状态下也会亮起并提示声音，这时用户就知道有新消息来临了。但是，一般情况 下手机锁屏后，Android系统为了省电以及减少CPU消耗，在一段时间后会使系统进入休眠状态，这时，Android系统中CPU会保持在一个相对较 低的功耗状态。针对前面的例子，收到新消息必定有网络请求，而网络请求是消耗CPU的操作，那么如何在锁屏状态乃至系统进入休眠后，仍然保持系统的网络状 态以及通过程序唤醒手机呢？答案就是Android中的WakeLock机制。
首先看看官方的解释：

```
PowerManager:This class gives you control of the power state of the device.
PowerManager.WakeLock: lets you say that you need to have the device on.
```

PowerManager 负责对Android设备电源相关进行管理，而系统通过各种锁对电源进行控制，WakeLock是一种锁机制，只要有人拿着这把锁，系统就无法进入休眠阶 段。既然要保持应用程序一直在后台运行，那自然要获得这把锁才可以保证程序始终在后台运行。之前我做过一个需求是要在后台跑一个Service执行轮询， 但发现一段时间以后，轮询就中断了（我测试是二十分钟后请求停止），但重新解锁屏幕后，轮询请求又开始了，后来在Stackoverflow上找到的 WakeLock的用法，试了一下，还挺管用。在使用这个方法之前，我把Service置成前台Service等方法都不奏效，不知在这个需求上大家是否 还有更好的办法，大家可以留言和我讨论！


接下来看如何使用WakeLock：

```java

	 WakeLock wakeLock = null;  
	     //获取电源锁，保持该服务在屏幕熄灭时仍然获取CPU时，保持运行  
	     private void acquireWakeLock()  
	     {  
	         if (null == wakeLock)  
	         {  
	             PowerManager pm = (PowerManager)this.getSystemService(Context.POWER_SERVICE);  
	             wakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK|PowerManager.ON_AFTER_RELEASE, "PostLocationService");  
	            if (null != wakeLock)  
	             {  
	                 wakeLock.acquire();  
	             }  
	         }  
	     }  
	       
	     //释放设备电源锁  
	     private void releaseWakeLock()  
	     {  
	         if (null != wakeLock)  
	         {  
	             wakeLock.release();  
	             wakeLock = null;  
	         }  
	     }  

```

上面第一个方法是获取锁，第二个方 法是释放锁，一旦获取锁后，及时屏幕在熄灭或锁屏长时间后，系统后台一直可以保持获取到锁的应用程序运行。获取到PowerManager的实例pm后， 再通过newWakeLock方法获取wakelock的实例，其中第一个参数是指定要获取哪种类型的锁，不同的锁对系统CPU、屏幕和键盘有不同的影 响，第二个参数是自定义名称。
各种锁的类型对CPU 、屏幕、键盘的影响：

- PARTIAL_WAKE_LOCK:保持CPU 运转，屏幕和键盘灯有可能是关闭的。
- SCREEN_DIM_WAKE_LOCK：保持CPU 运转，允许保持屏幕显示但有可能是灰的，允许关闭键盘灯
- SCREEN_BRIGHT_WAKE_LOCK：保持CPU 运转，允许保持屏幕高亮显示，允许关闭键盘灯
- FULL_WAKE_LOCK：保持CPU 运转，保持屏幕高亮显示，键盘灯也保持亮度
- ACQUIRE_CAUSES_WAKEUP：强制使屏幕亮起，这种锁主要针对一些必须通知用户的操作.
- ON_AFTER_RELEASE：当锁被释放时，保持屏幕亮起一段时间


最后别忘了声明权限：

```
<uses-permission android:name="android.permission.WAKE_LOCK"/>
<uses-permission android:name="android.permission.DEVICE_POWER"/>
```

以上就针对提出的一个需求总结了一个方法，有更好方法的朋友希望在留言处提出，共同讨论改进之处，谢谢！
需求：要在后台跑一个Service执行轮询，屏幕熄灭或锁屏后，仍然需要保持Service一直处于轮询状态。

