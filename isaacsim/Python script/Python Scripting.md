[Python Environment — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/python_scripting/manual_standalone_python.html#python-environment)
## **Python 脚本概念——独立（Standalone）与交互式（Interactive）**  
在 NVIDIA Isaac Sim 中，可以用两种方式编写并运行 Python 脚本：独立脚本和交互式脚本。

- **独立 Python 脚本** 从命令行执行，常用于自动化任务或批量运行仿真。
    
- **交互式 Python 脚本** 在内置的 Python 控制台中执行，适合探索 Isaac Sim API、调试或试验代码片段。  
    无论哪种方式，都可以用来开发自定义扩展（例如新的机器人控制器或传感器），并与 Omniverse 应用进行交互。

### 解释

1. **独立（Standalone）脚本**
    
    - **使用场景**：当你需要反复、无人值守地运行一个完整流程（如批量仿真、数据采集、训练管道）时，将代码写成 `.py` 文件，通过系统终端或脚本调度器（如 cron、Jenkins）一键启动最为方便。
        
    - **优点**：可集成到 CI/CD、集群调度或远程服务器；易于日志记录与结果归档；可与其他命令行工具链无缝衔接。
        
    - **注意**：脚本中要负责初始化与关闭 Isaac Sim 环境，管理好资源（GPU、窗口等）。
        
2. **交互式（Interactive）脚本**
    
    - **使用场景**：在调试阶段或学习 API 时，直接在 Isaac Sim 内置的 Python 控制台（Script Editor 或 VS Code 连接会话）输入并运行代码，可立即查看效果，快速调整。
        
    - **优点**：零部署成本，不用每次都重启整个模拟；便于一步步验证 API 调用；可以动态检查场景、变量和状态。
        
    - **注意**：不适合生产流水线，因为手动交互不易自动化；长脚本或复杂流程也会显得琐碎。
        
3. **两者的协同**
    
    - **从交互到独立**：通常先在控制台里以交互方式调试出核心逻辑，再把成熟的代码组织成独立脚本，实现自动化运行。
        
    - **从独立到交互**：运行出现问题时，可把关键部分拷到交互模式逐行调试，定位 API 调用或环境初始化的细节。
        
4. **在扩展开发中的应用**
    
    - 无论是开发新的机器人控制器、传感器插件，还是构建定制化的仿真工作流，都可以先在交互式环境中试验接口与数据结构，确认无误后，封装为独立脚本或 Extension 插件，实现团队共享与版本管理。


## Core API Overview[Core API Overview — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/python_scripting/core_api_overview.html)

所有以 USD 表示的事物都是带有属性（attributes）的原语（prim）。

模拟（Simulation，简称 sim）通过以编程方式改变这些属性，将这些原语推进时间演变。

应用（Application）负责管理模拟的宏观层面（例如如何渲染场景）以及用户如何与之交互。如果模拟有图形用户界面（GUI），它也是应用的一部分。

Stage（舞台）是 USD 的一个概念，用来定义原语在模拟中的逻辑和关联上下文。比如，一个杯子（mug prim）位于一张桌子（table prim）上，这种关系就是通过这两个原语在同一个舞台上的相对位置以及它们各自的属性来表达的。因此，舞台为应用提供了上下文：原语无法脱离舞台而存在，所以任何处理原语的应用都需要一个舞台才能运行。

同样，世界（World）为模拟提供了上下文，它定义了哪些原语与当前时间流、场景相关，并管理对用户最重要的那些模拟方面。

举例来说，想象你去剧院看戏。剧院就像应用，是你进入剧目的门户；而模拟就是戏剧本身，由一个剧本（program）来定义。你入座，可以看到舞台（stage），戏剧将在这里上演。演出开始时，幕布升起，呈现出由道具和演员组成的场景，他们在其中表演。当要转换到下一个场景时，幕布落下，场景被重置，然后再度升起，展现下一幕。幕后负责管理幕布、道具等机械装置的舞台工作人员，就相当于戏剧的“世界”（world）。


### 专业解释

#### 1. USD 原语（prim）与属性（attributes）

- **原语（prim）**：USD（Universal Scene Description）中的基本构建块。可以把它理解为场景中的一个“对象”，比如一个模型、一个灯光、一个摄像机，或者更复杂的集合体。
    
- **属性（attributes）**：每个 prim 都携带一组可读写的属性，用来描述它的形状、材质、位置、朝向、动画曲线等。
    
- **意义**：将一切都抽象为“带属性的 prim”，大大统一了对场景元素的操作接口。无论你操控的是一个物理对象、灯光还是相机，都通过读写属性完成。
    

#### 2. Simulation（模拟）

- **定义**：模拟进程会随着时间推进，实时或离线地修改 prim 的属性，从而在场景里产生运动、物理交互、动画效果等。
    
- **机制**：底层可能集成物理引擎（如 NVIDIA PhysX、Flex）或自定义逻辑，每一帧都会计算出新的属性值（位置、速度、变换矩阵等），写回到 prim 上。
    
- **作用**：将静态的 USD 场景转化为动态的时序演变，使得机器人、自动化驾驶等仿真成为可能。
    

#### 3. Application（应用）

- **职责**：
    
    1. **渲染管理**：设置渲染器（OpenGL、Vulkan、RTX Path Tracing 等），决定如何将 prim 可视化。
        
    2. **用户交互**：负责 GUI 界面事件（鼠标、键盘、触摸）、调试面板、参数面板等，帮助用户操控和监视模拟。
        
    3. **资源调度**：加载/卸载 USD 文件与贴图、管理内存与 GPU 资源。
        
- **示例**：Isaac Sim 自带的应用框架就整合了窗口管理、工具栏、属性编辑器以及实时预览视图。
    

#### 4. Stage（舞台）

- **USD 概念**：Stage 相当于一个场景图（scene graph），包含了所有 prim 的层次结构、关系网络和变换（transform）信息。
    
- **功能**：
    
    1. **组织结构**：通过父子关系和引用（references）管理复杂场景。
        
    2. **变换继承**：子 prim 会继承父 prim 的变换矩阵，从而简化整体布局。
        
    3. **多层编辑**：可以叠加多层 USD 编辑层（edit layer），支持版本控制与协同创作。
        
- **意义**：Stage 为 prim 提供了“舞台”——一个可以安放、组织、管理它们的上下文环境。
    

#### 5. World（世界）

- **定位**：比 Stage 更上层，更侧重“运行时”上下文。
    
- **职责**：
    
    1. **活跃 prim 管理**：决定哪些 prim 会参与当前模拟循环（例如摄像机视锥体外的物体可以休眠）。
        
    2. **时间步长控制**：统一管理模拟的时间步进（fixed timestep vs. variable timestep），确保物理和动画稳定。
        
    3. **场景事件**：广播碰撞、触发器、传感器读数等事件给应用或用户脚本。
        
- **类比**：“世界”就像游戏引擎的场景管理器，负责整体的 update loop 与事件调度。
    

#### 6. “剧院与戏剧”比喻

- **剧院 = Application**：提供基础设施（座位、灯光、声音系统），是观众（用户）进入体验的入口。
    
- **舞台 = Stage**：承载具体的布景和道具，是视觉和空间组织的场所。
    
- **戏剧 = Simulation**：随着剧情（时间）演进，道具（prim）和演员（逻辑）不断变化。
    
- **幕后工作人员 = World**：在观众看不见的地方操纵幕布、场景转换和技术细节，保证演出顺利进行。




# Python Environment


#### **SimulationApp**  
`SimulationApp` 类提供了一组便捷函数，用于管理 NVIDIA Isaac Sim 应用程序的生命周期。

**使用示例：**  
下面的代码演示了如何使用 `SimulationApp` 来创建一个应用、向前推进仿真时间，然后退出。

> **注意**  
> 任何 Omniverse 级别的 `import` 必须在 `SimulationApp` 实例化之后进行。因为相关的 API 是由扩展/运行时插件系统提供，必须先加载该系统，才能导入这些模块。

> <font color="#c00000">**重要**</font>
> 
> - **无头模式（headless）运行时：**
>     
>     1. 在初始化 `SimulationApp` 时，将配置中的 `"headless"` 设置为 `True`。
>         
>     2. 注释掉所有会创建或打开 matplotlib 窗口的调用。
>

```python
from isaacsim import SimulationApp

# 创建 SimulationApp 实例，启用无头模式
simulation_app = SimulationApp({"headless": True})

### 在 helper 加载完成后，这里再执行任何 Omniverse 相关的导入 ###

simulation_app.update()  # 渲染/模拟一帧
simulation_app.close()   # 清理并关闭应用

```

#### 解释
- **SimulationApp 的定位**
    
    - `SimulationApp` 是 Isaac Sim 提供的“应用壳”（application wrapper），封装了创建、运行、渲染、事件循环以及销毁等常见操作。
        
    - 它背后调用 Omniverse Kit 框架，自动完成窗口管理、渲染循环、插件加载、Python 环境初始化等繁琐流程，使用户可以专注于编写仿真逻辑。
        
- **插件系统与延迟导入**
    
    - Omniverse/Isaac Sim 的功能模块（渲染器、物理引擎、机器人控制接口等）都是以插件形式动态注册到 Kit 运行时中。
        
    - 因此，如果在实例化 `SimulationApp` 之前就 `import isaac.*`，这些模块尚未加载进来，会导致找不到或无法初始化接口。
        
    - 正确做法是先创建 `SimulationApp`，等它完成底层 Kit 和插件的加载后，再执行任何需要 Omniverse API 的导入。
        
- **头、无头模式（GUI vs. Headless）**
    
    - **图形界面模式（默认）**：`SimulationApp()` 会创建一个可视化窗口并启动渲染循环，适合可视化调试与交互。
        
    - **无头模式**：设置 `{"headless": True}` 后，不会弹出窗口，渲染仅在后台进行，适合批处理脚本、服务器端跑批、CI/CD 等场景。同时需要避免调用任何弹出图形界面（如 matplotlib），否则会因无显示环境而报错。
        
- **核心方法**
    
    - `update()`：单步推进一次渲染与仿真循环。
        
        - 渲染：根据当前场景状态调用渲染后端，更新帧缓冲。
            
        - 仿真：如果你在脚本中注册了物理或自定义逻辑，这里会触发对应的回调。
            
    - `close()`：
        
        - 断开渲染循环，卸载插件，释放所有资源（GPU、内存、线程等），并优雅退出 Kit。
            
- **典型使用流程**
    
    1. **初始化**：创建 `SimulationApp(config)`。
        
    2. **导入插件**：在创建后再 `import carb, omni, isaac.*` 等。
        
    3. **构建场景**：加载 USD 场景、创建或获取 prim，配置相机、灯光、机器人等。
        
    4. **仿真循环**：在脚本中调用若干次 `update()`（可在循环体内决定何时退出）。
        
    5. **清理退出**：`close()`，确保所有后台线程和资源都被正确释放。




### Enabling additional extensions

	可以通过两种方法为项目添加扩展

#### 方法一：在 Experience 文件中声明（静态加载）

- 把扩展写到 `.exp.kit` 的 `[dependencies]` 段中。
    
- 启动时 Kit 会自动读取这个段落，按列表一次性加载。
    
- 适合“项目级”或“固定配置”——每次运行都要这些扩展。


<font color="#7030a0">在你的 `.exp.kit` 文件的 `[dependencies]` 段落里，列出要启用的扩展。例如在 `apps/isaacsim.exp.base.python.kit` 中：</font>
```kotlin
[dependencies]
"omni.kit.window.stage"  = {}   # 启用舞台（Stage）窗口
"omni.kit.widget.layers" = {}   # 启用图层（Layers）面板

```

#### 方法二：在 Python 代码中动态启用（运行时加载）

在脚本里通过 API 手动加载扩展，按需在运行时打开／关闭：

```python
from isaacsim import SimulationApp
from isaacsim.core.utils.extensions import enable_extension

# 启动带 UI 的 Kit 应用
simulation_app = SimulationApp({"headless": False})

# 运行时启用扩展
enable_extension("omni.kit.window.stage")   # 打开舞台窗口
enable_extension("omni.kit.widget.layers")  # 打开图层面板

# 刷新应用，使扩展立即生效
simulation_app.update()

```
**特点**：无需修改 Experience 文件，适合交互式调试、脚本化流程或按条件加载扩展



# 核心API教程



### 将示例代码转换为独立应用程序

#### 说明

- 在Windows系统中，使用 `python.bat` 代替 `python.sh`。
    
- `python.sh` 的工作原理与 `python.bat` 类似。
    

正如在“工作流”中提到的，在这个工作流中，机器人应用程序会在启动时立即从Python中运行，并且你可以控制何时执行物理仿真和渲染步骤。

以下是将示例转换为独立应用程序的步骤。

#### 步骤

1. **创建新的 Python 文件**
    

打开一个新的文件，命名为 `my_application.py`，并添加以下代码：

```python
# 启动 Isaac Sim，所有其他导入之前
# 这是任何独立应用程序的前两行默认代码
from isaacsim import SimulationApp
simulation_app = SimulationApp({"headless": False})  # 我们也可以以无头模式运行。

from isaacsim.core.api import World
from isaacsim.core.api.objects import DynamicCuboid
import numpy as np

# 创建一个世界对象
world = World()
world.scene.add_default_ground_plane()  # 添加默认地面

# 添加一个动态立方体
fancy_cube =  world.scene.add(
    DynamicCuboid(
        prim_path="/World/random_cube",
        name="fancy_cube",
        position=np.array([0, 0, 1.0]),  # 初始位置
        scale=np.array([0.5015, 0.5015, 0.5015]),  # 尺寸
        color=np.array([0, 0, 1.0]),  # 蓝色
    ))

# 在查询与关节相关的内容之前需要重置世界
# 建议在添加资产后总是执行重置，以便物理处理能够正确传递
world.reset()

# 运行500次物理和渲染步骤
for i in range(500):
    position, orientation = fancy_cube.get_world_pose()  # 获取立方体的世界坐标
    linear_velocity = fancy_cube.get_linear_velocity()  # 获取立方体的线性速度
    # 将结果显示在终端
    print("Cube position is : " + str(position))
    print("Cube's orientation is : " + str(orientation))
    print("Cube's linear velocity is : " + str(linear_velocity))
    
    # 我们可以控制物理仿真和渲染的步进
    # 在此工作流中，物理和渲染步骤同步进行
    world.step(render=True)  # 执行一个物理步骤和一个渲染步骤

# 关闭 Isaac Sim
simulation_app.close()

```


使用以下命令运行程序：

```cmd
./python.sh ./exts/isaacsim.examples.interactive/isaacsim/examples/interactive/user_examples/my_application.py

```

独立程序需要完成启动仿真、world和stage的创建等流程，是一个主程序。




## 添加机器人
[Hello Robot — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/core_api_tutorials/tutorial_core_hello_robot.html)
学习如何添加和控制机器人

![[Pasted image 20250428104345.png]]

