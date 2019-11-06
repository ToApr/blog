---
title: RabbitMq集群
date: 2019-11-05 17:11:21
tags: rabbitmq
---
# RabbitMq 集群
  三台机子 - rabbit1，rabbit2， rabbit3
 ### 1.第一步  
配置各个节点的hosts 文件，让各个节点都能互相识别对方的存在。比如在LÏnux 系统中可以编辑/etc/hosts 文件，在其上添加E 地址与节点
Windows Host 位置    C:\Windows\System32\drivers\etc  
名称的映射信息:  

|ip|机器名|
|---|---|
|192 . 168.0 .2 |rabbit1|
|192.168.0 .3 | rabbit2|
|192.168.0 . 4 |rabbit3|

### 2. 第二步  
编辑RabbitMQ 的cookie 文件，以确保各个节点的cookie 文件使用的是同一个值。
可以读取node1 节点的cookie 值， 然后将其复制到node2 和node3 节点中。cookie 文件默认路
径为C:\WINDOWS\system32\config\systemprofile 或者C:\Users%USERNAME%.erlang.cookie 相当于密钥令牌，集群中的RabbitMQ 节点需要通过交换密钥令牌以获得相互认证。  
### 3. 第三步  
Rabbit2 和rabbit3加入rabbit1.  

重置 rabbit2 ,rabbit3.   

**＃on rabbit2 **  

`rabbitmqctl stop_app ＃=>停止节点兔子@ rabbit2 ...完成。   `

`rabbitmqctl reset ＃=>重置节点rabbit @ rabbit2 ...   `

`rabbitmqctl join_cluster rabbit @ rabbit1 ＃=>群集节点rabbit @ rabbit2与[rabbit @ rabbit1] ...完成。  `

`rabbitmqctl start_app ＃=>起始节点兔子@ rabbit2 ...完成。  `

`rabbitmqctl cluster_status 查看集群状态`



**#on rabbit3**   

`rabbitmqctl stop_app ＃=>停止节点兔子@ rabbit3 ...完成。#on rabbit3   `

`rabbitmqctl reset ＃=>重置节点rabbit @ rabbit3 ...   `

`rabbitmqctl join_cluster rabbit @ rabbit2 ＃=>群集节点兔子@ rabbit3与兔子@ rabbit2 ...完成。  `

`rabbitmqctl start_app ＃=>启动节点rabbit @ rabbit3 ...完成。  `

`rabbitmqctl cluster_status 查看集群状态  `

＃=>节点兔子的集群状态@ rabbit3 ... ＃=> [{nodes，[{disc，[rabbit @ rabbit1，rabbit @ rabbit2，rabbit @ rabbit3]}]}，＃=> { running_nodes，[rabbit @ rabbit2，rabbit @ rabbit3]}] ＃=> ...完成。#on rabbit3  

移除集群节点
两种方法： 
1.	要移除的节点存活，可以正常使用ctl 命令  

`rabbitmqctl  stop_app  停止节点  `

`rabbitmqctl reset      重置节点，包括集群信息。  `

验证  

`rabbitmqctl  start_app   `

`rabbitmqctl cluster_status   ` 

2.要移除的节点死了，不能使用ctl命令  

 `rabbitmqctl forget_cluster_node rabbit@nodel –offline ` 

" 一offline" 参数的添加让其可以在非运行状态下将nodel 剥离出当前集群。


