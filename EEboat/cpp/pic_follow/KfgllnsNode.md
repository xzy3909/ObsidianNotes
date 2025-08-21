ic/KfgllnsNode
  in : /boatImu_l /gnss
  out: /fused_path /boat_gnss_Init
  function: 发布<boat_msgs::GNSSFloat64>-boat_gnss_Init-/boat_gnss_Init, <nav_msgs::Path>-rosPath_-/fused_path

/serviceCall
  in : "/global_direction" --- "/global_direction"由NavigationNodelet.cpp中的pub_dir-<std_msgs::Float64>-"/global_direction"发布
       "/pic_follow_dir" --- PathFollow中的update_gps中pic_follow_clinet.call(PF_srv)----PF_srv/服务由AoidNode.cpp中的avoidServer-<AvoidNode::avoidService>-"/pic_follow_dir"发布
  out: ?