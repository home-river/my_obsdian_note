# Prim
## arcticulation
### 概述

`Articulation` 是 Isaac Sim 中用于封装“关节化”刚体（机器人、机械臂等多自由度系统）的 Prim View。它将多个关节（DOF）、刚体（body）、关节绳（tendon）等组织在一起，提供高效的**批量状态读写**、**物理属性配置**、**关节控制指令下发**以及**生命周期管理**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`body_names`|刚体（link）名称列表|
|`dof_names`|自由度（DOF）名称列表|
|`joint_names`|关节（joint）名称列表|
|`num_bodies`|刚体数|
|`num_dof`|总自由度数|
|`num_fixed_tendons`|固定绳（tendon）数|
|`num_joints`|关节数|
|`num_shapes`|形状（shape）数|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|
|`pause_motion()`|暂停关节动力学更新|
|`resume_motion()`|恢复关节动力学更新|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `Articulation` 对象有效性|
|`is_physics_handle_valid()`|检查底层物理句柄是否有效|
|`is_visual_material_applied()`|检查是否已应用可视材质|

#### 3. 批量控制与动作下发

|方法名|说明|
|---|---|
|`apply_action(control_actions)`|按控制向量一次性下发所有 DOF 指令|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视材质|
|`switch_control_mode(mode)`|切换全局控制模式（位置/速度/力矩）|
|`switch_dof_control_mode(mode, dof_index)`|切换单个 DOF 的控制模式|

#### 4. 状态读取（Getters）

##### 4.1 关节／刚体状态

|方法名|返回值说明|
|---|---|
|`get_joint_positions()`|当前所有关节位置（rad 或 m）|
|`get_joint_velocities()`|当前所有关节速度|
|`get_angular_velocities()`|各 body 角速度|
|`get_linear_velocities()`|各 body 线速度|
|`get_world_poses()`|各 body 世界坐标系下的位姿|
|`get_local_poses()`|各 body 相对于父节点的局部位姿|
|`get_world_scales()`|各 body 世界缩放|
|`get_local_scales()`|各 body 局部缩放|

##### 4.2 动力学／物理性质

|方法名|返回值说明|
|---|---|
|`get_body_masses()`|各刚体质量|
|`get_body_inv_masses()`|各刚体质量倒数|
|`get_body_inertias()`|各刚体惯性张量|
|`get_body_inv_inertias()`|各刚体惯性张量倒数|
|`get_body_coms()`|各刚体质心位置|
|`get_body_disable_gravity()`|各 body 是否关闭重力|
|`get_friction_coefficients()`|关节摩擦系数|
|`get_gains()`|驱动器增益|
|`get_mass_matrices()`|广义质量矩阵|
|`get_mass_matrix_shape()`|质量矩阵维度|
|`get_coriolis_and_centrifugal_forces()`|科氏与向心力|
|`get_generalized_gravity_forces()`|广义重力力|
|`get_jacobians()`|各 DOF 关节雅可比矩阵|
|`get_jacobian_shape()`|雅可比矩阵维度|

##### 4.3 极限与约束

|方法名|返回值说明|
|---|---|
|`get_dof_limits()`|DOF 的最小/最大位置或速度限制|
|`get_drive_types()`|各 DOF 驱动类型（position/velocity/effort）|
|`get_effort_modes()`|各 DOF 的力矩模式|
|`get_joint_max_velocities()`|各关节最大速度|
|`get_max_efforts()`|各关节最大力矩|
|`get_sleep_thresholds()`|休眠阈值|
|`get_stabilization_thresholds()`|稳定化阈值|
|`get_enabled_self_collisions()`|自碰撞使能标志|

##### 4.4 观测与反馈

|方法名|返回值说明|
|---|---|
|`get_applied_actions()`|最近一次下发的控制指令|
|`get_applied_joint_efforts()`|最近一次下发的关节力矩|
|`get_measured_joint_efforts()`|仿真反馈的关节力矩|
|`get_measured_joint_forces()`|仿真反馈的关节轴向力|
|`get_position_residuals()`|位置残差|
|`get_velocity_residuals()`|速度残差|
|`get_visibilities()`|各 body 可见性|
|`get_applied_visual_materials()`|已应用的可视材质|

##### 4.5 索引查询

|方法名|参数|返回值说明|
|---|---|---|
|`get_body_index(body_name)`|body 名称|对应数组索引|
|`get_dof_index(dof_name)`|DOF 名称|对应数组索引|
|`get_joint_index(joint_name)`|joint 名称|对应数组索引|
|`get_link_index(link_name)`|link 名称|对应数组索引|

#### 5. 状态写入（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_joint_positions(positions)`|关节位置数组|直接写入关节位置|
|`set_joint_velocities(velocities)`|关节速度数组|直接写入关节速度|
|`set_joint_efforts(efforts)`|关节力矩数组|直接写入关节力矩|
|`set_joint_position_targets(positions)`|位置控制目标数组|下发位置控制命令|
|`set_joint_velocity_targets(velocities)`|速度控制目标数组|下发速度控制命令|
|`set_angular_velocities(values)`|各 body 角速度|直接写入|
|`set_linear_velocities(values)`|各 body 线速度|直接写入|
|`set_world_poses(poses)`|各 body 世界位姿|直接写入|
|`set_local_poses(poses)`|各 body 局部位姿|直接写入|
|`set_world_scales(scales)`|各 body 世界缩放|直接写入|
|`set_local_scales(scales)`|各 body 局部缩放|直接写入|
|`set_body_masses(values)`|各刚体质量|直接写入|
|`set_body_inertias(values)`|各刚体惯性张量|直接写入|
|`set_body_disable_gravity(values)`|重力使能标志数组|打开/关闭重力|
|`set_effort_modes(mode)`|单一模式或数组|设置 DOF 的力矩模式|
|`set_enabled_self_collisions(flags)`|布尔数组|打开/关闭自碰撞|
|`set_friction_coefficients(values)`|摩擦系数数组|设置关节摩擦|
|`set_gains(values)`|增益数组|设置驱动器增益|
|`set_max_efforts(values)`|最大力矩数组|设置关节最大力矩|
|`set_max_joint_velocities(values)`|最大速度数组|设置关节最大速度|
|`set_sleep_thresholds(thresholds)`|休眠阈值数组|设置休眠阈值|
|`set_stabilization_thresholds(thresholds)`|稳定化阈值数组|设置稳定化阈值|
|`set_solver_position_iteration_counts(counts)`|迭代计数数组|设置求解器位置迭代次数|
|`set_solver_velocity_iteration_counts(counts)`|迭代计数数组|设置求解器速度迭代次数|
|`set_default_state()`|—|写入当前状态为默认状态|
|`set_joints_default_state()`|—|写入所有关节当前状态为默认|
|`set_fixed_tendon_properties()`|各种 tendon 参数（阻尼、刚度等）|写入固定绳属性|


---

## ClothPrim

