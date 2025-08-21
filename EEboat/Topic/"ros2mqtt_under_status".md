DES:
		huoqu stm32 xinxi zhizuo yifeng chuantizhaungtai "ros2mqtt/under/status"
	info:
		status_data["SOC"] = Utils::limit_precision(energy_left, 1);
		status_data["vol"] = Utils::limit_precision(vol, 1);
		status_data["cur"] = Utils::limit_precision(power_ptr_->current, 1);
		status_data["motor_l"] = boat_motor_duty[boat_msgs::BoatMotor::PADDLEWHEELLEFT];
		status_data["motor_r"] = boat_motor_duty[boat_msgs::BoatMotor::PADDLEWHEELRIGHT];
		status_data["weight"] = weight_.weight;
		status_data["scatter"].clear();
		status_data["scatter"].append(boat_motor_duty[boat_msgs::BoatMotor::SCATTER]); // 蓝龙号[scatter_state,-1,-1] 滨州船[-1,left,right]
		status_data["scatter"].append(-1);
		status_data["scatter"].append(-1);
		status_data["convey"].clear();
		status_data["convey"].append(boat_motor_duty[boat_msgs::BoatMotor::CONVEY]);
		status_data["convey"].append(-1);
		status_data["convey"].append(-1);
		status_data["stir"] = boat_motor_duty[boat_msgs::BoatMotor::STIR];
		status_data["pump"].clear();
		status_data["pump"].append(boat_motor_duty[boat_msgs::BoatMotor::PUMPMAIN]);
		status_data["pump"].append(boat_motor_duty[boat_msgs::BoatMotor::PUMPSLAVE]);
		status_data["transformer"] = boat_motor_duty[boat_msgs::BoatMotor::TRANSFORMER];
		status_data["mode"] = status_ptr_->state_cmd;
		status_data["type"] = status_ptr_->type_state;
		status_data["auto_state"] = status_ptr_->auto_state;
		status_data["state_cmd"] = status_ptr_->state_cmd;
SUB:
	[[StateManager.cpp]]
		understatusCallback()
PUB:
	[[Info2NetNodelet.cpp]]
		timer1Callback()