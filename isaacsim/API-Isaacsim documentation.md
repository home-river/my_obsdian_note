[Isaac Sim: Extensions API — Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/py/index.html)

| 目录分类                 | 模块名称                                            | 用途说明                                                       |
| -------------------- | ----------------------------------------------- | ---------------------------------------------------------- |
| **核心与风格**            | C++ API / Coding Style Guide                    | C++ 接口定义及编码规范，供底层扩展或插件开发参考。                                |
| **应用级扩展**            | isaacsim.app.*                                  | 与主应用（Omni Kit）窗口、启动流程相关的 UI 和设置扩展，如关于窗口、App 选择器、初始化配置等。    |
| **资产管理**             | isaacsim.asset.*                                | 资产浏览、导入（URDF、HeightMap）、生成（传送带、占用网格）及其 UI 辅助工具。            |
| **基准测试**             | isaacsim.benchmark.*                            | 性能测试示例及后台服务，用于评估模拟效率与稳定性。                                  |
| **代码编辑器集成**          | isaacsim.code_editor.*                          | 与 Jupyter Notebook、VS Code 的集成支持，方便在外部编辑器中编写与调试脚本。         |
| **核心功能**             | isaacsim.core.*                                 | 核心运行时 API（场景管理、仿真管理、Prim 操作、通用工具等），是所有高层扩展的基础。             |
| **Cortex 行为框架**      | isaacsim.cortex.*                               | 提供行为树（Behavior）框架及示例，用于构建复杂智能体逻辑。                          |
| **示例与教学**            | isaacsim.examples.*                             | 各类功能示例（浏览器、交互、UI、扩展模板），方便学习与快速上手。                          |
| **GUI 组件**           | isaacsim.gui.*                                  | 通用 UI 组件、菜单与属性面板扩展，可复用于自定义界面开发。                            |
| **场景合成（Replicator）** | isaacsim.replicator.*                           | 合成数据管线：域随机化、行为脚本、场景块、数据记录与写入器，用于生成训练数据。                    |
| **机器人运动与设置**         | isaacsim.robot_motion._, isaacsim.robot_setup._ | Lula 运动规划、装配、增益调谐、抓取编辑、URDF/XRDF 导入与编辑等机器人配置与运动生成工具。       |
| **机器人模块**            | isaacsim.robot.*                                | 各类机器人模型（Manipulators、Wheeled）、抓手、策略示例及其 UI 组件和 USD Schema。 |
| **ROS 集成**           | isaacsim.ros1._, isaacsim.ros2._                | 与 ROS1/ROS2 的桥接、TF 可视化、URDF 导入等，便于与机器人中间件对接。               |
| **传感器仿真**            | isaacsim.sensors.*                              | Camera、PhysX、RTX 等传感器模拟及 UI，支持物理与光线追踪传感器。                  |
| **模拟与存储**            | isaacsim.simulation_app, isaacsim.storage.*     | Kit 应用辅助、场景存储后端（本地 USD 存储）等。                               |
| **测试框架**             | isaacsim.test.*                                 | 单元测试、DocTest 工具，保证代码正确性与文档示例有效性。                           |
| **实用工具**             | isaacsim.util.*                                 | 调试绘制、碰撞检测、相机检查、网格合并、物理属性编辑等通用脚本工具。                         |
| **XR 支持**            | isaacsim.xr.openxr                              | OpenXR 接口支持，用于 VR/AR 交互集成。                                 |
| **其他**               | omni.isaac.dynamic_control, omni.kit.loop-isaac | 动态控制低层 API 与循环运行器，为高性能控制与自定义主循环提供支持。                       |


#### C++ API

|模块|用途说明|
|---|---|
|`C++ API`|Isaac Sim 的底层 C++ 接口定义，供插件或核心扩展使用。|
|`Coding Style Guide`|C++ 代码的风格与规范，保证项目一致性与可维护性。|

#### Extensions (App)

|模块|用途说明|
|---|---|
|`isaacsim.app.about`|“关于”窗口扩展，展示版本与版权信息。|
|`isaacsim.app.selector`|应用选择器，用于在多个 Isaac Sim 体验间切换。|
|`isaacsim.app.setup`|启动与初始化流程扩展，配置默认场景和环境。|

