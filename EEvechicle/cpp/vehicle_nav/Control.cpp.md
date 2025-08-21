DES:
	基于pub_cmd和pub_cmds构建复杂控制指令
		"/vehicle_cmd"
		"/vehicle_cmds"
		"/ros2mqtt/pid/plotparams"
	在ControlNodelet里调用初始化
 函数构造
	1. 参数加载:
		1. LoadParamsPtr()
		2. set_motors()???
		3.  set_direction()
		4. pointConvey_control()
			return self
	2. 数据处理:
		1.  simple_vel_control() --- UnUse
		2.  heading_control()
			simple_heading_control()
				IN:
					double ref_dir
				OUT:
					double v_z
		3. vel_control()
			vel_pid_control()
				IN:
					double ref_vel
					double cur_velcur
					bool is_filter
				OUT:
					vel_pid_ptr
					pid
						->power_vel
						->last_error
						->last_de_error
			OUT:
				path_follow_pid->power_vel
		4. ang_pid_control()
			IN:
				double ref_ang_vel
				double cur_ang_vel
				bool is_filter
			OUT:
				ang_vel_pid_ptr
		5. heading_pid_control()
			IN:
				double path_dir
				double ship_dir
			OUT:
				pid
					->omega
					->last_error
					->last_d_error
	3. 消息发布:
		1. pub_cmd形式
			1. pub_speed(2) 
				IN:
					line_v
					ang_v
					vel_pid_ptr
					ang_vel_pid_ptr
				OUT:
					pub_cmd->publish(cmd)
			2. pub_stop()
			3. pub_reset()
		3. pub_cmds形式
			1. pub_push_rod()
		4. pub_pid_plotParams
			1. pub_pidConfig()
Relation:
	Topic:
		PUB:
			[["vehicle_cmd"]]
			[["vehicle_cmds"]]
			[["ros2mqtt_pid_plotparams"]]
		SUB:
	FILE:
		"LogVehicleCmd"
		"LogVehicleCmds"
		"LogControl"