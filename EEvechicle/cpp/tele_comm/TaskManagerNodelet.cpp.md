函数:
	1. AppStart()
		IN:
		OUT:
		1. taskChoose()
			IN:
				resume_path
				task_path
			OUT:
				resumeData
				taskdata
				taskStart(task_path)
					IN:
						task_path
					OUT:
						pub_under_task_msg()
							IN:
								vehicleId_msg
								state
								latitudelongitude
								cur_node_index
								left_loop_num
								begin_time
								tangkou_info
									.tangkou_id
									.path_type_id
									.tangkou_rate
									.rate
							OUT:
								[["ros2mqtt_under_task"]]
									pub_under_task.publish(under_task_msg);
	2. taskCmd( &cmd )
		IN:
			&cmd
		OUT:
			pub_cmd.publish(state_cmd)