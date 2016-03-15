性能篇（工具一）-内存使用情况Memory Monitor
---
###(一)Memory Monitor简介
Andorid studio 新增一个查看设备对应应用使用过程中内存占用情况的工具-Memory Monitor，用于实时查看App的内存分配情况、判断是否过多GC操作导致卡顿。  
###(二)Memory Monitor的使用
来一段内存泄露代码（为了加大泄露增加了List保存ImageView）

    /**
     * 测试内存泄露
     *
     * @author peter_wang
     * @create-time 16/3/7 15:13
     */
     public class HeapLeakActivity extends Activity {

	    private TextView textView;
	
	    private List<ImageView> mImageViewList;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_heap_leak);
	        textView = (TextView) findViewById(R.id.textview);
	        mImageViewList = new ArrayList<>();
	        for (int i = 0; i < 100; i++) {
	            ImageView iv = new ImageView(this);
	            iv.setImageResource(R.drawable.abc_ab_share_pack_holo_dark);
	            mImageViewList.add(iv);
	        }
	        Handler handler = new Handler();
	
	        handler.postDelayed(new Runnable() {
	            @Override
	            public void run() {
	                textView.setText("Done");
	            }
	        }, 800000L);
	    }
	}

![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%B8%80%EF%BC%89-%E5%86%85%E5%AD%98%E4%BD%BF%E7%94%A8%E6%83%85%E5%86%B5Memory%20Monitor1.png?raw=true)
A:设备选择  
B:监控APP选择  
C:内存实时数据  
D:APP已占用内存及最大可占用内存  
不断打开此页面，因为设置Handler延迟处理事件，所以每个页面都无法短时间内回收，APP占用内存不断增加，直到达到可占用最高值，此时在1m后，系统自动给APP增大最大内存值。
###(三)Memory Monitor案例分析
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%B8%80%EF%BC%89-%E5%86%85%E5%AD%98%E4%BD%BF%E7%94%A8%E6%83%85%E5%86%B5Memory%20Monitor2.png?raw=true)
Memory Monitor 是一个内存监控工具，相当于医生看病的"望闻问切"中的望，只能简单监测内存。  
上图中第一个区域内存突然加大，后面偶尔会促发GC回落，但始终占用一定内存，可能是内存泄露；第二个区域是内存抖动，短时间内多次内存分配释放，APP卡顿。