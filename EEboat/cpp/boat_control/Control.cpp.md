1. 重要成员：
	1. [[NavigationNodelet.cpp]] 
		1. pub_cmd - "/boatcmd" [["boatcmd"]]
		2. pub_cmds - "/boatcmds" [["boatcmds"]]
2. 函数：
	1. LoadParamsPtr()
		DES:
			在[[NavigationNodelet.cpp]] onInit()中被调用
				加载boat_param_ptr参数[[BoatParam.cpp]]
		IN: 
			&boat_param_ptr
		OUT:
			[[BoatParam.cpp]] 
				->boat_param_ptr
					->path_params_ptr
					->convey_control_params_ptr
						->vel_pid_ptr
						->d_power_step
						->max_power
			[[Control.cpp]]
				->path_follow_pid
					->power_vel
					->last_error
					->last_d_error
					->p = this->control_params_ptr->vel_pid_ptr->config.kp;
					->i = this->control_params_ptr->vel_pid_ptr->config.ki;
					->d = this->control_params_ptr->vel_pid_ptr->config.kd;
	2. simple_headin_control(...)
		DES:
		IN:
			err_dir 与目标方向的差值
			angle_v 角速度，角度制
		OUT:
			直接修改angle_v
	3. simple_vel_control(double err_dir, double max_pwm, float &line_v)
		DES:
			在[[Control.cpp]] vel_control()中被调用
				返回simple_vel简易线速度
		IN:
			err_dir 与目标方向的差值
			max_pow 明轮最大占空比
		OUT:
			&line_v 线速度，实际是明轮占空比
	4. vel_pid_contol(2)
		DES:
			根据目标速度ref_vel更新cur_vel
		IN:
			ref_vel
			cur_vel
			is_filter
			&pid
		OUT:
			ref_vel
			cur_vel
	5. vel_contorl(...)
		DES:
			在其他文件调用时
				[[PathFollow.cpp]] 
			将方向传递到control中.
		IN:
			double err_dir
			double ref_vel 
			double cur_vel
			bool is_filter
		OUT:
			[[PathFollow.cpp]] 
				path_follow_pid->power_vel;
				返回速度
	6. pub_speed(line_v, ang_v)
		DES:
			被其他函数调用
			[[PathFollow.cpp]] update_gps()
			通过pub_cmd->publish(...)向电机发送速度信息
		IN:
			line_v
			ang_v
		OUT:
			pub_cmd->publish(cmd)
	7. pub_scs(double scatter, double convey, double stir)
		DES:
			搅笼甩盘联动控制发布
		IN:
			double scatter 甩盘转速
			double convey 搅笼转速
			double stir
		OUT:
			pub_cmds-publish(cmds)
	8. pubCmd(int cmdType, int cmdData)
		DES:
			控制发布元函数
		IN:
			cmdType, cmdData
		OUT:
			pub_cmd->publish(cmd)
	9. pub_pump_main()
	10. pub_pump_slave()
	11. pub_reset()
	12. pub_trans()
	13. set_direction(float actual, float target)
		DES:
			在其他文件调用时
				[[PathFollow.cpp]] 
			将方向传递到control中.
		IN:
			actual 船舶方向
			target 路径方向
		OUT:
			this->ship_dir
			this->path_dir
	14. heading_pid_control()-UnUse
	15. heading_control()-UnUse
	16. pointConvey_control
		DES:
		IN:
		OUT:
			return convey = 0
	17. pub_pidConfig话题名
		DES:
			发布参考参数，当前速度等参数，用于调试pid参数
		IN:
			&pid
		OUT:
			pub_pid_plotParams->publish(pid_config_msg);
	