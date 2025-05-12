## 机器人设置

这些工具和教程详细介绍了如何使用不同的导入和导出工具，将资产转换为 USD（Universal Scene Description）格式，或从 USD 格式转换回来，以及如何在仿真中构建自定义机器人。

#### 工具

- **导入向导 (Import Wizard)**
    
- **导入器与导出器 (Importers & Exporters)**
    
    - URDF
        
    - MJCF
        
    - CAD
        
    - Shapenet（已弃用）
        
- **编辑器工具 (Editor Tools)**
    
    - 合并网格工具 (Merge Mesh Utility)
        
    - 调整关节驱动增益 (Tuning Joint Drive Gains)
        
    - 增益调节扩展 (Gain Tuner Extension)
        
    - 检查器工具 (Inspector Tools)
        
        - 物理检查器 (Physics Inspector)
            
        - 仿真数据可视化工具 (Simulation Data Visualizer)
            
        - 抓取编辑器 (Grasp Editor)
            

#### 格式

- **教程系列 (Tutorial Series)**
    
    - 设置移动机器人 (Rig a Mobile Robot)
        
    - 导入操控臂 (Import a Manipulator)
        
    - 设置闭环结构 (Rig Closed-Loop Structures)
        
    - 组装机器人和刚体 (Assemble Robots And Rigid Bodies)
        

#### 资产优化

- **优化技巧与深度讲解 (Asset Optimization Tips and Deep Dives)**
    
- **机器人设置技巧 (Robot Setup Tips)**



---

## Import Wizard

Isaac Sim 提供了丰富的工具库，用于多种机器人仿真场景，包括数字孪生、远程操作和强化学习。**Isaac Sim Import Wizard**（导入向导）可以帮助你：

- 识别适合导入机器人所需的工具
    
- 为每个工具提供离线使用说明
    
- 按推荐顺序逐步引导你完成整个流程
    

> **注意**  
> 导入向导目前处于 alpha 阶段，仍在测试和开发中。如遇到任何 bug 或有改进建议，请前往 Isaac Sim Developer Resources 分享反馈，帮助我们提升功能和体验。感谢你的参与和耐心。

### Getting Started

1. 在菜单中依次打开 **Window > Extensions**，在搜索框输入 “Import Wizard”，启用该扩展。
    
2. 启用并加载后，依次打开 **Window > Isaac Sim Import Wizard**，欢迎页面将会以新窗口形式打开。
    
3. 在欢迎页面的 **Instruction**（使用说明）区域查看导入向导的使用方法。
    

使用导入向导有四种主要方式：

1. **Questionnaire**
    
    - 点击绿色的 **Do You Need Help Getting Started?** 按钮。
        
    - 回答一系列多选问题后，将推荐一组工具。
        
    - 可将该推荐列表保存为自定义 Pipeline 以便日后使用。
        
2. **已知工具的说明**
    
    - 勾选你想要的工具，再点击 **Next**，逐一查看所选工具的文档和操作步骤。
        
3. **Common Tools**
    
    - 从下拉菜单中选择 **Common Tools**，查看常用工具的使用说明，点击 **Next** 可依次浏览所有工具。
        
4. **Custom Pipeline**
    
    - 如果你已经为工作流选定了一系列工具，并希望每次导入资产时都自动按此顺序执行，可创建自定义 Pipeline。
        
    - 详见 “Customize Pipelines” 部分。
        

每个工具页还包括“Additional Resources”区域，汇总了该工具关联的所有文档页链接，并提供一键打开扩展文件夹的快捷方式，方便查看该扩展所包含的 Python 脚本和数据。

---

## User Interface

Import Wizard 界面主要分为四个部分：

### Welcome Page
[[import  wizard2.jpeg|Open: Pasted image 20250512105503.png]]
![[import  wizard2.jpeg]]

|Ref|Option|功能描述|
|---|---|---|
|1|Open Questionnaire|打开 **Questionnaire**|
|2|Instructions|查看如何使用导入向导|
|3|Menu Location|在菜单栏中导入向导的位置|
|4|Use Custom Pipeline|查看 **Customize Pipelines** 详情|
|5|Available Tools List|列出所有可用工具，按预设顺序展示（非 Isaac Sim 全部工具）|
|6|Advance Tips|展开查看更多提示|
|7|Jump To List|快速访问任意工具|
|8|Available Resources Links|Isaac Sim 文档和论坛链接|
|9|Next|开始执行 Pipeline|

