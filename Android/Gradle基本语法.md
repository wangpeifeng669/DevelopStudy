Gradle基本语法
===
###（一）Groovy脚本语言
groovy是基于java语言，底层是把 groovy 转换为 java 语言实现，具体研究可以查看[这篇文章](http://wiki.jikexueyuan.com/project/deep-android-gradle/three-five.html)。

###（二）Gradle
Gradle 提供了一套 [DSL（领域特定语言）](http://www.infoq.com/cn/articles/dsl-discussion)，在 Groovy 基础上添加了自己的语法，是定制化的 Groovy。

###（三）Gradle简单使用
新建项目TestPic后，

项目结构  

 TestPic/  
          | settings.gradle  
          | build.gradle  
            + app/  
          | build.gradle  

settings.gradle配置编译的模块

    //编译的项目
    include ':app'

TestPic/build.gradle

	    // 全局配置，应用到所有的子项目和模块
	
	    //buildscript中的声明是gradle脚本自身需要使用的资源。可以声明的资源包括依赖项、第三方插件、maven仓库地址等。
	    //而在build.gradle文件中直接声明的依赖项、仓库地址等信息是项目自身需要的资源。
	    //gradle运行时先运行buildscript
	    buildscript {
	    //从中央库里面获取依赖,JCenter是存放所有Maven引用库的地方，导入后，就能在dependencies中依赖远程库
	        repositories {
	            jcenter()
	        }
	        //依赖gradle库，相当于导入类库
	        dependencies {
	            classpath 'com.android.tools.build:gradle:1.1.0'
	
	            // NOTE: Do not place your application dependencies here; they belong
	            // in the individual module build.gradle files
	        }
	    }
	
	// 应用到所有的子项目和模块
	allprojects {
	    repositories {
	        jcenter()
	    }
	}
	
执行顺序问题

	task myTask  <<  {
	   println ' I am myTask'
	}
	
对于<<代表 doLast 方法添加一个 Action。  
如果代码没有加<<，则这个任务在脚本 initialization（也就是你无论执行什么任务，这个任务都会被执行，I am myTask 都会被输出）的时候执行，如果加了<<，则在 gradle myTask 后才执行。
###（四）Gradle本质分析
####（1）脚本类对应关系
一般我们创建项目会生成settings.gradle和build.gradle文件，根据[gradle官方文档](https://docs.gradle.org/current/dsl/)，每个 gradle 脚本对应一个类，settings.gradle对应[Settings](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html)提供项目初始化配置模块功能，build.gradle对应[Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html)提供项目模块编译等操作。
####（2）默认引用对象关系
[官方文档](https://docs.gradle.org/current/userguide/writing_build_scripts.html#N10E18)中提到在脚本文件中使用的属性和函数，如果未在脚本中声明，则默认是Project对象的属性和函数。  

	println "name: " + project.name
	println "name: " + name
输出结果是一致的，project是 Project 自身的属性引用。  

buildscript则是Project中的函数

	void buildscript(Closure configureClosure);
参数是闭关，只有一个参数省略了括号。

repositories也是Project中的函数

	void repositories(Closure configureClosure);

jcenter不是Project中的函数，根据repositories函数说明，在这个闭包参数中默认传入RepositoryHandler作为委托对象，类似Project是整个 script 的委托对象。

	repositories { }

	Configures the repositories for this project.
	
	This method executes the given closure against the RepositoryHandler for this project. The RepositoryHandler is passed to the closure as the closure's delegate.
	
####（3）android plugin
通过在 build 中设置android [plugin](https://docs.gradle.org/current/userguide/plugins.html)

	apply plugin: 'com.android.application'
引用 android 应用插件，[android gradle dsl](http://google.github.io/android-gradle-dsl/current/index.html) 中有相关 api 说明

	android {
	
	    // 多渠道打包
	    productFlavors {
	        channel0 {
	            manifestPlaceholders = [provider: "0", channel: "0"]
	        }
	        channel1 {
	            manifestPlaceholders = [provider: "1", channel: "1"]
	        }
	    }
	
	    //当前运行时编译的sdk版本号
	    compileSdkVersion 21
	}
[AppExtension](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.AppExtension.html)是扩展类，android代表AppExtension类，command点击 android进入AppExtension类，继承BaseExtension类。

	public class AppExtension extends BaseExtension
    //productFlavors函数调用
	void productFlavors(Action<? super NamedDomainObjectContainer<GroupableProductFlavor>> action)
channel0和channel1都是GroupableProductFlavor类。

	compileSdkVersion 21
	//compileSdkVersion函数调用
	void compileSdkVersion(int apiLevel) {
        compileSdkVersion("android-" + apiLevel)
    }