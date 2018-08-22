---
layout: post
title:  "Linux-Chrony"
date:   2018-08-22 00:00:00
categories: [Linux]
tags: [Chrony]
---

# Chrony
chrony CentOS 7 同步时间

------------
## 情景
某LAN 内有 3 台机器，需要保持LAN内机器时间一致。

		A-机器IP: 192.168.137.100
		B-机器IP: 192.168.137.101
		C-机器IP: 192.168.137.102

## 分析
	以 A-机器作为时间服务器，B-机器、C-机器 的时间与 A-机器 的时间同步即可。
	


------------
## 操作


- 修改 A 机器 /etc/chrony.conf 文件

	添加：
	
		server 192.168.137.100 iburst # NTP 服务器
		allow 192.168/16 	# 服务端允许连接的客户端网段
		local stratum 10

	如:
    ```
     [root@192 ~]# cat /etc/chrony.conf 
     # Use public servers from the pool.ntp.org project.
     # Please consider joining the pool (http://www.pool.ntp.org/join.html).
     #server 0.centos.pool.ntp.org iburst
     #server 1.centos.pool.ntp.org iburst
     #server 2.centos.pool.ntp.org iburst
     #server 3.centos.pool.ntp.org iburst
     server 192.168.137.100 iburst
     
     # Ignore stratum in source selection.
     stratumweight 0
     
     # Record the rate at which the system clock gains/losses time.
     driftfile /var/lib/chrony/drift
     
     # Enable kernel RTC synchronization.
     rtcsync
     
     # In first three updates step the system clock instead of slew
     # if the adjustment is larger than 10 seconds.
     makestep 10 3
     
     # Allow NTP client access from local network.
     allow 192.168/16
     
     # Listen for commands only on localhost.
     bindcmdaddress 127.0.0.1
     bindcmdaddress ::1
     
     # Serve time even if not synchronized to any NTP server.
     local stratum 10
     
     keyfile /etc/chrony.keys
     
     # Specify the key used as password for chronyc.
     commandkey 1
     
     # Generate command key if missing.
     generatecommandkey
     
     # Disable logging of client accesses.
     noclientlog
     
     # Send a message to syslog if a clock adjustment is larger than 0.5 seconds.
     logchange 0.5
     
     logdir /var/log/chrony
     #log measurements statistics tracking
     [root@192 ~]# 

	
    ```

- 修改 B、C 机器 /etc/chrony.conf 文件

	添加：
	
		server 192.168.137.100 iburst # NTP 服务器
	
		local stratum 10


	如:
    ```
    [root@192 ~]# cat /etc/chrony.conf 
    # Use public servers from the pool.ntp.org project.
    # Please consider joining the pool (http://www.pool.ntp.org/join.html).
    #server 0.centos.pool.ntp.org iburst
    #server 1.centos.pool.ntp.org iburst
    #server 2.centos.pool.ntp.org iburst
    #server 3.centos.pool.ntp.org iburst
    server 192.168.137.100 iburst
    
    # Ignore stratum in source selection.
    stratumweight 0
    
    # Record the rate at which the system clock gains/losses time.
    driftfile /var/lib/chrony/drift
    
    # Enable kernel RTC synchronization.
    rtcsync
    
    # In first three updates step the system clock instead of slew
    # if the adjustment is larger than 10 seconds.
    makestep 10 3
    
    # Allow NTP client access from local network.
    #allow 192.168/16
    
    # Listen for commands only on localhost.
    bindcmdaddress 127.0.0.1
    bindcmdaddress ::1
    
    # Serve time even if not synchronized to any NTP server.
    local stratum 10
    
    keyfile /etc/chrony.keys
    
    # Specify the key used as password for chronyc.
    commandkey 1
    
    # Generate command key if missing.
    generatecommandkey
    
    # Disable logging of client accesses.
    noclientlog
    
    # Send a message to syslog if a clock adjustment is larger than 0.5 seconds.
    logchange 0.5
    
    logdir /var/log/chrony
    #log measurements statistics tracking
    [root@192 ~]# 
    
    	
    ```



- 重启 chrond 服务
	
	重启A、B、C机器的chronyd 服务

    ```
    [root@192 ~]# systemctl restart chronyd.service
    [root@192 ~]# 
    ```

- 同步时间

	在 B、C 机器执行 ``chronyc -a makestep`` 同步时间即可。



## 注意事项

    1.chronyd 与 ntpd 服务是两个相同功能的东西，不能同时运行。
	也就是说，chronyd 的 status 是 running 时，ntpd 的 status 应是dead。
	
    2.同步失败，注意防火墙。


## 相关命令

```
	systemctl enable chronyd.service  # chronyd 服务开机启动
	systemctl status chronyd.service  # chronyd 服务状态

	timedatectl set-timezone Asia/Shanghai  #设置时区Asia/Shanghai
```