`ClothPrim` 是 Isaac Sim 中用于封装布料（cloth）仿真对象的 Prim View。它管理一组布料实例的粒子、弹簧和碰撞属性，提供高效的**批量状态读写**、**物理参数配置**、**可视材质管理**以及**生命周期控制**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`count`|布料实例数量|
|`max_particles_per_cloth`|每个布料的最大粒子数|
|`max_springs_per_cloth`|每个布料的最大弹簧（spring）数|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `ClothPrim` 对象有效性|
|`is_physics_handle_valid()`|检查底层物理句柄是否有效|
|`is_visual_material_applied()`|检查是否已应用可视材质|

#### 3. 可视材质管理

|方法名|说明|
|---|---|
|`apply_visual_materials(visual_materials)`|一次性应用所有布料可视材质|
|`get_applied_visual_materials()`|获取已应用的可视材质|

#### 4. 状态读取（Getters）

##### 4.1 布料物理参数

|方法名|返回值说明|
|---|---|
|`get_cloths_bend_stiffnesses()`|各布料弯曲刚度|
|`get_cloths_dampings()`|各布料阻尼|
|`get_cloths_shear_stiffnesses()`|各布料剪切刚度|
|`get_cloths_stretch_stiffnesses()`|各布料拉伸刚度|
|`get_stretch_stiffnesses()`|（别名）各布料拉伸刚度|
|`get_spring_dampings()`|各弹簧阻尼|
|`get_pressures()`|各布料内部气压|
|`get_particle_masses()`|各粒子质量|
|`get_particle_groups()`|各布料粒子分组|
|`get_self_collisions()`|各布料自碰撞开关|
|`get_self_collision_filters()`|各布料自碰撞过滤器|

##### 4.2 布料位姿与拓扑

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|各布料相对于父节点的局部位姿|
|`get_local_scales()`|各布料局部缩放|
|`get_world_poses()`|各布料世界位姿|
|`get_world_positions()`|各粒子世界坐标|
|`get_world_scales()`|各布料世界缩放|

##### 4.3 运动学状态

|方法名|返回值说明|
|---|---|
|`get_velocities()`|各粒子速度|
|`get_visibilities()`|各布料可见性状态|

##### 4.4 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取所有布料的默认状态|

### 五、状态写入（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_cloths_bend_stiffnesses(values)`|弯曲刚度数组|设置弯曲刚度|
|`set_cloths_dampings(values)`|阻尼数组|设置阻尼|
|`set_cloths_shear_stiffnesses(values)`|剪切刚度数组|设置剪切刚度|
|`set_cloths_stretch_stiffnesses(values)`|拉伸刚度数组|设置拉伸刚度|
|`set_spring_dampings(damping)`|弹簧阻尼|设置弹簧阻尼|
|`set_stretch_stiffnesses(stiffness)`|拉伸刚度|设置拉伸刚度|
|`set_particle_masses(masses)`|粒子质量数组|设置粒子质量|
|`set_particle_groups(particle_groups)`|粒子分组数组|设置粒子分组|
|`set_pressures(pressures)`|内部气压数组|设置气压|
|`set_self_collisions(self_collisions)`|自碰撞布尔数组|设置自碰撞开关|
|`set_self_collision_filters(self_collision_filters)`|自碰撞过滤器数组|设置自碰撞过滤器|
|`set_local_poses()`|—|写入当前局部位姿|
|`set_local_scales(scales)`|缩放数组|写入当前局部缩放|
|`set_world_poses()`|—|写入当前世界位姿|
|`set_world_positions(positions)`|位置数组|写入当前粒子世界位置|
|`set_velocities(velocities)`|速度数组|写入当前粒子速度|
|`set_visibilities(visibilities)`|可见性布尔数组|写入可见性状态|
|`set_default_state()`|—|写入当前状态为默认状态|

---
## DeformablePrim

`DeformablePrim` 是 Isaac Sim 中用于封装可变形刚体（deformable body）仿真对象的 Prim View。它管理每个体的碰撞网格与仿真网格的顶点、单元、材料属性及物理求解器参数，提供高效的**批量状态读写**、**材料与可视材质管理**、**物理参数配置**以及**生命周期控制**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`count`|可变形刚体实例数量|
|`max_collision_mesh_elements_per_body`|每体碰撞网格最大单元（elements）数|
|`max_collision_mesh_vertices_per_body`|每体碰撞网格最大顶点数|
|`max_simulation_mesh_elements_per_body`|每体仿真网格最大单元数|
|`max_simulation_mesh_vertices_per_body`|每体仿真网格最大顶点数|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `DeformablePrim` 对象有效性|
|`is_physics_handle_valid()`|检查底层物理句柄是否有效|
|`is_visual_material_applied()`|检查是否已应用可视材质|

#### 3. 材料管理

|方法名|说明|
|---|---|
|`apply_deformable_materials(deformable_materials)`|一次性应用所有可变形材料属性|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视化材质|
|`get_applied_deformable_materials()`|获取已应用的可变形材料属性|
|`get_applied_visual_materials()`|获取已应用的可视化材质|

#### 4. 状态读取（Getters）

##### 4.1 碰撞网格

|方法名|返回值说明|
|---|---|
|`get_collision_mesh_indices()`|碰撞网格拓扑索引数组|
|`get_collision_mesh_nodal_positions()`|碰撞网格顶点世界坐标|
|`get_collision_mesh_element_rest_poses()`|单元初始位姿|
|`get_collision_mesh_element_deformation_gradients()`|单元形变梯度|
|`get_collision_mesh_element_rotations()`|单元旋转张量|
|`get_collision_mesh_element_stresses()`|单元应力|

##### 4.2 仿真网格

|方法名|返回值说明|
|---|---|
|`get_simulation_mesh_indices()`|仿真网格拓扑索引数组|
|`get_simulation_mesh_rest_points()`|仿真网格顶点初始参考位置|
|`get_simulation_mesh_nodal_positions()`|仿真网格顶点世界坐标|
|`get_simulation_mesh_nodal_velocities()`|仿真网格顶点速度|
|`get_simulation_mesh_element_rest_poses()`|仿真单元初始位姿|
|`get_simulation_mesh_element_deformation_gradients()`|仿真单元形变梯度|
|`get_simulation_mesh_element_rotations()`|仿真单元旋转张量|
|`get_simulation_mesh_element_stresses()`|仿真单元应力|
|`get_simulation_mesh_kinematic_targets()`|仿真网格顶点运动学目标位置|

##### 4.3 物理与求解器设置

|方法名|返回值说明|
|---|---|
|`get_self_collisions()`|自碰撞开关布尔数组|
|`get_self_collision_filter_distances()`|自碰撞过滤距离数组|
|`get_settling_thresholds()`|稳定判定阈值|
|`get_sleep_thresholds()`|休眠阈值|
|`get_sleep_dampings()`|休眠阻尼|
|`get_solver_position_iteration_counts()`|求解器位置迭代次数|
|`get_vertex_velocity_dampings()`|顶点速度阻尼|

