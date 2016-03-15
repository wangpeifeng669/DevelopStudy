性能篇（工具二）-内存泄露排查MAT
---
###(一)Memory Analyzer Tool简介
Momory Analyzer Tool 是 android 中极好的内存分析工具，目前Android Studio支持导出和简单分析，更详细的分析还需要自己下载[MAT 工具](http://www.eclipse.org/mat/downloads.php)。
###(二)Memory Analyzer Tool的使用
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


###(三)Memory Analyzer Tool案例分析