### Tool Instructions Page
[[import  wizard1.jpeg|Open: Pasted image 20250512105515.png]]
![[import  wizard1.jpeg]]

|Ref|Option|功能描述|
|---|---|---|
|1|Name of the Tool|当前查看的工具名称|
|2|Instructions|如何使用该工具的详细说明|
|3|Menu Location|在菜单栏中再次找到该工具的位置|
|4|Advance Tips|展开查看更多提示|
|5|Available Resources Links|相关文档、Python API、示例和扩展文件夹链接|
|6|Next Tool|跳转到 Pipeline 中的下一个工具|
|7|Previous Tool|返回上一个工具|
|8|Done|关闭导入向导|
|9|Jump To List|快速访问其他可用工具|
|10|Start Over|返回 Welcome Page|

### Questionnaire
[[import  wizard.jpeg|Open: Pasted image 20250512105522.png]]
![[import  wizard.jpeg]]
Questionnaire 会通过一系列问题，帮助你筛选出适合的工具。回答左侧问题后，右侧会显示推荐的工具列表。点击任意推荐按钮即可跳转到该工具的说明页面。完成问卷后，可点击 **Save This Pipeline** 将该 Pipeline 保存以备后用。更多自定义 Pipeline 使用方法请参见 “Customize Pipelines” 部分。

### Customize Pipelines

当你经常重复相同步骤时，可自定义 Pipeline 顺序。在导入向导欢迎页勾选 **Use Custom Pipeline** 即可载入已保存的 Pipeline。

- 模板文件位于 `/data/custom_pipeline.json`，格式示例：
    
    json
    
    复制
    
    `{     "Import Pipeline": [         "Import URDF from File",         "Articulation and Physics Inspector",         "Gain Tuner",         "Controller OmniGraphs"     ] }`
    
- 键名为 Pipeline 名称，值为工具名称数组，顺序即导入向导中执行的顺序。
    
- 可在同一文件中定义多个 Pipeline，只要键名不同即可。
    
- 若使用 Questionnaire 生成 Pipeline，可在末尾选择将其追加或覆盖到默认的 `custom_pipeline.json`，或指定新的文件路径。




---

# 导入器与导出器

### URDF

- **URDF Importer Extension**：用于将 URDF 格式的机器人模型导入 Isaac Sim。
    
- **Tutorial: Import URDF**：导入 URDF 的分步教程。
    
- **USD to URDF Exporter**：将场景中的 USD 模型导出为 URDF 格式。
    
- **Tutorial: Export URDF**：导出 URDF 的分步教程。
    

### MJCF

- **MJCF Importer Extension**：用于将 MJCF（MuJoCo XML）格式的机器人模型导入 Isaac Sim。
    
- **Tutorial: Import MJCF**：导入 MJCF 的分步教程。
    

### CAD

- **Onshape importer**：从 Onshape 平台直接拉取并导入 CAD 模型。
    
- **CAD Converter**：在多种 CAD 格式之间进行转换，以生成可用于 Isaac Sim 的 USD 资产。
    

### Shapenet (Deprecated)

- **ShapeNet Importer Tutorial**：介绍如何使用 ShapeNet Importer 的教程（功能已弃用）。
    
- **ShapeNet Importer**：用于从 ShapeNet 库导入模型的扩展（已弃用）。




------
## URDF Importer Extension

### 关于

> **注意**  
> 从 Isaac Sim 2023.1.0 版本开始，URDF 导入器已经开源。源代码及贡献信息可以在我们的 GitHub 仓库中找到。

URDF Importer Extension 用于导入机器人的 URDF 表示。URDF（Unified Robot Description Format，统一机器人描述格式）是一种用于在 ROS 中表示机器人模型的 XML 格式。

要导入 URDF 文件，请在顶部菜单栏点击 **File > Import**。  
该扩展默认启用。如果被禁用，可以通过 Extension Manager 搜索 `isaacsim.asset.importer.urdf` 重新启用该扩展。

### URDF Importer Extension 概述

导入结果会记录在 **Output Log** 中，可以从屏幕底部访问。**Output Log** 会显示导入过程中的任何错误或警告。对于更详细的日志信息，可以直接打开 Isaac Sim 的日志文件，切换控制台模式为 Info，或者使用 `--verbose` 参数启动 Isaac Sim，显示终端输出的日志结果。

### 命名规范

> **警告**  
> 为遵循 USD Prim 名称规范，link 或 joint 名称中的特殊字符不支持，特殊字符会被替换为下划线。如果替换后的名称以下划线开头，会在前面加一个字母“a”。建议直接在 URDF 文件中修改这些名称。

