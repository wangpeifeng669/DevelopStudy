jsp简介
===
###（一）jsp起源
servlet 实现输出时需要写很多out.println较为繁琐，诞生 jsp 技术将 UI 层分离。
###（二）jsp 执行流程
jsp 是内嵌在 html 中的特殊语法，执行时会转换为 servlet，执行流程：  
request->jsp 转成 servlet->编译servlet程 class->执行 class 并返回 response  
class 生成后不更新 jsp 文件会重复使用，提升性能。

