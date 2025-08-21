加载优先级
更新方式
?????
DES:
	用于APP加载本地参数，并通过APP修改参数并修改本地参数和刷新程序参数。
	检查并向MQTT上发传感器异常
	更新boat_state
1. 重要成员 
	boat_state.resize(25, 255);
	boat_state[boat_msgs::BoatStateCheck::LAST_STATE] = 1;
	boat_state[boat_msgs::BoatStateCheck::LAST_CMD] = 0;
	issensorError.resize(25, 0);
	issensorWarning.resize(25, 0);
	boat_error.resize(25, 0);
	boat_warning.resize(25, 0);
	isCallException.resize(25, 0);
	stateErrorcount.resize(25, 0);
	sensorError.resize(11, "");
	sensorError[0] = "NO_ERROR";
	sensorError[1] = "ERROR_OPEN";
	sensorError[2] = "ERROR_STALL";
	sensorError[3] = "ERROR_SUPPLY";
	sensorError[4] = "ERROR_SENSOR";
	sensorError[5] = "ERROR_CONNECT";
	sensorError[6] = "NO_DATA_RECEIVED";
	sensorError[7] = "DATA_PARSING_ERROR";
	sensorError[8] = "NO_WAVEFORM_CAPTURED";
	sensorError[9] = "TELE_CONTROL_DISCONNECTED";
	sensorError[10] = "IMU_DROP";
	sensorWarning.resize(32, "");
	sensorWarning[0] = "NO_WARNING";
	sensorWarning[1] = "WARNING_OPEN";
	sensorWarning[2] = "WARNING_STALL";
	sensorWarning[3] = "WARNING_STALL, WARNING_OPEN";
	sensorWarning[4] = "WARNING_SUPPLY";
	sensorWarning[5] = "WARNING_SUPPLY, WARNING_OPEN";
	sensorWarning[6] = "WARNING_SUPPLY, WARNING_STALL";
	sensorWarning[7] = "WARNING_SUPPLY, WARNING_STALL, WARNING_OPEN";
	sensorWarning[8] = "WARNING_SENSOR";
	sensorWarning[9] = "WARNING_SENSOR, WARNING_OPEN";
	sensorWarning[10] = "WARNING_SENSOR, WARNING_STALL";
	sensorWarning[11] = "WARNING_SENSOR, WARNING_STALL, WARNING_OPEN";
	sensorWarning[12] = "WARNING_SENSOR, WARNING_SUPPLY";
	sensorWarning[13] = "WARNING_SENSOR, WARNING_SUPPLY, WARNING_OPEN";
	sensorWarning[14] = "WARNING_SENSOR, WARNING_SUPPLY, WARNING_STALL";
	sensorWarning[15] = "WARNING_SENSOR, WARNING_SUPPLY, WARNING_STALL, WARNING_OPEN";
	sensorWarning[16] = "WARNING_CONFIG";
	sensorWarning[17] = "WARNING_CONFIG, WARNING_OPEN";
	sensorWarning[18] = "WARNING_CONFIG, WARNING_STALL";
	sensorWarning[19] = "WARNING_CONFIG, WARNING_STALL, WARNING_OPEN";
	sensorWarning[20] = "WARNING_CONFIG, WARNING_SUPPLY";
	sensorWarning[21] = "WARNING_CONFIG, WARNING_SUPPLY, WARNING_OPEN";
	sensorWarning[22] = "WARNING_CONFIG, WARNING_SUPPLY, WARNING_STALL";
	sensorWarning[23] = "WARNING_CONFIG, WARNING_SUPPLY, WARNING_STALL, WARNING_OPEN";
	sensorWarning[24] = "WARNING_CONFIG, WARNING_SENSOR";
	sensorWarning[25] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_OPEN";
	sensorWarning[26] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_STALL";
	sensorWarning[27] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_STALL, WARNING_OPEN";
	sensorWarning[28] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_SUPPLY";
	sensorWarning[29] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_SUPPLY, WARNING_OPEN";
	sensorWarning[30] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_SUPPLY, WARNING_STALL";
	sensorWarning[31] = "WARNING_CONFIG, WARNING_SENSOR, WARNING_SUPPLY, WARNING_STALL, WARNING_OPEN";
	NetWorkStatus.resize(2, "");
	NetWorkStatus[0] = "NETWORK DISABLE";
	NetWorkStatus[1] = "NETWORK ENABLE";
	GPSStatus.resize(2, "");
	GPSStatus[0] = "DISABLE";
	GPSStatus[1] = "ENABLE";
	BATTERYStatus.resize(2, "");
	BATTERYStatus[0] = "DISABLE";
	BATTERYStatus[1] = "ENABLE";
