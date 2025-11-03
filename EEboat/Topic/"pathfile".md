DES:
	用于传递路径文件路径
SUB:
	[[NavigationNodelet.cpp]]
		pathfileCallback
	std::shared_ptr<ros::Subscriber> pathfile_sub = std::make_shared<ros::Subscriber>(nh.subscribe("pathfile", 1, &NavigationNodelet::, this));
PUB:
	[[TaskManagerNodelet.cpp]]
		taskStart()