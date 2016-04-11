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
###（四）Gradle本质分析
####（1）脚本类对应关系
一般我们创建项目会生成settings.gradle和build.gradle文件，根据[gradle官方文档](https://docs.gradle.org/current/dsl/)，每个 gradle 脚本对应一个类，settings.gradle对应[Settings](https://docs.gradle.org/current/dsl/org.gradle.api.initialization.Settings.html)提供项目初始化配置模块功能，build.gradle对应[Project](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html)提供项目模块编译等操作。