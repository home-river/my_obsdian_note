
## Isaac Sim Asset Browser [Beta]

**功能总览**

- **用途**：浏览并加载 USD 资产到场景，可选择作为 Reference（引用）或 Payload（有效载荷）。
    
- **入口**：菜单 Window → Browser → Asset Browser。
    

---
![[Isaacsim asset Browser.png]]
### 界面元素说明

下表列出 Asset Browser 中的各个控件名称（中英文对照）及其功能说明。

| 界面元素 (English)           | 界面元素 (中文) | 功能描述                                                  | 编号  |
| ------------------------ | --------- | ----------------------------------------------------- | --- |
| Category Menu            | 分类菜单      | 点击某分类以查看该分类下的所有资产。                                    | 1   |
| Individual Asset         | 单个资产      | 单击：打开左侧选项面板；双击：在视口打开原始 USD 文件；拖拽到视口：以 Payload 方式加载资产。 | 2   |
| Load as Reference Button | 作为引用加载按钮  | 将选中资产作为 Reference 添加到当前场景中。                           | 3   |
| Open File Button         | 打开文件按钮    | 在视口中打开并加载原始 USD 文件，方便直接编辑。                            | 4   |
| Variant Options          | 变量选项      | 如果 USD 文件包含 VariantSets，可在加载前预先选择所需变体。                | 5   |
| Search Bar               | 搜索栏       | 输入关键词，在当前选中分类下筛选资产名称。                                 | 6   |
| File Path                | 文件路径      | 显示所选资产的文件路径，鼠标悬停可查看完整路径。                              | 7   |
| Additional Functions     | 其他功能      | 点击展开更多操作（如下载、复制路径、打开所在目录等）。                           | 8   |
| Option Panel Toggle      | 选项面板切换按钮  | 切换显示或隐藏左侧的资产详情与操作面板。                                  | 9   |

---

### 已知问题与临时配置示例

**已知问题**

1. 搜索仅在“当前分类”内进行，若需全局搜索，请先选中 **All** 分类再输入关键词。
    
2. 切换分类后会重置搜索状态，可能导致结果混淆。
    
3. 默认仅显示 .usd 文件，若要查看图片 (.png/.jpg) 或文档 (.yaml/.txt) 等其他类型，请切换到 Content Browser（需安装 Omniverse Nucleus）或修改过滤设置。
    
4. 非 USD 资产目前只能下载到本地查看，面板内提供下载按钮或右键缩略图下载。
    

**临时配置示例**  
在 Script Editor 中运行以下脚本，可临时调整文件后缀过滤及缩略图显示设置：

```python
import carb.settings
import omni.kit

settings = carb.settings.get_settings()
# 取消后缀过滤，显示所有文件类型
settings.set("/exts/isaacsim.asset.browser/data/filter_file_suffixes", [])
# 显示无缩略图的文件
settings.set("/exts/isaacsim.asset.browser/data/hide_file_without_thumbnails", False)

# 重启 Asset Browser 扩展使配置生效
mgr = omni.kit.app.get_app().get_extension_manager()
mgr.set_extension_enabled_immediate("isaacsim.asset.browser", False)
mgr.set_extension_enabled_immediate("isaacsim.asset.browser", True)
```

> **提示**：资产列表有缓存，重启扩展后首次切换分类会触发刷新并更新缓存。



-----

## Isaac Sim App Selector

**功能总览**

- **用途**：快速启动不同的 Isaac Sim 模式并定位相关文件路径。
    
- **入口**：Isaac Sim 启动器中选择 App Selector 界面。
    

---
[[isaac sim selector.jpeg|Open: Pasted image 20250507113307.png]]
![[isaac sim selector.jpeg]]
### 界面元素说明 / UI Elements

下表列出 Isaac Sim App Selector 中各项控件的中英文对照及其功能说明：

