DES:订阅各个路径信息，整合为
	"/ros2mqtt/under/way" [["ros2mqtt_under_way"]]
	"/ros2mqtt/server/path" [["ros2mqtt_server_path"]]  发布
1. 函数:
	1. onInit()
		DES:
		IN:
		OUT:
	2. Refresh()
		DES:
		IN:
		OUT:
	3. LoadParameters()
		DES:
		IN:
		OUT:
	4. timerCallback()
		DES:
		IN:
		OUT:
	5. gps_callback()
	6. upper_way_callback()
	7. telectrl_callback()
	8. auto_callback()
	9. requestSrv()
	10. sync_paths()
	11. sendPath2App()
	12. pathResponse_callback()
	13. saveTaskId()
	14. latlon2local()
	15. local2latlon()
	16. shrinkCircle()
	17. pathPlanner()
	18. callsetPolygonSrv()
	19. callplanPathSrv()
	20. callupdateParamSrv()
	21. waypoint_callback()
	22. test()
	23. reversePaths()
2. Relation:
	Topic:
		SUB:
			[["gnss"]]
			[["boattelectrl"]]
			[["auto_state"]]
			[["mqtt2ros_upper_way"]]
			[["mqtt2ros_server_path_response"]]
			[["waypoint_list"]] 
		PUB:
			[["ros2mqtt_under_way"]]
			[["ros2mqtt_server_path"]]
		CLI:
			[["mqtt_client_is_connected"]]
			[["coverage_planner_set_polygon"]]
			[["coverage_planner_plan_path"]]
			[["coverage_planner_update_param"]]
		SRV:
			[["refresh_path2app_params"]]
	FILE:
		