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

## hello world
[Hello World](https://docs.isaacsim.omniverse.nvidia.com/latest/core_api_tutorials/tutorial_core_hello_world.html)

```python
from isaacsim.examples.interactive.base_sample import BaseSample  # 从示例库中引入 BaseSample，所有交互式示例的基础类

class HelloWorld(BaseSample):
    def __init__(self) -> None:
        # 调用父类构造函数，完成内部状态初始化
        # 注意：此处不应做场景资产的加载或变量赋值，这些操作将在 setup_scene 中完成
        super().__init__()  
        return

    # setup_scene 在初次加载空白场景（EMPTY stage）时被调用一次，用于放置场景中的初始资产
    # 由于热重载（hot-reload）后不会再次走此函数，因此所有在这里完成的操作只会执行一次
    def setup_scene(self):
        # 获取 BaseSample 中管理的 World 实例，World 是对场景（USD Stage）和仿真环境的封装
        world = self.get_world()  
        # 在场景中添加一张默认的地面平面，便于机器人或物体有支撑面
        world.scene.add_default_ground_plane()  
        return

```

### 1. **BaseSample 的职责**

`BaseSample` 是所有交互式示例和扩展应用的“模版”或“脚手架”，它封装了常见的初始化、回调管理、热重载等通用逻辑，典型功能包括：

1. **加载世界（Load World）**
    
    - 提供一个 UI 按钮或者命令接口，一键将预先定义好的 USD 场景和资产加载进模拟环境。
        
2. **清空世界（Clear World）**
    
    - 当用户创建或切换到一个新的 Stage 时，自动清除已有资产，保证场景干净、互不干扰。
        
3. **重置对象（Reset Objects）**
    
    - 在“重置”操作被触发时（例如按下 Reset 按钮），将场景中所有可重置对象恢复到初始状态（位置、姿态、物理属性等）。
        
4. **热重载（Hot Reload）**
    
    - 支持在脚本文件修改后，无需重启整个应用，就能动态地卸载旧逻辑、加载新脚本，并重新初始化世界或调用专门的 reload 回调。
        

这些通用行为让你在编写具体机器人示例时，无需反复造轮子，只需继承 `BaseSample`、覆盖关键回调，就能快速搭建交互式仿真应用。

---

### 2. **World：仿真环境的中心枢纽**

`World` 是对整个模拟场景的高级封装，它贯穿了仿真执行流程中的各个阶段，主要职责包括：

- **时间和事件管理**
    
    - 注册 “start”、“step”（每个物理步）、“draw”（每帧渲染）、“reset” 等回调函数；
        
    - 在合适的时机自动调用，协调渲染、物理引擎和用户逻辑。
        
- **物理仿真步进**
    
    - 在每个物理时间步里（通常以固定频率调用），更新碰撞检测、刚体动力学等；
        
    - 并触发用户在 `on_step()` 中编写的控制或监测逻辑。
        
- **场景重置**
    
    - 统一调用各类资产或任务的 `reset()` 方法，恢复到初始设置；
        
    - 保证多次实验之间的可重复性。
        
- **任务管理（Tasks）**
    
    - 支持将高层次操作封装成“任务”（Task），例如：抓取、路径规划、环境感知等；
        
    - 之后可以按需注册、启动或销毁这些任务。
        

通过 `world`，开发者无需直接操作底层 USD API，就能完成仿真循环的核心流程。

---

### **3. Scene：USD 资产的管理器**

`Scene` 是 `World` 内部组合的另一个关键组件，专门负责对 **USD Stage** 中的资产进行统一管理：

- **添加资产**
    
    - 提供诸如 `add_default_ground_plane()`、`add_reference()`, `add_robot()` 等便捷 API；
        
    - 在 Stage 上快速插入地面、几何体、机器人模型等。
        
- **操作与查询**
    
    - 支持查找已有 Prim（例如 `scene.get_prim("/World/Robot")`），以及动态修改它们的属性（位置、材质、物理属性）。
        
- **重置与清理**
    
    - 给每个管理的资产注册重置回调，在 World 执行场景重置时一并调用；
        
    - 支持按类别清空（只移除机器人或只移除传感器等）或彻底清空整个 Stage。
        

Scene 的存在，让您可以把注意力集中在“我想在场景里放什么、怎么放、怎么控制”，而不用被 USD 的繁琐路径和原语细节绕晕。


---

### 单例模式（singleton world）

**单例模式（Singleton）概念**

在软件设计中，**单例模式**保证某个类在整个进程中只有一个实例，并且提供一个全局访问点。对于 Isaac Sim 来说，整个平台在同一次运行会话（session）中只能存在**一个** `World` 对象，这是合理且必要的，因为：

- 仿真循环、渲染管线、物理引擎等底层子系统都要围绕同一个世界状态协同工作。
    
- 如果出现多个世界实例，彼此之间的资源（如 GPU、物理场景）会冲突，无法正常交互。
    
- 统一管理时序回调与场景重置，也必须针对唯一的 `World`。

```python
# 文件：scene_helper.py

from isaacsim.core.api import World  # 直接导入单例

def add_simple_obstacle():
    """
    辅助脚本：在任意位置添加一个简单立方体障碍物，
    使用 World.instance() 保证与主脚本中的同一个世界。
    """
    # 无需继承 BaseSample，直接获取单例世界
    world = World.instance()
    scene = world.scene

    # 在场景里增加一个参考原语（参考外部 USD 链接）
    # 这里以 add_reference 为例，也可以用 add_box, add_sphere 等便捷方法
    obstacle = scene.add_reference(
        usd_path="path/to/box.usd",
        prim_path="/World/Obstacle",
        translation=(1.0, 0.5, 0.0),
        scale=0.2
    )
    # 为了物理仿真，给障碍物添加碰撞属性
    obstacle.get_physics_api().attach_collision()

    print("已添加障碍物：", obstacle.prim_path)

```

- **主脚本／示例脚本**  
    继承自 `BaseSample`，在 `__init__` 中让父类初始化仿真框架，之后在 `setup_scene`、`on_start`、`on_step`、`on_reload` 等回调中通过 `self.get_world()` 访问同一个 `World` 实例，并进行场景构建与逻辑编写。
    
- **辅助脚本／模块**  
    如果不在 `BaseSample` 的子类里，或者您想在其他文件中对场景进行扩展，只需导入 `isaacsim.core.api.World`，调用 `World.instance()`，就能拿到与主脚本完全相同的单例 `World`，进行资产添加、查询、重置等操作。



---



### 热重载方法

热重载允许你直接修改脚本内容，并通过load快速重新加载

- **Ctrl+S 热重载**  
    保存脚本后，Isaac Sim 自动检测到文件变化并重载（hot-reload），无需重启整个应用。
    
- **重新加载场景模板**  
    如果修改了 `setup_scene()` 中的内容，需要通过菜单 `File > New From Stage Template > Empty` 然后点击 **LOAD**，以确保场景从空白模板重新构建。仅改动脚本里其它逻辑时，则直接点 **LOAD** 即可。
    
- **启动仿真**  
    点击界面上的 **PLAY** 按钮，开始物理仿真，可见立方体在重力作用下掉落


```python
# 文件名：hello_world_add_cube.py

from isaacsim.examples.interactive.base_sample import BaseSample
import numpy as np
# 导入 DynamicCuboid，用于在场景中创建或引用一个动态立方体刚体
from isaacsim.core.api.objects import DynamicCuboid

class HelloWorld(BaseSample):
    """用于演示如何在场景中添加一个动态立方体刚体。"""

    def __init__(self) -> None:
        # 调用父类构造：初始化 USD Stage、渲染器、物理引擎、事件回调等
        super().__init__()
        return

    def setup_scene(self):
        """
        场景初次加载时调用（只在空白场景加载后执行一次）。
        在此处添加地面和平面，以及一个立方体刚体。
        """
        # 通过 BaseSample 提供的接口获取全局唯一的 World 实例
        world = self.get_world()
        # 在场景中添加一个默认地面，带物理碰撞属性，便于立方体落到地面上
        world.scene.add_default_ground_plane()

        # 使用 DynamicCuboid 构造函数创建一个刚体立方体
        fancy_cube = world.scene.add(
            DynamicCuboid(
                prim_path="/World/random_cube",      # USD Stage 中的路径
                name="fancy_cube",                    # 在 Scene 中注册的唯一名称
                position=np.array([0.0, 0.0, 1.0]),   # 初始位置，单位为米
                scale=np.array([0.5015, 0.5015, 0.5015]),  # 立方体尺寸（长宽高）
                color=np.array([0.0, 0.0, 1.0]),      # 立方体颜色，RGB 归一化
            )
        )

        # 可选：对 fancy_cube 进一步配置，如添加动力学属性、传感器等
        return

# 程序入口：实例化并运行示例
if __name__ == "__main__":
    sample = HelloWorld()
    sample.run()

```


---

### 打印物体的物理属性

- **何时调用 `setup_post_load`？**
    
    - 每次点击界面上的 **LOAD** 按钮后触发（包括第一次从模板加载或热重载后）。
        
    - Isaac Sim 会在调用此函数之前完成一次物理步进，确保所有物理对象的内部句柄（如速度、碰撞形态）都已就绪。
        
- **核心步骤**
    
    1. **获取世界实例**：`self._world = self.get_world()`
        
    2. **通过名称拿到对象**：`self._cube = self._world.scene.get_object("fancy_cube")`
        
    3. **查询属性**：调用 `get_world_pose()`、`get_linear_velocity()` 等方法获取位姿和线速度。
```python
# 文件：hello_world_inspect_cube.py

from isaacsim.examples.interactive.base_sample import BaseSample
import numpy as np
from isaacsim.core.api.objects import DynamicCuboid

class HelloWorld(BaseSample):
    """演示如何在场景中添加立方体，并在加载后打印其世界位姿与速度。"""

    def __init__(self) -> None:
        # 调用父类构造，初始化仿真世界与回调系统
        super().__init__()
        return

    def setup_scene(self):
        """
        初次从空白模板加载时调用。添加地面和平面，并放置立方体刚体。
        热重载时不会再次执行，除非重新加载模板。
        """
        world = self.get_world()  # 通过基类接口获取 World 单例
        world.scene.add_default_ground_plane()  # 添加默认物理地面

        # 创建并注册一个动态立方体刚体
        world.scene.add(
            DynamicCuboid(
                prim_path="/World/random_cube",       # USD Stage 中的路径
                name="fancy_cube",                     # Scene 中检索用的唯一名称
                position=np.array([0.0, 0.0, 1.0]),    # 初始位置，单位为米
                scale=np.array([0.5015, 0.5015, 0.5015]),  # 立方体尺寸（各轴长度）
                color=np.array([0.0, 0.0, 1.0]),       # 颜色（RGB 归一化）
            )
        )
        return

    async def setup_post_load(self):
        """
        在每次点击 LOAD 后调用（包括首次加载与热重载后）。
        物理引擎已完成一步仿真，可安全查询物理属性。
        """
        # 缓存世界和立方体对象，便于后续操作
        self._world = self.get_world()
        self._cube = self._world.scene.get_object("fancy_cube")

        # 查询世界位姿（位置 + 四元数方向）
        position, orientation = self._cube.get_world_pose()
        # 查询线速度（米/秒）
        linear_velocity = self._cube.get_linear_velocity()

        # 打印到终端
        print("Cube position is :", position)
        print("Cube's orientation is :", orientation)
        print("Cube's linear velocity is :", linear_velocity)
        return

# 程序入口：实例化并运行示例
if __name__ == "__main__":
    sample = HelloWorld()
    sample.run()

```

在 `BaseSample` 的执行流程里，当你点击界面上的 **LOAD** 按钮时，内部会按照下面的顺序走：

1. 清空或重置场景（如果需要的话），然后调用你实现的 `setup_scene()`。
    
2. **进行一次物理仿真步进**（physics step），让物理引擎初始化所有刚体、关节、碰撞形态等底层句柄。
    
3. 将控制权切回给框架，并 **调度** 执行 `async def setup_post_load(...)` 这个协程函数。
    

因此，`setup_post_load` 里去调用诸如 `get_world_pose()`、`get_linear_velocity()` 这类依赖物理句柄的接口，就能正确地拿到刚体在这一次仿真步进之后的位姿和速度。

简而言之，Isaac Sim 会**先跑完一次物理仿真步进**，再“等待”这一步完成后，才去调用`setup_post_load`，保证所有物理数据都是最新、可用的。


#### async协程函数的概念

`async` 是 Python 3.5+ 引入的一个关键字，用于定义 **异步协程函数**（asynchronous coroutine function）。它的主要作用和特点有：

1. **标记协程**
    
    - 在函数定义前加上 `async def`，表示这是一个协程（coroutine），函数体内可以使用 `await` 关键字来“等待”另一段耗时操作完成（例如 I/O、定时器、其他协程等）。
        
    - 调用这样的函数并不会立刻执行函数体，而是返回一个 **coroutine 对象**，需要被调度到事件循环（event loop）中才会真正运行。
        
2. **配合 `await` 使用**
    
```python
    async def foo():
    result = await some_other_coroutine()
    return result

```

     `await` 表示“让出”执行权给事件循环，直到被等待的协程或异步操作完成后，再回到当前协程继续执行。
        
3. **与普通函数的区别**
    
    - 普通函数（`def`）调用时立即执行；
        
    - 异步函数（`async def`）调用时会返回一个协程对象，必须通过 `await`（或类似 `asyncio.run()`、框架调度）才能执行实际逻辑。
        
4. **在 Isaac Sim 中的用法**
    
    - `BaseSample` 框架内部维护了一个异步调度机制，点击 **LOAD** 时，它会把 `setup_post_load` 这样的 `async def` 函数提交给事件循环并 `await` 执行。
        
    - 因此，你可以在 `setup_post_load` 里安全地执行需要等待物理步进完成的逻辑，而不用担心阻塞主线程或渲染循环。

---
### 跟踪物体属性

```python
# 初始化Isaac Sim应用（headless=False显示图形界面）
from isaacsim import SimulationApp
simulation_app = SimulationApp({"headless": False})

# 导入必要模块
from isaacsim.core.api import World
from isaacsim.core.api.objects import DynamicCuboid
import numpy as np

# 创建世界并添加地平面
world = World()
world.scene.add_default_ground_plane()

# 添加蓝色动态立方体（位置z=1.0使其悬空）
fancy_cube = world.scene.add(
    DynamicCuboid(
        prim_path="/World/random_cube",
        position=np.array([0, 0, 1.0]),  # x,y,z坐标
        scale=np.array([0.5, 0.5, 0.5]), # 尺寸
        color=np.array([0, 0, 1.0]),     # RGB蓝色
    ))

# 重置世界使物理生效
world.reset()

# 主模拟循环（500步）
for _ in range(500):
    # 获取立方体状态
    pos, orn = fancy_cube.get_world_pose()
    vel = fancy_cube.get_linear_velocity()
    
    # 打印状态信息
    print(f"Position: {pos}, Velocity: {vel}")
    
    # 推进物理模拟并渲染
    world.step(render=True)

# 关闭模拟器
simulation_app.close()
```





---

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

**解释：**

不像继承baseexample的方法，这种组织代码的形式可以直接手动调用底层api，快速了解细节，但是不如BaseExample那样有条理。

代码流程如下：

- **手动创建 `SimulationApp`**：决定 headless、窗口大小、扩展列表等
    
- **手动创建 `World`**：立刻就获得对场景的引用
    
- **添加资产**：`world.scene.add_*`
    
- **手动调用 `world.reset()`**：确保物理句柄生效
    
- **手动循环**：你控制“多少步→渲染→物理→读状态”
    
- **调用 `simulation_app.close()`**：显式退出


使用以下命令运行程序：
```cmd
./python.sh ./exts/isaacsim.examples.interactive/isaacsim/examples/interactive/user_examples/my_application.py

```

独立程序需要完成启动仿真、world和stage的创建等流程，是一个主程序。




## 添加机器人（hello robot）
[Hello Robot — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/core_api_tutorials/tutorial_core_hello_robot.html)
学习如何添加和控制机器人

### 工作流程

1. 在user_examples文件夹创建三个.py文件
	分别是__init__.py、my_Robot.py、my_Robot_extension.py
2. 分别往三个文件中写入如下代码
3. 打开isaacsim GUI，打开Windows -> Examples -> robotic example
4. 点击对应目录 load 代码



### 文件hello_Robot.py
```python
from isaacsim.examples.interactive.base_sample import BaseSample  # 引入示例框架的基类 BaseSample
from isaacsim.core.utils.nucleus import get_assets_root_path    # 获取 Nucleus Server 上资产根路径的工具
from isaacsim.core.utils.stage import add_reference_to_stage   # 将 USD 文件作为引用添加到场景的工具
from isaacsim.core.api.robots import Robot                      # 高层次机器人 API，用于包装 Articulation 对象
import carb                                                     # NVIDIA 通用日志库

class HelloWorld(BaseSample):
    def __init__(self) -> None:
        super().__init__()  # 调用父类构造函数完成初始化
        return

    def setup_scene(self):
        # --- 场景搭建阶段 ---
        world = self.get_world()                       # 从 BaseSample 中获取当前 World 对象
        world.scene.add_default_ground_plane()         # 添加默认平面

        # 查找已配置的 Nucleus Server 中 /Isaac 路径的根目录
        assets_root_path = get_assets_root_path()
        if assets_root_path is None:
            # 如果找不到，就记录一个错误日志
            carb.log_error("Could not find nucleus server with /Isaac folder")

        # 组合出 Jetbot 资产的完整 USD 路径
        asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"

        # 将 USD 文件作为引用（reference）添加到场景中
        # 在 /World 下创建一个 XForm prim，名为 Fancy_Robot，指向这个 USD 文件
        add_reference_to_stage(usd_path=asset_path, prim_path="/World/Fancy_Robot")

        # 使用高层 API 将刚才创建的 prim 包装成 Robot 对象并加入场景管理
        # 这一步会为机器人关节初始化物理句柄，并提供设置/获取属性的方法
        jetbot_robot = world.scene.add(
            Robot(prim_path="/World/Fancy_Robot", name="fancy_robot")
        )

        # 注意：在第一次 reset() 之前，Articulation 的物理句柄尚未初始化
        # 所以这里访问 num_dof 会得到 None
        print("Num of degrees of freedom before first reset: " + str(jetbot_robot.num_dof))
        return

    async def setup_post_load(self):
        # --- 场景加载并重置后 ---
        self._world = self.get_world()
        self._jetbot = self._world.scene.get_object("fancy_robot")

        # 经过第一次 reset() 后，关节数已被正确初始化
        print("Num of degrees of freedom after first reset: " + str(self._jetbot.num_dof))
        # 输出所有关节的初始位置
        print("Joint Positions after first reset: " + str(self._jetbot.get_joint_positions()))
        return

```



### 文件hello_Robot_extension.py
```python
# my_custom_extension.py

  

import os
import omni.ext
from isaacsim.examples.browser import get_instance as get_browser_instance
from isaacsim.examples.interactive.base_sample import BaseSampleUITemplate

#准备导入我自己的扩展包

from isaacsim.examples.interactive.user_examples.hello_Robot import HelloWorld

class MyCustomExtension(omni.ext.IExt):
    def on_startup(self, ext_id: str):
        """扩展启动时被调用，用来注册示例到 Examples Browser"""
        self.ext_id = ext_id
        self.example_name = "My Custom Example"
        self.category = "MyCategory"  # 可以按功能或模块分组

        ui_kwargs = {

            "ext_id": ext_id,

            "file_path": os.path.abspath(__file__),

            "title": "My Custom Example",                                       # 示例标题
            "doc_link": "https://my.docs.link/for/custom_example",              # 示例文档链接
            "overview": "my test is here,only English",         # 示例概述
            # 把你自定义的示例逻辑实例传进去
            "sample": HelloWorld(),  

        }

  
        # 创建 UI 模板实例
        ui_handle = BaseSampleUITemplate(**ui_kwargs)

  
        # 注册到 Examples Browser
        get_browser_instance().register_example(
            name=self.example_name,
            execute_entrypoint=ui_handle.build_window,
            ui_hook=ui_handle.build_ui,
            category=self.category,
        )

  
    def on_shutdown(self):
        """扩展卸载或关闭时被调用，用来注销示例"""
        get_browser_instance().deregister_example(
            name=self.example_name,
            category=self.category,
        )
```
### 文件__init__.py
```python
from isaacsim.examples.interactive.user_examples.hello_Robot import HelloWorld
from isaacsim.examples.interactive.user_examples.hello_Robot_extension import MyCustomExtension
```


## 让机器人动起来

```python
# 导入必要的模块
from isaacsim.examples.interactive.base_sample import BaseSample  # 基础示例基类
from isaacsim.core.utils.types import ArticulationAction  # 关节动作数据结构
from isaacsim.core.utils.nucleus import get_assets_root_path  # 获取Nucleus资产路径
from isaacsim.core.utils.stage import add_reference_to_stage  # USD场景引用工具
from isaacsim.core.api.robots import Robot  # 机器人操作接口
import numpy as np  # 数值计算库
import carb  # Omniverse日志工具

class HelloWorld(BaseSample):
    def __init__(self) -> None:
        """初始化示例类"""
        super().__init__()  # 必须调用父类初始化
        return

    def setup_scene(self):
        """场景搭建阶段（物理引擎尚未激活）"""
        world = self.get_world()  # 获取世界实例
        world.scene.add_default_ground_plane()  # 添加默认地平面
        
        # 获取Nucleus服务器资产路径
        assets_root_path = get_assets_root_path()
        if assets_root_path is None:
            carb.log_error("未找到Nucleus服务器/Isaac目录")
            return
            
        # 加载Jetbot机器人USD模型
        asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        add_reference_to_stage(
            usd_path=asset_path, 
            prim_path="/World/Fancy_Robot"  # 指定场景中的挂载路径
        )
        
        # 将机器人Prim包装为Robot对象
        world.scene.add(
            Robot(prim_path="/World/Fancy_Robot", name="fancy_robot")
        )
        return

    async def setup_post_load(self):
        """物理重置后的设置阶段（可安全访问物理属性）"""
        self._world = self.get_world()  # 保存世界实例引用
        self._jetbot = self._world.scene.get_object("fancy_robot")  # 获取机器人实例
        
        # 获取关节控制器（用于后续运动控制）
        # 注意：必须在物理重置后才能调用
        self._jetbot_articulation_controller = self._jetbot.get_articulation_controller()
        
        # 注册物理回调（每个物理步长自动调用）
        self._world.add_physics_callback(
            "sending_actions",  # 回调名称（可自定义）
            callback_fn=self.send_robot_actions  # 绑定的回调函数
        )
        return

    def send_robot_actions(self, step_size):
        """
        物理步长回调函数
        参数:
            step_size: 物理步长时间（秒）
        """
        # 创建随机速度指令（5rad/s以内的随机值）
        random_velocities = 5 * np.random.rand(2,)  # Jetbot有2个驱动轮
        
        # 构建关节动作指令（位置和力矩设为None表示不控制）
        action = ArticulationAction(
            joint_positions=None,  # 不设置目标位置
            joint_efforts=None,   # 不设置目标力矩  
            joint_velocities=random_velocities  # 设置目标速度
        )
        
        # 应用控制指令（以下两种方式等效）
        # 方式1：通过关节控制器应用
        self._jetbot_articulation_controller.apply_action(action)
        
        # 方式2：直接通过机器人对象应用
        # self._jetbot.apply_action(action)
        return
```

1. 新增导入——准备控制与随机数工具
```python
from isaacsim.core.utils.types import ArticulationAction
import numpy as np
```
- **ArticulationAction**
    - Isaac Sim 提供的标准数据结构，用来一次性打包对关节的三种指令：`joint_positions`（位置）、`joint_velocities`（速度）、`joint_efforts`（力矩）。
    - 你只需要填你关心的那一项，其他设为 `None` 即可。

- **numpy (np)**
    - 用于生成“随机速度”示例，演示如何在物理循环里动态计算控制信号。


2. 在 `setup_post_load` 拿到“关节控制器”并注册回调
```python
self._jetbot_articulation_controller = self._jetbot.get_articulation_controller()

self._world.add_physics_callback(
    "sending_actions", 
    callback_fn=self.send_robot_actions
)
```
- **`get_articulation_controller()`**
    
    - 在物理重置（`reset()`）后，机器人底层的物理句柄才初始化完毕，才能拿到这个控制器对象。
        
    - 通过它，你可以调用 `.apply_action(...)` 来下发打包好的指令。
        
- **`add_physics_callback`**
    
    - 把你的 `send_robot_actions` 方法挂到“物理子步”上。
        
    - 每当物理引擎推进一个子时间步，就会调用一次这个回调，参数 `step_size` 告诉你这一步的时长（秒）。
        
    - 名称 `"sending_actions"` 只是给回调起个标识，随便取，只要不冲突即可。



3. 回调函数 `send_robot_actions`——实时下发动作
```python
   def send_robot_actions(self, step_size):
    random_velocities = 5 * np.random.rand(2,)  
    action = ArticulationAction(
        joint_positions=None,
        joint_efforts=None,
        joint_velocities=random_velocities
    )
    self._jetbot_articulation_controller.apply_action(action)

```
- **生成随机速度**
    
    - `np.random.rand(2,)` 生成 ∈ [0,1) 的二维向量，乘以 5 → ∈ [0,5) rad/s。
        
    - 对应 Jetbot 两个驱动轮的速度指令。
        
- **构建 `ArticulationAction`**
    
    - 只设置 `joint_velocities`，其余设为 `None`，表示“只给速度，下发位置和力矩控制留空”。
        
- **下发指令**
    
    - **方式1**：`controller.apply_action(action)`
        
    - **方式2**：`robot.apply_action(action)`（两者等效，都最终通过物理接口发送给模拟器）
        

这样，每个物理子步你的机器人就会收到一组新的速度，被物理引擎拾取并执行，从而“动”起来。


### 轮式机器人控制

```python
# 导入必要的模块
from isaacsim.examples.interactive.base_sample import BaseSample  # 基础示例基类
from isaacsim.core.utils.nucleus import get_assets_root_path    # 获取Nucleus资产路径
from isaacsim.robot.wheeled_robots.robots import WheeledRobot   # 轮式机器人专用类
from isaacsim.core.utils.types import ArticulationAction         # 关节动作数据结构
import numpy as np  # 数值计算库

class HelloWorld(BaseSample):
    def __init__(self) -> None:
        """初始化示例类"""
        super().__init__()  # 必须调用父类初始化
        return
  

    def setup_scene(self):

        """场景搭建阶段（物理引擎尚未激活）"""
        world = self.get_world()  # 获取世界实例
        world.scene.add_default_ground_plane()  # 添加默认地平面
        # 获取Nucleus服务器资产路径
        assets_root_path = get_assets_root_path()
        # 构建Jetbot机器人USD文件路径
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        # 添加轮式机器人（专用WheeledRobot类）
        self._jetbot = world.scene.add(
            WheeledRobot(
                prim_path="/World/Fancy_Robot",  # 场景中的路径
                name="fancy_robot",              # 实例名称
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],  # 轮关节名
                create_robot=True,               # 自动创建机器人
                usd_path=jetbot_asset_path      # USD模型路径
            )
        )
        return

    async def setup_post_load(self):
        """物理重置后的设置阶段（可安全访问物理属性）"""
        self._world = self.get_world()  # 保存世界实例引用
        self._jetbot = self._world.scene.get_object("fancy_robot")  # 获取机器人实例
        # 注册物理回调（每个物理步长自动调用）
        self._world.add_physics_callback(
            "sending_actions",  # 回调名称
            callback_fn=self.send_robot_actions  # 绑定的回调函数
        )
        return
        
    def send_robot_actions(self, step_size):
        """
        物理步长回调函数
        参数:
            step_size: 物理步长时间（秒）
        """
        # 使用专用轮式控制接口（apply_wheel_actions）
        self._jetbot.apply_wheel_actions(
            ArticulationAction(
                joint_positions=None,      # 不设置目标位置
                joint_efforts=None,       # 不设置目标力矩  
                joint_velocities=5 * np.random.rand(2,)  # 设置随机速度（5rad/s内）
            )
        )
        return
```

同样为该脚本添加一个extension脚本，将其加入交互式example中，可以直接load加载。


## *两轮机器人控制器（add a Controller）*

示例中，使用Controller模块进行高层的轮式机器人控制

### 创建自定义控制器

```python
# 导入必要的模块
from isaacsim.examples.interactive.base_sample import BaseSample  # 基础示例基类
from isaacsim.core.utils.nucleus import get_assets_root_path    # 获取Nucleus服务器资产路径
from isaacsim.robot.wheeled_robots.robots import WheeledRobot   # 轮式机器人专用类
from isaacsim.core.utils.types import ArticulationAction         # 关节动作数据结构
from isaacsim.core.api.controllers import BaseController        # 控制器基类
import numpy as np  # 数值计算库

class CoolController(BaseController):
    """自定义差速驱动控制器（开环控制）"""
    def __init__(self):
        """
        初始化控制器参数
        注意：轮半径和轮距必须与实物机器人匹配
        """
        super().__init__(name="my_cool_controller")  # 指定控制器名称
        self._wheel_radius = 0.03    # 驱动轮半径（单位：米）
        self._wheel_base = 0.1125    # 左右轮间距（单位：米）
        return
        
    def forward(self, command):
        """
        差速运动学前向计算
        参数:
            command: [线速度(m/s), 角速度(rad/s)]
        返回:
            ArticulationAction: 可被机器人直接执行的动作指令
        """
        # 差速驱动运动学转换公式：
        # 左轮速度 = (2*线速度 - 角速度*轮距) / (2*轮半径)
        # 右轮速度 = (2*线速度 + 角速度*轮距) / (2*轮半径)
        joint_velocities = [
            ((2 * command[0]) - (command[1] * self._wheel_base)) / (2 * self._wheel_radius),
            ((2 * command[0]) + (command[1] * self._wheel_base)) / (2 * self._wheel_radius)
        ]
        # 返回关节速度指令（位置和力矩设为None表示不控制）
        return ArticulationAction(
            joint_velocities=joint_velocities,
            joint_positions=None,
            joint_efforts=None
        )
class HelloWorld(BaseSample):
    """Isaac Sim轮式机器人控制示例"""
    def __init__(self) -> None:
        """初始化示例场景"""
        super().__init__()  # 必须调用父类初始化
        return
        
    def setup_scene(self):
        """场景搭建阶段（物理引擎尚未激活）"""
        world = self.get_world()  # 获取世界实例
        world.scene.add_default_ground_plane()  # 添加默认地平面
        # 加载Jetbot机器人USD模型
        assets_root_path = get_assets_root_path()  # 获取Nucleus资产根路径
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        # 创建轮式机器人实例
        # 关键参数说明：
        # - wheel_dof_names: 指定驱动轮关节名称（必须与USD文件内一致）
        # - create_robot: 自动完成物理实体创建
        world.scene.add(
            WheeledRobot(
                prim_path="/World/Fancy_Robot",  # 场景中的Prim路径
                name="fancy_robot",              # 实例名称（用于后续获取）
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],
                create_robot=True,
                usd_path=jetbot_asset_path      # USD模型文件路径
            )
        )
        return

    async def setup_post_load(self):
        """物理重置后的设置阶段（可安全访问物理属性）"""
        self._world = self.get_world()
        # 获取已创建的机器人实例
        self._jetbot = self._world.scene.get_object("fancy_robot")
        # 注册物理回调（每个物理步长自动调用）
        # 注意：回调函数应保持轻量以避免影响实时性
        self._world.add_physics_callback(
            "sending_actions",  # 回调名称（用于调试识别）
            callback_fn=self.send_robot_actions  # 绑定的回调函数
        )
        # 初始化自定义控制器（必须在物理重置后创建）
        self._my_controller = CoolController()
        return

    def send_robot_actions(self, step_size):
        """
        物理步长回调函数
        参数:
            step_size: 当前物理步长时间（单位：秒）
        """
        # 设置控制指令：
        # - 0.20 m/s 前进速度
        # - π/4 rad/s (45度/秒) 转向速度
        target_command = [0.20, np.pi / 4]  
        # 通过控制器计算关节指令
        action = self._my_controller.forward(target_command)
        # 应用动作到机器人
        # 等效方法：self._jetbot.apply_wheel_actions(action)
        self._jetbot.apply_action(action)
        return
```

使用自定义的CoolController类（继承BaseController），定义好物理参数，并通过公式计算把高层的[V，ω]命令转化成可用于两轮机器人的两个轮子的命令，然后返回一个正确的`ArcticulationAction`类给实例。==这样做的好处就是把动作指令抽象出来并解耦，方便后续修改==

`forward` 方法是你在自定义控制器（继承自 `BaseController`）里必须实现的核心接口，用来把“高层运动命令”映射成“底层关节动作”。具体来说：

1. **接口契约**
    
    - 在 `BaseController` 里，`forward(self, command)` 被定义或约定为“接收一组控制命令（如 `[v, ω]`），返回一个 `ArticulationAction`” 的抽象方法。
        
    - Isaac Sim 的控制循环会通过统一接口调用你的控制器——它并不知道你具体用了什么算法，只会调用 `controller.forward(command)` 来获取下一步动作。
        
2. **职责分离**
    
    - **控制器（Controller）**：只负责“把指令变成动作”，也就是 `forward` 里的运动学／动力学计算。
        
    - **示例（Sample）**：负责“何时调用控制器、把动作下发给机器人”，如你在 `send_robot_actions` 里做的那样：
        
```python
        action = self._my_controller.forward(target_command) 
        self._jetbot.apply_action(action)
```
        
3. **为什么一定要它？**
    
    - **统一调用**：Isaac Sim 的框架在物理步回调里，拿到的是一个控制器实例，它只知道“要调用 `forward`”来得到 `ArticulationAction`，然后把它发给机器人。
        
    - **扩展性**：只要你实现了 `forward`，就可以无缝替换成任何复杂的控制算法（PID、MPC、强化学习策略……），而不必改动示例调度和回调那一套框架代码。


### 创建复合控制器

```python
# 导入基础模块
from isaacsim.examples.interactive.base_sample import BaseSample  # 基础示例类
from isaacsim.core.utils.nucleus import get_assets_root_path    # Nucleus资产路径工具
from isaacsim.robot.wheeled_robots.robots import WheeledRobot   # 轮式机器人基类
# 导入控制器模块
from isaacsim.robot.wheeled_robots.controllers.wheel_base_pose_controller import WheelBasePoseController  # 位姿控制器
from isaacsim.robot.wheeled_robots.controllers.differential_controller import DifferentialController  # 差速控制器
import numpy as np  # 数值计算库

class HelloWorld(BaseSample):
    """使用复合控制器的轮式机器人导航示例"""
    
    def __init__(self) -> None:
        """初始化示例"""
        super().__init__()  # 必须调用父类初始化
        return

    def setup_scene(self):
        """场景搭建阶段（物理引擎尚未激活）"""
        world = self.get_world()  # 获取世界实例
        world.scene.add_default_ground_plane()  # 添加默认地平面
        
        # 加载Jetbot机器人USD模型
        assets_root_path = get_assets_root_path()
        if assets_root_path is None:
            raise RuntimeError("未找到Nucleus服务器资产路径")
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        
        # 创建轮式机器人实例
        world.scene.add(
            WheeledRobot(
                prim_path="/World/Fancy_Robot",  # 场景中的Prim路径
                name="fancy_robot",              # 实例名称
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],  # 驱动轮关节名
                create_robot=True,               # 自动创建物理实体
                usd_path=jetbot_asset_path      # USD模型路径
            )
        )
        return

    async def setup_post_load(self):
        """物理重置后的设置阶段"""
        self._world = self.get_world()
        self._jetbot = self._world.scene.get_object("fancy_robot")  # 获取机器人实例
        
        # 初始化复合控制器
        self._my_controller = WheelBasePoseController(
            name="cool_controller",  # 控制器名称
            open_loop_wheel_controller=DifferentialController(
                name="simple_control",  # 底层差速控制器名称
                wheel_radius=0.03,      # 轮半径（单位：米）
                wheel_base=0.1125       # 轮距（单位：米）
            ),
            is_holonomic=False  # 非全向移动机器人
        )
        
        # 注册物理回调（60Hz固定频率）
        self._world.add_physics_callback(
            "sending_actions",  # 回调标识符
            callback_fn=self.send_robot_actions  # 回调函数
        )
        return

    def send_robot_actions(self, step_size):
        """
        物理步长回调函数
        参数:
            step_size: 物理步长时间（单位：秒）
        """
        # 获取当前机器人位姿
        current_pos, current_orn = self._jetbot.get_world_pose()
        
        # 设置目标位置（X=0.8m, Y=0.8m）
        target_pos = np.array([0.8, 0.8])  
        
        # 通过控制器计算控制指令
        action = self._my_controller.forward(
            start_position=current_pos,    # 当前XY位置
            start_orientation=current_orn, # 当前朝向
            goal_position=target_pos       # 目标XY位置
        )
        
        # 应用控制指令到机器人
        self._jetbot.apply_action(action)
        return
```

代码重点：
```python
# 初始化复合控制器
        self._my_controller = WheelBasePoseController(
            name="cool_controller",  # 控制器名称
            open_loop_wheel_controller=DifferentialController(
                name="simple_control",  # 底层差速控制器名称
                wheel_radius=0.03,      # 轮半径（单位：米）
                wheel_base=0.1125       # 轮距（单位：米）
            ),
            is_holonomic=False  # 非全向移动机器人
        )
```

上面的代码实例化一个复合控制器`WheelBasePoseController`，并在其中增加了一个差速控制器`DifferentialController`。

```python
 # 获取当前机器人位姿
        current_pos, current_orn = self._jetbot.get_world_pose()
        
        # 设置目标位置（X=0.8m, Y=0.8m）
        target_pos = np.array([0.8, 0.8])  
        
        # 通过控制器计算控制指令
        action = self._my_controller.forward(
            start_position=current_pos,    # 当前XY位置
            start_orientation=current_orn, # 当前朝向
            goal_position=target_pos       # 目标XY位置
        )
```

然后通过直接获得当前机器人的朝向等姿态，设置好目标位置，让机器人直接过去，简洁高效。



## *机械手*
学习如何添加一个机械臂并实现抓取任务

### Using the PickAndPlace Controller（使用抓放控制器）
```python
# 导入必要的模块
from isaacsim.examples.interactive.base_sample import BaseSample  # 基础示例类
from isaacsim.robot.manipulators.examples.franka import Franka    # Franka机械臂类
from isaacsim.core.api.objects import DynamicCuboid               # 动态立方体
from isaacsim.robot.manipulators.examples.franka.controllers import PickPlaceController  # 抓放控制器
import numpy as np  # 数值计算库

class HelloWorld(BaseSample):
    """Franka机械臂抓取放置示例"""
    
    def __init__(self) -> None:
        """初始化示例"""
        super().__init__()  # 必须调用父类初始化
        return

    def setup_scene(self):
        """场景搭建阶段"""
        world = self.get_world()
        world.scene.add_default_ground_plane()  # 添加地平面
        
        # 添加Franka机械臂
        franka = world.scene.add(
            Franka(
                prim_path="/World/Fancy_Franka",  # USD场景中的路径
                name="fancy_franka"               # 实例名称
            )
        )
        
        # 添加可抓取的蓝色立方体
        world.scene.add(
            DynamicCuboid(
                prim_path="/World/random_cube",
                name="fancy_cube",
                position=np.array([0.3, 0.3, 0.3]),  # 初始位置(X,Y,Z)
                scale=np.array([0.0515, 0.0515, 0.0515]),  # 尺寸缩放
                color=np.array([0, 0, 1.0]),  # RGB蓝色
            )
        )
        return

    async def setup_post_load(self):
        """物理初始化后设置"""
        self._world = self.get_world()
        self._franka = self._world.scene.get_object("fancy_franka")  # 获取机械臂实例
        self._fancy_cube = self._world.scene.get_object("fancy_cube")  # 获取立方体实例
        
        # 初始化抓放控制器
        self._controller = PickPlaceController(
            name="pick_place_controller",
            gripper=self._franka.gripper,          # 绑定机械臂夹爪
            robot_articulation=self._franka        # 绑定机械臂本体
        )
        
        # 注册物理回调（固定60Hz频率）
        self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
        
        # 初始化夹爪为打开状态
        self._franka.gripper.set_joint_positions(
            self._franka.gripper.joint_opened_positions  # 使用预设的张开角度
        )
        
        # 启动模拟（异步版本避免阻塞）
        await self._world.play_async()
        return

    async def setup_post_reset(self):
        """重置后初始化"""
        self._controller.reset()  # 重置控制器状态机
        self._franka.gripper.set_joint_positions(
            self._franka.gripper.joint_opened_positions
        )
        await self._world.play_async()  # 重启模拟
        return

    def physics_step(self, step_size):
        """
        物理步长回调函数（核心控制逻辑）
        参数:
            step_size: 物理步长时间（秒）
        """
        # 获取立方体当前位置（仅需XYZ坐标）
        cube_position, _ = self._fancy_cube.get_world_pose()
        
        # 设置目标放置位置（Z坐标考虑立方体半高）
        goal_position = np.array([-0.3, -0.3, 0.0515 / 2.0])  
        
        # 获取机械臂当前关节角度
        current_joint_positions = self._franka.get_joint_positions()
        
        # 通过控制器计算动作指令
        actions = self._controller.forward(
            picking_position=cube_position,          # 抓取点坐标
            placing_position=goal_position,          # 放置点坐标
            current_joint_positions=current_joint_positions  # 当前关节状态
        )
        
        # 应用控制指令到机械臂
        self._franka.apply_action(actions)
        
        # 检查任务是否完成（状态机到达终态）
        if self._controller.is_done():
            self._world.pause()  # 暂停模拟
        return
```

==高层状态机封装==
```python
self._controller = PickPlaceController(
    name="pick_place_controller",
    gripper=self._franka.gripper,
    robot_articulation=self._franka
)

```
- `PickPlaceController` 内部已经实现了「接近→下降→闭爪→抬起→移动→下降→开爪→收回」的完整状态机，你只要初始化一次即可。
    
- 省去了自己编写每个阶段轨迹规划、时序切换、夹爪控制等琐碎逻辑。

==统一的 forward 接口==
```python
actions = self._controller.forward(
    picking_position=cube_position,
    placing_position=goal_position,
    current_joint_positions=current_joint_positions
)
```
- 输入三要素：
    
    1. 抓取点坐标
        
    2. 放置点坐标
        
    3. 当前关节状态
        
- 输出：一次性可直接下发的 `ArticulationAction`（包含机械臂各关节和夹爪命令）。


==与物理回调解耦==

```python
self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
```
- - 在每个物理时间步（60 Hz）中只做“读状态 → 调控制器 → 下发动作 → 检查完成”四步，控制逻辑与示例框架完全分离，易于维护和扩展。
        
- **通用可复用性**
    
    - 该控制器并不依赖 Franka 特殊运动学，只要机械臂支持标准 Articulation API（`get_joint_positions`、`apply_action`）并提供夹爪接口，就能“拿来就用”。
        
    - 你可以更换到 UR、KUKA、甚至自定义机械臂上，复用同样的高层抓放逻辑。


这个示例的==核心流程==就是：

1. **物理回调驱动**  
    每个仿真物理步（默认 60 Hz）都会触发 `physics_step(step_size)`，保证你的控制逻辑和物理引擎同步。
    
2. **状态采样**  
    在 `physics_step` 里，你先读取当前系统状态：
    
    - 拿到物体（立方体）的实时位置
        
    - 拿到机械臂的实时关节角度
        
3. **高层控制器计算**  
    然后把这些状态和你的抓／放目标位置一起，传给 `PickPlaceController.forward(...)`：
    
    - 控制器内部根据抓放状态机，输出本时间步的关节和夹爪命令
        
4. **执行动作**  
    最后把 `actions` 直接下发给机械臂：
    
    这样机械臂就会在物理引擎中按照控制器算好的轨迹一步步运动。
    
5. **任务终止检查**  
    控制器内部维护了状态机，当抓放序列全部完成后，`is_done()` 返回 `True`，示例就调用 `world.pause()` 停止仿真。


### 标准化任务Task
什么是==Task==？ NVIDIA Isaac Sim 中的 Task 类提供了一种模块化场景创建、信息检索和计算指标的方法。它有助于创建具有复杂逻辑的更复杂场景。你需要使用 Task 类重写之前的代码。

```python
# 导入模块
from isaacsim.examples.interactive.base_sample import BaseSample
from isaacsim.robot.manipulators.examples.franka import Franka
from isaacsim.core.api.objects import DynamicCuboid
from isaacsim.robot.manipulators.examples.franka.controllers import PickPlaceController
from isaacsim.core.api.tasks import BaseTask  # 任务系统基类
import numpy as np

class FrankaPlaying(BaseTask):
    """Franka机械臂抓取任务（继承BaseTask实现标准化任务）"""
    
    def __init__(self, name):
        """
        初始化任务
        参数:
            name: 任务名称（需唯一）
            offset: 可选的世界坐标系偏移量
        """
        super().__init__(name=name, offset=None)
        self._goal_position = np.array([-0.3, -0.3, 0.0515 / 2.0])  # 放置目标位置
        self._task_achieved = False  # 任务完成标志
        return

    def set_up_scene(self, scene):
        """任务场景搭建（自动由世界调用）"""
        super().set_up_scene(scene)
        scene.add_default_ground_plane()  # 添加地平面
        
        # 添加动态立方体（抓取目标）
        self._cube = scene.add(DynamicCuboid(
            prim_path="/World/random_cube",
            name="fancy_cube",
            position=np.array([0.3, 0.3, 0.3]),  # 初始位置
            scale=np.array([0.0515, 0.0515, 0.0515]),  # 尺寸（5.15cm立方体）
            color=np.array([0, 0, 1.0])  # 初始蓝色
        ))
        
        # 添加Franka机械臂
        self._franka = scene.add(Franka(
            prim_path="/World/Fancy_Franka",
            name="fancy_franka"
        ))
        return

    def get_observations(self):
        """获取任务观测数据（供控制器使用）"""
        cube_position, _ = self._cube.get_world_pose()
        current_joint_positions = self._franka.get_joint_positions()
        
        # 返回结构化观测数据
        observations = {
            self._franka.name: {
                "joint_positions": current_joint_positions,  # 机械臂关节角度
            },
            self._cube.name: {
                "position": cube_position,          # 立方体当前位置
                "goal_position": self._goal_position  # 目标位置
            }
        }
        return observations

    def pre_step(self, control_index, simulation_time):
        """物理步长前的回调（用于任务状态检测）"""
        cube_position, _ = self._cube.get_world_pose()
        
        # 检查是否到达目标位置（欧氏距离<2cm）
        if not self._task_achieved and np.mean(np.abs(self._goal_position - cube_position)) < 0.02:
            # 修改立方体颜色为绿色（任务完成视觉反馈）
            self._cube.get_applied_visual_material().set_color(color=np.array([0, 1.0, 0]))
            self._task_achieved = True
        return

    def post_reset(self):
        """重置任务状态"""
        self._franka.gripper.set_joint_positions(
            self._franka.gripper.joint_opened_positions  # 重置夹爪为打开状态
        )
        self._cube.get_applied_visual_material().set_color(color=np.array([0, 0, 1.0]))  # 重置为蓝色
        self._task_achieved = False  # 重置完成标志
        return


class HelloWorld(BaseSample):
    """主示例类（集成任务系统）"""
    
    def __init__(self) -> None:
        super().__init__()
        return

    def setup_scene(self):
        """场景初始化（将任务添加到世界）"""
        world = self.get_world()
        world.add_task(FrankaPlaying(name="my_first_task"))  # 注册自定义任务
        return

    async def setup_post_load(self):
        """物理初始化后设置"""
        self._world = self.get_world()
        
        # 从任务中获取机械臂实例
        self._franka = self._world.scene.get_object("fancy_franka")
        
        # 初始化抓放控制器
        self._controller = PickPlaceController(
            name="pick_place_controller",
            gripper=self._franka.gripper,      # 绑定夹爪
            robot_articulation=self._franka    # 绑定机械臂
        )
        
        # 注册物理回调
        self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
        await self._world.play_async()  # 启动模拟
        return

    async def setup_post_reset(self):
        """重置后初始化"""
        self._controller.reset()  # 重置控制器状态机
        await self._world.play_async()
        return

    def physics_step(self, step_size):
        """物理步长回调（核心控制逻辑）"""
        # 从任务系统获取最新观测数据
        current_observations = self._world.get_observations()
        
        # 计算控制指令
        actions = self._controller.forward(
            picking_position=current_observations["fancy_cube"]["position"],  # 动态获取立方体位置
            placing_position=current_observations["fancy_cube"]["goal_position"],
            current_joint_positions=current_observations["fancy_franka"]["joint_positions"]
        )
        
        # 应用控制指令
        self._franka.apply_action(actions)
        
        # 检查任务完成状态
        if self._controller.is_done():
            self._world.pause()  # 暂停模拟
        return
```


==1.任务（BaseTask）封装==：

```python
class FrankaPlaying(BaseTask):
    …
    def set_up_scene(self, scene):    # 场景搭建
    def get_observations(self):        # 读取观测
    def pre_step(self,…):             # 任务状态检测
    def post_reset(self):             # 任务重置

```
- 继承 `BaseTask`，自动接入世界的任务管理器，无需手动管理场景、重置、完成判定。
    
- `get_observations` 负责规范化地收集所有“agent”和“object”的状态数据，为控制器提供统一接口。

==2.统一观测接口==

```python
observations = {
  "fancy_franka": {"joint_positions": …},
  "fancy_cube":   {"position": …, "goal_position": …}
}

```
- 上层脚本（HelloWorld）通过 `world.get_observations()` 一次性拿到所有任务观测，解耦了“任务内部状态采样”与“控制器使用”。


==3.预步（pre_step）做完成检测==

```python
def pre_step(self, control_index, simulation_time):
    if 距离 < 阈值:
        标记任务完成，改变立方体颜色

```
- 在每帧执行控制前，用 `pre_step` 检测抓放是否达成，并做视觉反馈（颜色变绿）。
    
- 与控制逻辑分离，职责单一。

==4.主循环中只关注决策与执行==

```python
def physics_step(self, step_size):
    obs = self._world.get_observations()
    actions = self._controller.forward(
        picking_position=obs["fancy_cube"]["position"],
        placing_position=obs["fancy_cube"]["goal_position"],
        current_joint_positions=obs["fancy_franka"]["joint_positions"]
    )
    self._franka.apply_action(actions)
```

- - HelloWorld 不再直接读物体／机械臂状态，而是依赖任务系统提供的观测字典。
        
    - 控制器只关心“给我观测，我给你动作”，形成清晰的“观测→决策→执行”闭环。
        

==5.可复用、可组合==

- 任何遵循同样观测格式的任务，都可以复用这个控制器和循环结构。
    
- 进一步可将多个 Task 同时添加到世界，实现更复杂的多任务协同。


**一句话总结**：
 把“抓放”从示例脚本里剥离成标准化 `BaseTask`——它负责场景搭建、观测输出、完成检测与重置；HelloWorld 只要在每帧拿到这个任务的观测，调用 `PickPlaceController.forward(...)`，再下发动作，就实现了高度解耦、可复用的抓放演示。



## 添加多个机器人

演示如何添加多个机器人

```python
# -*- coding: utf-8 -*-
from isaacsim.examples.interactive.base_sample import BaseSample
from isaacsim.robot.manipulators.examples.franka.tasks import PickPlace
from isaacsim.robot.wheeled_robots.robots import WheeledRobot
from isaacsim.core.utils.nucleus import get_assets_root_path
from isaacsim.robot.wheeled_robots.controllers.wheel_base_pose_controller import WheelBasePoseController
from isaacsim.robot.manipulators.examples.franka.controllers import PickPlaceController
from isaacsim.robot.wheeled_robots.controllers.differential_controller import DifferentialController
from isaacsim.core.api.tasks import BaseTask
from isaacsim.core.utils.types import ArticulationAction
import numpy as np


class RobotsPlaying(BaseTask):
    """Franka机械臂与Jetbot协同任务（状态机实现）"""
    
    def __init__(
        self,
        name
    ):
        # 调用父类初始化并设置任务名
        super().__init__(name=name, offset=None)
        # Jetbot的目标位置 [X, Y, Z]
        self._jetbot_goal_position = np.array([1.3, 0.3, 0])
        # 任务状态机初始状态（0: 导航, 1: 减速, 2: 抓取）
        self._task_event = 0
        # 构造子任务：Franka抓取-放置任务
        self._pick_place_task = PickPlace(
            cube_initial_position=np.array([0.1, 0.3, 0.05]),
            target_position=np.array([0.7, -0.3, 0.0515 / 2.0])
        )
        return

    def set_up_scene(self, scene):
        # 父类场景搭建初始化
        super().set_up_scene(scene)
        # 在场景中创建Franka机械臂和立方体（子任务内部实现）
        self._pick_place_task.set_up_scene(scene)
        # 获取Jetbot模型路径并加载
        assets_root_path = get_assets_root_path()
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        self._jetbot = scene.add(
            WheeledRobot(
                prim_path="/World/Fancy_Jetbot",
                name="fancy_jetbot",
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],
                create_robot=True,
                usd_path=jetbot_asset_path,
                position=np.array([0, 0.3, 0]),  # 初始位置在Y轴偏移0.3m
            )
        )
        # 调整Franka机械臂的初始位置，避免与Jetbot或立方体冲突
        pick_place_params = self._pick_place_task.get_params()
        self._franka = scene.get_object(pick_place_params["robot_name"]["value"])
        self._franka.set_world_pose(position=np.array([1.0, 0, 0]))
        self._franka.set_default_state(position=np.array([1.0, 0, 0]))
        return

    def get_observations(self):
        # 获取Jetbot当前位置与朝向
        current_jetbot_position, current_jetbot_orientation = self._jetbot.get_world_pose()
        # 构造观测字典，包含任务状态与Jetbot信息
        observations= {
            "task_event": self._task_event,
            self._jetbot.name: {
                "position": current_jetbot_position,
                "orientation": current_jetbot_orientation,
                "goal_position": self._jetbot_goal_position
            }
        }
        # 合并Franka抓放子任务的观测
        observations.update(self._pick_place_task.get_observations())
        return observations

    def get_params(self):
        # 获取子任务参数，并添加Jetbot和Franka名称
        pick_place_params = self._pick_place_task.get_params()
        params_representation = pick_place_params
        params_representation["jetbot_name"] = {"value": self._jetbot.name, "modifiable": False}
        params_representation["franka_name"] = pick_place_params["robot_name"]
        return params_representation

    def pre_step(self, control_index, simulation_time):
        # 状态0：Jetbot导航阶段，判断是否到达目标
        if self._task_event == 0:
            current_jetbot_position, _ = self._jetbot.get_world_pose()
            # 当XY平面误差小于4cm时，切换到状态1
            if np.mean(np.abs(current_jetbot_position[:2] - self._jetbot_goal_position[:2])) < 0.04:
                self._task_event += 1
                self._cube_arrive_step_index = control_index
        # 状态1：减速缓冲阶段，固定等待200步后进入抓取状态
        elif self._task_event == 1:
            if control_index - self._cube_arrive_step_index == 200:
                self._task_event += 1
        return

    def post_reset(self):
        # 重置时打开Franka夹爪，并回到导航初始状态
        self._franka.gripper.set_joint_positions(
            self._franka.gripper.joint_opened_positions
        )
        self._task_event = 0
        return


class HelloWorld(BaseSample):
    """主程序：集成多机器人协同仿真"""
    
    def __init__(self) -> None:
        super().__init__()
        return

    def setup_scene(self):
        # 注入复合任务到世界中
        world = self.get_world()
        world.add_task(RobotsPlaying(name="awesome_task"))
        return

    async def setup_post_load(self):
        # 物理加载后获取世界与任务参数
        self._world = self.get_world()
        task_params = self._world.get_task("awesome_task").get_params()
        # 获取Franka和Jetbot实例，用于后续控制
        self._franka = self._world.scene.get_object(task_params["franka_name"]["value"])
        self._jetbot = self._world.scene.get_object(task_params["jetbot_name"]["value"])
        # 获取立方体名称，用于抓取控制
        self._cube_name = task_params["cube_name"]["value"]
        # 初始化Franka抓放控制器
        self._franka_controller = PickPlaceController(
            name="pick_place_controller",
            gripper=self._franka.gripper,
            robot_articulation=self._franka
        )
        # 初始化Jetbot位姿控制器（差速）
        self._jetbot_controller = WheelBasePoseController(
            name="cool_controller",
            open_loop_wheel_controller=
                DifferentialController(name="simple_control", wheel_radius=0.03, wheel_base=0.1125)
        )
        # 注册仿真每步回调并启动异步仿真
        self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
        await self._world.play_async()
        return

    async def setup_post_reset(self):
        # 在每次仿真重置后，重置控制器状态并继续仿真
        self._franka_controller.reset()
        self._jetbot_controller.reset()
        await self._world.play_async()
        return

    def physics_step(self, step_size):
        # 每个物理步根据状态机选择执行导航、刹车或抓取动作
        current_observations = self._world.get_observations()
        # 状态0：Jetbot导航
        if current_observations["task_event"] == 0:
            self._jetbot.apply_wheel_actions(
                self._jetbot_controller.forward(
                    start_position=current_observations[self._jetbot.name]["position"],
                    start_orientation=current_observations[self._jetbot.name]["orientation"],
                    goal_position=current_observations[self._jetbot.name]["goal_position"]
                )
            )
        # 状态1：Jetbot急刹车
        elif current_observations["task_event"] == 1:
            self._jetbot.apply_wheel_actions(ArticulationAction(joint_velocities=[-8, -8]))
        # 状态2：Franka执行抓取
        elif current_observations["task_event"] == 2:
            # 停止Jetbot
            self._jetbot.apply_wheel_actions(ArticulationAction(joint_velocities=[0.0, 0.0]))
            # 计算并下发Franka抓放动作
            actions = self._franka_controller.forward(
                picking_position=current_observations[self._cube_name]["position"],
                placing_position=current_observations[self._cube_name]["target_position"],
                current_joint_positions=current_observations[self._franka.name]["joint_positions"]
            )
            self._franka.apply_action(actions)
        # 如果抓放完成，则暂停仿真
        if self._franka_controller.is_done():
            self._world.pause()
        return

```

代码解读：

1. `RobotPlaying类`
   
	- 首先在初始化中设置jetbot的位置和任务状态机，并使用`PickPlace`模块创建一个`franka`机械臂抓取的子任务。
	  
	- 在`set_up_scene（）`中调用子任务的方法来设置franka机械臂和立方体，然后自定义添加jetbot，最后调整franka机械臂的位置。
	  
	- 在`get_observations`中，先构建观测字典，包括jetbot的位置，朝向，目标位置，任务状态机等，然后通`observations.update(self._pick_place_task.get_observations())`来合并完整的观测字典。

	- 使用`get_params`来定义一个外部接口，返回聚合的参数，供外部脚本来初始化配置的。

	- 最后使用`pre_step`来进行状态转移。`post_reset`方法用来重置任务


2. `HelloWorld`类
	在这个类中执行主要的活动，调用任务来搭建任务场景，初始化获取对应的实例，控制器等，最后按照观测数据来执行对应的任务。



## 多个任务

#### 示例1

```python
from isaacsim.examples.interactive.base_sample import BaseSample
from isaacsim.core.utils.nucleus import get_assets_root_path
from isaacsim.robot.manipulators.examples.franka.tasks import PickPlace
from isaacsim.robot.manipulators.examples.franka.controllers import PickPlaceController
from isaacsim.robot.wheeled_robots.robots import WheeledRobot
from isaacsim.robot.wheeled_robots.controllers.wheel_base_pose_controller import WheelBasePoseController
from isaacsim.robot.wheeled_robots.controllers.differential_controller import DifferentialController
from isaacsim.core.api.tasks import BaseTask
from isaacsim.core.utils.types import ArticulationAction
from isaacsim.core.utils.string import find_unique_string_name
from isaacsim.core.utils.prims import is_prim_path_valid
import numpy as np


class RobotsPlaying(BaseTask):
    def __init__(self, name, offset=None):
        # 调用父类构造，传入任务名和可选偏移量
        super().__init__(name=name, offset=offset)
        # 任务状态机：0=导航，1=刹车等待，2=执行抓放
        self._task_event = 0
        # 随机化 Jetbot 的目标 X 坐标，并加上整体场景偏移
        self._jetbot_goal_position = (
            np.array([np.random.uniform(1.2, 1.6), 0.3, 0])  # 随机目标点
            + self._offset                                 # 全局偏移
        )
        # 创建一个 PickPlace 子任务，并传递相同的偏移量
        self._pick_place_task = PickPlace(
            cube_initial_position=np.array([0.1, 0.3, 0.05]),      # 初始方块位置
            target_position=np.array([0.7, -0.3, 0.0515 / 2.0]),   # 放置目标位置
            offset=offset                                         # 子任务也平移
        )
        return

    def set_up_scene(self, scene):
        # 父类场景初始化（包括保存 scene 引用、注册基础对象列表等）
        super().set_up_scene(scene)
        # 初始化子任务的场景（创建 Franka + 方块 + 目标）
        self._pick_place_task.set_up_scene(scene)

        # 为 Jetbot 生成唯一的实例名，避免重名
        jetbot_name = find_unique_string_name(
            initial_name="fancy_jetbot",
            is_unique_fn=lambda x: not self.scene.object_exists(x)
        )
        # 为 Jetbot 生成唯一的 USD prim 路径，避免路径冲突
        jetbot_prim_path = find_unique_string_name(
            initial_name="/World/Fancy_Jetbot",
            is_unique_fn=lambda x: not is_prim_path_valid(x)
        )
        # 获取本地资产根目录，拼接 Jetbot 的 USD 文件路径
        assets_root_path = get_assets_root_path()
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        # 将 WheeledRobot（Jetbot）添加到场景
        self._jetbot = scene.add(
            WheeledRobot(
                prim_path=jetbot_prim_path,       # 唯一路径
                name=jetbot_name,                 # 唯一名称
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],
                create_robot=True,
                usd_path=jetbot_asset_path,       # USD 模型文件
                position=np.array([0, 0.3, 0]),   # 初始放置位置（局部）
            )
        )
        # 把 Jetbot 加入 BaseTask 管理的对象列表，以便统一偏移、重置
        self._task_objects[self._jetbot.name] = self._jetbot

        # 获取子任务里 Franka 的实例名，然后从 scene 中获取对象引用
        pick_place_params = self._pick_place_task.get_params()
        self._franka = scene.get_object(pick_place_params["robot_name"]["value"])
        # 读取当前 Franka 世界坐标
        current_position, _ = self._franka.get_world_pose()
        # 将 Franka 在 X 方向平移 +1m（带入偏移后意义是叠加两级移动）
        new_franka_pos = current_position + np.array([1.0, 0, 0])
        self._franka.set_world_pose(position=new_franka_pos)
        self._franka.set_default_state(position=new_franka_pos)

        # 对所有注册在 self._task_objects 的对象，执行一次整体偏移
        # 包括 Jetbot、Franka、方块、目标
        self._move_task_objects_to_their_frame()
        return

    def get_observations(self):
        # 读取 Jetbot 当前位姿
        current_jetbot_position, current_jetbot_orientation = \
            self._jetbot.get_world_pose()
        # 构造本任务的观测信息，名称后缀加 "_event" 保证多任务环境下的唯一性
        observations = {
            self.name + "_event": self._task_event,
            self._jetbot.name: {
                "position": current_jetbot_position,
                "orientation": current_jetbot_orientation,
                "goal_position": self._jetbot_goal_position
            }
        }
        # 将子任务的观测信息合并进来
        observations.update(self._pick_place_task.get_observations())
        return observations

    def get_params(self):
        # 从子任务获取参数字典（包含 cube_name、robot_name 等）
        pick_place_params = self._pick_place_task.get_params()
        params_representation = pick_place_params
        # 把 Jetbot 的名字也加入参数，并标记为不可修改
        params_representation["jetbot_name"] = {
            "value": self._jetbot.name,
            "modifiable": False
        }
        # Franka 名称沿用子任务的 robot_name 参数
        params_representation["franka_name"] = pick_place_params["robot_name"]
        return params_representation

    def pre_step(self, control_index, simulation_time):
        # 状态机逻辑：
        # 0=导航到目标，1=刹车等待，2=切换到抓放子任务
        if self._task_event == 0:
            pos, _ = self._jetbot.get_world_pose()
            # XY 平面距离小于 0.04m → 进入等待阶段
            if np.mean(np.abs(pos[:2] - self._jetbot_goal_position[:2])) < 0.04:
                self._task_event += 1
                # 记录进入阶段的步索引，用于时序延迟
                self._cube_arrive_step_index = control_index
        elif self._task_event == 1:
            # 等待固定 200 步后，切换到抓放
            if control_index - self._cube_arrive_step_index == 200:
                self._task_event += 1
        return

    def post_reset(self):
        # 每次重置后打开 Franka 夹爪，并将状态机回到初始
        self._franka.gripper.set_joint_positions(
            self._franka.gripper.joint_opened_positions
        )
        self._task_event = 0
        return


class HelloWorld(BaseSample):
    def __init__(self) -> None:
        super().__init__()
        # 用于存储所有子任务的列表
        self._tasks = []
        self._num_of_tasks = 3  # 同时运行几个任务
        # 控制器列表
        self._franka_controllers = []
        self._jetbot_controllers = []
        # 各种机器人对象和方块名称
        self._jetbots = []
        self._frankas = []
        self._cube_names = []
        return

    def setup_scene(self):
        # 在世界中创建多个 RobotsPlaying 任务，并沿 Y 方向分散
        world = self.get_world()
        for i in range(self._num_of_tasks):
            world.add_task(
                RobotsPlaying(
                    name="my_awesome_task_" + str(i),
                    offset=np.array([0, (i * 2) - 3, 0])  # 每个任务偏移不同的 Y
                )
            )
        return

    async def setup_post_load(self):
        # 场景加载后，获取每个任务和对应的机器人／方块
        self._world = self.get_world()
        for i in range(self._num_of_tasks):
            # 存储任务实例
            task = self._world.get_task(name="my_awesome_task_" + str(i))
            self._tasks.append(task)
            params = task.get_params()
            # 获取 Franka、Jetbot 实例
            self._frankas.append(
                self._world.scene.get_object(params["franka_name"]["value"])
            )
            self._jetbots.append(
                self._world.scene.get_object(params["jetbot_name"]["value"])
            )
            # 记录方块名称，以便观察其位置
            self._cube_names.append(params["cube_name"]["value"])
            # 为每个 Franka 创建一个 PickPlaceController，并指定自定义的事件时序
            self._franka_controllers.append(
                PickPlaceController(
                    name="pick_place_controller",
                    gripper=self._frankas[i].gripper,
                    robot_articulation=self._frankas[i],
                    # 事件时序列表，控制夹取／放置各阶段的时长
                    events_dt=[0.008, 0.002, 0.5, 0.1, 0.05, 0.05, 0.0025, 1, 0.008, 0.08]
                )
            )
            # 为每个 Jetbot 创建差速控制器
            self._jetbot_controllers.append(
                WheelBasePoseController(
                    name="cool_controller",
                    open_loop_wheel_controller=DifferentialController(
                        name="simple_control",
                        wheel_radius=0.03,
                        wheel_base=0.1125
                    )
                )
            )
        # 注册每个物理仿真步的回调函数
        self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
        await self._world.play_async()
        return

    async def setup_post_reset(self):
        # 重置时清空每个控制器的状态，然后继续仿真
        for ctrl in self._franka_controllers:
            ctrl.reset()
        for ctrl in self._jetbot_controllers:
            ctrl.reset()
        await self._world.play_async()
        return

    def physics_step(self, step_size):
        # 每步仿真中，根据不同任务的状态机做动作分配
        current_observations = self._world.get_observations()
        for i, task in enumerate(self._tasks):
            event_key = task.name + "_event"
            jetbot = self._jetbots[i]
            franka = self._frankas[i]
            cube_name = self._cube_names[i]
            # 状态 0：导航
            if current_observations[event_key] == 0:
                action = self._jetbot_controllers[i].forward(
                    start_position=current_observations[jetbot.name]["position"],
                    start_orientation=current_observations[jetbot.name]["orientation"],
                    goal_position=current_observations[jetbot.name]["goal_position"]
                )
                jetbot.apply_wheel_actions(action)
            # 状态 1：急刹
            elif current_observations[event_key] == 1:
                jetbot.apply_wheel_actions(
                    ArticulationAction(joint_velocities=[-8.0, -8.0])
                )
            # 状态 2：停止并执行抓放
            elif current_observations[event_key] == 2:
                # 先停车
                jetbot.apply_wheel_actions(
                    ArticulationAction(joint_velocities=[0.0, 0.0])
                )
                # 然后让 Franka 执行 pick & place
                actions = self._franka_controllers[i].forward(
                    picking_position=current_observations[cube_name]["position"],
                    placing_position=current_observations[cube_name]["target_position"],
                    current_joint_positions=current_observations[franka.name]["joint_positions"]
                )
                franka.apply_action(actions)
        return

    def world_cleanup(self):
        # 热重载或脚本清除时，清空所有列表，避免残留引用
        self._tasks = []
        self._franka_controllers = []
        self._jetbot_controllers = []
        self._jetbots = []
        self._frankas = []
        self._cube_names = []
        return

```

*整体代码编写流程

在RobotsPlaying中，先定义任务状态机，随机化jetbot 的目标位置，并通过pickplace创建一个franka的子任务。
 - 场景设置中，先初始化父类场景，然后初始化子任务场景，然后添加jetbot，对franka进行偏移，最后再整体偏移一次。
 - 然后就是任务状态机逻辑的编写

在HelloWorld中，创建列表来存储任务和任务的对象，通过循环创建并加入偏移来把不同的任务的位置分开，在物理仿真中，也是根据不同任务的状态机进行循环分配指令。

----



***问答1

为什么在设置场景的时候，只有`self._task_objects[self._jetbot.name]= self._jetbot`，而不对franka进行这一操作？
- 因为 Franka 是由你在 `RobotsPlaying` 里组合进来的那个 `PickPlace` 子任务创建并注册的，所以它的生命周期（偏移、重置、管理）都已经在 `PickPlace.set_up_scene` 内部完成了；而在 `RobotsPlaying` 自己的 `self._task_objects` 里，只需要登记它自己新创建的对象——也就是 Jetbot——才能保证后续调用 `_move_task_objects_to_their_frame()` 和重置时，Jetbot 会按照偏移量一起被平移或复位。

简单说，就是：

- **Franka 和方块、目标，都在 `PickPlace` 里注册到它自己的 `task_objects`**，不需要在外层再重复；
    
- **Jetbot 是 `RobotsPlaying` 层新加的实体，所以要在这一级的 `self._task_objects` 里登记，才能让它参与统一的偏移／重置流程**。

---
***问答2

在def set_up_scene(self, scene):中，为什么要用get_params来获取参数，并且后续获取franka实例？难道嵌套的子任务的参数没办法直接被主任务获取么？

- 1. **子任务封装与解耦**  
    `PickPlace` 作为一个独立的子模块，它自己管理——创建、命名、偏移、重置——方块、抓手、机械臂等一系列对象。为了不让外层的 `RobotsPlaying` 直接依赖它的内部属性，`PickPlace` 提供了一个干净的接口：`get_params()`，把所有重要的“结果”／“句柄”都以字典形式暴露出来（比如 `"robot_name": {...}`）。  
    这样做一方面保证了子模块的封装性，一方面也让外层任务拿到必要的信息而不需要关心子任务的内部实现细节。
    
2. **对象创建时机与唯一性**  
    `PickPlace.set_up_scene(scene)` 会在场景里真正加载 Franka 的 USD 模型，给它起一个唯一的名字和 prim 路径。此时 `RobotsPlaying` 并不知道名字是什么，除非让子任务告诉它。而且子任务内部可能会对名字做随机或防冲突处理（`find_unique_string_name`），所以外层必须通过子任务提供的参数来查，而不能“硬编码”或直接访问子任务的私有成员。
    

总结一下：

> 虽然 `PickPlace` 把 Franka 放到场景里了，但它在自己内部用私有属性保存；外层任务要拿到这个实例，只能通过子任务公开的 `get_params()` 接口获取它的名字，再通过场景查回对象。这样既保持了模块间的低耦合，又能确保获取到正确的、经子任务唯一化处理过的机器人实例。

---
***问答3
	
对任务偏移的理解

- - **本地立方体（任务坐标系）的定义**
    
    - 在 `PickPlace` 或 `RobotsPlaying` 里，你所有的坐标（方块初始位置、Franka 机械臂基座、Jetbot 目标点……）都写成相对于本地原点 `(0,0,0)` 的局部坐标。
        
    - 这就好像在这个本地立方体里画了一个小场景，里面放好了你的机器人和物体。
        
- **`offset`：把本地立方体在全局（World）里搬家**
    
    - 当你 new 一个 `RobotsPlaying(name, offset)`，这个 `offset` 就是告诉它要把“立方体”移动到世界坐标系里的哪个位置。
        
    - 例如 `offset = [0, 2, 0]`，就把整个小场景往 Y 轴正方向平移 2 米。
        
- **内部偏移（局部坐标）→ 世界坐标**
    
    - 每个物体先在本地立方体里定好位置，比如 Fr­anka 放在 `[0.1, 0, 0]`，方块放在 `[0.3, 0.2, 0]`。
        
    - 渲染到世界时，系统就把它们的局部坐标加上 `offset`，例如世界里的实际坐标是 `[0.1, 0, 0] + [0, 2, 0] = [0.1, 2, 0]`。
        
- **再一次整体平移**
    
    - 最后调用 `move_task_objects_to_their_frame()`，相当于对整个“立方体”做一次保险式的同步搬迁，把所有已经注册的物体都按那个同样的 `offset` 推到世界里对应位置，保证与第一次加偏移完全一致。

---


### 示例2

```python
# 导入必要的模块和类
from isaacsim.examples.interactive.base_sample import BaseSample
from isaacsim.core.utils.nucleus import get_assets_root_path
from isaacsim.robot.manipulators.examples.franka.tasks import PickPlace
from isaacsim.robot.manipulators.examples.franka.controllers import PickPlaceController
from isaacsim.robot.wheeled_robots.robots import WheeledRobot
from isaacsim.robot.wheeled_robots.controllers.wheel_base_pose_controller import WheelBasePoseController
from isaacsim.robot.wheeled_robots.controllers.differential_controller import DifferentialController
from isaacsim.core.api.tasks import BaseTask
from isaacsim.core.utils.types import ArticulationAction
from isaacsim.core.utils.string import find_unique_string_name
from isaacsim.core.utils.prims import is_prim_path_valid
import numpy as np

# 定义RobotsPlaying类，继承自BaseTask
class RobotsPlaying(BaseTask):
    def __init__(self, name, offset=None):
        # 调用父类构造函数
        super().__init__(name=name, offset=offset)
        # 初始化任务事件为0
        self._task_event = 0
        # 随机生成Jetbot的目标位置
        self._jetbot_goal_position = np.array([np.random.uniform(1.2, 1.6), 0.3, 0]) + self._offset
        # 创建PickPlace任务实例，设置立方体的初始位置和目标位置
        self._pick_place_task = PickPlace(cube_initial_position=np.array([0.1, 0.3, 0.05]),
                                        target_position=np.array([0.7, -0.3, 0.0515 / 2.0]),
                                        offset=offset)
        return

    # 设置场景
    def set_up_scene(self, scene):
        super().set_up_scene(scene)
        # 设置PickPlace任务的场景
        self._pick_place_task.set_up_scene(scene)
        
        # 查找唯一的Jetbot名称和prim路径
        jetbot_name = find_unique_string_name(
            initial_name="fancy_jetbot", is_unique_fn=lambda x: not self.scene.object_exists(x)
        )
        jetbot_prim_path = find_unique_string_name(
            initial_name="/World/Fancy_Jetbot", is_unique_fn=lambda x: not is_prim_path_valid(x)
        )
        
        # 获取资源根路径
        assets_root_path = get_assets_root_path()
        # 设置Jetbot的USD文件路径
        jetbot_asset_path = assets_root_path + "/Isaac/Robots/Jetbot/jetbot.usd"
        
        # 向场景中添加Jetbot
        self._jetbot = scene.add(
            WheeledRobot(
                prim_path=jetbot_prim_path,
                name=jetbot_name,
                wheel_dof_names=["left_wheel_joint", "right_wheel_joint"],  # 设置轮子的关节名称
                create_robot=True,
                usd_path=jetbot_asset_path,
                position=np.array([0, 0.3, 0]),  # 设置初始位置
            )
        )
        # 将Jetbot添加到任务对象字典中
        self._task_objects[self._jetbot.name] = self._jetbot
        
        # 获取PickPlace任务的参数
        pick_place_params = self._pick_place_task.get_params()
        # 获取Franka机械臂对象
        self._franka = scene.get_object(pick_place_params["robot_name"]["value"])
        # 获取Franka的当前位置并调整位置
        current_position, _ = self._franka.get_world_pose()
        self._franka.set_world_pose(position=current_position + np.array([1.0, 0, 0]))
        self._franka.set_default_state(position=current_position + np.array([1.0, 0, 0]))
        
        # 将任务对象移动到它们的坐标系中
        self._move_task_objects_to_their_frame()
        return

    # 获取观察值
    def get_observations(self):
        # 获取Jetbot的当前位置和方向
        current_jetbot_position, current_jetbot_orientation = self._jetbot.get_world_pose()
        observations= {
            self.name + "_event": self._task_event,  # 添加任务事件，确保名称唯一
            self._jetbot.name: {
                "position": current_jetbot_position,
                "orientation": current_jetbot_orientation,
                "goal_position": self._jetbot_goal_position  # Jetbot的目标位置
            }
        }
        # 合并PickPlace任务的观察值
        observations.update(self._pick_place_task.get_observations())
        return observations

    # 获取参数
    def get_params(self):
        # 获取PickPlace任务的参数
        pick_place_params = self._pick_place_task.get_params()
        params_representation = pick_place_params
        # 添加Jetbot和Franka的名称参数
        params_representation["jetbot_name"] = {"value": self._jetbot.name, "modifiable": False}
        params_representation["franka_name"] = pick_place_params["robot_name"]
        return params_representation

    # 前向步进函数，在每个物理步进前调用
    def pre_step(self, control_index, simulation_time):
        # 任务事件0：Jetbot移动到目标位置
        if self._task_event == 0:
            current_jetbot_position, _ = self._jetbot.get_world_pose()
            # 检查Jetbot是否接近目标位置
            if np.mean(np.abs(current_jetbot_position[:2] - self._jetbot_goal_position[:2])) < 0.04:
                self._task_event += 1  # 更新任务事件
                self._cube_arrive_step_index = control_index  # 记录到达时的控制索引
        # 任务事件1：等待200个控制步进
        elif self._task_event == 1:
            if control_index - self._cube_arrive_step_index == 200:
                self._task_event += 1  # 更新任务事件
        return

    # 重置后处理函数
    def post_reset(self):
        # 打开Franka的夹爪
        self._franka.gripper.set_joint_positions(self._franka.gripper.joint_opened_positions)
        # 重置任务事件
        self._task_event = 0
        return

# 定义HelloWorld类，继承自BaseSample
class HelloWorld(BaseSample):
    def __init__(self) -> None:
        super().__init__()
        # 初始化任务列表
        self._tasks = []
        # 设置任务数量为3
        self._num_of_tasks = 3
        # 初始化控制器列表
        self._franka_controllers = []
        self._jetbot_controllers = []
        # 初始化机器人对象列表
        self._jetbots = []
        self._frankas = []
        # 初始化立方体名称列表
        self._cube_names = []
        return

    # 设置场景
    def setup_scene(self):
        world = self.get_world()
        # 添加3个RobotsPlaying任务，每个任务有不同的偏移量
        for i in range(self._num_of_tasks):
            world.add_task(RobotsPlaying(name="my_awesome_task_" + str(i), offset=np.array([0, (i * 2) - 3, 0])))
        return

    # 异步设置函数，在场景加载后调用
    async def setup_post_load(self):
        self._world = self.get_world()
        for i in range(self._num_of_tasks):
            # 获取任务实例
            self._tasks.append(self._world.get_task(name="my_awesome_task_" + str(i)))
            # 获取任务参数
            task_params = self._tasks[i].get_params()
            # 获取Franka和Jetbot对象
            self._frankas.append(self._world.scene.get_object(task_params["franka_name"]["value"]))
            self._jetbots.append(self._world.scene.get_object(task_params["jetbot_name"]["value"]))
            # 获取立方体名称
            self._cube_names.append(task_params["cube_name"]["value"])
            
            # 创建Franka的PickPlace控制器
            self._franka_controllers.append(PickPlaceController(
                name="pick_place_controller",
                gripper=self._frankas[i].gripper,
                robot_articulation=self._frankas[i],
                # 设置控制器的事件时间间隔，用于控制状态转换速度
                events_dt=[0.008, 0.002, 0.5, 0.1, 0.05, 0.05, 0.0025, 1, 0.008, 0.08]))
            
            # 创建Jetbot的轮式基础姿态控制器
            self._jetbot_controllers.append(WheelBasePoseController(
                name="cool_controller",
                open_loop_wheel_controller=DifferentialController(
                    name="simple_control",
                    wheel_radius=0.03,  # 轮子半径
                    wheel_base=0.1125  # 轮子间距
                )))
        
        # 添加物理步进回调函数
        self._world.add_physics_callback("sim_step", callback_fn=self.physics_step)
        # 异步播放世界
        await self._world.play_async()
        return

    # 异步重置后处理函数
    async def setup_post_reset(self):
        for i in range(len(self._tasks)):
            # 重置所有控制器
            self._franka_controllers[i].reset()
            self._jetbot_controllers[i].reset()
        # 异步播放世界
        await self._world.play_async()
        return

    # 物理步进回调函数
    def physics_step(self, step_size):
        # 获取当前观察值
        current_observations = self._world.get_observations()
        for i in range(len(self._tasks)):
            # 根据任务事件应用不同的控制动作
            if current_observations[self._tasks[i].name + "_event"] == 0:
                # 事件0：控制Jetbot移动到目标位置
                self._jetbots[i].apply_wheel_actions(
                    self._jetbot_controllers[i].forward(
                        start_position=current_observations[self._jetbots[i].name]["position"],
                        start_orientation=current_observations[self._jetbots[i].name]["orientation"],
                        goal_position=current_observations[self._jetbots[i].name]["goal_position"]))
            elif current_observations[self._tasks[i].name + "_event"] == 1:
                # 事件1：控制Jetbot后退
                self._jetbots[i].apply_wheel_actions(ArticulationAction(joint_velocities=[-8.0, -8.0]))
            elif current_observations[self._tasks[i].name + "_event"] == 2:
                # 事件2：控制Jetbot停止，并控制Franka执行抓取放置动作
                self._jetbots[i].apply_wheel_actions(ArticulationAction(joint_velocities=[0.0, 0.0]))
                actions = self._franka_controllers[i].forward(
                    picking_position=current_observations[self._cube_names[i]]["position"],
                    placing_position=current_observations[self._cube_names[i]]["target_position"],
                    current_joint_positions=current_observations[self._frankas[i].name]["joint_positions"])
                self._frankas[i].apply_action(actions)
        return

    # 世界清理函数，在热重载或清除后调用
    def world_cleanup(self):
        # 清空所有列表
        self._tasks = []
        self._franka_controllers = []
        self._jetbot_controllers = []
        self._jetbots = []
        self._frankas = []
        self._cube_names = []
        return
```

 ***一、`RobotsPlaying` 里的改动

1. **更完整的生命周期钩子**
    
    - **新增** `set_up_scene(self, scene)`：取代了之前把所有场景搭建写在构造函数里的做法，现在先在这里把 PickPlace 子任务、Jetbot、Franka 都按顺序挂到 `scene` 上。
        
    - **新增** `pre_step(self, control_index, simulation_time)`：状态机从外层 `physics_step` 搬到任务内部，`pre_step` 里根据 `_task_event` 切换 Jetbot 的导航→急刹→等待→抓放。
        
    - **新增** `post_reset(self)`：每次 reset 时单独打开 Franka 夹爪并把 `_task_event` 重置，比以前在 HelloWorld 里统一清零更本地化。
        
    - **新增** `world_cleanup(self)`：给热重载或退出时清理 `self._task_objects`，防止残留。
        
2. **Jetbot 的创建方式变了**
    
    - 以前是直接在 HelloWorld 里 `world.add_task` 完成，现在在 `set_up_scene` 用 `scene.add(WheeledRobot(...))`，并用 `find_unique_string_name` 去生成 prim 路径 和 名称，保证不重复。
        
3. **Franka 的初始化**
    
    - 通过 `PickPlace` 子任务拿到 `robot_name` 后，直接用 `scene.get_object(...)` 抓取出来，然后手动 `set_world_pose` 和 `set_default_state` 平移到新位置，而不再单纯依赖 offset 叠加。
        
4. **参数与观测的合并**
    
    - `get_observations()` 先构建自己（Jetbot）的观测，再 `update(self._pick_place_task.get_observations())` 把 PickPlace 的观测拼进来。
        
    - `get_params()` 也把 PickPlace 的 `robot_name`、`cube_name` 拷过来，并额外加上 `jetbot_name` 字段。
        

---

***二、`HelloWorld` 驱动层面的改动

1. **物理回调不再做状态机**
    
    - 状态机逻辑全搬到 `RobotsPlaying.pre_step`，HelloWorld 的 `physics_step` 只负责按索引取出各任务的观测，然后仅做「导航→急刹→抓放」动作下发，不再操心事件切换。
        
2. **重置钩子改名并异步化**
    
    - 之前叫 `setup_post_reset`，直接在其中遍历 `ctrl.reset()`；新版本依旧保留这个功能（并加上 `async`），风格上与 `setup_post_load` 对齐。
        
3. **清理接口**
    
    - 新增 `world_cleanup`，在热重载或示例结束时，把所有存的列表（任务、机器人、控制器、方块名）清空，防止内存或引用泄漏。
        
4. **小的命名和组织差异**
    
    - 在遍历任务时，老版 `for i, task in enumerate(self._tasks)` 改成了 `for i in range(len(self._tasks))`，这种写法虽效果一致，但可读性稍逊。
        
    - `setup_scene`、`setup_post_load`、`setup_post_reset` 三大阶段的职责更清晰：`setup_scene` 只挂任务，`setup_post_load` 只抓对象 & 建控制器，`setup_post_reset` 只复位控制器。
        

---

**总结：**

- 新版把「**场景搭建**」、「**状态机**」、「**观测/参数接口**」这几块高度解耦，放到 `RobotsPlaying` 里自己管理；
    
- 而 `HelloWorld` 则更聚焦于「**多任务并行管理**」和「**统一的回调/复位流程**」。



## 数据日志

### 日志记录

代码解读：
```python
def _on_logging_event(self, val):
    # 获取当前仿真世界对象
    world = self.get_world()
    # DataLogger 对象在 World 中默认已定义
    data_logger = world.get_data_logger()  
    
    # 如果数据记录器尚未启动，则进行初始化
    if not data_logger.is_started():
        # 从任务参数中读取机器人与目标物体的名称
        robot_name = self._task_params["robot_name"]["value"]
        target_name = self._task_params["target_name"]["value"]

        # -------------------------------
        # 定义逐帧（每个物理时间步）记录数据的函数
        # 当 DataLogger 已启动时，World 会在每个时间步调用它
        # -------------------------------
        def frame_logging_func(tasks, scene):
            return {
                # 机器人各关节当前位置（转成 list 方便 JSON 序列化）
                "joint_positions": scene.get_object(robot_name)
                                     .get_joint_positions()
                                     .tolist(),
                # 控制器在该步施加的目标关节角
                "applied_joint_positions": scene.get_object(robot_name)
                                                .get_applied_action()
                                                .joint_positions
                                                .tolist(),
                # 目标物体在世界坐标系中的位置
                "target_position": scene.get_object(target_name)
                                        .get_world_pose()[0]
                                        .tolist(),
            }

        # 把记录函数注册到 DataLogger，之后每个物理步都会回调
        data_logger.add_data_frame_logging_func(frame_logging_func)
    
    # 根据 val 决定开始或暂停记录
    if val:
        data_logger.start()   # 开始数据记录
    else:
        data_logger.pause()   # 暂停数据记录
    return

```

初始化数据格式为字典，里面的每个值都是一个list

|字段|含义|
|---|---|
|`joint_positions`|机器人各关节当前实际位置|
|`applied_joint_positions`|当步控制器想要施加的关节位置（目标值）|
|`target_position`|目标物体在世界坐标系下的 XYZ 位置|

把格式化函数注册回调：

```python
data_logger.add_data_frame_logging_func(frame_logging_func)
```


----

日志存储：

```python
def _on_save_data_event(self, log_path):
    # 获取当前仿真世界对象
    world = self.get_world()
    # DataLogger 对象在 World 中默认已定义
    data_logger = world.get_data_logger()

    # 将已收集的数据保存到指定的 JSON 文件
    data_logger.save(log_path=log_path)

    # 重置 DataLogger 内部状态，方便后续重新开始一次新的数据记录
    data_logger.reset()
    return

```


---

### 加载数据并回放

`仅回放franka`

```python
async def _on_replay_trajectory_event_async(self, data_file):
    # 从json文件加载数据
    self._data_logger.load(log_path=data_file)
    world = self.get_world()
    await world.play_async()
    # 添加物理回调函数，用于在每一帧设置关节目标
    world.add_physics_callback("replay_trajectory", 
							    self._on_replay_trajectory_step)
    return

def _on_replay_trajectory_step(self, step_size):
    if self._world.current_time_step_index < self._data_logger.get_num_of_data_frames():
        # 同步时间步并获取同一时间步的数据帧
        data_frame = self._data_logger.get_data_frame(
        data_frame_index=self._world.current_time_step_index)
        # 将记录的动作应用到关节控制器
        self._articulation_controller.apply_action(
  ArticulationAction(
  joint_positions=data_frame.data["applied_joint_positions"])
        )
    return
```


`回放场景`

```python
def _on_replay_scene_step(self, step_size):
    # 判断是否还有数据帧未回放，防止越界
    if self._world.current_time_step_index < self._data_logger.get_num_of_data_frames():
        # 获取目标物体的名称
        target_name = self._task_params["target_name"]["value"]

        # 获取当前时间步对应的数据帧
        data_frame = self._data_logger.get_data_frame(
            data_frame_index=self._world.current_time_step_index
        )

        # 下发关节目标到控制器，模拟重放记录的动作
        self._articulation_controller.apply_action(
            ArticulationAction(joint_positions=data_frame.data["applied_joint_positions"])
        )

        # 将目标物体的位置设置为数据记录中的目标位置（世界坐标系）
        self._world.scene.get_object(target_name).set_world_pose(
            position=np.array(data_frame.data["target_position"])
        )

    return

```

相比于仅回放机械臂franka，增加了对目标物体位置的回放。



# 场景设置

以下脚本只能在默认的新 Stage 上运行一次。你可以先通过 **File → New** 创建一个新 Stage，然后在 **Window → Script Editor** 中运行这些脚本进行尝试。

### 添加物体
下面的代码片段将具有给定属性的`立方体`和一个`地平面`添加到场景中。
```python
import numpy as np
from isaacsim.core.api.objects import DynamicCuboid
from isaacsim.core.api.objects.ground_plane import GroundPlane
from isaacsim.core.api.physics_context import PhysicsContext

# 创建物理上下文：负责初始化物理引擎并开始步进
PhysicsContext()

# 在 /World 下添加一个地面平面
GroundPlane(
    prim_path="/World/groundPlane",  # Stage 路径
    size=10,                         # 边长 10 m 的正方形
    color=np.array([0.5, 0.5, 0.5])  # 中性灰
)

# 在 /World 下添加一个可动态碰撞的立方体
DynamicCuboid(
    prim_path="/World/cube",         # Stage 路径
    position=np.array([-0.5, -0.2, 1.0]),  # 初始位置 (x, y, z)，单位米
    scale=np.array([0.5, 0.5, 0.5]),       # 长宽高各 0.5 m
    color=np.array([0.2, 0.3, 0.0])        # 深绿色
)

```

在 Isaac Sim 脚本里简单地写一句 `PhysicsContext()` 就相当于**初始化／启动 PhysX 物理引擎并为当前 Stage 建立一个物理上下文**（physics scene）。如果场景里已经有物理上下文，这句话会直接返回现成的实例；如果还没有，它就会自动创建并把 PhysX 插件加载进来。

#### PhysX的功能

| 功能              | 具体作用                                                            | 与机器人仿真的关系                                              |
| --------------- | --------------------------------------------------------------- | ------------------------------------------------------ |
| **刚体动力学**       | 计算物体在重力、外力、关节约束下的 6 DoF 运动；处理质量、惯性、线性/角速度等                      | 让机械臂、移动平台、掉落的方块等都能按真实物理运动                              |
| **碰撞检测 & 解析**   | 精确检测几何体接触并计算接触点、法向力、摩擦                                          | 评估抓手与物体接触、箱子堆叠、机器人撞墙等                                  |
| **关节/铰链约束**     | Revolute、Prismatic、Spherical、Fixed 以及复杂的 **Articulation**（多体系统） | Isaac Sim 的 Franka、UR10 等机械臂就是用 PhysX Articulation 建模的 |
| **连续碰撞 (CCD)**  | 避免高速小物体「穿透」                                                     | 让快速甩动的夹爪不穿过薄板                                          |
| **软体/粒子（扩展组件）** | Cloth、流体、Digitial Twins                                         | 某些版本支持柔体或颗粒，用于袋装、倒液体等场景                                |
| **GPU 加速**      | 在支持的显卡上用 CUDA 计算刚体和碰撞，大幅提升大规模场景性能                               | Isaac Sim 可选 “GPU PhysX”，一次模拟上千物体仍保持实时                 |

---
### 视图对象  
此扩展中的视图类是相似图元的集合。视图类以向量化方式操作底层对象。大多数视图API要求在使用前初始化世界和物理模拟。这可以通过将视图类添加到世界的场景并按如下方式重置世界来实现。

```python
from isaacsim.core.api.world import World
from isaacsim.core.prims import RigidPrim
from isaacsim.core.api.objects import DynamicCuboid

# 1) 创建一个全新的 World（含 PhysX 场景、时间步管理等）
world = World()

# 2) 在 /World/cube_0 路径下生成一个可动态碰撞的立方体
cube = DynamicCuboid(prim_path="/World/cube_0")

# 3) 定义一个 RigidPrim 视图：
#    prim_paths_expr 接收正则/通配符表达式，
#    这里匹配 /World/cube_0、/World/cube_1 … /World/cube_100 共 101 个潜在物体
rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-100]")

# 4) 把视图对象添加进场景管理器（Scene）
world.scene.add(rigid_prim)

# 5) 重置 World ：
#    - 把 USD 里的 Prim 实例化到物理场景
#    - 为每个 view 绑定 PhysX 句柄
#    - 刷新缓存（数量、索引、内存指针等）
world.reset()

# 6) 至此，rigid_prim 已完成初始化，可直接调用如下接口
# poses = rigid_prim.get_world_poses()       # 获取所有匹配刚体的世界位姿
# masses = rigid_prim.get_masses()           # 获取质量

```

为什么 **`world.reset()`** 后视图才能用？

| 阶段                                 | View 内部状态                                                                                                       | 说明                                                                      |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **创建 View（第 3 步）**                 | 只记录「表达式」和对 Scene 的引用；**尚未**扫描 Stage，也没有 PhysX 句柄。                                                               |                                                                         |
| **`world.scene.add(view)`（第 4 步）** | 把 View 放进一个待“刷新列表”。但是物体还没真正生成，View 也不会去初始化。                                                                     |                                                                         |
| **`world.reset()`**                | - 加载/实例化所有 Prim → 生成对应 PhysX Actor  <br>‑ 遍历 Scene 中所有 View，对照表达式找到匹配 Prim 并缓存  <br>‑ 建立 GPU/CPU 内存映射，准备批量查询/写入 | View 完整初始化后，才有 `get_world_poses()`、`set_linear_velocities()` 等高效批量 API。 |


如果在 **Isaac Sim Python 脚本** 中运行，上述方法有效。但如果通过 **窗口 -> 脚本编辑器 (Window -> Script Editor)** 运行代码片段，则需要使用 **异步版本** 的 `reset`，如下所示：


```python
import asyncio
from isaacsim.core.api.world import World
from isaacsim.core.prims import RigidPrim
from isaacsim.core.api.objects import DynamicCuboid

async def init():
    # 如果存在World实例则清除
    if World.instance():
        World.instance().clear_instance()
    world = World()
    # 初始化模拟环境
    await world.initialize_simulation_context_async()
    # 添加默认地平面
    world.scene.add_default_ground_plane(z_position=-1.0)
    # 创建立方体对象
    cube = DynamicCuboid(prim_path="/World/cube_0")
    # 创建刚体prim，匹配路径表达式
    rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-100]")
    # 视图类在添加到场景且世界重置时会内部初始化
    world.scene.add(rigid_prim)
    # 重置世界
    await world.reset_async()
    # rigid_prim现在已完成初始化可以使用

# 异步运行初始化函数
asyncio.ensure_future(init())
```

***为什么要这样做？

- **如果你把脚本作为独立的 Isaac Sim Python 启动文件执行**（例如 `./isaac-sim.sh standalone_examples/my_script.py`），脚本里用普通的同步 `world.reset()` 就可以正常工作。
    
- **但如果你在 Isaac Sim 图形界面的 Window → Script Editor 里直接粘贴并运行代码片段**，因为 UI 线程已经在运行自己的事件循环，为了避免阻塞界面，你必须改用 **异步版本的 `reset_async()`**，并在前面加 `await`。
原因是 Script Editor 里的代码会在 Omniverse 的主协程环境中执行，若调用同步的 `reset()` 会阻塞渲染和输入；用异步版本则让出控制权，保证界面流畅。



### 创建 RigidPrim  
以下代码片段向场景中添加三个立方体，并创建一个 RigidPrim（原名为 RigidPrimView）来批量操作它们

```python
import asyncio
import numpy as np
from isaacsim.core.api.world import World
from isaacsim.core.prims import RigidPrim
from isaacsim.core.api.objects import DynamicCuboid

async def example():
    # 检查是否存在World实例，存在则清除
    if World.instance():
        World.instance().clear_instance()
    
    # 创建新的World实例
    world = World()
    # 异步初始化模拟环境
    await world.initialize_simulation_context_async()
    # 添加默认地平面，z轴位置为-1.0
    world.scene.add_default_ground_plane(z_position=-1.0)

    # 创建3个刚体立方体
    for i in range(3):
        # 在指定路径下创建立方体，路径格式为"/World/cube_0"到"/World/cube_2"
        DynamicCuboid(prim_path=f"/World/cube_{i}")

    # 创建视图对象用于批量操作立方体
    # 使用正则表达式匹配路径"/World/cube_0"到"/World/cube_2"
    rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-2]")
    # 将刚体prim添加到场景中
    world.scene.add(rigid_prim)
    # 异步重置世界，完成初始化
    await world.reset_async()
    
    # 设置刚体的世界位姿
    # 使用numpy数组指定三个立方体的位置坐标：
    # 第一个立方体在(0, 0, 2)
    # 第二个立方体在(0, -2, 2) 
    # 第三个立方体在(0, 2, 2)
    rigid_prim.set_world_poses(positions=np.array([[0, 0, 2], [0, -2, 2], [0, 2, 2]]))

# 异步运行示例函数
asyncio.ensure_future(example())
```


这段脚本演示了 **如何批量创建多个刚体立方体，然后用 `RigidPrim` 视图一次性设置它们的世界位姿**。核心流程与之前的示例类似，但这里通过 `set_world_poses()` 把 3 个方块一次性移动到空中（ z = 2 m），随后它们会因重力自由下落。



### 创建RigidContactView
在物理仿真中，有时需要监测两种重要的力学数据：

1. 每个刚体受到的合力（所有接触力的矢量和）
    
2. 特定刚体之间的相互作用力
    

这些功能可以通过RigidPrim管理的RigidContactView工具来实现。


```python
import asyncio
import numpy as np
from isaacsim.core.api.world import World
from isaacsim.core.prims import RigidPrim
from isaacsim.core.api.objects import DynamicCuboid

async def example():
    # 检查并清除现有的World实例
    if World.instance():
        World.instance().clear_instance()
    
    # 创建新的World实例并初始化模拟环境
    world = World()
    await world.initialize_simulation_context_async()
    # 添加默认地平面
    world.scene.add_default_ground_plane()

    # 创建3组上下堆叠的刚体立方体
    for i in range(3):
        # 创建底部立方体（红色，边长2，质量1.0）
        DynamicCuboid(
            prim_path=f"/World/bottom_box_{i+1}", 
            size=2, 
            color=np.array([0.5, 0, 0]),  # RGB红色
            mass=1.0
        )
        # 创建顶部立方体（蓝色，边长2，质量1.0）
        DynamicCuboid(
            prim_path=f"/World/top_box_{i+1}", 
            size=2, 
            color=np.array([0, 0, 0.5]),  # RGB蓝色
            mass=1.0
        )

    # 创建底部立方体的RigidPrim视图
    # 设置接触过滤器仅检测与顶部立方体的接触
    bottom_box = RigidPrim(
        prim_paths_expr="/World/bottom_box_*",  # 匹配所有底部立方体
        name="bottom_box",
        positions=np.array([
            [0, 0, 1.0],     # 第一个立方体位置（中心）
            [-5.0, 0, 1.0],  # 第二个立方体位置（左侧）
            [5.0, 0, 1.0]    # 第三个立方体位置（右侧）
        ]),
        contact_filter_prim_paths_expr=["/World/top_box_*"],  # 设置接触过滤器
        #只保留和top_box_*相关的接触到矩阵中
    )

    # 创建顶部立方体的RigidPrim视图
    # 启用接触力追踪功能
    top_box = RigidPrim(
        prim_paths_expr="/World/top_box_*",  # 匹配所有顶部立方体
        name="top_box",
        positions=np.array([
            [0.0, 0, 3.0],    # 第一个立方体位置（中心上方）
            [-5.0, 0, 3.0],   # 第二个立方体位置（左侧上方）
            [5.0, 0, 3.0]     # 第三个立方体位置（右侧上方）
        ]),
        track_contact_forces=True,  # 启用接触力追踪
    )

    # 将视图对象添加到场景中
    world.scene.add(top_box)
    world.scene.add(bottom_box)
    # 重置模拟世界
    await world.reset_async()

    # 打印底部立方体受到的净接触力
    print("底部立方体净接触力:", bottom_box.get_net_contact_forces())
    # 打印顶部与底部立方体之间的接触力矩阵
    print("接触力矩阵:", bottom_box.get_contact_force_matrix())

# 异步运行示例
asyncio.ensure_future(example())
```

##### 1. `prim_paths_expr="/World/bottom_box_*"` —— `*` 能匹配什么？

`prim_paths_expr` 接收 **USD 路径的“通配/正则表达式”**，用来告诉 `RigidPrim` 要把哪些 Prim 收进视图。  
Isaac Sim 在底层用 **Omni.Kit 的 WildcardMatching**（近似 shell‑style 通配）+ 少量正则扩展 来解析：

|写法|含义|示例会匹配|
|---|---|---|
|`*`|任意长度、任意字符（包括 `/` 之外的所有可见字符）|`/World/cubeA`、`/World/cube_123`|
|`?`|任意单个字符|`/World/cube_1`、`/World/cube_a`|
|`[a‑z]`|字符集合|`[0‑2]` 匹配 `0`,`1`,`2`|
|`[!…]`|取反集合|`[!0‑9]` 匹配非数字|
|`{foo,bar}`|“或” 选择|`/World/{box,sphere}_*`|
|末尾 `/**`|递归匹配子路径|`/World/Robot/**` 匹配 Robot 下所有层级|

##### 2. `contact_filter_prim_paths_expr=["/World/top_box_*"]` —— 过滤器做什么？

- **目的**：让 `RigidPrim` 在 **查询接触/碰撞信息** 时 **只关心与这些表达式匹配的对象**。
    
    - `get_contact_force_matrix()` 生成的矩阵就只列出 “bottom_box_* ↔ top_box_*” 之间的力；与地面或其它物体的接触会被过滤掉（仍参与物理求解，只是不会计入“我要统计的结果”）。



##### 3. `track_contact_forces=True` —— 为什么要开？

- **开关作用**：告诉 PhysX/Isaac Sim **在解算器里累积并存储本视图相关的接触力数据**。
    
- **关闭时（默认 False）**：
    
    - 模拟仍然计算碰撞、施加约束，但 **`get_net_contact_forces()` / `get_contact_force_matrix()` 会直接返回全零**。
        
    - 这样可以节约 GPU/CPU 复制带宽，对没有力反馈需求的大规模场景更高效。
        
- **开启后**：
    
    1. 每物理步（`world.step()`）都会把该刚体受到的 **每个接触点力向量** 汇总成：
        
        - **净接触力**（fx, fy, fz）
            
        - **逐对矩阵**：bottom_i ← top_j 产生的力
            
    2. 这些结果在 **下一帧** 通过视图 API 读取。
---


关于摩擦力和接触力的更详细信息可以分别通过 **get_friction_data** 和 **get_contact_force_data** 接口获取。这些API能够提供传感器基元（sensor prims）与过滤基元（filter prims）之间的所有接触力及接触点数据。其中，**get_contact_force_data** 还可返回接触距离和接触法线向量。

#### 示例说明

在以下示例中，我们向场景添加了三个立方体，并对每个立方体施加大小为10的切向力。随后，通过上述API获取所有接触信息，并汇总各接触点的数据，最终计算出立方体与地平面之间的摩擦力/法向力。


```python
import asyncio
import numpy as np
from isaacsim.core.api.world import World
from isaacsim.core.prims import RigidPrim
from isaacsim.core.api.objects import DynamicCuboid
from isaacsim.core.api.materials.physics_material import PhysicsMaterial
from isaacsim.core.utils.stage import create_new_stage_async, update_stage_async

async def contact_force_example():
    # 设置重力加速度值
    g = 10  
    # 创建新的场景舞台
    await create_new_stage_async()
    
    # 清理现有World实例（如果存在）
    if World.instance():
        World.instance().clear_instance()
    
    # 初始化World和地面
    world = World()
    world.scene.add_default_ground_plane()
    await world.initialize_simulation_context_async()
    
    # 创建物理材质（摩擦系数设为0.5）
    material = PhysicsMaterial(
        prim_path="/World/PhysicsMaterials",
        static_friction=0.5,  # 静摩擦系数
        dynamic_friction=0.5  # 动摩擦系数
    )
    
    # 创建3个蓝色立方体并应用物理材质
    for i in range(3):
        DynamicCuboid(
            prim_path=f"/World/Box_{i+1}", 
            size=2,  # 立方体尺寸
            color=np.array([0, 0, 0.5]),  # 蓝色
            mass=1.0  # 质量1.0
        ).apply_physics_material(material)

    # 创建RigidPrim视图用于获取接触力信息
    # 设置关注立方体与地面之间的接触（最多30个接触点）
    box_view = RigidPrim(
        prim_paths_expr="/World/Box_*",  # 匹配所有立方体
        positions=np.array([
            [0, 0, 1.0],    # 中间立方体
            [-5.0, 0, 1.0], # 左侧立方体
            [5.0, 0, 1.0]   # 右侧立方体
        ]),
        contact_filter_prim_paths_expr=["/World/defaultGroundPlane/GroundPlane/CollisionPlane"],  # 只检测与地面的接触
        max_contact_count=3 * 10  # 每个立方体最多10个接触点
    )

    # 添加视图到场景并重置世界
    world.scene.add(box_view)
    await world.reset_async()

    # 施加外力（X轴方向）
    forces = np.array([[g, 0, 0], [g, 0, 0], [g, 0, 0]])
    box_view.apply_forces(forces)
    await update_stage_async()

    # 获取摩擦力和接触力数据（时间步长1/60秒）
    # 切向摩擦力数据
    friction_forces, friction_points, friction_pair_contacts_count, friction_pair_contacts_start_indices = box_view.get_friction_data(dt=1/60)
    # 法向接触力数据
    forces, points, normals, distances, pair_contacts_count, pair_contacts_start_indices = box_view.get_contact_force_data(dt=1/60)
    
    # 初始化聚合矩阵
    force_aggregate = np.zeros((box_view._contact_view.num_shapes, box_view._contact_view.num_filters, 3))
    friction_force_aggregate = np.zeros((box_view._contact_view.num_shapes, box_view._contact_view.num_filters, 3))

    # 处理每对物体间的接触力
    for i in range(pair_contacts_count.shape[0]):  # 遍历所有物体
        for j in range(pair_contacts_count.shape[1]):  # 遍历所有过滤器
            # 获取接触点索引和数量
            start_idx = pair_contacts_start_indices[i, j]
            friction_start_idx = friction_pair_contacts_start_indices[i, j]
            count = pair_contacts_count[i, j]
            friction_count = friction_pair_contacts_count[i, j]
            
            # 计算法向力聚合（点乘法向量）
            pair_forces = forces[start_idx : start_idx + count]
            pair_normals = normals[start_idx : start_idx + count]
            force_aggregate[i, j] = np.sum(pair_forces * pair_normals, axis=0)
            
            # 计算切向摩擦力聚合
            pair_forces = friction_forces[friction_start_idx : friction_start_idx + friction_count]
            friction_force_aggregate[i, j] = np.sum(pair_forces, axis=0)

    # 输出结果
    print("摩擦力矩阵: \n", friction_force_aggregate)
    print("接触力矩阵: \n", force_aggregate)
    print("API获取的接触力矩阵: \n", box_view.get_contact_force_matrix(dt=1/60))
    print("API获取的净接触力: \n", box_view.get_net_contact_forces(dt=1/60))

# 异步运行示例
asyncio.ensure_future(contact_force_example())
```

| 变量名                                      | 数据类型 / 典型 shape†       | 角色     | 说明                                                         |
| ---------------------------------------- | ---------------------- | ------ | ---------------------------------------------------------- |
| **friction_forces**                      | `np.ndarray (K_f, 3)`  | 原始摩擦数据 | 每个接触点的 **切向摩擦力向量** _(fx, fy, fz)_，已按 `dt` 换算成牛顿            |
| **friction_points**                      | `np.ndarray (K_f, 3)`  | 原始摩擦数据 | 与 `friction_forces` 对应的接触点世界坐标                             |
| **friction_pair_contacts_count**         | `np.ndarray (S, F)`    | 索引/计数  | 每个 _shape i_ ↔ _filter j_ 的摩擦接触点数量                         |
| **friction_pair_contacts_start_indices** | `np.ndarray (S, F)`    | 索引/计数  | 上述摩擦点在一维数组 `friction_forces / friction_points` 中的 **起始索引** |
| **forces**                               | `np.ndarray (K, 3)`    | 原始法向数据 | 每个接触点的 **总接触力向量**（含法向＋切向分量，但通常与法向同向）                       |
| **points**                               | `np.ndarray (K, 3)`    | 原始法向数据 | 与 `forces` 对应的接触点世界坐标                                      |
| **normals**                              | `np.ndarray (K, 3)`    | 原始法向数据 | 每点接触面的单位法向（shape→filter 方向）                                |
| **distances**                            | `np.ndarray (K,)`      | 原始法向数据 | 穿透深度 (‑) 或分离距离 (+)                                         |
| **pair_contacts_count**                  | `np.ndarray (S, F)`    | 索引/计数  | 每个 _shape i_ ↔ _filter j_ 的接触点数量                           |
| **pair_contacts_start_indices**          | `np.ndarray (S, F)`    | 索引/计数  | 上述接触点在 `forces / points / normals` 一维数组中的起始索引              |
| **force_aggregate**                      | `np.ndarray (S, F, 3)` | 聚合结果   | 对每对 (shape, filter) **沿法向分量求和** 后的总力                       |
| **friction_force_aggregate**             | `np.ndarray (S, F, 3)` | 聚合结果   | 对每对 (shape, filter) **切向摩擦力向量求和**                          |




### 设置网格物体的质量属性

以下代码片段演示如何为物理对象设置质量参数（也可选择使用密度替代质量直接设定）：


```python
import omni
from pxr import UsdPhysics
from omni.physx.scripts import utils

# 获取当前USD舞台(stage)对象
stage = omni.usd.get_context().get_stage()

# 执行命令创建一个立方体网格基元
# prim_type指定为"Cube"表示创建立方体
result, path = omni.kit.commands.execute(
    "CreateMeshPrimCommand", 
    prim_type="Cube"
)

# 获取刚创建的立方体prim对象
cube_prim = stage.GetPrimAtPath(path)

# 将立方体设置为刚体(RigidBody)
# 使用convexHull碰撞形状，False表示不删除原有几何形状
utils.setRigidBody(
    cube_prim, 
    "convexHull",  # 碰撞形状类型为凸包
    False          # 保留原始几何形状
)

# 应用质量API到立方体prim
mass_api = UsdPhysics.MassAPI.Apply(cube_prim)

# 设置立方体的质量属性为10(单位:千克)
mass_api.CreateMassAttr(10)

# 或者可以设置密度属性(单位:kg/m³)
# 注意：密度和质量只需设置一个，物理引擎会自动计算另一个
mass_api.CreateDensityAttr(1000)  # 1000 kg/m³ 相当于水的密度
```

- 使用 Kit 命令系统（Command）来“可撤销”地操作场景。
    
- `"CreateMeshPrimCommand"`：内置命令，用于创建网格（Mesh）基元。
    
- `prim_type="Cube"`：指定生成的网格是立方体；执行后会在 Stage 根目录下生成一个 `/Cube` Prim（路径由 Kit 自动分配，保存在 `path` 变量中）。
- `utils.setRigidBody(prim, shape, removeOrigGeom)`：工具函数封装了给 Prim 添加 USD Physics RigidBody API 和碰撞形状的逻辑。
    
- `"convexHull"`：碰撞几何体类型，凸包(Convex Hull) 更贴合原始网格外形且性能友好。
    
- `False`：表示**保留**原始渲染网格，不用碰撞网格替换它；若设为 `True`，则仅保留碰撞网格，去掉原始可视几何。



### 获取网格的大小  

下面的代码片段展示了如何获取网格的大小。

```python
import omni
from pxr import Usd, UsdGeom, Gf

# 获取当前USD舞台(stage)上下文
stage = omni.usd.get_context().get_stage()

# 创建一个圆锥体(Cone)网格基元
# 使用CreateMeshPrimCommand命令，指定prim_type为"Cone"
result, path = omni.kit.commands.execute(
    "CreateMeshPrimCommand", 
    prim_type="Cone"  # 创建圆锥体而非立方体
)

# 获取刚创建的圆锥体prim对象
prim = stage.GetPrimAtPath(path)

# 计算圆锥体的边界框(Bounding Box)
# 1. 创建边界框缓存，使用默认时间码和包含默认用途(purpose)
bbox_cache = UsdGeom.BBoxCache(
    Usd.TimeCode.Default(),  # 使用默认时间码
    includedPurposes=[UsdGeom.Tokens.default_]  # 包含默认用途的几何体
)

# 2. 清除缓存以确保计算最新结果
bbox_cache.Clear()

# 3. 计算圆锥体的世界空间边界框
prim_bbox = bbox_cache.ComputeWorldBound(prim)

# 4. 计算对齐的轴对齐边界框范围
prim_range = prim_bbox.ComputeAlignedRange()

# 5. 获取边界框尺寸(x,y,z长度)
prim_size = prim_range.GetSize()

# 打印圆锥体的尺寸信息
# 注意：这是圆锥体边界框的尺寸，不是底面半径和高度
print("圆锥体边界框尺寸:", prim_size)  # 输出格式: (宽度, 高度, 深度)
```

**`bbox_cache = UsdGeom.BBoxCache(Usd.TimeCode.Default(), includedPurposes=[UsdGeom.Tokens.default_])`**  
构造一个 `BBoxCache` 实例，用于快速计算 Prim 的包围盒；

- `Usd.TimeCode.Default()` 表示取默认时间（通常是动画的第 0 帧）。
    
- `includedPurposes=[UsdGeom.Tokens.default_]` 限定只计算“默认用途”的几何。






### 为整个场景批量添加语义数据

语义标签（Semantic Labels/Tags）是为物体或数据添加的**结构化描述信息**，用于赋予计算机可理解的**语义含义**。在仿真、机器人、计算机视觉等领域，它本质上是将原始数据（如3D模型、图像像素）与人类知识（如物体类别、功能属性）关联起来的桥梁。
1. **算法训练与推理**
    
    - 在机器学习中，语义标签是监督学习的真值（Ground Truth），比如：
        
        - 计算机视觉模型通过像素级语义标签（如“道路”“行人”）学习图像分割。
            
        - 机器人通过物体语义标签（如“可抓取”“危险”）决策行为。
          



以下代码演示如何通过遍历场景层级（Stage），以编程方式为所有对象自动添加语义标签：

```python
import omni.usd
from isaacsim.core.utils.semantics import add_update_semantics

def remove_prefix(name, prefix):
    """移除名称中的指定前缀
    
    Args:
        name (str): 原始名称
        prefix (str): 要移除的前缀
        
    Returns:
        str: 移除前缀后的名称
    """
    if name.startswith(prefix):
        return name[len(prefix):]
    return name

def remove_numerical_suffix(name):
    """移除名称中的数字后缀
    
    Args:
        name (str): 原始名称
        
    Returns:
        str: 移除数字后缀后的名称
        例如: "Chair_123" -> "Chair"
    """
    suffix = name.split("_")[-1]
    if suffix.isnumeric():
        return name[:-len(suffix)-1]  # 移除后缀和前面的下划线
    return name

def remove_underscores(name):
    """移除名称中的所有下划线
    
    Args:
        name (str): 原始名称
        
    Returns:
        str: 移除下划线后的名称
    """
    return name.replace("_", "")

# 获取当前USD舞台
stage = omni.usd.get_context().get_stage()

# 遍历舞台中的所有prim
for prim in stage.Traverse():
    # 只处理网格类型的prim
    if prim.GetTypeName() == "Mesh":
        # 获取prim路径的最后一部分作为原始标签
        label = str(prim.GetPrimPath()).split("/")[-1]
        
        # 清理标签名称:
        # 1. 移除"SM_"前缀(常见于导入的资产)
        label = remove_prefix(label, "SM_")
        # 2. 移除数字后缀(如"_123")
        label = remove_numerical_suffix(label)
        # 3. 移除所有下划线
        label = remove_underscores(label)
        
        # 添加/更新语义标签
        # semantic_label: 清理后的名称作为语义标签
        # type_label: 固定为"class"表示分类标签
        add_update_semantics(prim, semantic_label=label, type_label="class")
```


### 物理操作指南（Physics How-Tos）

---

***创建一个物理场景（Create A Physics Scene）

```python
import omni  # Omniverse USD 上下文接口
from pxr import Gf, Sdf, UsdPhysics  # 引入基础向量、SDF 路径和物理场景 API

# 获取当前 Stage
stage = omni.usd.get_context().get_stage()

# 在场景中定义并添加一个物理场景 Prim
scene = UsdPhysics.Scene.Define(stage, Sdf.Path("/World/physicsScene"))
# 设置重力方向向量（单位为世界坐标）
scene.CreateGravityDirectionAttr().Set(Gf.Vec3f(0.0, 0.0, -1.0))
# 设置重力大小（cm/s^2）
scene.CreateGravityMagnitudeAttr().Set(981.0)
```

下面代码段展示了如何设置特定的物理选项，本例中启用了 CPU 物理并使用 TGS 求解器：


```python
from pxr import PhysxSchema  # 引入 PhysX 场景扩展 API

# 将 PhysxSceneAPI 应用于刚才创建的物理场景
PhysxSchema.PhysxSceneAPI.Apply(stage.GetPrimAtPath("/World/physicsScene"))
# 获取对应的 API 对象
physxSceneAPI = PhysxSchema.PhysxSceneAPI.Get(stage, "/World/physicsScene")
# 启用连续碰撞检测（CCD）
physxSceneAPI.CreateEnableCCDAttr(True)
# 启用稳定化处理
physxSceneAPI.CreateEnableStabilizationAttr(True)
# 禁用 GPU 物理加速（使用 CPU）
physxSceneAPI.CreateEnableGPUDynamicsAttr(False)
# 设置广域检测类型为多箱体分层（MBP）
physxSceneAPI.CreateBroadphaseTypeAttr("MBP")
# 设置求解器类型为 TGS
physxSceneAPI.CreateSolverTypeAttr("TGS")
```

***向场景添加地面（Adding a Ground Plane）

```python
import omni  # Omniverse USD 上下文接口
from pxr import PhysicsSchemaTools, Gf  # 引入地面添加工具和向量类

# 获取当前 Stage
stage = omni.usd.get_context().get_stage()
# 在 /World/groundPlane 路径下创建一个 Z 向上、边长 100 cm，Z 坐标为 -100 的平面
PhysicsSchemaTools.addGroundPlane(
    stage,                    # Stage 实例
    "/World/groundPlane",   # Prim 路径
    "Z",                     # 指定 Z 轴为向上方向
    100,                      # 平面大小（cm）
    Gf.Vec3f(0, 0, -100),     # 平面位置
    Gf.Vec3f(1.0)             # 法线长度（1.0）
)
```

---

***为网格启用物理和碰撞（Enable Physics And Collision For a Mesh）

_该脚本假设场景中已有一个物理场景。_

```python
import omni  # Omniverse USD 上下文接口
from omni.physx.scripts import utils  # 引入 PhysX 实用函数

# 在 Stage 中创建一个立方体网格
stage = omni.usd.get_context().get_stage()
result, path = omni.kit.commands.execute(
    "CreateMeshPrimCommand",  # 创建网格 Prim 的命令
    prim_type="Cube"          # 指定类型为 Cube
)
# 获取新创建的 Prim
cube_prim = stage.GetPrimAtPath(path)
# 启用刚体和碰撞，使用凸包近似；若需要更精确可改用 "convexDecomposition"
utils.setRigidBody(cube_prim, "convexHull", False)
```

如需**更紧凑的碰撞近似**，请将最后一行改为：

```python
utils.setRigidBody(cube_prim, "convexDecomposition", False)
```

要验证碰撞网格是否启用，可点击界面右上角的“眼睛”图标 → “按类型显示” → “物理网格”，该模式下对象会以粉色轮廓显示其碰撞网格。

---

***遍历场景并为子节点分配碰撞（Traverse a Stage and Assign Collision Meshes to Children）

```python
import omni  # Omniverse USD 上下文接口
from pxr import Usd, UsdGeom, Gf  # 引入基类、几何定义和向量
from omni.physx.scripts import utils  # 引入 PhysX 实用函数

stage = omni.usd.get_context().get_stage()

# 辅助函数：在指定路径添加一个立方体
def add_cube(stage, path, size: float = 10, offset: Gf.Vec3d = Gf.Vec3d(0, 0, 0)):
    cubeGeom = UsdGeom.Cube.Define(stage, path)  # 定义立方体 Prim
    cubeGeom.CreateSizeAttr(size)                # 设置立方体边长
    cubeGeom.AddTranslateOp().Set(offset)        # 设置位置偏移

# 以下示例用于演示：
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Torus")
# 在 /World/Cube_0 路径下创建一个偏移 (100,100,0) 的立方体
add_cube(stage, "/World/Cube_0", offset=Gf.Vec3d(100, 100, 0))
# 在 /World/Nested/Cube 路径下创建一个嵌套偏移 (100,0,100) 的立方体
add_cube(stage, "/World/Nested/Cube", offset=Gf.Vec3d(100, 0, 100))

# 从根节点遍历所有 Prim
curr_prim = stage.GetPrimAtPath("/")
for prim in Usd.PrimRange(curr_prim):
    # 如果是几何形状（柱体、胶囊、圆锥、球体、立方体）
    if (
        prim.IsA(UsdGeom.Cylinder)
        or prim.IsA(UsdGeom.Capsule)
        or prim.IsA(UsdGeom.Cone)
        or prim.IsA(UsdGeom.Sphere)
        or prim.IsA(UsdGeom.Cube)
    ):
        # 为常规模型使用凸包（convexHull）碰撞近似
        utils.setCollider(prim, approximationShape="convexHull")
    elif prim.IsA(UsdGeom.Mesh):
        # 对网格 Prim 使用三角网格，或可选其他方式："convexDecomposition"、"boundingSphere"、"boundingCube"
        utils.setCollider(prim, approximationShape="None")
```

---

***执行重叠测试（Do Overlap Test）

下面代码检测并报告对象是否与指定立方体/球体区域重叠。假设场景已有物理场景，且所有对象均已启用碰撞网格，且已点击播放按钮。

参数说明：`extent`、`origin` 和 `rotation`（或 `origin` 和 `radius`）定义了要检测的立方体/球体区域。PhysX 查询将返回重叠对象的数量。

```python
import carb  # 核心日志与基础类型
import omni
import omni.physx
from omni.physx import get_physx_scene_query_interface  # 引入场景查询接口
from pxr import UsdGeom, Gf, Vt  # 几何类型、向量类型和数组类型

# 碰撞命中回调：将命中对象显示为红色
def report_hit(hit):
    hitColor = Vt.Vec3fArray([Gf.Vec3f(180.0/255.0, 16.0/255.0, 0.0)])  # 红色
    usdGeom = UsdGeom.Mesh.Get(omni.usd.get_context().get_stage(), hit.rigid_body)
    usdGeom.GetDisplayColorAttr().Set(hitColor)
    return True

# 执行重叠测试
def check_overlap():
    extent = carb.Float3(20.0, 20.0, 20.0)          # 立方体半尺寸
    origin = carb.Float3(0.0, 0.0, 0.0)              # 中心点
    rotation = carb.Float4(0.0, 0.0, 1.0, 0.0)       # 旋转四元数
    # 立方体区域重叠测试，返回命中数量
    numHits = get_physx_scene_query_interface().overlap_box(extent, origin, rotation, report_hit, False)
    # 球体区域重叠测试：
    # numHits = get_physx_scene_query_interface().overlap_sphere(radius, origin, report_hit, False)
    return numHits > 0  # 有命中返回 True
```

---

***执行射线测试（Do Raycast Test）

下面代码检测射线与场景对象的最近交点。假设场景已有物理场景，且碰撞网格已启用，并已点击播放按钮。

参数说明：`origin`、`rayDir` 和 `distance` 定义了射线起点、方向和长度。查询结果包含命中对象引用及距离信息。

```python
import carb
import omni
import omni.physx
from omni.physx import get_physx_scene_query_interface  # 场景查询接口
from pxr import UsdGeom, Vt, Gf

# 射线测试函数
def check_raycast():
    origin = carb.Float3(0.0, 0.0, 0.0)        # 射线起点
    rayDir = carb.Float3(1.0, 0.0, 0.0)        # 射线方向
    distance = 100.0                          # 射线长度（cm）
    # 执行最接近命中测试
    hit = get_physx_scene_query_interface().raycast_closest(origin, rayDir, distance)
    if hit["hit"]:
        # 命中时将对象变为黄色，并获取距离
        usdGeom = UsdGeom.Mesh.Get(omni.usd.get_context().get_stage(), hit["rigidBody"])
        hitColor = Vt.Vec3fArray([Gf.Vec3f(1.0, 1.0, 0.0)])  # 黄
        usdGeom.GetDisplayColorAttr().Set(hitColor)
        distance = hit["distance"]
        return usdGeom.GetPath().pathString, distance
    return None, 10000.0

print(check_raycast())  # 示例打印结果
```

---

### USD 操作指南（USD How-Tos）

---

***创建、修改和分配材质（Creating, Modifying, Assigning Materials）

```python
import omni
from pxr import UsdShade, Sdf, Gf  # 引入材质 API、SDF 路径和向量

mtl_created_list = []  # 存放新建材质的路径列表
# 从库中创建并绑定 MDL 材质，示例使用 OmniGlass.mdl
omni.kit.commands.execute(
    "CreateAndBindMdlMaterialFromLibrary",
    mdl_name="OmniGlass.mdl",  # MDL 文件名
    mtl_name="OmniGlass",      # 材质名称
    mtl_created_list=mtl_created_list,
)
# 获取新创建材质的 Prim
stage = omni.usd.get_context().get_stage()
mtl_prim = stage.GetPrimAtPath(mtl_created_list[0])
# 设置材质参数（可在 .mdl 文件或 Stage 详情面板查看参数名称）
omni.usd.create_material_input(mtl_prim, "glass_color", Gf.Vec3f(0, 1, 0), Sdf.ValueTypeNames.Color3f)
omni.usd.create_material_input(mtl_prim, "glass_ior", 1.0, Sdf.ValueTypeNames.Float)
# 创建一个立方体并绑定材质
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
cube_prim = stage.GetPrimAtPath(path)
cube_mat_shade = UsdShade.Material(mtl_prim)
UsdShade.MaterialBindingAPI(cube_prim).Bind(cube_mat_shade, UsdShade.Tokens.strongerThanDescendants)
```

---

***给材质分配纹理（Assigning a Texture to a Material）

```python
import omni
import carb  # 配置读取
from pxr import UsdShade, Sdf

# 获取默认 Nucleus 服务器路径（可根据配置调整）
default_server = carb.settings.get_settings().get("/persistent/isaac/asset_root/default")
mtl_created_list = []
# 创建并绑定 OmniPBR.mdl 材质
omni.kit.commands.execute(
    "CreateAndBindMdlMaterialFromLibrary",
    mdl_name="OmniPBR.mdl",
    mtl_name="OmniPBR",
    mtl_created_list=mtl_created_list,
)
stage = omni.usd.get_context().get_stage()
mtl_prim = stage.GetPrimAtPath(mtl_created_list[0])
# 设置纹理贴图路径输入
omni.usd.create_material_input(
    mtl_prim,
    "diffuse_texture",  # 参数名
    default_server + "/Isaac/Samples/DR/Materials/Textures/marble_tile.png",
    Sdf.ValueTypeNames.Asset
)
# 创建立方体并绑定材质
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
cube_prim = stage.GetPrimAtPath(path)
cube_mat_shade = UsdShade.Material(mtl_prim)
UsdShade.MaterialBindingAPI(cube_prim).Bind(cube_mat_shade, UsdShade.Tokens.strongerThanDescendants)
```

---

***为 Prim 添加变换矩阵（Adding a Transform Matrix to a Prim）

```python
import omni
from pxr import Gf, UsdGeom

stage = omni.usd.get_context().get_stage()
# 创建一个立方体
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
# 获取 Prim 并设置变换矩阵
cube_prim = stage.GetPrimAtPath(path)
xform = UsdGeom.Xformable(cube_prim)
transform = xform.AddTransformOp()
mat = Gf.Matrix4d()                  # 创建 4x4 变换矩阵
mat.SetTranslateOnly(Gf.Vec3d(.10, 1, 1.5))  # 设置平移
mat.SetRotateOnly(Gf.Rotation(Gf.Vec3d(0,1,0), 290))  # 设置绕 Y 轴旋转
transform.Set(mat)  # 应用到 Prim
```

---

***对齐两个 USD Prim（Align Two USD Prims）

```python
import omni
from pxr import UsdGeom, Gf

stage = omni.usd.get_context().get_stage()
# 创建第一个立方体
result, path_a = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
prim_a = stage.GetPrimAtPath(path_a)
# 设置第一个立方体的变换
xform = UsdGeom.Xformable(prim_a)
transform = xform.AddTransformOp()
mat = Gf.Matrix4d()
mat.SetTranslateOnly(Gf.Vec3d(.10, 1, 1.5))
mat.SetRotateOnly(Gf.Rotation(Gf.Vec3d(0, 1, 0), 290))
transform.Set(mat)
# 创建第二个立方体
result, path_b = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
prim_b = stage.GetPrimAtPath(path_b)
# 获取第一个立方体的世界变换矩阵
pose = omni.usd.utils.get_world_transform_matrix(prim_a)
# 清除第二个立方体的原有变换
xform = UsdGeom.Xformable(prim_b)
xform.ClearXformOpOrder()
# 将第二个立方体对齐到第一个立方体的姿态
xform_op = xform.AddXformOp(UsdGeom.XformOp.TypeTransform, UsdGeom.XformOp.PrecisionDouble, "")
xform_op.Set(pose)
```

---

***获取选定 Prim 在当前时间戳下的世界变换（Get World Transform At Current Timestamp For Selected Prims）

```python
import omni
from pxr import UsdGeom, Gf

usd_context = omni.usd.get_context()
stage = usd_context.get_stage()

#### 以下部分用于测试，可在已有选定 Prim 时移除
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
cube_prim = stage.GetPrimAtPath(path)
xform = UsdGeom.Xformable(cube_prim)
transform = xform.AddTransformOp()
mat = Gf.Matrix4d()
mat.SetTranslateOnly(Gf.Vec3d(.10, 1, 1.5))
mat.SetRotateOnly(Gf.Rotation(Gf.Vec3d(0, 1, 0), 290))
transform.Set(mat)
omni.usd.get_context().get_selection().set_prim_path_selected(path, True, True, True, False)
####

# 获取所有选中的 Prim 路径列表
selected_prims = usd_context.get_selection().get_selected_prim_paths()
# 获取当前时间码（timecode）
timeline = omni.timeline.get_timeline_interface()
timecode = timeline.get_current_time() * timeline.get_time_codes_per_seconds()
# 遍历并打印各 Prim 在该时间点的世界变换
for s in selected_prims:
    curr_prim = stage.GetPrimAtPath(s)
    print("Selected", s)
    pose = omni.usd.utils.get_world_transform_matrix(curr_prim, timecode)
    print("Matrix Form:", pose)
    print("Translation:", pose.ExtractTranslation())
    q = pose.ExtractRotation().GetQuaternion()
    print(
        "Rotation:", q.GetReal(), ",",
        q.GetImaginary()[0], ",",
        q.GetImaginary()[1], ",",
        q.GetImaginary()[2]
    )
```

---

***将当前 Stage 保存为 USD 文件（Save Current Stage to USD）

```python
import omni
import carb  # 日志与配置

# 示例：在场景中创建一个立方体 Prim
result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
# 保存当前 Stage 到指定路径，可用于脚本生成后调试加载
omni.usd.get_context().save_as_stage(
    "/path/to/asset/saved.usd",  # 输出文件路径
    None                          # 可选层配置
)
```



---
# 工具代码片段（Util Snippets）

---

***简单异步任务（Simple Async Task）

```python
import asyncio  # Python 异步库
import omni     # Omniverse Kit 主模块

# 异步任务：等待下一帧后暂停模拟
async def pause_sim(task):
    # 等待传入的任务完成
    done, pending = await asyncio.wait({task})
    if task in done:
        print("等待到下一帧，暂停模拟")
        # 调用时间线接口暂停播放
        omni.timeline.get_timeline_interface().pause()

# 开始播放模拟
omni.timeline.get_timeline_interface().play()
# 获取一个将在下一帧更新完成时完成的异步任务
task = asyncio.ensure_future(omni.kit.app.get_app().next_update_async())
# 启动 pause_sim 异步函数
asyncio.ensure_future(pause_sim(task))
```

---

***获取相机参数（Get Camera Parameters）

下面脚本展示如何从当前视口获取相机参数。

```python
import omni
from omni.syntheticdata import helpers  # 相机辅助工具
import math  # 数学库

# 获取当前 USD Stage
stage = omni.usd.get_context().get_stage()
# 获取活动视口 API
viewport_api = omni.kit.viewport.utility.get_active_viewport()
# 设置视口渲染分辨率，下次帧更新时生效
viewport_api.set_texture_resolution((512, 512))
# 获取实际分辨率
(width, height) = viewport_api.get_texture_resolution()
aspect_ratio = width / height  # 长宽比
# 获取绑定到视口的相机 Prim
camera = stage.GetPrimAtPath(viewport_api.get_active_camera())
# 读取相机属性
focal_length = camera.GetAttribute("focalLength").Get()         # 焦距
horiz_aperture = camera.GetAttribute("horizontalAperture").Get()  # 水平光圈
vert_aperture = camera.GetAttribute("verticalAperture").Get()    # 垂直光圈
# 裁剪平面
near, far = camera.GetAttribute("clippingRange").Get()
# 视场角（弧度）
fov = 2 * math.atan(horiz_aperture / (2 * focal_length))
# 计算投影矩阵
proj_mat = helpers.get_projection_matrix(fov, aspect_ratio, near, far)

# 计算焦平面中心坐标（像素空间）
focal_x = height * focal_length / vert_aperture
focal_y = width * focal_length / horiz_aperture
center_x = height * 0.5
center_y = width * 0.5
```

---

***渲染（Rendering）

对于频繁更新大量几何体，有三种主要 API 可选择：

- **UsdGeom.Points**：适用于渲染大量点（与渲染器交互）
    
- **UsdGeom.PointInstancer**：适用于具有物理交互且需要实例化的几何体
    
- **DebugDraw**：仅用于可视化，不参与渲染或物理，性能最高
    

下面分别展示示例。

***UsdGeom.Points

```python
import random
import omni.usd
from pxr import UsdGeom

class Example():
    def create(self):
        N = 500  # 点数量
        # 随机生成点位置列表
        self.point_list = [(
            random.uniform(-2.0, 2.0),
            random.uniform(-0.1, 0.1),
            random.uniform(-1.0, 1.0)
        ) for _ in range(N)]
        # 点大小
        self.sizes = [0.05 for _ in range(N)]

        points_path = "/World/Points"
        stage = omni.usd.get_context().get_stage()
        # 定义 Points API
        self.points = UsdGeom.Points.Define(stage, points_path)
        # 设置点坐标和大小
        self.points.CreatePointsAttr().Set(self.point_list)
        self.points.CreateWidthsAttr().Set(self.sizes)
        # 设置统一颜色
        self.points.CreateDisplayColorPrimvar("constant").Set([(1, 0, 1)])

    def update(self):
        # 更新点位置
        for i in range(len(self.point_list)):
            self.point_list[i] = (
                random.uniform(-2.0, 2.0),
                random.uniform(-0.1, 0.1),
                random.uniform(-1.0, 1.0)
            )
        # 应用更新
        self.points.GetPointsAttr().Set(self.point_list)

import asyncio
import omni
example = Example()
example.create()

async def update_points():
    # 循环 10 次，每次等待 10 帧
    for _ in range(10):
        for _ in range(10):
            await omni.kit.app.get_app().next_update_async()
        example.update()

asyncio.ensure_future(update_points())
```

---

***UsdGeom.PointInstancer

```python
import random
import omni.usd
from pxr import UsdGeom, Gf

class Example():
    def create(self):
        N = 500
        scale = 0.05  # 缩放因子
        # 随机位置、颜色、缩放列表
        self.point_list = [(
            random.uniform(-2.0, 2.0),
            random.uniform(-0.1, 0.1),
            random.uniform(-1.0, 1.0)
        ) for _ in range(N)]
        self.colors = [(1, 1, 1, 1) for _ in range(N)]
        self.sizes = [(1.0, 1.0, 1.0) for _ in range(N)]

        stage = omni.usd.get_context().get_stage()
        # 定义要实例化的原型立方体
        cube_path = "/World/Cube"
        cube = UsdGeom.Cube(stage.DefinePrim(cube_path, "Cube"))
        cube.AddScaleOp().Set(Gf.Vec3d(1, 1, 1) * scale)
        cube.CreateDisplayColorPrimvar().Set([(0, 1, 1)])

        # 定义 PointInstancer
        instance_path = "/World/PointInstancer"
        self.point_instancer = UsdGeom.PointInstancer(
            stage.DefinePrim(instance_path, "PointInstancer")
        )
        # 设置实例位置
        self.positions_attr = self.point_instancer.CreatePositionsAttr()
        self.positions_attr.Set(self.point_list)
        # 设置缩放
        self.scale_attr = self.point_instancer.CreateScalesAttr()
        self.scale_attr.Set(self.sizes)
        # 指向原型几何
        self.point_instancer.CreatePrototypesRel().SetTargets([cube.GetPath()])
        # 所有实例都使用原型索引 0
        self.proto_indices_attr = self.point_instancer.CreateProtoIndicesAttr()
        self.proto_indices_attr.Set([0] * len(self.point_list))

    def update(self):
        # 更新位置列表
        for i in range(len(self.point_list)):
            self.point_list[i] = (
                random.uniform(-2.0, 2.0),
                random.uniform(-0.1, 0.1),
                random.uniform(-1.0, 1.0)
            )
        # 应用更新
        self.positions_attr.Set(self.point_list)

import asyncio
import omni
example = Example()
example.create()

async def update_points():
    for _ in range(10):
        for _ in range(10):
            await omni.kit.app.get_app().next_update_async()
        example.update()

asyncio.ensure_future(update_points())
```

---

***DebugDraw（调试绘制）

```python
import random
from isaacsim.util.debug_draw import _debug_draw  # 调试绘制接口

class Example():
    def create(self):
        # 获取调试绘制接口
        self.draw = _debug_draw.acquire_debug_draw_interface()
        N = 500
        # 随机生成点位置、颜色、大小列表
        self.point_list = [(
            random.uniform(-2.0, 2.0),
            random.uniform(-0.1, 0.1),
            random.uniform(-1.0, 1.0)
        ) for _ in range(N)]
        self.color_list = [(
            random.uniform(0, 1),
            random.uniform(0, 1),
            random.uniform(0, 1),
            1
        ) for _ in range(N)]
        self.size_list = [10.0 for _ in range(N)]

    def update(self):
        # 更新点位置列表
        for i in range(len(self.point_list)):
            self.point_list[i] = (
                random.uniform(-2.0, 2.0),
                random.uniform(-0.1, 0.1),
                random.uniform(-1.0, 1.0)
            )
        # 清空并绘制新点
        self.draw.clear_points()
        self.draw.draw_points(self.point_list, self.color_list, self.size_list)

import asyncio
import omni
example = Example()
example.create()

async def update_points():
    for _ in range(10):
        for _ in range(10):
            await omni.kit.app.get_app().next_update_async()
        example.update()

asyncio.ensure_future(update_points())
```

---

***渲染帧延迟（Rendering Frame Delay）

默认渲染管线允许最多 3 帧"在途"，以提高 FPS，但可能导致渲染结果滞后。

若需零延迟（渲染数据与最新模拟状态同步），请使用以下体验文件：

```python
from omni.isaac.kit import SimulationApp

# 使用 zero_delay 体验文件启动无头应用
SimulationApp({"headless": True}, experience="apps/omni.isaac.sim.zero_delay.python.kit")
```


---
# 机器人仿真代码片段（Robot Simulation Snippets）

> **注意**：以下脚本只能在默认新场景（Stage）下运行一次。您可以通过菜单 **File → New** 新建场景，然后在 **Window → Script Editor** 中运行这些脚本。

---

#### 创建关节机构（Articulations）与批量视图（ArticulationView）

下面示例向场景中添加两个 Franka 机械臂，并创建一个 `Articulation` 视图对象，以便批量控制它们的属性。

```python
import asyncio
import numpy as np
from isaacsim.core.api.world import World
from isaacsim.core.prims import Articulation
from isaacsim.core.utils.nucleus import get_assets_root_path
from isaacsim.core.utils.stage import add_reference_to_stage

async def example():
    # 如果已有 World 实例，则先清除
    if World.instance():
        World.instance().clear_instance()
    # 创建新的仿真世界
    world = World()
    await world.initialize_simulation_context_async()
    # 添加默认地面
    world.scene.add_default_ground_plane()

    # 添加两个 Franka 机械臂引用
    asset_path = get_assets_root_path() + "/Isaac/Robots/Franka/franka_alt_fingers.usd"
    add_reference_to_stage(usd_path=asset_path, prim_path="/World/Franka_1")
    add_reference_to_stage(usd_path=asset_path, prim_path="/World/Franka_2")

    # 使用 Articulation 视图批量处理机械臂
    frankas_view = Articulation(prim_paths_expr="/World/Franka_[1-2]", name="frankas_view")
    world.scene.add(frankas_view)
    await world.reset_async()

    # 设置机械臂基座的世界坐标位置
    new_positions = np.array([[-1.0, 1.0, 0], [1.0, 1.0, 0]])
    frankas_view.set_world_poses(positions=new_positions)
    # 设置每个机械臂的关节角度（9 个 DOF）
    joint_positions = np.array([
        [1.5]*9,
        [1.5]*9
    ])
    frankas_view.set_joint_positions(joint_positions)

# 启动异步示例
asyncio.ensure_future(example())
```

> 更多 `Articulation` API 操作，请参阅官方文档。

---

#### 关节控制（Joints Control）

以下示例假设场景中已有一个路径为 `/Franka` 的 Franka 机械臂。您可以通过菜单 **Create → Isaac → Robots → From Library → Manipulators → Franka** 添加。

> **提示**：至少要让仿真跑一帧后，动态控制 API 才能正常工作。可通过：
> 
> - 点击界面顶部的 ▶️ 播放按钮；
>     
> - 或运行：
>     
> 
> ```python
> import omni
> omni.timeline.get_timeline_interface().play()
> ```

以下代码片段示例了不同的控制模式：位置、速度、力矩等。请在 **Script Editor** 中按需运行。

---

##### 位置控制（Position Control）

```python
from omni.isaac.dynamic_control import _dynamic_control
import numpy as np

dc = _dynamic_control.acquire_dynamic_control_interface()
# 获取机械臂对象
articulation = dc.get_articulation("/Franka")
# 若目标状态有变，每帧都需唤醒机构
dc.wake_up_articulation(articulation)
# 随机生成 9 关节角目标
joint_angles = [np.random.rand(9) * 2 - 1]
# 设置位置目标
dc.set_articulation_dof_position_targets(articulation, joint_angles)
```

##### 单关节位置控制（Single DOF Position Control）

```python
from omni.isaac.dynamic_control import _dynamic_control
import numpy as np

dc = _dynamic_control.acquire_dynamic_control_interface()
articulation = dc.get_articulation("/Franka")
dc.wake_up_articulation(articulation)
# 查找名为 panda_joint2 的单个关节
dof_ptr = dc.find_articulation_dof(articulation, "panda_joint2")
# 设置该关节位置目标
dc.set_dof_position_target(dof_ptr, -1.5)
```

##### 速度控制（Velocity Control）

```python
# 首先将所有 Revolute/Prismatic Joint 的刚度设为 0
from pxr import UsdPhysics
import omni.usd
stage = omni.usd.get_context().get_stage()
for prim in stage.TraverseAll():
    typ = prim.GetTypeName()
    if typ in ["PhysicsRevoluteJoint","PhysicsPrismaticJoint"]:
        drive = (UsdPhysics.DriveAPI.Get(prim, "angular")
                 if typ=="PhysicsRevoluteJoint" else
                 UsdPhysics.DriveAPI.Get(prim, "linear"))
        if drive:
            drive.GetStiffnessAttr().Set(0)

# 然后设置关节速度目标
from omni.isaac.dynamic_control import _dynamic_control
import numpy as np
dc = _dynamic_control.acquire_dynamic_control_interface()
articulation = dc.get_articulation("/Franka")
dc.wake_up_articulation(articulation)
# 随机生成 9 关节速度目标
joint_vels = [-np.random.rand(9) * 10]
dc.set_articulation_dof_velocity_targets(articulation, joint_vels)
```

##### 单关节速度控制（Single DOF Velocity Control）

```python
# 设置 panda_joint2 的驱动刚度为 0
from pxr import UsdPhysics
import omni.usd
stage = omni.usd.get_context().get_stage()
p2_drive = UsdPhysics.DriveAPI.Get(
    stage.GetPrimAtPath("/Franka/panda_link1/panda_joint2"), "angular")
p2_drive.GetStiffnessAttr().Set(0)

# 设置该关节速度目标
from omni.isaac.dynamic_control import _dynamic_control
import numpy as np
dc = _dynamic_control.acquire_dynamic_control_interface()
articulation = dc.get_articulation("/Franka")
dc.wake_up_articulation(articulation)
dof_ptr = dc.find_articulation_dof(articulation, "panda_joint2")
dc.set_dof_velocity_target(dof_ptr, 0.2)
```

##### 力矩控制（Torque Control）

```python
from omni.isaac.dynamic_control import _dynamic_control
import numpy as np
dc = _dynamic_control.acquire_dynamic_control_interface()
articulation = dc.get_articulation("/Franka")
dc.wake_up_articulation(articulation)
# 随机生成 9 关节力矩目标
joint_efforts = [-np.random.rand(9) * 1000]
dc.set_articulation_dof_efforts(articulation, joint_efforts)
```

---

#### 对象类型检查（Check Object Type）

```python
from omni.isaac.dynamic_control import _dynamic_control

dc = _dynamic_control.acquire_dynamic_control_interface()
# 查看给定 Prim 的对象类型
obj_type = dc.peek_object_type("/Franka")
print(obj_type)  # 应输出 ObjectType.OBJECT_ARTICULATION
```

---

#### 查询关节机构信息（Query Articulation）

```python
from omni.isaac.dynamic_control import _dynamic_control

dc = _dynamic_control.acquire_dynamic_control_interface()
# 获取 Franka 机构句柄（可自动更新）
art = dc.get_articulation("/Franka")
# 查询结构信息
num_joints = dc.get_articulation_joint_count(art)
num_dofs   = dc.get_articulation_dof_count(art)
num_bodies = dc.get_articulation_body_count(art)
# 查找特定 DOF
dof_ptr    = dc.find_articulation_dof(art, "panda_joint2")
```

---

#### 读取关节状态（Read Joint State）

```python
from omni.isaac.dynamic_control import _dynamic_control

dc = _dynamic_control.acquire_dynamic_control_interface()
# 获取机构句柄
art = dc.get_articulation("/Franka")
# 获取所有 DOF 的完整状态（位置、速度、力矩）
dof_states = dc.get_articulation_dof_states(art, _dynamic_control.STATE_ALL)
print(dof_states)

# 获取单个 DOF 状态
ptr = dc.find_articulation_dof(art, "panda_joint2")
dof_state = dc.get_dof_state(ptr, _dynamic_control.STATE_ALL)
# 打印该 DOF 的位置
print(dof_state.pos)
```