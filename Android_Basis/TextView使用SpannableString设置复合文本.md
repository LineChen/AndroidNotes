TextView通常用来显示普通文本，但是有时候需要对其中某些文本进行样式、事件方面的设置。Android系统通过SpannableString类来对指定文本进行相关处理，具体有以下功能：

1. BackgroundColorSpan 背景色
2. ClickableSpan 文本可点击，有点击事件
3. ForegroundColorSpan 文本颜色（前景色）
4. MaskFilterSpan 修饰效果，如模糊(BlurMaskFilter)、浮雕(EmbossMaskFilter)
5. MetricAffectingSpan 父类，一般不用
6. RasterizerSpan 光栅效果
7. StrikethroughSpan 删除线（中划线）
8. SuggestionSpan 相当于占位符
9. UnderlineSpan 下划线
10. AbsoluteSizeSpan 绝对大小（文本字体）
11. DynamicDrawableSpan 设置图片，基于文本基线或底部对齐。
12. ImageSpan 图片
13. RelativeSizeSpan 相对大小（文本字体）
14. ReplacementSpan 父类，一般不用
15. ScaleXSpan 基于x轴缩放
16. StyleSpan 字体样式：粗体、斜体等
17. SubscriptSpan 下标（数学公式会用到）
18. SuperscriptSpan 上标（数学公式会用到）
19. TextAppearanceSpan 文本外貌（包括字体、大小、样式和颜色）
20. TypefaceSpan 文本字体
21. URLSpan 文本超链接


**1、BackgroundColorSpan 背景色**

