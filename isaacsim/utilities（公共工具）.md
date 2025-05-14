
# 模板

我们提供了多种模板和模板生成工具，帮助您快速启动项目。

- **自定义互动示例（Custom Interactive Examples）**  
    您可以直接基于已有示例进行修改，以满足自己的需求。
    
- **扩展模板生成器（Extension Template Generator）**  
    使用此工具可创建全新的扩展项目。生成器所提供的模板已集成 Isaac Sim 库，并针对机器人应用进行了优化。
    
- **自定义扩展（C++）**  
    若需更通用的 C++ 扩展模板，可使用该选项。
    
- **高级扩展模板生成器（Advanced Extension Template Generator for VS Code）**  
    支持任意组合的 C++、Python、OmniGraph、GUI 元素等，满足更复杂的扩展开发需求。
    

> 以上均适用于基于扩展（Extension-based）的项目。  
> 若要创建独立运行的项目，请浏览 `PATH_TO_ISAAC_SIM/standalone_examples` 文件夹下的示例，并以此作为起点。



---
## 自定义交互示例

您可以在 NVIDIA Isaac Sim 的示例浏览器（Examples Browser）中创建自定义示例，使您的示例与其他示例一并展示并可直接访问。

### BaseSampleUITemplate 与 BaseSample 类

`BaseSampleUITemplate` 和 `BaseSample` 类提供了示例的基本结构，使其外观和行为与示例浏览器中的其他示例保持一致。它们会自动生成一个 **Load** 按钮和一个 **Reset** 按钮，将与仿真器异步交互的复杂性封装起来，让示例具备开箱即用的交互功能。

### 创建自定义示例的步骤

1. **复制示例文件到用户目录**  
    将已有示例文件复制到 `isaacsim/examples/interactive/user_examples` 下：
    
```bash
cd exts/isaacsim.examples.interactive/isaacsim/examples/interactive
cp hello_world/hello_world* user_examples/
```
    
2. **编辑 Extension 脚本**  
    打开并修改以下文件中的高亮行：
    
```python
    # 文件路径：.../interactive/user_examples/hello_world_extension.py

import os
import omni.ext
from isaacsim.examples.interactive.base_sample import BaseSampleUITemplate
from isaacsim.examples.interactive.user_examples import HelloWorld

class HelloWorldExtension(omni.ext.IExt):
    def on_startup(self, ext_id: str):
        # 示例名称，命名空间
        self.example_name = "Awesome Example"
        self.category     = "MyExamples"

        ui_kwargs = {
            "ext_id":    ext_id,
            "file_path": os.path.abspath(__file__),
            "title":     "My Awesome Example",
            "doc_link":  "https://docs.omniverse.nvidia.com/isaacsim/latest/core_api_tutorials/tutorial_core_hello_world.html",
            "overview":  "此示例演示如何通过异步脚本在 Isaac Sim 中完成有趣的操作。",
            "sample":    HelloWorld(),
        }

        ui_handle = BaseSampleUITemplate(**ui_kwargs)

        # 将示例注册到示例浏览器中
        get_browser_instance().register_example(
            name=             self.example_name,
            execute_entrypoint=ui_handle.build_window,
            ui_hook=            ui_handle.build_ui,
            category=           self.category,
        )

    return
```
    
3. **更新包初始化**  
    在 `isaacsim/examples/interactive/user_examples/__init__.py` 中添加：
    
```python
from isaacsim.examples.interactive.user_examples.hello_world import HelloWorld
from isaacsim.examples.interactive.user_examples.hello_world_extension import HelloWorldExtension
  
```
    

> **注意**：
> 
> - 每次修改代码后，按 **Ctrl+S** 保存，Isaac Sim 会自动热重载（hot-reload）。
>     
> - 若需添加更多按钮或复杂交互，可参考其他示例；在示例浏览器右上角点击文件夹图标，即可直接查看底层脚本。


---

## 扩展模板生成器

扩展模板生成器可在本地为您生成基于 UI 的扩展项目。可用的扩展模板涵盖多种 Isaac Sim 应用场景，结构化地帮助您学习如何构建满足需求的自定义 UI 工具。

### 快速入门

要使用扩展模板生成器创建并启用新扩展，请按以下步骤操作：

