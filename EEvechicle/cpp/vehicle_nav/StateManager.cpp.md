what is error_warning_map?????
Fuction:
1. onInit()
	DES:初始化
2. Refresh()
	DES:
		[["refresh_statemanger_params"]]服务函数，传入文件路径，调用LoadParameters()加载参数;
	IN:
		&req(param_path) / "refresh_file" default="$(find vehicle_nav)/config/refresh_params.yaml"
	OUT:
		LoadParameters(file):
			将is_shield屏蔽配置传递到exceptionConfig[[exceptionConfig.json]] 
3. timerCallback()
	DES:
		1s回调函数，更新isheartbeatFlag，每120秒调用clearExceptionConfig清空全局异常变量
		检测网络异常、通信异常、gps异常、mqtt上发异常，更新对应标志位
	IN:
		ros::Time
			用于更新isheartbeatFlag
		cleaConfigTime_
			调用clearExceptionConfig
	OUT:
		isheartbeatFlag
		clearExceptionConfig():
			重置exceptionConfigData，backgroundexceptionData
		network_exception_flag
		isCommSerialError
		GPSErrorFlag
		is_pub_error
4. autoCallback()
	DES:
		订阅[["auto_state"]]，赋值auto_state
	IN:
		&msg
	OUT:
		auto_state
5. pathstopCallback()
	DES:
		订阅[["pathstop"]]，更新is_task_return标志位
	IN:
		&msg
	OUT:
		is_task_return
6. heartbeatCallback()
	DES:
		订阅[["mqtt2ros_under_heartbeat"]]，更新isheartbeatFlag标志位
	IN:
		&msg
	OUT:
		isheartbeatFlag
7. NetWorkStateCallback()
	DES:
		调用[["mqtt_client_is_connected"]]服务，更新network_exception_flag
			if(network_exception_flag):
			 调用callExceptionProcessSrv()恢复任务[["task_exception_handler"]]
			 else: chuyue chuyu auto_state meipingbi rtk 断网持续5min 任务暂停
	IN:
		&msg
		exceptionConfigData
			rtk
	OUT:
		network_exception_flag
			callExceptionProcessSrv()
				[["task_exception_handler"]] resume/pause
		vehicle_state
8. isNetworkAvailable()
	DES:
		ping
	IN:
	OUT:
9. stateService()
	DES:
		[["get_state"]]服务函数，访问vehicle_state获取状态
	IN:
		req.IDs
	OUT:
		res.State
10. statusCallback()

11. equipmentStatusCallback()
12. tangkouInfoCallback()
13. underpowerCallback()
14. understatusCallback()
15. gpsStateCallback()
16. stateCheckCallback()
	DES:
		[["vehicles_statecheck"]]回调函数，将下位机状态赋值给vehicle_state，vehicle_error
	IN:
		&msg
	OUT:
		vehicle_state
		vehicle_error
17. stateChecksCallback()
18. isEnableTask()
19. exceptionProcess()
20. callExceptionProcessSrv()
	DES:
		服务调用封装
	IN:
		&exception_client
		&exception_cmd
	OUT:
		client.call(cmd)
21. isStateError()
22. pubExceptionStatus()
23. loadExceptionConfig()
24. clearExceptionConfig()
	DES:
		读取[[exceptionConfig.json]]
		加载异常内容，赋值全局变量，并清空
		未修改读取文件
	IN:
		&filename [[exceptionConfig.json]] 
	OUT:
		重置exceptionConfigData
		backgroundexceptionData
25. LoadParameters()
	DES:
		加载[[refresh_params.yaml]] ，传递屏蔽到[[exceptionConfig.json]] 
	IN:
		 file_path/[[refresh_params.yaml]]
	OUT:
		[[exceptionConfig.json]] 
26. loadMinPowerForTask()
Relation:
	Topic:
		SUB:
			[["gnss"]]
			[["vehicles_statecheck"]]
			[["vehicle_statechecks_error"]]
			[["vehicle_status"]]
			[["tangkou_task_info"]]
			[["vehicle_equipment_status_error"]]
			[["auto_state"]]
			[["pathstop"]]
			[["mqtt2ros_under_heartbeat"]]
			[["ros2mqtt_under_status"]]
			[["ros2mqtt_under_power"]]
		PUB:
			[["ros2mqtt_under_exception"]]
		CLI:
			[["mqtt_client_is_connected"]]
			[["NavigationNodelet_hovering"]]
			[["control_exception_handler"]]
			[["task_exception_handler"]]
			[["task_return_exception_handler"]]
		SRV:
			[["get_state"]]
			