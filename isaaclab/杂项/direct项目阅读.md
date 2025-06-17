
# franka_cabinet项目

## franka_cabinet_env.py

### **一、环境配置类变量 (`FrankaCabinetEnvCfg`)**

#### 1. 基础参数

|变量名|类型|描述|默认值|
|---|---|---|---|
|`episode_length_s`|float|单episode时长(秒)|8.3333|
|`decimation`|int|物理模拟降采样率|2|
|`action_space`|int|动作空间维度|9|
|`observation_space`|int|观测空间维度|23|
|`state_space`|int|状态空间维度(未使用)|0|

#### 2. 物理模拟参数 (`sim`)

|变量名|描述|
|---|---|
|`dt`|物理时间步长(1/120秒)|
|`render_interval`|渲染间隔(=decimation)|
|`friction_combine_mode`|摩擦系数组合模式("multiply")|
|`restitution_combine_mode`|恢复系数组合模式("multiply")|
|`static_friction`|静摩擦系数(1.0)|
|`dynamic_friction`|动摩擦系数(1.0)|
|`restitution`|恢复系数(0.0)|

#### 3. 场景参数 (`scene`)

|变量名|描述|
|---|---|
|`num_envs`|并行环境数量(4096)|
|`env_spacing`|环境间距(3.0单位)|
|`replicate_physics`|是否复制物理实例(True)|

#### 4. 机器人参数 (`robot`)

|变量名|描述|
|---|---|
|`prim_path`|USD路径模板("/World/envs/env_.*/Robot")|
|`usd_path`|机器人模型路径|
|`joint_pos`|初始关节位置(7个关节+手指)|
|`pos/rot`|基座初始位姿|
|`actuators`|执行器分组配置(肩/前臂/手)|

#### 5. 橱柜参数 (`cabinet`)

|变量名|描述|
|---|---|
|`prim_path`|USD路径模板("/World/envs/env_.*/Cabinet")|
|`usd_path`|橱柜模型路径|
|`joint_pos`|初始关节位置(门/抽屉)|
|`pos/rot`|基座初始位姿|
|`actuators`|执行器分组配置(抽屉/门)|

#### 6. 地面参数 (`terrain`)

|变量名|描述|
|---|---|
|`prim_path`|USD路径("/World/ground")|
|`terrain_type`|地形类型("plane")|
|`collision_group`|碰撞组(-1)|

#### 7. 奖励系数

|变量名|描述|默认值|
|---|---|---|
|`action_scale`|动作缩放因子|7.5|
|`dof_velocity_scale`|关节速度缩放因子|0.1|
|`dist_reward_scale`|距离奖励系数|1.5|
|`rot_reward_scale`|朝向奖励系数|1.5|
|`open_reward_scale`|开度奖励系数|10.0|
|`action_penalty_scale`|动作惩罚系数|0.05|
|`finger_reward_scale`|手指奖励系数|2.0|

---

### **二、环境类成员变量 (`FrankaCabinetEnv`)**

#### 1. 核心对象

|变量名|类型|描述|
|---|---|---|
|`_robot`|`Articulation`|机器人实例|
|`_cabinet`|`Articulation`|橱柜实例|
|`_terrain`|`TerrainImporter`|地面实例|

#### 2. 物理参数

|变量名|描述|
|---|---|
|`dt`|控制步长(=sim.dt * decimation)|
|`actions`|当前步动作张量|

#### 3. 机器人状态

|变量名|描述|
|---|---|
|`robot_dof_lower_limits`|关节位置下限|
|`robot_dof_upper_limits`|关节位置上限|
|`robot_dof_speed_scales`|关节速度缩放系数|
|`robot_dof_targets`|目标关节位置|

#### 4. 位姿计算

|变量名|描述|
|---|---|
|`robot_local_grasp_pos/rot`|机械臂局部抓取位姿|
|`drawer_local_grasp_pos/rot`|抽屉局部抓取位姿|
|`robot_grasp_pos/rot`|机械臂全局抓取位姿|
|`drawer_grasp_pos/rot`|抽屉全局抓取位姿|

#### 5. 坐标轴定义

|变量名|描述|
|---|---|
|`gripper_forward_axis`|夹爪前进轴([0,0,1])|
|`drawer_inward_axis`|抽屉内推轴([-1,0,0])|
|`gripper_up_axis`|夹爪上轴([0,1,0])|
|`drawer_up_axis`|抽屉上轴([0,0,1])|

#### 6. 索引缓存

|变量名|描述|
|---|---|
|`hand_link_idx`|机械臂手部链接索引|
|`left_finger_link_idx`|左手指链接索引|
|`right_finger_link_idx`|右手指链接索引|
|`drawer_link_idx`|抽屉链接索引|

#### 7. 奖励跟踪

|变量名|描述|
|---|---|
|`extras["log"]`|各奖励分量日志字典|

---

### **三、关键方法局部变量**

#### 1. 观测计算 (`_get_observations`)

|变量名|描述|
|---|---|
|`dof_pos_scaled`|归一化关节位置|
|`to_target`|手部到目标的向量|

#### 2. 奖励计算 (`_compute_rewards`)

|变量名|描述|
|---|---|
|`d`|手部-目标欧氏距离|
|`dist_reward`|距离奖励分量|
|`axis1-4`|朝向计算用坐标轴|
|`dot1/dot2`|点积计算结果|
|`rot_reward`|朝向奖励分量|
|`action_penalty`|动作惩罚分量|
|`open_reward`|开度奖励分量|
|`lfinger_dist/rfinger_dist`|手指距离|

#### 3. 位姿计算 (`_compute_grasp_transforms`)

|变量名|描述|
|---|---|
|`global_franka_rot/pos`|计算机械臂全局位姿|
|`global_drawer_rot/pos`|计算抽屉全局位姿|

#### 4. 重置逻辑 (`_reset_idx`)

|变量名|描述|
|---|---|
|`joint_pos`|重置后的关节位置|
|`joint_vel`|重置后的关节速度(0)|
|`zeros`|橱柜重置用零张量|

---

### **四、辅助工具变量**

#### 位姿计算 (`get_env_local_pose`)

|变量名|描述|
|---|---|
|`env_pos`|环境原点位置|
|`xformable`|USD几何对象|
|`world_transform`|局部->世界变换矩阵|
|`world_pos`|世界坐标系位置|
|`world_quat`|世界坐标系旋转四元数|
|`px/py/pz`|相对位置分量|
|`qw/qx/qy/qz`|四元数分量|