完整的 NVIDIA Isaac Sim 命名规范请参见 Isaac Sim Conventions 文档。

### 导入选项

#### 模型

提供在 Stage 中导入或作为引用模型添加的选项。如果选择 **Create in Stage**，则可选择将其设置为默认 Prim 或在导入时清空 Stage。默认情况下，这两个选项均未选中。

#### 链接

选择 **Moveable base**（可移动底座，例如轮式机器人）或 **Static base**（静态底座，例如 6 自由度机械臂）。

- 如果选择 **Moveable base**，底座链接会被设置为可移动；
    
- 如果选择 **Static base**，底座链接会固定，并附带一个 root_joint。
    
- **Default Density** 用于没有质量值指定的链接。如果密度设置为 0，物理引擎将使用默认值自动计算密度。
    

#### 关节与驱动

提供界面以配置各个关节，加载默认值。

##### 忽略 Mimic

如果选中此项，导入时会忽略 `<mimic>` 标签。否则，带有 mimic 标签的关节将会使用 PhysX Mimic API，与主关节协同工作。

##### 关节配置

可以选择直接通过 **Stiffness**（刚度）或 **Natural Frequency**（自然频率）来配置关节。保存的值始终以刚度表示。

- **Stiffness**：直接编辑关节驱动的刚度和阻尼。
    
- **Natural Frequency**：根据所需的自然频率计算关节驱动的刚度和阻尼比，公式如下：
    
    $\text { 刚度 }=(2 \pi f)^{2} \times I \quad \text { 阻尼 }=2 r \sqrt{\text { 刚度 } \times I}​$
    
    其中 f 为自然频率，r 为阻尼比，I 为关节的等效转动惯量。阻尼比 r=1.0 表示临界阻尼，r<1.0 为欠阻尼，r>1.0 为过阻尼。
    

刚度值用于控制位置驱动的强度，刚度和阻尼结合应用时有助于减少振动。

##### 多重编辑

要同时编辑多个关节，可以使用 Ctrl+点击选择单个关节，或使用 Shift+点击选择一系列关节。选中后，修改的值将应用到所有选中的关节。

#### 驱动类型

驱动类型可选 **Acceleration**（加速度）或 **Force**（力）。

- **Acceleration** 驱动在施加力之前归一化惯性，使其不受机器人质量变化的影响，等同于理想的阻尼执行器。
    
- **Force** 驱动直接施加力矩或力，类似弹簧-阻尼系统。
    

#### 目标

可选 **None**（无）、**Position**（位置）、**Velocity**（速度）。

- 如果驱动类型设置为位置，目标将是旋转关节的弧度值，或是直线关节的距离单位。
    
- 如果驱动类型设置为速度，目标则为每秒的单位。
    
- 如果关节配置为 Mimic，则无法更改目标类型。
    

#### 碰撞体

- **Collision From Visuals**：如果选中此项，若未提供碰撞体，将从可视网格生成碰撞体；否则，不会为该链接创建碰撞体。
    
- **Collider Type**：选择 **Convex Hull**（凸包）或 **Convex Decomposition**（凸分解）。
    
    - **Convex Hull** 将为碰撞网格创建一个凸包。
        
    - **Convex Decomposition** 将为碰撞网格创建多个凸包，更加符合可视化资产的形状。
        
- **Allow self-collision**：允许相邻链接之间发生自碰撞。如果碰撞网格在关节处相交，可能导致物理不稳定。
    
- **Replace Cylinders with Capsules**：选中时，圆柱体碰撞体将被替换为胶囊体原语。
    

> **注意**  
> 除非确认机器人各链接之间没有自碰撞，否则建议将 **Allow self-collision** 设置为 false。  
> 您必须拥有写入权限到用于导入的输出目录。默认情况下，导入目录为当前打开的 Stage，请根据需要更改此目录。

### 从 ROS2 节点导入 URDF

启用扩展 `isaacsim.ros2.urdf` 后，可启用此功能。该功能将打开一个独立的 URDF 导入器界面，允许您定义包含机器人描述的 ROS2 节点。

在 **Node** 文本框中输入节点名称。如果修改了导入设置或发布的节点，点击 **Refresh** 更新节点。

> **注意**  
> 此功能仅在启用 ROS2 桥接时可用。  
> 详细的使用教程请参见 **Import from ROS2 Node Tutorial**。

