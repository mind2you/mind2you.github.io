---
layout: post
title:  "Cache-Redis"
date:   2015-03-08 22:21:49
categories: [Cache]
tags: [Redis]
---

# Redis

------------
## Standalone（单节点）


- Download tar (example: redis-3.2.8.tar.gz)

- Make
See README.


------------
## Cluster（集群）


- 以 redis-3.2.8 运行3个实例为例，比如，建立如下目录结构

```
redis-3.2.8_cluster
		├── config
		│   ├── redis_31337.conf
		│   ├── redis_31338.conf
		│   └── redis_31339.conf
		│
		├── dbfile
		│
		├── logs
		│
		├── redis-3.2.8
		│   ├── mkreleasehdr.sh
		│   ├── redis-benchmark
		│   ├── redis-check-aof
		│   ├── redis-check-rdb
		│   ├── redis-cli
		│   ├── redis-sentinel
		│   ├── redis-server
		│   └── redis-trib.rb
		│
		├── redis-3.2.2.gem #ruby redis gem
		└── redis-trib.rb
```


- 一些配置项

```
	bind <ip>
	port <port>
	daemonize yes
	pidfile <path>
	logfile <filepath>
	dbfilename <filename>
	dir <directory>
	appendfilename <filename>
	cluster-enabled yes
	
```

参数的具体意义参考redis doc。
config下的三个文件 redis_31337.conf


- 运行redis instances
如：

```
/home/test/redis-3.2.8_cluster/redis-3.2.8/redis-server /home/test/redis-3.2.8_cluster/config/redis_31337.conf
/home/test/redis-3.2.8_cluster/redis-3.2.8/redis-server /home/test/redis-3.2.8_cluster/config/redis_31338.conf
/home/test/redis-3.2.8_cluster/redis-3.2.8/redis-server /home/test/redis-3.2.8_cluster/config/redis_31339.conf
```


- Create cluster
	执行命令

```
./redis-trib.rb create --replicas 0 192.168.137.128:31337 192.168.137.128:31338 192.168.137.128:31339	
```




ERROR-1:若提示 increase "ulimit -n"则，以root用户登录主机
在文件`` /etc/security/limits.d/20-nproc.conf ``添加如下行：

```
<redis安装用户>      soft    nofile     65536
<redis安装用户>      hard    nofile     65536
```


ERROR-2:如果执行`` ./redis-trib.rb ``命令报错
确保机器有ruby的环境下，下载 redis gem，执行 `` gem install ./redis-3.2.2.gem `` 命令安装。




