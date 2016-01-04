View绘制过程详解
===
参考资料：https://github.com/CharonChui/AndroidNote/blob/master/Android%E5%8A%A0%E5%BC%BA/View%E7%BB%98%E5%88%B6%E8%BF%87%E7%A8%8B%E8%AF%A6%E8%A7%A3.md

界面窗口的根布局是DecorView，该类继承自FrameLayoutView绘制在ViewGroup中使用ViewRootImpl。View的绘制过程从ViewRootImpl.performTraversals()开始。