##### 4.4 位姿与可视化

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|各实例相对于父节点的局部位姿|
|`get_local_scales()`|各实例局部缩放|
|`get_world_poses()`|各实例世界位姿|
|`get_world_scales()`|各实例世界缩放|
|`get_visibilities()`|各实例及网格可见性状态|

##### 4.5 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取所有实例的默认状态|

### 五、状态写入（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_collision_mesh_indices(values)`|拓扑索引数组|写入碰撞网格拓扑|
|`set_collision_mesh_rest_points(values)`|顶点参考位置数组|写入碰撞网格初始顶点位置|
|`set_default_state()`|—|写入当前状态为默认状态|
|`set_local_poses()`|—|写入当前局部位姿|
|`set_local_scales(scales)`|缩放数组|写入当前局部缩放|
|`set_self_collision_filter_distances(values)`|过滤距离数组|设置自碰撞过滤距离|
|`set_self_collisions(values)`|布尔数组|设置自碰撞开关|
|`set_settling_thresholds(values)`|阈值数组|设置稳定判定阈值|
|`set_simulation_mesh_indices(values)`|拓扑索引数组|写入仿真网格拓扑|
|`set_simulation_mesh_kinematic_targets(positions)`|运动学目标位置数组|写入仿真网格运动学目标|
|`set_simulation_mesh_nodal_positions(positions)`|顶点位置数组|写入仿真网格顶点位置|
|`set_simulation_mesh_nodal_velocities(velocities)`|速度数组|写入仿真网格顶点速度|
|`set_simulation_mesh_rest_points(values)`|顶点参考位置数组|写入仿真网格初始顶点位置|
|`set_sleep_dampings(values)`|阻尼数组|设置休眠阻尼|
|`set_sleep_thresholds(values)`|阈值数组|设置休眠阈值|
|`set_solver_position_iteration_counts(values)`|迭代计数数组|设置求解器位置迭代次数|
|`set_vertex_velocity_dampings(values)`|阻尼数组|设置顶点速度阻尼|
|`set_visibilities(visibilities)`|可见性布尔数组|写入可见性状态|
|`set_world_poses()`|—|写入当前世界位姿|

---
## GeometryPrim

`GeometryPrim` 是 Isaac Sim 中用于封装几何体（geometry）对象的 Prim View。它管理一组几何体的碰撞、物理材质、可视材质及变换属性，提供高效的**批量状态读写**、**碰撞与接触配置**、**材质管理**以及**生命周期控制**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`geoms`|几何体名称列表|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 碰撞配置与开关

|方法名|说明|
|---|---|
|`apply_collision_apis()`|启用碰撞相关 API|
|`disable_collision()`|禁用所有几何体碰撞|
|`enable_collision()`|启用所有几何体碰撞|
|`is_collision_enabled()`|查询碰撞是否已启用|

#### 3. 材质管理

|方法名|说明|
|---|---|
|`apply_physics_materials(physics_materials)`|一次性应用所有物理材质|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视材质|
|`get_applied_physics_materials()`|获取已应用的物理材质|
|`get_applied_visual_materials()`|获取已应用的可视材质|

#### 4. 状态读取（Getters）

##### 4.1 碰撞与接触属性

|方法名|返回值说明|
|---|---|
|`get_collision_approximations()`|碰撞近似类型数组|
|`get_contact_offsets()`|接触偏移距离数组|
|`get_rest_offsets()`|静止偏移距离数组|
|`get_min_torsional_patch_radii()`|最小扭转补丁半径数组|
|`get_torsional_patch_radii()`|扭转补丁半径数组|

##### 4.2 接触力与摩擦

|方法名|返回值说明|
|---|---|
|`get_contact_force_data()`|接触力数据数组|
|`get_contact_force_matrix()`|接触力矩阵|
|`get_net_contact_forces()`|净接触力向量数组|
|`get_friction_data()`|摩擦数据数组|

##### 4.3 变换与可视化

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|各几何体相对于父节点的局部位姿|
|`get_local_scales()`|各几何体局部缩放|
|`get_world_poses()`|各几何体世界位姿|
|`get_world_scales()`|各几何体世界缩放|
|`get_visibilities()`|各几何体可见性状态|

##### 4.4 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取所有几何体的默认状态|

### 五、状态写入（Setters）

| 方法名                                                 | 参数         | 说明          |
| --------------------------------------------------- | ---------- | ----------- |
| `set_collision_approximations(approximation_types)` | 碰撞近似类型数组   | 写入碰撞近似      |
| `set_contact_offsets(offsets)`                      | 接触偏移距离数组   | 写入接触偏移      |
| `set_rest_offsets(offsets)`                         | 静止偏移距离数组   | 写入静止偏移      |
| `set_min_torsional_patch_radii(radii)`              | 最小扭转补丁半径数组 | 写入最小扭转补丁半径  |
| `set_torsional_patch_radii(radii)`                  | 扭转补丁半径数组   | 写入扭转补丁半径    |
| `set_local_poses()`                                 | —          | 写入当前局部位姿    |
| `set_local_scales(scales)`                          | 缩放数组       | 写入当前局部缩放    |
| `set_visibilities(visibilities)`                    | 可见性布尔数组    | 写入可见性状态     |
| `set_default_state()`                               | —          | 写入当前状态为默认状态 |
| `set_world_poses()`                                 | —          | 写入当前世界位姿    |

---
## ParticleSystem

`ParticleSystem` 是 Isaac Sim 中用于封装粒子系统（particles）的 Prim View。它管理粒子的材料、接触与碰撞参数、自碰撞、求解器与全局环境（如风）设置，提供高效的**批量状态读写**、**物理参数配置**、**材质管理**以及**生命周期控制**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`count`|粒子系统实例数量|
|`name`|粒子系统名称|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `ParticleSystem` 对象有效性|
|`is_physics_handle_valid()`|检查底层物理句柄是否有效|

#### 3. 材质管理

|方法名|说明|
|---|---|
|`apply_particle_materials(particle_materials)`|一次性应用所有粒子材料|
|`get_applied_particle_materials()`|获取已应用的粒子材料|

#### 4. 状态读取（Getters）

##### 4.1 接触与碰撞参数

|方法名|返回值说明|
|---|---|
|`get_contact_offsets()`|全局接触偏移距离|
|`get_particle_contact_offsets()`|粒子级接触偏移距离|
|`get_rest_offsets()`|静止偏移距离|
|`get_fluid_rest_offsets()`|流体静止偏移距离|
|`get_solid_rest_offsets()`|固体静止偏移距离|

##### 4.2 系统与自碰撞开关

|方法名|返回值说明|
|---|---|
|`get_enable_ccds()`|连续碰撞检测开关|
|`get_global_self_collisions_enabled()`|全局自碰撞开关|
|`get_particle_systems_enabled()`|粒子系统总开关|

