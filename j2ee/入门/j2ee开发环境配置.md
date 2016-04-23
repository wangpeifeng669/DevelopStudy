j2ee开发环境配置
===
安装电脑配置：  
macbook air  
mac os  
默认自带 jdk
###（一）Eclipse for EE 下载
从[下载地址](http://www.eclipse.org/downloads/)下载Eclipse
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_1.png?raw=true)

###（二）Tomcat 下载
从[这里](http://tomcat.apache.org/download-80.cgi)下载Tomcat
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_2.png?raw=true)

###（三）Tomcat配置测试
cd到Tomcat所在目录，然后执行：sh ./bin/startup.sh，就可以启动Tomcat，执行sh ./bin/shutdown，就可以关闭Tomcat。  
启动 tomcat 后在浏览器地址栏中输入http://localhost:8080，显示
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_3.png?raw=true)
说明启动成功。
为了方便启动，可以写个 shell，peter_run.sh，需要修改 bin 里面的文件为可执行权限。

	pwd
	cd "${0%/*}"
	pwd
	chmod -R 777 ./bin
	sh ./bin/startup.sh

###（四）给 Eclipse 增加 Tomcat 插件
下载下来的 Eclipse 没有自带 Tomcat 插件，偏好设置里面没有 tomcat 选项，参考[这里](http://blog.csdn.net/guyuealian/article/details/50779810)增加Tomcat插件。
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_4.png?raw=true)

###（五）Eclipse配置 tomcat
####1、按图配置 tomcat
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_4.png?raw=true)

####2、增加运行环境
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/%E5%85%A5%E9%97%A8/j2ee%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE_5.png?raw=true)