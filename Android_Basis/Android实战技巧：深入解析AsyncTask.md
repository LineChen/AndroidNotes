**AsyncTask的介绍及基本使用方法**

关于AsyncTask的介绍和基本使用方法可以参考官方文档和Android实战技巧：多线程AsyncTask这里就不重复。

**AsyncTask引发的一个问题**

上周遇到了一个极其诡异的问题，一个小功能从网络上下载一个图片，然后放到 ImageView中，是用AsyncTask来实现的，本身逻辑也很简单，仅是在doInBackground中用HTTP请求把图片的输入流取出，然 后用BitmapFactory去解析，然后再把得到的Bitmap放到ImageView中。这个应用是用4.0的SDK开发的，也是运行在4.0上面 的。但是有时候下载这张图片去要用很久很久，甚至要等上几分钟。通过调试发现一个令人难以接受的事实：竟然是doInBackground()未及时执 行，也就是它并没有在#execute()调用之后马上执行，而是等待了很久才得以执行。
神马情况，难道AsyncTask不是线程，难道不是异步，难道AsyncTask另有内幕？

**AsyncTask的内幕**

AsyncTask主要有二个部分：一个是与主线各的交互，另一个就是线程的管理调 度。虽然可能多个AsyncTask的子类的实例，但是AsyncTask的内部Handler和ThreadPoolExecutor都是进程范围内共 享的，其都是static的，也即属于类的，类的属性的作用范围是CLASSPATH，因为一个进程一个VM，所以是AsyncTask控制着进程范围内 所有的子类实例。

- 与主线程交互
>与主线程交互是通过Handler来进行的，因为本文主要探讨AsyncTask在任务调度方面的，所以对于这部分不做细致介绍，感兴趣的朋友可以去看AsyncTask的源码

- 线程任务的调度
>内部会创建一个进程作用域的线程池来管理要运行的任务，也就就是说当你调用了 AsyncTask#execute()后，AsyncTask会把任务交给线程池，由线程池来管理创建Thread和运行Therad。对于内部的线程 池不同版本的Android的实现方式是不一样的：

**Android2.3以前的版本，也即SDK/API 10和以前的版本**

内部的线程池限制是5个，也就是说同时只能有5个线程运行，超过的线程只能等待，等待 前面的线程某个执行完了才被调度和运行。换句话说，如果一个进程中的AsyncTask实例个数超过5个，那么假如前5个都运行很长时间的话，那么第6个 只能等待机会了。这是AsyncTask的一个限制，而且对于2.3以前的版本无法解决。如果你的应用需要大量的后台线程去执行任务，那么你只能放弃使用 AsyncTask，自己创建线程池来管理Thread，或者干脆不用线程池直接使用Thread也无妨。不得不说，虽然AsyncTask较 Thread使用起来比较方便，但是它最多只能同时运行5个线程，这也大大局限了它的实力，你必须要小心的设计你的应用，错开使用AsyncTask的时 间，尽力做到分时，或者保证数量不会大于5个，否则就可能遇到上面提到的问题。要不然就只能使用JavaSE中的API了。

![task1](http://)

**Android 3.0以后，也即SDK/API 11和以后的版本**

可能是Google意识到了AsyncTask的局限性了，从Android 3.0开始对AsyncTask的API做出了一些调整：

1.execute()提交的任务，按先后顺序每次只运行一个
>也就是说它是按提交的次序，每次只启动一个线程执行一个任务，完成之后再执行第二个任务，也就是相当于只有一个后台线程在执行所提交的任务(Executors.newSingleThreadPool())。
![task2](http://)

2.新增了接口#executeOnExecutor()
>这个接口允许开发者提供自定义的线程池来运行和调度Thread，如果你想让所有的任 务都能并发同时运行，那就创建一个没有限制的线程池(Executors.newCachedThreadPool())，并提供给AsyncTask。 这样这个AsyncTask实例就有了自己的线程池而不必使用AsyncTask默认的。

3.新增了二个预定义的线程池SERIAL_EXECUTOR和THREAD_POOL_EXECUTOR
>其实THREAD_POOL_EXECUTOR并不是新增的，之前的就有，只不过之前(Android 2.3)它是AsyncTask私有的，未公开而已。THREAD_POOL_EXECUTOR是一个corePoolSize为5的线程池，也就是说最多只有5个线程同时运行，超过5个的就要等待。所以如果使用executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR)就跟2.3版本的AsyncTask.execute()效果是一样的。
![task3](http://)
而SERIAL_EXECUTOR是新增的，它的作用是保证任务执 行的顺序，也就是它可以保证提交的任务确实是按照先后顺序执行的。它的内部有一个队列用来保存所提交的任务，保证当前只运行一个，这样就可以保证任务是完 全按照顺序执行的，默认的execute()使用的就是这个，也就是executeOnExecutor(AsyncTask.SERIAL_EXECUTOR)与execute()是一样的。

**前面问题的解法**

了解了AsyncTask的内幕就知道了前面问题的原因：因为是4.0平台，所以所有 的AsyncTask并不都会运行在单独的线程中，而是被SERIAL_EXECUTOR顺序的使用线程执行。因为应用中可能还有其他地方使用 AsyncTask，所以到网络取图片的AsyncTask也许会等待到其他任务都完成时才得以执行而不是调用executor()之后马上执行。
那么解决方法其实很简单，要么直接使用Thread，要么创建一个单独的线程池 (Executors.newCachedThreadPool())。或者最简单的解法就是使用 executeOnExecutor(AsyncTask.THREAD_POOL_EXECUTOR)，这样起码不用等到前面的都结束了再执行


**AsyncTask的使用注意事项**

曾建议使用AsyncTask而不是使用Thread，但是AsyncTask似乎又有它的限制，这就要根据具体的需求情况而选择合适的工具，No Silver Bullet。下面是一些建议：


- 改善你的设计，少用异步处理线程的开销是非常大的，同时异步处理也容易出错，难调试，难维护，所以改善你的设计，尽可能的少用异步。对于一般性的数据库查询，少量的I/O操作是没有必要启动线程的。

- 与主线程有交互时用AsyncTask，否则就用ThreadAsyncTask被设计出来的目的就是为了满足Android的特殊需求：非主线程不能操作(UI)组件，所以AsyncTask扩展Thread增强了与主线程的交互的能力。如果你的应用没有与主线程交互，那么就直接使用Thread就好了。

- 当有需要大量线程执行任务时，一定要创建线程池线程的开销是非常大的，特别是创建一个新线程，否则就不必设计线程池之类的工具了。当 需要大量线程执行任务时，一定要创建线程池，无论是使用AsyncTask还是Thread，因为使用AsyncTask它内部的线程池有数量限制，可能 无法满足需求；使用Thread更是要线程池来管理，避免虚拟机创建大量的线程。比如从网络上批量下载图片，你不想一个一个的下，或者5个5个的下载，那 么就创建一个CorePoolSize为10或者20的线程池，每次10个或者20个这样的下载，即满足了速度，又不至于耗费无用的性能开销去无限制的创 建线程。

- 对于想要立即开始执行的异步任务，要么直接使用Thread，要么单独创建线程池提供给AsyncTask默认的AsyncTask不一定会立即执行你的任务，除非你提供给他一个单独的线程池。如果不与主线程交互，直接创建一个Thread就可以了，虽然创建线程开销比较大，但如果这不是批量操作就没有问题。

- Android的开发没有想像中那样简单，要多花心思和时间在代码上和测试上面，以确信程序是优质的














