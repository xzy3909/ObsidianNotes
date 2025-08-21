推杆控制不在这????
Function:
	1. timerCallback() --- UnUse
	2. gpsCallback()
		IN:
			[["gnss"]]
		OUT:
			gnss_data
	3. imuCallback()
		IN:
			[["livox_imu111"]]
		OUT:
			imu_data
	4. imu_Callback()
		IN:
			[["vehicle_imu"]]
		OUT:
			imu_boat_data
			angular_speed_control(imu->gyro.z);
				yaw角处理????
	5. motorsCallback()
		IN:
			[["vehicle_motors"]]
		OUT:
			[[Control.cpp]]
				control->set_motors(msg)
					->LPR_motor
					->leftTPR_motor
					->rightTPR_motor
	6. cmdVelCallback()
		IN:
			[["cmd_vel"]]
		OUT:
			[[Control.cpp]]
				control->vel_pid_control(ref_vel, cur_speed_u, true);
				control->ang_pid_control(ref_ang_v, cur_ang_v, true);
				control->pub_speed();[["vehicle_cmd"]] 
	7. Refresh()
	8. ExceptionPorcess()
		OUT:
			[[Control.cpp]]
				pub_push_ros(0,0,0)
					异常停止
				pub_reset()
				pub_stop()
			is_LPR_error
			is_TPRLEFT_error
			is_TPRRIGHT_error
Relation:
	Topic:
		SUB:
			[["cmd_vel"]]
			[["gnss"]]
			[["livox_imu111"]]
			[["vehicle_motors"]]
			[["vehicle_imu"]]
		PUB:
			[["vehicle_cmd"]]
			[["vehicle_cmds"]]
			[["ros2mqtt_pid_plotparams"]]
		CLI:
		SRV:
			[["refresh_control_params"]]
	FILE:
		