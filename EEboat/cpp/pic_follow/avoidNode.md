  !!!!重点关注avoidServer-<avoidService>-/pic_follow_dir，该服务用于发布ref_vel和......
  in : 
    /points-<boat_msgs::Points> 图像得数据到的障碍物点
    /boatultrasound-<boat_msgs::BoatUltrasound> 超声波数据，用于处理得到障碍物点
    /boat_gnss_InIt-<boat_msgs::GNSSFloat64> 初始化？
    /boatmotors-<boat_msgs::BoatMotors> 根据明轮占空比，锁定相应超声波探头
  out(viz): 
    皆为visualization::Marker格式，用于可视化
    /pic_obs
    /ult_obs
    /plan_path
    /target_path
    /global_point
    /global_path
  ！！service:
    /pic_follow_dir-avoidServer-<avoidService> 在PathFollow.cpp中update_gps()中，通过PF_srv<boat_msgs::PicFollowSrv>来获取规划速度
    /refresh_aviod_params-refreshServer-<refreshService>