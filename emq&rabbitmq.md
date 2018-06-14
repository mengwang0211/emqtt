### 搭建emq

	1: 下载启动emq
	
		下载emq
		
		cd /usr/local
		mkdir emq
		cd emq
		【这里是ubuntu版本 其他版本对应其他链接	】
		wget http://emqtt.com/downloads/2316/ubuntu16_04 -O emqtt
		unzip emqtt
		cd emqtt
		
		# 启动emqttd
		./bin/emqttd start
		
		/
			# 检查运行状态
			./bin/emqttd_ctl status

			# 停止emqttd
			./bin/emqttd stop
		/
		
		启动后 可以看到 dashboard的信息 ip:18083  eg：139.219.139.250：18083
		
### 负载均衡安装与配置

	
	cd /usr/local
	
	// 下载haproxy源码 码云的镜像国内服务器比较快 github的镜像比较慢 
	git clone https://gitee.com/mirrors/haproxy.git
	
	// 源码编译 源码编译前需要有gcc环境  ubuntu 用apt-get安装gcc  centos用yum安装gcc  (Google或Baidu即可)
	sudo make TARGET=linux2628 PREFIX=/etc/haproxy  // 2628为linux内核版本 可用 uname -r 查看内核 然后替代
	
	// 安装
	sudo make install PREFIX=/etc/haproxy
	
	cd etc/haproxy
	
	touch haproxy.cfg
	vi haproxy.cfg
	
	// emq1 ip信息
	// emq2 ip信息
	
	listen mqtt-ssl
    bind *:8883 ssl crt /etc/ssl/emqttd/emq.pem no-sslv3
    mode tcp
    maxconn 50000
    timeout client 600s
    default_backend emq_cluster

	backend emq_cluster
		mode tcp
		balance source
		timeout server 50s
		timeout check 5000
		server emq1 10.0.8.5:1883 check inter 10000 fall 2 rise 5 weight 1
		server emq2 10.0.8.6:1883 check inter 10000 fall 2 rise 5 weight 1
		source 0.0.0.0 usesrc clientip
	
	
### emq配置信息

	cd /etc
	
	EMQ 2.0 消息服务器通过 etc/ 目录下配置文件进行设置，主要配置文件包括:

	配置文件					说明
	etc/emq.conf			EMQ 2.0 消息服务器配置文件
	etc/acl.conf			EMQ 2.0 默认ACL规则配置文件
	etc/plugins/*.conf		EMQ 2.0 各类插件配置文件
	
	touch emq.conf
	
	vi emq.conf
	
	## Cluster name
	cluster.name = emqcl

	## Cluster Autoheal: on | off
	cluster.autoheal = on

	## Clean down node of the cluster
	cluster.autoclean = 5m

	node.name = emqttd@127.0.0.1

	## Cluster discovery strategy: manual | static | mcast | dns | etcd | k8s
	cluster.discovery = static
	cluster.static.seeds = emqttd@127.0.0.1,emqc2@10.0.8.5,emqc3@10.0.8.6
	
	
	
	需要开放18083端口作为http dashboard的页面接口
	需要开放1883端口作为tcp mqtt broker的端口
	
	export EMQ_NODE_NAME=emq@s1.emqtt.io && ./bin/emqttd start
	
	./bin/emqttd_ctl cluster join node1@10.0.8.5
	
	
	
	
	
	
	
	
		