##### 4.3 性能参数

|方法名|返回值说明|
|---|---|
|`get_max_depenetration_velocities()`|最大去穿透速度|
|`get_max_neighborhoods()`|最大邻域粒子数|
|`get_max_velocities()`|最大粒子速度|

##### 4.4 求解器与环境设置

|方法名|返回值说明|
|---|---|
|`get_solver_position_iteration_counts()`|求解器位置迭代次数|
|`get_simulation_owners()`|归属仿真域标识|
|`get_winds()`|全局风场向量|

### 五、状态写入（Setters）

| 方法名                                            | 参数       | 说明          |
| ---------------------------------------------- | -------- | ----------- |
| `set_contact_offsets(values)`                  | 接触偏移数组   | 写入全局接触偏移    |
| `set_particle_contact_offsets(values)`         | 粒子接触偏移数组 | 写入粒子级接触偏移   |
| `set_rest_offsets(values)`                     | 静止偏移数组   | 写入静止偏移      |
| `set_fluid_rest_offsets(values)`               | 流体静止偏移数组 | 写入流体静止偏移    |
| `set_solid_rest_offsets(values)`               | 固体静止偏移数组 | 写入固体静止偏移    |
| `set_enable_ccds(values)`                      | 布尔数组     | 设置连续碰撞检测开关  |
| `set_global_self_collisions_enabled(values)`   | 布尔数组     | 设置全局自碰撞开关   |
| `set_particle_systems_enabled(values)`         | 布尔数组     | 设置粒子系统总开关   |
| `set_max_depenetration_velocities(values)`     | 数值数组     | 设置最大去穿透速度   |
| `set_max_neighborhoods(values)`                | 数值数组     | 设置最大邻域粒子数   |
| `set_max_velocities(values)`                   | 数值数组     | 设置最大粒子速度    |
| `set_solver_position_iteration_counts(values)` | 迭代计数数组   | 设置求解器位置迭代次数 |
| `set_simulation_owners(values)`                | 标识数组     | 写入仿真域归属     |
| `set_winds(values)`                            | 向量数组     | 写入全局风场      |


---
## RigidPrim

`RigidPrim` 是 Isaac Sim 中用于封装刚体（rigid body）对象的 Prim View。它管理刚体的形状数量、物理力学参数、接触与碰撞数据、力与扭矩应用、重力与物理开关，以及几何变换与可视材质等，提供高效的**批量状态读写**、**物理参数配置**、**生命周期控制**和**力/扭矩应用**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`num_shapes`|刚体形状（子几何体）数量|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `RigidPrim` 对象有效性|
|`is_physics_handle_valid()`|检查底层物理句柄是否有效|
|`is_visual_material_applied()`|检查是否已应用可视材质|

#### 3. 力、扭矩与物理开关

|方法名|说明|
|---|---|
|`apply_forces(forces)`|对刚体施加力|
|`apply_forces_and_torques_at_pos()`|在指定位置同时施加力与扭矩|
|`disable_gravities()`|禁用刚体重力|
|`enable_gravities()`|启用刚体重力|
|`disable_rigid_body_physics()`|禁用刚体物理仿真|
|`enable_rigid_body_physics()`|启用刚体物理仿真|

#### 4. 可视材质管理

|方法名|说明|
|---|---|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视材质|
|`get_applied_visual_materials()`|获取已应用的可视材质|

### 三、状态读取（Getters）

##### 3.1 动态状态

|方法名|返回值说明|
|---|---|
|`get_current_dynamic_state()`|当前动态状态（激活/休眠等）|
|`get_linear_velocities()`|刚体线速度数组|
|`get_angular_velocities()`|刚体角速度数组|
|`get_velocities()`|综合速度（线＋角）数组|

##### 3.2 物理属性

|方法名|返回值说明|
|---|---|
|`get_masses()`|质量数组|
|`get_inv_masses()`|质量倒数数组|
|`get_inertias()`|惯性张量数组|
|`get_inv_inertias()`|惯性张量倒数数组|
|`get_densities()`|密度数组|
|`get_friction_data()`|摩擦数据数组|

##### 3.3 接触与碰撞

|方法名|返回值说明|
|---|---|
|`get_contact_force_data()`|接触力原始数据|
|`get_contact_force_matrix()`|接触力矩阵|
|`get_net_contact_forces()`|净接触力向量数组|

##### 3.4 休眠与质心

|方法名|返回值说明|
|---|---|
|`get_sleep_thresholds()`|休眠阈值数组|
|`get_coms()`|刚体质心位置数组|

##### 3.5 几何变换与可视化

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|相对于父节点的局部位姿数组|
|`get_local_scales()`|局部缩放数组|
|`get_world_poses()`|世界坐标系下位姿数组|
|`get_world_scales()`|世界缩放数组|
|`get_visibilities()`|可见性布尔数组|

##### 3.6 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取当前默认状态|

### 四、状态写入（Setters）

| 方法名                                  | 参数      | 说明          |
| ------------------------------------ | ------- | ----------- |
| `set_angular_velocities(velocities)` | 角速度数组   | 写入刚体角速度     |
| `set_linear_velocities(velocities)`  | 线速度数组   | 写入刚体线速度     |
| `set_velocities(velocities)`         | 综合速度数组  | 写入线＋角综合速度   |
| `set_coms(coms)`                     | 质心位置数组  | 写入刚体质心位置    |
| `set_default_state()`                | —       | 写入当前状态为默认状态 |
| `set_densities(densities)`           | 密度数组    | 写入密度        |
| `set_inertias(values)`               | 惯性张量数组  | 写入惯性张量      |
| `set_local_poses()`                  | —       | 写入当前局部位姿    |
| `set_local_scales(scales)`           | 缩放数组    | 写入当前局部缩放    |
| `set_masses(masses)`                 | 质量数组    | 写入质量        |
| `set_sleep_thresholds(thresholds)`   | 阈值数组    | 写入休眠阈值      |
| `set_visibilities(visibilities)`     | 可见性布尔数组 | 写入可见性状态     |
| `set_world_poses()`                  | —       | 写入当前世界位姿    |

---
## SdfShapePrim

`SdfShapePrim` 是 Isaac Sim 中用于封装基于 Signed Distance Field（SDF）形状的 Prim View。它管理 SDF 查询点、碰撞近似、物理与可视材质、接触数据、SDF 参数与几何变换等，提供高效的**SDF 查询与梯度计算**、**批量状态读写**、**碰撞与接触配置**、**生命周期控制**等功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`num_query_points`|每个 Shape 可用于 SDF 查询的采样点数量|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部物理句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 碰撞配置与开关

|方法名|说明|
|---|---|
|`apply_collision_apis()`|启用碰撞相关 API|
|`disable_collision()`|禁用碰撞|
|`enable_collision()`|启用碰撞|
|`is_collision_enabled()`|查询碰撞是否已启用|

