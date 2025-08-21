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


this->sub_hover = nh.subscribe("/mqtt2ros/server/hover", 10, &TaskManagerNodelet::callback_hover, this);
	1. LoadParameters(3)
	2. ResetParams()
	3. Refresh(boat_msgs::RefreshParams::Request &req, boat_msgs::RefreshParams::Response &res)
	4. timerCallback(const ros::TimerEvent &event)
	5. callback_FollowInfo(const boat_msgs::PathFollowInfo::ConstPtr &msg)
	6. callback_gps(const boat_msgs::GNSSFloat64::ConstPtr &msg)
	7. callback_status(const boat_msgs::BoatStatus::ConstPtr &status)
	8. callback_ServerTaskExecute(const std_msgs::String::ConstPtr &msg)
	9. callback_hover(const std_msgs::String::ConstPtr &msg)
	10. start_hovering(int cmds)
	11. stop_hovering()
	12. pub_under_task_msg()
	13. pub_trans(int data, bool isUse)
	14. path_stop(std::string data)
	15. AppStart(const Json::Value &data)
	16. taskStart(const std::string &taskPath)
	17. taskPause()
	18. taskRecover()
	19. taskFinish()
	20. taskEnds()
	21. taskCmd(const int &cmd)
	22. transformerTask()
	23. getUpandDown(const std::string &json_file)
	24. pubTaskEndsMsg()
	25. breakPointResumeTask(const Json::Value &data)
	26. addingtaskPause(void)
	27. isReachtargetPoint(const int &index)
	28. ScheduledTask(void)
	29. taskChoose(void)
	30. ExceptionPorcess(boat_msgs::ExceptionSrv::Request &req, boat_msgs::ExceptionSrv::Response &res)
	31. updateTimeService(boat_msgs::updateTime::Request &req, boat_msgs::updateTime::Response &res)
	32. 