| Ref # | Option                   | Result                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ----- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1     | Isaac Sim Full           | 选择主要的 Isaac Sim 应用程序                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 2     | Isaac Sim Full Streaming | 选择无头（headless）Isaac Sim，并通过 [Isaac Sim WebRTC Streaming Client](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-setup-livestream-webrtc) 进行实时流媒体传输。<br>                                                                                                                                                                                                                                                                                                                                                                                                                |
| 3     | Package Utilities        | - **Package Path**：在文件浏览器或终端中打开包目录，或复制该包的路径。<br>    <br>- **Open In Terminal**：用于打开终端，方便运行 [Python Environment](https://docs.isaacsim.omniverse.nvidia.com/latest/python_scripting/manual_standalone_python.html#isaac-sim-python-environment) 或直接执行启动脚本来运行 Isaac Sim。<br>    <br>- **Clear Caches**：运行脚本清除纹理、着色器和 Kit 缓存，并按提示选择要清除的特定缓存。<br><br><br><br>                                                                                                                                                                                                                                                                      |
| 4     | Additional Arguments     | **Starts the selected app with the added command line flags.**  <br>使用附加的命令行标志启动所选应用。<br><br>**常用命令示例：**<br><br>- `-v`：在终端中输出详细日志<br>    <br>- `--reset-user`：重置用户配置<br>    <br>- `--reset-user --/app/window/width=1920`：设置应用窗口宽度为 1920<br>    <br>- `--reset-user --/app/window/height=1080`：设置应用窗口高度为 1080<br>    <br>- `--reset-user --/app/renderer/resolution/width=1920`：设置渲染器分辨率宽度为 1920<br>    <br>- `--reset-user --/app/renderer/resolution/height=1080`：设置渲染器分辨率高度为 1080<br>    <br>- `--/renderer/activeGpu=0`：指定 Isaac Sim 使用编号为 0 的 GPU<br>    <br><br>**注意**：使用 `--help` 标志运行 Isaac Sim 可查看其他可用命令行参数。          |
| 5     | ROS Options              | - **ROS Bridge Extension**：选择要运行的 ROS Bridge（ROS/ROS2），留空则禁用该功能。<br>    <br>- **Use Internal ROS2 Internal Libraries**：在本地无 ROS2 安装时，选择内部 ROS2 发布版库来运行 Isaac Sim。<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| 6     | Settings                 | - **Other settings（其他设置）**<br>    <br>- **Set selection as new default app（设为新的默认应用）**<br>    <br>    - 与 “Automatically start default app（自动启动默认应用）” 配合使用时非常有用。<br>        <br>    - 先在上方选中一个模式（#1–#4），勾选此项，然后点击 `START` 或 `CLOSE` 来保存该设置。<br>        <br>- **Automatically start default app（自动启动默认应用）**<br>    <br>    - 启动时将跳过 Isaac Sim App Selector，直接运行已设为默认的应用模式。<br>        <br>- **Keep App Selector window opened（保持选择器窗口开启）**<br>    <br>    - 勾选后，按下 **START** 后 App Selector 窗口将保持开启，不会自动关闭。<br>        <br>- **Show startup console（显示启动控制台）**<br>    <br>    - 勾选后，按下 **START** 时将在终端窗口中运行 Isaac Sim 并显示控制台输出。 |
| 7     | Start                    | Press the START button to run the selected mode of Isaac Sim                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

    

---

### 配置文件示例 / Configuration File Example

App Selector 的配置文件位于：

```
~/.local/share/ov/data/Kit/Isaac-Sim_App_Selector/<version>/user.config.json
```

默认内容如下：

```json
{
  "persistent": {
    "ext": {
      "isaacsim.app.selector": {
        "default_app": "omni.isaac.sim",
        "auto_start": false,
        "persistent_selector": false,
        "show_console": true,
        "extra_args": "",
        "ros_bridge_extension": 0,
        "ros_internal_libs": 0
      }
    }
  }
}
```

> **提示**：如需修改默认应用或自动启动，可直接编辑此文件或在界面中调整相关选项。


---

[[2eebeb8e7180ac58391cde06a134e151_MD5.jpeg|Open: Pasted image 20250507140456.png]]
![[2eebeb8e7180ac58391cde06a134e151_MD5.jpeg]]
## Edit选项卡
选项卡下各个选项的意思

| 分类  | 命令                        | 快捷键              | 功能说明                    |
| --- | ------------------------- | ---------------- | ----------------------- |
| 常规  | Undo                      | Ctrl + Z         | 撤销上一步操作                 |
| 常规  | Redo                      | Ctrl + Y         | 恢复上一步被撤销的操作             |
| 常规  | Repeat                    | Ctrl + R         | 重复上一次操作                 |
| 选择  | Instance                  | Shift + Ctrl + I | 选择实例                    |
| 选择  | Duplicate                 | Ctrl + D         | 复制当前选择                  |
| 选择  | Duplicate – All Layers    | —                | 复制所有图层                  |
| 选择  | Duplicate – Collapsed     | —                | 复制折叠状态下的内容              |
| 常规  | Parent                    | P                | 将当前对象设置为某个对象的父级（在层级结构中） |
| 常规  | Unparent                  | Shift + P        | 解除父子关系                  |
| 常规  | Group                     | Ctrl + G         | 将选中的元素组合在一起，形成一个组       |
| 常规  | Ungroup                   | —                | 解除当前的分组，将元素分开           |
| 常规  | Toggle Visibility         | H                | 切换对象的可见性，隐藏或显示它         |
| 常规  | Deactivate                | Shift + Del      | 将当前选中的对象停用              |
| 常规  | Delete                    | Del              | 删除选中的对象                 |
| 常规  | Delete – All Layers       | —                | 删除所有图层                  |
| 常规  | Rename                    | F2               | 重命名选中的对象或元素             |
| 常规  | Hide Unselected           | —                | 隐藏所有未选中的对象，仅显示选中的对象     |
| 常规  | Unhide All                | —                | 显示所有隐藏的对象               |
| 常规  | Focus                     | —                | 使选中的对象获得焦点，通常指将视图聚焦到该对象 |
| 常规  | Capture Screenshot        | F10              | 捕捉当前视图的屏幕截图             |
| 常规  | Toggle Visualization Mode | —                | 切换可视化模式（如线框模式、实物模式等）    |
| 常规  | Preferences               | —                | 打开首选项设置界面，允许用户配置软件的各种选项 |
  
  



  
  
  ![[Create选项卡.jpeg]]
  
  


## Create选项卡

| 选项        | 功能说明                            |
| --------- | ------------------------------- |
| Mesh      | 创建网格对象，用于定义 3D 物体的形状和结构。        |
| Shape     | 创建基础几何体（立方体、球体、圆柱体等），构建模型的基础部分。 |
| Lights    | 添加光源对象，为场景提供光照效果。               |
| Audio     | 添加音频对象，在 3D 应用或游戏中加入声音元素。       |
| Camera    | 创建摄像机对象，用于设定观察场景的视角。            |
| Scope     | 设置视角范围或特定观察区域（具体功能视软件而定）。       |
| Materials | 定义材料属性（颜色、纹理、反射等），决定物体表面效果。     |
| Xform     | 调整物体变换（位置、旋转、缩放等）。              |
| Graphs    | 创建用于数据或函数可视化的图形对象。              |
| Physics   | 添加物理组件（重力、碰撞等），用于模拟物体的物理行为。     |

**Assets 部分**：

| 资产类型         | 功能说明                                  |
| ------------ | ------------------------------------- |
| Robots       | 创建机器人模型或相关功能组件。                       |
| Environments | 构建场景环境元素，如地面、天空、背景等。                  |
| Sensors      | 添加仿真传感器（温度、光照、运动等），用于模拟感应功能。          |
| April Tags   | 添加用于视觉识别的 AprilTag 标签，常见于机器人定位与导航系统中。 |
  
  
  ### Mesh子选项

|选项|功能说明|
|---|---|
|Cone|创建一个圆锥体|
|Cube|创建一个立方体|
|Cylinder|创建一个圆柱体|
|Disk|创建一个圆盘|
|Plane|创建一个平面|
|Sphere|创建一个球体|
|Torus|创建一个圆环（甜甜圈形状）|
|Settings|打开网格创建的参数设置或更多选项|
  ###  **Shape 选项卡**

|选项|功能说明|
|---|---|
|Capsule|创建胶囊形状|
|Cone|创建圆锥形状|
|Cube|创建立方体形状|
|Cylinder|创建圆柱形状|
|Sphere|创建球体形状|
|High Quality|高质量选项|

 ### **Light 选项卡**

|选项|功能说明|
|---|---|
|Cylinder Light|圆柱形光源|
|Disk Light|圆盘形光源|
|Distant Light|远距离光源|
|Dome Light|半球形光源|
|Rect Light|长方形光源|
|Sphere Light|球形光源|

### **Audio 选项卡**

|选项|功能说明|
|---|---|
|Spatial Sound|空间音频|
|Non-Spatial Sound|非空间音频|
|Listener|音频监听器|

### **Material 选项卡**

|选项|功能说明|
|---|---|
|OmniSurface|通用表面材质|
|OmniGlass|玻璃材质|
|OmniPBR|PBR 材质（物理基础渲染）|
|USD Materials|USD 材质|
|USD Preview Surface|USD 预览表面材质|

###  **Graphs 选项卡**

|选项|功能说明|
|---|---|
|Action Graph|动作图|
|Push Graph|推送图|
|Lazy Graph|懒加载图|

###  **Physics 选项卡**

|选项|功能说明|
|---|---|
|Physics Scene|物理场景|
|Ground Plane|地面平面|
|Joint|连接体|
|Physics Material|物理材质|
|Collision Group|碰撞组|
|Particle System|粒子系统|
|Vehicle|车辆模型|
|Tire Friction Table|轮胎摩擦表|

### **Robot 选项卡**

|选项|功能说明|
|---|---|
|Asset Browser|资产浏览器|
|Ant|蚂蚁机器人示例|
|Boston Dynamics Spot (Quadruped)|波士顿动力四足机器人|
|Franka Emika Panda Arm|法兰卡·艾米卡·熊猫臂|
|Humanoid|人形机器人示例|
|Nova Carter with Sensors|带传感器的 Nova Carter|
|Quadcopter|四旋翼飞行器|

### **Environment 选项卡**

|选项|功能说明|
|---|---|
|Asset Browser|资产浏览器|
|Black Grid|黑色网格示例|
|Flat Grid|扁平网格示例|
|Simple Room|简单房间示例|
  
  ### **Sensor 子选项卡**

| 选项                       | 功能说明                   |
| ------------------------ | ---------------------- |
| Asset Browser            | 资产浏览器，用于浏览和管理传感器资产     |
| **Generic Sensors**      | 常见传感器类别                |
| Camera and Depth Sensors | 摄像头和深度传感器，用于捕捉视觉和深度数据  |
| RTX Lidar                | RTX 激光雷达传感器，用于扫描环境     |
| Contact Sensor           | 接触传感器，用于检测物体接触         |
| Imu Sensor               | 惯性测量单元传感器，用于感应运动和方向    |
| PhysX Lidar              | PhysX 激光雷达传感器，用于3D环境扫描 |
| LightBeam Sensor         | 光束传感器，用于检测和测量光线        |
  
  
  ## **Windows 选项卡**

|选项|功能说明|
|---|---|
|**Browsers**|浏览器选项，用于管理和查看内容|
|**Examples**|示例场景和资源|
|**Graph Editors**|图形编辑器，常用于编辑行为图等|
|**Viewports**|显示视图窗口，用于观察场景和模型|
|**Collection**|项目集合，用于管理和分类资源|
|**Commands**|控制命令窗口|
|**Console**|控制台，显示调试信息和日志|
|**Hotkeys**|热键设置，配置快捷键|
|**Layer**|层级管理，用于控制各层内容显示|
|**Physics Stage Settings**|物理阶段设置|
|**Property**|查看或编辑对象属性|
|**Render Settings**|渲染设置|
|**Script Editor**|脚本编辑器，用于编辑自定义脚本|
|**Simulation Output Settings**|仿真输出设置|
|**Stage**|设置舞台或环境的基础配置|
|**Extensions**|扩展管理，安装和管理插件|
  
### **Browser 子选项卡**

|选项|功能说明|
|---|---|
|**Content**|内容管理，显示可用的所有资源|
|**Isaac Sim Assets**|Isaac Sim 资产，包含与机器人和仿真相关的资源|
|**Materials**|材质，包含与材质相关的资源|
|**NVIDIA Assets**|NVIDIA 资产，包含与 NVIDIA 相关的资源|
|**SimReady Explorer**|SimReady 浏览器，用于快速查看和管理资源|
### **Examples 子选项卡**

|选项|功能说明|
|---|---|
|**Physics Examples**|物理相关示例|
|**Robotics Examples**|机器人相关示例|
|**Warp Sample Scenes**|Warp场景示例，用于测试和演示|

### **Graph Editors 子选项卡**

|选项|功能说明|
|---|---|
|**Action Graph**|动作图，用于控制对象的行为|
|**Generic Graph**|通用图，用于处理不同类型的节点|
|**MDL Material Graph**|MDL材质图，用于编辑材质和纹理|

### **Viewports 子选项卡**

| 选项             | 功能说明      |
| -------------- | --------- |
| **Viewport 1** | 显示第一个视图窗口 |
| **Viewport 2** | 显示第二个视图窗口 |
## *Tools 选项卡*

| 选项                  | 功能说明              |
| ------------------- | ----------------- |
| **Animation**       | 动画相关工具            |
| **Replicator**      | 用于合成数据生成和处理       |
| **Physics**         | 物理模拟相关工具          |
| **Robotics**        | 机器人相关工具           |
| **USD**             | USD 文件格式和场景管理工具   |
| ***工具栏***           | ***说明***          |
| **Main ToolBar**    | 主要工具栏，包含基础操作和功能   |
| **Physics Toolbar** | 物理相关的工具栏，提供物理仿真功能 |
### **Animation 子选项卡**

|选项|功能说明|
|---|---|
|Simplify Animation Curve|简化动画曲线|
|Stage Recorder|记录动画的阶段信息|
|Timesamples to Curves|将时间样本转换为曲线|

### **Replicator 子选项卡**

|选项|功能说明|
|---|---|
|Replicator YAML|生成并配置Replicator的YAML文件|
|Semantics Schema Editor|语义架构编辑器|
|Synthetic Data Recorder|合成数据记录器|

### **Physics 子选项卡**

|选项|功能说明|
|---|---|
|Collision Groups Filtering Matrix|碰撞组过滤矩阵|
|Physics API Editor|物理 API 编辑器|
|Physics Inspector|物理检查器|
|PhysX Character Controller|PhysX 控制器|

### **Robotics 子选项卡**

|选项|功能说明|
|---|---|
|Asset Editors|资产编辑器|
|OmniGraph Controllers|OmniGraph 控制器|
|Block World Generator|块世界生成器|
|Occupancy Map|占用图|
|Grasp Editor|抓取编辑器|
|Lula Robot Description Editor|Lula机器人描述编辑器|

### **USD 子选项卡**

|选项|功能说明|
|---|---|
|USD Paths|显示和管理 USD 路径|
|Variant Presenter|变体展示，用于变体的展示和选择|
|Variant Editor|变体编辑器|


## **Utilities 选项卡**

|选项|功能说明|
|---|---|
|**OmniGraph Tool Kit**|提供图形工具包，用于可视化和编辑 OmniGraph 网络|
|**Physics Debugger**|物理调试器，帮助调试物理相关的错误和问题|
|**Profiler**|性能分析工具，提供程序执行时的性能数据|
|**Statistics**|显示统计数据，通常用于查看性能和资源消耗|
|**Generate Extension Templates**|生成扩展模板，快速创建自定义功能|
|**Registered Actions**|注册的操作，管理和查看已经注册的操作|


## **Layout 选项卡**

|选项|功能说明|
|---|---|
|**UI Toggle Visibility**|切换界面可见性，快捷键：F7|
|**Fullscreen Mode**|切换全屏模式，快捷键：F11|
|**Default Layout**|恢复默认布局，快捷键：Ctrl + 1|
|**Visual Scripting**|切换到可视化脚本布局，快捷键：Ctrl + 6|
|**Save Layout**|保存当前布局|
|**Load Layout**|加载已保存的布局|
|**Quick Save**|快速保存当前布局，快捷键：Ctrl + 7|
|**Quick Load**|快速加载布局，快捷键：Ctrl + 8|
## **Help 选项卡**

|选项|功能说明|
|---|---|
|**Examples**|示例资源，帮助用户快速入门|
|- **Physics Examples**|物理相关的示例|
|- **Robotics Examples**|机器人相关的示例|
|- **Warp Sample Scenes**|Warp场景示例，适用于仿真测试和演示|
|**Isaac Sim Reference**|Isaac Sim 相关文档|
|- **About**|关于Isaac Sim的信息|
|- **Online Guide**|在线帮助文档|
|- **Online Forums**|在线论坛，用户交流的地方|
|- **Scripting Manual**|脚本手册，关于如何编写脚本的文档|
|**Omniverse Reference**|Omniverse平台相关文档|
|- **Kit Programming Manual**|Kit编程手册，讲解如何使用Omniverse Kit|
|- **Omni UI Docs**|Omni UI文档，关于界面和工具的文档|
|**Physics Reference**|物理编程文档|
|- **Physics Programming Manual**|物理编程手册，介绍物理引擎的使用|
|**Warp Reference**|Warp平台相关文档|
|- **Getting Started**|入门指南，帮助用户开始使用Warp|
|- **Documentation**|Warp文档，详细的Warp功能和教程|
|**USD**|USD 相关文档|
|- **USD Reference Guide**|USD参考指南，详细讲解USD文件格式和使用|
|**F1**|快捷键F1，直接打开帮助文档|



---
## Environment Setup

Isaac Sim 是基于 NVIDIA Omniverse 构建的，并使用 Omniverse Kit 提供的工具。Omniverse Kit 自带一个默认的 UI，使您能够轻松编辑 USD stage。在本教程中，您将学习设置环境、在 USD stage 上添加和编辑简单对象及其属性、使用关节和连杆绑定刚体，以及添加相机和传感器的基本步骤。目标是帮助您掌握导航 Isaac Sim 的基础技能，熟悉常用术语，并使用 GUI 构建环境和设置机器人。

## Learning Objectives

本教程将指导您使用 Isaac Sim GUI 提供的工具，构建一个启用物理仿真的虚拟世界，包括：

- 设置全局舞台属性
    
- 设置全局物理属性
    
- 添加地面平面
    
- 添加灯光
    

## Getting Started

### Prerequisites

要开始一个干净的 Isaac Sim stage，请在 “File” 菜单中单击 “New”。该 stage 默认包含一个 World Xform 和一个 defaultLight，可在左侧 Stage 树的 Stage 标签下找到。

## Setting up Stage Properties

在向舞台添加任何内容之前，先确认当前的舞台属性设置符合预期规范。

1. 转到 “Edit > Preferences” 打开偏好设置面板。
    
2. 在面板左侧的列中，浏览按类别分组的 Omniverse Kit 各种设置。
    
3. 在左侧列中选择 “Stage”，查看以下属性：
    
    - **Up 轴**：默认在 Isaac Sim 中为 Z。如果您的资源在其他软件中创建时使用了不同的上轴，导入时可能会出现旋转。
        
    - **阶段单位**：2022.1 之前的 Isaac Sim 版本使用厘米，但现在默认是米。然而，Omniverse Kit 的默认单位仍为厘米。如遇到 USD 单位偏差 100 倍，请留意。
        
    - **默认旋转顺序**：按 X、Y、Z 的顺序执行旋转。
        
![[场景设置.png]]
## Creating the Physics Scene

要添加用于模拟真实物理（包括重力和物理时间步长）的 Physics Scene：

1. 在菜单栏中单击 “Create > Physics > Physics Scene”。
    
2. 验证 PhysicsScene 已添加到 Stage 树。
    
3. 单击该条目以查看其属性。您会看到重力指向 -Z 方向，大小为 9.8，提醒您长度单位为米。
    

除非模拟数百个刚体和机器人，否则使用 CPU 求解器比 GPU 更高效，因此本教程中：

- 禁用 GPU dynamics，并在 Physics Scene 的属性页中将 Broadphase 类型设置为 MBP。
    

![[场景设置2.png]]

## Adding a Ground Plane

地面平面可防止启用物理的对象坠落到平面下方。尽管可视区域仅覆盖每个方向 25 米，但其碰撞属性会无限延伸。

1. 在菜单栏中单击 “Create > Physics > Ground Plane” 添加地面平面。
    
2. 单击眼睛图标并选择 “Grid” 打开网格，以便更容易看到平面。
    

## Lighting

每个新舞台都会预先包含一个 defaultLight，否则您将看不到任何内容。该默认灯光是舞台中 Environment xform 的子节点，可在 Stage 树中找到。

要创建额外的聚光灯：

1. 如果尚未添加地面平面，请先执行 “Create > Physics > Ground Plane”，以便能看到灯光反射。
    
2. 转到 “Create > Light > Sphere Light”。
    
3. 将灯光向上移动 7 个单位，并在 X、Y 轴上将旋转设置为 0，使其朝下。
    
4. 在属性面板中，展开 “Main > Color”，单击颜色栏并选择所需颜色。
    
5. 将 “Main > Intensity” 设置为 1e6；将 “Main > Radius” 设置为 0.05；在 “Shaping > cone:angle” 中将聚光角度限制为 45 度；在 “Shaping > cone:softness” 中将边缘柔化设置为 0.05。
    
6. 为了更好地对比，可在 defaultLight 的属性面板中将 “Main > Intensity” 调低至 300。
    
![[场景设置3.png]]

## Summary

本教程介绍了创建适合物理仿真和测试 Isaac Sim 的虚拟世界所需的基本步骤，涵盖了：

- 添加地面平面、灯光和物理场景。
    

## Next Steps

继续阅读 “Add Simple Objects” 教程，了解如何向 Isaac Sim 添加简单对象并编辑其属性。

## Further Learning

若想深入了解并使用更丰富的世界构建工具，可参考我们的姊妹产品 Omniverse Composer。


---

## 添加简单对象 (Add Simple Objects)

本教程将引导您使用基本的 GUI 功能在舞台上添加对象，并介绍如何检查和修改它们的物理属性和材质属性。

## 学习目标 (Learning Objectives)

本教程涵盖以下内容：

- 添加并操作简单形状
    
- 为对象启用物理属性
    
- 检查碰撞属性
    
- 编辑摩擦等物理参数
    
- 编辑颜色与反射率等材质属性
    

## 入门 (Getting Started)

### 前提条件 (Prerequisites)

在开始本教程之前，请先完成 **Environment Setup**。



---


## 添加简单对象 (Add Simple Objects)

本教程将引导您使用基本的 GUI 功能在舞台上添加对象，并介绍如何检查和修改它们的物理属性和材质属性。

### 学习目标 (Learning Objectives)

本教程涵盖以下内容：

- 添加并操作简单形状
    
- 为对象启用物理属性
    
- 检查碰撞属性
    
- 编辑摩擦等物理参数
    
- 编辑颜色与反射率等材质属性
    

### 入门 (Getting Started)

***前提条件 (Prerequisites)

在开始本教程之前，请先完成 **Environment Setup**。[[GUI#Environment Setup]]

### 向场景添加对象 (Adding Objects to the Scene)

添加对象到舞台的方式有很多，基本都是在 Stage Context Tree 中定义一个 USD primitive。我们的目标是创建一个简单的双轮机器人，因此先用基础形状构建车身和车轮。

_创建车身 (Creating the Body)_

1. 在菜单栏中点击 **Create > Shapes > Cube**，您会在视口中看到一个立方体以及 “Move gizmo”（红、蓝、绿箭头）。
    
2. 点击并拖拽蓝色箭头，将立方体提升到地面平面之上。
    
3. 在应用左侧，点击 **Scale 图标**（或选中立方体后按 **R**），激活缩放控件。
    
4. 点击并拖拽红色控件，将立方体沿 X 轴方向缩放。
    
5. 在 **Property **窗口中展开 **Transform > translate**，将平移设置为 `(0, 0, 1)`，然后在 **scale** 中设为 `(1, 2, 0.5)`。
    

***创建车轮 (Creating the Wheels)

1. 同样地，通过 **Create > Shapes > Cylinder** 添加一个圆柱体。
    
2. 在 **Geometry** 中，将 **Radius** 设为 `0.5`，将 **Height** 设为 `1.0`。
    
3. 将其放置到 `x = 1.5, z = 1.0`，并绕 Y 轴旋转 `90°`。
    
4. 在 Stage Tree 中右键该圆柱，选择 **Duplicate**。将复制体移动到 `x = -1.5`，保持其他参数不变。
    ![[添加机器人1.jpeg]]

> **Important:**  
> 在 2022.2.0 版本中，直接在属性窗口中输入欧拉角方向有个已知的 bug，可以改为输入四元数，或直接使用 gizmo 进行旋转。

### 添加物理属性 (Adding Physics Properties)

到目前为止，立方体和圆柱体仅是可视 prim，并未附加任何物理或碰撞属性。按下 **Play** 后，它们不会因重力而移动。我们接下来将它们转为带碰撞的刚体。

1. 在 Stage Tree 中选中 Cube 和两个 Cylinder（按住 **Ctrl+Shift** 或若为连续项则按 **Shift**）。
    
2. 在 **Property** 选项卡中点击 **+ Add**，选择 **Physics > Rigid Body with Colliders Preset**。
    
3. 按 **Play**，验证所有对象会下落到地面。
    

“Rigid Body with Colliders Preset” 会自动添加 **Rigid Body API** 和 **Collision API**。您也可以单独添加或移除这些 API：

- 在 **Property** 面板中下拉找到 **Rigid Body** 和 **Collider** 模块。
    
- 再次点击 **+ Add** 可单独添加。
    
- 点击模块右上角的 **X** 可删除。
    
![[添加机器人2.webp]]
***检查碰撞网格 (Examine Collision Meshes)

要在视口中查看碰撞网格轮廓：

1. 在视口上方点击眼睛图标，选择 **Show By Type > Physics > Colliders > All**。
    
2. 验证紫色轮廓是否包围了所有带有碰撞的对象（包括立方体、圆柱体和地面平面）。
    
[[添加机器人3.jpeg|Open: Pasted image 20250508081136.png]]
![[添加机器人3.jpeg]]
### 添加接触与摩擦参数 (Adding Contact and Friction Parameters)

要修改摩擦属性，需先创建物理材质，然后将其分配给目标对象。

1. 在菜单栏中点击 **Create > Physics > Physics Material**。
    
2. 在弹出框中选择 **Rigid Body Material**，在 Stage Tree 中会出现一个新的 **PhysicsMaterial**。
    
3. 在该 **PhysicsMaterial** 的属性面板中调节摩擦系数、恢复系数等参数。
    
4. 将材质应用到对象：
    
    - 在 Stage Tree 中选中目标对象。
        
    - 在 **Property** 选项卡中找到 **Materials on Selected Model**，从下拉列表中选择刚创建的物理材质。
        
[[添加机器人4.jpeg|Open: Pasted image 20250508081319.png]]
![[添加机器人4.jpeg]]
### 材质属性 (Material Properties)

虽然对象会反射先前添加的聚光灯颜色，但默认并未赋予真正的材质颜色。关闭聚光灯可验证。
[[添加机器人5.jpeg|Open: Pasted image 20250508081542.png]]
![[添加机器人5.jpeg]]
要更改对象颜色，同样需要创建并分配材质：

1. 点击 **Create > Materials > OmniPBR**，重复两次。
    
2. 在 Stage Tree 中右键新材质，将它们重命名为 `body` 和 `wheel`。
    
3. 在各自的材质 **Property** 面板中，通过 **Materials on Selected Models** 指定对应的刚体。
    
4. 选中材质，调整 **Shader > Albedo > Base Color**，以及 **Reflectivity**, **Roughness** 等属性。
    
5. 验证车身和车轮的颜色已随设置改变。
    [[添加机器人6.jpeg|Open: Pasted image 20250508081623.png]]
![[添加机器人6.jpeg]]

### 总结 (Summary)

本教程讲解了如何在 GUI 中：

- 添加并操作基础形状
    
- 编辑物理属性、碰撞属性和材质属性
    

***下一步 (Next Steps)

- 继续阅读 **Working with USD**，学习如何保存世界并以 USD 格式加载资产。
    
- 前往 **Assemble a Simple Robot**，了解如何将这些几何体组装成可移动的车辆。


---

## 装配一个简单机器人

NVIDIA Isaac Sim 的 GUI 界面功能与 NVIDIA Omniverse™ USD Composer 中用于世界构建的功能相同。在本系列教程中，我们将重点介绍与机器人应用最相关的 GUI 功能。对于更复杂的一般世界创建，请参阅 Omniverse Composer。

在本教程中，我们将为一个由三段刚体和两个 Revolute Joint（旋转关节）组成的简单“机器人”绑定铰链，以介绍关节（joints）和构件（articulations）的基本概念。我们将把在 **Add Simple Objects** 中添加到舞台（stage）中的对象，变成一个具有矩形机身和两个圆柱形车轮的模拟移动机器人。虽然对于通过 **Importing your Onshape Document** 或 **URDF Importer Extension** 导入的机器人，此步骤并非必需，但这些概念对于调优机器人和使用 articulations 组装对象非常重要。

### 学习目标

本教程演示如何为双轮移动机器人绑定铰链，并涵盖以下内容：

- 组织 **Stage Tree** 层级结构
    
- 在两个刚体之间添加关节（joints）
    
- 添加关节驱动（joint drives）及其属性
    
- 添加构件（articulations）
    
- 通过 Articulation Velocity Controller 移动机器人
    

### 入门准备

**先决条件**

- 完成 **Add Simple Objects**。
    

### 添加关节

1. 如果你已在 GUI Tutorials 后保存了自己的 `mock_robot.usd`，可通过 **File > Open** 打开该文件；否则，加载资产浏览器（asset browser）中位于 **Samples > Rigging > MockRobot > mock_robot_no_joints** 的资源。**不要**以引用（reference）方式加载，因为需要对文件进行永久修改。
    
2. 为了便于管理，在 **Stage Tree** 中右键单击 **Create > Scope** 来创建一个 Scope，用于存放关节。
    
3. 要在两个刚体之间添加 Revolute Joint，先在 **Stage Tree** 中先后选中父体（例如 `body/body`），再按住 `Ctrl` 选中子体（例如 `wheel_left/wheel_left`）。
    
4. 选中后，右键单击并选择 **Create > Physics > Joints > Revolute Joint**。此时，`RevoluteJoint` 会出现在 **wheel_left** 下，将其重命名为 `wheel_joint_left`。
    
5. 在 **Property** 选项卡中，确认 **body0** 为 `/mock_robot/body/body`（立方体），**body1** 为 `/mock_robot/wheel_left/wheel_left`（圆柱体）。
    
6. 设置 **Local Rotation 0** 为 `0.0`，**Local Rotation 1** 为 `-90.0`，以修正两个模型之间的旋转错位。
    
7. 将关节的 **Axis** 更改为 `Y`。
    
8. 对右侧车轮重复步骤 3–7，创建并配置 `wheel_joint_right`。
    
![[组装机器人1.jpeg]]
添加关节后，按 **Play**，你会发现原本会各自下落的三个刚体现在像通过 Revolute Joint 连接在一起一样联合运动。按住 `Shift` 键并在视口中拖拽机器人任意部位，可直观地看到它们协同移动。
![[组装机器人2.webp]]
### 添加关节驱动

添加关节只是建立了机械连接，要对关节进行控制并驱动它们，需添加 Drive API。

1. 在 **Stage Tree** 中选中两个关节（`wheel_joint_left` 和 `wheel_joint_right`）。
    
2. 在 **Property** 选项卡中点击 **+ Add**，选择 **Physics > Angular Drive**，即可为两个关节同时添加驱动。
    
3. 配置驱动模式：
    
    - **Position Control**（位置控制）：将 **Stiffness** 设置为较高值，将 **Damping** 设置为较低或 `0`。
        
    - **Velocity Control**（速度控制）：将 **Damping** 设置为较高值，将 **Stiffness** 设置为 `0`。
        
4. 对于车轮关节，更适合使用速度控制模式，因此将两侧车轮的 **Damping** 设为 `1e4`，**Target Velocity** 设为 `200`。
    
5. 如需限制关节行程，可在 **Raw USD Properties > Lower Limit** 和 **Upper Limit** 中设置范围。
    
6. 按 **Play**，即可看到模拟移动机器人驶离原地。
    
![[组装机器人3.webp]]
### 添加构件

虽然直接驱动关节可以移动机器人，但使用 articulations（构件）能获得更高的仿真精度、减少关节误差，并能处理更大的质量比。要将一串连接的刚体和关节转成单个构件，需要添加 Articulation Root 组件。

根据 Physics Core: Articulation 中对构件树的定义：

> - 对于固定基底（fixed-base）构件，将 Articulation Root Component 添加到：
>     
>     1. 将构件基底固定到世界的固定关节，或
>         
>     2. USD 层级中该固定关节的某个祖先。
>         
> - 对于浮动基底（floating-base）构件，将 Articulation Root Component 添加到：
>     
>     1. 构件的根刚体链接，或
>         
>     2. USD 层级中该根链接的某个祖先。
>         

在本教程中，按以下步骤为机器人添加构件根：

1. 在 **Stage Tree** 中选中 `mock_robot`。
    
2. 在 **Property** 选项卡中点击 **+ Add**。
    
3. 选择 **Physics > Articulation Root**。
    
4. 验证结果应与资产浏览器中 **Samples > Rigging > MockRobot > mock_robot_rigged** 提供的资源一致。
    

### 添加控制器

构件就绪后，可使用工具测试机器人的运动：

1. 转到 **Tools > Robotics > Omnigraph Controllers > Joint Velocity**，添加一个 **Articulation Velocity Controller** Graph。该图可通过设置各关节的目标速度来控制机器人移动。
    
2. 点击 “Add” 按钮，为 **Robot Prim**（或 **Articulation Root**）参数选择带有 Articulation Root API 的 prim，此处为 `/mock_robot`。
    
3. 点击 **OK**，创建 Graph。
    
4. 按 **Play** 启动仿真，若当前已有默认速度目标，机器人会立即朝目标运动。
    
5. 若需修改关节命令，可在 **Stage Tree** 中展开 `/World/Graphs/velocity_controller/JointCommandArray`，选中 **JointCommandArray**，然后在 **Property** 选项卡的 **Raw USD Properties** 中调整 **input0**、**input1** 等参数。
    

> **注意**  
> Articulation controllers 使用弧度（radians），而在选择单个关节时 **Property** 选项卡下的 Drive API 默认属性以度（degrees）为单位。

此机器人也可使用 Differential Controller 控制。有关 Omnigraph Controller 快捷方式，请参阅 **Commonly Used Omnigraph Shortcuts**。
[[组装机器人4.jpeg|Open: Pasted image 20250508094032.png]]
![[组装机器人4.jpeg]]
### 小结

在本教程中，你已学习如何：

- 使用关节（joints）连接刚体
    
- 添加关节驱动（joint drives）以控制关节
    
- 将关节链转为构件（articulations）
    
- 使用 Articulation Velocity Controller 控制机器人
    

#### 后续步骤

- 继续学习 **Add Camera and Sensors**，以了解如何为车辆添加摄像头。
    

#### 相关阅读

- **Physics Core: Articulation**（关于关节和构件仿真的更多细节）




---
## 添加相机与传感器 (Add Camera and Sensors)

Isaac Sim 提供多种传感器，用于感知环境和机器人状态。本教程将指导您如何为模拟机器人挂载相机传感器，该流程同样适用于其他传感器。有关相机及其他传感器的详细信息，请参阅我们的高级教程和手册中的 Sensor Extensions。

### 学习目标 (Learning Objectives)

本教程将详细讲解如何：

- 添加相机
    
- 将相机附加到几何体
    

### 入门 (Getting Started)

***前提条件 (Prerequisites)

- 完成 Getting Started 教程及 GUI Tutorials 系列中的前序教程
    
- 了解相机坐标系和轴的基础知识
    

> **注意**  
> 建议使用 Isaac/Samples/Rigging/MockRobot/mock_robot_rigged.usd 文件开始本教程，以获得标准化的场景设置。

---

### 添加相机 (Adding Camera)

1. 在菜单栏选择 **Create > Camera**。此时舞台树中会出现一个 Camera 节点，并在视图中显示一个灰色的相机视锥线框。
    
2. 和其他对象一样，可以直接移动或旋转该 Camera 的 transform。
    
3. 或者，可先在视口中调整至所需视角，再点击视口左上角的 **Camera** 按钮，选择 **Camera > Create Camera from View**。新相机会出现在舞台树中，且可在 **Camera** 按钮的列表中进行切换。
    

---
![[添加相机.png]]
### 检查相机 (Inspect the Camera)

1. 选择 **Tools > Robotics > Camera Inspector**，打开 Camera Inspector Extension。
    
2. 在下拉菜单中确认已列出相机，必要时点击 **Refresh** 按钮以发现新相机。
    
3. 选中要检查的相机，可在新建的视口中实时查看相机画面，并根据需要获取或设置相机姿态。
    

---

### 将相机附加到机器人 (Attach a Camera to Robot)

1. 将新建相机重命名为 `car_camera`。
    
2. 为便于定位相机视角，在菜单栏选择 **Window > Viewports > Viewport 2**，打开第二个视口窗口。
    
3. 保持一个视口为 **Perspective** 视图，另一个切换至 `car_camera` 视角：点击该视口顶部的 **Cameras** 菜单，选择 **Camera > car_camera**。
    
4. 在 Stage Tree 中将 `car_camera` 拖拽到 `body` 下，令相机与机身一同移动。
    
5. 调整 `car_camera` 的 Transform 参数使其略向下、面向前方：
    
    - Translation: `x = -0.6, y = 0, z = 2.2`
        
    - Orientation: `x = 0, y = -80, z = -90`
        
    - Scale: `x = 1, y = 1, z = 1`
        
6. 在 Perspective 视口中验证：一侧为车载相机视图，另一侧显示相机相对于机器人机身与地面的整体位置与朝向。
    
7. 按 **Play**，相机将随机器人一起运动。
    

> **重要提示**  
> 如果在展示时直接移动相机视图，会修改相机自身属性。推荐方法是：
> 
> 1. 在机身下创建一个新的空 Prim，并设置好相对偏移；
>     
> 2. 将相机附加到该 Prim。  
>     如此一来，即便不慎移动了相机，只需将其相对于父 Prim 的位置和朝向参数重置为零，即可恢复初始安装位置。
>     

---

### 总结 (Summary)

本教程中，您学习了如何：

- 使用 Camera Inspector Extension 实时查看和配置相机
    
- 添加相机并将其挂载在机器人机身上
    

---

### 下一步 (Next Steps)

- 前往 **Omniverse Script Editor**，学习如何在 GUI 中运行 Python API。
    
- 如需为更复杂的机器人添加相机和传感器，请参考 **Rig a Mobile Robot** 教程。



---
## Create 菜单

下表列出 Isaac Sim 中 **Create** 选项卡下常用的场景创建与布局功能。

| Menu Item    | 操作说明                             |
| ------------ | -------------------------------- |
| Light        | 创建自定义光源。（详情见光照文档）                |
| Camera       | 向场景中添加相机。                        |
| Material     | 基于模板创建 Omniverse 材质。             |
| Physics      | 添加物理相关组件（刚体、碰撞体、关节等）。（详情见物理文档）   |
| Sensors      | 向场景中添加相机、基于 RTX、PhysX 或物理的其他传感器。 |
| Robots       | 向场景中添加各类机器人资源。                   |
| Environments | 向场景中添加各类环境资源（如室内场景、户外地形等）。       |
| April Tag    | 向场景中添加 AprilTag 识别标签资源。          |

---
## Replicator 菜单

下面列出了在 Isaac Sim 中 **Tools → Replicator** 下常用的一组工具和扩展，用于 omni.replicator 的生成、可视化和合成数据记录。

| UI Element              | 操作说明                                                   |
| ----------------------- | ------------------------------------------------------ |
| Semantics Schema Editor | 添加语义标注信息。（详情见 Semantics Schema Editor 文档）              |
| Synthetic Data Recorder | 合成数据记录器，用于录制传感器输出及元数据。（详情见 Synthetic Data Recorder 文档） |
| ReplicatorYAML          | 基于 Replicator YAML 配置生成数据集。                            |
| Start                   | 启动随机化流程并将结果写入磁盘（omni.replicator）。                      |
| Step                    | 执行一次随机化操作并将结果写入磁盘（omni.replicator）。                    |
| Preview                 | 执行一次随机化迭代但不写入磁盘，仅在视图中预览效果（omni.replicator）。            |



---
## 选择模式 (Selection Modes)

Omniverse 中有两种选择模式：**Selection by type** 和 **Selection by model kind**。

- **Selection by type** 会在场景树中尽可能深入地选中被点击的 Prim。
    
- **Selection by model kind** 则从被点击的 mesh 开始，向上遍历场景树，直到遇到用户在属性窗口中指定的 Model Kind 为止。  
    每个 Prim 的 **type** 在创建时即已确定，不可更改；而 **kind** 是可在属性窗口中由用户设置的属性。​[Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/gui/selection-modes.html)
    

### 切换选择模式 (Changing Selection Mode)

点击视口右侧工具栏最顶部的图标，可在 **Selection by type** 和 **Selection by model kind** 之间切换：​[Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/gui/selection-modes.html)
![[选择模式1.png]]
> **Note:**
> 
> - 按 `T` 热键可快速切换选择模式。
>     
> - 按 `Q` 热键可切换到 “Select from Transform” 模式。
>     

右键点击该按钮，还会弹出选择模式的展开菜单，提供针对类型和 Model Kind 的筛选选项。​[Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/gui/selection-modes.html)
![[选择模式2.png]]
### 按类型选择 (Type Selection)

当按钮显示为 **两个灰色方块 + 一个橙色方块** 时，表示处于 Type Selection 模式。​[Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/gui/selection-modes.html)

![[选择模式3.png]]
- 在此模式下，点击视口中的对象会选中场景树中最深层的对应 Prim（通常是 mesh，也可能是 light 或 camera）。
    
- 右键按钮后，可在以下四个筛选项中切换：
    
    - **All Prim Types**（默认，不做过滤）
        
    - **Meshes**
        
    - **Lights**
        
    - **Cameras**
        
- Prim 的 type 在创建时确定且不可编辑。
    

> **Note:** 在 Prim 模式下，也可点击对象的包围框 (bounding box) 选中其父级组。
![[选择模式4.png]]
### 按模型种类选择 (Model Kind Selection)

当按钮显示为 **单个灰色方块** 时，表示处于 Model Kind Selection 模式。​[Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/gui/selection-modes.html)
![[选择模式5.png]]
- 在此模式下，点击视口中的对象会先定位到 Type Selection 会选中的最深层 Prim，然后向上遍历场景树，选中第一个具有对应 Kind 属性的 Prim。
    
- 右键按钮后，可在以下五个筛选项中切换：
    
    - **All Model Kinds**（默认，不做过滤）
        
    - **Assembly**
        
    - **Group**
        
    - **Component**
        
    - **Subcomponent**
        
- 通过结合在场景结构中为各 Prim 设置 Kind 属性，以及选择相应的 Model Kind 过滤器，可实现基于用途的分层化群组选择。


---
## 键盘快捷键参考

键盘快捷键通过提供“热键”，可减少大量点击操作，实现“一键直达”式的高效交互。

---

## 视口控制

|输入|备用输入|功能|
|---|---|---|
|右键 + W|右键 + ↑|向前移动|
|右键 + S|右键 + ↓|向后移动|
|右键 + A|右键 + ←|向左移动|
|右键 + D|右键 + →|向右移动|
|右键 + Q|右键 + Page Up|向上移动|
|右键 + E|右键 + Page Down|向下移动|
|滚轮|Opt + 右键|缩放|
|左键|—|选中|
|Esc|—|取消选中|
|选中 + `F`|—|聚焦到所选物体|
|取消选中 + `F`|—|聚焦到全局视图|
|Opt + 左键|—|围绕视口中心旋转|
|中键（按住）|—|平移视图|
|右键（按住）|—|旋转视图|
|右键（单击）|—|调出上下文菜单|
|Shift + H|—|显示/隐藏网格及 HUD 信息|
|F7|—|启用/禁用所有 UI 元素可见性|
|F11|—|切换全屏模式|
|F10|—|截取屏幕截图|

> **注意：**  
> 在执行任一移动命令时，按住 Shift 可将移动速度加倍，按住 Ctrl 可将移动速度减半。

---

## 选择

|输入|备用输入|功能|
|---|---|---|
|Ctrl + A|—|选中当前场景中的所有资源|
|Ctrl + I|—|反选：选中所有未被选中的资源，并取消选中已选中的资源|
|Esc|—|取消选中当前场景中的所有资源|

---

## 文件操作

|输入|备用输入|功能|
|---|---|---|
|Ctrl + S|—|保存文件|
|Ctrl + O|—|打开文件|

---

## 资源控制

|输入|备用输入|功能|
|---|---|---|
|Del|—|删除所选资源|
|Ctrl + Shift + I|—|实例化当前资源|
|Ctrl + D|—|复制当前资源|
|Ctrl + G|—|将所选资源分组到容器中|
|H|—|切换所选资源的可见性|

---

## 动画控制

|输入|备用输入|功能|
|---|---|---|
|Space|—|播放/暂停动画|

---

## 自定义快捷键

可通过 “Hotkeys Extension” 自定义快捷键组合，以提升操作效率。