#### 3. 材质管理

|方法名|说明|
|---|---|
|`apply_physics_materials(physics_materials)`|一次性应用所有物理材质|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视材质|
|`get_applied_physics_materials()`|获取已应用的物理材质|
|`get_applied_visual_materials()`|获取已应用的可视材质|

#### 4. SDF 查询与参数

|方法名|返回值说明|
|---|---|
|`get_sdf_and_gradients(points)`|返回各查询点的 SDF 值与梯度|
|`get_sdf_margins()`|SDF 边界宽度|
|`get_sdf_narrow_band_thickness()`|SDF 窄带厚度|
|`get_sdf_resolution()`|SDF 全局分辨率|
|`get_sdf_subgrid_resolution()`|SDF 子网格分辨率|

#### 5. 接触与碰撞属性

|方法名|返回值说明|
|---|---|
|`get_collision_approximations()`|碰撞近似类型数组|
|`get_contact_offsets()`|接触偏移距离数组|
|`get_rest_offsets()`|静止偏移距离数组|
|`get_min_torsional_patch_radii()`|最小扭转补丁半径数组|
|`get_torsional_patch_radii()`|扭转补丁半径数组|

#### 6. 接触力与摩擦

|方法名|返回值说明|
|---|---|
|`get_contact_force_data()`|原始接触力数据|
|`get_contact_force_matrix()`|接触力矩阵|
|`get_net_contact_forces()`|净接触力向量数组|
|`get_friction_data()`|摩擦数据数组|

#### 7. 变换与可视化

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|相对于父节点的局部位姿数组|
|`get_local_scales()`|局部缩放数组|
|`get_world_poses()`|世界位姿数组|
|`get_world_scales()`|世界缩放数组|
|`get_visibilities()`|可见性布尔数组|

#### 8. 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取当前默认状态|

### 三、状态写入（Setters）

| 方法名                                                 | 参数         | 说明            |
| --------------------------------------------------- | ---------- | ------------- |
| `set_collision_approximations(approximation_types)` | 碰撞近似类型数组   | 写入碰撞近似        |
| `set_contact_offsets(offsets)`                      | 接触偏移距离数组   | 写入接触偏移        |
| `set_rest_offsets(offsets)`                         | 静止偏移距离数组   | 写入静止偏移        |
| `set_min_torsional_patch_radii(radii)`              | 最小扭转补丁半径数组 | 写入最小扭转补丁半径    |
| `set_torsional_patch_radii(radii)`                  | 扭转补丁半径数组   | 写入扭转补丁半径      |
| `set_sdf_margins(values)`                           | 数值         | 设置 SDF 边界宽度   |
| `set_sdf_narrow_band_thickness(values)`             | 数值         | 设置 SDF 窄带厚度   |
| `set_sdf_resolution(values)`                        | 整数         | 设置 SDF 全局分辨率  |
| `set_sdf_subgrid_resolution(values)`                | 整数         | 设置 SDF 子网格分辨率 |
| `set_local_poses()`                                 | —          | 写入局部位姿        |
| `set_local_scales(scales)`                          | 缩放数组       | 写入局部缩放        |
| `set_visibilities(visibilities)`                    | 可见性布尔数组    | 写入可见性状态       |
| `set_default_state()`                               | —          | 写入当前状态为默认状态   |
| `set_world_poses()`                                 | —          | 写入世界位姿        |

---
## XFormPrim

`XFormPrim` 是 Isaac Sim 中用于封装通用变换（transform）对象的 Prim View。它管理几何体的可视材质、变换状态与可见性等，提供简洁的**批量状态读写**、**材质管理**和**生命周期控制**功能。

### 一、类成员属性

|属性名|含义|
|---|---|
|`is_non_root_articulation_link`|是否为非根关节链接（仅对关节链有效）|

### 二、类方法

#### 1. 初始化与生命周期

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部句柄|
|`post_reset()`|环境重置后重新初始化状态|

#### 2. 有效性检测

|方法名|说明|
|---|---|
|`is_valid()`|检查 `XFormPrim` 对象有效性|
|`is_visual_material_applied()`|检查是否已应用可视材质|

#### 3. 可视材质管理

|方法名|说明|
|---|---|
|`apply_visual_materials(visual_materials)`|一次性应用所有可视材质|
|`get_applied_visual_materials()`|获取已应用的可视材质|

### 三、状态读取（Getters）

##### 3.1 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state()`|获取当前默认状态|

##### 3.2 几何变换

|方法名|返回值说明|
|---|---|
|`get_local_poses()`|相对于父节点的局部位姿数组|
|`get_local_scales()`|局部缩放数组|
|`get_world_poses()`|世界位姿数组|
|`get_world_scales()`|世界缩放数组|

##### 3.3 可见性

|方法名|返回值说明|
|---|---|
|`get_visibilities()`|可见性布尔数组|

### 四、状态写入（Setters）

| 方法名                              | 参数      | 说明          |
| -------------------------------- | ------- | ----------- |
| `set_default_state()`            | —       | 写入当前状态为默认状态 |
| `set_local_poses()`              | —       | 写入当前局部位姿    |
| `set_local_scales(scales)`       | 缩放数组    | 写入当前局部缩放    |
| `set_world_poses()`              | —       | 写入当前世界位姿    |
| `set_visibilities(visibilities)` | 可见性布尔数组 | 写入可见性状态     |

---
# Single Prim

## SingleArticulation

`SingleArticulation` 是 Isaac Sim 中用于封装单个关节链实例的 Prim View。它集成了动作控制、物理属性、自碰撞、动力学状态与几何变换等，提供从控制输入到物理状态读写的完整接口。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|Articulation Prim 的路径|
|`dof_names`|自由度名称列表|
|`dof_properties`|自由度属性列表|
|`num_bodies`|关节链中的刚体数量|
|`num_dof`|自由度总数|
|`handles_initialized`|是否已初始化底层句柄|

### 二、类方法

#### 1. 初始化与有效性检测

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部句柄|
|`post_reset()`|环境重置后重新初始化状态|
|`is_valid() → bool`|检查对象有效性|
|`is_visual_material_applied() → bool`|检查是否已应用可视材质|

#### 2. 动作与材质管理

|方法名|说明|
|---|---|
|`apply_action(control_actions)`|应用控制动作|
|`get_applied_action() → array`|获取已应用动作|
|`apply_visual_material(visual_material)`|应用可视材质|
|`get_applied_visual_material() → material`|获取已应用材质|

#### 3. 重力与自碰撞

|方法名|说明|
|---|---|
|`disable_gravity()`|禁用重力|
|`enable_gravity()`|启用重力|
|`get_enabled_self_collisions() → bool`|获取自碰撞状态|
|`set_enabled_self_collisions(flag: bool)`|写入自碰撞状态|

#### 4. Articulation 信息

