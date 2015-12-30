Scroller 使用及原理分析
===
###（一）简单样例

    /**
     * 滚动控件 Scroller 滚动测试布局，实现手势滚动ScrollerTestLayout内部控件
     *
     * @author peter_wang
     * @create-time 15/11/9 08:52
     */     
    public class ScrollerTestLayout extends LinearLayout     {
    private Scroller mScroller;
    private Context mContext;

    /**
     * 监听滚动的最小偏差值
     */
    private int mStartMoveSlop;
    /**
     * 记录起点touch的X坐标
     */
    private int mStartMotionX;
    /**
     * 记录上次touch的X坐标
     */
    private int mLastMotionX;

    public ScrollerTestLayout(Context context) {
        super(context);
        mContext = context;
        init();
    }

    public ScrollerTestLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        mContext = context;
        init();
    }

    private void init() {
        mScroller = new Scroller(mContext);
        mStartMoveSlop = ViewConfiguration.get(mContext).getScaledTouchSlop();

        setBackgroundColor(mContext.getResources().getColor(android.R.color.white));
       addChildView();
    }

    private void addChildView() {
        TextView tv = new TextView(mContext);
    tv.setBackgroundColor(mContext.getResources().getColor(android.R.color.holo_green_light));
        tv.setText(mContext.getString(R.string.drag_me));
    tv.setTextColor(mContext.getResources().getColor(android.R.color.white));
        addView(tv);
    }

    @Override
    public void computeScroll() {
        // 判断Scroll是否滚动完成
        if (mScroller.computeScrollOffset()) {
            // 调用View的scrollto完成实际的滚动
            scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
            //必须调用该方法，否则不一定能看到滚动效果
            postInvalidate();
        }
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int x = (int) event.getX();

        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                mStartMotionX = x;
                mLastMotionX = x;
                break;

            case MotionEvent.ACTION_MOVE:
                int deltaX = mLastMotionX - x;
                if (Math.abs(deltaX) > mStartMoveSlop) {
                    smoothScrollTo(deltaX);
                    mLastMotionX = x;
                }
                break;

            case MotionEvent.ACTION_UP:
                mLastMotionX = x;
                resetScroll();
                break;
        }
        return true;
    }

    private void resetScroll() {
        // 回到起点
        mScroller.startScroll(mStartMotionX - mLastMotionX, 0, mLastMotionX - mStartMotionX,0);
        // 这里必须调用invalidate()才能保证computeScroll()会被调用，否则不一定会刷新界面，看不到滚动效果
        invalidate();
    }

    private void smoothScrollTo(int deltaX) {
        // 设置mScroller的滚动偏移量
        mScroller.startScroll(mStartMotionX - mLastMotionX, 0, deltaX,0);
        // 这里必须调用invalidate()才能保证computeScroll()会被调用，否则不一定会刷新界面，看不到滚动效果
        invalidate();
    }
    }
###（二）原理分析
####（1）Scroller.startScroll
    public void startScroll(int startX, int startY, int dx, int dy, int duration) {
        mMode = SCROLL_MODE;
        mFinished = false;
        mDuration = duration;
        mStartTime = AnimationUtils.currentAnimationTimeMillis();
        mStartX = startX;
        mStartY = startY;
        mFinalX = startX + dx;
        mFinalY = startY + dy;
        mDeltaX = dx;
        mDeltaY = dy;
        mDurationReciprocal = 1.0f / (float) mDuration;
     }
配置滚动参数，保存起始点、终止点及所需时间，启动滚动。

####（2）ViewGroup.draw
ViewGroup无实现，调用子类View.draw。
    
    public void draw(Canvas canvas) {
        /*
         * Draw traversal performs several drawing steps which must be executed
         * in the appropriate order:
         *
         *      1. Draw the background
         *      2. If necessary, save the canvas' layers to prepare for fading
         *      3. Draw view's content
         *      4. Draw children
         *      5. If necessary, draw the fading edges and restore layers
         *      6. Draw decorations (scrollbars for instance)
         */
         dispatchDraw(canvas);
       }

####（3）ViewGroup.dispatchDraw
    protected void dispatchDraw(Canvas canvas) {

    more |= drawChild(canvas, child, drawingTime);

    }
绘制子View。

####（4）View.draw
    boolean draw(Canvas canvas, ViewGroup parent, long drawingTime) {

        int sx = 0;
        int sy = 0;
        if (!hasDisplayList) {
            computeScroll();
            sx = mScrollX;
            sy = mScrollY;
        }
        canvas.translate(mLeft - sx, mTop - sy);

    }
调用computeScroll计算滚动到的位置，canvas进行平移。

####（5）View.computeScroll
	 @Override
	public void computeScroll() {
        // 判断Scroll是否滚动完成
        if (mScroller.computeScrollOffset()) {
            // 调用View的scrollto完成实际的滚动
            scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
            //必须调用该方法，否则不一定能看到滚动效果
            postInvalidate();
        }
	}
一般是重写该方法，调用scrollTo进行平移，同时刷新View的显示，不断调用computeScroll实现平移，从而实现平滑移动。

####（6）Scrolle.computeScrollOffset
	public boolean computeScrollOffset() {
        if (mFinished) {
            return false;
        }

        int timePassed = (int)(AnimationUtils.currentAnimationTimeMillis() - mStartTime);

        if (timePassed < mDuration) {
            switch (mMode) {
            case SCROLL_MODE:
                final float x = mInterpolator.getInterpolation(timePassed * mDurationReciprocal);
                mCurrX = mStartX + Math.round(x * mDeltaX);
                mCurrY = mStartY + Math.round(x * mDeltaY);
                break;
            case FLING_MODE:

                break;
            }
        }
        else {
            mCurrX = mFinalX;
            mCurrY = mFinalY;
            mFinished = true;
        }
        return true;
    }
在滚动过程中，利用Interpolator计算平滑移动的坐标点，不断刷新平移位置，再利用computeScroll实现平移。


