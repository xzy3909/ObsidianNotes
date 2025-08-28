vehicle_state????全局状态变量有哪些，是否重复????
	-------->error_warning_map记录 油位水位温度湿度，左右履带三推杆????
	-------->vehicle_state记录剩余信息???那些???
exceptionThreshold传感器异常阈值
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
		error_warning_map
			将故障传递backgroundexceptionData变量
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
	DES:
		[["vehicle_status"]]回调函数，获取auto_state和state_cmd
	IN:
		&status
	OUT:
		vehicle_state
			.LAST_STATE
			.LAST_CMD
		if(isCommSerialError)
			rest;
				error_warning_map
				issensorWarning
				issensorError
11. equipmentStatusCallback()
	DES:
		获取油位、水位、温度、湿度信息[["vehicle_equipment_status_error"]] 
	IN:
		&status
	OUT:
		error_warning_map.emplace
			OIL_LEVEL
			WATER_LEVEL
			TEMPERATURE
			HUMIDITY
12. tangkouInfoCallback()-todo
	DES:
		塘口信息回调函数[["tangkou_task_info"]] 
	IN:
		&msg
	OUT:
		auto_state = 1
		tangkou_info = *msg
13. underpowerCallback()
	DES:
		[["ros2mqtt_under_power"]] 回调函数
	IN:
		&msg
	OUT:
		underPowerData
14. understatusCallback()
	DES:
		[["ros2mqtt_under_status"]]回调函数
			获取电量
	IN:
		&msg
	OUT:
		vehicle_state
			.BATTERY
15. gpsStateCallback()
	DES:
		[["gnss"]]回调函数，获取速度cur_vel，GPS状态
	IN:
		&gnss
	OUT:
		cur_vel
		vehicle_state
			.GPS
		GPSStatus
16. stateCheckCallback()
	DES:
		单个状态回调函数
		[["vehicles_statecheck"]]回调函数，将下位机状态赋值给vehicle_state，vehicle_error
	IN:
		&msg
	OUT:
		vehicle_state
		vehicle_error
17. stateChecksCallback()
	DES:
		多个状态回调函数
		[["vehicle_statechecks_error"]] 
	IN:
		&msg
	OUT:
		vehicle_state
		vehicle_error
		vehicle_warning
		isEnableTask()
			检查异常，尝试调用作业，上发异常
		exceptionProcess()
			检查异常，执行就近任务返航
18. isEnableTask()--???--逻辑流程
	DES:
		在stateChecksCallback()中被调用
			检查异常并尝试调用[["task_exception_handler"]]和[["control_exception_handler"]] 服务
	IN:
		遍历vehicle_warning中的履带推杆信息
		NetWorkStatus
		GPSStatus
		BATTERYStatus
		sensorError
	OUT:
		pubExceptionStatus
		[["task_exception_handler"]] 
		[["control_exception_handler"]] 
19. exceptionProcess()--???--逻辑流程
	DES:
		检查异常，有异常则执行就近任务返航
	IN:
		vehicle_state
		stateErrorcount
	OUT:
20. callExceptionProcessSrv()
	DES:
		服务调用封装，异常处理接口
	IN:
		&exception_client
		&exception_cmd
	OUT:
		client.call(cmd)
21. isStateError()
	DES:
		在isEnableTask()和exceptionProcess()中被调用
			检查状态返回标志位isError
	IN:
		&vehicle_state, const int &id
	OUT:
		isError标志位-(stm32，电机，油位水位)
22. pubExceptionStatus()
	DES:
		异常上发函数[["ros2mqtt_under_exception"]] 
			在timerCallback(),NetWorkStateCallback(),isEnableTask(),exceptionProcess()中被调用
	IN:
		&exceptionstatus
	OUT:
		pub_under_exception_.publish(exception_msg);
23. loadExceptionConfig()
	DES:
		加载异常数据到全局变量
		在onInit中被调用，加载[[exceptionConfig.json]] 
	IN:
		[[exceptionConfig.json]] / &filename /refresh_file/update_file
	OUT:
		LoadParameters()
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
26. loadMinPowerForTask()---UnUse
	DES:
		根据行驶任务历程，判断当前剩余电量是否具备工作条件
	IN:
		task_path[[Task_path.json]]
	OUT:
		remainPower
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
			