|方法名|返回／说明|
|---|---|
|`get_articulation_body_count() → int`|刚体总数|
|`get_articulation_controller() → Controller`|获取关节链控制器|
|`get_dof_index(dof_name: str) → int`|获取指定自由度的索引|

### 三、状态读取（Getters）

##### 3.1 关节状态

|方法名|返回值说明|
|---|---|
|`get_joint_positions() → array`|各关节位置|
|`get_joint_velocities() → array`|各关节速度|
|`get_measured_joint_efforts() → array`|测得的关节力矩|
|`get_measured_joint_forces() → array`|测得的关节力|

##### 3.2 速度与残差

|方法名|返回值说明|
|---|---|
|`get_linear_velocity() → vec3`|本体线速度|
|`get_angular_velocity() → vec3`|本体角速度|
|`get_world_velocity() → vec3`|世界坐标系下总体速度|
|`get_position_residual() → float`|位置残差|
|`get_velocity_residual() → float`|速度残差|

##### 3.3 求解器参数与休眠

|方法名|返回值说明|
|---|---|
|`get_sleep_threshold() → float`|休眠阈值|
|`get_solver_position_iteration_count() → int`|位置迭代次数|
|`get_solver_velocity_iteration_count() → int`|速度迭代次数|
|`get_stabilization_threshold() → float`|稳定化阈值|

##### 3.4 变换与可见性

|方法名|返回值说明|
|---|---|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

##### 3.5 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state() → State`|获取当前默认状态|
|`get_joints_default_state() → StateArray`|获取各关节默认状态|

### 四、状态写入（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_joint_positions(positions: array)`|positions|写入关节位置|
|`set_joint_velocities(velocities: array)`|velocities|写入关节速度|
|`set_joint_efforts(efforts: array)`|efforts|写入关节力矩|
|`set_linear_velocity(velocity: vec3)`|velocity|写入线速度|
|`set_angular_velocity(velocity: vec3)`|velocity|写入角速度|
|`set_sleep_threshold(threshold: float)`|threshold|写入休眠阈值|
|`set_solver_position_iteration_count(count: int)`|count|写入位置迭代次数|
|`set_solver_velocity_iteration_count(count: int)`|count|写入速度迭代次数|
|`set_stabilization_threshold(threshold: float)`|threshold|写入稳定化阈值|
|`set_local_pose()`|—|写入当前局部位姿|
|`set_local_scale(scale: vec3)`|scale|写入当前局部缩放|
|`set_world_pose()`|—|写入当前世界位姿|
|`set_visibility(visible: bool)`|visible|写入可见性状态|
|`set_default_state()`|—|写入当前默认状态|
|`set_joints_default_state()`|—|写入各关节默认状态|
|`set_enabled_self_collisions(flag: bool)`|flag|写入自碰撞状态|

---

## SingleClothPrim

`SingleClothPrim` 是 Isaac Sim 中用于封装单一布料对象的 Prim View。它管理布料的物理参数、粒子系统、可视材质、动力学状态与变换，提供从参数配置到状态读写的完整接口。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|Cloth Prim 的路径|
|`particle_system`|对应的粒子系统对象|
|`mesh`|布料的网格几何体|

### 二、类方法

#### 1. 初始化与有效性检测

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部句柄|
|`post_reset()`|场景重置后重新初始化状态|
|`is_valid() → bool`|检查对象有效性|
|`is_visual_material_applied() → bool`|检查是否已应用可视材质|

#### 2. 可视材质管理

|方法名|说明|
|---|---|
|`apply_visual_material(visual_material)`|应用可视材质|
|`get_applied_visual_material() → material`|获取已应用材质|

### 三、状态读取（Getters）

#### 3.1 物理参数

|方法名|返回值说明|
|---|---|
|`get_cloth_bend_stiffness() → float`|布料弯曲刚度|
|`get_cloth_shear_stiffness() → float`|布料剪切刚度|
|`get_cloth_stretch_stiffness() → float`|布料拉伸刚度|
|`get_cloth_damping() → float`|布料阻尼系数|
|`get_spring_damping() → float`|弹簧阻尼系数|
|`get_stretch_stiffness() → float`|弹簧拉伸刚度（弹簧常数）|
|`get_pressure() → float`|内部压力|
|`get_self_collision() → bool`|自碰撞是否启用|
|`get_self_collision_filter() → array`|自碰撞过滤规则|

#### 3.2 动态与默认状态

|方法名|返回值说明|
|---|---|
|`get_current_dynamic_state() → State`|当前动态状态|
|`get_default_state() → State`|默认状态|
|`get_particle_group() → ParticleGroup`|粒子分组对象|

#### 3.3 变换与可见性

|方法名|返回值说明|
|---|---|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

### 四、状态写入（Setters）

| 方法名                                                       | 参数                    | 说明        |
| --------------------------------------------------------- | --------------------- | --------- |
| `set_cloth_bend_stiffness(stiffness: float)`              | stiffness             | 写入布料弯曲刚度  |
| `set_cloth_shear_stiffness(stiffness: float)`             | stiffness             | 写入布料剪切刚度  |
| `set_cloth_stretch_stiffness(stiffness: float)`           | stiffness             | 写入布料拉伸刚度  |
| `set_cloth_damping(damping: float)`                       | damping               | 写入布料阻尼系数  |
| `set_spring_damping(damping: float)`                      | damping               | 写入弹簧阻尼系数  |
| `set_stretch_stiffness(stiffness: float)`                 | stiffness             | 写入弹簧拉伸刚度  |
| `set_pressure(pressure: float)`                           | pressure              | 写入内部压力    |
| `set_self_collision(self_collision: bool)`                | self_collision        | 写入自碰撞状态   |
| `set_self_collision_filter(self_collision_filter: array)` | self_collision_filter | 写入自碰撞过滤规则 |
| `set_default_state()`                                     | —                     | 写入当前默认状态  |
| `set_particle_group(particle_group: ParticleGroup)`       | particle_group        | 写入粒子分组    |
| `set_local_pose()`                                        | —                     | 写入当前局部位姿  |
| `set_local_scale(scale: vec3)`                            | scale                 | 写入当前局部缩放  |
| `set_world_pose()`                                        | —                     | 写入当前世界位姿  |
| `set_visibility(visible: bool)`                           | visible               | 写入可见性状态   |


---
## SingleDeformablePrim

`SingleDeformablePrim` 是 Isaac Sim 中用于封装单一可变形体（deformable）对象的 Prim View。它管理可变形材质、可视材质、网格索引、动力学状态、物理参数、自碰撞与几何变换，提供从参数配置到状态读写的完整接口。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|Deformable Prim 的路径|
|`mesh`|可变形体的网格几何体|

### 二、类方法

#### 1. 初始化与有效性检测

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部句柄|
|`post_reset()`|场景重置后重新初始化状态|
|`is_valid() → bool`|检查对象有效性|
|`is_visual_material_applied() → bool`|检查是否已应用可视材质|

#### 2. 材质管理

