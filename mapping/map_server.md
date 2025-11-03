1. 地图储存格式：
	1. YAML 包含描述栅格地图的属性
		1. image
		2. resolution
		3. origin：2-D pose
		4. occupaied_thresh
		5. free_thresh
		6. nagate
		7. mode：trinary, scale, or raw. value interpretation
	2. png 包含栅格信息
2. 发布话题与服务：
	1. map_metadata (nav_msgs/MapMetaData)
		[[nav_msgs-MapMetaData]]
	2. map (nav_msgs/OccupancyGrid)
		[[nav_msgs-OccupancyGrid]]
	3. static_map (nav_msgs/GetMap)
		[[nav_msgs-GetMap]]
3. 使用方法：
	1. rosrun map_server map_server <map.yaml>
	2. rosrun map_server map_saver -f mymap
	3. rosrun map_server map_saver --occ 90 --free 10 -f mymap map:=/move_base/global_costmap/costmap
4. Reference：
	1. https://wiki.ros.org/map_server
