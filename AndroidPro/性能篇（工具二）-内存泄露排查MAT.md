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
在 Android Studio 中打开Android Device Monitor，选择要分析的app包名，点击左上角导出hprof文件。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT1.png?raw=true)
将 hprof 文件从 Dalvik 格式转换成 j2se 格式，使用 hprof-conv 命令，hprof-conv dump.hprof converted-dump.hprof。  
下载[MAT 工具](http://www.eclipse.org/mat/downloads.php)。  
用 MAT 工具打开转换后的 hprof 文件。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT2.png?raw=true)
主要包括包括两部分：  
**Histogram**可以列出内存中每个对象的名字、数量以及大小。  
**Dominator Tree**会将所有内存中的对象按大小进行排序，并且可以分析对象之间的引用结构。  
####分析Histogram
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT3.png?raw=true)
左边显示app占用内存中的对象，Retained Heap 代表该对象及其所有引用占用的内存量。byte 内存泄露的可能性最大，右键选择Path To GC Roots->exclude weak refefrence（weak 引用会自动回收不需要查找）。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT4.png?raw=true)
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT5.png?raw=true)
发现 byte 是被HeapLeakActivity引用的，导致泄露，而 Activity 不能被回收，下面显示是存在 Message 未处理。
####分析Dominator Tree
Class Name 下方可以输入搜索词，一般 Andorid 内存泄露都是 Activity 为主，输入 Activity 搜索。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/%E6%80%A7%E8%83%BD%E7%AF%87%EF%BC%88%E5%B7%A5%E5%85%B7%E4%BA%8C%EF%BC%89-%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2%E6%8E%92%E6%9F%A5MAT6.png?raw=true)
出现同时存在多个HeapLeakActivity，说明出现了内存泄露，再用Path To GC Roots查找泄露原因。