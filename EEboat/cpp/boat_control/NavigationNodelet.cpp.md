1. 函数:
	1. void NavigationNodelet::onInit();
		初始化发布者，订阅者，服务
		创建
			boat_param_ptr[[BoatParam.cpp]]
				加载参数
			control
			path_follow[[PathFollow.cpp]] 
			convey_control[[ConveyControl.cpp]] 
			hovering
			path_attribute[[AttributeSet.cpp]]的指针传递给AttributeSet类,用于修改path_follow参数
		OUT:
			为各个成员加载 boat_param_ptr [[boat_param_ptr]] 
			[[Control.cpp]] 
				control->LoadParamsPtr(boat_param_ptr);
				control->pub_reset();
			[[ConveyControl.cpp]] 
				convey_control->LoadParamsPtr(boat_param_ptr);
			[[PathFollow.cpp]] 
				path_follow->LoadParamsPtr(boat_param_ptr);
	2. void NavigationNodelet::pathfileCallback(const std_msgs::String::ConstPtr &msg)
		DES:
			读取路径，设置路径,pathfile_sub回调
		IN:
			"/pathfile"[["pathfile"]]-------path.json
		OUT:
			[[AttributeSet.cpp]]
				path_attribute->setPathAttribute();
			[[PathFollow.cpp]]
				path_follow->set_path();
					初始化路径变量，为后续导航做准备
				path_follow->setIsAvoid();
					设置是否避障
	3. void NavigationNodelet::gpsCallback(const boat_msgs::GNSSFloat64::ConstPtr &msg)
		DES:
			记录gps初始点到bglobal_params_ptr
			发布"/gps_odom"用于EKF融合
		IN:
			[["gnss"]] - "/gnss"
		OUT:
			[[boat_param_ptr]]
				boat_param_ptr->global_params_ptr->init_gps
			[["gps_odom"]] - "/gps_odom" 
	4. void NavigationNodelet::ekfCallback(const nav_msgs::Odometry::ConstPtr &msg)
		DES:
			读取EKF滤波数据"odometry/filtered", 转换并传递姿态信息，调用path_follow;
		IN:
			[["odometry_filtered"]] - "odometry/filtered"
		OUT:
			path_follow->update_gps(true);
			[[boat_param_ptr]]
				boat_param_ptr->global_params_ptr->cur_gps = ekf_gps;
				boat_param_ptr->global_params_ptr->cur_gnss.lat = ekf_gps.lat;
				boat_param_ptr->global_params_ptr->cur_gnss.lon = ekf_gps.lon;
			[[PathFollow.cpp]]
				path_follow->update_gps(true);
	5. void NavigationNodelet::autoCallback(const std_msgs::Int8::ConstPtr &msg)
		DES:
			更新节点变量auto_state，更新自动模式
		IN:
			[["auto_state"]] - "/auto_state"
		OUT:
			局部变量auto_state
	6. void NavigationNodelet::pathstopCallback(const std_msgs::String::ConstPtr &msg)
		DES:
			路径重置回调
		IN:
			[["pathstop"]] - "/pathstop"
		OUT:
			[[PathFollow.cpp]]
				path_follow->resetParams()
			[[Control.cpp]]
				control->LoadParamsPtr(boat_param_ptr)
				control->pub_scs()
				control->pub_stop()
			[[AttributeSet.cpp]]
				path_attribute
	7. void NavigationNodelet::sensorCallback(const boat_msgs::BoatWeight::ConstPtr &msg)
		DES:
			重量检测函数，传递重量，ddd
		IN:
			[["boatweight"]] - "/boatweight"
		OUT:
			[[boat_param_ptr]] 
				boat_param_ptr->global_params_ptr->cur_weight = msg->weight;
			[[ConveyControl.cpp]]
				convey_control->is_received_weight = true;
	8. void NavigationNodelet::imuCallback(const boat_msgs::BoatImu::ConstPtr &msg)
		DES:
			订阅"/boatimu_l"，传递到局部变量cur_imu---UnUse
		IN:
			[["boatimu_l"]] - "/boatimu_l"
		OUT:
			NaN
	9. void NavigationNodelet::pathConversion(const Json::Value &data, std::vector<GnssMath::gnss_base_type> &paths, int &path_target_index)
		DES:
			工具函数，处理路径???????
		IN:
			Json data, paths, path_target_index
		OUT:
			?????
	10. void NavigationNodelet::showToPc(const std::vector<GnssMath::gnss_base_type> &path, const std::vector<Json::Value> &attribute)
		DES: app显示路径???
		IN:
			path (list): 规划好的路径信息
			attri (list, optional): 路径点属性
				pathfileCallback中处理后的path_attribute->path_attribute_
		OUT:
			[["ros2mqtt_under_map"]]-"/ros2mqtt/under/map"
				
	11. bool NavigationNodelet::Refresh(boat_msgs::RefreshParams::Request &req, boat_msgs::RefreshParams::Response &res)
		DES:
			用来调用各个重置函数ServiceServer运作逻辑?????
		IN:
			[["refresh_control_params"]] - "/refresh_control_params"
		OUT:
			[[boat_param_ptr]]
				boat_param_ptr->RefreshParam(req.params_path);
			[[PathFollow.cpp]]
				path_follow->LoadParamsPtr(boat_param_ptr);
			[[Control.cpp]]
				control->LoadParamsPtr(boat_param_ptr);
	12. bool NavigationNodelet::ExceptionPorcess(boat_msgs::ExceptionSrv::Request &req, boat_msgs::ExceptionSrv::Response &res)
		DES:
			异常处理服务 ServiceServer????
		IN:
			[["nav_exception_hander"]] - req.cmd
		OUT:
			[[Control.cpp]]
			[[ConveyControl.cpp]] 
	13. void NavigationNodelet::callExceptionProcessSrv(const int &exception_cmd)
		DES:
			调用异常处理服务,与ExceptionPorcess的关系????
		IN:
			exception_cmd
			他处调用
		OUT:
			ExceptionPorcess
2. Relation:
	Topic:
		SUB:
			[["pathfile"]]
			[["gnss"]]
			[["odometry_filtered"]]
			[["auto_state"]]
			[["pathstop"]]
			[["boatweight"]]
			[["boatimu_l"]]
		PUB:
			[["boatcmd"]]
			[["boatcmds"]]
			[["global_direction"]]
			[["follow_info"]]
			[["ros2mqtt_under_map"]]
			[["ros2mqtt_pid_plotparams"]]
			[["ros2mqtt_under_hover"]]
			[["gps_odom"]]
		CLI：
			[["task_exception_handler"]]
		SRV:
			[["refresh_control_params"]]
			[["nav_exception_handler"]]