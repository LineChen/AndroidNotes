在Android开发中，定时执行任务的3种实现方法：
一、采用Handler与线程的sleep(long)方法（不建议使用，java的实现方式）
二、采用Handler的postDelayed(Runnable, long)方法（最简单的android实现）
三、采用Handler与timer及TimerTask结合的方法(比较多的任务时建议使用)
下面逐一介绍：
一、采用Handle与线程的sleep(long)方法
Handler主要用来处理接受到的消息。这只是最主要的方法，当然Handler里还有其他的方法供实现，有兴趣的可以去查API，这里不过多解释。
1. 定义一个Handler类，用于处理接受到的Message。

```java
Handler handler = new Handler() {  
	    public void handleMessage(Message msg) {  
	        // 要做的事情  
	        super.handleMessage(msg);  
	    }  
	};  
```


2.新建一个实现Runnable接口的线程类，如下：

```java
public class MyThread implements Runnable {  
	    @Override  
	    public void run() {  
	        // TODO Auto-generated method stub  
	        while (true) {  
	            try {  
	                Thread.sleep(10000);// 线程暂停10秒，单位毫秒  
	                Message message = new Message();  
	                message.what = 1;  
	                 handler.sendMessage(message);// 发送消息  
	             } catch (InterruptedException e) {  
	                 // TODO Auto-generated catch block  
	                 e.printStackTrace();  
	             }  
	         }  
	     }  
	 }  
```


3.在需要启动线程的地方加入下面语句：

`new Thread(new MyThread()).start();`  


4.启动线程后，线程每10s发送一次消息。

二、采用Handler的postDelayed(Runnable, long)方法
这个实现比较简单一些。
1. 定义一个Handler类

```java
	 Handler handler=new Handler();  
	 Runnable runnable=new Runnable() {  
	     @Override  
	     public void run() {  
	         // TODO Auto-generated method stub  
	         //要做的事情  
	         handler.postDelayed(this, 2000);  
	     }  
	 };  
```

2.启动计时器

`handler.postDelayed(runnable, 2000);//每两秒执行一次runnable.`  

3.停止计时器

`handler.removeCallbacks(runnable); `


三、采用Handler与timer及TimerTask结合的方法
1. 定义定时器、定时器任务及Handler句柄

```java
	private final Timer timer = new Timer();  
	private TimerTask task;  
	Handler handler = new Handler() {  
	    @Override  
	    public void handleMessage(Message msg) {  
	        // TODO Auto-generated method stub  
	        // 要做的事情  
	        super.handleMessage(msg);  
	    }  
	 };  
```

2.初始化计时器任务

```java
	task = new TimerTask() {  
	    @Override  
	    public void run() {  
	        // TODO Auto-generated method stub  
	        Message message = new Message();  
	        message.what = 1;  
	        handler.sendMessage(message);  
	    }  
	};   
```

3.启动定时器

`timer.schedule(task, 2000, 2000);`   

4.停止计时器

`timer.cancel();`


简要说一下上面三步提到的一些内容：

1. 定时器任务（TimerTask）顾名思义，就是说当定时器到达指定的时间时要做的工作，这里是想Handler发送一个消息，由Handler类进行处理。
2. java.util.Timer.schedule(TimerTask task, long delay):这个方法是说，dalay/1000秒后执行task.只执行一次。
3. java.util.Timer.schedule(TimerTask task, long delay, long period)：这个方法是说，delay/1000秒后执行task,然后进过period/1000秒再次执行task，这个用于循环任务，执行无数 次，当然，你可以用timer.cancel();取消计时器的执行。

>每一个Timer仅对应唯一一个线程。
Timer不保证任务执行的十分精确。
Timer类的线程安全的。


####Android如何设置定时每十分钟执行一次任务

```java
private Handler handler  = new Handler(){
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        if(msg.waht == 1){
            //todo something....
        }
    }
};
```
 
 
```java
private Timer timer = new Timer(true);
 
//任务
private TimerTask task = new TimerTask() {
  public void run() {
    Message msg = new Message();
    msg.waht = 1;
    handler.sendMessage(msg);
  }
};
 
//启动定时器
timer.schedule(task, 0, 10*60*1000);
```
