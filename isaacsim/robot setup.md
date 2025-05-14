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


---

# Editor Tools

## 合并网格工具（Merge Mesh Utility）

### 关于

合并网格工具扩展（Merge Mesh Utility Extension）用于将多个 prim 合并为一个单一的网格。当被合并的网格包含多种材质时，会自动使用几何子集（Geometry Subsets）来管理不同的材质。

要访问此扩展，请在顶部菜单栏选择：  
**Tools > Robotics > Asset Editors > Mesh Merge Tool**。

该扩展默认启用。如果被禁用，可以在扩展管理器（Extension Manager）中搜索 `isaacsim.util.merge_mesh` 重新启用。

---

### 用户界面

#### 合并网格工具界面
[[4de90cf105b833c3c9763ac59f7677a4_MD5.jpeg|Open: Pasted image 20250514142114.png]]
![[4de90cf105b833c3c9763ac59f7677a4_MD5.jpeg]]

---

### 配置选项

***输入（Input）

- **Source Prim**  
    显示当前选中用于合并的 prim。该文本框会根据场景中的选择自动填充。第一个被选中的 prim 会作为合并后资产的基准和原点。
    
- **Submeshes**  
    所选 prim 包含的网格数量。
    
- **Geometry Subsets**  
    所选 prim 包含的几何子集数量。
    
- **Materials**  
    所选 prim 使用的唯一材质数量。
    

***note

`提示：如需更改网格的原点，可以先选择一个处于期望原点姿态的空 Xform，然后再选择所有需要合并的网格。`

#### 输出（Output）

- **Destination Prim**  
    合并后网格的自动生成输出路径。
    
- **Geometry Subsets**  
    合并后创建的几何子集数量。每种唯一材质会在最终合并网格上生成一个子集。
    

---

#### 合并选项

- **Clear Parent Transform**  
    选中时，合并后网格的变换会位于世界原点；否则，保持与源 prim 相同的变换。
    
- **Deactivate source assets**  
    选中时，被合并的 prim 会被设置为“非激活”（实际上被删除，但可后续重新激活）。
    
- **Combine Materials**  
    选中时，需要提供 Looks 文件夹的 Prim。所有使用同名材质的网格会被合并为一个 geomsubset，并将该材质移动到指定的 Looks 文件夹。这对于 Onshape 和 CAD 导入的资产非常有用，这些资产通常包含内部 Looks Scope，作为材质 USD 层的子层。
    

---

### 教程与示例

以下示例展示了如何最佳地使用此扩展：

[Merge Mesh Utility — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/robot_setup/ext_isaacsim_util_merge_mesh.html)

---

## 调整关节驱动增益（Tuning Joint Drive Gains）

### 学习目标

在本教程中，我们将学习如何调整机器人关节的参数，使其表现符合预期。在开始本教程之前，建议先完成《导入 URDF 模型》教程。

通过“添加控制器（Adding a Controller）”，你也可以直接发送力控制命令并实现自定义的关节驱动逻辑，但这超出了本教程的范围。

---

### 理解关节驱动（Joint Drives）

关节驱动是一种**双比例控制器（dual-proportional controller）**，用于将关节设定到目标位置。其中一个比例增益调节位置误差，另一个增益调节速度误差。由于历史原因，这两个增益被称为：

- **Stiffness（刚度）**：位置误差的增益；
    
- **Damping（阻尼）**：速度误差的增益。
    