2. 函数
	1. onInit(void)
		DES:
			初始化
	2. Refresh(）
		DES:
			[["refresh_statemanger_params"]]服务，根据[[refresh_params.yaml]]的"is_clear_update_params"判断调用LoadParameters()，更新[[exceptionConfig.json]]
			传递data到exceptionConfigData用作后续判断
		IN:
			&req.params_path
			[[exceptionConfig.json]]
				"exceptionConfig"
		OUT:
			exceptionConfigData
			&res
	3. timerCallback()
		DES:
			定时检测传感器是否正常，若有问题则
				调用pubExceptionStatus()向MQTT上发异常
		IN:
			
		OUT:
			isheartbeatFlag
			isCommSerialError
			GPSErrorFlag
			[["ros2mqtt_under_exception"]]
				pubExceptionStatus()
	4. autoCallback()
		DES:
			更新auto_state
		IN:
			[["auto_state"]]
		OUT:
			auto_state
	5. pathstopCallback()
		DES:
			更新返航标志位is_task_return
		IN:
			[["pathstop"]]
		OUT:
			is_task_return
	6. heartbeatCallback()
		DES: 
			获取"/ros2mqtt/under/heartbeat"（投料船->上位机），若有则将isheartbeatFlag置1
		IN:
			[["ros2mqtt_under_heartbeat"]]
			isheartbeatFlag
		OUT:
			isheartbeatFlag
	7. NetWorkStateCallback()
		DES:
			调用[["mqtt_client_is_connected"]]服务
				更新boat_state
				更新isConnectedis_task_pause标志位
				[["task_exception_handler"]]
		IN:
			ID
		OUT:
			[["task_exception_handler"]] 
				callExceptionProcessSrv(task_client_, PAUSE/RESUME);
			isConnected
			is_task_pause
	8. isNetworkAvailable() --- UnUse
		DES：ping?
	9. hoveringStateCallback() --- UnUse
		DES:
			悬停请求
	10. stateService()
		DES:
			[["get_state"]]服务函数 ，STM请求状态boat_state(网络，gps,电量)
		IN:
			boat_state
			req.ID
		OUT:
			res.Status
				// 用于STM32判断是否收到请求服务的response
	11. statusCallback()
		DES:
			订阅STM32状态，更新boat_state
		IN:
			isCommSerialError
			"/boatstatus"[["boatstatus"]] 
		OUT:
			boat_state
			error_warning_map.erase(boat_msgs::BoatStateCheck::LAST_STATE);
			issensorWarning[boat_msgs::BoatStateCheck::LAST_STATE] = false;
			issensorError[boat_msgs::BoatStateCheck::LAST_STATE] = false;
	12. understatusCallback()
		DES:
			"/ros2mqtt/uder/status"回调
		IN:
			[["ros2mqtt_under_status"]]	
		OUT:
			boat_state
				更新称重和电池电量
	13. gpsStateCallback()
	14. stateCheckCallback()
	15. stateChecksCallback()
	16. isEnableTask()
	17. exceptionProcess()
	18. callExceptionProcessSrv()
		DES:
			服务请求封装 
		IN:
			&exception_client
				nav_client_
				task_clinet_
			&exception_cmd
		OUT:
	19. isStateError()
	20. pubExceptionStatus()
		DES:
			[["ros2mqtt_under_exception"]]
				异常上发封装
		IN:
			&exceptionstatus
		OUT:
			[["ros2mqtt_under_exception"]]
			pub_under_exception_.publish(exception_msg);
	21. loadExceptionConfig()
	22. clearExceptionConfig()
		DES:
			清除exceptionConfigData中的数据
		IN:
			exceptionConfigData
		OUT:
			exceptionConfigData
			backgroundexceptionData
	23. exceptionTest()
	24. LoadParameters()
		DES:
			被[[StateManager.cpp]] 
				Refresh()调用
			把文件里的参数写到[[exceptionConfig.json]]中
		IN:
			&filename-refresh_file
		OUT:
			[[exceptionConfig.json]]
				exceptionConfig
	25. loadMinPowerForTask()
3. Relations:
	Topic:
		SUB:
			[["gnss"]]
			[["boatstatecheck"]]
			[["boatstatechecks"]]
			[["boatstatus"]]  
			[["auto_state"]]
			[["pathstop"]]
			[["ros2mqtt_under_heartbeat"]]
			[["ros2mqtt_under_status"]]
		PUB:
			[["ros2mqtt_under_exception"]]
		CLI:
			[["mqtt_client_is_connected"]]
			[["nav_exception_handler"]]
			[["task_exception_handler"]]
		SRV:
			[["get_state"]] 
			[["refresh_statemanger_params"]]
	FILE:
		[[exceptionConfig.json]]