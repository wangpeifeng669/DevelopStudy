Android Touch事件分发详解
===
参考资料：
https://github.com/CharonChui/AndroidNote/blob/master/Android%E5%8A%A0%E5%BC%BA/Android%20Touch%E4%BA%8B%E4%BB%B6%E5%88%86%E5%8F%91%E8%AF%A6%E8%A7%A3.md
http://codetheory.in/understanding-android-input-touch-events/https://gist.github.com/Leaking/16e682b1ffac3a59c3df

源码在线地址：http://androidxref.com/

###1、Activity.dispatchTouchEvent()
    public boolean dispatchTouchEvent(MotionEvent ev) {
       if (ev.getAction() == MotionEvent.ACTION_DOWN) {
          onUserInteraction();
       }
       if (getWindow().superDispatchTouchEvent(ev)) {
          return true;
       }
       return onTouchEvent(ev);
    }
getWindow()引用PhoneWindow对象，Window对象是顶层窗口，管理界面的显示和事件的响应；每个Activity 均会创建一个PhoneWindow对象， 是Activity和整个View系统交互的接口。
###2、PhoneWindow.superDispatchTouchEvent
    public boolean superDispatchTouchEvent(MotionEvent event) {
       return mDecor.superDispatchTouchEvent(event);
    }
DecorView是Window最顶层的对象。
###3、DecorView.superDispatchTouchEvent
     private final class DecorView extends FrameLayout implements RootViewSurfaceTaker {
        ...
     }
DecorView是Window最顶层的对象。

###4、FrameLayout.superDispatchTouchEvent
无实现，引用ViewGroup中的方法。

###5、ViewGroup.superDispatchTouchEvent
####（1）验证子 view是否调用requestDisallowInterceptTouchEvent
    final boolean intercepted;
    if (actionMasked == MotionEvent.ACTION_DOWN || mFirstTouchTarget != null) {
       final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
       if (!disallowIntercept) {
            intercepted = onInterceptTouchEvent(ev);
            ev.setAction(action); // restore action in case it was changed
       } else {
            intercepted = false;
       }
    } else {
       // There are no touch targets and this action is not an initial down
       // so this view group continues to intercept touches.
       intercepted = true;
    }
####（2）子View不允许父 View拦截事件，则调用onInterceptTouchEvent
####（3）不被取消也不被拦截，则遍历所有点击范围内的View，执行点击事件
    if (!canceled && !intercepted) {
      …
        if (dispatchTransformedTouchEvent(ev, cancelChild,target.child, target.pointerIdBits)){
          handled = true;

                       if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
                ...
                newTouchTarget = addTouchTarget(child, idBitsToAssign);
                    ...
             }
       }
      …
    }
dispatchTransformedTouchEvent()将Touch事件传递给子View做递归处理，最终是调用View.onTouchEvent，若onTouchEvent返回true则代表消耗该事件，addTouchTarget生成mFirstTouchTarget。

    if (mFirstTouchTarget == null) {
        // No touch targets so treat this as an ordinary view.
        handled = dispatchTransformedTouchEvent(ev, canceled, null,TouchTarget.ALL_POINTER_IDS);
    } else {
        ...
         if (dispatchTransformedTouchEvent(ev, cancelChild,target.child, target.pointerIdBits)) {
             handled = true;
         }
        ...
    }
非ACTION_DOWN就从此处开始执行,比如ACTION_MOVE和ACTION_UP。mFirstTouchTarget为空则代表子view的onTouch为false，则不继续传递给子View，执行ViewGroup自身的onTouch，不为空则后续事件继续传递给子View。

####6、dispatchTransformedTouchEvent中调用View.dispatchTouchEvent
    public boolean dispatchTouchEvent(MotionEvent event)     {
       ...
       if (onFilterTouchEventForSecurity(event)) {
          //noinspection SimplifiableIfStatement
          ListenerInfo li = mListenerInfo;
          if (li != null && li.mOnTouchListener != null&& (mViewFlags & ENABLED_MASK) ==       ENABLED&& li.mOnTouchListener.onTouch(this, event)) {
             result = true;
          }

          if (!result && onTouchEvent(event)) {
             result = true;
          }
       }
      ...
    }