#### Extensions (Asset)

|模块|用途说明|
|---|---|
|`isaacsim.asset.browser`|资产浏览器 UI，查看和筛选 USD、URDF 等资源。|
|`isaacsim.asset.exporter.urdf`|将 USD 场景导出为 URDF 格式的工具。|
|`isaacsim.asset.gen.conveyor`|传送带生成器脚本，快速构建移动平台。|
|`isaacsim.asset.gen.conveyor.ui`|传送带生成器的 UI 面板。|
|`isaacsim.asset.gen.omap`|占用网格（Occupancy Map）生成工具。|
|`isaacsim.asset.gen.omap.ui`|占用网格的 UI 控件扩展。|
|`isaacsim.asset.importer.heightmap`|基于高度图的地形导入扩展。|

#### Extensions (Benchmark)

|模块|用途说明|
|---|---|
|`isaacsim.benchmark.examples`|性能基准测试示例脚本。|
|`isaacsim.benchmark.services`|后台性能测试服务接口。|

#### Extensions (Code Editor)

|模块|用途说明|
|---|---|
|`isaacsim.code_editor.jupyter`|Jupyter Notebook 集成支持。|
|`isaacsim.code_editor.vscode`|VS Code 编辑和调试集成。|

#### Extensions (Core)

|模块|用途说明|
|---|---|
|`isaacsim.core.api`|核心 Python API（世界、场景、仿真管理等）。|
|`isaacsim.core.cloner`|场景对象克隆工具。|
|`isaacsim.core.deprecation_manager`|弃用 API 管理与兼容层。|
|`isaacsim.core.includes`|公共头文件与常量定义。|
|`isaacsim.core.nodes`|OmniGraph 核心节点库。|
|`isaacsim.core.prims`|Prim 操作与封装（Articulation、RigidBody 等）。|
|`isaacsim.core.simulation_manager`|仿真生命周期与步进控制。|
|`isaacsim.core.throttling`|运行速率限制与帧率控制。|
|`isaacsim.core.utils`|通用辅助函数（路径、时间、Mathematics 等）。|
|`isaacsim.core.version`|版本查询与兼容性检查。|

#### Extensions (Cortex)

|模块|用途说明|
|---|---|
|`isaacsim.cortex.behaviors`|行为树示例，用于构建智能体决策逻辑。|
|`isaacsim.cortex.framework`|Isaac Cortex 行为框架核心。|

#### Extensions (Examples)

|模块|用途说明|
|---|---|
|`isaacsim.examples.browser`|示例浏览器 UI，快速查看官方样例。|
|`isaacsim.examples.extension`|扩展模板生成示例。|
|`isaacsim.examples.interactive`|交互式示例脚本集合。|
|`isaacsim.examples.ui`|UI 功能示例。|

#### Extensions (GUI)

|模块|用途说明|
|---|---|
|`isaacsim.gui.components`|通用 UI 组件（按钮、面板、列表等）。|
|`isaacsim.gui.menu`|菜单栏及菜单项扩展。|
|`isaacsim.gui.property`|属性面板增强与自定义控件。|

#### Extensions (Replicator)

|模块|用途说明|
|---|---|
|`isaacsim.replicator.behavior`|场景行为脚本，用于自动化动作序列。|
|`isaacsim.replicator.behavior.ui`|行为脚本的 UI 调度面板。|
|`isaacsim.replicator.domain_randomization`|域随机化工具，生成多样化训练场景。|
|`isaacsim.replicator.examples`|生成式合成示例。|
|`isaacsim.replicator.scene_blox`|可复用场景构建模块（Scene Blox）。|
|`isaacsim.replicator.synthetic_recorder`|合成数据录制器。|
|`isaacsim.replicator.writers`|数据写入器（图像、标签、TF 等）。|

#### Extensions (Robot Motion & Setup)

