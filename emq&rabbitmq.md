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

	
	
		