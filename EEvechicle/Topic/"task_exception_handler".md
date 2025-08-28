DES:
	执行效果?????
	req.cmd
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
CLI:
	[[StateManager.cpp]]
		task_client_
		NetWorkStateCallback(resume)
		isEnable()
SRV:
	[[TaskManagerNodelet.cpp]]
		ExceptionPorcess
			exception_srv_()