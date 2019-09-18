> 下面代码未完成

```java
public class MyViewGroup extends ViewGroup {
    public MyViewGroup(Context context) {
        super(context);
    }

    public MyViewGroup(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);

        //对所有的子View进行测量
        measureChildren(widthMeasureSpec, heightMeasureSpec);

        int widthMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSize = MeasureSpec.getSize(widthMeasureSpec);
        int heightMode = MeasureSpec.getMode(heightMeasureSpec);
        int heightSize = MeasureSpec.getSize(heightMeasureSpec);

        int childCount = getChildCount();

        if (childCount == 0) {
            //没有子View，容器不应该存在
            setMeasuredDimension(0, 0);
        } else {
            if (widthMode == MeasureSpec.EXACTLY && heightMode == MeasureSpec.EXACTLY) {
                //宽高确定
                Log.e("HLP", "宽高确定值");
            } else if (widthMode == MeasureSpec.AT_MOST && heightMode == MeasureSpec.AT_MOST) {
                //宽高包裹
                Log.e("HLP", "宽高wrap_content");
            } else if (widthMode == MeasureSpec.EXACTLY && heightMode == MeasureSpec.AT_MOST) {
                //宽确定&高包裹
                Log.e("HLP", "宽确定值&高wrap_content");
            } else if (heightMode == MeasureSpec.EXACTLY && widthMode == MeasureSpec.AT_MOST) {
                //宽包裹&高确定
                Log.e("HLP", "宽wrap_content&高确定值");
            }
        }
    }
    

    @Override
    protected void onLayout(boolean b, int i, int i1, int i2, int i3) {

    }
}
```

