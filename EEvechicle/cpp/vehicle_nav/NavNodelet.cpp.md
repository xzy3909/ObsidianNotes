DES:
	控制运动
	异常重置推杆
Function:
1. NavInterfaces()
	DES:
		[["NavInterface"]]服务函数
	IN:
		req.task_cmd
			START
			PAUSE
			RECOVER
			END
			FINISH
	OUT:
		nav3DStart()
		nav3DPause()
		nav3DRecover()
		nav3DPause()
		Finish()
2. nav3DPause()
	DES:
	IN:
	OUT:
		auto_state = 0
		is_paused = true;
		[["reset_visibility_graph"]] 
3. nav3DStart()
	DES:
	IN:
		task_path[[Task_path.json]] 
	OUT:
		tangkou_task_list.push_back(task_item);
		[["global_path"]] 
			global_goal_pub_->publish(global_path);
Relation:
	Topic:
		SUB:
		PUB:
			[["reset_visibility_graph"]] 
	SRV:
		[["NavInterface"]]
		[["task_return_exception_handler"]] 
