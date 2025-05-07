
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
