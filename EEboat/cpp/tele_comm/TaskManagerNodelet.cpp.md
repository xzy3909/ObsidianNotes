1. 重要成员
2. 函数
	1. onInit()
		pub
			"/boatcmd" [["boatcmd"]] 
				pub_cmd
			"/boatcmds" [["boatcmds"]]
				pub_cmds
			"/auto_state" [["auto_state"]]
				pub_autoState
			"/pathfile" [["pathfile"]]
				pub_pathFile
			"/pathstop" [["pathstop"]]
				pub_pathStop
			"/ros2mqtt/under/task" [["ros2mqtt_under_task"]]
				pub_under_task
			"/ros2mqtt/under/resume" [["ros2mqtt_under_resume"]]
				pub_under_resume
		sub
			"/gnss" [["gnss"]]
				callback_gps
			"/follow_info" [["follow_info"]]
				callback_FollowInfo
			"/mqtt2ros/server/task_execute" [["mqtt2ros_server_task_execute"]]
				callback_ServerTaskExecute
			"/boatstatus"
				callback_status
	2. LoadParameters()
	3. ResetParams()
	4. timerCallback()
		DES:
			每秒分布任务信息
		IN:
		OUT:
			pub_under_task_msg()
	5. callbck_FollowInfo()
		DES:
			[["follow_info"]] 
		IN:
			isReachtargetPoint()
		OUT:
			addingtaskPause()
			resumeTask.remainDistneedBattery()
	6. callback_gps()
	7. callback_status()
		DES:
			[["boatstatus"]] 回调函数
		IN:
		OUT:
	8. callback_ServerTaskExecute()
		DES:
			[["mqtt2ros_server_task_execute"]] 回调函数，app启动信息接收
		IN:
			msg
		OUT:
			taskCmd(APP_START);
			AppStart(taskData);
	9. callback_hover()
	10. start_hovering()
	11. stop_hovering
	12. pub_under_task_msg()
		DES:
			[["ros2mqtt_under_task"]] pub
		IN:
		OUT:
			pub_under_task.publish(under_task_msg);
	13. pub_trans() ?????
	14. path_stop()
	15. AppStart()
		DES:
			在callback_ServerTaskExecute中调用传入话题[["mqtt2ros_server_task_execute"]] 
			，读取app传入服务器端发过来路径及属性
		IN:
			AppStart(taskData);
			taskChoose();
			taskStart(return_path);
		OUT:
			data_config ---> task_path
	16. taskStart()
		DES:
			执行一键启动命令的函数，修改相关状态，实现与APP交互控制
		IN:
		OUT:
			pub_pathFile.publish(file_msg); [["pathfile"]] 
	17. taskPause()
	18. taskRecover()
	19. taskFinish()
	20. taskEnds()
	21. taskCmd()
		DES:
			boat_msgs::BoatCmd::STATE_CMD发布封装 [["boatcmd"]]
		IN:
			&cmd
		OUT:
			[["boatcmd"]] 
	22. transformerTask()
	23. getUpandDown()
	24. pubTaskEndsMsg()
	25. breakPointResumeTask()
	26. addingtaskPause()
	27. isReachtargetPoint()
		DES:
			到达目标点判断
		IN:
		OUT:
	28. ScheduledTask()
	29. taskChoose()
		DES:
			选择要执行的任务
		IN:
			adding_task
			task_path
		OUT:
			taskStart(task_path);
	30. ExceptionPorcess()
	31. updateTimeService()
this->sub_hover = nh.subscribe("/mqtt2ros/server/hover", 10, &TaskManagerNodelet::callback_hover, this);
	