1. 从菜单栏依次选择 **Utilities > Generate Extension Templates**，打开扩展生成器。
    
2. 在弹出的窗口中选择所需的模板类型，展开相应表单，并填写以下字段：
    
    - **Extension Path**：`<Extension_Host_Dir>/my.extension.name`
        
    - **Extension Name**：`my.extension.name`
        
    - **Extension Description**：`My Extension Description`
        
3. 点击 **Generate Extension**，生成模板。
    
4. 打开扩展管理器：依次选择 **Window > Extensions**，在搜索框右侧点击汉堡菜单图标，选择 **Settings**，调出路径表。
    
    - 若您在第 2 步选择的 `<Extension_Host_Dir>` 已在“Extension Search Path”列表中，可跳过此步。
        
    - 若未在列表中，请滚动到末尾，点击“Extension Search Path” 最后一行“edit”列的“+”号，输入完整的 `<Extension_Host_Dir>` 路径。
        
5. 在扩展管理器中搜索新扩展：
    
    - 若您使用的是默认的 Extension Search Path，它将出现在 **NVIDIA** 选项卡下。
        
    - 若是新添加的搜索路径，则出现在 **Third Party** 选项卡下。
        
6. 启用该扩展，并确认其已出现在 Isaac Sim 顶部菜单栏中。
    

> **命令行方式启用扩展**  
> 在终端运行 Isaac Sim 时，可通过命令行参数启用：
> 

> 
```bash
./isaac-sim.sh --ext-folder {path_to_user_ext_folder} --enable {ext_directory_name}
```
> Windows 下使用 `python.bat` 代替 `isaac-sim.sh`。

7. 打开生成的 Python 模块目录，阅读其中的 `README.md`，熟悉模板代码结构。
    
[[扩展.webp|Open: 扩展.webp]]
![[扩展.webp]]
---

### 模板选项

- **Load Scenario 模板**  
    提供一个简易 UI，包含 **Load**、**Reset**、**Run** 三个按钮，帮助您快速上手，对 USD 舞台直接进行加载和重置操作，而无需深入了解底层仿真器细节。
    
- **Scripting 模板**  
    在 Load Scenario 模板的基础上，将 **Run** 按钮实现为可运行脚本，演示如何在 UI 扩展中编写更高级的脚本化行为。
    
- **Configuration Tooling 模板**  
    提供基础资产配置工具，例如在舞台上查找 Articulation，并动态生成 UI 界面，允许用户控制所选 Articulation 的各个关节。
    
- **UI Component Library 模板**  
    演示各类 `UIElementWrapper` 的使用方式，包括回调函数所需的参数和返回值类型，帮助您快速上手 UI 组件开发。
    

---

