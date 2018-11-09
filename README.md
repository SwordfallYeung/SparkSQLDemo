# SparkSQLDemo
记录spark sql的一些基本知识点

spark sql优化<br/>
https://blog.csdn.net/zhao897426182/article/details/78354083

### spark sql优化比对：
查找两个表的共同数据<br/>
方式1：<br/>
>　//共同的imsi
　val commonImsiDs = sparkSession.sql("select distinct(t1.imsi) from imsiRecord1 t1, imsiRecord2 t2 where t1.imsi = t2.imsi")
　commonImsiDs.createOrReplaceTempView("commonImsi")
　commonImsiDs.show()
 
方式2：<br/>
>　val imsiRecord1CommonDs = sparkSession.sql("select t1.imsi, count(t1.imsi) as imsiCount1 from imsiRecord1 t1 group by t1.imsi")
　imsiRecord1CommonDs.createOrReplaceTempView("imsiRecord1CommonDs")
　val imsiRecord2CommonDs = sparkSession.sql("select t2.imsi, count(t2.imsi) as imsiCount2 from imsiRecord2 t2 group by t2.imsi")　　　　
　imsiRecord2CommonDs.createOrReplaceTempView("imsiRecord2CommonDs")
　val commonDS = sparkSession.sql("select t1.imsi, t1.imsiCount1, t2.imsiCount2 from imsiRecord1CommonDs t1, imsiRecord2CommonDs t2 where t1.imsi=t2.imsi")
　commonDS.createOrReplaceTempView("commonDS")
　commonDs.show()
 
经过测试，发现方式2运行时间比方式1的少，原因可能在于方式1一开始运算的量是两个表的总和，运行的时间比较耗费；而方式2逐个削减表的量，然后再寻找相同的数据出来，运算的量少，运行时间快。
