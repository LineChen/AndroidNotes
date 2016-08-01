#### Fresco简单的使用—SimpleDraweeView

百学须先立志—学前须知：在我们平时加载图片(不管是下载还是加载本地图片…..)的时候，我们经常会遇到这样一个需求，那就是当图片正在加载时应该呈现正在加载时的图像， 当图片加载失败时应该呈现图片加载时的图像，当我们重新加载这张图片时，应该呈现重试时图像，直到这张图片加载完成。这些繁琐并且重复的如果得不到简化的 话，那将是一个开发人员的噩梦，现在好了，我们用 Facebook 出品的一个强大的图片加载组件 Fresco 几行代码就可以搞定以上问题了。


1. SimpleDraweeView最基本的使用
2. SimpleDraweeView的圆形图
3. SimpleDraweeView的圆角图
4. SimpleDraweeView的缩放类型

Fresco中文说明：[http://www.fresco-cn.org/](http://www.fresco-cn.org/)

Fresco项目GitHub地址：[https://github.com/facebook/fresco](https://github.com/facebook/fresco)


- 常见问题：

初次使用，我们就先简单书写我们的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_adv"
        android:layout_width="300dp"
        android:layout_height="300dp"
        android:layout_centerInParent="true"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

简单的书写完布局和实现代码之后我们来运行一下。

```java
public java.lang.RuntimeException: Unable to start activity ComponentInfo{scp.com.frescodemo/scp.com.frescodemo.MainActivity}: 

android.view.InflateException: Binary XML file line #5: Error inflating class com.facebook.drawee.view.SimpleDraweeView
                at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2264)
                at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2313)
                at android.app.ActivityThread.access$1100(ActivityThread.java:141)
                at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1238)
                at android.os.Handler.dispatchMessage(Handler.java:102)
                at android.os.Looper.loop(Looper.java:136)
                at android.app.ActivityThread.main(ActivityThread.java:5336)
                at java.lang.reflect.Method.invokeNative(Native Method)
                at java.lang.reflect.Method.invoke(Method.java:515)
                at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:871)
                at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:687)
                at dalvik.system.NativeStart.main(Native Method)
         Caused by: android.view.InflateException: Binary XML file line #5: Error inflating class com.facebook.drawee.view.SimpleDraweeView
```


修改我们的 MainActivity 里代码：
运行报错了！怎么回事呢？这里啊，是因为我们没有在应用调用 setContentView() 之前进行初始化Fresco造成的；解决办法：


```java
public class MainActivity extends AppCompatActivity { 
@Override 
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState); 
	Fresco.initialize(this); 
	setContentView(R.layout.activity_main); 
    } 
  }

```


- **占位图—placeholderImage：**
在此之前我们需要一张占位图 icon_placeholder.png 大家右键另存为即可：

![placeholderImage](http://)

修改我们的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_adv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        ></com.facebook.drawee.view.SimpleDraweeView>
</RelativeLayout>
```

- **正在加载图—progressBarImage：**

在此之前我们需要一张正在加载图 icon_progress_bar.png

![progressBarImage](http://)

修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>

```

正在加载图本身是不可以转的，但是呢，加上了 fresco:progressBarAutoRotateInterval="5000" 属性，那么它就可以旋转了；可以看到，正在加载图一闪而过，这是因为我们加载的图片很小，网络也很好。

![test1](http://)


![code1](http://)


- **失败图—failureImage：**

![failureImage](http://)

修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test2](http://)


![code2](http://)


- **重试图—retryImage：**

![retryImage](http://)


修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="centerCrop"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test3](http://)

![code3](http://)


- **淡入淡出动画—fadeDuration：**

修改我们刚刚写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="centerCrop"
        fresco:fadeDuration="5000"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```


![test4](http://)

![test5](http://)

![code4](http://)


- **背景图—backgroundImage：**

这里呢，我们的背景图采用的是一个系统所提供的颜色中的一种。
修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```


![test6](http://)

![code6](http://)


- **叠加图—overlayImage：**

这里呢，我们的背景图采用的是一个系统所提供的颜色中的一种。
修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="centerCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:pressedStateOverlayImage="@android:color/holo_green_dark"
        fresco:overlayImage="@android:color/black"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test7](http://)

![code7](http://)

- **圆形图—roundAsCircle：**

一行代码搞定圆形图：设置roundAsCircle为true；
修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="centerCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:roundAsCircle="true"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test8](http://)

![code8](http://)


- **圆角图—roundedCornerRadius：**

修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="fitCenter"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="centerInside"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="centerInside"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="centerCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:roundedCornerRadius="30dp"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![code9](http://)

![round1](http://)
![round2](http://)
![round3](http://)
![round4](http://)

![code10](http://)

- **圆形圆角边框宽度及颜色—roundingBorder：**

修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="focusCrop"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="focusCrop"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="focusCrop"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="focusCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:roundAsCircle="true"
        fresco:roundedCornerRadius="30dp"
        fresco:roundTopLeft="true"
        fresco:roundTopRight="true"
        fresco:roundBottomLeft="true"
        fresco:roundBottomRight="true"
        fresco:roundingBorderWidth="10dp"
        fresco:roundingBorderColor="@android:color/black"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test11](http://)

![code11](http://)


- **圆形或圆角图像底下的叠加颜色—roundWithOverlayColor：**

修改我们刚刚书写的 activity_main.xml ：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:fresco="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.facebook.drawee.view.SimpleDraweeView
        android:id="@+id/main_sdv"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_centerInParent="true"
        fresco:actualImageScaleType="focusCrop"
        fresco:placeholderImage="@mipmap/icon_placeholder"
        fresco:placeholderImageScaleType="focusCrop"
        fresco:progressBarImage="@mipmap/icon_progress_bar"
        fresco:progressBarImageScaleType="focusCrop"
        fresco:progressBarAutoRotateInterval="5000"
        fresco:failureImage="@mipmap/icon_failure"
        fresco:failureImageScaleType="focusCrop"
        fresco:retryImage="@mipmap/icon_retry"
        fresco:retryImageScaleType="focusCrop"
        fresco:fadeDuration="5000"
        fresco:backgroundImage="@android:color/holo_orange_light"
        fresco:roundWithOverlayColor="@android:color/darker_gray"
        fresco:roundAsCircle="true"
        ></com.facebook.drawee.view.SimpleDraweeView>

</RelativeLayout>
```

![test12](http://)

![code12](http://)

- **缩放类型—ScaleType：**

| 类型 | 描述 |
|--------|--------|
|   center     |    居中，无缩放     |
|	centerCrop	|	保持宽高比缩小或放大，使得两边都大于或等于显示边界。居中显示。	|
|	focusCrop	|	同centerCrop, 但居中点不是中点，而是指定的某个点	|
|	centerInside|使两边都在显示边界内，居中显示。如果图尺寸大于显示边界，则保持长宽比缩小图片。|
|	fitCenter	|	保持宽高比，缩小或者放大，使得图片完全显示在显示边界内。居中显示	|
|	fitStart	|	同上。但不居中，和显示边界左上对齐	|
|	fitEnd	|		同fitCenter， 但不居中，和显示边界右下对齐	|
|	fitXY	|	不保存宽高比，填充满显示边界none	|
|	none	|	如要使用tile mode显示, 需要设置为none	|





- **XML属性**


| XML属性 | 意义 |
|--------|--------|
|fadeDuration|淡入淡出动画持续时间(单位：毫秒ms)|
|actualImageScaleType|实际图像的缩放类型|
|placeholderImage|占位图|
|placeholderImageScaleType|占位图的缩放类型|
|progressBarImage|进度图|
|progressBarImageScaleType|进度图的缩放类型|
|progressBarAutoRotateInterval|进度图自动旋转间隔时间(单位：毫秒ms)|
|failureImage|失败图|
|failureImageScaleType|失败图的缩放类型|
|retryImage|重试图|
|retryImageScaleType|重试图的缩放类型|
|backgroundImage|背景图|
|overlayImage|叠加图|
|pressedStateOverlayImage|按压状态下所显示的叠加图|
|roundAsCircle|设置为圆形图|
|roundedCornerRadius|圆角半径|
|roundTopLeft|左上角是否为圆角|
|roundTopRight|右上角是否为圆角|
|roundBottomLeft|左下角是否为圆角|
|roundBottomRight|右下角是否为圆角|
|roundingBorderWidth|圆形或者圆角图边框的宽度|
|roundingBorderColor|圆形或者圆角图边框的颜色|
|roundWithOverlayColor|圆形或者圆角图底下的叠加颜色(只能设置颜色)|
|viewAspectRatio|控件纵横比|
