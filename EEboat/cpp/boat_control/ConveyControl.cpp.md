1. 值:
	1. is_received_weight
2. 函数:
	1.  LoadParamsPtr()
		DES:
			在[[NavigationNodelet.cpp]] onInit()中被调用
				加载boat_param_ptr参数[[BoatParam.cpp]]
		IN:
			&boat_param_ptr
		OUT:
			......
	2. ResetParams()
		DES:
			变量初始化
	3. timer_Callback(const ros::TimerEvent &event)
		DES:
			1s定时函数，在需要下料时 conveyPlanner_->isForage[curIndex]=true 更新：
				当前重量cur_weight
				globalFeedParams->remainDist
					convey_k
		IN:
			handle_sensor_msg()
				调用函数实时更新重量cur_weight
			handle_gnss_msg()
				调用函数实时更新globalFeedParams->remainDist
			k = fitLine()
				计算下料速率，用于输出分析
			[[ConveyPlanner.cpp]]
				->isForage[]
					下料标志队列
		OUT:
			cur_weight
			last_gnss
			updateconveyK(last_ref_T1_weight, last_T1_weight)
				更新convey_k
	4. updateconveyK(const double &last_ref_convey_weight, const double &last_convey_weight)
		DES:
			通过目标下料量和实际下料量计算调整convey_k系数
		IN:
			last_ref_convey_weight
			last_convey_weight
		OUT:
			convey_k
			convey_k_record
	5. handle_sensor_msg()
		DES:
			读取global_params_ptr->cur_weight
				以g传递给 cur_weight
		IN:
			global_params_ptr->cur_weight[[BoatParam.cpp]]
		OUT:
			cur_weight
	6. handle_gnss_msg()
		DES:
			读取cur_gnss，读取[[ConveyPlanner.cpp]] conveyList、[[PathFollow.cpp]] path，用segmentProjection计算得到
				globalFeedParams->remainDist
		IN:
			global_params_ptr->cur_gnss
			double dl = segmentProjection线段投影
		OUT:
			globalFeedParams->remainDist = conveyPlanner_->conveyList[curIndex].remainDist;[[ConveyPlanner.cpp]]
	7. get_vel_convey(double total_distance, double weight, double v_spread_max)
		DES:
			计算参考速度，撒料过快则降低船速
		IN:
		OUT:
	8. convey_control(bool cmdSend, int cur_target_index)???整体逻辑以及convey_k
		DES:
			在[[PathFollow.cpp]] update_gps(cmdSend)中被调用
				传入cur_target_index路径号，根据路径号索引路径撒料状态[[ConveyPlanner.cpp]] ->isForage
				根据各种情况对甩盘搅笼等进行控制
		IN:
			cmdSend -- bool isGnssVaild
			curIndex = cur_target_index 路径号
		OUT:
	9. void ConveyControl::convey_planning_error_corr(void)
	10. fitLine()
		DES:
			被[[ConveyControl.cpp]] timer_Callback()调用
				传入weight_record
				返回k
		IN:
			&points -- weight_record
		OUT:
			k
	11. update_convey_k(void)
		DES:
			在[[PathFollow.cpp]] waypointCallback被调用，
				将convey_k记录到YAML中???
		IN:
			convey_k_record
		OUT:
			refresh_file
				将convey_k_record写入文件
	12. void ConveyControl::convey_k_saved -- HowToUse???
	13. void ConveyControl::convey_offset_update(void)
		DES:
			拟合电机死区
		IN:
			convey_control_params_ptr->convey_mode
		OUT:
			convey_offset
	14. void ConveyControl::resumeTask(void)
		DES:
			恢复任务函数
		IN:
			
		OUT:
	15. void ConveyControl::stirControl(void)
		DES:
			被[[ConveyControl.cpp]] isReachPoint()调用
				根据重量调节stir频率
		IN:
			global_params_ptr->cur_weight
			convey_control_params_ptr->start_stir_max_weight
		OUT:
			control_->pubCmd();
	16. void ConveyControl::setStopConveyControl(const bool &is_motor_error)
		DES:
			搅笼停转指令
	17. void ConveyControl::setStopStirControl(const bool &is_stop_stir)
		DES:
			搅拌棍停转指令
	18. void ConveyControl::setStopUpdateParam(const bool &is_stop_update)
		DES:
			参数停止更新开关
	19. bool ConveyControl::isWeighChange(void)
		DES:
			重量变化检测参数
	20. void ConveyControl::controlExceptionProtect(void)
		DES:
			搅笼保护
	21. bool ConveyControl::isReachPoint(void) ??? move_close冲突
		DES:
			判断是否到达目标点，若到达点，重新计算相关参数
		IN:
		OUT: