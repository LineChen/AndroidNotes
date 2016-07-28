**AsyncTask的介绍及基本使用方法**
关于AsyncTask的介绍和基本使用方法可以参考官方文档和Android实战技巧：多线程AsyncTask这里就不重复。

**AsyncTask引发的一个问题**
上周遇到了一个极其诡异的问题，一个小功能从网络上下载一个图片，然后放到 ImageView中，是用AsyncTask来实现的，本身逻辑也很简单，仅是在doInBackground中用HTTP请求把图片的输入流取出，然 后用BitmapFactory去解析，然后再把得到的Bitmap放到ImageView中。这个应用是用4.0的SDK开发的，也是运行在4.0上面 的。但是有时候下载这张图片去要用很久很久，甚至要等上几分钟。通过调试发现一个令人难以接受的事实：竟然是doInBackground()未及时执 行，也就是它并没有在#execute()调用之后马上执行，而是等待了很久才得以执行。
神马情况，难道AsyncTask不是线程，难道不是异步，难道AsyncTask另有内幕？

**AsyncTask的内幕**
AsyncTask主要有二个部分：一个是与主线各的交互，另一个就是线程的管理调 度。虽然可能多个AsyncTask的子类的实例，但是AsyncTask的内部Handler和ThreadPoolExecutor都是进程范围内共 享的，其都是static的，也即属于类的，类的属性的作用范围是CLASSPATH，因为一个进程一个VM，所以是AsyncTask控制着进程范围内 所有的子类实例。

- 与主线程交互
与主线程交互是通过Handler来进行的，因为本文主要探讨AsyncTask在任务调度方面的，所以对于这部分不做细致介绍，感兴趣的朋友可以去看AsyncTask的源码

- 线程任务的调度
内部会创建一个进程作用域的线程池来管理要运行的任务，也就就是说当你调用了 AsyncTask#execute()后，AsyncTask会把任务交给线程池，由线程池来管理创建Thread和运行Therad。对于内部的线程 池不同版本的Android的实现方式是不一样的：

**Android2.3以前的版本，也即SDK/API 10和以前的版本**
内部的线程池限制是5个，也就是说同时只能有5个线程运行，超过的线程只能等待，等待 前面的线程某个执行完了才被调度和运行。换句话说，如果一个进程中的AsyncTask实例个数超过5个，那么假如前5个都运行很长时间的话，那么第6个 只能等待机会了。这是AsyncTask的一个限制，而且对于2.3以前的版本无法解决。如果你的应用需要大量的后台线程去执行任务，那么你只能放弃使用 AsyncTask，自己创建线程池来管理Thread，或者干脆不用线程池直接使用Thread也无妨。不得不说，虽然AsyncTask较 Thread使用起来比较方便，但是它最多只能同时运行5个线程，这也大大局限了它的实力，你必须要小心的设计你的应用，错开使用AsyncTask的时 间，尽力做到分时，或者保证数量不会大于5个，否则就可能遇到上面提到的问题。要不然就只能使用JavaSE中的API了。

![task1](http://)

**Android 3.0以后，也即SDK/API 11和以后的版本**