|模块|用途说明|
|---|---|
|`isaacsim.robot_motion.lula`|LULA 运动规划库。|
|`isaacsim.robot_motion.lula_test_widget`|LULA 测试 UI 控件。|
|`isaacsim.robot_motion.motion_generation`|通用运动生成工具。|
|`isaacsim.robot_setup.assembler`|机器人组件装配器。|
|`isaacsim.robot_setup.gain_tuner`|控制增益调谐面板。|
|`isaacsim.robot_setup.grasp_editor`|抓取编辑器 UI。|
|`isaacsim.robot_setup.import_wizard`|URDF/XRDF 导入向导（α版）。|
|`isaacsim.robot_setup.xrdf_editor`|XRDF 机器人描述编辑器。|

#### Extensions (Robot)

|模块|用途说明|
|---|---|
|`isaacsim.robot.manipulators`|机械臂模型集合与封装。|
|`isaacsim.robot.manipulators.examples`|机械臂示例脚本。|
|`isaacsim.robot.manipulators.ui`|机械臂控制 UI 组件。|
|`isaacsim.robot.policy.examples`|强化学习推理示例。|
|`isaacsim.robot.schema`|Robot USD Schema 定义。|
|`isaacsim.robot.surface_gripper`|表面抓手模型。|
|`isaacsim.robot.surface_gripper.ui`|抓手 UI 组件。|
|`isaacsim.robot.wheeled_robots`|差速轮式移动机器人模型。|
|`isaacsim.robot.wheeled_robots.ui`|轮式机器人 UI 组件。|

#### Extensions (ROS 1 & 2)

|模块|用途说明|
|---|---|
|`isaacsim.ros1.bridge`|ROS 1 与 Isaac Sim 通信桥接。|
|`isaacsim.ros2.bridge`|ROS 2 与 Isaac Sim 通信桥接。|
|`isaacsim.ros2.tf_viewer`|ROS 2 TF 数据可视化。|
|`isaacsim.ros2.urdf`|ROS 2 URDF 导入器。|

#### Extensions (Sensors)

|模块|用途说明|
|---|---|
|`isaacsim.sensors.camera`|相机传感器仿真 API。|
|`isaacsim.sensors.camera.ui`|相机仿真 UI 控件。|
|`isaacsim.sensors.physics`|物理传感器（接触、力）仿真。|
|`isaacsim.sensors.physics.examples`|物理传感器示例。|
|`isaacsim.sensors.physics.ui`|物理传感器 UI 组件。|
|`isaacsim.sensors.physx`|PhysX 物理传感器接口。|
|`isaacsim.sensors.physx.examples`|PhysX 传感器示例。|
|`isaacsim.sensors.physx.ui`|PhysX 传感器 UI 组件。|
|`isaacsim.sensors.rtx`|RTX 传感器（光线追踪）仿真。|
|`isaacsim.sensors.rtx.ui`|RTX 传感器 UI 组件。|

#### Extensions (Simulation)

|模块|用途说明|
|---|---|
|`isaacsim.simulation_app`|Kit 应用级仿真助手与入口。|

#### Extensions (Storage)

|模块|用途说明|
|---|---|
|`isaacsim.storage.native`|本地 USD 场景存储后端。|

#### Extensions (Test)

|模块|用途说明|
|---|---|
|`isaacsim.test.collection`|测试用例集合。|
|`isaacsim.test.docstring`|DocTest 文档测试工具。|

#### Extensions (Util)

|模块|用途说明|
|---|---|
|`isaacsim.util.camera_inspector`|相机属性检查工具。|
|`isaacsim.util.clash_detection`|碰撞/干涉检测工具。|
|`isaacsim.util.debug_draw`|调试绘制接口。|
|`isaacsim.util.merge_mesh`|网格合并工具。|
|`isaacsim.util.physics`|物理属性编辑与调试。|

#### Extensions (XR)

|模块|用途说明|
|---|---|
|`isaacsim.xr.openxr`|OpenXR 支持，用于 VR/AR 交互。|

#### Other Extensions

|模块|用途说明|
|---|---|
|`omni.isaac.dynamic_control`|低层动态控制 API（关节、力度、速度）。|
|`omni.kit.loop-isaac`|自定义主循环与 Isaac 运行器。|



