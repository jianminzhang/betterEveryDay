#PySpark

##参考

https://www.iteblog.com/archives/1395.html

##PySpark算子


1、join、leftOuterJoin和rightOuterJoin的区别

使用方法都是a.join(b)

join只保留两个集合中共同都有的键值部分；

leftOterJoin以a的键值为准，如果存在a中存在b中不存在的键值，那么b的属性部分为None

2、collect()类似操作会将结果load到内存，尽量少用collect()方法
