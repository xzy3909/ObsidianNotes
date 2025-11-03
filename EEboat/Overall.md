APP任务执行流程：
	1. app下发mqtt话题{}/server/task/execute，对应rostopic话题[["mqtt2ros_server_task_execute"]] 
	2. [[TaskManagerNodelet.cpp]] 中的callback_ServerTaskExecute回调调用AppStart()
	3. AppStart中将路径点、任务属性等信息写入[[Task_path.json]] 中,diaoyon taskStart()
	4. taskStart() pub_pathFile.publish(file_msg); [["pathfile"]] 
	5. [[NavigationNodelet]] pathfileCallback() set path and attribute; diaoyon path_attribute ------>setPathAttribute, pathConversion
	6.  [[AttributeSet.cpp]] setPathAttribute() ---> dealCurPointAttribute() ---> setParameters()导航避障属性设置
isFeed?  判断是否撒料依据conveyList.Weight>0
	7. convey_control()  conveyPlanner_--->initCalculation()
	8. ConveyPlanner isFeed(segmentsList) change feedplannerData
	9. ConveyPlanner defaultPlanning push_back(segment)
	10.  ConveyPlanner conveyList -> isFeed() ---> feedplannerData
Weight
	11. LoadParamPtr() this->convey_control_params_ptr = boat_param_ptr->convey_control_params_ptr ----> globalFeedParams->totalWeight