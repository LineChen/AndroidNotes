##问题

如何获取一个控件的长和高，相信很多朋友第一眼看见这个问题都会觉得很简单，直接在onCreate里面调用getWidth、getMeasuredWidth不就可以获得了吗，但是，事实上是并没有简单的，不信的话，你可以去试一下，在onCreate里面，你是无法获得长宽值的，始终为0。（自己试了一下确实是）。

##原因

这是为什么呢，其实熟悉view绘制流程的朋友应该一眼就看出来了，在onCreate中，我们的控件其实还并没有画好，换句话说，等onCreate方法执行完了，我们定义的控件才会被度量(measure)，所以我们在onCreate方法里面通过view.getHeight()获取控件的高度或者宽度肯定是0。

##解决

No1：

```java
int w = View.MeasureSpec.makeMeasureSpec(0,
View.MeasureSpec.UNSPECIFIED);
int h = View.MeasureSpec.makeMeasureSpec(0,
        View.MeasureSpec.UNSPECIFIED);
imageView.measure(w, h);
int height = imageView.getMeasuredHeight();
int width = imageView.getMeasuredWidth();
```

这种方法很简单，就是我们自己来测量



No2：

```java
ViewTreeObserver vto = imageView.getViewTreeObserver();
 vto.addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
     public boolean onPreDraw() {
         vto.removeOnPreDrawListener(this);
         int height = imageView.getMeasuredHeight();
         int width = imageView.getMeasuredWidth();
         return true;
     }
 });
```


这个方法，我们需要注册一个ViewTreeObserver的监听回调，这个监听回调，就是专门监听绘图的，既然是监听绘图，那么我们自然可以获取测量值了，同时，我们在每次监听前remove前一次的监听，避免重复监听。


No3：

```java
ViewTreeObserver vto = imageView.getViewTreeObserver();  
 vto.addOnGlobalLayoutListener(new OnGlobalLayoutListener() {
     @Override  
     public void onGlobalLayout() {
         imageView.getViewTreeObserver().removeGlobalOnLayoutListener(this);
         imageView.getHeight();
         imageView.getWidth();
     }  
 });
```

这个方法于第2个方法基本相同，但他是全局的布局改变监听器，所以是最推荐使用的。

OK，现在看来，看似简单问题也不是那么简单吧。

以上。