更多详细说明，请参阅《[Extension Template Generator Explained](https://docs.isaacsim.omniverse.nvidia.com/latest/utilities/extension_templates_tutorial.html#isaac-sim-app-tutorial-extension-templates)》。

---

## 扩展模板生成器详解
[Extension Template Generator Explained — Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/utilities/extension_templates_tutorial.html)
### 基本概念

扩展模板生成器（Extension Template Generator）提供的每个模板都具有通用的底层结构，并在其上加了一层简单的实现。在每个模板的根目录下，都有一个名为`./scripts`的文件夹，所有支持扩展的Python代码都存放在这里。`./scripts`文件夹中有三个常见的Python文件：

- **global_variables.py**  
    该脚本用于存储用户在创建扩展时通过扩展模板生成器指定的全局变量，比如标题和描述等。
    
- **extension.py**  
    这是一个包含标准样板代码的类，用于让用户扩展显示在工具栏上。这个类旨在满足大多数使用场景，通常无需修改。在`extension.py`中，会创建一些有用的标准回调函数，用户可以在`ui_builder.py`中完善这些函数。
    
- **ui_builder.py**  
    这是用户进入模板的主要入口。在这里，用户可以看到为其设置的有用回调函数，并可以创建与用户自定义回调函数关联的UI元素。该文件有最详细的文档说明，用户在进行重大修改前应仔细阅读。
    

通常，用户只需修改`./scripts/ui_builder.py`即可让扩展按需工作。在`ui_builder.py`中，用户会找到一组标准回调函数，这些函数将用户与仿真器连接起来：

- `on_menu_callback()`：扩展被打开时调用
    
- `on_timeline_event()`：时间轴停止、暂停或播放时调用
    
- `on_physics_step()`：每个物理步调用（仅在时间轴播放时发生）
    
- `on_stage_event()`：舞台打开或关闭时调用
    
- `cleanup()`：扩展关闭时清理物理订阅等资源
    
- `build_ui()`：用户自定义UI的函数
    

在提供的扩展模板中，大部分实现都在`build_ui()`函数里。模板利用了一组围绕`omni.ui`元素的包装类（UIElementWrappers），让用户可以轻松创建和管理各种UI元素。例如，用户可以创建一个`FloatField` UI元素，每当用户在UI中修改该字段时，都会调用用户的回调函数，并传入新的浮点值。

每个扩展模板都通过`build_ui()`中的一组主控回调函数来构建UI。这些回调函数包含了让UI顺畅运行的所有逻辑，并方便用户将自定义代码连接到应用中。

---

## Loaded Scenario Template（加载场景模板）

加载场景模板为用户提供了一个简单的UI，包含三个按钮：Load（加载）、Reset（重置）和Run（运行）。这样设计是为了让用户能以最直接的方式编写代码，影响USD舞台，而无需深入了解底层仿真器的内部机制。用户只需了解以下几个简单概念：

### 重要概念

在Omniverse Kit应用中，有一个仿真时间轴，可以在左侧工具栏直接停止、暂停和播放。只有时间轴处于活动状态（未停止）时，物理仿真才会运行。因此，用户无法在时间轴停止时控制机器人关节（Articulation），并且在时间轴从停止到播放时，需要对某些资产（如Articulation）进行初始化。加载场景模板的目的是让用户更容易与仿真器交互，而无需处理初始化等细节。

在`isaacsim.core.api.world`中有一个单例类`World`，用于以简单明了的方式设置和管理仿真。在该模板中，`World`由Load和Reset按钮管理，确保在调用用户回调函数时，仿真器处于可预测的状态。用户与`World`的交互被简化为`world.scene.add(user_object)`，其中`user_object`是`isaacsim.core.api`中的任意对象。

为确保功能正常，所有时间轴的操作都应通过Load和Reset按钮完成。如果用户在UI外部（左侧工具栏）点击Stop和Play按钮，可能会导致问题。因此，模板会直接处理用户在UI外部操作时间轴的情况，必要时重置UI，以保证用户回调函数的假设成立。

### 实现细节

**Load按钮**有两个回调函数：

- `def setup_scene_fn():`  
    按下Load按钮时，会创建一个新的World实例，然后调用此函数。用户应在此加载资产到舞台，并通过`world.scene.add()`添加到World中。
    
- `def setup_post_load_fn():`  
    用户可以假设资产已通过`setup_scene_fn`加载，对象已正确初始化，且时间轴暂停在第0步。
    

**Reset按钮**有两个回调函数：

- `pre_reset_fn()`：在World重置前调用，此时仿真器状态不确定。
    
- `post_reset_fn()`：用户可以假设对象已正确初始化，时间轴暂停在第0步。  
    还可以假设添加到World的对象已回到默认位置。例如，cube prim会回到`setup_scene_fn()`创建时的位置。
    

**Run按钮**不与World关联。它是一个StateButton（状态按钮），在Run和Stop两种状态间切换。StateButton可以有三个回调函数：

- `on_a_click()`：按钮显示a_text时调用
    
- `on_b_click()`：按钮显示b_text时调用
    
- `physics_callback_fn()`：如果指定，在按钮处于B状态时，每个物理步都会调用此函数；在A状态时取消物理订阅
    

> **注意**  
> 这些函数的调用方式可在`UIBuilder`类中查看（`isaacsim.examples.extension`扩展的`template_source_files/loaded_scenario_workflow/ui_builder.py`文件）。

**如何体验：**  
打开Template Generator（菜单：Utilities > Generate Extension Templates），在Loaded Scenario Template部分创建新扩展。启用扩展（Window > Extensions，搜索扩展名），点击同名工具栏入口即可。
[[扩展1.jpeg|Open: Pasted image 20250514140034.png]]
![[扩展1.jpeg]]
---

## Scripting Template（脚本模板）

脚本模板是加载场景模板的自然扩展，展示了如何在NVIDIA Isaac Sim中通过UI扩展实现更高级的脚本行为。该模板采用与加载场景模板相同的加载和重置机器人位置机制，但将Run按钮实现为脚本。

通过该模板演示的模式，用户可以编写长时间运行的函数，在每个物理步检查并发送新命令，或判断是否需要返回。脚本模板实现了`goto_position()`、`open_gripper_franka()`和`close_gripper_franka()`等函数，这些函数串联起来，实现了简单的抓取与放置任务。

### 实现细节

UI的实现细节与加载场景模板一致，本节重点介绍脚本行为的实现。可以利用Python的yield/generator机制编写每帧检查的长时间运行函数。`scenario.py`文件中实现了`my_script()`函数，包含`goto_position()`、`open_gripper_franka()`和`close_gripper_franka()`的调用。`my_script()`使用了`yield`和`yield from`语句，这样可以用`self._script_generator = self.my_script()`将其包装为生成器。每个物理步调用`next(self._script_generator)`，执行代码直到遇到下一个`yield`（无论在`my_script()`还是嵌套函数中）。

以`open_gripper_franka()`为例：

```python
def open_gripper_franka(self, articulation):
    open_gripper_action = ArticulationAction(np.array([.04,.04]), joint_indices = np.array([7,8]))
    articulation.apply_action(open_gripper_action)

    # 每帧检查一次，直到夹爪完全打开
    while not np.allclose(articulation.get_joint_positions()[7:],np.array([.04,.04]), atol=.001):
        yield()

    return True
```

`my_script()`通过`yield from open_gripper_franka()`调用该函数。`open_gripper_franka()`向Franka机器人发送打开夹爪的命令，然后在每个物理步检查夹爪是否到达目标位置。一旦到达目标位置，函数停止`yield`，返回True，表示成功。控制流回到`my_script()`，继续执行下一个函数。

**如何体验：**  
打开Template Generator（菜单：Utilities > Generate Extension Templates），在Scripting Template部分创建新扩展。启用扩展（Window > Extensions，搜索扩展名），点击同名工具栏入口即可。
[[扩展2.jpeg|Open: Pasted image 20250514140517.png]]
![[扩展2.jpeg]]
---

## Configuration Tooling Template（配置工具模板）

配置工具模板提供了一个简单的模板，是构建资产配置工具的坚实基础。实现中创建了一个下拉菜单，自动查找舞台上的所有Articulation，并动态创建一个UI框架，用户可以通过该框架控制所选Articulation的每个关节。

与加载场景模板不同，该扩展不控制时间轴或舞台。用户可以选择任意对象，读取和写入其状态。构建资产配置工具是更高级的用例，因此需要对仿真时间轴有更好的内部理解。例如，Articulation只有在时间轴播放时才能访问，因此模板只允许用户在时间轴播放时修改所选Articulation。

### 实现细节

下拉菜单通过一个函数填充，该函数会在USD舞台上查找所有指定类型的对象。该功能直接集成在DropDown UI包装器中，模板底部还保留了该函数的一个版本，方便用户自定义。

每当从下拉菜单选择新项时，机器人控制框架会通过构建函数重建。这是一种强大的动态UI工具创建范式。在该模板中，框架可以向用户报告未选中机器人，或在一切正常时列出所选机器人的所有关节。

**如何体验：**  
打开Template Generator（菜单：Utilities > Generate Extension Templates），在Configuration Tooling Template部分创建新扩展。启用扩展（Window > Extensions，搜索扩展名），点击同名工具栏入口。最后，在新舞台（File > New）中添加Franka机器人（Create > Robots > Franka Emika Panda Arm），即可进行操作。
[[isaacsim/图片存放/扩展/扩展.jpeg|Open: Pasted image 20250514140715.png]]
![[isaacsim/图片存放/扩展/扩展.jpeg]]
---

## UI Component Library（UI组件库模板）

UI组件库模板演示了每个UIElementWrapper的用法。设置自定义UI工具时应以此为参考。最重要的是，该模板展示了每个UIElementWrapper可附加的回调函数所需的参数和返回值类型。该模板省略了Load和Reset按钮，这些特殊按钮已在加载场景模板中演示。模板中的UI组件不会直接影响仿真，只会调用用户回调函数。

组件库模板包装了omni.ui中的部分元素，每个包装器都对UI组件的布局和标签有自己的设计，以确保与其他包装组件并排时美观。高级用户可以在包装组件旁边添加omni.ui组件，不会有问题。

**如何体验：**  
打开Template Generator（菜单：Utilities > Generate Extension Templates），在UI Component Library部分创建新扩展。启用扩展（Window > Extensions，搜索扩展名），点击同名工具栏入口。新窗口中会展示全部UI元素。
[[扩展 1.jpeg|Open: Pasted image 20250514140722.png]]
![[扩展 1.jpeg]]
---

## 总结

本教程介绍了NVIDIA Isaac Sim扩展模板生成器提供的各类模板。每个模板都具有通用的底层结构，并通过简单实现展示不同的用例。用户可以参考这些模板，快速开始构建高度自定义的基于UI的扩展，而无需深入了解仿真器的内部机制。

---

## 进一步学习

结合这些模板，用户还应参考UIElementWrapper对象的API文档。

---

## VS Code 高级扩展模板生成器

Isaac Sim VS Code Edition 是一个 Visual Studio Code 扩展，专为 NVIDIA Omniverse（尤其是 Isaac Sim）开发提供支持。其功能之一就是生成高级扩展模板。
[[e26928e95249e7ed83d9e4834f3f3584_MD5.jpeg|Open: Pasted image 20250514140944.png]]
![[e26928e95249e7ed83d9e4834f3f3584_MD5.jpeg]]
### 扩展模板生成器向导

Isaac Sim VS Code Edition 提供了扩展模板生成器向导，可以生成以下类型的扩展模板：

- **可直接使用的扩展（Python）**
    
- **需要基于 Kit 构建系统的扩展（C++ 和/或 Python）**
    

下表列出了模板可生成的扩展组件，以及它们在不同编程语言下的可用性：

|组件|Python|C++|描述|
|---|---|---|---|
|Extension|是|是|定义一个继承自 omni.ext.IExt 的扩展类|
|API|是|是|定义/暴露代码库的 API（应用程序编程接口）|
|OmniGraph|是|是|使用 OmniGraph 框架创建可视化脚本节点|
|Pybind|否|是|使用 pybind11 反射 C++ 代码，使其可被 Python 调用|
|UI|是|否|使用 Omniverse UI 框架创建图形用户界面（GUI）|
|Tests|是|是|为扩展创建测试用例|

---

### 可直接使用的扩展（Ready-To-Use Extensions）

可直接使用的扩展（Python）创建后无需额外配置或构建系统，即可直接使用。

#### 创建扩展

> **注意**  
> 要让扩展可被发现，包含扩展的文件夹必须被列入 Isaac Sim 的扩展搜索路径。  
> 如果没有，可以通过 Isaac Sim 的 Extensions Manager（菜单：Window > Extensions）添加。点击 Extensions Manager 的汉堡菜单图标，选择 Settings，然后添加扩展所在文件夹路径。

> **提示**  
> 为方便起见，Isaac Sim 安装根目录下的 extsUser 文件夹已被列入扩展搜索路径，建议在该文件夹下创建扩展。

**操作步骤：**

1. 在 VS Code 编辑器中，打开 Isaac Sim VS Code Edition 的扩展模板生成器向导（菜单：Templates > Extension）。
    
2. 至少填写/检查以下字段：
    
    - **Ext. name**：扩展名称，例如 my.custom.extension
        
    - **Ext. path**：包含扩展的文件夹路径，例如 PATH_TO_ISAAC_SIM/extsUser
        
    - 勾选“Ready-to-use extension”复选框
        
    - 启用需要生成的具体组件
        
3. 点击“Create”生成扩展。检查指定路径下是否生成了扩展。此时扩展已可用，可根据需要修改代码。
    

#### 运行扩展

1. 启动 Isaac Sim，在 Extension Manager 中搜索并启用新建的扩展（使用扩展名称）。
    
2. 根据所创建的组件，可能会有以下效果（无需额外修改）：
    
    - **Extension/API**：扩展被启用
        
    - **OmniGraph**：可在 Action Graph（菜单：Create > Visual Scripting > Action Graph）中实例化节点 OgnMyCustomExtensionPy
        
    - **UI**：点击 Window > My Custom Extension 菜单可打开示例窗口
        
    - **Tests**：可在 Isaac Sim 根目录下的终端运行测试
        
    
    **Linux/Windows 测试命令示例：**
    
    ```bash
    .\kit\kit --empty --enable omni.kit.test --/exts/omni.kit.test/runTestsAndQuit=true --/exts/omni.kit.test/testExts/0='my.custom.extension' --ext-folder "extsUser" --no-window --allow-root
    ```
    

---

### 需要 Kit 构建系统的扩展

需要 Kit 构建系统的扩展（C++ 和/或 Python）必须作为 Kit SDK 应用（如 Isaac Sim App Template 或 Omniverse Kit App Template）的一部分进行配置和编译。

#### 构建 App Template

获取 Isaac Sim App Template，并按照其文档进行设置和构建。

#### 创建扩展

> **提示**  
> 为方便起见，Isaac Sim App Template 根目录下的 source/extensions 文件夹已在构建系统中配置为扩展源码搜索路径。建议在该文件夹下创建扩展（如不存在可自行创建）。

**操作步骤：**

1. 在 VS Code 编辑器中，打开 Isaac Sim VS Code Edition 的扩展模板生成器向导（菜单：Templates > Extension）。
    
2. 至少填写/检查以下字段：
    
    - **Ext. name**：扩展名称，例如 my.custom.extension
        
    - **Ext. path**：扩展源码文件夹路径，例如 PATH_TO_ISAAC_SIM_APP_TEMPLATE/source/extensions
        
    - 取消勾选“Ready-to-use extension”复选框（如已勾选）
        
    - 启用需要生成的具体组件
        
3. 点击“Create”生成扩展。检查指定路径下是否生成了扩展。
    

#### 配置构建系统

根据所创建的组件，需进行如下配置：

- **OmniGraph**：编辑 `tools/deps/kit-sdk-deps.packman.xml` 文件，添加 USD 依赖：
    
    ```xml
    <import path="...all-deps.packman.xml">
        <!-- 只需添加下一行 -->
        <filter include="usd-${config}"/>
    </import>
    <!-- 只需添加下一行 -->
    <dependency name="usd-${config}" linkPath="../../_build/target-deps/usd/${config}"/>
    ```
    
- **Tests**：编辑 `tools/deps/kit-sdk-deps.packman.xml` 文件，添加 doctest 依赖：
    
    ```xml
    <import path="...all-deps.packman.xml">
        <!-- 只需添加下一行 -->
        <filter include="doctest"/>
    </import>
    <!-- 只需添加下一行 -->
    <dependency name="doctest" linkPath="../../_build/target-deps/doctest"/>
    ```
    

#### 构建扩展

在 Isaac Sim App Template 根目录下的终端运行以下命令进行构建：

**Linux/Windows 构建命令示例：**

```bash
.\repo.bat build
```

#### 运行扩展

1. 启动 Isaac Sim，在 Extension Manager 中搜索并启用新建的扩展（使用扩展名称）。
    
2. 根据所创建的组件，可能会有以下效果（无需额外修改）：
    
    - **Extension/API**：扩展被启用
        
    - **OmniGraph**：可在 Action Graph 中实例化节点 OgnMyCustomExtensionPy（Python）和/或 OgnMyCustomExtensionCpp（C++）
        
    - **Pybind（仅C++）**：通过 pybind11 暴露的 C++ API 可被 Python 调用。例如，在 Script Editor（菜单：Window > Script Editor）中执行：
        
        ```python
        import my.custom.extension
        
        interface = my.custom.extension.acquire_extension_interface()
        my.custom.extension.set_default_status("custom status")
        interface.register_object(10)
        my.custom.extension.release_extension_interface()
        ```
        
    - **UI（仅Python）**：点击 Window > My Custom Extension 菜单可打开示例窗口
        
    - **Tests**：可在 Isaac Sim App Template 根目录下的终端运行测试
        
        **Linux/Windows 测试命令示例：**
        
        ```bash
        .\_build\windows-x86_64\release\tests-my.custom.extension.bat
        ```
        

---
