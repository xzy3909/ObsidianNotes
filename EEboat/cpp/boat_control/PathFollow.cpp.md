1. 全局变量
	1. PUB:
		[[NavigationNodelet.cpp]]
			this->pub_dir = pub_dir
			this->pub_follow = pub_follow
			this->control_ = control
		[[boat_param_ptr]]
			path_follow_params_ptr = std::make_shared<Boat::BoatParam::path_follow_params>();
			global_params_ptr = std::make_shared<Boat::BoatParam::golbal_params>();
			ilos_ptr = std::make_shared<Boat::BoatParam::ILOS>();
2. 函数
	1. PathFollow::PathFollow(ros::NodeHandle &n, ros::NodeHandle &nh, std::shared_ptr<ros::Publisher> pub_dir, std::shared_ptr<ros::Publisher> pub_follow, const std::shared_ptr<Boat::Control> &control)
		DES:
		IN:
		OUT:
	2. void PathFollow::LoadParamsPtr(const std::shared_ptr<Boat::BoatParam> &boat_param_ptr)
		DES:
			在[[NavigationNodelet.cpp]] onInit()中被调用
				加载boat_param_ptr参数
		IN:
			[[boat_param_ptr]] - &boat_param_ptr
		OUT:
			path.clear();
			ref_vel = this->path_follow_params_ptr->ref_vel;
			followVel = ref_vel;
	3. PathFollow::resetParams()
		DES:
			重置路径
		IN:
			
		OUT:
			重置局部变量cur_gnss, ref_gnss,......
	4. void PathFollow::set_path(const std::vector<GnssMath::gnss_base_type> &path, int init_target_index, int step_target_index, int path_target_index, int loop_num)
		DES:
			被其他文件调用
			[[NavigationNodelet.cpp]] pathfileCallback()
			初始化路径变量
				path (list): 规划好的路径
				init_target_index (int, optional): 初始目标点. Defaults to 0.
				step_target_index (int, optional): 路径点航行顺序，1为正序，-1为倒序. Defaults to 1.
				path_target_index (int, optional): 路径终点. Defaults to -1.
				loop_num (int, optional): 路径循环航行次数. Defaults to 1.
			调用get_angle(path)函数
		IN:
			path, init_target_index, step_target_index, path_target_index, loop_num
		OUT:
			set_path
				this->is_arrive_goal = false;
				this->path = path;
				this->ref_LatLng.clear();
				this->loopNum = loop_num;
				this->cur_target_index = init_target_index;
				this->step_target_index = step_target_index;
				this->path_target_index = (path_target_index + path.size()) % path.size();
			get_angles(path);
			[[boat_param_ptr]] - global_params_ptr
				ref_gnss = global_params_ptr->cur_gnss;什么情况下更新?????
	5. void PathFollow::update_gps(bool cmdSend)[[NavigationNodelet.cpp]]
		DES:
			路径规划主要函数，收到"/gnss"数据时运行,调用
				[[PathFollow.cpp]] 
					move_close() 
						首先考虑is_arrive_goal = true的情况
					GlobalDirection()
					decelerationSpeed(),
				[[avoidNode]]
				[[Control.cpp]] vel_contorl()
				获取 line_v, ang_v
			调用[[Control.cpp]] pub_speed(line_v, ang_v)
				控制明轮电机
		IN:
			cmdSend
				true/false,其他函数调用时传递
			path
			double direction = getDirection();
				船舶坐标系全局方向，描述的是与目标方向的差值
				调用getDirection()函数
			bool isAvoid = getIsAvoid();
				调用getIsAvoid()函数，更新isAvoid标志位
			[[avoidNode]]  
				followDir = PF_srv.response.followDir;
				followVel = PF_srv.response.followVel;
				isDecelerate = PF_srv.response.isDecelerate;
				通过避障服务获取???上面3个变量是什么
			[[Control.cpp]]
				line_v = control_->vel_control(direction, tempVel, cur_speed_u, false);
					通过control_->vel_control获取线速度
				ang_v = 0;??????
		OUT:
			pub_dir->publish(direction_msg);//发布全局方向便于后期调试
			[[avoidNode]] 
				bool pic_flag = pic_follow_client.call(PF_srv);
				if (pic_flag){...}
				调用"/pic_follow_dir"服务???
			[[Control.cpp]]
				control_->pub_speed(line_v, ang_v);
					
				control_->simple_heading_control(direction, ang_v);
	6. void PathFollow::decelerationSpeed(double targetDist, double cur_vel, float &planVel)
		DES:
			在update_gps中被调用，用于调整速度
		IN:
			targetDist 目标距离
			ref_vel 参考速度
			cur_vel 当前速度
			dec_vel 减速速度
			R_decelerate 减速半径
			cur_R_thresh 小圈半径
		OUT:
			返回 planDecVel
	7. void PathFollow::move_close()
		DES:
			被[[PathFollow.cpp]] update_gps()调用
				用于处理接近到达 目标点/终点??? 的情况
			到达目标点后将 is_arrive_goal 标志位置true
			if (dist_to_cur_target < path_follow_params_ptr->cur_R_thresh || degree >= 90) // cur_R_thresh[cur_target_index]????
		IN:
			is_arrive_goal
			path
		OUT:
			is_arrive_goal = true;
			waypointCallback(is_arrive_goal);
	8. double PathFollow::Global_direction(double ref_vel, double cur_vel)
		DES:
			iLOS算法实现????
		IN:
			ref_vel, cur_vel
		OUT:
			[[Control.cpp]] 
				control_->setdirection(ship_dir, path_dir);
					将计算出的船体方向和路径方向，传递到control中
			iLOS导航处理
			return global_dir_body
				返回全局方向
	9. double PathFollow::get_deviation(void)----UnUse
		NaN
	10. std::vector<doublePathFollow::get_angles(const std::vector<GnssMath::gnss_base_type&P_GPS)
		DES:
		IN:
			path
		OUT:
			angles???在pathfollow里装作用
	11. void PathFollow::waypointCallback(const bool arrived_flag)
		DES:
			被[[PathFollow.cpp]] move_close()调用
				更新参数 follow_info、global_params_ptr
			通过[[NavigationNodelet.cpp]] pub_follow->publish
				发布目标点状态"/follow_info"[["follow_info"]]
		IN:
			is_arrive_goal
			
			cur_target_index;
			last_target_index;
			step_target_index;
			path_target_index;
			arrived_flag;
			loopNum;
			dist_to_cur_target;
		OUT:
			follow_info
			global_params_ptr
			[[ConveyControl.cpp]] 
				convey_control_->update_convey_k();
					记录convey_k, move_close()只有终点判断吗???
			[[NavigationNodelet.cpp]]
			[["follow_info"]]
				pub_follow->publish(follow_info);
					"/follow_info"
	12. void PathFollow::set_path_attribute(const std::weak_ptr path_attribute)
			path_attribute_ = path_attribute;
	13. void PathFollow::set_convey_control(const std::shared_ptr<ConveyControl convey_control)
		convey_control_ = convey_control;
	14. void PathFollow::set_gps(const boat_msgs::GNSSFloat64::ConstPtr &gnss)
		cur_gps = * gnss;
	15. void PathFollow::set_R_threshold(const Json::Value &attributes)
		DES:
			在[[AttributeSet.cpp]] dealNextPointAttribute()中被调用
				打印，执行设置目标点半径阈值属性
		IN:
		OUT:
			cur_R_thresh;
				小圈半径
	16. void PathFollow::setParameters(const Json::Value &attributes)
		DES:
			在[[AttributeSet.cpp]] dealCurPointAttribute被调用
				读取传递参数
		IN:
			[[AttributeSet.cpp]]
				cur_point_attribute_
		OUT:
			BoatCmd
				cmd_knob??????为例，cmd传递到哪里
			ref_vel
			max_power
			loopNum
			cycle_mode
			motor_type
	17. void PathFollow::InitParams()
		DES:
			设置初始参数
		IN:
			NaN
		OUT:
			cur_target_index = 0;
			last_target_index = -1;
			step_target_index = 1;
			path_target_index = -1;
			loopNum = 1;
			is_arrive_goal = true;
			dist_to_cur_target = INFINITY;
	18. bool PathFollow::getIsAvoid()
		DES:
			根据isCustom判断是否启动避障
		IN:
			isCustom
				是否自定义避障标志位
		OUT:
			return bool返回逻辑以及 isAvoid[]????
				return path_follow_params_ptr->is_open_avoid && isAvoid[cur_target_index];
				return path_follow_params_ptr->is_open_avoid;
	19. double PathFollow::getDirection()
		DES:
			避障速度followVel<0时,将isGlobalDir置false，当前速度cur_speed_u>0.3时置true，调用Global_direction函数获取全局方向
		IN:
			bool isGlobalDir
				是否计算全局方向标志位
		OUT:
			调用并返回Global_direction()
	20. void PathFollow::setIsAvoid(const Json::Value &data)
		DES:
			在其他函数中被调用
			[[NavigationNodelet.cpp]] pathfileCallback()
			更新isCustom标志位
		IN:
			Json &data
		OUT:
			isCustom
				更新自定义路径段避障标志位