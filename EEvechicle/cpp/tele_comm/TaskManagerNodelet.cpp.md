arrived究竟由谁更新????
悬停类型????
任务管理节点功能列表：

* 基础功能：app or 遥控器 开始任务，任务暂停，任务继续，任务终止，任务完成。开启悬停，关闭悬停。 电缸上升，下降。app一键返航，app一键启动

* 其他功能：断点继续撒料，多次撒料，定时定量，异常返航功能(这个功能新添加的，看最新代码)。
函数:
	1. onInit()
		DES:
	2. LoadParameters(3)
		DES:
			加载参数
		IN&OUT:
			getParam(...)
				"machine_name"
				"task_path" -> [[Task_path.json]]
				"tele_task_path" -> [[Tele_Task_path.json]]
				"return_path" -> [[Return_path.json]]
				"update_file" -> [[update_params.yaml]]
				"params_config" -> [[params_config.yaml]]
				"refresh_file" -> [[refresh_params.yaml]]
				"resume_path" -> [[Resume_path.json]]
				"adding_task" -> [[AddingTasks.json]]
				"exception_config" -> [[exceptionConfig.json]]
				"feed_planner" -> [[feedPlanner.json]] 
			LoadParameters(...)
				[[params_config.yaml]]
				[[refresh_params.yaml]]
				[[update_params.yaml]]
					hover_r
					hover_R
					trans_flag
					convey_mode
					is_open_hover
					is_task_finish_open_hover
			resumeTask
				.task_path [[Task_path.json]]
				.resume_path [[Resume_path.json]] 
			taskLog
				exceptionConfigLog.readPath = [[exceptionConfig.json]] 
				feedPlannerLog.readPath = [[feedPlanner.json]]
				resumeTaskLog.readPath = [[Resume_path.json]]
	3. ResetParams()
		DES:
	4. Refresh()
		DES:
			[["refresh_taskmanger_params"]] 服务函数，选择加载参数
		IN:
			req.params_path
		OUT:
			is_clear_update_params
				LoadParameters(refresh_file/req.params_path)
	5. timerCallback()
		DES:持续发布任务路径信息
			1s定时函数，检查是否到达路径arrived标志位，调用pub_under_task_msg()发布任务信息
		IN:
			receivedData
		OUT:
			调用调用pub_under_task_msg()发布任务路径信息
	6. callback_FollowInfo()
		DES:
			[["follow_info"]]回调函数，更新arrived标志位
		IN:
			&msg
		OUT:
			arrived
			cur_node_index
			next_node_index
			left_loop_num
	7. callback_gps()
		DES:
			[["gnss"]]回调函数
		IN:
			&msg
		OUT:
			cur_gnss
			latitude
			longitude
			resumeTask.cur_pose
				.lat
				.lon
	8. callback_status()
		DES:
			下位机状态检测
		IN:
			[["vehicle_status"]]
				status
		OUT:
			receivedData = true
			auto_state = 0/1
			[["ros2mqtt_under_task"]]
				pub_under_task_msg()
				taskStart()
	9. navCallback()
		DES:
			[["nav_info"]]回调函数，更新arrived，auto_state
		IN:
			&msg
		OUT:
			nav_info
			cur_node_index
			next_node_index
			arrived
			auto_state
	10. tangkouInfoCallback()
		DES:
			[["tangkou_task_info"]]回调函数
		IN:
			&msg
				rate--(完成率)
			pair.first
		OUT:
			arrived
			pathIndex_msg = pair.second;
	11. callNav()
		DES:
			[["NavInterface"]] 服务请求函数
		IN:
			&cmd
		OUT:
			client_nav_.call(nav_srv)
	12. latlon2local()
		DES:
			将[[Task_path.json]]中的路径转换到笛卡尔坐标系 
		IN:
			task_path[[Task_path.json]] 
		OUT:
			task_path[[Task_path.json]] 
	13. local2latlon()
		DES:
			将[[Task_path.json]]中的路径转换到经纬度 
		IN:
			task_path[[Task_path.json]] 
		OUT:
			task_path[[Task_path.json]] 
	14. callback_ServerTaskExecute()
		DES:
			[["mqtt2ros_server_task_execute"]]回调函数，app下发任务实现
		IN:
			&msg
		OUT:
			AppStart()
			[["NavInterface"]]
				callNav(vehicle_msgs::NavInterface::Request::START);
					[["NavInterface"]] 
				taskPause();
				taskRecover();
				taskEnds();
	15. callback_hover()
		DES:
			[["mqtt2ros_server_hover"]] 回调函数，获取hover_lat，hover_lat
				调用taskCmd()进行悬停PC悬停???
		IN:
			&msg
		OUT:
			hover_lat
			hover_lon
			taskCmd()
	16. start_hovering()
		DES:
			[["NavigationNodelet_hovering"]]服务调用
		IN:
			cmds
			task_path[[Task_path.json]] 
		OUT:
			调用[["NavigationNodelet_hovering"]]服务
	17. stop_hovering()
		DES:
			[["NavigationNodelet_hovering"]]服务调用
		IN:
			cmds
			task_path[[Task_path.json]] 
		OUT:
			调用[["NavigationNodelet_hovering"]]服务
	18. pub_under_task_msg()
		DES:
			获取路径信息，赋值under_task并发布[["ros2mqtt_under_task"]] 
		IN:
		OUT:
			under_task
			[["ros2mqtt_under_task"]] --->MQTT
			pub_under_task.publish(under_task_msg);
	19. path_stop()----toWhere????
		DES:
			-->StateManager.cpp::pathstopCallback
				is_task_return = false
		IN:
			data
		OUT:
			[["pathstop"]] 
	20. AppStart()
		DES:
			在callback_ServerTaskExecute中被调用，接收[["mqtt2ros_server_task_execute"]] 的 data
		IN:
			&data-->taskData
		OUT:
			pathIdList
				--->pub_under_task_msg()
			tangkouId_pathIndex_map
				--->tangkouInfoCallback() pathIndex_msg
					--->pub_under_task_msg()
			taskChoose()
	21. taskStart()
		DES:
			在callback_status中被调用，用于遥控器一键执行任务
			zand
		IN:
			taskPath
		OUT:
			pub_under_task_msg
				[["ros2mqtt_under_task"]] 
	22. taskPause()
		DES:
			向MQTT发送暂停任务[["ros2mqtt_under_task"]] 
		IN:
		OUT:
			[["ros2mqtt_under_task"]] 
	23. taskRecover()
		DES:
			向MQTT发送恢复任务[["ros2mqtt_under_task"]] 
		IN:
		OUT:
			[["ros2mqtt_under_task"]] 
	24. taskFinish()
		DES:
			向MQTT发送完成任务[["ros2mqtt_under_task"]] 
		IN:
		OUT:
			[["ros2mqtt_under_task"]] 
	25. taskEnds()
		DES:
			向MQTT发送结束任务[["ros2mqtt_under_task"]] 
		IN:
		OUT:
			[["ros2mqtt_under_task"]] 
	26. taskCmd()
		DES:
			[["vehicle_cmd"]]发布封装，目前
		IN:
			&cmd
		OUT:
			[["vehicle_cmd"]] 
				pub_cmd.publish(state_cmd)
	27. pubTaskEndsMsg()
		DES:
			[["ros2mqtt_under_resume"]]回调函数
		IN:
			[[Resume_path.json]] 
		OUT:
			[["ros2mqtt_under_resume"]] 
	28. breakPointResumeTask()---UnUsed
		DES:
		IN:
			&data
			resume_path[[Resume_path.json]] 
		OUT:
			taskStart(resume_path/task_path);
	29. isReachtargetPoint()---UnUsed
		DES:
			判断是否达到目标点，更新last_index
		IN:
			&index
		OUT:
			last_index
	30. taskChoose()
		DES:
			在AppStart()中被调用，读取路径文件，调用taskStart()
		IN:
			resume_path[[Resume_path.json]]
			task_path[[Task_path.json]] 
		OUT:
			taskStart(task_path)
	31. ExceptionPorcess()
		DES:
			[["task_exception_handler"]]服务函数
		IN:
			req.cmd
		OUT:
			[["NavInterface"]]
			[["vehicle_cmd"]] 
			ENABLE_TASK
				is_start_task = true;
			DISABLE_TASK
				is_start_task = false;
			TASK_PAUSE
				taskCmd(APP_PAUSE);
				callNav(vehicle_msgs::NavInterface::Request::PAUSE);
				taskPause();
			TASK_RESUME
				taskCmd(APP_RECOVER);
				callNav(vehicle_msgs::NavInterface::Request::RECOVER);
				taskRecover();

Relation:
	Topic:
		SUB:
			[["gnss"]]
			[["follow_info"]]
			[["mqtt2ros_server_task_execute"]]
			[["vehicle_status"]]
			[["mqtt2ros_server_hover"]]
			[["nav_info"]]
			[["tangkou_task_info"]]
		PUB:
			[["vehicle_cmd"]]
			[["vehicle_cmds"]]
			[["auto_state"]]
			[["pathfile"]]
			[["ros2mqtt_under_task"]]
			[["ros2mqtt_under_resume"]]
		CLI:
			[["NavigationNodelet_hovering"]]
			[["NavInterface"]]
		SRV:
			[["task_exception_handler"]]
			[["refresh_taskmanger_params"]]
	FILE:
		[[Task_path.json]]
		"machine_name", this->vehicleId_msg
		"task_path", this->task_path
		"tele_task_path", this->tele_task_path
		"return_path", this->return_path
		"update_file", this->update_file
		"params_config", this->params_config
		"refresh_file", this->refresh_file
		"resume_path", this->resume_path
		"adding_task", this->adding_task
		"exception_config", this->exception_config
		"feed_planner", this->feed_planner