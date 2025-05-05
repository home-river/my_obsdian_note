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
