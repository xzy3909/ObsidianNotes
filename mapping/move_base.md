1. MoveBase构造：
	- tf_是坐标变换TF2的接口对象;
	- as_则是Action服务器;
	- planner_costmap_ros_是用于全局规划器的代价地图对象;
	- controller_costmap_ros_则是局部规划器所用的代价地图对象;
	- bgp_loader_是装载全局规划器插件的工具;
	- blp_loader_是装载局部规划器插件的工具;
	- planner_plan_, latest_plan_ 和controller_plan_是三个记录规划结果的缓存;
	- runPlanner_, setup_, p_freq_change_, c_freq_change_, new_global_plan_则是一些控制和反映MoveBase系统状态的布尔变量。
2. Reference
	https://blog.csdn.net/lovely_yoshino/article/details/136872464