|方法名|说明|
|---|---|
|`apply_deformable_material(deformable_materials)`|应用可变形体物理材质|
|`get_applied_deformable_material() → material`|获取已应用的可变形体材质|
|`apply_visual_material(visual_material)`|应用可视材质|
|`get_applied_visual_material() → material`|获取已应用的可视材质|

### 三、状态读取（Getters）

#### 3.1 网格索引与静止点

|方法名|返回值说明|
|---|---|
|`get_collision_mesh_indices() → array`|碰撞网格索引数组|
|`get_simulation_mesh_indices() → array`|仿真网格索引数组|
|`get_simulation_mesh_rest_points() → array`|仿真网格静止点位置数组|

#### 3.2 动态与默认状态

|方法名|返回值说明|
|---|---|
|`get_current_dynamic_state() → State`|当前动态状态|
|`get_default_state() → State`|默认状态|

#### 3.3 物理参数与自碰撞

|方法名|返回值说明|
|---|---|
|`get_self_collision() → bool`|自碰撞是否启用|
|`get_self_collision_filter_distance() → float`|自碰撞过滤距离|
|`get_settling_threshold() → float`|稳定阈值|
|`get_sleep_damping() → float`|睡眠阻尼系数|
|`get_sleep_threshold() → float`|睡眠阈值|
|`get_solver_position_iteration_count() → int`|位置求解迭代次数|
|`get_vertex_velocity_damping() → float`|顶点速度阻尼系数|

#### 3.4 几何变换与可见性

|方法名|返回值说明|
|---|---|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

### 四、状态写入（Setters）

| 方法名                                                                         | 参数                             | 说明         |
| --------------------------------------------------------------------------- | ------------------------------ | ---------- |
| `set_default_state()`                                                       | —                              | 写入当前默认状态   |
| `set_local_pose()`                                                          | —                              | 写入当前局部位姿   |
| `set_local_scale(scale: vec3)`                                              | scale                          | 写入当前局部缩放   |
| `set_world_pose()`                                                          | —                              | 写入当前世界位姿   |
| `set_self_collision(self_collision: bool)`                                  | self_collision                 | 写入自碰撞状态    |
| `set_self_collision_filter_distance(self_collision_filter_distance: float)` | self_collision_filter_distance | 写入自碰撞过滤距离  |
| `set_settling_threshold(settling_threshold: float)`                         | settling_threshold             | 写入稳定阈值     |
| `set_sleep_damping(sleep_damping: float)`                                   | sleep_damping                  | 写入睡眠阻尼系数   |
| `set_sleep_threshold(sleep_threshold: float)`                               | sleep_threshold                | 写入睡眠阈值     |
| `set_solver_position_iteration_count(iterations: int)`                      | iterations                     | 写入位置求解迭代次数 |
| `set_vertex_velocity_damping(vertex_velocity_damping: float)`               | vertex_velocity_damping        | 写入顶点速度阻尼系数 |
| `set_visibility(visible: bool)`                                             | visible                        | 写入可见性状态    |


---
## SingleGeometryPrim

`SingleGeometryPrim` 是 Isaac Sim 中用于封装单一几何体对象的 Prim View。它管理物理与可视材质、碰撞与接触参数、几何变换与可见性，提供从参数配置到状态读写的完整接口。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|Geometry Prim 的路径|
|`geom`|几何体对象引用|

### 二、类方法

#### 1. 初始化与有效性检测

|方法名|说明|
|---|---|
|`initialize()`|构建并激活内部句柄|
|`post_reset()`|场景重置后重新初始化状态|
|`is_valid() → bool`|检查对象有效性|
|`is_visual_material_applied() → bool`|检查是否已应用可视材质|

#### 2. 材质管理

|方法名|说明|
|---|---|
|`apply_ph[object Object][object Object][object Object][object Object][object Object][object Object][object Object][object Object] 应用物理材质||
|`get_applied_physics_material() → material`|获取已应用物理材质|
|`apply_visual_material(visual_material)`|应用可视材质|
|`get_applied_visual_material() → material`|获取已应用可视材质|

### 三、状态读取（Getters）

#### 3.1 碰撞与接触

|方法名|返回值说明|
|---|---|
|`get_collision_approximation() → type`|碰撞近似类型|
|`get_collision_enabled() → bool`|碰撞是否启用|
|`get_contact_force_data() → array`|原始接触力数据|
|`get_contact_force_matrix() → matrix`|接触力矩阵|
|`get_contact_offset() → float`|接触偏移量|
|`get_net_contact_forces() → array`|净接触力向量|
|`get_friction_data() → array`|摩擦参数数据|

#### 3.2 补丁与偏移

|方法名|返回值说明|
|---|---|
|`get_min_torsional_patch_radius() → float`|最小扭转补丁半径|
|`get_torsional_patch_radius() → float`|扭转补丁半径|
|`get_rest_offset() → float`|静止偏移量|

#### 3.3 几何变换与可见性

|方法名|返回值说明|
|---|---|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

#### 3.4 默认状态

|方法名|返回值说明|
|---|---|
|`get_default_state() → State`|获取当前默认状态|

### 四、状态写入（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_collision_approximation(approximation_type)`|approximation_type|写入碰撞近似类型|
|`set_collision_enabled(enabled: bool)`|enabled|写入碰撞开关|
|`set_contact_offset(offset: float)`|offset|写入接触偏移量|
|`set_default_state()`|—|写入当前默认状态|
|`set_local_pose()`|—|写入当前局部位姿|
|`set_local_scale(scale: vec3)`|scale|写入当前局部缩放|
|`set_rest_offset(offset: float)`|offset|写入静止偏移量|
|`set_min_torsional_patch_radius(radius: float)`|radius|写入最小扭转补丁半径|
|`set_torsional_patch_radius(radius: float)`|radius|写入扭转补丁半径|
|`set_visibility(visible: bool)`|visible|写入可见性状态|
|`set_world_pose()`|—|写入当前世界位姿|

---
## SingleParticleSystem

`SingleParticleSystem` 封装了 PhysX 粒子系统 (流体 / 沙粒 / VFX 粒子)，提供从各向异性控制到碰撞‑休眠参数的完整接口，可对粒子材质、粒子‑粒子/粒子‑网格交互与解算器迭代进行动态配置。

### 一、核心属性

|属性名|含义|
|---|---|
|`prim_path`|粒子系统 Prim 路径|
|`name`|粒子系统名称|
|`particle_system`|低层 PhysX PxParticleSystem 句柄|
|`prim`|USD Prim 句柄|

### 二、方法分类

#### 1. 初始化与有效性

|方法名|说明|
|---|---|
|`initialize()`|构建内部句柄|
|`post_reset()`|场景 reset 后重初始化|
|`is_valid() → bool`|句柄是否有效|

#### 2. 启用／模式切换