### 机器人属性

可能有许多属性需要调整，这些属性可能分布在多个不同的 Schema 和 API 中。获取和设置参数的一般步骤如下：

1. 查找参数所属的 API。最常见的 API 可以在 Pixar USD API 文档中找到。
    
2. 获取该 API 应用的 Prim 句柄。例如，Articulation 和 Drive API 应用于关节，Mass API 应用于刚体。
    
3. 获取 API 句柄后，可以通过 Get 或 Set 方法修改相关属性。
    

例如，如果我们要设置车轮的驱动速度和执行器的刚度：

```python
# 获取左右车轮的 DriveAPI 
left_wheel_drive = UsdPhysics.DriveAPI.Get(stage.GetPrimAtPath("/carter/chassis_link/left_wheel"), "angular") right_wheel_drive = UsdPhysics.DriveAPI.Get(stage.GetPrimAtPath("/carter/chassis_link/right_wheel"), "angular")  # 设置速度驱动目标（度/秒） 
left_wheel_drive.GetTargetVelocityAttr().Set(150) right_wheel_drive.GetTargetVelocityAttr().Set(150)  
# 设置驱动阻尼，控制速度驱动的强度 
left_wheel_drive.GetDampingAttr().Set(15000) right_wheel_drive.GetDampingAttr().Set(15000)  
# 设置驱动刚度，控制位置驱动的强度 
# 在此示例中，由于采用速度控制，刚度应设置为 0 
left_wheel_drive.GetStiffnessAttr().Set(0) right_wheel_drive.GetStiffnessAttr().Set(0)
```

另外，您还可以使用 **Omniverse Commands Tool Extension** 来更改 UI 中的值，并获取与之对应的 Omniverse 命令。

> **注意**
> 
> - 使用位置控制时，必须设置 **drive stiffness**。
>     
> - 使用速度控制时，必须设置 **drive damping**。
>     
> - 同时设置刚度和阻尼有助于减少位置控制中的振动。

## 自定义 Isaac Sim URDF 属性和标签

### sensor.isaac_sim_config

该属性用于传感器标签中，为传感器提供 Isaac Sim 配置。它有两种使用方式：一是使用 Isaac Sim 提供的预配置激光雷达，二是使用用户定义的配置。对于用户定义的配置，需要提供配置 JSON 的位置，否则提供预配置激光雷达的名称。测试文件中提供了一个示例配置文件，位于 `data/lidar_sensor_template/lidar_sensor_template.json`。

> **注意**  
> 当使用自定义激光雷达配置时，导入器会尝试在 `isaacsim.sensors.rtx` 文件夹中创建符号链接。如果在 Windows 上遇到错误代码：1314，请尝试以管理员权限运行 Isaac Sim，或手动创建符号链接。如果出现错误代码：183，说明符号链接已存在，请检查并在必要时手动替换。

#### 示例



```xml
<robot>
    <link name="root_link"/>
    <joint name="root_to_base" type="fixed">
        <parent link="root_link"/>
        <child link="link_1"/>
    </joint>
    <link name="link_1"/>

    <sensor name="custom_lidar" type="ray" update_rate="30" isaac_sim_config="../lidar_sensor_template/lidar_template.json">
        <parent link="link_1"/>
        <origin xyz="0.5 0.5 0" rpy="0 0 0"/>
    </sensor>

    <sensor name="preconfigured_lidar" type="ray" update_rate="30" isaac_sim_config="Velodyne_VLS128">
        <parent link="link_1"/>
        <origin xyz="0.5 1.5 0" rpy="0 0 0"/>
    </sensor>
</robot>
```

### loop_joint

定义一个关节用于闭合运动学链环。这对于具有闭环运动学链的机器人非常有用，例如四足机器人在髋部的环形关节。该环形关节在 URDF 中定义如下：

```xml
<loop_joint name="loop_joint_name" type="spherical">
    <link1 link="link_1" rpy="0 0 0" xyz="0 0 0"/>
    <link2 link="link_2" rpy="0 0 0" xyz="0 0 0"/>
</loop_joint>

```
### fixed_frame

固定框架用于定义附加到链接的参考点。它用于定义参考点（例如传感器位置或末端执行器偏移），而无需使用 link 标签。固定框架在 URDF 中定义如下：

```xml
<fixed_frame name="frame_0">
    <parent link="link_1"/>
    <origin rpy="0.0 0.0 0.0" xyz="1.00 -0.020 0.10"/>
</fixed_frame>
```

