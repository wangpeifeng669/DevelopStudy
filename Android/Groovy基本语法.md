Groovy基本语法
===
在线编译器：http://ideone.com/ai3ZbD

Groovy是Java平台上设计的面向对象编程语言，可以作为Java平台的脚本语言使用。在 android studio 中用的 gradle 就是基于 Groovy 语言的，学习了[《Groovy 入门经典》](http://book.douban.com/subject/2307272/)，要点如下。

#####（1）为什么使用脚本语言
脚本语言和系统语言（如 java）设计的目的不同。脚本语言多用于连接已有的程序，不是实现复杂的算法和数据结构，代码量少，开发效率高，多用于小中型项目。

#####（2）数值和表达式
Groovy是一门面向对象的语言，所以任何东西都是对象，能直接调用方法，如123.plus(456)
定义变量用 def，弱类型不需要定义类型，而且可以改变类型，如
def count = 0  
count = '0' 

#####（3）字符串
字符串可以用单引号(')、双引号(")、三引号('' '' '')表示，单引号不解释字符串，双引号三引号解释字符串可以加入变量，如
def age = 2  
'my age is ${age}'//my age is ${age}  
"my age is ${age}"//my age is 2 

#####（4）列表（list）、映射（map）和范围（range）
列表范例：
def numbers = [11,12,13,14]  
numbers[3]//14  
numbers[-1]//负数倒序获取，14  
[11,12,13,14].add(15)//[11,12,13,14,15] 
映射范例：
def names = ['1':'wang','2':'li',3:'zhang']  
names['1']//wang  
names.1//wang  
names[3]//zhang  
names.put('4','guo') 
范围范例：
def start = 1  
def finish = 4  
start..finish//[1,2,3,4]，包含关系  
start..<finish//[1,2,3]，不包含关系 

#####（5）输入输出
print 'i am'//不换行输出  
println 'i am'//换行输出  

def nums = [1,2,3]  
def names = ['1':'wang','2':'li',3:'zhang']  
println "${nums}"//[1,2,3]  
println "${names}"// ['1':'wang','2':'li',3:'zhang']  
  
def i = 1  
def f = 1.0  
def s = 'me'  
print("%i,%f,%s\n",[i,f,s])//1,1.0,me  

#####（6）方法
最简单的方法调用
def greetings(){
  println "hello world"
}
greetings()

#####（7）流程控制
def LENGTH = 10
println "start"
for(count in 1..LENGTH)
  println "count:${count}"
println "done"

#####（8）闭包
//基本闭包的使用，def 变量名 = {参数名-> 执行部分}
def doubles = {
	item->
	println item*2
}

//最原始的调用
doubles.call(3)
//可以去除call
doubles(3)
//只有一个参数可以括号也去除
doubles 3

指定参数名item
def doubles = {
	item->
	println item*2
}
//默认参数用it代替
def plus = {
	println it+2
}
def list = [1,2,3,4]

list.each doubles
//只有一个参数可以去除括号
list.each(plus)