|方法名|说明|
|---|---|
|`apply_particle_isotropy()` / `apply_particle_anisotropy()`|切换粒子各向同性 / 各向异性|
|`apply_particle_smoothing()`|开启流体平滑|
|`set_particle_system_enabled(value)`|全局启停粒子系统|

#### 3. 材质与属性

|方法名|说明|
|---|---|
|`apply_particle_material(particle_materials)`|绑定粒子材质|
|`get_applied_particle_material() → material`|读取已用粒子材质|

#### 4. 读取接口（Getters）

|方法名|返回值说明|
|---|---|
|`get_contact_offset()` / `get_rest_offset()`|粒子‑网格接触/静止偏移|
|`get_particle_contact_offset()`|粒子‑粒子接触偏移|
|`get_fluid_rest_offset()` / `get_solid_rest_offset()`|流体 / 固体静止偏移|
|`get_enable_ccd() → bool`|是否启用 CCD|
|`get_global_self_collision_enabled() → bool`|自碰撞是否全局启用|
|`get_max_depenetration_velocity()`|最大去穿透速度|
|`get_max_neighborhood()` / `get_max_velocity()`|最大邻域粒子数 / 速度限制|
|`get_solver_position_iteration_count()`|解算器位置迭代次数|
|`get_wind()`|全局风场向量|
|`get_particle_system_enabled() → bool`|系统是否启用|
|`get_simulation_owner()`|多实例仿真调度归属|

#### 5. 写入接口（Setters）

| 方法名                                                                 | 参数           | 说明          |
| ------------------------------------------------------------------- | ------------ | ----------- |
| `set_contact_offset(value)` / `set_rest_offset(value)`              | value: float | 修改接触/静止偏移   |
| `set_particle_contact_offset(value)`                                | value: float | 修改粒子‑粒子接触偏移 |
| `set_fluid_rest_offset(value)` / `set_solid_rest_offset(value)`     | float        | 流体 / 固体偏移   |
| `set_enable_ccd(value: bool)`                                       | —            | 开关 CCD      |
| `set_global_self_collision_enabled(value: bool)`                    | —            | 开关自碰撞       |
| `set_max_depenetration_velocity(value)` / `set_max_velocity(value)` | float        | 穿透/速度限制     |
| `set_max_neighborhood(value)`                                       | int          | 最大邻域粒子数     |
| `set_solver_position_iteration_count(value)`                        | int          | 修改位置迭代次数    |
| `set_wind(value)`                                                   | Vec3         | 设置风向 / 风速   |
| `set_simulation_owner(value)`                                       | string / int | 指定仿真归属      |
| `set_particle_system_enabled(value: bool)`                          | —            | 启／停粒子系统     |


---

## SingleRigidPrim

`SingleRigidPrim` 封装单一刚体 Prim（Rigid Body）的物理与渲染接口，既可作为静态几何，也可启用刚体动力学。它支持对质量、密度、速度、质心 (CoM) 等惯性属性及睡眠阈值的读写，同时管理可视材质与可见性。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|刚体 Prim 在 USD 场景中的路径|

### 二、类方法

#### 1. 初始化与有效性

|方法名|说明|
|---|---|
|`initialize()`|构建内部句柄|
|`post_reset()`|场景 reset 后重新配置|
|`is_valid() → bool`|检查句柄/Prim 是否有效|
|`is_visual_material_applied() → bool`|可视材质是否已应用|

#### 2. 物理使能 / 停用

|方法名|说明|
|---|---|
|`enable_rigid_body_physics()`|启用刚体动力学|
|`disable_rigid_body_physics()`|停用刚体动力学（转静态）|

#### 3. 材质管理

|方法名|说明|
|---|---|
|`apply_visual_material(visual_material)`|应用可视材质|
|`get_applied_visual_material() → material`|读取当前可视材质|

#### 4. 读取接口（Getters）

|方法名|返回值说明|
|---|---|
|`get_current_dynamic_state() → State`|当前动力学状态|
|`get_default_state() → State`|默认状态|
|`get_mass() → float`|质量|
|`get_density() → float`|密度|
|`get_com() → (position, orientation)`|质心位置与朝向|
|`get_linear_velocity() → Vec3`|线速度|
|`get_angular_velocity() → Vec3`|角速度|
|`get_sleep_threshold() → float`|睡眠阈值|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → Vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → Vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

#### 5. 写入接口（Setters）

| 方法名                                                                | 关键参数       | 说明          |
| ------------------------------------------------------------------ | ---------- | ----------- |
| `set_default_state()`                                              | —          | 保存当前为默认状态   |
| `set_mass(mass)` / `set_density(density)`                          | float      | 改变质量 / 密度   |
| `set_com(position, orientation)`                                   | Vec3, Quat | 设置质心位置/朝向   |
| `set_linear_velocity(velocity)` / `set_angular_velocity(velocity)` | Vec3       | 设置线速度 / 角速度 |
| `set_sleep_threshold(threshold)`                                   | float      | 设置睡眠阈值      |
| `set_local_pose()` / `set_world_pose()`                            | —          | 写入局部 / 世界位姿 |
| `set_local_scale(scale)`                                           | Vec3       | 写入局部缩放      |
| `set_visibility(visible)`                                          | bool       | 开关可见性       |


---
## SingleXFormPrim

`SingleXFormPrim` 封装了 USD 中的 **Xform Prim**（纯变换节点，可作为组或空物体），提供对可视材质与局部/世界变换、可见性的统一接口。常用于将多个子 Prim 编组或作为动画/控制骨骼的空节点。

### 一、类成员属性

|属性名|含义|
|---|---|
|`prim_path`|Xform Prim 在 USD 中的路径|

### 二、类方法

#### 1. 初始化与有效性

|方法名|说明|
|---|---|
|`initialize()`|构建内部句柄|
|`post_reset()`|场景 reset 后重新同步状态|
|`is_valid() → bool`|Prim / 句柄是否合法|
|`is_visual_material_applied() → bool`|可视材质是否已绑定|

#### 2. 材质管理

|方法名|说明|
|---|---|
|`apply_visual_material(visual_material)`|绑定可视材质|
|`get_applied_visual_material() → material`|读取已绑定材质|

#### 3. 读取接口（Getters）

|方法名|返回值说明|
|---|---|
|`get_default_state() → State`|默认状态|
|`get_local_pose() → Pose`|局部位姿|
|`get_local_scale() → Vec3`|局部缩放|
|`get_world_pose() → Pose`|世界位姿|
|`get_world_scale() → Vec3`|世界缩放|
|`get_visibility() → bool`|可见性|

#### 4. 写入接口（Setters）

| 方法名                                     | 关键参数 | 说明          |
| --------------------------------------- | ---- | ----------- |
| `set_default_state()`                   | —    | 保存当前为默认状态   |
| `set_local_pose()` / `set_world_pose()` | —    | 写入局部 / 世界位姿 |
| `set_local_scale(scale)`                | Vec3 | 写入局部缩放      |
| `set_visibility(visible)`               | bool | 切换可见性       |