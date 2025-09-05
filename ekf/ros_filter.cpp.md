Fuction:
1. initialize()
	DES:
	IN:
		loadParams()
	OUT:
2. loadParams()
	DES:
		加载参数，为定义的出入创建订阅者
	IN:
		nhLocal_.param("...")
			std::stringstream ss;
			ss << "odom" << topicInd++;
			std::string odomTopicName = ss.str();
	OUT:
		topicSubs_.push_back(
			nh_.subscribe<nav_msgs::Odometry>(odomTopic, odomQueueSize,
			boost::bind(&RosFilter::odometryCallback, this, boost::placeholders::_ 1,
				odomTopicName, poseCallbackData, twistCallbackData),
				ros::VoidPtr(), ros::TransportHints().tcpNoDelay(nodelayOdom)));
		while (moreParams);
3. imuCallback()
	