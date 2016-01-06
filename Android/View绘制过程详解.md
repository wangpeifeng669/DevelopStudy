View绘制过程详解
===
参考资料：https://github.com/CharonChui/AndroidNote/blob/master/Android%E5%8A%A0%E5%BC%BA/View%E7%BB%98%E5%88%B6%E8%BF%87%E7%A8%8B%E8%AF%A6%E8%A7%A3.md

界面窗口的根布局是DecorView，该类继承自FrameLayoutView绘制在ViewGroup中使用ViewRootImpl。View的绘制过程从ViewRootImpl.performTraversals()开始。
###（一）View绘制过程总览
    private void performTraversals() {
        // 是否需要Measure
        if (!mStopped) {
           ...
           performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);
           ...
        }

        // 是否需要Layout
        if (didLayout) {
           performLayout(lp, desiredWindowWidth, desiredWindowHeight);
           ...
        }

        // 是否需要Draw
        if (!cancelDraw && !newSurface) {
           ...
           performDraw();
           ...
        }
    }

performMeasure过程

    private void performMeasure() {
      - 1.调用View.measure方法
          mView.measure():
      - 2.measure内部会调用onMeasure方法,但是因为这里mView是DecorView，所以会调用FrameLayout的onMeasure方法。
          onMeasure(FrameLayout)
      - 3. 内部设置ViewGroup的宽高
          setMeasuredDimension
          并且对每个子View进行遍历测量
          for (int i = 0; i < count; i++) {
              final View child = getChildAt(i);
      - 4. 对每个子View调用measureChildWithMargins方法
          measureChildWithMargins(child, widthMeasureSpec, 0, heightMeasureSpec, 0);  
      -5. measureChildWithMargins内部调用子View的measure方法
          meausre
      - 6. measure方法内部又调用onMeasure方法
          onMeasure(View)
      - 7. onMeasure方法内部调用setMeasuredDimension
          setMeasuredDimension
      - 8. setMeasuredDimension内部调用setMeasuredDimensionRaw
          setMeasuredDimensionRaw
          }
    }

performLayout过程

    private void performLayout(WindowManager.LayoutParams lp, int desiredWindowWidth,int desiredWindowHeight) {
    - 1. host.layout
         host.layout(0, 0, host.getMeasuredWidth(), host.getMeasuredHeight());
    - 2. layout方法会分别调用setFrame()和onLayout()方法
         setFrame()
         onLayout()
    - 3. 因为host是mView也就是DecorView也就是FrameLayout的子类。FrameLayout的onLayout方法如下
         for (int i = 0; i < count; i++) {
            final View child = getChildAt(i);
            if (child.getVisibility() != GONE) {
    - 4. 遍历每个子View，并分别调用layout方法。
         child.layout(childLeft, childTop, childLeft + width, childTop + height);
            }
         }
    }

performDraw过程

    // 1. ViewRootImpl.performDraw()
    private void performDraw() {
    // 2. ViewRootImpl.draw()
    draw(fullRedrawNeeded); 
    // 3. ViewRootImpl.drawSoftware
    drawSoftware
    // 4. 内部调用mView.draw,也就是FrameLayout.draw(). 
    mView.draw()(FrameLayout)
    // 5. FrameLayout.draw方法内部会调用super.draw方法，也就是View.draw方法.
    super.draw(canvas);
    // 6. View.draw方法内部会分别调用onDraw绘制自己以及dispatchDraw绘制子View.
    onDraw
    // 绘制子View
    dispatchDraw
    // 7. dispatchDraw方法内部会遍历所有子View.
       for (int i = 0; i < childrenCount; i++) {
    // 8. 对每个子View分别调用drawChild方法
          drawChild()
    // 9. drawChild方法内部会对该子View调用draw方法，进行绘制。然后draw又会调用onDraw等，循环就开始了。  
          child.draw()
       }
    }


###（二）onMeasure
View设置宽高大小。  
measure方法是final类型，要自定义View测量时只能调用onMeasure。覆写onMeasure方法的时候，必须调用setMeasuredDimension(int,int)来存储这个View经过测量宽高。  
onMeasure参数为MeasureSpec，用来设置宽高值，由尺寸size和模式mode构成，作为元装入int中，减少对象分配。  
**模式分三种类型：**  
**UNSPECIFIED**：parent没有对child强加任何限制，child可以是它想要的任何尺寸，减少使用  
**EXACTLY**：parent为child决定了一个绝对尺寸，当child设置match_parent或者具体尺寸时的模式  
**AT_MOST**：child可以是自己任意的大小，但是有个绝对尺寸的上限，当child设置wrap_content时的模式

###（三）onLayout
View在ViewGroup中的摆放设置。  
onLayout在View中为空方法，需要在ViewGroup中控制子View的摆放位置。  

    //@param changed 该参数指出当前ViewGroup的尺寸或者位置是否发生了改变  
    //@param left top right bottom 当前ViewGroup相对于其父控件的坐标位置
    protected void onLayout(boolean changed,int left, int top, int right, int bottom);
    其中再布局各个子 View
    int childCount = getChildCount();        
    for ( int i = 0; i < childCount; i++ ) {
        …
        View childView = getChildAt(i);
        //执行ChildView的绘制
        childView.layout(mPainterPosX,mPainterPosY,mPainterPosX+width, mPainterPosY+height);
        …
    }   

###（四）onDraw
[View的绘制](http://developer.android.com/intl/zh-cn/training/custom-views/custom-drawing.html)。  
用Paint在Canvas上画画，支持线、图形、图片等的绘制。对象的创建尽量不要在onDraw中进行，自绘很频繁，容易影响性能。绘制更多性能请查看[官方文档](http://developer.android.com/intl/zh-cn/training/custom-views/optimizing-view.html)。

