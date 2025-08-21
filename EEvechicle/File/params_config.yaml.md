[[TaskManagerNodelet.cpp]] 
```YAML
d_power_step: 10

motor_type: 1 #水陆模式

cycle_mode: 1 #到头是原路返回(-1)还是到首尾相连(1)

init_convey: 9.5
pump_init_convey: 13
gram_per_sec_min: 6.27
gram_per_meter_min: 2
convey_max: 100.0
duty_cnt_set: 30
T1: 10.0
T2: 20.0
dgm_max: 20
dgm_min: -20
dgm_k: 0.7
is_update_k: true
is_close_loop: true
is_adjust_vel: true
weight_offset: 1.0
weight_speed_k: 0
weight_scatter_k: 0
weight_filter_k: 0.7
weight_sample_time: 0.5
weight_convey_zero_k1: 0.1
weight_convey_zero_k2: 0.0

#悬停控制参数
R_vel_min: 0.01
R_vel_max: 0.2
# 悬停半径
hover_r: 2.0
hover_R: 6.0
# vel
back_vel: -10 # -(0-50)

  
heading_bias: -90
dist_between_antennas: 0.85
```
