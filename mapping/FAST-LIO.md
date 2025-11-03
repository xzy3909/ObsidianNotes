记录mid360+FAST_LIO建图包的使用
1. mid360驱动
	1. 版本20.04noetic
		livox_ros_driver2
		Livox-SDK2
	2. 驱动配置
		```json
		{
		
			"lidar_summary_info" : {
				"lidar_type": 8
			},
			"MID360": {
				"lidar_net_info" : {
				"cmd_data_port": 56100,
				"push_msg_port": 56200,
				"point_data_port": 56300,
				"imu_data_port": 56400,
				"log_data_port": 56500
				},
			"host_net_info" : {
				"cmd_data_ip" : "192.168.1.100", // 主机ip
				"cmd_data_port": 56101,
				"push_msg_ip": "192.168.1.100",
				"push_msg_port": 56201,
				"point_data_ip": "192.168.1.100",
				"point_data_port": 56301,
				"imu_data_ip" : "192.168.1.100",
				"imu_data_port": 56401,
				"log_data_ip" : "",
				"log_data_port": 56501
				}
			},
			"lidar_configs" : [
				{
					"ip" : "192.168.1.101",      // 雷达ip
					"pcl_data_type" : 1,
					"pattern_mode" : 0,
					"extrinsic_parameter" : {
						"roll": 0.0,
						"pitch": 0.0,
						"yaw": 0.0,	
						"x": 0,		
						"y": 0,		
						"z": 0
					}
				}
			]	
		}
		```
2. FAST_LIO使用
	1. 仿真
		livox的雷达数据通过.lvx2文件储存，需要转换
		https://github.com/SupremeLyre/lvx2_to_rosbag
	2. 实操
		1. FAST-LIO 可以将点云数据保存为.pcd格式
			将 FAST_LIO/mid360.yaml 中的 pcd_save_en 设置为 true
		2. 通过 pcl_viewer 指令来查看保存的点云
		3. 通过 pcd2pgm 功能包来将三维点云转换为二维栅格地图
3. Reference
	1. https://blog.csdn.net/m0_62948300/article/details/140209873
	