# zabbix集成spring_boot_actuator



#### 1、关于监控系统主要用zabbix因此将各个执行器的监控放在zabbix查看

![image-20221031154922729](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031154922729.png)

​        因为PJE项目具有三个执行器因此需要监控3个执行器、并且监控系统主要是zabbix因此放普罗米修斯也行，放zabbix。本次采用zabbix因此直接监控普罗米修斯接口、创建zabbix监控项。





## 一、普通监控项模板

准备：三个监控项、应用集、触发器、发动发现

![image-20221031155020318](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155020318.png)

![image-20221031155056472](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155056472.png)

![image-20221031155107518](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155107518.png)

最重要的是将接口的数据以文本的形式获取，再通过监控项来切割每个需要监控的监控项。

### 1.1、创建获取文件的监控项

![image-20221031160305213](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031160305213.png)

```shell
#名称
xxl-job-admin.prometheus
#键值
xxl-job-admin.prometheus
#URL
http://{HOST.CONN}:{$PJE_XXL_JOB_PORT}/{$JOB_3}/actuator/prometheus
```



### 1.2、宏定义

![image-20221031160514238](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031160514238.png)





## 二、有变量的监控项-自动发现

![image-20221031155147999](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155147999.png)





### 2.1、自动发现

![image-20221031155222458](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155222458.png)



### 2.2、进程配置

![image-20221031155302409](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155302409.png)

```shell
#进程

http_server_requests_seconds_count{exception=~".*",method=~".*",outcome=~".*",status=~".*",uri=~".*",}
```





### 2.3、定义变量

![image-20221031155355269](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155355269.png)



### 2.4、配置监控项原型



#### 2.4.1、监控项原型配置



![image-20221031155426411](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155426411.png)

```shell
#名称
xxl-job-admin.http_server_requests_seconds_count[{#METHOD} [{#STATUS}] - {#URI}]

#键值
xxl-job-admin.http_server_requests_seconds_count[{#EXCEPTION},{#METHOD},{#OUTCOME},{#STATUS},{#URI}]

#主要项
PJE Xxl-job-admin-Prometheus: xxl-job-admin.prometheus


```





#### 2.4.2、监控项原型进程配置

![image-20221031155532615](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031155532615.png)





```shell
#参数

http_server_requests_seconds_count{exception="{#EXCEPTION}",method="{#METHOD}",outcome="{#OUTCOME}",status="{#STATUS}",uri="{#URI}",}

```







## 三、获取最新数据

### 3.1触发器配置

![image-20221031160711210](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031160711210.png)



```shell
#名称
PJE Xxl-job-admin Database Down
#表达式
{PJE Xxl-job-admin-Prometheus:xxl-job-admin.components.db[status].str(DOWN)}=1 or {PJE Xxl-job-admin-Prometheus:xxl-job-admin.components.db[status].nodata(10m)}=1
```



![image-20221031160748290](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031160748290.png)





## 四、grafana设置

### 4.1、总体设置





### 4.2、变量设置

![image-20221031160939448](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031160939448.png)



### 4.3、quick facts

![image-20221031161047121](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161047121.png)

```shell
    主要监控进程的启动时间、应用开始时间、堆使用占用内存、非堆占用内存、执行器的健康状况

```



### 4.4、HTTP Statics

![image-20221031161303194](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161303194.png)



```
    主要监控错误日志、Tomcat状态、http每个请求
```



### 4.5、JVM Memory Total

![image-20221031161445234](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161445234.png)



```
    主要监控jvm堆使用、总使用
```



### 4.6、JVM MIsc

![image-20221031161541728](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161541728.png)



### 4.7、 JVM Memory Pools

![image-20221031161616384](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161616384.png)

 

### 4.8、  JVM Statistics-GC and Classloading

![image-20221031161700495](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161700495.png)





### 4.9、Buffer Pools

![image-20221031161738510](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161738510.png)



### 4.10、Tomcat Statics

![image-20221031161808561](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161808561.png)



### 4.11、Logback Statistics

![image-20221031161830230](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161830230.png)



### 4.12、HikarCP Statistics

该监控只有xxl-job-admin具有。

![image-20221031161902088](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031161902088.png)







![image-20221031162005464](https://github.com/liangmartin/zabbix-spring_boot_actuator/blob/master/images/image-20221031162005464.png)





## 五、模板

```shell
模板在svn中
```



