```java
SpannableString spanText = new SpannableString("萝卜白菜的博客 -- http://orgcent.com");
spanText.setSpan(new BackgroundColorSpan(Color.GREEN), 0, spanText.length(),
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**2、ClickableSpan 文本可点击，有点击事件**
查看：Android使用TextView实现无下划线超链接

**3、ForegroundColorSpan 文本颜色（前景色）**

```java
spanText = new SpannableString("萝卜白菜的博客 -- http://orgcent.com");
spanText.setSpan(new ForegroundColorSpan(Color.BLUE), 6, spanText.length(),
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**4、MaskFilterSpan 修饰效果，如模糊(BlurMaskFilter)、浮雕(EmbossMaskFilter)**

```java
spanText = new SpannableString("MaskFilterSpan -- http://orgcent.com");
int length = spanText.length();
//模糊(BlurMaskFilter)
MaskFilterSpan maskFilterSpan = new MaskFilterSpan(new BlurMaskFilter(3, Blur.OUTER));
spanText.setSpan(maskFilterSpan, 0, length - 10, Spannable.
SPAN_INCLUSIVE_EXCLUSIVE);
//浮雕(EmbossMaskFilter)
maskFilterSpan = new MaskFilterSpan(new EmbossMaskFilter(new float[]{1,1,3}, 1.5f, 8, 3));
spanText.setSpan(maskFilterSpan, length - 10, length, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**6、RasterizerSpan 光栅效果**

```java
spanText = new SpannableString("StrikethroughSpan");
spanText.setSpan(new StrikethroughSpan(), 0, 7, Spannable.
SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```


**7、StrikethroughSpan 删除线（中划线）**

```java
spanText = new SpannableString("StrikethroughSpan");
spanText.setSpan(new StrikethroughSpan(), 0, 7, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**8、SuggestionSpan**

相当于占位符，一般用在EditText输入框中。当双击此文本时，会弹出提示框选择一些建议（推荐的）文字，选中的文本将替换此占位符。在输入法上用的较多。
PS：API 14新增，暂无示例。

**9、UnderlineSpan 下划线**

```java
spanText = new SpannableString("UnderlineSpan");
spanText.setSpan(new UnderlineSpan(), 0, spanText.length(),
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**10、AbsoluteSizeSpan 绝对大小（文本字体）**

```java
spanText = new SpannableString("AbsoluteSizeSpan");
spanText.setSpan(new AbsoluteSizeSpan(20, true), 0, spanText.length(),
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```


**11、DynamicDrawableSpan 设置图片，基于文本基线或底部对齐。**

```java
DynamicDrawableSpan drawableSpan =
 new DynamicDrawableSpan(DynamicDrawableSpan.ALIGN_BASELINE) {
    @Override
    public Drawable getDrawable() {
        Drawable d = getResources().getDrawable(R.drawable.ic_launcher);
        d.setBounds(0, 0, 50, 50);
        return d;
    }
};
DynamicDrawableSpan drawableSpan2 = new DynamicDrawableSpan(
DynamicDrawableSpan.ALIGN_BOTTOM) {
    @Override
    public Drawable getDrawable() {
          Drawable d = getResources().getDrawable(R.drawable.ic_launcher);
          d.setBounds(0, 0, 50, 50);
                return d;
            }
        };
spanText.setSpan(drawableSpan, 3, 4, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
spanText.setSpan(drawableSpan2, 7, 8, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```


**12、ImageSpan 图片**

```java
spanText = new SpannableString("ImageSpan");
Drawable d = getResources().getDrawable(R.drawable.ic_launcher);
</strong>d.setBounds(0, 0, 50, 50);
spanText.setSpan(new ImageSpan(d), 3, 4, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```


**13、RelativeSizeSpan 相对大小（文本字体）**

```java
spanText = new SpannableString("RelativeSizeSpan");
//参数proportion:比例大小
spanText.setSpan(new RelativeSizeSpan(2.5f), 3, 4,
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```


**15、ScaleXSpan 基于x轴缩放**

```java
spanText = new SpannableString("ScaleXSpan -- 萝卜白菜的博客");
//参数proportion:比例大小
spanText.setSpan(new ScaleXSpan(3.8f), 3, 7, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**16、StyleSpan 字体样式：粗体、斜体等**

```java
spanText = new SpannableString("StyleSpan -- 萝卜白菜的博客");
//Typeface.BOLD_ITALIC:粗体+斜体
spanText.setSpan(new StyleSpan(Typeface.BOLD_ITALIC), 3, 7,
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**17、SubscriptSpan 下标（数学公式会用到）**

```java
spanText = new SpannableString("SubscriptSpan -- 萝卜白菜的博客");
spanText.setSpan(new SubscriptSpan(), 6, 7, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**18、SuperscriptSpan 上标（数学公式会用到）**

```java
spanText = new SpannableString("SuperscriptSpan -- 萝卜白菜的博客");
spanText.setSpan(new SuperscriptSpan(), 6, 7, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**19、TextAppearanceSpan 文本外貌（包括字体、大小、样式和颜色）**

```java
spanText = new SpannableString("TextAppearanceSpan -- 萝卜白菜的博客");
//若需自定义TextAppearance，可以在系统样式上进行修改
spanText.setSpan(new TextAppearanceSpan(this, android.R.style.TextAppearance_Medium),
 6, 7, Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
PS:系统还提供了相关值TextAppearance_Small, TextAppearance_Large等。如有需要可在以上样式基础上修改。
```

**20、TypefaceSpan 文本字体**

```java
spanText = new SpannableString("TypefaceSpan -- 萝卜白菜的博客");
//若需使用自定义字体，可能要重写类TypefaceSpan
spanText.setSpan(new TypefaceSpan("monospace"), 3, 10,
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
```

**21、URLSpan 文本超链接**

```java
spanText = new SpannableString("URLSpan -- 萝卜白菜的博客");
spanText.setSpan(new URLSpan("http://orgcent.com"), 10, spanText.length(),
Spannable.SPAN_INCLUSIVE_EXCLUSIVE);
mTVText.append("\n");
mTVText.append(spanText);
//让URLSpan可以点击
mTVText.setMovementMethod(new LinkMovementMethod());
```