先执行OnTouchListener.onTouch后执行onTouchEvent   

分析源码可以得到如下结论。  
（1）MotionEvent对象封装所有touch事件信息，包括Touch位置、历史记录、第几个手指等.  
（2）所有touch事件入口在Activity.dispatchTouchEvent()  
（3）ViewGroup中存在onInterceptTouchEvent专门用来拦截子View接收touch事件，View 中存在onTouchEvent响应touch事件。  
（4）子View调用requestDisallowInterceptTouchEvent不允许父View拦截touch事件，则父View的onInterceptTouchEvent失效。  
（5）touch事件传递过程：  
ViewGroup1包含ViewGroup2，ViewGroup2包含View，理想状态下（onInterceptTouchEvent和onTouch都为false），从布局由外向内隧道式向下分发，依次调用ViewGroup1.onInterceptTouchEvent、ViewGroup2.onInterceptTouchEvent，再冒泡式向上处理，依次调用View.onTouch、ViewGroup2.onTouch、ViewGroup1.onTouch、Activity.onTouch。  
若ViewGroup2.onTouch返回true，则后续分发都直接到ViewGroup2.onTouch处理，ViewGroup1.onInterceptTouchEvent、ViewGroup2.onInterceptTouchEvent、ViewGroup2.onTouch；  
若ViewGroup2.onInterceptTouchEvent返回true，则拦截后续事件，后续事件分发ViewGroup1.onInterceptTouchEvent、ViewGroup1.onTouch、Activity.onTouch。

测试 demo  

    public class TouchTestActivity extends ActionBarActivity {
    public static final String TAG = TouchTestActivity.class.getSimpleName();

     @Override
     protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          TouchLayout touchOuterLayout = new TouchLayout(this,"out");
          TouchLayout touchInnerLayout = new TouchLayout(this,"in");
          touchInnerLayout.isInterceptTouchEvent = true;
          touchInnerLayout.isTouchEvent = true;
          TouchView touchView = new TouchView(this);
          touchView.setText("测试的View");
          touchInnerLayout.addView(touchView);
          touchOuterLayout.addView(touchInnerLayout);
          setContentView(touchOuterLayout);
         }

     @Override
     public boolean dispatchTouchEvent(MotionEvent ev) {
          Log.i(TAG, "call Activity dispatchTouchEvent");
          return super.dispatchTouchEvent(ev);
         }

     @Override
     public boolean onTouchEvent(MotionEvent event) {
          Log.i(TAG, "call Activity onTouchEvent");
          return true;
         }

     class TouchLayout extends LinearLayout {
          private String mName;
          public boolean isInterceptTouchEvent = false;
          public boolean isTouchEvent = false;

          public TouchLayout(Context context, String name) {
               super(context);
               mName = name;
              }

          @Override
          public boolean onInterceptTouchEvent(MotionEvent ev) {
               Log.i(TAG, "call ViewGroup onInterceptTouchEvent,name " + mName);
               return isInterceptTouchEvent;
              }

          @Override
          public boolean onTouchEvent(MotionEvent event) {
               Log.i(TAG, "call ViewGroup onTouchEvent,name " + mName);
               return isTouchEvent;
              }

          @Override
          public void setOnTouchListener(OnTouchListener l) {
               super.setOnTouchListener(l);
               Log.i(TAG, "call ViewGroup OnTouchListener,name " + mName);
              }
         }

     class TouchView extends TextView {
          public TouchView(Context context) {
               super(context);
              }

          @Override
          public boolean onTouchEvent(MotionEvent event) {
               Log.i(TAG, "call View onTouchEvent " + event.getAction());
               return true;
              }
         }
    }
