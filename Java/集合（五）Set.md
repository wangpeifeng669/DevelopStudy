集合（五）Set
===
###（一）HashSet
1. 使用 HashMap 保存数据，很多原理跟 HashMap 类似。

    	HashMap<E, HashSet<E>> backingMap;
		@Override
		public boolean add(E object) {
		    return backingMap.put(object, this) == null;
		}
		@Override
	    public boolean contains(Object object) {
	        return backingMap.containsKey(object);
	    }
	    
2. contains跟 HashMap 一致，也是判断对象的 hash 值和 equal。

###（二）TreeSet
1. 有排序功能的 Set，构造器默认采用TreeMap生成，接口支持扩展。
 
		public TreeSet() {
		    backingMap = new TreeMap<E, Object>();
		}   
2. TreeMap中存放对象只需实现Comparator，即具有排序功能，存放时会自动排序。

###（三）HashMap和HashSet的区别
详见[这篇文章](http://www.importnew.com/6931.html)。