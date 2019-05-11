# 源码分析

* **限定自己的高度不能超过默认值**

```java
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int idealHeight = this.dpToPx(this.getDefaultHeight()) + this.getPaddingTop() + this.getPaddingBottom();
        switch(MeasureSpec.getMode(heightMeasureSpec)) {
        case -2147483648:
                //固定高度不能超过默认的值
            heightMeasureSpec = MeasureSpec.makeMeasureSpec(Math.min(idealHeight, MeasureSpec.getSize(heightMeasureSpec)), 1073741824);
            break;
        case 0:
            heightMeasureSpec = MeasureSpec.makeMeasureSpec(idealHeight, 1073741824);
        }
        ...
    }
```