> 💡 注意  
> 这些关节驱动是**隐式的** —— 意味着位置和速度约束是相对于当前时间步直接施加的，这与传统工程中基于闭环控制（使用上一时间步的反馈值）的方式不同。详见 [Articulation Joint Drives](https://docs.omniverse.nvidia.com/isaacsim/latest) 相关章节。

- 刚度可以理解为一个弹簧常数，乘以位置误差，模拟弹簧拉伸后产生的力。
    
- 阻尼来源于对零速度的目标设定 —— 任何移动都会引发“阻止运动”的反应。但其实目标速度可以是任意值，作用方式类似于位置控制中的刚度。
    

#### 控制力的计算公式：

$$
\tau = \text{stiffness} \cdot (q - q_{\text{target}}) + \text{damping} \cdot (\dot{q} - \dot{q}_{\text{target}})
$$

其中：

- $q$：当前关节位置；
    
- $\dot{q}$：当前关节速度；
    
- $q_{\text{target}} 和 {q}_{\text{target}}$：目标位置和速度。
    

当 $\dot{q}_{\text{target}} = 0$ 时，这个控制器退化为常规的 PD 控制器。

适用于旋转（revolute）关节和直线（prismatic）关节。

关节的最大力（max force）将对 τ\tau 进行限制（clamp）。此外，驱动模式（drive type）将决定施加的力是直接作用为力/力矩，还是转换为加速度。

---

### 驱动模式（Drive Modes）

这种双比例控制器主要有两种控制方式：

- **位置控制（Position Target）**：用于通过设定目标距离/角度来驱动关节；
    
- **速度控制（Velocity Target）**：多用于轮子或其他自由旋转的物体。
    

#### 配置方式：

- 若希望使用**位置控制**：设置 `Stiffness > 0`，`Damping` 为任意值；
    
- 若希望使用**速度控制**：设置 `Stiffness = 0`，`Damping > 0`。
    

---

### 增益调整（Gain Tuning）

随意设置增益可能会导致机器行为异常：

- 刚度/阻尼过低：可能不足以克服惯性，目标值与实际偏差大；
    
- 刚度过高：可能导致超调和震荡。
    

#### 位置控制建议步骤：

1. **每个关节单独调整**：
    
    - 初始设置：`Damping = 0`，仅调整 `Stiffness`；
        
    - 增加 `Stiffness` 直到关节能够靠近目标位置；
        
    - 然后将 `Stiffness` 减小一个数量级；
        
    - 添加 `Damping`，初始值为 `Stiffness` 的 1/10；
        
    - 若需更快响应，可进一步减小阻尼；
        
    - 最后微调两者，权衡稳定性、响应时间与超调。
        

> ⚠ 注意：具体调整过程会因机器人结构及控制系统不同而有所差异。

#### 重力补偿（可选）：

如果需要模拟重力补偿控制，在属性面板中选中机器人所有刚体，勾选 **Disable Gravity**。

---

### 速度限制与工业机器人

多数工业机器人出厂时就已调好 PD 控制器，可实现精准位置控制，且速度限制明确。

为了模拟这种行为，可以：

1. 将先前调整好的 `Stiffness` 值增加一倍；
    
2. 在属性面板中，设置关节的最大速度：
    
    - 路径：`Joint -> Advanced -> Maximum Joint velocity`；
        
3. 运行仿真，检查速度是否达到规范要求；
    
4. 若速度仍超出范围，不建议无限提高刚度，应保持在合理范围微调。
    

---

### 速度控制建议步骤：

1. **每个关节单独调整**：
    
    - 设置 `Stiffness = 0`，仅调整 `Damping`；
        
    - 增加 `Damping`，直到接近目标速度；
        
    - 若考虑负载变化，可增加约 10% 的阻尼；
        
    - 可设置关节的最大速度/力限制控制输出范围。
        

---

### 自主尝试（Try it yourself）

你可以导入任意机器人模型，尝试调整关节驱动参数。为了更清晰地测试增益建议：

- 构建一个简单的 **直线关节连接到固定基座** 的刚体；
    
- 设定刚体质量；
    
- 调整驱动参数使其：
    
    - 快速移动到目标位置；
        
    - 且超调控制在目标的 **1%** 以内。
        

---

### 工具推荐：**增益调节器扩展（Gain Tuner Extension）**

该工具允许你可视化不同增益设置下的响应效果，便于更直观地优化控制策略。

---

### Gain Tuner 扩展

#### 关于

Gain Tuner 扩展用于调节所选关节结构（Articulation）的刚度与阻尼增益。当导入新机器人或需要微调现有机器人的增益时，该扩展非常有用。


> [!注意] 注意
> 模拟器必须至少运行过一帧才能检查所选的关节结构。如有疑问，请点击播放按钮切换运行状态

该扩展默认处于启用状态。如果曾被禁用，可在 Extension Manager 中搜索 `isaacsim.robot_setup.gain_tuner` 重新启用。

要访问该扩展，请在顶部菜单栏点击 **Tools > Robotics > Asset Editors > Gain Tuner**。
[[c49ba58c8d91323d7acedd17a91ce35f_MD5.jpeg|Open: Pasted image 20250514143709.png]]
![[c49ba58c8d91323d7acedd17a91ce35f_MD5.jpeg]]

---
### Gain Tuner 概览

#### 用户指南

##### 概述

Gain Tuner 的目的是为每个机器人关节找到一组刚度与阻尼增益，使其能够以极小误差跟随指令轨迹。Gain Tuner 提供一组参数化测试，让用户可以快速评估当前增益的效果，同时也提供了手动调节工具。本教程将带领用户了解以下内容：

- **正弦增益测试（Sinusoidal Gains Test）**：对每个关节发送持续的正弦轨迹，轨迹在关节最大速度和加速度范围内。
    
- **阶跃函数测试（Step Function Test）**：发送不连续的关节目标位置，检查机器人行为是否合理。
    
- **增益调节选项（Options For Tuning Gains）**：用户用于调整增益的不同方式。
    
- **增益测试设置（Gains Test Settings）**：增益测试的用户参数，用于全面评估关节结构增益对行为的影响。
    

---

### 正弦增益测试

正弦增益测试对每个机器人关节发送位置和速度都连续、一致的正弦轨迹。即：指令速度的积分等于指令位置。测试输出为每个关节在位置与速度跟踪方面的均方根误差（RMSE）。测试过程中会绘制每个关节的位置和速度随时间的变化图。

该测试可作为机器人关节能够接受的任意连续轨迹的代理测试。在发送连续轨迹时，有几个合理的限制：

- 指令速度与位置应保持一致（速度积分应得位置）。
    
- 指令速度不得超过关节的最大速度和加速度限制。
    
- 指令位置不应超过位置限制，即任何情况下都不能在非零速度下撞击位置极限。
    

“增益测试设置”中的参数允许用户在上述限制内详细探索关节结构的行为。

以下图示展示了 UR10 机器人在不同增益下进行正弦增益测试的情况。第一张图中增益调节不当，结果图中显示了明显的跟踪误差。第二张图则显示了点击 “SET STIFF GAINS” 后的完美跟踪表现。

- **增益调节差的正弦测试结果**
    [[36408b94389ff82e16e881a4b8af6170_MD5.jpeg|Open: Pasted image 20250514144213.png]]
![[36408b94389ff82e16e881a4b8af6170_MD5.jpeg]]
- **高刚度增益下的正弦测试结果**
    [[63aa5a348f5cadf87992e09d8751d336_MD5.jpeg|Open: Pasted image 20250514144216.png]]
![[63aa5a348f5cadf87992e09d8751d336_MD5.jpeg]]

---

### 阶跃函数测试

阶跃函数测试向关节发送不连续的位置指令，同时速度设为零。通常这种控制方式并不适用于实际场景，理想情况下机器人的指令应始终保持位置与速度的一致性。该测试主要作为一个合理性验证手段。

理想情况下，当机器人接收到一个不连续的位置目标时，每个关节应迅速达到最大速度，其位置变化曲线应接近线性。位置指令的持续时间根据关节的最大速度确定，也就是说：位置目标会保持略长于以最大速度完成该动作所需的时间。

以下图示展示了 UR10 机器人在不同增益下的阶跃函数测试。第一张图中的增益较差，关节未能在时间限制内到达目标位置。第二张图中采用了高刚度增益，每个关节能迅速达到最大速度并完成动作。

---

### 增益调节选项

用户会发现，有很多不同的增益组合都能实现近乎完美的位置与速度跟踪效果。一种便捷的方法是点击 “**SET STIFF GAINS**” 按钮。这会将每个关节的刚度设置为 1×10151×10^{15}，阻尼设置为 1×1051×10^5。

对于工业机器人手臂而言，设定高刚度增益是非常合理的近似，因为这意味着关节能准确响应指令，在与环境发生碰撞时也不会产生柔顺行为。但对于某些机器人操作臂而言，某些关节的柔顺性可能非常重要。因此在这类情况下需要手动调节增益以适应具体用途。

在 Gain Tuner 中，点击 “**Tune Gains -> Hand Tune Gains**” 可以逐个关节调节增益。增益采用对数尺度表示。例如，值为 4.3 表示增益为 104.3≈2×10410^{4.3} ≈ 2×10^4。可以单独修改每个关节，也可以通过面板顶部的 “**Stiffness Multiplier**” 和 “**Damping Multiplier**” 统一调节所有关节的比例。
[[be19a9861d9524ace981a807c4628d0f_MD5.jpeg|Open: Pasted image 20250514144439.png]]
![[be19a9861d9524ace981a807c4628d0f_MD5.jpeg]]

---

### 增益测试设置

该部分允许用户修改多种增益测试参数，以全面探索机器人在不同增益配置下的行为。某些参数是互相关联的，即修改一个参数可能会自动调整另一个。

- **Test Duration（测试时长）**：每个增益测试的持续时间（单位：秒）。若机器人动作较慢（最大速度较低），可能需要延长测试时间以完整观察一次周期。
    
- **Joint Range Maximum（关节最大范围）**：在测试中每个关节允许的最大移动范围，默认为 2π。某些模型的关节可转动多圈，可能导致测试周期过长，可调整此参数。
    
- **Joint Range Used（关节使用比例）**：在最大范围限制基础上再乘以一个比例因子，默认值为 0.9，表示测试不会接近极限的 10%。如需测试极限附近的行为，可调高此值。
    
- **Initial Position Impulse（初始位置冲击）**：为关节初始位置添加偏移量 cc，用于测试位置指令突变的响应能力。小幅冲击应能迅速稳定。


	
    [[3c5f3c029a82b0d604f40dbc14d89a56_MD5.jpeg|Open: Pasted image 20250514144714.png]]
![[3c5f3c029a82b0d604f40dbc14d89a56_MD5.jpeg]]
							**initial Position Impulse of 3.0**

- **Initial Velocity Impulse（初始速度冲击）**：为关节初始速度添加偏移量 cc，测试速度突变时的稳定性。
    [[5c8cb8f699c2b393681d6a74b24b44fd_MD5.jpeg|Open: Pasted image 20250514144853.png]]
![[5c8cb8f699c2b393681d6a74b24b44fd_MD5.jpeg]]
							**Initial Velocity Impulse of 3.0**

- **Max Velocity（最大速度）**：在正弦测试中达到的最大速度。受关节移动范围与周期的限制（例如：降低最大速度会延长周期，反之亦然）。此外也受到关节最大加速度和最大速度的约束。
    
- **Period（周期）**：正弦指令的周期。与最大速度和移动范围相关，周期不能低于一定下限。可调节周期以观察关节之间的科氏效应（Coriolis effects）。
    
- **Include Joint In Gains Test（是否参与测试）**：可将某个关节从测试中排除，使其在测试过程中保持静止。该功能有助于确认其他关节移动时其仍能维持稳定。也可配合初始冲击参数测试收敛行为。
    

---

### 进一步学习

“[**Tuning Joint Drive Gains](https://docs.isaacsim.omniverse.nvidia.com/latest/robot_setup/joint_tuning.html#isaac-sim-app-tutorial-advanced-joint-tuning)**” 教程将更详细地解释增益与运动响应之间的物理关系。

---

好的！我会结合你提供的网页内容，对翻译进行修订和补充，确保内容准确、结构清晰，并尽量还原官方文档的风格。以下是修订后的中文版：

---

# 抓取编辑器（Grasp Editor）

## 学习目标

本教程将介绍如何在 NVIDIA Isaac Sim 中使用 Grasp Editor 扩展，手动创建并仿真特定夹爪/物体对的抓取动作。这些抓取动作会被存储在 isaac_grasp 格式的 YAML 文件中，可与运动生成算法结合使用，实现夹爪的精准抓取。

---

## 入门

要开始使用 Grasp Editor 扩展，你需要在 Isaac Sim 中准备好相关资源：

- **具备抓取能力的 Articulation**：可以是独立的夹爪，也可以是安装在机械臂上的夹爪。
    
- **待抓取物体的 USD 文件**。
    
- **明确夹爪和物体的 USD 坐标系（frame）**：通常选取物体网格中心和夹爪基座的 frame。
    

你可以[下载本教程使用的 stage](https://docs.isaacsim.omniverse.nvidia.com/latest/robot_setup/grasp_editor.html#download-the-tutorial-stage)并跟随操作。

---

## 什么是 Isaac Grasp 文件？

Grasp Editor 扩展的输出是 isaac_grasp 格式的 YAML 文件。每个文件存储一组针对特定夹爪/物体对的抓取动作。文件结构如下：

```yaml
format: isaac_grasp
format_version: 1.0

object_frame_link: /World/mug
gripper_frame_link: /World/panda_hand

grasps:
  grasp_0:
      confidence: 1.0
      position: [-0.04346, 0.06759, 0.19895]
      orientation: {w: 0.00332, xyz: [0.98453, 0.16837, 0.04837]}
      cspace_position:
        panda_finger_joint1: 0.00943
      pregrasp_cspace_position:
        panda_finger_joint1: 0.04
```

isaac_grasp 文件不仅可以通过 Grasp Editor 创建，也可以导入外部已编写的抓取动作进行可视化和验证。

每个抓取动作由夹爪和物体的相对位置定义。为确保相对位置有意义，需为夹爪和物体选择代表性的 frame。Grasp Editor 会将这些 frame 的 USD 路径写入 object_frame_link 和 gripper_frame_link 字段。由于 isaac_grasp 文件也可能在外部生成（甚至不依赖 USD），Grasp Editor 在导入时会忽略这两个字段，用户需自行确保 frame 的正确性。

每个抓取动作（如 grasp_0）包含以下字段：

- **confidence**：抓取动作的置信度。
    
- **position**：夹爪 frame 相对于物体 frame 的平移。
    
- **orientation**：夹爪 frame 相对于物体 frame 的姿态（四元数）。
    
- **cspace_position**：夹爪各关节的抓取时关节位置。
    
- **pregrasp_cspace_position**：夹爪各关节张开时的位置。
    

实际应用时，先将夹爪移动到 pregrasp_cspace_position 并对准 position 和 orientation，然后闭合夹爪至 cspace_position。如果已知物体在世界坐标系下的位置和姿态，夹爪的目标位置和姿态可通过相对变换计算得到。

---


# 抓取编辑器

---

## 学习目标

本教程说明如何在 NVIDIA Isaac Sim 中使用抓取编辑器（Grasp Editor）扩展，为特定夹爪/物体组合手动编写并仿真抓取。生成的抓取信息会存储在 isaac_grasp 格式的 YAML 文件中，可与运动生成算法配合使用，以移动夹爪至目标位置并抓取指定物体。

---

## 快速开始

要开始使用抓取编辑器扩展，需在 Isaac Sim 中准备以下资源：

1. **可抓取的 Articulation**
    
    - 可以是独立的浮动夹爪，也可以是连接在机械臂末端的夹爪。
        
2. **物体的 USD 模型**
    
    - 需要一个 USD 格式的物体资源。
        
3. **参考帧**
    
    - 为夹爪和物体各自指定一个 USD 参考帧，通常选在物体网格中心和夹爪基座处。
        

> 您可 [在此下载](https://chatai.qqyunsd.com/c/682444ad-1a00-800b-9620-3fe3fd923d1c#) 本教程用到的示例场景并跟随操作。

---

## 什么是 Isaac Grasp 文件？

抓取编辑器输出一个遵循 `isaac_grasp` 格式的 YAML 文件。单个文件中存储某一夹爪/物体组合的多个抓取条目。其基本结构如下：

```yaml
format: isaac_grasp
format_version: 1.0

object_frame_link: /World/mug
gripper_frame_link: /World/panda_hand

grasps:
  grasp_0:
    confidence: 1.0
    position: [-0.04346, 0.06759, 0.19895]
    orientation: {w: 0.00332, xyz: [0.98453, 0.16837, 0.04837]}
    cspace_position:
      panda_finger_joint1: 0.00943
    pregrasp_cspace_position:
      panda_finger_joint1: 0.04
```

isaac_grasp 文件并不需要源自 Grasp Editor 扩展。Grasp Editor 扩展的作用在于：既可用于创建 isaac_grasp 文件，也可用于将其他渠道（非 USD）创建的抓取数据导入以进行可视化和验证。

一个抓取（grasp）的定义基于夹爪与物体之间的相对位姿。为了使该相对位姿具有意义，需要为夹爪和物体各自选定一个代表性参考帧。Grasp Editor 会将这些参考帧的 USD 路径写入 isaac_grasp 文件中的 `object_frame_link` 和 `gripper_frame_link` 字段。但由于 isaac_grasp 文件也可能在完全不经过 USD 的情况下由外部工具生成，Grasp Editor 在导入时会忽略这两个字段。因此，使用 Grasp Editor 导入外部抓取时，用户需自行识别正确的 USD 参考帧。

在 isaac_grasp 文件中，每个抓取都拥有一个唯一名称（例如 `grasp_0`）。该名称下包含的字段有：

- **confidence**：描述抓取质量的参数。
    
- **position**：夹爪参考帧相对于物体参考帧的平移向量。
    
- **orientation**：夹爪参考帧相对于物体参考帧的旋转四元数或旋转矩阵。
    
- **cspace_position**：一个字典，列出所有用于控制夹爪的关节的目标位置（抓取闭合时的关节状态）。
    
- **pregrasp_cspace_position**：一个字典，列出所有用于控制夹爪的关节的预抓取位置（夹爪张开时的关节状态）。
    

综合而言，实际执行抓取时可按如下步骤进行：

1. 将夹爪置于 `pregrasp_cspace_position` 所定义的张开状态；
    
2. 将夹爪移动到相对于物体的正确位置和姿态；
    
3. 关闭夹爪，直到各关节达到 `cspace_position` 的闭合状态。
    

若物体在世界坐标系中的位置与姿态分别由 $T_o$ , $R_o$ 给出，而抓取文件中的 `position` 和 `orientation` 字段定义了相对于物体坐标系的变换$\sigma T_g$, $\sigma R_g$，则夹爪在世界坐标系中的目标平移 $T_g$ 和旋转 $R_g$ 可由下式计算：


$$
\begin{aligned} T_g &= R_o \,\sigma T_g + T_o, \\ R_g &= R_o \,\sigma R_g. \end{aligned}
$$
---

### 使用 Grasp Editor

### 选择帧

Grasp Editor 是一个基于 UI 的扩展，可用于创建和导入 `isaac_grasp` 文件。在 NVIDIA Isaac Sim 中，Grasp Editor 位于工具栏的 **Isaac Utils → Grasp Editor**。第一步是在场景中添加一个关节体    （`Articulation`）和一个物体。关节体可以是独立的手爪，也可以是附着在机械臂上的手爪；物体则需是任何带有网格的非关节体。

**Grasp Editor 选择帧界面**  
[[33ee07bdf360b95bf8bd8197b4d4b211_MD5.jpeg|Open: Pasted image 20250514153526.png]]
![[33ee07bdf360b95bf8bd8197b4d4b211_MD5.jpeg]]
在“选择帧”面板中，选中要操作的关节体和物体。你可以在场景大纲中右键点击目标 prim，然后选择“Copy Prim Path”来复制物体的 prim 路径。此外，需要为导出的 `isaac_grasp` 文件指定一个路径（以 `.yaml` 结尾）。Grasp Editor 支持将一系列抓取写入到同一个导出文件，但不支持在已有文件上追加或修改；如果给定的导出路径已存在，旧文件将被新内容覆盖。

在本教程中，我们将为从 Franka Emika Panda 机械臂中独立出来的 Panda 手爪与一个杯子（mug）创建抓取。当点击“Ready”时，Grasp Editor 会校验所有输入字段，并对所选物体（即杯子）执行必要的转换，使其可被抓取——具体而言，它会为物体添加 UsdPhysics 的刚体（Rigid Body）和碰撞（Collision）组件，从而赋予物体碰撞几何并允许受外力驱动。

> `**注意**`：这些转换不会被自动回退到原始资产，因此除非你确实希望对 USD 场景做持久化修改，否则不建议保存该 USD 场景。

### 选择参考帧

在此面板中，你可以为夹爪和物体分别选择用以描述其位姿的参考帧。理解并正确选择这些帧至关重要：

1. **与运动规划管道的兼容性**  
    大多数运动生成算法不会直接解析 USD 文件，而是依赖 URDF。如果你选用的帧并未在对应的 URDF 中定义，那么所创建的抓取对于这些算法而言将毫无意义。
    
2. **与检测／定位系统的对齐**  
    例如，若通过相机识别物体姿态，则该视觉系统会隐含一个物体参考帧；在此情形下，你必须在 USD 场景中选用与该系统对应的帧。若场景中尚无合适的帧，就需在物体路径下（如 `/World/mug`）新建一个参考帧。
    

在本教程里，我们直接使用夹爪与物体的基座帧（base frames）。即便使用整台 Franka Panda 机械臂，抓取操作依然应基于 `panda_hand` 这一手爪帧。一旦点击“Finalize”后，这些参考帧的信息就会写入到输出的 `isaac_grasp` 文件中，并且无法再做更改。

Grasp Editor 会将所选参考帧的 USD 路径写入到导出的 `isaac_grasp` 文件中，但对于不解析 USD 的运动算法而言，这些路径信息本身是不可读的。
[[685d5c3abf0f830d45841b760b56ab74_MD5.jpeg|Open: Pasted image 20250514153907.png]]
![[685d5c3abf0f830d45841b760b56ab74_MD5.jpeg]]

### 关节设置

在此菜单中，需要为关节体中的各个关节选择哪些是夹爪的活动自由度（DOF）。Panda 手爪虽然有两根指头，但其中一根是模仿关节（mimic joint）：如图所示，调整 `panda_finger_joint1` 的值时，`panda_finger_joint2` 会同步运动，因此实际上只有一个自由度在控制夹爪。
[[4e816cf62615f573404637e9419d5a92_MD5.gif|Open: isaac_grasp_editor_1.gif]]
![[4e816cf62615f573404637e9419d5a92_MD5.gif]]
**选择活动关节**

- 勾选“Part of Gripper”以将该自由度纳入夹爪控制。
    
- 勾选后会展开以下设置：
    
    - **Position When Open**：张开状态时的关节位置（仿真将从此状态向闭合状态运动）。
        
    - **Position When Closed**：完全闭合时的关节位置。
        
    - **Grasp Speed**：仿真中从张开到闭合移动的速度。
        
    - **Max Effort Magnitude**：仿真时该关节可施加在物体上的最大力/力矩（单位 N 或 N·m）。
        

> **说明**：至少需有一个自由度被标记为“Part of Gripper”，才可导出抓取；只有被标记的活动自由度才会写入输出的 `isaac_grasp` 文件。

---

### 工具（Utils）

工具菜单中有两项实用功能，可以辅助使用 Grasp Editor：

1. **Mask Collision**：屏蔽夹爪与物体之间的碰撞，用于手动将物体移动到合适位置时避免碰撞干扰。仿真抓取或导入抓取时，这些碰撞会自动恢复。
    ![[53e37183649400a5b131f847ea244b7a_MD5.gif]]
2. **Show Physics Colliders**：可视化当前资产的物理碰撞体（collider）。如果仿真抓取时发现接触不完整，可通过此功能确认碰撞体几何是否正确。Grasp Editor 不具备自动修复碰撞体的功能，但可在无需仿真的情况下屏蔽碰撞并手动摆放姿态，然后导出抓取。

如果仿真抓取时物体与夹爪之间看起来并未完全接触，你可以使用 “Show Physics Colliders” 按钮来可视化与你的资源关联的碰撞几何。修复不正确的碰撞体几何不在此扩展的功能范围内，但 Grasp Editor 确实允许你在不进行仿真的情况下编写抓取。在这种情况下，你可以屏蔽碰撞（Mask Collision），并通过可视化方式手动将物体移至合适位置。
[[5403053e8804b4b2b210521e04306ec3_MD5.jpeg|Open: isaac_grasp_editor_3.png]]
![[5403053e8804b4b2b210521e04306ec3_MD5.jpeg]]
---

### Author a Grasp（编写抓取）

抓取可借助仿真功能来辅助创作。仅凭手动将资源移动到看似正确的位置往往不够精确。如下图所示，先将杯子大致移动到可被夹取的位置，然后点击 **Simulate** 按钮，根据所设关节参数闭合夹爪。这样，杯口会被推至夹爪指尖的正中央，并且夹爪指尖会停留在与物体精确接触的位置，从而大大提升写入输出文件的抓取质量的可靠性。仿真完成后，导出面板将自动填充当前抓取数据，随后即可将其写入文件。

#### Simulating a Grasp（仿真抓取）
[[e5b662d3afe50e7ce23b89af113b5df9_MD5.gif|Open: isaac_grasp_editor_3.gif]]
![[e5b662d3afe50e7ce23b89af113b5df9_MD5.gif]]
有时仿真抓取可能不适用于你的使用场景，例如：

- 资产的物理碰撞体不准确；
    
- 夹爪的开闭机械结构比 Grasp Editor 所能表达的更复杂。
    

在以上任一情况下，最佳做法是通过外部方式将资源摆到位，然后点击 **Skip Sim** 跳过仿真导出抓取。例如，一些真实机器人手爪具有高度耦合的自由度及复杂机械结构，需要以编程方式精确复现其动作，通过发送关节命令来驱动 USD 资产。在这种情况下，你可以开启碰撞检测，使用外部脚本或 OmniGraph 节点将夹爪移动至目标抓取姿态，再利用 Grasp Editor 的导出功能，将 USD 场景中当前的抓取状态写入 `isaac_grasp` 文件。
### 添加外部力和力矩

Grasp Editor 的一个额外功能是可以将外部力和力矩施加到抓取仿真中。这可以帮助辨别哪些抓取能够在物体上获得最佳的力闭合。施加的力和力矩的大小可以在“添加外部刚体力”面板中选择。力和力矩可以分别选择单个标量值。对于力，选择非零的值 $v$ 会沿着刚体基座的每个轴施加 $±v$ 牛顿的力；对于力矩，选择值 $v$ 会在每个轴上施加 $±v$ 牛顿·米的力矩。

下图展示了闭合抓取后施加 3 N 的力进行测试。此测试失败，杯子在施 [3, 0, 0] 的力后飞了出去。然后选择较小的 0.5 N 力，杯子虽移动，但抓取仍然保持住。
[[69982eff10fb9b3a6eccc79e5e143e3e_MD5.gif|Open: isaac_grasp_editor_4.gif]]
![[69982eff10fb9b3a6eccc79e5e143e3e_MD5.gif]]


### Exporting Grasps（导出抓取）

一旦抓取已完全仿真完毕，或已选择跳过仿真（Skip Sim），导出面板（Export Frame）即会可用。点击 **Export** 时，当前 USD 场景（Stage）的状态将被用来填充 `isaac_grasp` 文件中的各个字段。

- **confidence** 字段  
    取自导出面板中的 “Confidence” 数值。
    
- **position** 与 **orientation** 字段  
    通过计算夹爪相对于物体参考帧的相对位姿获得。这里使用的是在“选择参考帧”（Select Frames of Reference）中定义的帧。
    
- **cspace_position** 字段  
    根据标记为“Part of Gripper”的所有 DOF 的当前位置生成。
    
- **pregrasp_cspace_position** 字段  
    对每个被标记为“Part of Gripper”的 DOF，取其在“Joint Settings”中 “Position When Open” 所设的值。
    

在此步骤中，可以连续创作多个抓取，并顺序地将它们导出到同一个 `.yaml` 格式的 `isaac_grasp` 文件中。
[[df6d8811d9e213a1dcc1c5a3c1e70f46_MD5.jpeg|Open: Pasted image 20250514155740.png]]
![[df6d8811d9e213a1dcc1c5a3c1e70f46_MD5.jpeg]]

### Importing Grasps（导入抓取）

除了编写抓取外，Grasp Editor 还可用于验证在其他地方创建的抓取。在 **Import** 面板中，选择一个 `isaac_grasp` 文件后点击 **Import** 即可导入抓取。本教程示例中使用的导入文件与导出时相同，但实际上可以是任意其他文件。

如下图所示，多个抓取已通过 Grasp Editor 创作并写入文件。导入后，这些抓取可快速按顺序进行可视化和仿真验证。
[[bee764a0abf3e07e3630d114eec990e8_MD5.gif|Open: isaac_grasp_editor_5.gif]]
![[bee764a0abf3e07e3630d114eec990e8_MD5.gif]]

---
### 在 Isaac Sim 中使用已编写的抓取

Grasp Editor 主要是一个基于 UI 的扩展，但通过 Python API，它也为在 NVIDIA Isaac Sim 中导入和使用已编写的抓取提供了一些实用功能。

本节展示了如何确定机器人应执行某个已编写抓取的目标位置。

#### 抓取场景
[[78e00ddbb2348e467355638e18000595_MD5.jpeg|Open: Pasted image 20250514160008.png]]
![[78e00ddbb2348e467355638e18000595_MD5.jpeg]]
以下代码片段导入了在《导入抓取》中演示的抓取文件，并确定了为了复制 `grasp_1`，`panda_hand` 参考帧应该处于何处。

```python
from isaacsim.robot_setup.grasp_editor import import_grasps_from_file, GraspSpec
from isaacsim.core.utils.xforms import get_world_pose

import_file_path = "/path/to/franka_mug_grasp.yaml"
grasp_spec = import_grasps_from_file(import_file_path)

mug_reference_frame = "/World/mug"

grasp_names = grasp_spec.get_grasp_names()

mug_trans, mug_quat = get_world_pose(mug_reference_frame)
gripper_trans_target, gripper_orientation_target = grasp_spec.compute_gripper_pose_from_rigid_body_pose("grasp_1", mug_trans, mug_quat)

print("Grasp Names:", grasp_names)
print("Gripper Translation Target:", gripper_trans_target)
print("Gripper Orientation Target:", gripper_orientation_target)
```

运行以上代码后，将显示 `isaac_grasp` 文件中每个抓取的名称以及应该设置的 `panda_hand` 参考帧的平移（translation）和姿态（orientation）目标。

输出示例：

```
Grasp Names: ['grasp_0', 'grasp_1', 'grasp_2']
Gripper Translation Target: [ 0.41496072 -0.03612298  0.27738899]
Gripper Orientation Target: [-0.1690746   0.63886658  0.12752551  0.73959483]
```

在该代码片段中，使用了在 Grasp Editor 中选定的杯子参考帧。请注意，使用运动生成算法来实现此抓取不在本教程的范围之内。

如需查看完整的功能集，请查阅我们 API 文档中的 `GraspSpec` 类。

---

# Rig a Mobile Robot

如果你在 Omniverse USD Composer 中构建了机器人，或使用了不会携带关节信息的导入器，那么在该机器人能够像多关节机器人一样运动并被 Isaac Sim API 控制之前，你需要对其进行绑定（rig）。这涉及到在各个部件之间定义关节类型，并设定关节行为的参数（例如刚度和阻尼）。本教程包含如何为一辆叉车进行绑定的逐步说明。

#### Learning Objectives

在本教程中，你将把一个未绑定的叉车 USD 资产转变为能够移动并可被 Isaac Sim 命令驱动的叉车。

#### Getting Started

**Prerequisite**

- 完成 “Getting Started Tutorials” 系列，以学习如何在 Isaac Sim 中进行基本导航和核心概念。
    
- 完成 “Assemble a Simple Robots” 以及 “Adding Sensors and Cameras” 教程，以掌握刚体 API、碰撞 API、关节、驱动（drives）和多关节结构（articulations）的概念。
    

**Reference USDs**  
我们在 Isaac Sim 资产浏览器（Beta）中提供了三个与本教程相关的 USD 资产，路径如下：

- Unrigged Forklift：`Samples > Rigging > Forklift > forklift_b_unrigged_cm.usd`
    
- Rigged Forklift：`Samples > Rigging > Forklift > forklift_b_rigged_cm.usd`
    

本教程将指导你完成从未绑定资产到已绑定资产的整个流程，已绑定资产可作为最终目标的参考。

---

## Rigging the Robot

#### Identify the Joints

在对资产进行任何修改前，绑定机器人的第一步是识别机器人上的所有关节，包括可驱动（actuated）和非驱动（unactuated）的。关节决定了各网格部件如何组织，识别关节类型及其自由度（DOF）是确保绑定后机器人按预期运动的关键。

对于这辆叉车，总共有 7 个自由度：

1. 前部有 4 个小滚轮（roller wheel），它们都是非驱动的旋转（revolute）关节，每个关节绕单一轴线旋转，故每个具有 1 个自由度。
    
2. 货叉（fork）相对于叉车主体沿直线上下移动，用以拾取托盘上的物体，这意味着货叉与车身之间有 1 个可驱动的棱柱（prismatic）关节。
    
3. 后部大轮（rear wheel）负责驱动叉车前后移动及转向，与该轮相关的可驱动关节有两个：
    
    - 一个是绕轮轴旋转的旋转关节，用于提供前后运动；
        
    - 另一个是位于后轮架（wheelbase）与叉车车身之间的旋转关节，用于实现转向枢轴。
        

## Organize the Hierarchy

1. 在资产浏览器中打开未绑定叉车资产：
    
    ```nginx
    Samples > Rigging > Forklift > forklift_b_unrigged_cm.usd
    ```
    
2. 根据所用导入器和原始资产设置，未绑定的 USD 文件可能呈现为扁平结构，舞台树（stage tree）中独立列出每个组件。
    
3. 这种扁平结构不仅阅读和导航困难，更关键的是，它并未定义哪些对象应作为整体运动，以及这些整体之间的父子关联。
    

[[862fb6231c89ae10160a88af613f1ff2_MD5.jpeg|Open: Pasted image 20250514160527.png]]
![[862fb6231c89ae10160a88af613f1ff2_MD5.jpeg]]


所有作为父级 prim 的子级网格，在父级 prim 移动时应一起移动。例如，网格上的贴纸和链条是叉车车身的一部分，整个车身，无论由多少螺丝或块体组成，都可以看作是机器人中的一个链接。将它们组织到一个父级 `body` prim 下，这样当 `body` 移动时，所有组成车身的子部件也会一起移动。

---

要为叉车组织 prim：

1. 创建两个 XForm，命名为 `body` 和 `lift`。
    
2. 将所有构成叉车车身的网格移到 `body` XForm 下，将操作者驾驶舱的网格移到 `lift` XForm 下。为了方便使用，USD 文件中提供的网格已经根据层级排序：所有位于 `Looks` 上方的网格属于 `lift` XForm，位于 `Looks` 下方的网格（从 Right Chain Wheel 到 Body Glass）属于 `body` XForm，剩下的网格是车架和车轮部分。
    
3. 为后轮、后轮转向枢轴以及每个前部滚轮支架创建新的 XForm。
    
4. 设置关节时，如果能使 XForm 的框架与相应车轮的框架对齐，将更为方便。为此，选择每个车轮的网格，并在其属性面板中的 **Transform** 分类下，可以看到两个组件：`Translate` 和 `Translate:pivot`。新创建的 XForm 的变换应为这两个组件的和。例如，如果 `translate` 为 X = $x_p$ , Y = $y_p$ , Z = $z_p$ ，则新 XForm 的变换应设置为：X = $x_1 + x_p$ , Y = $y_1 + y_p$, Z = $z_1 + z_p$。
    
5. 车轮网格的 `Translate` 应设置为相应网格的 `Translate:pivot` 属性的反向。例如，如果 `Translate` 为 X, Y, Z，`Translate:pivot` 为 $Xp$, $Yp$, $Zp$，则 `Translate` 应设置为 -$Xp$, -$Yp$, -$Zp$。
    
6. 将相应的网格移到 XForm 下，这将定义它们之间的父子关系。
    

验证最终的层级结构如下所示：
[[1d56a5da8b9b45842d124d2788e7601d_MD5.jpeg|Open: Pasted image 20250514161101.png]]
![[1d56a5da8b9b45842d124d2788e7601d_MD5.jpeg]]

---
****注意**  
如果在此环节遇到困难，请在资产浏览器中查看已绑定骨骼的叉车作为参考：
```nginx
Samples > Rigging > Forklift > forklift_b_rigged_cm.usd
```

---

## 指定碰撞网格（Assign Collision Meshes）

下一步要确保所有网格的碰撞属性已正确设置。如果未设置碰撞属性，机器人在运动过程中可能会根据关节配置发生自穿透。

所提供的 USD 文件中，`body` 与 `lift` 的正确碰撞网格已预先配置，无需手动设置。但为了说明，以下是为 `SM_Forklift_Body_B01_01` 网格添加碰撞的步骤：

1. 在 `lift` XForm 下选中 `SM_Forklift_Body_B01_01` 网格，右键选择 **Add > Physics > Collider Preset**。
    
2. 默认的碰撞近似方式为 **Convex Hull**，可在属性面板（Property Tab）中向下滚动至 **Collision** 区域查看。
    
3. 要可视化碰撞体，在视口右上方点击 “眼睛” 图标，选择 **Show By Type > Physics > Colliders > Selected**。
    
4. 选中刚添加碰撞的网格时，应能看到粉色轮廓。注意，**Convex Hull** 对货叉的碰撞区覆盖过大，会包含不必要的区域，可能妨碍其它物体的正常存在。
    

可以使用不同的近似方式来定义碰撞网格。例如：

1. 选中任意已有碰撞体的网格，在其属性面板的 **Colliders** 区域，将近似方式改为 **Convex Decomposition**。
    
2. 更新碰撞网格的可视化。此时生成的网格会更加紧贴物体表面，因为分解后的凸多面体能更精准地逼近模型形状。
    
3. 可尝试其它近似方式，选择最适合的设置。
    

对所有通过关节相互作用的网格都按照上述流程设置碰撞。在属于后轮旋转支架的 `SM_Forklift_BackWheelbase_B01_01` 网格上，同样将碰撞近似方式设为 **Convex Decomposition**。
[[591089561d45734907c401d22abedbad_MD5.jpeg|Open: Pasted image 20250514161413.png]]
![[591089561d45734907c401d22abedbad_MD5.jpeg]][[204c51ab931df54a096a476075b60887_MD5.jpeg|Open: Pasted image 20250514161417.png]]
![[204c51ab931df54a096a476075b60887_MD5.jpeg]]
轮子的处理稍有不同：任何不够光滑、不能精确捕捉车轮形状与曲面的碰撞近似，在驱动轮子时都会导致颠簸运动。可通过使用圆柱体来近似碰撞网格来避免此问题。

1. 在菜单中选择 **Create > Shape > Cylinder**。
    
2. 设置圆柱体的缩放为：X=0.16、Y=0.16、Z=0.08；将其朝向（Orient）设为 Y=90。
    
3. 右键此圆柱体，选择 **Duplicate**，创建 4 个副本，分别对应 4 个前部滚轮。
    
4. 将这些圆柱体拖拽到各自前滚轮的 XForm 下，并将它们在各轴上的 Transform 全部重置为 0，这样圆柱体轴线就与 XForm 轴线完全对齐。
    
5. 右键圆柱体，选择 **Add > Physics > Collider**，为其添加碰撞体。
    
6. 对后轮重复同样操作，但因尺寸更大，将圆柱体缩放改为 X=0.3、Y=0.3、Z=0.1，Orient 同样设为 Y=90。
    
7. 确保所有 XForm 都已添加刚体属性：右键 XForm，选择 **Add > Physics > Rigid Body**。注意，刚体 prim 不能包含其他刚体子 prim。
    

至此，所有适当的碰撞网格和属性均已设置完成，你即可进入添加关节（joints）的阶段。

[[a27e59d67f0ee5842a83fa595e644580_MD5.jpeg|Open: Pasted image 20250514161519.png]]
![[a27e59d67f0ee5842a83fa595e644580_MD5.jpeg]]


## 添加关节和驱动（Add Joints and Drives）

在此步骤中，为叉车添加合适的关节。

---

#### 棱柱关节（Prismatic Joint）

第一个关节是叉车车身（body）与货叉（lift）之间的关节，需要在两者之间实现直线运动，使货叉相对于车身上下移动。

1. 选中 `lift` XForm，按住 Ctrl 键再选中 `body` XForm。
    
2. 在两者高亮时，右键选择 **Create > Physics > Joints > Prismatic Joint**。
    
3. 在场景树中找到新创建的棱柱关节并选中，在属性面板中将 **axis** 设置为 `Z`，表示直线运动沿 Z 轴方向。
    
4. 在属性面板中设置关节限位（limits），此处暂设为 **lower = –15**，**upper = 200**。
    
5. 为该关节添加线性驱动（Linear Drive）：左键单击关节，选择 **Add > Physics > Linear Drive**。
    
6. 设置驱动参数：**Damping = 10000**，**Stiffness = 100000**，并将目标位置（target position）设为 **–15**，使货叉初始位置接近地面。
    [[24c503f963e92fb69b03a5c89392c8a7_MD5.jpeg|Open: Pasted image 20250514161743.png]]
![[24c503f963e92fb69b03a5c89392c8a7_MD5.jpeg]]

---

#### 旋转关节（Revolute Joints）

1. **为所有前滚轮支架创建旋转关节**
    
    - 选中 `body` XForm，按住 Ctrl 键再选中任一滚轮支架对应的 XForm。
        
    - 右键选择 **Create > Physics > Joint > Revolute Joint**。
        
    - 确认在该滚轮的 XForm 下已添加 Revolute Joint。
        
    - 验证关节位置是否与滚轮的旋转轴对齐；如未对齐，请调整关节位置，并在属性面板中将 **axis** 设置为 `X`。
        
    - 按相同流程对其余三组前滚轮支架依次添加旋转关节。
        
2. **为后轮关节创建 Scope**
    
    - 在舞台空白处右键，选择 **Create > Scope**，命名为 `back_wheel_joints`。
        
    - 将后轮相关的两个旋转关节（后轮驱动关节与后轮转向关节）拖入此 Scope。
        
3. **添加后轮驱动关节**
    
    - 选中 `back_wheel_swivel` 和 `back_wheel` XForm，右键 **Create > Physics > Joint > Revolute Joint**，在两者间创建关节，确保其位置与后轮中心对齐。
        
    - 为此关节添加角度驱动（Angular Drive）：
        
        - **Damping = 10000**
            
        - **Stiffness = 100**
            
4. **添加后轮转向关节**
    
    - 选中 `body` 和 `back_wheel_swivel` XForm，右键 **Create > Physics > Joint > Revolute Joint**。
        
    - 在属性面板中将 **axis** 切换为 `Z`，并设置限位：
        
        - **lower = –60**
            
        - **upper = 60**  
            （表示车架可左右转向的角度范围，单位为度。）
            
    - 为此关节添加角度驱动（Angular Drive）：
        
        - **Damping = 100**
            
        - **Stiffness = 100000**
            
5. **为前滚轮关节创建 Scope**
    
    - 在舞台空白处右键，**Create > Scope**，命名为 `roller_joints`。
        
    - 将所有前滚轮支架的 Revolute Joint 拖入该 Scope。
        

> **提示**：在按 **Play** 之前，务必为场景添加 **Physics Scene** 和 **Ground Plane**。

[[7e4afaa8c5daa9ec21bcbbb5c8990a0e_MD5.jpeg|Open: Pasted image 20250514161931.png]]
![[7e4afaa8c5daa9ec21bcbbb5c8990a0e_MD5.jpeg]]

## **添加关节链（Articulations）**  
最后一步是为叉车添加关节链，并将所有关节合并到一个关节链（Articulation Chain）中，这可以让物理求解器在处理机器人等多关节对象时更加高效。参考 USD 资产中已为该 prim 添加了此步骤；如果未添加，请按以下操作：

1. 选中 `SMV_Forklift_B01_01` XForm，右键选择 **Add > Physics > Articulation Root**。
    
2. 在属性面板中取消勾选 **Self Collision**。
    

> **注意事项**
> 
> - 若将关节根（Articulation Root）放在资产的根 XForm prim 上（Isaac Sim 资产的标准做法），模拟会自动将其关联到机器人中的某个刚体，从而最小化关节树的深度。
>     
> - 若需手动指定关节根位置，应将其置于机器人某刚体组件上——移动机器人推荐放在底盘或机体上，机械臂则放在固定关节处。
>     
> - 请确认所用资产与示例中的 Rigged Forklift 资产设置一致。
>     

---

**转换单位**  
原始资产以厘米（cm）为单位；当它被加载到以米（m）为单位的场景中时，会自动转换为米（参见 Metrics Assembler）。加载后，请确保其与示例中“Meters”版本的 Rigged Forklift 资产保持一致。

---

**运行验证**

1. 在后轮关节的 Angular Drive 面板中，将目标速度（Target Velocity）设为 **–200**。
    
2. 点击播放（Play），确认叉车能向前行驶。
    

---

**小结**  
本教程演示了如何将一个未绑定骨骼的 USD 叉车资产，组织结构、添加碰撞网格、关节与驱动，最终在 Isaac Sim 中通过命令驱动其运动。

---

**故障排除**  
若在模拟运行或运动后出现“机器人爆炸”现象，请检查各碰撞网格之间是否发生了自碰撞。







---

# **导入机械臂**

**学习目标**  
本教程演示如何将新的机械臂导入 NVIDIA Isaac Sim，为其创建控制器，并执行拾取与放置任务。

完成本教程后，您将能够针对 NVIDIA Isaac Sim 中的新机械臂创建拾取与放置任务及其控制器。本教程中我们将使用 Denso 的 COBOTTA pro 900 机械臂。

> **注意**：本教程创建的所有示例文件均可在 `standalone_examples/api/isaacsim.robot.manipulators/cobotta_900` 目录下找到，以供验证。

**预计耗时：30–35 分钟**

---

## 开始使用

### 前置条件

在开始本教程之前，请先复习 Core API Hello World 和 GUI Tutorial 系列教程。

### 检查关节结构

1. 启动 NVIDIA Isaac Sim 并打开 Asset Browser，导航至 **Robots > Denso > COBOTTA pro 900**，将 `cobotta_pro_900.usd` 文件拖入场景（stage）。
    
2. （可选）也可右键点击 Asset Browser 中的文件选择 **Download** 将 USD 文件下载到本地。
    
3. 新建场景：**File > New**，此时默认会添加一个 `defaultLight`。
    
4. 在 **World** 下创建 **XForm**，并重命名为 `denso`。
    
5. 将机器人 USD 文件以引用（reference）方式添加到新建的 `denso` XForm。
    
6. 为场景添加物理场（Physics Scene）。
    
7. 打开物理检查器（Physics Inspector）：**Tools > Physics > Physics Inspector** 或点击 Physics 工具栏上的按钮。
    
8. 在场景中选中 COBOTTA 机械臂，并在 Physics Inspector 中点击刷新按钮。
    

在 **DOF View** 中熟悉各个关节的自由度（DOF）。注意：

- 每个 DOF 的默认目标位置均为 0。
    
- 抓手（gripper）有 6 个关节。
    
- 记录抓手部分的 DOF 名称，以便后续使用 NVIDIA Isaac Sim 提供的 `ParallelGripper` 类。
    
- 我们仅使用抓手中的 2 个关节进行控制——`finger_joint` 和 `right_outer_knuckle_joint`；将 DOF 7 和 8 设为 0.628，使它们在其他关节夹取物体时避开视线。
    

尝试拖動滑杆更改目标位置，并验证 DOF 位置能达到指定值。
[[273e4fe4fb74f9dd7fdbd5471cf6023f_MD5.jpeg|Open: Pasted image 20250514163744.png]]
![[273e4fe4fb74f9dd7fdbd5471cf6023f_MD5.jpeg]]
> **注**：如需了解更多物理检查器用法，请参见  [Physics Inspector](https://docs.omniverse.nvidia.com/extensions/latest/ext_physics/support-ui.html#physics-inspector)文档。

---

## 控制抓手

接下来编写一个简单脚本，让抓手不断开合，以验证我们能否加载 USD 文件并对其进行基本控制。

1. 在 `COBOTTA_SCRIPTS_PATH/` 下新建文件夹 `gripper_control.py`（使用 standalone 工作流）。
    
2. 将以下代码粘贴进去：
    

```python
from isaacsim import SimulationApp

simulation_app = SimulationApp({"headless": False})

from isaacsim.core.api import World
from isaacsim.robot.manipulators.manipulators import SingleManipulator
from isaacsim.robot.manipulators.grippers import ParallelGripper
from isaacsim.core.utils.stage import add_reference_to_stage
from isaacsim.core.utils.types import ArticulationAction
import numpy as np

my_world = World(stage_units_in_meters=1.0)

# 使用 Isaac Sim 提供的资产路径
asset_path = assets_root_path + "/Isaac/Robots/Denso/cobotta_pro_900.usd"
# 如已下载至本地，请修改为本地路径
# asset_path = "/home/user_name/cobotta_pro_900/cobotta_pro_900/cobotta_pro_900.usd"

add_reference_to_stage(usd_path=asset_path, prim_path="/World/cobotta")

# 定义抓手
gripper = ParallelGripper(
    end_effector_prim_path="/World/cobotta/onrobot_rg6_base_link",
    joint_prim_names=["finger_joint", "right_outer_knuckle_joint"],
    joint_opened_positions=np.array([0, 0]),
    joint_closed_positions=np.array([0.628, -0.628]),
    action_deltas=np.array([-0.628, 0.628]),
)

# 定义机械臂
my_denso = my_world.scene.add(
    SingleManipulator(
        prim_path="/World/cobotta", name="cobotta_robot",
        end_effector_prim_name="onrobot_rg6_base_link", gripper=gripper
    )
)

# 将抓手的其他关节设置为打开状态，避免干扰
joints_default_positions = np.zeros(12)
joints_default_positions[7] = 0.628
joints_default_positions[8] = 0.628
my_denso.set_joints_default_state(positions=joints_default_positions)

my_world.scene.add_default_ground_plane()
my_world.reset()

i = 0
while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
        i += 1
        gripper_positions = my_denso.gripper.get_joint_positions()
        if i < 500:
            # 缓慢关闭抓手
            my_denso.gripper.apply_action(
                ArticulationAction(joint_positions=[
                    gripper_positions[0] + 0.1,
                    gripper_positions[1] - 0.1
                ])
            )
        elif i < 1000:
            # 缓慢打开抓手
            my_denso.gripper.apply_action(
                ArticulationAction(joint_positions=[
                    gripper_positions[0] - 0.1,
                    gripper_positions[1] + 0.1
                ])
            )
        else:
            i = 0

simulation_app.close()
```

运行命令：

```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/gripper_control.py
```
[[3d02e66e32eaefa8e5268ce271387d40_MD5.gif|Open: isaac_sim_advanced_add_new_manipulator_8.gif]]
![[3d02e66e32eaefa8e5268ce271387d40_MD5.gif]]

---

## 添加跟随目标任务

为 COBOTTA 添加一个跟随目标任务，使末端执行器跟随场景中的方块。

1. 在 `COBOTTA_SCRIPTS_PATH/tasks/` 下新建 `follow_target.py`，定义任务类：
    

```python
from isaacsim.robot.manipulators.manipulators import SingleManipulator
from isaacsim.robot.manipulators.grippers import ParallelGripper
from isaacsim.core.utils.stage import add_reference_to_stage
import isaacsim.core.api.tasks as tasks
from typing import Optional
import numpy as np

class FollowTarget(tasks.FollowTarget):
    def __init__(
        self,
        name: str = "denso_follow_target",
        target_prim_path: Optional[str] = None,
        target_name: Optional[str] = None,
        target_position: Optional[np.ndarray] = None,
        target_orientation: Optional[np.ndarray] = None,
        offset: Optional[np.ndarray] = None,
    ) -> None:
        super().__init__(
            name=name,
            target_prim_path=target_prim_path,
            target_name=target_name,
            target_position=target_position,
            target_orientation=target_orientation,
            offset=offset,
        )

    def set_robot(self) -> SingleManipulator:
        # TODO: 修改为本地 USD 文件路径
        asset_path = "/home/user_name/cobotta_pro_900/cobotta_pro_900/cobotta_pro_900.usd"
        add_reference_to_stage(usd_path=asset_path, prim_path="/World/cobotta")
        gripper = ParallelGripper(
            end_effector_prim_path="/World/cobotta/onrobot_rg6_base_link",
            joint_prim_names=["finger_joint", "right_outer_knuckle_joint"],
            joint_opened_positions=np.array([0, 0]),
            joint_closed_positions=np.array([0.628, -0.628]),
            action_deltas=np.array([-0.628, 0.628])
        )
        manipulator = SingleManipulator(
            prim_path="/World/cobotta",
            name="cobotta_robot",
            end_effector_prim_name="onrobot_rg6_base_link",
            gripper=gripper
        )
        joints_default_positions = np.zeros(12)
        joints_default_positions[7] = 0.628
        joints_default_positions[8] = 0.628
        manipulator.set_joints_default_state(positions=joints_default_positions)
        return manipulator
```

2. 在 `COBOTTA_SCRIPTS_PATH/` 下新建 `follow_target_example.py`，运行示例：
    

```python
from isaacsim import SimulationApp
from isaacsim.core.api import World
from tasks.follow_target import FollowTarget
import numpy as np

simulation_app = SimulationApp({"headless": False})
my_world = World(stage_units_in_meters=1.0)

# 初始化跟随任务，设置方块目标位置
my_task = FollowTarget(
    name="denso_follow_target",
    target_position=np.array([0.5, 0, 0.5])
)
my_world.add_task(my_task)
my_world.reset()

# 获取任务参数
task_params = my_world.get_task("denso_follow_target").get_params()
target_name = task_params["target_name"]["value"]
denso_name = task_params["robot_name"]["value"]
my_denso = my_world.scene.get_object(denso_name)
articulation_controller = my_denso.get_articulation_controller()

while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
        observations = my_world.get_observations()
        print(observations)

simulation_app.close()
```

```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/follow_target_example.py
```
[[280c0b6ac8fdc72102f9bafd7e27f2d2_MD5.jpeg|Open: Pasted image 20250514164329.png]]
![[280c0b6ac8fdc72102f9bafd7e27f2d2_MD5.jpeg]]
---

## 使用逆运动学（IK）求解

1. 在 `COBOTTA_SCRIPTS_PATH/rmpflow/` 下创建 `robot_descriptor.yaml`，内容如下：
    

```yaml
api_version: 1.0
cspace:
    - joint_1
    - joint_2
    - joint_3
    - joint_4
    - joint_5
    - joint_6
root_link: world
default_q: [0.00, 0.00, 0.00, 0.00, 0.00, 0.00]
cspace_to_urdf_rules: []
composite_task_spaces: []
```

2. 在 `COBOTTA_SCRIPTS_PATH/` 下新建 `ik_solver.py`，定义 IK 求解器：
    

```python
from isaacsim.robot_motion.motion_generation import ArticulationKinematicsSolver, LulaKinematicsSolver
from isaacsim.core.prims import Articulation
from typing import Optional

class KinematicsSolver(ArticulationKinematicsSolver):
    def __init__(
        self,
        robot_articulation: Articulation,
        end_effector_frame_name: Optional[str] = None
    ) -> None:
        # TODO: 修改配置文件路径
        self._kinematics = LulaKinematicsSolver(
            robot_description_path="COBOTTA_SCRIPTS_PATH/rmpflow/robot_descriptor.yaml",
            urdf_path="/home/user_name/cobotta_pro_900/cobotta_pro_900.urdf"
        )
        if end_effector_frame_name is None:
            end_effector_frame_name = "onrobot_rg6_base_link"
        super().__init__(robot_articulation, self._kinematics, end_effector_frame_name)
```

3. 修改 `follow_target_example.py`，使用 IK 求解控制：
    

```python
from isaacsim import SimulationApp
from isaacsim.core.api import World
from tasks.follow_target import FollowTarget
from ik_solver import KinematicsSolver
import numpy as np
import carb

simulation_app = SimulationApp({"headless": False})
my_world = World(stage_units_in_meters=1.0)

my_task = FollowTarget(name="denso_follow_target", target_position=np.array([0.5, 0, 0.5]))
my_world.add_task(my_task)
my_world.reset()

task_params = my_world.get_task("denso_follow_target").get_params()
target_name = task_params["target_name"]["value"]
denso_name = task_params["robot_name"]["value"]
my_denso = my_world.scene.get_object(denso_name)

# 初始化 IK 控制器
my_controller = KinematicsSolver(my_denso)
articulation_controller = my_denso.get_articulation_controller()

while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
        observations = my_world.get_observations()
        actions, succ = my_controller.compute_inverse_kinematics(
            target_position=observations[target_name]["position"],
            target_orientation=observations[target_name]["orientation"],
        )
        if succ:
            articulation_controller.apply_action(actions)
        else:
            carb.log_warn("IK 求解未收敛，无动作执行。")

simulation_app.close()
```
[[28c703904034d63e3b476b40db3710c5_MD5.gif|Open: isaac_sim_advanced_add_new_manipulator_10.gif]]
![[28c703904034d63e3b476b40db3710c5_MD5.gif]]
```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/follow_target_example.py
```

> **注**：为简化教学，未启用自碰撞，因此在某些场景下机械臂链接可能会相互重叠。

---

## 使用 RMPflow 控制

1. 在 `COBOTTA_SCRIPTS_PATH/rmpflow/` 下创建 `denso_rmpflow_common.yaml`，并复制以下内容：
    

```yaml
joint_limit_buffers: [.01, .01, .01, .01, .01, .01]
rmp_params:
    cspace_target_rmp:
        metric_scalar: 50.
        position_gain: 100.
        damping_gain: 50.
        robust_position_term_thresh: .5
        inertia: 1.
    cspace_trajectory_rmp:
        p_gain: 100.
        d_gain: 10.
        ff_gain: .25
        weight: 50.
    # …（其余按教程粘贴）
canonical_resolve:
    max_acceleration_norm: 50.
    projection_tolerance: .01
    verbose: false
body_cylinders:
    - name: base
      pt1: [0,0,.333]
      pt2: [0,0,0.]
      radius: .05
body_collision_controllers:
    - name: onrobot_rg6_base_link
      radius: .05
```

2. 在 `COBOTTA_SCRIPTS_PATH/controllers/` 下创建 `rmpflow.py`，定义 RMPflow 控制器：
    

```python
import isaacsim.robot_motion.motion_generation as mg
from isaacsim.core.prims import Articulation

class RMPFlowController(mg.MotionPolicyController):
    def __init__(
        self,
        name: str,
        robot_articulation: Articulation,
        physics_dt: float = 1.0 / 60.0
    ) -> None:
        # TODO: 修改配置路径
        self.rmpflow = mg.lula.motion_policies.RmpFlow(
            robot_description_path="COBOTTA_SCRIPTS_PATH/rmpflow/robot_descriptor.yaml",
            rmpflow_config_path="COBOTTA_SCRIPTS_PATH/rmpflow/denso_rmpflow_common.yaml",
            urdf_path="/home/user_name/cobotta_pro_900/cobotta_pro_900.urdf",
            end_effector_frame_name="onrobot_rg6_base_link",
            maximum_substep_size=0.00334
        )
        self.articulation_rmp = mg.ArticulationMotionPolicy(
            robot_articulation, self.rmpflow, physics_dt
        )
        super().__init__(name=name, articulation_motion_policy=self.articulation_rmp)
        self._default_position, self._default_orientation = \
            self._articulation_motion_policy._robot_articulation.get_world_pose()
        self._motion_policy.set_robot_base_pose(
            robot_position=self._default_position,
            robot_orientation=self._default_orientation
        )

    def reset(self):
        super().reset()
        self._motion_policy.set_robot_base_pose(
            robot_position=self._default_position,
            robot_orientation=self._default_orientation
        )
```

3. 修改 `follow_target_example.py`，使用 RMPflow 控制器：
    

```python
from isaacsim import SimulationApp
from isaacsim.core.api import World
from tasks.follow_target import FollowTarget
from controllers.rmpflow import RMPFlowController
import numpy as np

simulation_app = SimulationApp({"headless": False})
my_world = World(stage_units_in_meters=1.0)

my_task = FollowTarget(name="denso_follow_target", target_position=np.array([0.5, 0, 0.5]))
my_world.add_task(my_task)
my_world.reset()

task_params = my_world.get_task("denso_follow_target").get_params()
target_name = task_params["target_name"]["value"]
denso_name = task_params["robot_name"]["value"]
my_denso = my_world.scene.get_object(denso_name)

# 初始化 RMPflow 控制器
my_controller = RMPFlowController(
    name="target_follower_controller", robot_articulation=my_denso
)
my_controller.reset()
articulation_controller = my_denso.get_articulation_controller()

while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
            my_controller.reset()
        observations = my_world.get_observations()
        actions = my_controller.forward(
            target_end_effector_position=observations[target_name]["position"],
            target_end_effector_orientation=observations[target_name]["orientation"],
        )
        articulation_controller.apply_action(actions)

simulation_app.close()
```

```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/follow_target_example.py
```

---

## 添加拾取放置（Pick & Place）任务

1. 在 `COBOTTA_SCRIPPTS_PATH/tasks/` 下创建 `pick_place.py`，定义任务类：
    

```python
from isaacsim.robot.manipulators.manipulators import SingleManipulator
from isaacsim.robot.manipulators.grippers import ParallelGripper
from isaacsim.core.utils.stage import add_reference_to_stage
import isaacsim.core.api.tasks as tasks
from typing import Optional
import numpy as np

class PickPlace(tasks.PickPlace):
    def __init__(
        self,
        name: str = "denso_pick_place",
        cube_initial_position: Optional[np.ndarray] = None,
        cube_initial_orientation: Optional[np.ndarray] = None,
        target_position: Optional[np.ndarray] = None,
        offset: Optional[np.ndarray] = None,
    ) -> None:
        super().__init__(
            name=name,
            cube_initial_position=cube_initial_position,
            cube_initial_orientation=cube_initial_orientation,
            target_position=target_position,
            cube_size=np.array([0.0515, 0.0515, 0.0515]),
            offset=offset,
        )

    def set_robot(self) -> SingleManipulator:
        # TODO: 修改资产路径
        asset_path = "/home/user_name/cobotta_pro_900/cobotta_pro_900/cobotta_pro_900.usd"
        add_reference_to_stage(usd_path=asset_path, prim_path="/World/cobotta")
        gripper = ParallelGripper(
            end_effector_prim_path="/World/cobotta/onrobot_rg6_base_link",
            joint_prim_names=["finger_joint", "right_outer_knuckle_joint"],
            joint_opened_positions=np.array([0, 0]),
            joint_closed_positions=np.array([0.628, -0.628]),
            action_deltas=np.array([-0.2, 0.2])
        )
        manipulator = SingleManipulator(
            prim_path="/World/cobotta",
            name="cobotta_robot",
            end_effector_prim_name="onrobot_rg6_base_link",
            gripper=gripper
        )
        joints_default_positions = np.zeros(12)
        joints_default_positions[7] = 0.628
        joints_default_positions[8] = 0.628
        manipulator.set_joints_default_state(positions=joints_default_positions)
        return manipulator
```

2. 在 `COBOTTA_SCRIPTS_PATH/` 下创建 `pick_up_example.py`，运行示例：
    

```python
from isaacsim import SimulationApp
from isaacsim.core.api import World
import numpy as np
from tasks.pick_place import PickPlace

simulation_app = SimulationApp({"headless": False})
my_world = World(stage_units_in_meters=1.0)

# 设置放置目标位置
target_position = np.array([-0.3, 0.6, 0])
target_position[2] = 0.0515 / 2.0
my_task = PickPlace(name="denso_pick_place", target_position=target_position)
my_world.add_task(my_task)
my_world.reset()

task_params = my_world.get_task("denso_pick_place").get_params()
denso_name = task_params["robot_name"]["value"]
my_denso = my_world.scene.get_object(denso_name)
articulation_controller = my_denso.get_articulation_controller()

while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
        observations = my_world.get_observations()
        print(observations)

simulation_app.close()
```

```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/pick_up_example.py
```
[[7dc89629c828943d30442bdb2d6d4e7c_MD5.jpeg|Open: Pasted image 20250514164509.png]]
![[7dc89629c828943d30442bdb2d6d4e7c_MD5.jpeg]]
---

## 添加 Pick & Place 控制器

1. 在 `COBOTTA_SCRIPTS_PATH/controllers/` 下创建 `pick_place.py`：
    

```python
import isaacsim.robot.manipulators.controllers as manipulators_controllers
from isaacsim.robot.manipulators.grippers import ParallelGripper
from .rmpflow import RMPFlowController
from isaacsim.core.prims import Articulation

class PickPlaceController(manipulators_controllers.PickPlaceController):
    def __init__(
        self,
        name: str,
        gripper: ParallelGripper,
        robot_articulation: Articulation,
        events_dt=None
    ) -> None:
        if events_dt is None:
            # 事件时间步，可根据运动平滑度进行调整
            events_dt = [0.005, 0.002, 1, 0.05, 0.0008, 0.005, 0.0008, 0.1, 0.0008, 0.008]
        super().__init__(
            name=name,
            cspace_controller=RMPFlowController(
                name=name + "_cspace_controller",
                robot_articulation=robot_articulation
            ),
            gripper=gripper,
            events_dt=events_dt,
            start_picking_height=0.6
        )
```

2. 修改 `pick_up_example.py`，使用 PickPlaceController：
    

```python
from isaacsim import SimulationApp
from isaacsim.core.api import World
import numpy as np
from tasks.pick_place import PickPlace
from controllers.pick_place import PickPlaceController

simulation_app = SimulationApp({"headless": False})
my_world = World(stage_units_in_meters=1.0)

target_position = np.array([-0.3, 0.6, 0])
target_position[2] = 0.0515 / 2.0
my_task = PickPlace(name="denso_pick_place", target_position=target_position)
my_world.add_task(my_task)
my_world.reset()

my_denso = my_world.scene.get_object("cobotta_robot")
# 初始化 Pick & Place 控制器
my_controller = PickPlaceController(
    name="controller",
    robot_articulation=my_denso,
    gripper=my_denso.gripper
)

task_params = my_world.get_task("denso_pick_place").get_params()
articulation_controller = my_denso.get_articulation_controller()
i = 0

while simulation_app.is_running():
    my_world.step(render=True)
    if my_world.is_playing():
        if my_world.current_time_step_index == 0:
            my_world.reset()
            my_controller.reset()
        observations = my_world.get_observations()
        # 传入观测值获取动作
        actions = my_controller.forward(
            picking_position=observations[task_params["cube_name"]["value"]]["position"],
            placing_position=observations[task_params["cube_name"]["value"]]["target_position"],
            current_joint_positions=observations[task_params["robot_name"]["value"]]["joint_positions"],
            end_effector_offset=np.array([0, 0, 0.25]),
        )
        if my_controller.is_done():
            print("完成拾取与放置")
        articulation_controller.apply_action(actions)

simulation_app.close()
```

```bash
./python.sh standalone_examples/api/isaacsim.robot.manipulators/cobotta_900/pick_up_example.py
```
[[3db6872915dce04af9f9cb725e966c77_MD5.gif|Open: isaac_sim_advanced_add_new_manipulator_12.gif]]
![[3db6872915dce04af9f9cb725e966c77_MD5.gif]]
---

## 总结

本教程涵盖了以下内容：

1. 使用 Articulation Inspector 检查机器人 USD。
    
2. 使用 Gain Tuner 调整机器人增益。
    
3. 添加跟随目标（Follow Target）和拾取放置（Pick & Place）任务。
    
4. 添加控制器以执行指定任务。
    
5. 创建简单的 RMPflow 配置文件。
    

## 后续学习

了解如何为新机械臂创建 RMPflow 配置文件及各字段的详细说明，请参阅  [**Configuring RMPflow for a New Manipulator**](https://docs.isaacsim.omniverse.nvidia.com/latest/manipulators/manipulators_configure_rmpflow_denso.html#isaac-sim-app-tutorial-configure-rmpflow-denso) 文档。


---

# Rig 闭环结构  
有些模型较难表达。机器人和夹爪仍然具有独特的特性和结构，并不常见。在本篇文档中，你将学习一些建模这些独特特性的技巧，并掌握一种通用的方法来管理这些特殊配置。

## 学习目标  
在本教程中，你将能够：

- 使用 USD Layer 编辑和测试资产
    
- 在 CAD 导入后添加材质并调整关节
    
- 打破闭环的 Articulation 链
    
- 添加关节驱动，包括 Mimic 关节
    
- 调整碰撞形状
    
- 通过搭建测试环境并使用 Gripper Controller Omnigraph 来测试夹爪
    

我们将从一个在 Onshape 中导入并建模好关节的 Robotiq 2F-85 平行夹爪 stp 文件开始。本教程并不直接涉及关节参数调优——配置资产时已提供了调优好的参数。想了解更多增益调优内容，请参阅《[[#调整关节驱动增益（Tuning Joint Drive Gains）]]》和[[#Gain Tuner 扩展]]。

## 开始之前

### 前置条件

1. 完成“入门教程”系列，掌握 NVIDIA Isaac Sim 的基础概念和导航操作。
    
2. 完成“组装简单机器人”和“添加传感器与摄像头”教程，了解刚体 API、碰撞 API、关节、驱动和 Articulation。
    
3. 阅读 Onshape 导入器相关文档，并观看在 Onshape 中给机器人绑定（rigging）的演示视频。
    
4. 在 Onshape 中已经导入 Robotiq 2F-85 夹爪，并建模好连接指爪与主体的关节。
    

---

## 机器人装配（Rigging）

### 使用 Layer 编辑与测试资产

所有刚体、质量及关节定义都在 Onshape 中完成，导入到 Isaac Sim 后，资产已包含基本的关节信息和刚体设置。要让资产完全可用，还需执行以下额外步骤：

1. **新建 Stage**  
    不要直接打开原始资产，而是通过 Layer 在它之上构建场景，并保存为新的 USD 文件，以免修改根层资产。举例来说，你可以添加地面平面和测试用物体，将测试设置保存在 Layer 中，同时保持原始夹爪资产干净无额外测试物体。
    
2. **另存为**  
    将当前 Stage 另存为 `Robotiq_2F_85_config.usd`，放在与你导入资产相同的文件夹下（可在 Property 面板的 Reference 或 Payload 区域中找到源文件并点击“Locate file”图标定位）。
    
3. **打开 Layer 面板**  
    将 `Robotiq_2F_85_edit.usd` 拖入 Root Layer。
    
    > 注意：源文件夹中还有 `Robotiq_2F_85_base.usd`，这是从 Onshape 导入后未经修改的干净 Stage，不要直接编辑，以便后续重新导入时保留更新能力。
    [[71c88c8ce64ed492c258d6128ba28d40_MD5.jpeg|Open: Pasted image 20250514165934.png]]
![[71c88c8ce64ed492c258d6128ba28d40_MD5.jpeg]]
4. **在 Authoring Layer 中编辑**  
    双击选择要编写的 Layer。若误在错误的 Layer 中做了改动，可将有差异（delta）的 prim 拖到正确的 Layer 中进行合并。我们通常先在 Root Layer 中完成所有编辑，满意后再拖到 `Robotiq_2F_85_edit.usd` Layer 中。
    
5. **关节命名**  
    本资产中关节命名示意：
[[f72ba6ab500197472ad85ea8fbd9c577_MD5.jpeg|Open: Pasted image 20250514170055.png]]
![[f72ba6ab500197472ad85ea8fbd9c577_MD5.jpeg]]
    

> 提示：在导入前，最好在 Onshape 中通过 Group Mates 将构成刚体的零件合并，并重命名指爪为 `left_finger_…`、`right_finger_…` 等，以简化 Stage 上的刚体结构。

---

### 导入后调整关节

有时 Onshape Client API 会导致关节旋转方向与设计图相反 180°。这时，选中被翻转的关节，在 Rotation 0 和 Rotation 1 的 X 轴上各加一个 180° 偏移即可。本资产中的四个指爪关节就需要这么调整。

- `[left,right]_outer_finger_joint` 的限制范围设为 `[0, 180]`
    
- `[finger_joint, right_outer_knuckle_joint]` 的限制范围设为 `[0, 75]`
    
- 其他关节保持无限制
    

#### 添加指尖物理材质

1. 打开菜单 `Create > Physics > Physics Material`
    
2. 选择 Rigid Body Material，重命名为 `fingertip_material`
    
3. 将静、动摩擦系数均设为 0.8（橡胶默认），摩擦组合模式设为 Max
    
4. 选中 `right_inner_finger` 和 `left_inner_finger`，在 Physics 材质属性中绑定该材质
    

---

### 打破 Articulation 闭环
[[bb216ff6190bbc3c17b38f5951a8e7a9_MD5.jpeg|Open: Pasted image 20250514170836.png]]
![[bb216ff6190bbc3c17b38f5951a8e7a9_MD5.jpeg]]
如需了解更多细节，请参阅《[物理仿真基础](https://docs.isaacsim.omniverse.nvidia.com/latest/physics/simulation_fundamentals.html#simulation-fundamentals)》一文。

**Articulation（关节编排）** 必须是一棵“运动学树”（kinematic tree），否则求解器（solver）会报闭环错误（loop-closure error）。

- **无需删除任何关节**。
    
- 要消除警告，只需在所有关节中 **选一个**，将它 **排除** 出 Articulation，让它以 **最大坐标关节**（maximal coordinate joint）的形式存在。
    
- 最大坐标关节在求解时优先级最低，因此它恰好承担了仿真中误差最大的那部分累积。
    

**如何选“排除对象”？**

1. **最短链路原则**：剔除后，剩余的 Articulation 链尽可能短。
    
2. **功能干扰最小**：剔除的关节不应影响机器人正常运动。
    
3. **理想属性**：该关节应仅承担空间约束，且具有以下特征：
    
    - 无运动范围限制（limits）
        
    - 无阻尼或摩擦阻力（damping/friction）
        
    - 无驱动（drive）
        
4. 若当前没有完全符合上述条件的关节，可先把它的“限位、阻尼、驱动”属性转移到相邻关节，再将其剔除。
    

**本例（并联夹爪）最佳方案**

- 选取连接“内轴”（inner shaft）与“夹爪主体”（gripper body）的 `inner_knuckle_joint`。
    
- 如下图所示，该关节以橙色标记，符合“最短链路”且对夹爪功能影响极低。
    
[[ad659a20a3fa6b031f6e01bece65bda5_MD5.jpeg|Open: Pasted image 20250514171656.png]]
![[ad659a20a3fa6b031f6e01bece65bda5_MD5.jpeg]]

---

## 测试环境搭建

由于夹爪尚未连接到任何可移动结构，无法直接测试其物理性能，因此需要先添加一个测试用的支架结构：

1. 在场景中创建两个 Xform 节点，并为它们应用刚体（Rigid Body）API。
    
2. 在 World 与第一个 Xform 之间添加一个固定关节（Fixed Joint）。
    
3. 在第一个 Xform 与第二个 Xform 之间添加一个平移关节（Prismatic Joint）。
    
4. 在第二个 Xform 与夹爪的 `base_link` 之间再添加一个平移关节。
    
5. 为这两个平移关节添加关节驱动（Joint Drive），以便通过位置命令实现抬升和平移操作。
    

在关节驱动的高级属性中，设置如下参数：

- **最大关节速度**（Max Joint Velocity）：5.0
    
- **关节行程限制**（Limits）：[0, 1]
    
- **阻尼系数**（Damping）：10000.0
    
- **刚度系数**（Stiffness）：10000.0
    

> 注意：新建的所有关节必须放置在 `Robotiq_2F_85` prim 之外。

为了便于观察夹爪的抓握效果，还需创建以下辅助物体：

- 一个 **圆柱体**，缩放至 `[0.05, 0.05, 0.2]`，位置设置为 `X = 0.12`。
    
- 一个 **地面平面**，位置设置为 `Z = -0.1`。
    

---

### 脚本示例

可在 **Script Editor**（窗口 > Script Editor）中粘贴并运行以下脚本，一键创建上述结构和辅助物体：

```python
from pxr import Usd, UsdGeom, UsdPhysics, PhysxSchema, PhysicsSchemaTools, Gf, Sdf
import omni.usd

stage = omni.usd.get_context().get_stage()

# 创建 Xform 节点
xform = UsdGeom.Xform.Define(stage, "/World/Xform")
xform_1 = UsdGeom.Xform.Define(stage, "/World/Xform_1")

# 为 Xform 节点应用刚体 API
for node in [xform, xform_1]:
    UsdPhysics.RigidBodyAPI.Apply(node.GetPrim())

# 创建 World → Xform 的固定关节
fixed_joint = UsdPhysics.FixedJoint.Define(
    stage,
    xform.GetPath().AppendChild("fixed_joint")
)
fixed_joint.CreateBody1Rel().SetTargets([str(xform.GetPath())])

# 创建第一个平移关节（沿 Z 轴）
prismatic_joint_1 = UsdPhysics.PrismaticJoint.Define(stage, "/World/Joint_Z")
prismatic_joint_1.CreateAxisAttr("Z")
prismatic_joint_1.CreateLowerLimitAttr(0.0)
prismatic_joint_1.CreateUpperLimitAttr(1.0)
prismatic_joint_1.CreateBody0Rel().SetTargets([str(xform.GetPath())])
prismatic_joint_1.CreateBody1Rel().SetTargets([str(xform_1.GetPath())])

# 创建第二个平移关节（沿 X 轴）
prismatic_joint_2 = UsdPhysics.PrismaticJoint.Define(stage, "/World/Joint_X")
prismatic_joint_2.CreateAxisAttr("X")
prismatic_joint_2.CreateLowerLimitAttr(0.0)
prismatic_joint_2.CreateUpperLimitAttr(1.0)
prismatic_joint_2.CreateBody0Rel().SetTargets([str(xform_1.GetPath())])
prismatic_joint_2.CreateBody1Rel().SetTargets([
    "/World/Robotiq_2F_85/base_link"  # 根据实际路径更新 base_link
])

# 为平移关节添加驱动，并设置阻尼和刚度
for joint in [prismatic_joint_1, prismatic_joint_2]:
    drive = UsdPhysics.DriveAPI.Apply(joint.GetPrim(), "linear")
    drive.CreateDampingAttr(10000.0)
    drive.CreateStiffnessAttr(10000.0)
    physx_joint = PhysxSchema.PhysxJointAPI.Get(stage, str(joint.GetPath()))
    physx_joint.CreateMaxJointVelocityAttr().Set(5.0)

# 添加地面平面
PhysicsSchemaTools.addGroundPlane(
    stage,
    "/World/groundPlane",
    "Z",
    100,
    Gf.Vec3f(0, 0, -0.1),
    Gf.Vec3f(1.0)
)

# 创建圆柱体并应用刚体与碰撞
result, path = omni.kit.commands.execute(
    "CreateMeshPrimCommand",
    prim_type="Cylinder"
)
cylinder_prim = stage.GetPrimAtPath(path)
cylinder_prim.GetAttribute("xformOp:scale").Set((0.05, 0.05, 0.2))
cylinder_prim.GetAttribute("xformOp:translate").Set((0.12, 0, 0))
cylinder_body = UsdPhysics.RigidBodyAPI.Apply(cylinder_prim)
UsdPhysics.CollisionAPI.Apply(cylinder_prim)
mass_api = UsdPhysics.MassAPI.Apply(cylinder_body.GetPrim())
mass_api.CreateMassAttr(0.20)

# 创建物理场景
scene = UsdPhysics.Scene.Define(stage, Sdf.Path("/physicsScene"))
physx_scene_api = PhysxSchema.PhysxSceneAPI.Apply(scene.GetPrim())
# 关闭 GPU 动力学（针对小规模测试场景）
physx_scene_api.CreateEnableGPUDynamicsAttr(False)
```

---

### 在 Physics Authoring Toolbar 中验证

1. 打开 **Physics Authoring Toolbar**（窗口 > Simulation > Physics Authoring Toolbar），并启用 **Physics Inspector**（点击 Settings 按钮，勾选 Physics Inspector）。
    
2. 选中 `Xform`、`Xform_1` 和 `Robotiq_2F_85`，如果未显示属性，可先启动/停止一次仿真，然后点击刷新按钮。
    
3. 拖动 `Joint_X` 驱动目标滑块，观察夹爪在场景中的“散开”效果。
    

> 小贴士：在 `Physics Inspector` 中修改关节属性时，无需运行完整仿真即可实时预览选中对象的物理行为。
---

## 添加关节驱动

1. 选中所有关节，在 Properties 面板依次点击 `Add > Physics > Angular Drive`（Prismatic 关节则选 Linear Drive）。
    
2. 本例中驱动指爪的关节为 `finger_joint` 和 `right_outer_knuckle_joint`。
    
3. 将它们的 Stiffness 设为 0（力控抓取），Damping 设为 5000。
    
4. 设置 Max Force 为 180（控制夹爪闭合时施加的压力）。
    
5. 根据数据表，将指尖角速度上限从线速度换算得到 130°/s，设置关节最大速度为 130。
    

|参数|值|
|---|---|
|Max Joint Velocity|130|
|Max Force|180|
|Damping|5000|
|Stiffness|0|

- 此时闭合时会出现指爪并拢不平行的现象，需为 `[left,right]_outer_finger_joint` 添加微小刚度（Stiffness = 0.05），模拟 Robotiq 手的弹簧结构，保持指爪平行直到接触物体。
    
- 在视频示例中，200 g 圆柱能成功抓取，但当质量达 2.5 kg（最大额定负载）时会打滑。可通过提高物理场景的时间步数（至少 80 Hz）来提升抓取成功率。注意提高步数会增加计算开销，应根据需要适度增大。
    
- 测试并行握持时，将圆柱尺寸 X 缩放到 0.08、X 位置移到 0.13，发现握持不稳。将 Max Force 降到 5 后再试，最大负载下即可成功完成平行抓取并提起。
    

---

## 添加 Mimic 关节

该夹爪通过一个输入命令同时驱动双指，实现方法是在驱动关节上添加 Mimic Joint。

1. 选中 `right_outer_knuckle_joint`，先清空之前的驱动设置。
    
2. 在 Properties 面板点击 `Add > Physics > Mimic Joint (RotZ)`。
    
3. Mimic 设置里，将 Gearing 设为 -1.0，Reference Joint 选为 `finger_joint`。
    
4. 注意：Mimic 关节会继承参考关节的所有驱动特性，再添加独立驱动会造成冲突，故需移除原驱动。
    

> 提示：若关节为 Revolute/Prismatic，旋转轴方向对行为无影响，仍建议与 DOF 轴保持一致。

重新运行仿真，验证左右指同步闭合。

---

## 碰撞网格

默认导入碰撞网格为 Convex Hull，是性能与精度的平衡。对夹爪指尖，我们常用更贴合几何形状的碰撞网格，以免与物体间出现缝隙。

1. 在视图顶部点击“Show By Type > Physics > Colliders > All”可查看碰撞轮廓。
    
2. 选中要修改的部件，在 Property 面板 Physics 区更改 Collider Approximation 为 Convex Decomposition 或其他合适类型。
    

若未看到 Physics/Collider 区，可在 Stage 树中上下切换选中的 prim。

---

## 自碰撞

若测试时发现左右指不发生碰撞，这是 Onshape 导入的默认行为。可在 `/World/Robotiq_2F85` 的 Articulation Root Options 中勾选 “Self-Collision Enabled”。

---

## 保存结果

配置完成后，将修改推回原始资产：

1. 打开 Layer 面板。
    
2. 选中 `Robotiq_2F_85` 及其子 prim，将它们拖入 `Robotiq_2F_85_edit.usd` Layer。
    
3. 点击各 Layer 的 保存图标。
    

可将最终资产与官方资产库中的版本进行对比。

---

## 使用 Omnigraph 控制夹爪

Physics Inspector 操作不够便捷，可借助 Omnigraph 构建简化开闭操作的控制器。在 `Robotiq_2F_85_config.usd` 中已有 `Gripper_Controller` Omnigraph：

1. 打开 Action Graph 窗口（`Window > Graph Editors > Action Graph`）。
    
2. 点击 Edit Action Graph 图标，选择 `/World/Gripper_Controller`。
    
3. 该图接收一个布尔输入，根据输入正/负方向设置关节驱动目标，实现一键开/闭。
    

可参照官方示例视频使用该控制器。

---

**总结**  
本教程介绍了从 Rigged Onshape 文档导入资产、后处理调整以构建正确的仿真层次结构、配置力控驱动与 Mimic 关节的完整流程。过程中我们验证并排查了仿真行为问题，优化性能。同时利用多层编辑（Layered Editing）方法，在保持测试环境的前提下，生成了一个可直接使用的夹爪资产。


---

# *组装机器人和刚体

---

### 学习目标

- 本教程演示如何使用 `isaacsim.robot_setup.assembler` 扩展，将两个 USD 资产组装为单个刚体。
    
- 本教程主要展示 Robot Assembler UI 工具的使用。
    
- 完成本教程后，用户将了解组装后实体的物理机制、何时使用 Robot Assembler，以及在 NVIDIA Isaac Sim 中组装刚体的当前限制。
    

> **5–10 分钟 教程**

---

### 开始使用

要使用本教程，用户应具备两个希望组装为一个的 USD 资产，例如：

- 需要附加到抓手的机械臂。
    
- 需要固定到移动底座上的机器人。
    

此处“机器人”指可归类为“关节联动体”（Articulation）的 USD 资产（具有可控自由度的刚体）。详见 Rigging Robots。

---

### 理解组装实体的机械原理

Robot Assembler 工具允许用户通过物理模拟的固定关节，将两个 USD 资产合并。合并后生成的新 USD 资产可保存，并在加载时无需再次使用 Robot Assembler。固定关节的物理模拟特性是正确使用该扩展的关键：在 Omniverse 中，物理仅在时间轴播放时生效；当物理未激活时，固定关节无任何作用。Robot Assembler 应仅用于那些在播放时需要运动的资产合并场景。例如，将机器人固定在静止的桌面上时，不需要固定关节，用户只需分别放置机器人和桌面，并在播放后它们会保持位置。

此外，由于两部分通过物理模拟的固定关节连接，其相对位置由物理求解器计算。如果在时间轴暂停时，两资产已相对正确放置，则播放后通常很稳定；但若在暂停时将某一部分大幅移动，启动播放时可能出现不稳定。

---

### 使用 Robot Assembler 工具

#### 组装机器人

在 NVIDIA Isaac Sim 工具栏中，依次选择：  
**Tools → Robotics → Asset Editors → Robot Assembler**。  
要使用 Robot Assembler，可先将待组装资产加载到 USD 场景中，然后点击左侧工具栏的 **PLAY** 按钮。
[[3ea8afb6f827c0bae0596a7343c2761a_MD5.jpeg|Open: Pasted image 20250514173745.png]]
![[3ea8afb6f827c0bae0596a7343c2761a_MD5.jpeg]]
您现在可以选择 “Base Robot”（基座机器人）和 “Attach Robot”（附加机器人）。“附加机器人”将成为一个浮动刚体，其位置由其附着在“基座机器人”上的框架决定。

**选中后，可使用 “Set Robot Position” 字段移动两个关节联动体**。这是在组装前后确认它们按预期工作的一种好方法。建议在组装前，通过 Set Robot Position 字段检查两个机器人的关节是否可控。

一旦选择了两个机器人，“Assembly Frame”（组装框架）选项将可用。用户可以选择希望附着机器人的两个框架。选择好合适的框架后，点击 “BEGIN ASSEMBLE”。在下一个面板中，您可以点击 “SELECT ATTACH POINT” 按钮，在舞台上编辑所选的 Xform Prim，来指定这两个框架之间的固定平移和旋转。一旦对固定变换满意，点击 “ASSEMBLE” 即可完成最终组装。
[[cda3a7681b036be8f13db27cf4886a3e_MD5.jpeg|Open: Pasted image 20250514173920.png]]
![[cda3a7681b036be8f13db27cf4886a3e_MD5.jpeg]]

两个机器人现在通过用户定义的固定关节连接在一起。当 “Base Robot”（基座机器人）移动时，“Attach Robot”（附加机器人）会随之移动。要通过代码控制组装后的机器人，用户可以像往常一样使用 `Articulation` 类：使用 “Base Robot” 的 prim 路径来控制基座机器人的自由度（DOFs），使用 “Attach Robot” 的 prim 路径来控制附加机器人的自由度。

在组装框架界面中，您还可以勾选一个选项，将两个关节联动体视为单一机器人。这样一来，两个机器人的所有自由度都可以通过 “Base Robot” 的 prim 路径进行控制，这也能使物理仿真更加稳定。

机器人组装完成后，由于关节系统已更改，您可能需要执行额外测试以验证仿真稳定性。有关调优关节的完整指南，请参见 [Articulation Stability Guide](https://docs.omniverse.nvidia.com/kit/docs/omni_physics/latest/dev_guide/guides/articulation_stability_guide.html)。
[[f1ac2766feb4e375a4e3248db71acfe7_MD5.jpeg|Open: Pasted image 20250514175116.png]]
![[f1ac2766feb4e375a4e3248db71acfe7_MD5.jpeg]]


> [!NOTE] Note
> 
> 组装后机器人组件的名称可能会在场景树中显示为红色。这是由于冗余路径属性导致的已知问题，将在后续版本中修复。


---

### **Assembling Other Rigid Bodies**  
***组装其他刚体

此工具也可用于将任意两个 USD 资产相互附着。为了对固定关节进行物理模拟，被组合的两个刚体必须已应用物理属性。Articulation 已自带物理属性，但 USD 网格（如安装支架）可能仅为可视化网格。Robot Assembler 扩展允许您在“Convert Prim To Rigid Body”面板中，将 USD 资产转换为具有物理属性的刚体。

在 “Base Robot” 和 “Attach Robot” 面板中，您可以筛选出非 Articulation 的刚体。在下方示例视频中，舞台上有一个 UR10 的支架，但它只是一个未应用物理属性的网格。使用 “Convert Prim To Rigid Body” 面板后，该网格将获得物理属性，便可附着到 UR10 上。转换完成后，即可像组装两个机器人一样组装这两个资产。

---

### **Assembly Summary Frame**  
***组装概览面板

当两个刚体组装完成后，“Assembly Summary Frame” 面板会展开，并生成一段代码片段，精确复现用户在 Robot Assembler UI 中的操作。也就是说，如果用户加载了某个 USD 场景并组装了两个刚体，所提供的代码片段在加载同一场景后即可执行相同的组装。以在“组装机器人”教程中将 Fanuc 机械臂与 Allegro 机械手连接为例，生成的代码片段如下：

```python
from omni.isaac.robot_assembler import RobotAssembler, AssembledRobot
from isaacsim.core.prims import Articulation
import numpy as np

base_robot_path = "/World/festo_cobot"
attach_robot_path = "/World/allegro_hand"
base_robot_mount_frame = "/flange"
attach_robot_mount_frame = "/allegro_mount"
fixed_joint_offset = np.array([0.0, 0.0, 0.0])
fixed_joint_orient = np.array([1.0, 0.0, 0.0, 0.0])
single_robot = False

robot_assembler = RobotAssembler()
assembled_robot = robot_assembler.assemble_articulations(
    base_robot_path,
    attach_robot_path,
    base_robot_mount_frame,
    attach_robot_mount_frame,
    fixed_joint_offset,
    fixed_joint_orient,
    mask_all_collisions=True,
    single_robot=single_robot
)

# 可在组装后编辑固定关节：
# offset, orient = assembled_robot.get_fixed_joint_transform()
# assembled_robot.set_fixed_joint_transform(np.array([.3,0,0]), np.array([1,0,0,0]))

# 也可拆解组装体，此后 AssembledRobot 对象将无法使用：
# assembled_robot.disassemble()

# 控制组装后机器人方式取决于 single_robot 标志：
if single_robot:
    # 将两个机器人视为基座路径下的单一 Articulation
    controllable_single_robot = Articulation(base_robot_path)
else:
    # 独立控制两个机器人
    base_robot = Articulation(base_robot_path)
    attach_robot = Articulation(attach_robot_path)
```

---

### **Collision Masking**  
***碰撞屏蔽

当在两个刚体之间创建固定关节时，Robot Assembler 会同时屏蔽它们之间的碰撞，以避免因固定关节而导致的物理约束冲突——即被连接帧的碰撞网格本应发生碰撞，但机器人因固定关节无法移动。若用户仅希望屏蔽固定关节强制碰撞的帧之间的碰撞，而保留其他部位的碰撞，可将上述代码片段中的 `mask_all_collisions` 标志设为 `False`，然后通过 `assembled_robot.mask_collisions(prim_path_a: str, prim_path_b: str)` 手动屏蔽特定碰撞对。此方法仅会屏蔽指定 prim 路径之间的碰撞。

---

### **Summary**  
***总结

在本教程中，我们学到：

- Robot Assembler 工具用于通过用户指定的固定关节，将两个机器人或刚体连接在一起。
    
- Robot Assembler 创建的固定关节经物理模拟，仅在时间轴播放时生效。
    
- 仅在资产需在播放时发生移动时，才需使用 Robot Assembler。
    
- 使用 UI 工具组装后，可通过 Python API 完全复现相同操作。