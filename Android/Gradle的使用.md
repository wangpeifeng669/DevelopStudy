Gradle的使用
===
###（一）定义
android 编译系统包含 gradle 构建插件，支持依赖导入及编译过程配置。

###（二）用途
####1、配置编译和应用信息
如编译sdk版本号、app版本号、应用id等。
####2、声明依赖
支持本地jar库引用、本地项目lib引用及maven网络库引用。
####3、测试应用
gradle通过测试文件夹自动生成测试的 apk，方便测试
####4、签名应用
支持配置签名信息
####5、多渠道打包
支持打包出多个版本apk

###（三）简单使用
新建项目后，导入图片加载lib库image_library 如下
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Android/pic/Gradle%E7%9A%84%E4%BD%BF%E7%94%A8.png?raw=true)

项目结构  

 TestPic/  
          | settings.gradle  
          | build.gradle  
            + app/  
          | build.gradle  
            + libraries image_library/  
          | build.gradle  

settings.gradle配置编译的模块

    //编译的项目
    include ':app'
    include ':image_library'

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

app/build.gradle

    //声明该模块是应用类型，能直接运行
    apply plugin: 'com.android.application'

    //所有app本身的配置，包括编译sdk版本、版本号、是否混淆、签名文件配置等
    android {
        //当前运行时编译的sdk版本号
        compileSdkVersion 21
        //构建工具的版本，其中包括了打包工具aapt、dx等等。这个工具的目录位于..your_sdk_path/build-tools/XX.XX.XX
        buildToolsVersion "21.1.1"

        //默认配置，包含debug、release等模式下的默认配置，可以在具体模式下覆盖设置
        defaultConfig {
            //app标识，区别不同app的凭证，和包名区别开，包名不再用来唯一标识app，修改包名不需要再修改代码，解耦
            //具体applicationId和package name的区别见http://blog.csdn.net/maosidiaoxian/article/details/41719357
            applicationId "com.cancai.testpic"
            //允许执行的手机sdk最小版本
            minSdkVersion 8
            //与编译无关，标识当前运行手机的sdk版本
            targetSdkVersion 21
            versionCode 1
            versionName "1.0"
        }
        //配置debug和release模式，包括签名及混淆等
        buildTypes {
            //release模式下的配置
            release {
                //是否使用混淆
                minifyEnabled false
                //混淆文件位置
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
    }

//依赖设置，设置引用的类库

    dependencies {
        //默认设置libs文件夹下的所有jar文件为引用类库
        compile fileTree(dir: 'libs', include: ['*.jar'])
        //使用maven上的库，引用时会查看本地是否存在，不存在下载
        compile 'com.android.support:appcompat-v7:21.0.3'
        compile 'com.jakewharton:butterknife:7.0.1'
        //引用image_library模块
        compile project(':image_library')
    }
