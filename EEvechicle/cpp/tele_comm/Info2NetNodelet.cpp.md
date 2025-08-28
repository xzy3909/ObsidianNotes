Function:
1. LoadParameters(2)
2. NetWorkIsConnected()
3. Refresh()
4. timerCallback()
	DES:
		20s定时检查心跳
		[["ros2mqtt_under_heartbeat"]]
	IN:
	OUT:
		pub
5. timer1Callback()
	DES:
		1s定时函数
		将所有下位机状态发送到对应的ros2mqtt话题中
	IN:
		sub under
	OUT:
		pub mqtt
6. callback_gps()
7. callback_imu()
8. callback_equipment()
9. callback_ultrasound()
10. callback_power()
11. callback_motors()
12. callback_global_path()
13. callback_local_path()
14. callback_status()
15. under_control()
Relation:
	Topic:
		SUB:
			[["gnss"]] 
			[["vehicle_imu"]]
			[["vehicle_status"]]
			[["vehicle_equipment_status"]]
			[["vehicle_ultrasound"]]
			[["vehicle_power"]]
			[["viz_path_topic"]]
			[["path"]]
		PUB:
			[["ros2mqtt_under_heartbeat"]]
			[["ros2mqtt_under_gps"]]
			[["ros2mqtt_under_imu"]]
			[["ros2mqtt_under_status"]]
			[["ros2mqtt_under_power"]]
			[["ros2mqtt_under_path"]]
			[["ros2mqtt_under_control"]] 
	CLI:
		[["mqtt_client_is_connected"]] 
	SRV:
		[["refresh_info2net_params"]]