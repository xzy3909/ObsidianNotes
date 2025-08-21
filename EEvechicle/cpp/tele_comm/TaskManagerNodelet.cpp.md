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
		DES:
		IN:
			receivedData
		OUT:
	6. callback_FollowInfo()
	7. callback_gps()
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
	10. tangkouInfoCallback()
	11. callNav()
	12. latlon2local()
	13. local2latlon()
	14. AppStart()
	15. callback_ServerTaskExecute()
	16. callback_hover()
	17. start_hovering()
	18. stop_hovering()
	19. pub_under_task_msg()
	20. path_stop()
	21. AppStart()
	22. taskStart()
		DES:
			zand
		IN:
			taskPath
		OUT:
	23. taskPause()
	24. taskRecover()
	25. taskFinish()
	26. taskEnds()
	27. taskCmd()
			IN:
			&cmd
		OUT:
			pub_cmd.publish(state_cmd)
	28. pubTaskEndsMsg()
	29. breakPointResumeTask()
	30. isReachtargetPoint()
	31. taskChoose()
	32. ExceptionPorcess()

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