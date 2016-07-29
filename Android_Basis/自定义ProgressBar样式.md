####横向进度条

```java
<?xml version="1.0" encoding="utf-8"?>
<!-- 定义用于进度条控件上的图层资源 -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
   
    <!-- 背景资源 -->
    <item android:id="@android:id/background"
          android:drawable="@drawable/custom_bg"/>
   
    <!--  二级进度资源 -->
    <item android:id="@android:id/secondaryProgress"
        android:drawable="@drawable/custom_secondary"
        />
   
    <!--  一级进度资源 -->
    <item android:id="@android:id/progress"
        android:drawable="@drawable/custom_progress"/> 

</layer-list>
```

使用：

```java
  <!-- 设置进度条的图层资源 -->
    <ProgressBar
        android:id="@id/progressBarId"
        android:layout_width="match_parent"
        android:layout_height="20dp"
        style="?android:attr/progressBarStyleHorizontal"
        android:progress="50"
        android:secondaryProgress="80"
        android:progressDrawable="@drawable/progressbar"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="10dp"
        />
```


####自定义progressbar旋转背景

```java
<ProgressBar
    android:layout_width="25dp"
    android:layout_height="25dp"
    android:indeterminateDrawable="@drawable/progressbar_loading"
    android:indeterminateDuration="900"
    />
```

下面是progressbar_loading.xml文件

```java
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <rotate android:drawable="@drawable/load1"
            android:fromDegrees="0.0" android:toDegrees="360.0" android:pivotX="50.0%"
            android:pivotY="50.0%"/>
    </item>
</layer-list>
```












