# XML属性

## 添加阴影

```
android:translationZ="2dp"
```

# 自定义XML属性

## 定义属性

* **定义枚举类型**

```xml
    <declare-styleable name="StatusRelativeLayout">
        <attr name="typePage" format="enum">
            <enum name="loading" value="0" />
            <enum name="default" value="1" />
        </attr>
    </declare-styleable>
```



## 获取属性

# 自定义View

## requestLayout&invalidate

* **requestLayout：view会走onMesure，onLayout,onDraw等方法，主要是大小和布局**
* **invalidate：view会走onDraw等方法，主要是绘制**

## 重载方法

* **当Java代码创建对象**

```java
    public Custom(Context context) {
        super(context);
    }
```

* **在布局文件中使用这个CustomView的时候会被调用，AttributeSet对应的就是设置的属性值集合**

```java
    public Custom(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }
```

* **defStyleAttr：当为0的时候，查找系统默认样式属性**

```java
    public Custom(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
```

* **defStyleRes：当为0的时候，查找系统默认样式资源**

```java
    public Custom(Context context, @Nullable AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }
```



# onMeasure(x,y)

>测量模式大小
| 测量模式    | 含义             |
| ----------- | ---------------- |
| UNSPECIFIED | 对应match_parent |
| AT_MOST     | 对应wrap_content |
| EXACTLY     | 固定尺寸         |

>自定义View代码
```
class SuperView(ctx: Context, attrs: AttributeSet) : View(ctx, attrs) {

    private var defaultWidth = 100  //如果是wrap_content,就指定默认宽度为100PX
    private var defaultHeight = 50  //如果是wrap_content，就默认指定默认高度为50PX
    private var width: Int? = null  //宽
    private var height: Int? = null //高

    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)

        val getWidthSize = MeasureSpec.getSize(widthMeasureSpec)
        val getWidthMode = MeasureSpec.getMode(widthMeasureSpec)
        val getHeightSize = MeasureSpec.getSize(heightMeasureSpec)
        val getHeightMode = MeasureSpec.getMode(heightMeasureSpec)

        when (getWidthMode) {
            MeasureSpec.UNSPECIFIED -> {
                //match_parent
                width = getWidthSize
            }
            MeasureSpec.AT_MOST -> {
                //wrap_content
                width = defaultWidth
            }
            MeasureSpec.EXACTLY -> {
                //固定大小
                width = getWidthSize
            }
        }

        when (getHeightMode) {
            MeasureSpec.EXACTLY -> {
                height = getHeightSize
            }
            MeasureSpec.UNSPECIFIED -> {
                height = getHeightSize
            }
            MeasureSpec.AT_MOST -> {
                height = defaultHeight
            }
        }

        setMeasuredDimension(width!!, height!!) //将宽高设置进去
    }
}
```

#onDraw(x)

##Paint类

```
        //重置
        paint.reset();
        //抗锯齿
        paint.setAntiAlias(true);
        //设置图像抖动处理，颜色更鲜艳
        paint.setDither(true);
        //设置颜色
        paint.setColor(Color.RED);
        //描边的粗细
        paint.setStrokeWidth(50);
        //设置样式-填充
        paint.setStyle(Paint.Style.STROKE);
        //设置样式-描边
//        paint.setStyle(Paint.Style.STROKE);
        //设置样式-填充+描边
//        paint.setStyle(Paint.Style.STROKE);
        //设置线帽-ROUND圆的，BUTT-无，SQUARE-方形
        paint.setStrokeCap(Paint.Cap.SQUARE);
        //设置交叉部分形状，MITER-锐角,ROUND-圆形,BEVEL-直线
        paint.setStrokeJoin(Paint.Join.ROUND);
        //设置文本间距
        paint.setLetterSpacing(12);
```

###绘制文本



##Path类

###画线

```
        Path path = new Path();
        //起始坐标
        path.moveTo(100, 100);
        //第二个点坐标
        path.lineTo(300, 100);
        //第三个点坐标
        path.lineTo(300, 300);
        //将各个坐标点相连成为新的形状
        canvas.drawPath(path, paint);
```

##Canvas

###画扇形

####RectF

* **在0,0坐标画一个200x200的矩形**

```
new RectF(0,0,200,200)
```