固定框架必须有唯一的名称和父链接对。

### 资产结构

Isaac Sim 导入的资产按特定结构组织，以便于管理、重用和仿真。每个资产都拆分为多个组件，可按以下类别进行组织：

#### 资产结构图
[[资产结构图.jpeg|Open: Pasted image 20250512111205.png]]
![[资产结构图.jpeg]]
要查看一个示例资产，参见 Isaac Sim 资产中的 Nova Carter：**Robots/NVIDIA/Carter/nova_carter/**。

#### 资产源

此阶段的资产表示从原始文件格式导入的原始形式。它们通常按以下方式组织：

- **Base Asset (asset_base.usd)**：包含资产的完整结构层次，例如机器人装配。
    
- **Parts (parts.usd)**：包括单个组件，每个网格一个 USD 文件。此模块化拆分确保了访问和管理的便捷。
    
- **Materials (materials.usd)**：资产所使用的物理基础渲染（PBR）材质集合。
    

**指南**：

- 源资产应保持不变，以确保它们可以无缝地重新导入，而不丢失下游修改。
    
- 一致性至关重要：结构层次、命名规范和部件装配必须保持完整。
    

#### 变换

此阶段准备资产以供仿真使用，重新组织并优化它。此变换在源资产包含嵌套刚体或复杂结构时是必要的，因为它不符合仿真要求。该结构必须展平，刚体应组织成简单列表，网格应简化以减少总数。变换过程包括：

- **重组结构**：创建仿真结构（例如，根据需要分离视觉和碰撞体）。
    
- **优化网格**：合并将作为单个刚体的网格。简化材质列表为单一视觉材质列表，清理并格式化网格作为可实例化参考，以提高性能。
    

> **注意**  
> 如果源资产已经是适合仿真的格式，可以跳过此步骤或其中的部分步骤。

#### 特性

在此阶段，添加仿真特性，每个特性都是在变换后的资产上叠加的轻量级层。特性包括但不限于物理设置、传感器配置和控制图。

**添加/修改特性的工作流程**：

1. 创建一个新的空阶段或打开现有的特性阶段。
    
2. 将优化后的资产（asset_sim_optimized.usd）作为子层添加。
    
3. 修改根层以添加/修改特性。
    
4. 在保存之前，从阶段构成中移除或禁用子层（优化后的资产）。
    
5. 将特性作为负载添加到最终资产中。可选地，配置一个变体集来通过列表快速切换不同的特性集。
    

#### 示例特性：

- **Physics (asset_physics.usd)**：添加刚体、碰撞体、关节和关节。
    
- **Sensors (asset_sensors.usd)**：定义传感器规格。
    
- **Control Graphs (asset_control.usd)**：为仿真添加控制特性。
    
- **ROS Integration (asset_ros.usd)**：配置 ROS Omnigraph 功能。
    

> **注意**  
> 物理特性是一个例外，它作为对默认 prim 的引用添加，而其他特性作为负载添加，并保持与优化资产的层连接。

### 最终资产的构成

最终的组合资产由 **asset.usd** 文件表示，整合了所有仿真所需的组件。通过以下构成过程实现：

- **子层**：基础或优化资产（asset_sim_optimized.usd）作为子层提供核心结构和视觉元素。
    
- **负载**：像传感器（asset_sensors.usd）和控制图（asset_control.usd）这样的特性作为负载动态加载。这样可以灵活有效地加载组件。
    
- **引用**：物理设置（asset_physics.usd）作为对默认 prim 的引用添加，确保一致的仿真准备配置。
    
- **变体**：可以在 asset.usd 文件中配置变体，以启用不同的特性集，例如替代传感器配置或控制设置，而无需重复资产。
    

这种模块化方法确保了最终资产文件既轻量又灵活，易于适应不同的仿真场景。

为了保持资产的组织性和可维护性，建议遵循上述结构和指南。这将帮助简化资产创建过程并提高仿真性能。

此外，建议将资产按文件夹组织，源资产单独放入一个文件夹，所有特性放入特性文件夹，最终资产保存到根文件夹。默认情况下，Isaac Sim 机器人导入器遵循此结构。

### 关键定义和注意事项

- **附加组件（Add-ons）**：作为临时子层使用的特性，创建特性时使用这些子层，保存特性资产时会断开子层连接。
    
- **负载（Payloads）**：动态加载的组件，有助于减少内存开销并改善模块化。
    

#### 示例

有关使用示例，请参见 **Tutorial: Import URDF**。