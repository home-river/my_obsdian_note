# Core（api）
### Articulations（关节）
#### ArticulationGripper（抓手部分）
```python
from isaacsim.core.api.articulations import ArticulationGripper
```
**导入类定义**  
从 Isaac Sim 的核心 API 中载入 `ArticulationGripper` 类，以便后面创建抓手对象并调用其方法。

```python
articulation_gripper = ArticulationGripper(gripper_dof_names=gripper_dof_names)
```
**实例化抓手对象**

- `gripper_dof_names`：一个字符串列表，每个元素对应抓手中一个自由度（Degree of Freedom，DOF）的名称，比如左右两个指爪的关节名。
    
- 这行代码创建了 `ArticulationGripper` 的实例 `articulation_gripper`，内部会存储这些 DOF 名称，以及后续用于控制它们的索引、目标位置等信息。

**对应关系**：

- **模型中的关节名称**：这些是你在创建机械模型时（在 USD 文件或通过编程设置时）为每个关节指定的名字。
    
- **gripper_dof_names 中的名称**：这些名称与模型中的关节名称一一对应，它们将在后续代码中用于控制抓手的动作。

```python
articulation_gripper.apply_action(action=action)
```
**应用控制指令**

- `action`：通常是一个数值数组或张量，表示对每个 DOF 发出的控制信号（比如目标位置、力矩或速度命令）。
    
- `apply_action()` 方法会将这些信号转发给底层的物理仿真控制器，让抓手朝指定目标运动。

```python
closed_position = articulation_gripper.closed_position
```

**获取“闭合”位置常量**

- `closed_position` 通常是一个数值（或数组），表示抓手完全闭合时的标准关节角度／位置。
    
- 从实例属性中读取，方便后续在决策逻辑里与当前关节位置比较，或者用作动作命令的上界。


```python
dof_indices = articulation_gripper.dof_indices
```

**获取 DOF 索引列表**

- `dof_indices` 是一个整数列表，对应 `gripper_dof_names` 在 USD Stage（场景）或物理引擎中分配到的具体 DOF 索引。
    
- 拿到索引后，可以通过底层 API 直接按索引访问或修改关节状态。



```python
positions = articulation_gripper.get_positions()
```
**读取当前关节位置**

- `get_positions()` 方法返回当前时刻所有 DOF 的实际位置（关节角度或伸缩量），通常以数组形式给出。
    
- 可用于监控、日志记录，或者在需要“恢复”到某个姿态时做状态保存。



```python
velocities = articulation_gripper.get_velocities()
```
**读取当前关节速度**

- `get_velocities()` 方法返回当前所有 DOF 的速度值，同样以数组形式返回。
    
- 对“惯性补偿”或控制器调优（比如PD控制）非常有用。



```python
articulation_gripper.initialize(root_prim_path=root_prim_path,
                             articulation_controller=articulation_controller)
```
**初始化抓手对象**

- `root_prim_path`：USD Stage 中该抓手对应的根 Prim（关节体系根节点）的路径字符串，例如 `"/World/robot/gripper"`.
    
- `articulation_controller`：一个控制器实例（通常由更高级的控制框架提供），负责在仿真循环中下发控制命令、收集状态。
    
- `initialize()` 会将 `ArticulationGripper` 与 USD 场景和物理控制器关联起来，建立从高层 API 到底层仿真引擎的映射。



```python
open_position = articulation_gripper.open_position
```
**获取“张开”位置常量**

- `open_position` 与 `closed_position` 类似，表示抓手完全张开时各 DOF 的位置。
    
- 可用作动作指令下界，或者在策略里快速切换到“空”状态。


```python
articulation_gripper.set_positions(positions=positions)
```
**批量设置关节位置**

- 将之前通过 `get_positions()` 读取并保存在 `positions` 里的数值，再次写回到仿真中。
    
- 常用于场景重置、从检查点恢复状态，或在非控制回路（直接状态设定）中瞬时“跳转”到某个姿态。


```python
articulation_gripper.set_velocities(velocities=velocities)
```

**批量设置关节速度**

- 与 `set_positions()` 同理，将之前保存的速度值恢复到当前仿真状态。
    
- 对于需要保持连续运动或准确模拟惯性场景（比如录制回放、碰撞检测测试）非常有帮助。



#### articulationsubset（关节子集）
这是关节系统的子集，允许直接控制某部分关节而不是全部关节。


**导入 ArticulationSubset 类**
```python
from isaacsim.core.api.articulations import ArticulationSubset
```

这一行从 `isaacsim.core.api.articulations` 模块导入了 `ArticulationSubset` 类，它是 Isaac Sim 中用于操作和管理机器人关节系统子集的类。使用这个类，你可以仅控制 Articulation 系统中的某些关节，而不是整个关节系统。

**实例化 ArticulationSubset 对象**
```python
articulation_subset = ArticulationSubset(articulation=articulation, joint_names=joint_names)
```
**解读**：

- 你通过 `ArticulationSubset` 类实例化一个对象 `articulation_subset`。
    
- 这个实例化需要两个参数：
    
    - `articulation`：这是你想要操作的完整关节系统，通常是一个机器人或机械臂的关节系统。
        
    - `joint_names`：这是一个字符串列表，包含了你想要操作的关节名称。这些关节将作为子集进行控制。
        

**作用**：  
创建一个 `ArticulationSubset` 对象，表示从完整的 `articulation` 系统中提取出的部分关节（由 `joint_names` 提供）。


**应用动作**
```python
articulation_subset.apply_action()
```
**解读**：

- `apply_action()` 方法用于将控制动作应用到 `articulation_subset` 上。这个方法通常需要一些控制指令作为输入，例如位置、速度或力矩等。
    
- **注意**：此处的 `apply_action()` 方法并没有传入参数，实际使用时通常会传入特定的动作数据（例如目标位置）。
    

**作用**：  
这行代码是将一个控制指令（动作）应用到指定的关节子集上，通常是执行某个动作如闭合、抓取等。

**获取已应用的动作**
```python
applied_action = articulation_subset.get_applied_action()
```
**解读**：

- `get_applied_action()` 方法用于返回已经应用的控制动作。
    
- 这个动作通常是一个关于关节位置、速度、力矩等的命令，可以帮助你检查当前系统执行了什么动作。
    

**作用**：  
查询并返回当前 `articulation_subset` 上应用的动作，通常用于调试或确认动作是否正确应用。


**获取关节的作用力**
```python
joint_efforts = articulation_subset.get_joint_efforts()
```
**解读**：

- `get_joint_efforts()` 方法用于获取关节的作用力（即关节施加的力矩）。
    
- 这些力矩信息对机器人动态建模、碰撞检测和控制器调试等非常有用。
    

**作用**：  
获取指定关节子集上的力矩或控制力，用于了解关节的当前受力状态。


**获取关节的位置**

```python
joint_positions = articulation_subset.get_joint_positions()
```
**解读**：

- `get_joint_positions()` 方法返回关节的当前位置信息，通常是角度（对于旋转关节）或位移（对于滑动关节）。
    

**作用**：  
读取当前关节的位置信息，用于状态监控、反馈控制、调试等。

**获取关节子集的索引**

```python
joint_subset_indices = articulation_subset.get_joint_subset_indices()
```
**解读**：

- `get_joint_subset_indices()` 返回当前关节子集的索引。这些索引通常是内部用于高效访问和操作关节数据的数字表示。
    

**作用**：  
用于获取关节子集在完整关节系统中的位置索引，有助于对关节的快速定位和访问。

**获取关节的速度**

```python
joint_velocities = articulation_subset.get_joint_velocities()
```
**解读**：

- `get_joint_velocities()` 返回当前关节子集的速度数据，通常是各关节的角速度或线速度。
    
- 这对于动态控制、控制器调节（如 PID 控制）非常重要。
    

**作用**：  
读取关节的运动速度，常用于控制和调节关节的行为，特别是在运动规划中。

**获取关节的状态**

```python
joints_state = articulation_subset.get_joints_state()
```
**解读**：

- `get_joints_state()` 方法返回关节的综合状态，可能包括关节的位置、速度、力矩等信息。
    
- 这个方法通常返回一个包含多个属性的复合对象，适合用来查看多个关节的状态。
    

**作用**：  
获取关节子集的综合状态信息，通常用于完整的反馈控制或者仿真结果分析。

**检查是否已初始化**

```python
is_initialized = articulation_subset.is_initialized
```
**解读**：

- `is_initialized` 属性用于检查该关节子集是否已经被初始化。初始化意味着该子集已经与场景中的关节系统建立了连接，能够正常工作。
    

**作用**：  
检查 `articulation_subset` 是否已经初始化，通常用于确保你在执行其他操作之前，子集已经准备好并能够正常运行。

**获取关节的索引**

```python
joint_indices = articulation_subset.joint_indices
```
**解读**：

- `joint_indices` 属性返回关节子集中的各个关节在 `articulation` 系统中的索引，便于在系统中高效管理和定位这些关节。
    

**作用**：  
获取关节子集的索引，用于在内部算法中快速查找和引用特定关节。

**创建关节操作**

```python
value =articulation_subset.make_articulation_action(
							joint_positions=joint_positions, 
							joint_velocities=joint_velocities)
```
**解读**：

- `make_articulation_action()` 用于创建一个新的关节操作，通常是基于指定的关节位置和速度。
    
- 这个操作可以在仿真或控制过程中使用，用来指定关节应执行的动作。
    

**作用**：  
生成一个新的动作，基于关节的目标位置和速度，可以在仿真中执行该动作。

**映射到关节顺序**

```python
value = articulation_subset.map_to_articulation_order(joint_values=joint_values)
```
**解读**：

- `map_to_articulation_order()` 用于将关节值映射到实际的关节顺序。这通常用于保证动作数据的顺序与物理系统中的关节顺序一致。
    

**作用**：  
将关节值（位置、速度等）映射到物理系统中的实际关节顺序，确保控制命令和仿真系统的匹配。



 **获取关节数目**


```python
num_joints = articulation_subset.num_joints
```

**解读**：

- `num_joints` 属性返回关节子集中的关节数量。这有助于理解当前子集涉及的关节数量，并在程序中做出相应调整。
    

**作用**：  
获取当前关节子集的关节数目，通常用于控制和资源管理。

---

**设置关节的作用力**

```python
articulation_subset.set_joint_efforts(efforts=efforts)
```

**解读**：

- `set_joint_efforts()` 用于设置关节的作用力，通常是关节的力矩（即驱动力）。
    
- `efforts` 参数是一个数组，包含了每个关节应施加的力。
    

**作用**：  
设置关节子集的力矩或驱动力，用于控制或调整关节的运动。

---

 **设置关节的位置**


```python
articulation_subset.set_joint_positions(positions=positions)
```

**解读**：

- `set_joint_positions()` 用于设置关节的位置，通常是指定目标位置，关节将朝这个目标位置运动。
    
- `positions` 是一个数组，包含每个关节的目标位置。
    

**作用**：  
设置关节的目标位置，用于控制关节的姿态。

---

**设置关节的速度**

```python
articulation_subset.set_joint_velocities(velocities=velocities)`
```

**解读**：

- `set_joint_velocities()` 用于设置关节的速度，通常是指定关节应以某种速度运动。
    
- `velocities` 是一个数组，包含每个关节的目标速度。
    

**作用**：  
设置关节的目标速度，用于控制关节的运动速率。


### controller（控制器）
`isaacsim.core.api.controllers` 模块是与 **控制器（Controller）** 相关的功能部分。控制器是机器人或机械系统中的核心组件之一，负责根据设定的目标（如位置、速度、力矩等）生成控制命令，并通过物理引擎将这些命令传递给机器人关节，从而使得机器人能够按照预定的行为进行运动。

#### ArticulationController
`ArticulationController` 是 **Isaac Sim** 中一个关键的控制器类，它专门用于控制多关节系统（例如机器人臂、机械装置等）。这个控制器主要用于操作和控制由多个关节组成的机器人系统，通过控制机器人关节的位置、速度、力矩等，以实现所需的动作。

**导入模块**
```python
from isaacsim.core.api.controllers import ArticulationController
```

**解读**：  
这行代码从 **Isaac Sim** 的控制器模块导入了 `ArticulationController` 类。这个控制器类用于控制多关节系统，如机器人臂，支持多种控制模式（位置、速度、力矩控制等）。

---

**创建控制器实例**
```python
articulation_controller = ArticulationController()
```

**解读**：  
创建一个 `ArticulationController` 对象 `articulation_controller`，用于控制机器人或机械臂的关节动作。

---

**应用控制动作**
```python
articulation_controller.apply_action(control_actions=control_actions)
```

**解读**：

- `apply_action()` 方法用于将控制动作 `control_actions` 应用到关节系统上。
    
- `control_actions` 是一个`ArcticulationAction`对象，它包含以下字段：

	- **`joint_positions`**：一个数组或张量，表示关节的目标位置。这个字段是可选的，可以为 `None`，如果没有提供该字段，默认不会应用位置控制。
    
	- **`joint_velocities`**：一个数组或张量，表示关节的目标速度。这个字段也是可选的，可以为 `None`，如果没有提供该字段，默认不会应用速度控制。
    
	- **`joint_efforts`**：一个数组或张量，表示关节的目标力矩（efforts）。如果没有提供该字段，默认会将力矩设为零。
    
	- **`joint_indices`**：一个可选的数组，表示指定的关节自由度（DOF）的索引，用于选择应用控制动作的关节。如果为 `None`，则默认应用于所有关节自由度。
    

**示例**：
```python
control_actions = [0.5, 0.2, -0.3]  # 目标位置、速度或力矩 articulation_controller.apply_action(control_actions=control_actions)
```

---

**获取已应用的控制动作**

```python
applied_action = articulation_controller.get_applied_action()
```

**解读**：  
`get_applied_action()` 返回当前已应用的控制动作。它帮助用户检查当前控制系统执行了哪些动作。

---

**获取关节的控制模式**
```python
effort_modes = articulation_controller.get_effort_modes()
```

**解读**：  
`get_effort_modes()` 返回关节的当前控制模式，通常是指力矩控制模式（Effort Mode）。它表示每个关节在控制过程中是否应用了力矩控制。

---

**获取控制器增益**
```python
gains = articulation_controller.get_gains()
```

**解读**：  
`get_gains()` 返回当前控制器的增益值，通常用于 PID 控制算法中。这些增益值调节了控制器对误差的响应。

---

 **获取关节的运动限制**

```python
joint_limits = articulation_controller.get_joint_limits()
```

**解读**：  
`get_joint_limits()` 返回关节的位置和速度限制，确保机器人关节在物理允许的范围内运动，防止超出最大角度或速度。

---

  **获取最大力矩**
```python
max_efforts = articulation_controller.get_max_efforts()
```

**解读**：  
`get_max_efforts()` 返回各关节可承受的最大力矩，防止关节或执行器施加过大力矩造成损坏。

---

 **初始化控制器**
```python
articulation_controller.initialize(articulation_view=articulation_view)
```

**解读**：  
`initialize()` 方法将控制器与具体的关节视图（`articulation_view`）进行绑定。`articulation_view` 是一个表示多关节系统的视图，控制器需要与该视图关联才能执行控制。

---

**设置力矩控制模式**
```python
articulation_controller.set_effort_modes(mode=mode)
```

**解读**：  
`set_effort_modes()` 用于设置所有关节的力矩控制模式为 `mode`。`mode` 可以是力矩控制（如 'torque'）或其他类型的模式。

**示例**：
```python
mode = 'torque' 
articulation_controller.set_effort_modes(mode=mode)
```

---

 **设置控制器增益**
```python
articulation_controller.set_gains()
```

**解读**：  
`set_gains()` 方法设置控制器的增益，通常用于控制算法（如 PID 控制器），调整机器人的响应速度、稳态误差等。

---

 **设置最大力矩**
```python
articulation_controller.set_max_efforts(values=values)
```

**解读**：  
`set_max_efforts()` 设置每个关节的最大允许力矩（efforts），防止关节过度负荷。

**示例**：
```python
values = [5.0, 10.0, 15.0]  # 每个关节的最大力矩 articulation_controller.set_max_efforts(values=values)
```

---

 **切换控制模式**
```python
articulation_controller.switch_control_mode(mode=mode)
```

**解读**：  
`switch_control_mode()` 切换整个系统的控制模式。`mode` 可以是 `position`（位置控制）、`velocity`（速度控制）、`effort`（力矩控制）等。

**示例**：
```python
mode = 'position'
articulation_controller.switch_control_mode(mode=mode)
```

---

 **切换特定关节的控制模式**
```python
articulation_controller.switch_dof_control_mode(dof_index=dof_index, mode=mode)
```

**解读**：  
`switch_dof_control_mode()` 用于切换某个具体自由度（DOF，通常是一个关节）的控制模式。`dof_index` 是关节的索引，`mode` 是要切换到的控制模式（如 `position`、`velocity` 或 `effort`）。

**示例**：
```python
dof_index = 0  # 第一个关节
mode = 'effort'  # 设置为力矩控制模式
articulation_controller.switch_dof_control_mode(dof_index=dof_index, mode=mode)
```


#### BaseController
它是一个抽象控制器类，所有具体的控制器类都应该继承自它，并实现 `forward` 方法。`BaseController` 作为基类，提供了控制器的基础框架和部分功能，继承它的子类会扩展并实现具体的控制逻辑。

1. **导入 `BaseController`**
```python
from isaacsim.core.api.controllers import BaseController
```

**解读**：

- 这行代码导入了 **`BaseController`** 类，它是一个抽象的基类，用于定义控制器的基本结构。
    
- `BaseController` 类通常是其他控制器（如 `ArticulationController`）的父类。具体的控制逻辑会在继承自 `BaseController` 的类中实现。
    

---
2. **创建 `BaseController` 实例**

```python
base_controller = BaseController(name=name)
```

**解读**：

- **`BaseController(name=name)`**： 这行代码创建了一个 `BaseController` 实例，并传入了一个名为 `name` 的参数。`name` 可能是控制器实例的名称或标识符，用于区分不同的控制器实例。
    
- **`name` 参数**：`name` 是用于标识控制器的字符串参数，可能在调试、日志记录或者用户界面中用于识别不同的控制器。
    
    但是，值得注意的是，`BaseController` 是一个 **抽象类**，不能直接实例化，因此在实际的使用中，这一行代码通常会抛出 `TypeError`，除非 `BaseController` 已经被某个具体类（如 `ArticulationController`）继承并实现了所有必要的方法。
    
---

3. **调用 `forward()` 方法**

```python
value = base_controller.forward()
```

**解读**：

- **`forward()`**：`forward` 是一个抽象方法，在 `BaseController` 中没有实现，必须在继承的子类中实现。该方法的目的是接受输入（如传感器观测数据、当前状态等），并生成控制动作（通常是 `ArticulationAction` 对象）。控制动作会传递给下层的控制系统（如 `ArticulationController`）。
    
- **返回值**：`forward()` 方法返回的是一个 **`ArticulationAction`** 对象，表示要执行的控制动作，通常包括关节位置、速度和力矩等。`value` 将存储这个返回的控制动作。
    
    **示例**：
    
```python
class MyController(BaseController):   
	def forward(self, observations: dict) -> ArticulationAction:         
		joint_positions = observations["joint_positions"]         
		return ArticulationAction(joint_positions=joint_positions)
		
controller = MyController(name="MyController") 
observations = {"joint_positions": [0.5, 0.3, -0.2]} 
value = controller.forward(observations)
```
在这个例子中，`forward` 方法会返回一个 `ArticulationAction` 对象，包含目标关节位置。
    
- **注意**：因为 `BaseController` 是抽象类，因此 `forward()` 方法在 `BaseController` 中没有实际实现。要使用这个方法，必须在继承类中实现具体的控制逻辑。
    

---

4. **调用 `reset()` 方法**
```python
base_controller.reset()
```

**解读**：

- **`reset()`**：`reset` 方法用于重置控制器的状态。通常，这会清除控制器中的任何累积状态或数据，恢复到初始状态，准备进行下一次控制。
    
- 在 `BaseController` 类中，`reset()` 方法没有具体实现，但它会在继承类中进行实现，通常用于重置控制器的内部状态、清除历史数据、重置机器人状态等。
    
    **示例**：  
    如果在 `MyController` 中实现了 `reset()` 方法，它可能会清除某些控制参数或重置关节位置：
    
```python
class MyController(BaseController):
    def forward(self, observations: dict) -> ArticulationAction:
        joint_positions = observations["joint_positions"]
        return ArticulationAction(joint_positions=joint_positions)

    def reset(self):
        self._last_position = [0.0, 0.0, 0.0]

```
在这个例子中，`reset()` 方法清除了控制器的 `last_position` 状态。


#### BaseGripperController
继承自 `BaseController`，专门用于控制机械臂的抓手（gripper）。该类提供了一个用于操作抓手的方法（如打开、闭合），并定义了一个框架，用于具体的抓手控制类（例如 `GripperController`）进行继承和实现。

1. **创建 `BaseGripperController` 实例**
```python
base_gripper_controller = BaseGripperController(name=name)
```

**解读**：

- 这行代码尝试实例化 `BaseGripperController` 类并传递 `name` 参数。
    
- **问题**：`BaseGripperController` 是一个 **抽象类**，不能直接实例化，因此此行代码在实际运行时会抛出 `TypeError`，除非你已经定义了一个继承 `BaseGripperController` 的具体子类（例如 `GripperController`）并实现了所有抽象方法。
    
- `name` 是控制器实例的名称，用于标识该控制器。
    

2. **调用 `close()` 方法**

```python
value=base_gripper_controller.close(
current_joint_positions=current_joint_positions)
```

**解读**：

- 这行代码调用了 `close()` 方法，传入当前的关节位置（`current_joint_positions`）。
    
- **问题**：`close()` 是一个抽象方法，在 `BaseGripperController` 中没有具体实现，因此这行代码会抛出 `NotImplementedError`，除非该方法在子类中被实现。子类需要提供抓手闭合的具体实现。
    
- `current_joint_positions` 是一个 NumPy 数组，表示当前抓手的关节位置。

**意义**：

- **抓手闭合**：与 `open()` 方法相对应，`close()` 方法表示控制器执行抓手闭合的操作。它计算当前抓手的关节位置（`current_joint_positions`）并生成一个新的控制指令来闭合抓手。
    
- **抽象实现**：与 `open()` 方法一样，`close()` 是一个抽象方法，子类需要实现这个方法来定义如何通过关节的控制来闭合抓手。
    
- **返回控制动作**：和 `open()` 一样，`close()` 方法返回一个 **`ArticulationAction`** 对象，表示闭合抓手所需的关节控制动作。

3. **调用 `forward()` 方法**
```python
value = base_gripper_controller.forward(action=action, current_joint_positions=current_joint_positions)
```

**解读**：

- 这行代码调用了 `forward()` 方法，传入了 `action`（动作类型，通常是 `"open"` 或 `"close"`）和 `current_joint_positions`（当前关节位置）。
    
- **问题**：`forward()` 方法是一个抽象方法，在 `BaseGripperController` 中没有具体实现，因此这行代码同样会抛出 `NotImplementedError`，除非在子类中实现了该方法。该方法会基于 `action` 执行抓手的操作，并返回一个控制动作（通常是 `ArticulationAction`）。
    
    例如，子类可能会实现 `forward()` 来决定是打开还是闭合抓手。


**意义**：

- **控制系统的核心计算**：`forward()` 方法是控制器的核心方法之一，通常用于接收当前状态并计算下一个控制动作。它通常会接收从传感器、外部输入或者内部计算得出的状态信息，并根据这些信息决定机器人的行为。
    
- **动作决策**：在该方法中，控制器根据传入的 `action`（例如 "open" 或 "close"）和当前的 `current_joint_positions`（关节的当前位置）来决定下一步应该如何控制抓手的运动。例如，"open" 操作会使抓手张开，"close" 操作会使抓手闭合。
    
- **返回控制动作**：`forward()` 方法通常会生成并返回一个 **`ArticulationAction`** 对象，表示控制指令（如目标位置、速度等），这些指令会被传递给机器人执行。
    

4. **调用 `forward()` 方法（无参数）**
```python
value = base_gripper_controller.forward()
```

**解读**：

- 这行代码再次调用了 `forward()` 方法，但没有传递任何参数。由于 `forward()` 是一个抽象方法，且在 `BaseGripperController` 中没有实现，因此这行代码会抛出 `NotImplementedError`，要求在子类中实现该方法。
    
    正常情况下，`forward()` 应该接收如 `action` 和 `current_joint_positions` 之类的参数。如果没有提供参数，可能会引发错误，或者根据设计，`forward()` 需要设置一些默认行为。
    

5. **调用 `open()` 方法**

```python
value = base_gripper_controller.open(current_joint_positions=current_joint_positions)
```

**解读**：

- 这行代码调用了 `open()` 方法，传入了当前的关节位置（`current_joint_positions`）。
    
- **问题**：`open()` 是一个抽象方法，在 `BaseGripperController` 中没有实现，因此这行代码会抛出 `NotImplementedError`。子类必须实现 `open()` 方法，定义抓手的具体打开操作，并返回一个 **`ArticulationAction`**（表示控制指令）。
    
**意义**：

- **抓手打开**：`open()` 方法通常表示一个具体的操作，用来指示控制器执行抓手的打开动作。它通过计算当前关节的位置（`current_joint_positions`）来决定抓手的动作，从而改变抓手的状态（通常是让抓手张开）。
    
- **抽象实现**：在 `BaseGripperController` 中，`open()` 是一个抽象方法，意味着没有给出具体的实现。子类（如具体的抓手控制器）需要提供实现，告诉控制器如何通过给定的关节位置打开抓手。
    
- **返回控制动作**：返回一个 **`ArticulationAction`** 对象，描述抓手打开所需的关节控制动作（例如目标位置、速度等）。


6. **调用 `reset()` 方法**

```python
base_gripper_controller.reset()
```

**解读**：

- 这行代码调用了 `reset()` 方法，通常用于将控制器的状态重置为初始状态。在 `BaseGripperController` 中，`reset()` 没有实际实现，因此这个调用不会做任何事情。如果在子类中有实现 `reset()`，它可能用于重置控制器的内部状态，或恢复抓手到默认状态。


---

### DataLogger
`DataLogger` 类用于收集、存储和读取仿真过程中产生的数据。它提供了数据的记录、暂停、恢复、重置等功能，并支持将数据保存到 JSON 文件中，也可以从 JSON 文件中加载已保存的数据。

使用前需要使用下面的语句导入`DataLogger`类
```python
from isaacsim.core.api.loggers import DataLogger
```

1. **创建 `DataLogger` 实例**
```python
data_logger = DataLogger()
```

**解读**：

- **创建 `DataLogger` 实例**：这行代码创建了一个 `DataLogger` 类的实例，名为 `data_logger`，用于收集和管理仿真数据。此对象将用于存储每个时间步的仿真数据，并能够控制数据记录的开始、暂停、保存和加载。
    

---

2. **添加数据到日志**
```python
data_logger.add_data(data=data, current_time_step=current_time_step, current_time=current_time)
```
**解读**：

- **`add_data()`**：此方法将新的数据添加到日志中，并将其保存在数据帧（`DataFrame`）中。
    
- **参数**：
    
    - `data`：表示要添加的数据，通常是一个字典，包含当前仿真步骤的数据。
        
    - `current_time_step`：当前的仿真时间步，通常是一个整数或浮动的数值，表示仿真进行的时间步。
        
    - `current_time`：当前的仿真时间，通常以秒为单位，表示仿真开始到当前的时间。
        
    
    **功能**：这行代码会将数据、时间步和时间信息作为一个 `DataFrame` 对象存储到 `data_logger` 的内部列表中。每个数据帧记录一个时间步的数据。
    

---

3. **添加数据帧日志函数**
```python
data_logger.add_data_frame_logging_func(func=func)
```

**解读**：

- **`add_data_frame_logging_func()`**：这个方法允许用户为 `DataLogger` 注册一个回调函数 `func`，该函数将在每个时间步被调用来收集数据。
    
- **参数**：
    
    - `func`：一个函数，签名为 `func(tasks, scene)`，它接收当前仿真任务和场景对象，返回一个字典，表示收集到的数据。
        
    
    **功能**：每次数据记录时，`DataLogger` 会调用这个 `func` 函数来获取当前的仿真数据。这使得用户可以在每个时间步动态地收集数据。
    

---

4. **获取指定的数据帧**

```python
data_frame = data_logger.get_data_frame(data_frame_index=data_frame_index)
```

**解读**：

- **`get_data_frame()`**：该方法从 `DataLogger` 中获取指定索引的数据帧。
    
- **参数**：
    
    - `data_frame_index`：指定的数据帧的索引。
        
    
    **返回值**：返回指定索引的 `DataFrame` 对象，包含该时间步的数据、时间步和时间信息。
    
    **功能**：用户可以使用此方法获取记录的数据帧，进行分析或调试。
    

---

5. **获取数据帧的数量**

```python
num_of_data_frames = data_logger.get_num_of_data_frames()
```

**解读**：

- **`get_num_of_data_frames()`**：该方法返回当前已收集的数据帧的数量。
    
    **返回值**：返回一个整数，表示当前记录的所有数据帧的数量。
    
    **功能**：用于检查当前记录的数据量，确认是否收集了足够的数据。
    

---
6. **检查数据收集是否已开始**

```python
value = data_logger.is_started()
```

**解读**：

- **`is_started()`**：该方法返回一个布尔值，表示数据收集是否已启动。
    
    **返回值**：
    
    - `True`：如果数据收集已开始或恢复。
        
    - `False`：如果数据收集处于暂停状态。
        
    
    **功能**：该方法可用于检查数据是否已经开始记录。
    

---
7. **从文件加载数据**


```python
data_logger.load(log_path=log_path)
```
**解读**：

- **`load()`**：该方法用于从指定的文件路径加载先前保存的数据。
    
- **参数**：
    
    - `log_path`：要加载的 JSON 文件路径。
        
    
    **功能**：从保存的数据文件中恢复数据，通常用于继续仿真或从之前的记录中读取数据。加载后，数据收集会从文件恢复的状态继续。
    

---
8. **暂停数据收集**

```python
data_logger.pause()
```

**解读**：

- **`pause()`**：该方法用于暂停数据收集。
    
    **功能**：暂停数据记录。这意味着数据不会被进一步收集，直到调用 `start()` 方法恢复收集。
    

---
9. **重置数据日志**

```python
data_logger.reset()
```

**解读**：

- **`reset()`**：该方法重置数据日志，清除所有已记录的数据，并暂停数据收集。
    
    **功能**：当需要从头开始新的数据记录时，使用此方法重置数据。所有先前收集的数据将被清空，准备重新开始收集数据。
    

---
10. **保存数据到文件**

```python
data_logger.save(log_path=log_path)
```

**解读**：

- **`save()`**：该方法将当前的收集数据保存到指定路径的 JSON 文件中。
    
- **参数**：
    
    - `log_path`：保存日志的目标文件路径。
        
    
    **功能**：将当前所有收集到的数据（存储在 `self._data_frames` 中）保存到文件中。此文件可以用于后续的加载或分析。
    
---
11. **开始数据收集**

```python
data_logger.start()
```

**解读**：

- **`start()`**：该方法用于恢复或启动数据收集。
    
    **功能**：如果数据收集已经暂停，调用此方法会恢复数据收集。如果之前没有开始过数据收集，它将启动数据收集。


### Material
在 **Isaac Sim** 中，`material` 模块通常涉及物理仿真中的材料属性的设置，尤其是与仿真物体的表面属性（如摩擦、反射、光泽等）相关。材料模块可以控制物体如何与其他物体交互，包括它们在仿真中的物理表现、外观和碰撞行为。

#### `DeformableMaterial(可变型材料)`
`DeformableMaterial`定义了一个用于模拟软体物体（如柔性材料或软体物体）在 Isaac Sim 中的变形材料类 。它通过使用 NVIDIA 的 PhysX 和 USD（Universal Scene Description）框架来处理物理属性，并将这些物理属性应用到指定路径的 USD prim（物体原型）上。

1. **导入 `DeformableMaterial`**

```python
from isaacsim.core.api.materials import DeformableMaterial
```

- 这行代码导入了 `DeformableMaterial` 类，允许在代码中创建并使用该类来操作变形材料。
    
---

2. **创建 `DeformableMaterial` 实例**

```python
deformable_material = DeformableMaterial(prim_path=prim_path)
```

- 这一行代码创建了一个 `DeformableMaterial` 对象 `deformable_material`。`prim_path` 是指定的 USD prim 路径，表明这个材料会应用到此路径下的物体。
    

---

3. **获取 `damping_scale`**

```python
damping_scale = deformable_material.get_damping_scale()
```

- 通过调用 `get_damping_scale()` 方法，获取当前变形材料的阻尼缩放系数 (`damping_scale`)。该系数描述了材料的阻尼效应，控制能量损耗的速率。
    
---

4. **获取 `dynamic_friction`**

```python
dynamic_friction = deformable_material.get_dynamic_friction()
```
- 调用 `get_dynamic_friction()` 方法获取当前的动态摩擦系数。该系数控制物体在相互作用时的摩擦力。
    
---

5. **获取 `elasticity_damping`**

```python
elasticity_damping = deformable_material.get_elasticity_damping()
```

- 使用 `get_elasticity_damping()` 获取当前的弹性阻尼系数。该系数控制物体在变形过程中的弹性恢复速率。
    
---

6. **获取 `poissons_ratio`**

```python
poissons_ratio = deformable_material.get_poissons_ratio()
```

- 通过 `get_poissons_ratio()` 获取当前的泊松比，描述材料在拉伸时其横向收缩的比率。
    

---

 7. **获取 `youngs_modululs`**

```python
youngs_modululs = deformable_material.get_youngs_modululs()
```

- 调用 `get_youngs_modululs()` 获取当前的杨氏模量，这个值控制材料的刚度，决定其在受力下的变形程度。
    

8. **初始化材料**
```python
deformable_material.initialize()
```

- 该行代码调用 `initialize()` 方法，初始化 `DeformableMaterial` 对象。这通常用于在仿真中准备该材料，使其开始参与物理仿真计算。
    

9. **检查 `DeformableMaterial` 是否有效**

```python
value = deformable_material.is_valid()
```

- 调用 `is_valid()` 方法检查当前 `DeformableMaterial` 对象是否有效。返回值 `value` 是一个布尔值，表示该对象是否有效（即对应的 USD prim 是否有效）。
    

10. **获取 `material` 对象**


```python
material = deformable_material.material
```

- 通过 `material` 属性，获取与该变形材料对象相关的 USD 材料对象。这个对象代表了物理材料的定义。
    

11. **获取 `name`**

```python
name = deformable_material.name
```

- 调用 `name` 属性获取 `DeformableMaterial` 对象的名称。如果没有显式设置名称，则返回 `None`。
    

12. **重置材料**

```python
deformable_material.post_reset()
```

- `post_reset()` 方法用于将 `DeformableMaterial` 对象的状态恢复到默认状态。一般来说，恢复到最初的物理状态。
    

13. **获取 `prim` 对象**

```python
prim = deformable_material.prim
```

- 通过 `prim` 属性获取当前变形材料对象的 USD prim。这个 prim 是在 USD Stage 中对应的物体原型，可以对其进行操作。
    

14. **获取 `prim_path`**

```python
prim_path = deformable_material.prim_path
```

- 使用 `prim_path` 属性获取变形材料对应的 prim 路径。这是一个字符串，表示在 USD Stage 中的物体位置。
    

15. **设置 `damping_scale`**

```python
deformable_material.set_damping_scale(value=value)
```

- 通过 `set_damping_scale()` 方法设置新的阻尼缩放系数。`value` 是你希望设置的系数值。
    

16. **设置 `dynamic_friction`**

```python
deformable_material.set_dynamic_friction(value=value)
```

- 使用 `set_dynamic_friction()` 方法设置新的动态摩擦系数。`value` 是你希望设置的系数值。
    

17. **设置 `elasticity_damping`**

```python
deformable_material.set_elasticity_damping(value=value)
```

- 通过 `set_elasticity_damping()` 方法设置新的弹性阻尼系数。`value` 是你希望设置的系数值。
    

18. **设置 `poissons_ratio`**

```python
deformable_material.set_poissons_ratio(value=value)
```

- 使用 `set_poissons_ratio()` 方法设置新的泊松比。`value` 是你希望设置的系数值。
    

19. **设置 `youngs_modululs`**


```python
deformable_material.set_youngs_modululs(value=value)
```

- 通过 `set_youngs_modululs()` 方法设置新的杨氏模量。`value` 是你希望设置的系数值。


#### `DeformableMaterialView`
**模块定位**  
`DeformableMaterialView` 用于 Isaac Sim 中批量管理符合给定 USD 路径正则的可变形材质。它统一封装了编辑模式（USD API）与仿真模式（PhysX 张量视图）下，对摩擦、弹性、阻尼、泊松比、杨氏模量等属性的高效读写。

---

**核心属性**

- `count` (int, property)：匹配到的材质数量。
    
- `name` (str, property)：构造时指定的视图名称。
    
- `_prim_paths` (List[str])：匹配到的 USD Prim 路径列表。
    
- `_prims` (List[Usd.Prim])：对应的 USD Prim 对象列表。
    
- `_regex_prim_paths` (str)：用于物理视图创建的正则表达式（`.`*→`*`）。
    
- `_backend` / `_backend_utils`：Torch 或 NumPy 张量后端及其工具。
    
- `_device` (str)：运行设备，编辑态固定为 `"cpu"`。
    
- `_material_apis` (List[PhysxDeformableBodyMaterialAPI | None])：编辑模式下延迟附加的材质 API。
    
- `_physics_sim_view` (SimulationView)：物理仿真视图实例（在 `initialize` 后存在）。
    
- `_physics_view` (SoftBodyMaterialView)：物理模式下的软体材质批量张量视图。
    

---

**全部接口**

> **生命周期**
> 
> - `__init__(prim_paths_expr: str, name: str = ..., dynamic_frictions=None, youngs_moduli=None, poissons_ratios=None, elasticity_dampings=None, damping_scales=None)`  
>     构造并缓存 Prim；选择后端；可选批量初始化各属性。
>     
> - `initialize(physics_sim_view: SimulationView = None) -> None`  
>     创建或接收仿真视图，生成软体材质张量视图并更新 `count`。
>     
> - `post_reset() -> None`  
>     仿真重置时的占位钩子，预留用于恢复初始状态（当前未实现）。
>     
> - `_invalidate_physics_handle_callback(event) -> None`  
>     Timeline 停止事件回调，清空过期 `_physics_view`。
>     

> **状态检查**
> 
> - `is_physics_handle_valid() -> bool`  
>     判断物理视图是否已有效创建。
>     
> - `is_valid(indices=None) -> bool`  
>     验证指定或全部 Prim 路径当前是否存在于 USD Stage。
>     

> **批量属性读写**  
> _每组方法接收可选 `indices`，`get_*` 另有 `clone` 参数。编辑模式下操作 USD API，仿真模式下操作张量视图。_

> - **动态摩擦**
>     
>     - `set_dynamic_frictions(values, indices=None) -> None`
>         
>     - `get_dynamic_frictions(indices=None, clone=True) -> Tensor/ndarray`
>         
> - **弹性阻尼**
>     
>     - `set_elasticity_dampings(values, indices=None) -> None`
>         
>     - `get_elasticity_dampings(indices=None, clone=True) -> Tensor/ndarray`
>         
> - **阻尼尺度**
>     
>     - `set_damping_scales(values, indices=None) -> None`
>         
>     - `get_damping_scales(indices=None, clone=True) -> Tensor/ndarray`
>         
> - **泊松比**
>     
>     - `set_poissons_ratios(values, indices=None) -> None`
>         
>     - `get_poissons_ratios(indices=None, clone=True) -> Tensor/ndarray`
>         
> - **杨氏模量**
>     
>     - `set_youngs_moduli(values, indices=None) -> None`
>         
>     - `get_youngs_moduli(indices=None, clone=True) -> Tensor/ndarray`



#### *OmniGlass*
`OmniGlass` 是基于 `VisualMaterial` 的子类，用于在 USD 场景中创建或包装一个 MDL 玻璃材质（`OmniGlass.mdl`），并暴露对“玻璃色”（`glass_color`）、折射率（`glass_ior`）、厚度（`depth`）和“薄壁”属性的便捷读写接口。

---

**初始化参数**:

|参数名|类型|说明|
|---|---|---|
|`prim_path`|`str`|USD场景中材质的路径（如 `/World/GlassMaterial`）。|
|`name`|`str`|材质的自定义名称（默认 `"omni_glass"`）。|
|`shader`|`Optional[UsdShade.Shader]`|可选的现有着色器Prim（若未提供，自动创建或查找）。|
|`color`|`Optional[np.ndarray]`|初始颜色（RGB格式，如 `np.array([0.8, 0.9, 1.0])`）。|
|`ior`|`Optional[float]`|初始折射率（默认无）。|
|`depth`|`Optional[float]`|初始深度值（默认无）。|
|`thin_walled`|`Optional[bool]`|是否启用薄壁模式（默认无）。|

**全部接口**

> **构造与初始化**
> 
> 
```python
OmniGlass(    
	prim_path: str,    
	name: str = "omni_glass",     
	shader: Optional[UsdShade.Shader] = None,     
	color: Optional[np.ndarray] = None,     
	ior: Optional[float] = None,     
	depth: Optional[float] = None,     
	thin_walled: Optional[bool] = None 
)
```

> - **功能**：
>     
>     1. 若已有指定路径的材质 Prim，则直接包装；否则通过 `CreateAndBindMdlMaterialFromLibrary("OmniGlass.mdl")` 创建并移动到目标路径。
>         
>     2. 查找或验证子节点 `shader`，将其作为 MDL Shader 绑定到材质上。
>         
>     3. 根据可选参数 `color`, `ior`, `depth`, `thin_walled`，调用对应 `set_*` 方法初始化属性。
>         
>     4. 最终在材质上创建 `surface` 输出，接入 Shader。
>         

> **属性写入方法**
> 
> - `set_color(color: np.ndarray) -> None`
>     
>     - 在 Shader 上创建或更新 `glass_color` 输入（Color3f）。
>         
> - `set_ior(ior: float) -> None`
>     
>     - 创建或更新**折射率** `glass_ior` 输入（Float）。
>         
> - `set_depth(depth: float) -> None`
>     
>     - 创建或更新**深度（玻璃的视觉厚度）** `depth` 输入（Float）。
>         
> - `set_thin_walled(thin_walled: bool) -> None`
>     
>     - 创建或更新**薄壁模式** (`thin_walled`): 是否启用薄壁效果（用于模拟薄玻璃）
>     - `thin_walled` 输入（Bool）。
>         

> **属性读取方法**
> 
> - `get_color() -> Optional[np.ndarray]`
>     
>     - 返回当前 `glass_color`，如果未设置则打印警告并返回 `None`。
>         
> - `get_ior() -> Optional[float]`
>     
>     - 类似地，读取 `glass_ior`。
>         
> - `get_depth() -> Optional[float]`
>     
>     - 读取 `depth`。
>         
> - `get_thin_walled() -> Optional[bool]`
>     
>     - 读取 `thin_walled`。
>

---
#### *OmniPBR*

`OmniPBR` 是基于 `VisualMaterial` 的子类，用于在 USD 场景中创建或包装一个通用 PBR MDL 材质（`OmniPBR.mdl`），并暴露对常见物理参数和贴图变换的便捷读写接口。

---

 ***关键特性

|英文参数|中文翻译|说明|
|---|---|---|
|`diffuse_color_constant`|基础颜色（漫反射常量）|控制材质的 RGB 漫反射颜色值|
|`diffuse_texture`|漫反射贴图|支持加载外部贴图文件（Asset 路径）|
|`texture_scale`|贴图缩放|UV 缩放比例 `[sx, sy]`|
|`texture_translate`|贴图平移|UV 平移偏移 `[tx, ty]`|
|`reflection_roughness_constant`|反射粗糙度|控制表面反射粗糙程度（范围 0.0–1.0）|
|`metallic_constant`|金属度|控制材质金属性质（范围 0.0–1.0）|
|`project_uvw`|UV 投影开关|启用/禁用自动将贴图投影到物体 UVW 坐标|

- **自动化初始化**：若目标路径不存在 Material 或 Shader Prim，自动创建并绑定 `OmniPBR.mdl`。
    
- **复用能力**：检测并复用场景中已存在的材质或着色器。
    
- **默认值**：初始化时自动设定 `reflection_roughness_constant = 0.5`，`project_uvw = True`。
    
- **错误处理**：调用 `get_*` 时，若对应输入未设置，则打印警告并返回 `None`。
    

---

 **初始化参数**

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|USD 场景中材质的路径（如 `/World/Materials/MyPBR`）|
|`name`|`str`|材质实例名称，默认 `"omni_pbr"`|
|`shader`|`Optional[UsdShade.Shader]`|可复用的现有着色器 Prim；未提供时新建一个|
|`texture_path`|`Optional[str]`|初始漫反射贴图文件路径（Asset），如 `"textures/wood.png"`|
|`texture_scale`|`Optional[np.ndarray]`|初始贴图缩放比例，格式 `np.array([sx, sy])`|
|`texture_translate`|`Optional[np.ndarray]`|初始贴图平移偏移，格式 `np.array([tx, ty])`|
|`color`|`Optional[np.ndarray]`|初始基础颜色，RGB 格式 `np.array([r, g, b])`|

---

 ***构造与初始化


```python
OmniPBR(     
		prim_path: str,     
		name: str = "omni_pbr",     
		shader: Optional[UsdShade.Shader] = None,     
		texture_path: Optional[str] = None,     
		texture_scale: Optional[np.ndarray] = None,     
		texture_translate: Optional[np.ndarray] = None,     
		color: Optional[np.ndarray] = None, 
		)
```

1. **包装或新建材质**
    
    - 如果指定路径已有 `UsdShade.Material`，则包装之并打印日志；否则在当前 Stage 上调用 `UsdShade.Material.Define` 新建。
        
2. **查找或创建 Shader**
    
    - 若目标路径下已存在 `shader` 子 Prim，复用之；否则 `UsdShade.Shader.Define` 创建一个新的。
        
    - 调用 `CreateIdAttr("OmniPBR")` 指定 MDL 着色器类型；插入 `out` 输出。
        
3. **创建输入与输出**
    
    - 在 Shader 上批量调用 `CreateInput`：
        
        - `diffuse_color_constant` (Color3f)
            
        - `reflection_roughness_constant` (Float)
            
        - `metallic_constant` (Float)
            
        - `diffuse_texture` (Asset)
            
        - `project_uvw` (Bool)
            
        - `texture_scale` (Float2)
            
        - `texture_translate` (Float2)
            
    - 在 Material 上创建 `surface`、`volume`、`displacement` 输出，并链接到 Shader 的 `out`。
        
    - 设置 Shader 的实现源为外部 Asset `OmniPBR.mdl`。
        
4. **参数初始化**
    
    - 若提供了 `color`、`texture_path`、`texture_scale`、`texture_translate`，依次调用对应的 `set_*` 方法。
        
    - 默认执行 `set_project_uvw(True)` 与 `set_reflection_roughness(0.5)`。
        

---

***属性写入方法（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_color(color: np.ndarray) -> None`|`color` (RGB 数组)|设置 `diffuse_color_constant`（漫反射基础颜色）|
|`set_texture(path: str) -> None`|`path` (Asset 路径)|设置 `diffuse_texture`（漫反射贴图文件）|
|`set_texture_scale(x: float, y: float) -> None`|`x, y` (缩放因子)|设置 `texture_scale`（UV 缩放比例）|
|`set_texture_translate(x: float, y: float) -> None`|`x, y` (平移偏移)|设置 `texture_translate`（UV 平移偏移）|
|`set_project_uvw(flag: bool) -> None`|`flag` (布尔)|设置 `project_uvw`（启用/禁用 UV 投影）|
|`set_reflection_roughness(amount: float) -> None`|`amount` (0.0–1.0)|设置 `reflection_roughness_constant`（反射粗糙度）|
|`set_metallic_constant(amount: float) -> None`|`amount` (0.0–1.0)|设置 `metallic_constant`（金属度）|

---

****属性读取方法（Getters）

| 方法名                                               | 返回类型                  | 说明                                                  |     |
| ------------------------------------------------- | --------------------- | --------------------------------------------------- | --- |
| `get_color() -> Optional[np.ndarray]`             | `np.ndarray` 或 `None` | 获取 `diffuse_color_constant`，未设置时警告并返回 `None`        |     |
| `get_texture() -> Optional[str]`                  | `str` 或 `None`        | 获取 `diffuse_texture` 路径，未设置时警告并返回 `None`            |     |
| `get_texture_scale() -> Optional[np.ndarray]`     | `np.ndarray` 或 `None` | 获取 `texture_scale`，未设置时警告并返回 `None`                 |     |
| `get_texture_translate() -> Optional[np.ndarray]` | `np.ndarray` 或 `None` | 获取 `texture_translate`，未设置时警告并返回 `None`             |     |
| `get_project_uvw() -> Optional[bool]`             | `bool` 或 `None`       | 获取 `project_uvw` 状态，未设置时警告并返回 `None`                |     |
| `get_reflection_roughness() -> Optional[float]`   | `float` 或 `None`      | 获取 `reflection_roughness_constant`，未设置时警告并返回 `None` |     |
| `get_metallic_constant() -> Optional[float]`      | `float` 或 `None`      | 获取 `metallic_constant`，未设置时警告并返回 `None`             |     |


#### *ParticleMaterial*

`ParticleMaterial` 是对基于位置的动力学（PBD）粒子材质的封装，用于模拟流体、布料和充气体。它统一应用于指定粒子系统下所有关联对象，通过 `ParticleMaterialView` 实现后端张量化读写。

---

***关键特性

|英文属性|中文翻译|说明|
|---|---|---|
|`friction`|摩擦系数|粒子与刚体／软体交互的摩擦系数|
|`particle_friction_scale`|粒子摩擦缩放|对固体粒子间摩擦的缩放系数|
|`damping`|速度阻尼|全局速度阻尼系数|
|`viscosity`|黏度|流体粒子的黏度|
|`vorticity_confinement`|涡度约束|为防止数值能量损失，向流体粒子添加涡旋效果|
|`surface_tension`|表面张力|流体粒子的表面张力|
|`cohesion`|内聚力|流体粒子间的内聚力|
|`adhesion`|粘附力|粒子（固体或流体）与刚体／软体的粘附作用|
|`particle_adhesion_scale`|粒子粘附缩放|对固体粒子间粘附的缩放系数|
|`adhesion_offset_scale`|粘附偏移缩放|粘附作用失效的偏移距离相对于静止偏移的比例|
|`gravity_scale`|重力缩放|重力加速度缩放因子，可反向模拟轻质充气体|
|`lift`|升力系数|用于布料/充气体的基本空气动力学升力模型|
|`drag`|阻力系数|用于布料/充气体的基本空气动力学阻力模型|

- **自动创建**：若 `prim_path` 下无 `UsdShade.Material`，自动定义一个新材质。
    
- **后端选择**：优先使用 `SimulationContext` 中的 Torch/NumPy 张量后端。
    
- **单一材质**：每个粒子系统仅支持一个材质实例，应用于所有关联对象。
    
- **警告机制**：输入值超出合理范围或访问未设置属性时打印日志警告。
    

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|目标 Material Prim 路径（如 `/World/Particles/FluidMat`）|
|`name`|`Optional[str]`|材质实例名称（默认 `"particle_material"`）|
|`friction`|`Optional[float]`|摩擦系数|
|`particle_friction_scale`|`Optional[float]`|粒子摩擦缩放|
|`damping`|`Optional[float]`|速度阻尼|
|`viscosity`|`Optional[float]`|黏度|
|`vorticity_confinement`|`Optional[float]`|涡度约束|
|`surface_tension`|`Optional[float]`|表面张力|
|`cohesion`|`Optional[float]`|内聚力|
|`adhesion`|`Optional[float]`|粘附力|
|`particle_adhesion_scale`|`Optional[float]`|粒子粘附缩放|
|`adhesion_offset_scale`|`Optional[float]`|粘附偏移缩放|
|`gravity_scale`|`Optional[float]`|重力缩放|
|`lift`|`Optional[float]`|升力系数|
|`drag`|`Optional[float]`|阻力系数|

---

 ***构造与初始化

```python
ParticleMaterial(     
				prim_path: str,     
				name: Optional[str] = "particle_material",     
				friction: Optional[float] = None,     
				particle_friction_scale: Optional[float] = None,     
				damping: Optional[float] = None,     
				viscosity: Optional[float] = None,     
				vorticity_confinement: Optional[float] = None,     
				surface_tension: Optional[float] = None,     
				cohesion: Optional[float] = None,     
				adhesion: Optional[float] = None,     
				particle_adhesion_scale: Optional[float] = None,     
				adhesion_offset_scale: Optional[float] = None,     
				gravity_scale: Optional[float] = None,     
				lift: Optional[float] = None,     
				drag: Optional[float] = None, 
				)
```

1. 获取或定义 `UsdShade.Material` Prim，并记录 `_prim_path`、`_prim`、`_material`。
    
2. 选择 `SimulationContext` 后端（Torch/NumPy），并将所有非 `None` 参数转换为张量。
    
3. 调用 `ParticleMaterialView`，传入路径表达式与各参数张量，实现批量物理视图封装。
    

---

***生命周期方法

|方法名|返回类型|说明|
|---|---|---|
|`initialize(physics_sim_view=None) -> None`|`None`|初始化底层 `ParticleMaterialView`，创建物理视图|
|`is_valid() -> bool`|`bool`|检查当前 prim 路径在 Stage 中是否存在|
|`post_reset() -> None`|`None`|在仿真重置时恢复到初始状态|

---

***属性写入方法（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_friction(value: float) -> None`|`value` (≥0)|设置摩擦系数|
|`set_particle_friction_scale(value: float) -> None`|`value` (≥0)|设置粒子摩擦缩放|
|`set_damping(value: float) -> None`|`value` (≥0)|设置速度阻尼|
|`set_viscosity(value: float) -> None`|`value` (≥0)|设置流体黏度|
|`set_vorticity_confinement(value: float) -> None`|`value` (≥0)|设置涡度约束|
|`set_surface_tension(value: float) -> None`|`value` (≥0)|设置流体表面张力|
|`set_cohesion(value: float) -> None`|`value` (≥0)|设置流体内聚力|
|`set_adhesion(value: float) -> None`|`value` (≥0)|设置粒子与刚体/软体粘附力|
|`set_particle_adhesion_scale(value: float) -> None`|`value` (≥0)|设置固体粒子间粘附缩放|
|`set_adhesion_offset_scale(value: float) -> None`|`value` (≥0)|设置粘附偏移失效比例|
|`set_gravity_scale(value: float) -> None`|`value`|设置重力缩放（可为负值反向重力）|
|`set_lift(value: float) -> None`|`value` (≥0)|设置升力系数|
|`set_drag(value: float) -> None`|`value` (≥0)|设置阻力系数|

---

***属性读取方法（Getters）

|方法名|返回类型|说明|
|---|---|---|
|`get_friction() -> float`|`float`|获取当前摩擦系数|
|`get_particle_friction_scale() -> float`|`float`|获取当前粒子摩擦缩放|
|`get_damping() -> float`|`float`|获取当前速度阻尼|
|`get_viscosity() -> float`|`float`|获取当前流体黏度|
|`get_vorticity_confinement() -> float`|`float`|获取当前涡度约束|
|`get_surface_tension() -> float`|`float`|获取当前流体表面张力|
|`get_cohesion() -> float`|`float`|获取当前流体内聚力|
|`get_adhesion() -> float`|`float`|获取当前粒子粘附力|
|`get_particle_adhesion_scale() -> float`|`float`|获取当前固体粒子粘附缩放|
|`get_adhesion_offset_scale() -> float`|`float`|获取当前粘附偏移失效比例|
|`get_gravity_scale() -> float`|`float`|获取当前重力缩放|
|`get_lift() -> float`|`float`|获取当前升力系数|
|`get_drag() -> float`|`float`|获取当前阻力系数|

#### *ParticleMaterialView*

`ParticleMaterialView` 用于在 Isaac Sim 中批量管理基于位置动力学（PBD）的粒子材质属性。它接受一个 Prim 路径正则表达式，封装所有匹配的材质 Prim，并在编辑模式（USD API）与仿真模式（PhysX 张量视图）下，统一读写摩擦、阻尼、黏度、升力等多种物理属性。

---

***关键特性

|英文属性|中文翻译|说明|
|---|---|---|
|`frictions`|摩擦系数|粒子与刚体/软体交互的摩擦系数|
|`particle_friction_scales`|粒子摩擦缩放|对固体粒子间摩擦的缩放系数|
|`dampings`|速度阻尼|全局速度阻尼系数|
|`viscosities`|黏度|流体粒子的黏度|
|`vorticity_confinements`|涡度约束|为防止数值能量损失，向流体粒子添加涡旋效应|
|`surface_tensions`|表面张力|流体粒子的表面张力|
|`cohesions`|内聚力|流体粒子间的内聚力|
|`adhesions`|粘附力|粒子（固体或流体）与刚体/软体的粘附作用|
|`particle_adhesion_scales`|粒子粘附缩放|对固体粒子间粘附的缩放系数|
|`adhesion_offset_scales`|粘附偏移缩放|粘附作用失效的偏移距离相对于静止距离的比例|
|`gravity_scales`|重力缩放|重力加速度缩放因子|
|`lifts`|升力系数|布料/充气体的基本空气动力学升力模型|
|`drags`|阻力系数|布料/充气体的基本空气动力学阻力模型|

- **编辑 vs 仿真**：在 Timeline 停止时，通过 USD API 对单个 Prim 属性读写；在仿真运行时，使用 PhysX 张量视图一次性交互全体数据。
    
- **后台切换**：优先使用 `SimulationContext` 的 Torch 张量后端，否则回退到 NumPy。
    
- **正则匹配**：支持用正则表达式批量匹配多个材质 Prim，并统一操作。
    
- **警告与异常**：正则匹配不出 Prim 抛异常；访问未设置属性时打印警告；索引越界或输入非法值亦会记录错误。
    

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_paths_expr`|`str`|匹配材质 Prim 的路径正则表达式（如 `"/World/Particles/.*Material"`）|
|`name`|`str`|视图名称，作为 Scene 的键（默认 `"particle_material_view"`）|
|`frictions`|`Optional[np.ndarray \| torch.Tensor]`|摩擦系数张量，形状 `(N,)`|
|`particle_friction_scales`|`Optional[np.ndarray \| torch.Tensor]`|粒子摩擦缩放张量，形状 `(N,)`|
|`dampings`|`Optional[np.ndarray \| torch.Tensor]`|速度阻尼张量，形状 `(N,)`|
|`viscosities`|`Optional[np.ndarray \| torch.Tensor]`|黏度张量，形状 `(N,)`|
|`vorticity_confinements`|`Optional[np.ndarray \| torch.Tensor]`|涡度约束张量，形状 `(N,)`|
|`surface_tensions`|`Optional[np.ndarray \| torch.Tensor]`|表面张力张量，形状 `(N,)`|
|`cohesions`|`Optional[np.ndarray \| torch.Tensor]`|内聚力张量，形状 `(N,)`|
|`adhesions`|`Optional[np.ndarray \| torch.Tensor]`|粘附力张量，形状 `(N,)`|
|`particle_adhesion_scales`|`Optional[np.ndarray \| torch.Tensor]`|粒子粘附缩放张量，形状 `(N,)`|
|`adhesion_offset_scales`|`Optional[np.ndarray \| torch.Tensor]`|粘附偏移缩放张量，形状 `(N,)`|
|`gravity_scales`|`Optional[np.ndarray \| torch.Tensor]`|重力缩放张量，形状 `(N,)`|
|`lifts`|`Optional[np.ndarray \| torch.Tensor]`|升力系数张量，形状 `(N,)`|
|`drags`|`Optional[np.ndarray \| torch.Tensor]`|阻力系数张量，形状 `(N,)`|

---


***生命周期方法

|方法名|返回类型|说明|
|---|---|---|
|`initialize(physics_sim_view=None) -> None`|`None`|创建或接收 `SimulationView`，并生成 PhysX 张量视图|
|`is_physics_handle_valid() -> bool`|`bool`|判断物理视图是否已初始化|
|`is_valid(indices=None) -> bool`|`bool`|验证所有（或指定）Prim 路径当前在 Stage 中是否有效|
|`post_reset() -> None`|`None`|在仿真重置时恢复到初始状态（当前空实现）|

---

***属性写入方法（Setters）

|方法名|参数|说明|
|---|---|---|
|`set_frictions(values, indices=None)`|`values` `(M,)` 张量|设置摩擦系数；`indices` 指定要操作的 Prim 下标，默认全部|
|`set_particle_friction_scales(values,...)`|同上|设置粒子摩擦缩放|
|`set_dampings(values,...)`|同上|设置速度阻尼|
|`set_viscosities(values,...)`|同上|设置黏度|
|`set_vorticity_confinements(values,...)`|同上|设置涡度约束|
|`set_surface_tensions(values,...)`|同上|设置表面张力|
|`set_cohesions(values,...)`|同上|设置内聚力|
|`set_adhesions(values,...)`|同上|设置粘附力|
|`set_particle_adhesion_scales(values,...)`|同上|设置粒子粘附缩放|
|`set_adhesion_offset_scales(values,...)`|同上|设置粘附偏移缩放|
|`set_gravity_scales(values,...)`|同上|设置重力缩放|
|`set_lifts(values,...)`|同上|设置升力系数|
|`set_drags(values,...)`|同上|设置阻力系数|

---

***属性读取方法（Getters）

|方法名|返回类型|说明|
|---|---|---|
|`get_frictions(indices=None, clone=True)`|`Tensor` `(M,)`|获取摩擦系数；`clone=False` 时返回内部缓冲切片|
|`get_particle_friction_scales(...)`|`Tensor` `(M,)`|获取粒子摩擦缩放|
|`get_dampings(...)`|`Tensor` `(M,)`|获取速度阻尼|
|`get_viscosities(...)`|`Tensor` `(M,)`|获取黏度|
|`get_vorticity_confinements(...)`|`Tensor` `(M,)`|获取涡度约束|
|`get_surface_tensions(...)`|`Tensor` `(M,)`|获取表面张力|
|`get_cohesions(...)`|`Tensor` `(M,)`|获取内聚力|
|`get_adhesions(...)`|`Tensor` `(M,)`|获取粘附力|
|`get_particle_adhesion_scales(...)`|`Tensor` `(M,)`|获取粒子粘附缩放|
|`get_adhesion_offset_scales(...)`|`Tensor` `(M,)`|获取粘附偏移缩放|
|`get_gravity_scales(...)`|`Tensor` `(M,)`|获取重力缩放|
|`get_lifts(...)`|`Tensor` `(M,)`|获取升力系数|
|`get_drags(...)`|`Tensor` `(M,)`|获取阻力系数|
#### _PhysicsMaterial_

`PhysicsMaterial` 用于在 Isaac Sim 中对单个物理材质（UsdShade.Material + UsdPhysics.MaterialAPI）进行定义与管理。它接收一个 Prim 路径，自动创建或查询对应的材质 Prim，并通过 MaterialAPI 在编辑与仿真模式下，对静态摩擦、动态摩擦、恢复系数等属性进行读写。

---

***关键特性

|英文属性|中文翻译|说明|
|---|---|---|
|`prim_path`|Prim 路径|材质在 USD Stage 中的唯一标识路径|
|`static_friction`|静态摩擦|材质在接触开始时的摩擦系数|
|`dynamic_friction`|动态摩擦|材质在运动过程中持续的摩擦系数|
|`restitution`|恢复系数|材质碰撞后的弹性恢复比例|
|隐式 MaterialAPI|自动应用|自动为 Prim 应用或查询 UsdPhysics.MaterialAPI 接口|
|方法对称性|一致接口|`set_xxx(value)` 与 `get_xxx() -> float?` 读写对称，用法简洁|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要定义或查找的材质 Prim 路径|
|`name`|`str`|材质名称，用于日志或上下文（默认 `"physics_material"`）|
|`static_friction`|`float?`|初始静态摩擦（可选）|
|`dynamic_friction`|`float?`|初始动态摩擦（可选）|
|`restitution`|`float?`|初始恢复系数（可选）|

---


***主要方法（Setters）

|方法|参数|说明|
|---|---|---|
|`set_static_friction(f: float)`|`f`|创建或更新 StaticFrictionAttr 并赋值|
|`set_dynamic_friction(f: float)`|`f`|创建或更新 DynamicFrictionAttr 并赋值|
|`set_restitution(r: float)`|`r`|创建或更新 RestitutionAttr 并赋值|

---

***主要方法（Getters）

| 方法                                 | 返回类型           | 说明                                       |
| ---------------------------------- | -------------- | ---------------------------------------- |
| `get_static_friction() -> float?`  | `float`/`None` | 若属性未设置，`log_warn` 并返回 `None`；否则返回当前静态摩擦值 |
| `get_dynamic_friction() -> float?` | `float`/`None` | 同上，针对动态摩擦                                |
| `get_restitution() -> float?`      | `float`/`None` | 同上，针对恢复系数                                |

---

#### _PreviewSurface_
`PreviewSurface` 类专门用于设置物体的 **视觉外观属性**，属于典型的材质系统组件。
`PreviewSurface` 是继承自 `VisualMaterial` 的可视化材质封装，用于在 Isaac Sim 中快速创建和管理基于 `UsdPreviewSurface` 的渲染着色器节点。它接受一个 Prim 路径，自动定义或查询对应的材质与 Shader Prim，并支持颜色、粗糙度、金属度等属性的初始化与读写。

---

***关键特性

|英文属性|中文翻译|说明|
|---|---|---|
|`prim_path`|Prim 路径|材质在 USD Stage 中的唯一标识路径|
|`shader`|着色器|可自定义的 `UsdShade.Shader`，默认为 `UsdPreviewSurface`|
|`color`|颜色|材质的漫反射颜色（RGB 三元组）|
|`roughness`|粗糙度|控制表面微观细节对光照的漫反射程度|
|`metallic`|金属度|控制材质金属感（非金属时为 0.0，纯金属时为 1.0）|
|自动连接|Surface 输出|在构造时会将材质的 SurfaceOutput 自动连接到 Shader 的 “surface” 插口|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要定义或查找的 Material Prim 路径|
|`name`|`str`|材质名称，用于日志或上下文（默认 `"preview_surface"`）|
|`shader`|`Optional[UsdShade.Shader]`|预先创建好的 Shader 对象，若为 `None` 则自动在 `${prim_path}/shader` 创建或查询|
|`color`|`Optional[np.ndarray]`|初始漫反射颜色，形状 `(3,)`，值域 [0,1]|
|`roughness`|`Optional[float]`|初始粗糙度属性，值域 [0,1]|
|`metallic`|`Optional[float]`|初始金属度属性，值域 [0,1]|

---


***主要方法（Setters）

|方法|参数|说明|
|---|---|---|
|`set_color(color: np.ndarray)`|`(3,)` 浮点数组|创建或更新 `diffuseColor` 输入并赋值|
|`set_roughness(roughness: float)`|`float`|创建或更新 `roughness` 输入并赋值|
|`set_metallic(metallic: float)`|`float`|创建或更新 `metallic` 输入并赋值|

---

***主要方法（Getters）

|方法|返回类型|说明|
|---|---|---|
|`get_color() -> np.ndarray?`|`(3,)` 浮点数组/None|若未设置则 `log_warn` 并返回 `None`，否则返回当前漫反射颜色|
|`get_roughness() -> float?`|`float`/None|若未设置则 `log_warn` 并返回 `None`，否则返回当前粗糙度|
|`get_metallic() -> float?`|`float`/None|若未设置则 `log_warn` 并返回 `None`，否则返回当前金属度|

---

#### _VisualMaterial_

`VisualMaterial` 类用于管理视觉材质的基本属性和操作，作为其他材质类的基类。

---

 ***关键特性

|英文属性|中文翻译|说明|
|---|---|---|
|`material`|材质|关联的 `UsdShade.Material` 对象|
|`shaders_list`|着色器列表|关联的 `UsdShade.Shader` 对象列表|
|`name`|名称|材质的名称|
|`prim_path`|Prim 路径|材质在 USD Stage 中的唯一标识路径|
|`prim`|Prim 对象|与材质关联的 `Usd.Prim` 实例|

---

***构造函数参数

|参数|类型|说明|
|---|---|---|
|`name`|`str`|材质的名称|
|`prim_path`|`str`|材质在 USD Stage 中的路径|
|`prim`|`Usd.Prim`|与材质关联的 `Usd.Prim` 实例|
|`shaders_list`|`List[UsdShade.Shader]`|关联的 `UsdShade.Shader` 对象列表|
|`material`|`UsdShade.Material`|关联的 `UsdShade.Material` 对象|

---

***主要属性

|属性|返回类型|说明|
|---|---|---|
|`material`|`UsdShade.Material`|返回关联的材质对象。|
|`shaders_list`|`List[UsdShade.Shader]`|返回关联的着色器对象列表。|
|`name`|`str`|返回材质的名称。|
|`prim_path`|`str`|返回材质在 USD Stage 中的路径。|
|`prim`|`Usd.Prim`|返回与材质关联的 `Usd.Prim` 实例。|

---

***主要方法

|方法|返回类型|说明|
|---|---|---|
|`material`|`UsdShade.Material`|返回关联的材质对象。|
|`shaders_list`|`List[UsdShade.Shader]`|返回关联的着色器对象列表。|
|`name`|`str`|返回材质的名称。|
|`prim_path`|`str`|返回材质在 USD Stage 中的路径。|
|`prim`|`Usd.Prim`|返回与材质关联的 `Usd.Prim` 实例。|


### Object
#### _DynamicCapsule_

`DynamicCapsule` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞和刚体动力学属性的胶囊体（Capsule），同时支持可视化和物理材质的配置。

---

***关键特性

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleRigidPrim`|单刚体基类|提供质量、密度、线速度、角速度等刚体动力学属性。|
|`FixedCapsule`|固定胶囊基类|提供胶囊形状的可视化与碰撞体形状配置。|
|碰撞 (Collider API)|Collision|自动添加胶囊形状的碰撞几何。|
|刚体 (Rigid Body API)|Rigid Body|自动添加刚体动力学属性并注册到物理子系统。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`。|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 的路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一）。默认 `"dynamic_capsule"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`。不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`。不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`。不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`。不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见。默认 `True`。|
|`color`|`Optional[np.ndarray]`|胶囊可视部分颜色 `(3,)`。默认 50% 灰。|
|`radius`|`Optional[float]`|胶囊半径。默认不变。|
|`height`|`Optional[float]`|胶囊高度（圆柱部分长度）。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质。|
|`mass`|`Optional[float]`|质量 (kg)，若路径新建且不指定，则默认 `0.02 kg`。|
|`density`|`Optional[float]`|密度 (kg/m³)。|
|`linear_velocity`|`Optional[Sequence[float]]`|初始线速度 `(3,)`，世界坐标系。|
|`angular_velocity`|`Optional[Sequence[float]]`|初始角速度 `(3,)`，世界坐标系。|

---


***主要方法

`DynamicCapsule` 本身并未新增实例方法，所有功能均通过继承自父类提供。主要可用的方法包括：

| 方法                      | 来源类                                        | 说明                                           |
| ----------------------- | ------------------------------------------ | -------------------------------------------- |
| (`…`省略若干 Getter/Setter) | `FixedCapsule`[[Core(api)#_FixedCapsule_]] | 管理胶囊形状、可视与材料属性的方法。                           |
| (`…`省略若干 Getter/Setter) | `SingleRigidPrim`                          | 管理刚体动力学属性的方法，如 `set_mass()`、`set_velocity()` |

---
#### _DynamicCone_

`DynamicCone` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞和刚体动力学属性的圆锥体（Cone），同时支持可视化和物理材质的配置。

---

***关键特性

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleRigidPrim`|单刚体基类|提供质量、密度、线速度、角速度等刚体动力学属性。|
|`FixedCone`|固定圆锥基类|提供圆锥形状的可视化与碰撞体形状配置。|
|碰撞 (Collider API)|Collision|自动添加圆锥形状的碰撞几何。|
|刚体 (Rigid Body API)|Rigid Body|自动添加刚体动力学属性并注册到物理子系统。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`。|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 的路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一）。默认 `"dynamic_cone"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`。不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`。不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`。不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`。不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见。默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆锥可视部分颜色 `(3,)`。默认 50% 灰。|
|`radius`|`Optional[float]`|圆锥底面半径。默认不变。|
|`height`|`Optional[float]`|圆锥高度（从底面到顶点距离）。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质。|
|`mass`|`Optional[float]`|质量 (kg)，若路径新建且不指定，则默认 `0.02 kg`。|
|`density`|`Optional[float]`|密度 (kg/m³)。|
|`linear_velocity`|`Optional[Sequence[float]]`|初始线速度 `(3,)`，世界坐标系。|
|`angular_velocity`|`Optional[Sequence[float]]`|初始角速度 `(3,)`，世界坐标系。|

---


***主要方法

`DynamicCone` 本身未新增新的实例方法，所有功能均通过继承自父类提供。主要可用的方法包括：

| 方法                                             | 来源类                                  | 说明              |
| ---------------------------------------------- | ------------------------------------ | --------------- |
| （诸如 `set_radius`, `set_height`, `set_color`）   | `FixedCone`[[Core(api)#_FixedCone_]] | 管理圆锥几何和材质属性的方法。 |
| （诸如 `set_mass`, `set_velocity`, `get_density`） | `SingleRigidPrim`                    | 管理刚体动力学属性的方法。   |

----

#### _DynamicCuboid_

`DynamicCuboid` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞和刚体动力学属性的立方体/长方体（Cuboid），同时支持可视化和物理材质的配置。

---

***关键特性

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleRigidPrim`|单刚体基类|提供质量、密度、线速度、角速度等刚体动力学属性。|
|`FixedCuboid`|固定立方体基类|提供立方体/长方体形状的可视化与碰撞体形状配置。|
|碰撞 (Collider API)|Collision|自动添加立方体形状的碰撞几何。|
|刚体 (Rigid Body API)|Rigid Body|自动添加刚体动力学属性并注册到物理子系统。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`。|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 的路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一）。默认 `"dynamic_cube"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`。不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`。不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`。不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`。不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见。默认 `True`。|
|`color`|`Optional[np.ndarray]`|立方体可视部分颜色 `(3,)`。默认 50% 灰。|
|`size`|`Optional[float]`|立方体每条边的长度。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质。|
|`mass`|`Optional[float]`|质量 (kg)，若路径新建且不指定，则默认 `0.02 kg`。|
|`density`|`Optional[float]`|密度 (kg/m³)。|
|`linear_velocity`|`Optional[Sequence[float]]`|初始线速度 `(3,)`，世界坐标系。|
|`angular_velocity`|`Optional[Sequence[float]]`|初始角速度 `(3,)`，世界坐标系。|

---


***主要方法

`DynamicCuboid` 本身未新增新的实例方法，所有功能均通过继承自父类提供。主要可用的方法包括：

| 方法                                             | 来源类                                      | 说明               |
| ---------------------------------------------- | ---------------------------------------- | ---------------- |
| （诸如 `set_size`, `set_color`）                   | `FixedCuboid`[[Core(api)#_FixedCuboid_]] | 管理立方体几何和材质属性的方法。 |
| （诸如 `set_mass`, `set_velocity`, `get_density`） | `SingleRigidPrim`                        | 管理刚体动力学属性的方法。    |

---

#### _DynamicCylinder_

`DynamicCylinder` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞和刚体动力学属性的圆柱体（Cylinder），同时支持可视化和物理材质的配置。

---

***关键特性

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleRigidPrim`|单刚体基类|提供质量、密度、线速度、角速度等刚体动力学属性。|
|`FixedCylinder`|固定圆柱基类|提供圆柱形状的可视化与碰撞体形状配置。|
|碰撞 (Collider API)|Collision|自动添加圆柱形状的碰撞几何。|
|刚体 (Rigid Body API)|Rigid Body|自动添加刚体动力学属性并注册到物理子系统。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`。|

---

***初始化参数

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 的路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一）。默认 `"dynamic_cylinder"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`。不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`。不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`。不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`。不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见。默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆柱可视部分颜色 `(3,)`。默认 50% 灰。|
|`radius`|`Optional[float]`|圆柱底面半径。默认不变。|
|`height`|`Optional[float]`|圆柱高度（圆柱轴向长度）。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质。|
|`mass`|`Optional[float]`|质量 (kg)，若路径新建且不指定，则默认 `0.02 kg`。|
|`density`|`Optional[float]`|密度 (kg/m³)。|
|`linear_velocity`|`Optional[Sequence[float]]`|初始线速度 `(3,)`，世界坐标系。|
|`angular_velocity`|`Optional[Sequence[float]]`|初始角速度 `(3,)`，世界坐标系。|

---


***主要方法

`DynamicCylinder` 本身未新增新的实例方法，所有功能均通过继承自父类提供。主要可用的方法包括：

| 方法                                             | 来源类                                          | 说明              |
| ---------------------------------------------- | -------------------------------------------- | --------------- |
| （诸如 `set_radius`, `set_height`, `set_color`）   | `FixedCylinder`[[Core(api)#_FixedCylinder_]] | 管理圆柱几何和材质属性的方法。 |
| （诸如 `set_mass`, `set_velocity`, `get_density`） | `SingleRigidPrim`                            | 管理刚体动力学属性的方法。   |

---

#### _DynamicSphere_

`DynamicSphere` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞和刚体动力学属性的球体（Sphere），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleRigidPrim`|单刚体基类|提供质量、密度、线速度、角速度等刚体动力学属性。|
|`FixedSphere`|固定球体基类|提供球体形状的可视化与碰撞体形状配置。|
|碰撞 (Collider API)|Collision|自动添加球体形状的碰撞几何。|
|刚体 (Rigid Body API)|Rigid Body|自动添加刚体动力学属性并注册到物理子系统。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一）。默认 `"dynamic_sphere"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`。不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`。不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`。不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`。不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见。默认 `True`。|
|`color`|`Optional[np.ndarray]`|球体可视部分颜色 `(3,)`。默认 50 % 灰。|
|`radius`|`Optional[float]`|球体半径。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质。|
|`mass`|`Optional[float]`|质量 (kg)，若路径新建且不指定，则默认 `0.02 kg`。|
|`density`|`Optional[float]`|密度 (kg/m³)。|
|`linear_velocity`|`Optional[Sequence[float]]`|初始线速度 `(3,)`，世界坐标系。|
|`angular_velocity`|`Optional[Sequence[float]]`|初始角速度 `(3,)`，世界坐标系。|

---

_**主要方法**_

`DynamicSphere` 本身未新增新的实例方法，所有功能均通过继承自父类提供。主要可用的方法包括：

| 方法                                          | 来源类                                      | 说明              |
| ------------------------------------------- | ---------------------------------------- | --------------- |
| `set_radius`, `set_color` 等                 | `FixedSphere`[[Core(api)#_FixedSphere_]] | 管理球体几何和材质属性的方法。 |
| `set_mass`, `set_velocity`, `get_density` 等 | `SingleRigidPrim`                        | 管理刚体动力学属性的方法。   |


---

#### _FixedCapsule_

`FixedCapsule` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞体但**无刚体动力学**属性的胶囊体（Capsule），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`VisualCapsule`|可视胶囊基类|提供胶囊形状的可视化几何与基础属性。|
|碰撞 (Collider API)|Collision|通过 `SingleGeometryPrim.set_collision_enabled(True)` 自动启用胶囊碰撞几何。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`（仅影响碰撞接触特性）。|
|刚体动力学|—|**无**；该类对应“固定”几何，不参与 Rigid Body API 模块计算。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一），默认 `"fixed_capsule"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`，不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`，不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`，不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|胶囊可视部分颜色 `(3,)`，默认 50 % 灰。|
|`radius`|`Optional[float]`|胶囊半径，默认不变。|
|`height`|`Optional[float]`|胶囊高度（圆柱轴向长度），默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质（静摩擦 0.2、动摩擦 1.0、回弹 0.0）。|

---

_**主要方法**_

`FixedCapsule` 本身未新增实例方法，功能均通过继承或工具函数提供。常用方法包括：

| 方法                                      | 来源类                                 | 说明                              |
| --------------------------------------- | ----------------------------------- | ------------------------------- |
| `set_radius`, `set_height`, `set_color` | `VisualCapsule`[[#_VisualCapsule_]] | 管理胶囊几何尺寸与可视属性。                  |
| `apply_physics_material`                | `FixedCapsule`                      | 将指定 `PhysicsMaterial` 应用到胶囊碰撞体。 |
| `set_collision_enabled`                 | `SingleGeometryPrim`                | 启用或禁用碰撞几何（已在构造函数中默认启用）。         |

---

#### _FixedCone_

`FixedCone` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞体但**无刚体动力学**属性的圆锥体（Cone），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`VisualCone`|可视圆锥基类|提供圆锥形状的可视化几何与基础属性。|
|碰撞 (Collider API)|Collision|通过 `SingleGeometryPrim.set_collision_enabled(True)` 自动启用圆锥碰撞几何。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`（仅影响碰撞接触特性）。|
|刚体动力学|—|**无**；该类对应“固定”几何，不参与 Rigid Body API 模块计算。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一），默认 `"fixed_cone"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`，不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`，不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`，不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆锥可视部分颜色 `(3,)`，默认 50 % 灰。|
|`radius`|`Optional[float]`|圆锥底面半径，默认不变。|
|`height`|`Optional[float]`|圆锥高度（轴向长度），默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质（静摩擦 0.2、动摩擦 1.0、回弹 0.0）。|

---

_**主要方法**_

`FixedCone` 本身未新增实例方法，功能均通过继承或工具函数提供。常用方法包括：

| 方法                                      | 来源类                           | 说明                              |
| --------------------------------------- | ----------------------------- | ------------------------------- |
| `set_radius`, `set_height`, `set_color` | `VisualCone`[[#_VisualCone_]] | 管理圆锥几何尺寸与可视属性。                  |
| `apply_physics_material`                | `FixedCone`                   | 将指定 `PhysicsMaterial` 应用到圆锥碰撞体。 |
| `set_collision_enabled`                 | `SingleGeometryPrim`          | 启用或禁用碰撞几何（已在构造函数中默认启用）。         |

---
#### _FixedCuboid_

`FixedCuboid` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞体但**无刚体动力学**属性的立方体（Cube），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`VisualCuboid`|可视立方体基类|提供立方体形状的可视化几何与基础属性。|
|碰撞 (Collider API)|Collision|通过 `SingleGeometryPrim.set_collision_enabled(True)` 自动启用立方体碰撞几何。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`（仅影响碰撞接触特性）。|
|刚体动力学|—|**无**；该类对应“固定”几何，不参与 Rigid Body API 模块计算。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一），默认 `"fixed_cube"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`，不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`，不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`，不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|立方体可视部分颜色 `(3,)`，默认 50 % 灰。|
|`size`|`Optional[float]`|每个立方体边长。默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质（静摩擦 0.2、动摩擦 1.0、回弹 0.0）。|

---

_**主要方法**_

`FixedCuboid` 本身未新增实例方法，功能均通过继承或工具函数提供。常用方法包括：

| 方法                                                                    | 来源类                               | 说明                                              |
| --------------------------------------------------------------------- | --------------------------------- | ----------------------------------------------- |
| `set_size`, `set_color`                                               | `VisualCuboid`[[#_VisualCuboid_]] | 管理立方体几何尺寸与可视属性。                                 |
| `apply_physics_material`                                              | `FixedCuboid`                     | 将指定 `PhysicsMaterial` 应用到立方体碰撞体。                |
| `set_collision_enabled`                                               | `SingleGeometryPrim`              | 启用或禁用碰撞几何（已在构造函数中默认启用）。                         |
| `set_rest_offset`, `set_contact_offset`, `set_torsional_patch_radius` | `FixedCuboid`                     | 设置立方体的物理碰撞偏移和补丁半径（仅在 `physics_material` 设置时有效）。 |

---
#### _FixedCylinder_

`FixedCylinder` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞体但**无刚体动力学**属性的圆柱体（Cylinder），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`VisualCylinder`|可视圆柱基类|提供圆柱形状的可视化几何与基础属性。|
|碰撞 (Collider API)|Collision|通过 `SingleGeometryPrim.set_collision_enabled(True)` 自动启用圆柱碰撞几何。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`（仅影响碰撞接触特性）。|
|刚体动力学|—|**无**；该类对应“固定”几何，不参与 Rigid Body API 模块计算。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一），默认 `"fixed_cylinder"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`，不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`，不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`，不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆柱可视部分颜色 `(3,)`，默认 50 % 灰。|
|`radius`|`Optional[float]`|圆柱底面半径，默认不变。|
|`height`|`Optional[float]`|圆柱高度（轴向长度），默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质（静摩擦 0.2、动摩擦 1.0、回弹 0.0）。|

---

_**主要方法**_

`FixedCylinder` 本身未新增实例方法，功能均通过继承或工具函数提供。常用方法包括：

| 方法                                      | 来源类                                   | 说明                              |
| --------------------------------------- | ------------------------------------- | ------------------------------- |
| `set_radius`, `set_height`, `set_color` | `VisualCylinder`[[#_VisualCylinder_]] | 管理圆柱几何尺寸与可视属性。                  |
| `apply_physics_material`                | `FixedCylinder`                       | 将指定 `PhysicsMaterial` 应用到圆柱碰撞体。 |
| `set_collision_enabled`                 | `SingleGeometryPrim`                  | 启用或禁用碰撞几何（已在构造函数中默认启用）。         |

---
#### _FixedSphere_

`FixedSphere` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个具有碰撞体但**无刚体动力学**属性的球体（Sphere），同时支持可视化和物理材质的配置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`VisualSphere`|可视球体基类|提供球体形状的可视化几何与基础属性。|
|碰撞 (Collider API)|Collision|通过 `SingleGeometryPrim.set_collision_enabled(True)` 自动启用球体碰撞几何。|
|可视化材质|Visual Material|支持自定义或自动创建的 `VisualMaterial`。|
|物理材质|Physics Material|支持自定义或自动创建的 `PhysicsMaterial`（仅影响碰撞接触特性）。|
|刚体动力学|—|**无**；该类对应“固定”几何，不参与 Rigid Body API 模块计算。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 Prim 路径。|
|`name`|`str`|在 Scene 中使用的短名称（需唯一），默认 `"fixed_sphere"`。|
|`position`|`Optional[np.ndarray]`|世界坐标系位置 `(3,)`，不指定则保持原状。|
|`translation`|`Optional[np.ndarray]`|本地坐标系平移 `(3,)`，不指定则保持原状。|
|`orientation`|`Optional[np.ndarray]`|四元数方向 `(w, x, y, z)` `(4,)`，不指定则保持原状。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|是否在渲染中可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|球体可视部分颜色 `(3,)`，默认 50% 灰。|
|`radius`|`Optional[float]`|球体半径，默认不变。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质，若不指定则自动创建默认材质。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质，若不指定则自动创建默认材质（静摩擦 0.2、动摩擦 1.0、回弹 0.0）。|

---

_**主要方法**_

`FixedSphere` 本身未新增实例方法，功能均通过继承或工具函数提供。常用方法包括：

| 方法                        | 来源类                               | 说明                              |
| ------------------------- | --------------------------------- | ------------------------------- |
| `set_radius`, `set_color` | `VisualSphere`[[#_VisualSphere_]] | 管理球体几何尺寸与可视属性。                  |
| `apply_physics_material`  | `FixedSphere`                     | 将指定 `PhysicsMaterial` 应用到球体碰撞体。 |
| `set_collision_enabled`   | `SingleGeometryPrim`              | 启用或禁用碰撞几何（已在构造函数中默认启用）。         |

---
#### _GroundPlane_

`GroundPlane` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个地面平面（Ground Plane），支持可视化和物理碰撞配置，但不参与刚体动力学仿真。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`PhysicsSchemaTools.addGroundPlane`|添加地面平面|在 USD 舞台上以指定法线和平面大小创建碰撞几何。|
|`SingleXFormPrim`|变换 Prim 封装|管理平面的位姿（位置、方向、缩放）和可视化材质。|
|`SingleGeometryPrim`|碰撞 Prim 封装|管理平面的碰撞几何和物理材质。|
|可视化材质|Visual Material|支持 `PreviewSurface` 或用户提供的 `VisualMaterial`，可自定义颜色或使用默认 50% 灰。|
|物理材质|Physics Material|支持用户提供或默认创建的 `PhysicsMaterial`（静摩擦、动摩擦、回弹可配置）。|
|重置与初始化|initialize/post_reset|提供 `initialize`、`post_reset` 方法，自动在世界重置（world.reset）时恢复默认状态。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径，例如 `"/World/GroundPlane"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"ground_plane"`。|
|`size`|`Optional[float]`|平面边长（单边），默认 `50.0`（按舞台单位自动换算）。|
|`z_position`|`Optional[float]`|平面的 Z 轴位置，默认 `0.0`。|
|`scale`|`Optional[np.ndarray]`|本地缩放 `(3,)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|可视平面颜色 `(3,)`，默认 `[0.5,0.5,0.5]`。|
|`physics_material`|`Optional[PhysicsMaterial]`|物理材质对象，若不指定则按静摩擦 `0.5`、动摩擦 `0.5`、回弹 `0.8` 创建默认材质。|
|`visual_material`|`Optional[VisualMaterial]`|可视材质对象，若不指定则使用 `PreviewSurface` 并应用 `color`；用户也可直接传入自定义 `VisualMaterial`。|

---

_**主要属性**_

|属性|返回类型|说明|
|---|---|---|
|`prim_path`|`str`|地面平面的 USD Prim 路径。|
|`name`|`str`|创建时指定的短名称。|
|`prim`|`Usd.Prim`|底层 USD Prim 对象。|
|`xform_prim`|`SingleXFormPrim`|管理可视与变换的封装对象。|
|`collision_geometry_prim`|`SingleGeometryPrim`|管理碰撞和物理材质的封装对象。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`initialize(physics_sim_view=None)`|创建或绑定到 PhysX 张量仿真视图，可在世界重置时自动初始化。|
|`post_reset()`|重置到默认状态（位置、方向）。|
|`is_valid() → bool`|检查封装的 Prim 路径在舞台上是否有效。|
|`apply_physics_material(material, weaker_than_descendants=False)`|将指定物理材质应用于平面碰撞体，可选择不覆盖子节点材质。|
|`get_applied_physics_material() → PhysicsMaterial`|返回当前应用的物理材质实例。|
|`set_world_pose(position=None, orientation=None)`|立即设置（瞬移）地面平面的世界坐标系位置与方向。|
|`get_world_pose() → Tuple[np.ndarray, np.ndarray]`|获取当前世界坐标系下的位置 `(3,)` 与四元数方向 `(4,)`。|
|`get_default_state() → XFormPrimState`|获取初始默认状态，用于重置时恢复。|
|`set_default_state(position=None, orientation=None)`|设置后续重置时的默认位置与方向。|

---

此封装将地面平面分为可视部分和碰撞部分，用户可灵活配置材质、颜色及物理特性，并通过初始化与重置方法轻松管理仿真生命周期中的状态。


#### _VisualCapsule_

`VisualCapsule` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个**仅可视、无碰撞和无刚体动力学**的胶囊体（Capsule），支持可视化材质、尺寸和变换属性。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`UsdGeom.Capsule`|USD 胶囊几何|底层 USD 几何类型，定义了胶囊的半径与高度。|
|`SingleGeometryPrim`|可视封装基类|管理 Prim 的路径、变换（位置/旋转/缩放）与可见性，但不启用碰撞。|
|可视化材质|Visual Material|支持用户传入或自动创建的 `PreviewSurface`，可指定颜色。|
|尺寸控制|Radius/Height API|提供 `set_radius`、`get_radius`、`set_height`、`get_height` 方法来动态调整胶囊尺寸。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径（例如 `"/World/Xform/Capsule"`）。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"visual_capsule"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`，不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`，不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`，不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|胶囊体表面颜色 `(r, g, b)`，默认 `[0.5, 0.5, 0.5]`。|
|`radius`|`Optional[float]`|胶囊半径，默认 `1.0`（如果未在 USD 中已有定义）。|
|`height`|`Optional[float]`|胶囊高度（从中心到一端的半长度两倍），默认 `1.0`（如果未在 USD 中已有定义）。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质对象，若不指定则使用 `PreviewSurface` 并应用 `color`。|

---

_**主要方法**_

| 方法                     | 返回/说明     |
| ---------------------- | --------- |
| `set_radius(radius)`   | 设置胶囊半径。   |
| `get_radius() → float` | 获取当前胶囊半径。 |
| `set_height(height)`   | 设置胶囊高度。   |
| `get_height() → float` | 获取当前胶囊高度。 |

---
#### _VisualCone_

`VisualCone` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个**仅可视、无碰撞和无刚体动力学**的圆锥体（Cone），支持可视化材质、尺寸与变换属性。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`UsdGeom.Cone`|USD 圆锥几何|底层 USD 几何类型，定义了圆锥的底面半径与高度。|
|`SingleGeometryPrim`|可视封装基类|管理 Prim 的路径、变换（位置/旋转/缩放）与可见性，但不启用碰撞。|
|可视化材质|Visual Material|支持用户传入或自动创建的 `PreviewSurface`，可指定颜色。|
|尺寸控制|Radius/Height API|提供 `set_radius`、`get_radius`、`set_height`、`get_height` 方法来动态调整圆锥尺寸。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径，例如 `"/World/Xform/Cone"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"visual_cone"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`，不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`，不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`，不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆锥可视部分颜色 `(r, g, b)`，默认 `[0.5, 0.5, 0.5]`。|
|`radius`|`Optional[float]`|圆锥底面半径，默认 `1.0`（如果未在 USD 中已有定义）。|
|`height`|`Optional[float]`|圆锥高度（轴向长度），默认 `1.0`（如果未在 USD 中已有定义）。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质对象，若不指定则使用 `PreviewSurface` 并应用 `color`。|

---

_**主要方法**_

| 方法                     | 返回/说明       |
| ---------------------- | ----------- |
| `set_radius(radius)`   | 设置圆锥底面半径。   |
| `get_radius() → float` | 获取当前圆锥底面半径。 |
| `set_height(height)`   | 设置圆锥高度。     |
| `get_height() → float` | 获取当前圆锥高度。   |


---

#### _VisualCuboid_

`VisualCuboid` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个**仅可视、无碰撞和无刚体动力学**的立方体（Cube），支持可视化材质、尺寸与变换属性。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`UsdGeom.Cube`|USD 立方体几何|底层 USD 几何类型，定义了立方体的边长。|
|`SingleGeometryPrim`|可视封装基类|管理 Prim 的路径、变换（位置/旋转/缩放）与可见性，但不启用碰撞。|
|可视化材质|Visual Material|支持用户传入或自动创建的 `PreviewSurface`，可指定颜色。|
|尺寸控制|Size API|提供 `set_size`、`get_size` 方法来动态调整立方体边长，并同步更新几何 Extent。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径，例如 `"/World/Xform/Cube"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"visual_cube"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`，不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`，不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`，不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|立方体可视部分颜色 `(r, g, b)`，默认 `[0.5, 0.5, 0.5]`。|
|`size`|`Optional[float]`|立方体边长，默认 `1.0`（如果未在 USD 中已有定义）。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质对象，若不指定则使用 `PreviewSurface` 并应用 `color`。|

---

_**主要方法**_

| 方法                   | 返回/说明                       |
| -------------------- | --------------------------- |
| `set_size(size)`     | 设置立方体的边长；同时更新底层 USD Extent。 |
| `get_size() → float` | 获取当前设置的边长值。                 |

---
#### _VisualCylinder_

`VisualCylinder` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个**仅可视、无碰撞和无刚体动力学**的圆柱体（Cylinder），支持可视化材质、尺寸与变换属性。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`UsdGeom.Cylinder`|USD 圆柱几何|底层 USD 几何类型，定义了圆柱的底面半径与高度。|
|`SingleGeometryPrim`|可视封装基类|管理 Prim 的路径、变换（位置/旋转/缩放）与可见性，但不启用碰撞。|
|可视化材质|Visual Material|支持用户传入或自动创建的 `PreviewSurface`，可指定颜色。|
|尺寸控制|Radius/Height API|提供 `set_radius`、`get_radius`、`set_height`、`get_height` 方法来动态调整圆柱尺寸。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径，例如 `"/World/Xform/Cylinder"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"visual_cylinder"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`，不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`，不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`，不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`，不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|圆柱可视部分颜色 `(r, g, b)`，默认 `[0.5, 0.5, 0.5]`。|
|`radius`|`Optional[float]`|圆柱底面半径，默认 `1.0`（如果未在 USD 中已有定义）。|
|`height`|`Optional[float]`|圆柱高度（轴向长度），默认 `1.0`（如果未在 USD 中已有定义）。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质对象，若不指定则使用 `PreviewSurface` 并应用 `color`。|

---

_**主要方法**_

| 方法                     | 返回/说明       |
| ---------------------- | ----------- |
| `set_radius(radius)`   | 设置圆柱底面半径。   |
| `get_radius() → float` | 获取当前圆柱底面半径。 |
| `set_height(height)`   | 设置圆柱高度。     |
| `get_height() → float` | 获取当前圆柱高度。   |


---
#### _VisualSphere_

`VisualSphere` 类是一个高层封装，用于在 Isaac Sim 中创建或封装一个**仅可视、无碰撞和无刚体动力学**的球体（Sphere），支持可视化材质、尺寸与变换属性。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`UsdGeom.Sphere`|USD 球体几何|底层 USD 几何类型，定义了球体的半径。|
|`SingleGeometryPrim`|可视封装基类|管理 Prim 的路径、变换（位置/旋转/缩放）与可见性，但不启用碰撞。|
|可视化材质|Visual Material|支持用户传入或自动创建的 `PreviewSurface`，可指定颜色。|
|尺寸控制|Radius API|提供 `set_radius`、`get_radius` 方法来动态调整球体半径。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要创建或封装的 USD Prim 路径，例如 `"/World/Xform/Sphere"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"visual_sphere"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`；不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`；不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`；不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`；不指定则保持原状。|
|`visible`|`Optional[bool]`|在渲染中是否可见，默认 `True`。|
|`color`|`Optional[np.ndarray]`|球体可视部分颜色 `(r, g, b)`，默认 `[0.5, 0.5, 0.5]`。|
|`radius`|`Optional[float]`|球体半径，默认 `1.0`（如果未在 USD 中已有定义）。|
|`visual_material`|`Optional[VisualMaterial]`|可视化材质对象；若不指定则使用 `PreviewSurface` 并应用 `color`。|

---

_**主要方法**_

| 方法                     | 返回/说明     |
| ---------------------- | --------- |
| `set_radius(radius)`   | 设置球体的半径。  |
| `get_radius() → float` | 获取当前球体半径。 |

---
### _PhysicsContext_

`PhysicsContext` 类为 Isaac Sim 中的物理场景提供了高层封装，负责创建或复用 USD Stage 中的 `PhysicsScene` Prim，并简化对各类物理设置（时间步长、重力、约束求解器、GPU 管线、CCD、稳定化等）的配置和查询。

---

***核心功能

- **自动创建/复用 PhysicsScene**
    
    - 如果 Stage 中不存在 `PhysicsScene`，则在指定路径（`prim_path`）创建一个新的；
        
    - 否则复用现有的场景，并应用物理属性 API（`PhysxSceneAPI`）。
        
- **统一接口管理物理参数**
    
    - 时间步长（`physics_dt`）、子步（`substeps`）
        
    - 重力方向与大小
        
    - 广播相位（Broadphase）算法（MBP/GPU）
        
    - 碰撞检测（CCD）与稳定化（Stabilization）
        
    - 求解器类型（TGS/PGS）
        
    - GPU 动力学及 Fabric 支持
        
    - 残差报告（Solver residuals）
        
- **高级 GPU 调优（如果使用 CUDA 设备）**
    
    - 并行线程数、缓冲区大小
        
    - 接触对（contacts）、聚合对（aggregate pairs）等容量限制
        
- **物理材质绑定**
    
    - 支持在场景中创建并绑定默认 `UsdPhysics.Material`，设置静/动摩擦与恢复系数
        

---

***初始化参数

|参数|类型|默认值|说明|
|---|---|---|---|
|`physics_dt`|`Optional[float]`|`1/60` 秒|主物理步长（秒），可选。|
|`prim_path`|`str`|`"/physicsScene"`|若无现有场景，则在此路径下创建新的 `PhysicsScene`。|
|`sim_params`|`dict`|`None`|可选字典，覆盖多种物理参数（参见下方“`sim_params` 可选键”）。|
|`set_defaults`|`bool`|`True`|是否使用一组推荐的默认配置（重力、CCD、稳定化、Solver: TGS、GPU 动力学等）。|

`sim_params` ***可选键

|键名|值类型|说明|
|---|---|---|
|`"gravity"`|List[float]|重力向量在各轴上的分量。|
|`"dt"`|`float`|自定义物理步长（代替 `physics_dt`）。|
|`"substeps"`|`int`|每帧内部的物理子步数量。|
|`"use_gpu_pipeline"`|`bool`|是否启用 PhysX GPU 管线（Suppress Readback）。|
|`"worker_thread_count"`|`int`|CPU 物理线程数。|
|`"enable_solver_residuals"`|`bool`|启用残差报告接口。|
|`"use_fabric"`|`bool`|启用 GPU Fabric 支持（软体/网格等）。|
|`"enable_scene_query_support"`|`bool`|支持场景查询（raycast 等）。|
|`"solver_type"`|`0` 或 `1`|0 → PGS，1 → TGS 求解器。|
|`"enable_stabilization"`|`bool`|是否在求解器中启用稳定化修正。|
|`"bounce_threshold_velocity"`|`float`|弹跳阈值速度。|
|`"friction_offset_threshold"`|`float`|摩擦偏移阈值。|
|`"friction_correlation_distance"`|`float`|摩擦关联距离。|
|`"default_physics_material"`|`dict`|`{"static_friction":, "dynamic_friction":, "restitution":}`，定义并绑定默认物理材质。|

---

***核心方法

|方法|功能|
|---|---|
|`set_physics_dt(dt, substeps)`|设置物理步长与子步。|
|`get_physics_dt()`|查询当前物理步长。|
|`set_gravity(value)`|设置重力方向与大小（自动依据 Stage 的 Up Axis）。|
|`get_gravity() → (direction: List, magnitude: float)`|获取当前重力向量与大小。|
|`enable_ccd(flag)` / `is_ccd_enabled()`|启用/查询连续碰撞检测（CCD）。|
|`enable_stablization(flag)` / `is_stablization_enabled()`|启用/查询求解器稳定化。|
|`set_solver_type(type)` / `get_solver_type()`|切换并查询求解器算法（TGS 或 PGS）。|
|`set_broadphase_type(type)` / `get_broadphase_type()`|设置并查询 Broadphase 算法（MBP/GPU）。|
|`enable_gpu_dynamics(flag)` / `is_gpu_dynamics_enabled()`|启用/查询 PhysX GPU Dynamics。|
|`enable_fabric(flag)` / `use_fabric` (属性)|启用/查询 Fabric 支持。|
|`enable_residual_reporting(flag)`|启用物理残差报告接口。|
|`get_solver_velocity_residual(report_max)`|获取速度残差（Max 或 RMS）。|
|`get_solver_position_residual(report_max)`|获取位置残差（Max 或 RMS）。|
|一系列 `set_gpu_...` / `get_gpu_...`|配置并查询各类 GPU 缓冲区与容量上限（contacts、patches、heap、buffer 等）。|

---

## Robot
#### _Robot_

`Robot` 类继承自 `SingleArticulation`，用于将 USD Stage 中的关节（Articulation）Prim 封装成可操控的机器人对象，集成了运动学、动力学接口与控制器管理。

---

***核心特性

- **Articulation 控制**：通过内置或自定义的 `ArticulationController` 实现各关节的驱动与反馈。
    
- **统一接口**：继承自 `SingleArticulation`，自动管理 Prim 路径、变换（位置／旋转／缩放）与可见性。
    
- **传感器管理**：预留 `_sensors` 列表，可在子类或后续扩展中注册与读取多种传感器数据。
    
- **一键复位**：`post_reset()` 方法在重置场景时，不仅恢复基础空间变换，还会应用关节的默认位置、速度与力矩。
    

---

***初始化参数

|参数|类型|默认值|说明|
|---|---|---|---|
|`prim_path`|`str`|—|USD Stage 中 Articulation Prim 的绝对路径，例如 `"/World/envs/env_0/panda"`。|
|`name`|`str`|`"robot"`|在 Scene 中的短名称，需与其他对象唯一。|
|`position`|`Optional[Sequence[float]]`|—|世界坐标系中的位置 `(x, y, z)`；不指定则保持 Prim 原有位置。|
|`translation`|`Optional[Sequence[float]]`|—|本地坐标系下的平移 `(x, y, z)`；不指定则保持 Prim 原有平移。|
|`orientation`|`Optional[Sequence[float]]`|—|四元数方向 `(w, x, y, z)`；不指定则保持 Prim 原有朝向。|
|`scale`|`Optional[Sequence[float]]`|—|本地缩放 `(sx, sy, sz)`；不指定则保持 Prim 原有缩放。|
|`visible`|`bool`|`True`|是否在渲染中可见。|
|`articulation_controller`|`Optional[ArticulationController]`|`None`|自定义的关节控制器；若为 `None`，则创建默认的 `ArticulationController`。|

---

***主要方法

|方法|返回／说明|
|---|---|
|`post_reset()`|在仿真重置（`Scene.reset()`）时被调用；会恢复根 Prim 的默认位置／方向，并对所有关节施加默认的状态（位置、速度、力矩）。|
|（继承自 `SingleArticulation` 的各类运动学／动力学接口）|如 `get_joint_positions()`、`set_joint_positions()`、`apply_joint_efforts()`、`compute_forward_kinematics()` 等。|

---
#### _RobotView_

`RobotView` 类继承自 `Articulation`，用于在 USD Stage 中**批量封装**一组关节机构（机器人）Prims，支持正则匹配、向量化变换与统一复位。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`prim_paths_expr`|Prim 路径表达式|使用正则或通配表达式一次性匹配并封装多个 Articulation Prim。|
|向量化变换|Vectorized Transforms|支持通过 NumPy 或 PyTorch 张量批量设置位置、平移、方向、缩放和可见性。|
|批量复位|Batch Reset|`post_reset()` 时对所有实例统一恢复根变换、关节默认状态及增益。|
|传感器管理|Sensor Management|保留 `_sensors` 列表，可为每个实例挂载并管理多种传感器。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_paths_expr`|`str`|用于匹配多个 Prim 路径的正则或通配表达式，例如 `"/World/envs/env.*/panda"`。|
|`name`|`str`|在 Scene 中使用的短名称，需唯一，默认 `"rigid_prim_view"`。|
|`positions`|`Optional[Union[np.ndarray, torch.Tensor]]`|各实例在世界坐标系中的位置，形状 `(N, 3)`；不指定则保持原状。|
|`translations`|`Optional[Union[np.ndarray, torch.Tensor]]`|各实例在本地坐标系的平移，形状 `(N, 3)`；不指定则保持原状。|
|`orientations`|`Optional[Union[np.ndarray, torch.Tensor]]`|各实例的四元数方向 `(w, x, y, z)`，形状 `(N, 4)`；不指定则保持原状。|
|`scales`|`Optional[Union[np.ndarray, torch.Tensor]]`|各实例的本地缩放因子，形状 `(N, 3)`；不指定则保持原状。|
|`visibilities`|`Optional[Union[np.ndarray, torch.Tensor]]`|各实例的可见性布尔标记，形状 `(N,)`；不指定则保持原状。|

---

_**主要方法**_

| 方法                                                                               | 返回/说明                                     |
| -------------------------------------------------------------------------------- | ----------------------------------------- |
| `post_reset()`                                                                   | 批量重置所有机器人：恢复根 Prim 变换、各关节默认位置、速度、力矩和控制增益。 |
| `get_joint_positions()`                                                          | 从所有实例中读取当前关节位置，返回形状 `(N, J)` 的数组或张量。      |
| `set_joint_positions(positions)`                                                 | 为所有实例同时设置目标关节位置，`positions` 形状 `(N, J)`。  |
| `apply_joint_efforts(efforts)`                                                   | 对所有实例施加关节力矩，`efforts` 形状 `(N, J)`。        |
| `compute_forward_kinematics()`                                                   | 批量计算并返回各实例末端执行器的位姿。                       |
| `compute_inverse_kinematics(targets)`                                            | 针对每个实例计算逆运动学，`targets` 形状 `(N, …)`。       |
| `set_default_state(positions, translations, orientations, scales, visibilities)` | 批量设置各实例的默认状态，用于后续 `post_reset()`。         |
| `initialize()`                                                                   | 初始化内部缓存与控制器，一定要在第一次使用前调用。                 |

----
## Scene
#### _Scene_

`Scene` 类提供了一个统一的接口，用于向 USD Stage 添加、管理及重置各种物理与可视化对象，并通过内部注册表跟踪它们的生命周期。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|Scene Registry|场景注册表|集中存储并管理所有添加到场景中的对象（刚体、机器人、传感器、材质等）。|
|统一添加|Unified Add|通过 `add` 方法将各种 `Prim` 或高层对象（如 `Robot`、`GroundPlane`）注册到场景中。|
|批量复位|Batch Post-Reset|调用 `post_reset()` 时，依次遍历注册表并对所有对象执行重置操作。|
|自动初始化|Auto Initialize|在仿真开始前，内部 `_finalize()` 会对所有对象执行一次性初始化。|
|AABB 计算|AABB Computation|可选启用/禁用轴对齐包围盒计算，并通过 `compute_object_AABB` 获取对象包围盒。|
|对象移除|Remove & Clear|支持按名称移除单个对象或清空整个场景（可选择仅从注册表或同时删除 USD Prim）。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|无|—|`Scene` 在构造时无需外部参数，自动创建内部 `SceneRegistry` 并初始化状态。|

---

_**主要方法**_

| 方法                                                               | 返回/说明                                            |
| ---------------------------------------------------------------- | ------------------------------------------------ |
| `add(obj: SingleXFormPrim)`                                      | 将支持的 `Prim` 或高层对象添加到场景注册表，并返回该对象。                |
| `add_ground_plane(...) → GroundPlane`                            | 创建并添加一个带物理材质的平面地面，可自定义尺寸、摩擦及颜色。                  |
| `add_default_ground_plane(...) → GroundPlane`                    | 引用 Isaac Sim 默认环境中的地面 USD，创建并添加至场景。              |
| `post_reset()`                                                   | 对注册表中的所有对象调用其 `post_reset()`，恢复默认状态。             |
| `_finalize(physics_sim_view)`                                    | 内部方法，在仿真启动前对所有对象执行 `initialize()`。               |
| `remove_object(name: str, registry_only: bool=False)`            | 按名称移除注册表项，若 `registry_only=False` 同时删除 USD Prim。 |
| `get_object(name: str) → SingleXFormPrim`                        | 根据名称从注册表中检索对象实例。                                 |
| `object_exists(name: str) → bool`                                | 判断名称是否已注册于场景中。                                   |
| `clear(registry_only: bool=False)`                               | 批量移除所有注册对象（可选择仅清注册表或同时删除 USD Prim）。              |
| `enable_bounding_boxes_computations()`                           | 启用包围盒计算功能，内部创建 `UsdGeom.BBoxCache`。              |
| `disable_bounding_boxes_computations()`                          | 禁用包围盒计算并清空缓存。                                    |
| `compute_object_AABB(name: str) → Tuple[np.ndarray, np.ndarray]` | 在启用包围盒计算后，返回指定对象的最小和最大包围盒点。                      |

---
#### _SceneRegistry_

`SceneRegistry` 类维护了一个中央注册表，用于跟踪并管理所有添加到场景中的各种对象实例，包括刚体、几何体、机器人、传感器、材质等。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|多类型字典存储|Multi-Type Dicts|为每种支持的对象类型（如 `RigidPrim`、`RobotView`、`DeformableMaterial` 等）各自维护一个独立字典。|
|统一访问接口|Unified Access|通过属性方法暴露各个内部字典，使外部可按类型安全地获取或遍历注册对象。|
|名称唯一性检查|Name Uniqueness|`name_exists(name)` 可快速检查任意类型的对象名称是否已被占用。|
|动态注册|Dynamic Registration|提供 `add_*` 系列方法，可注册新对象实例到相应字典。|
|移除与检索|Remove & Get|支持 `remove_object(name)` 仅从注册表中移除对象，以及 `get_object(name)` 按名称检索对象。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|无|—|`SceneRegistry` 在构造时无需任何外部参数，会自动创建各注册子字典。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`name_exists(name: str) → bool`|检测任意注册类别中是否已有给定名称的对象，返回布尔值。|
|`add_rigid_object(name, obj: SingleRigidPrim)`|注册单刚体对象到刚体字典。|
|`add_geometry_object(name, obj: SingleGeometryPrim)`|注册单几何体对象到几何体字典。|
|`add_articulated_system(name, obj: SingleArticulation)`|注册单一关节系统对象。|
|`add_articulated_view(name, obj: Articulation)`|注册关节视图对象（批量封装）。|
|`add_robot(name, obj: Robot)`|注册单机器人对象。|
|`add_robot_view(name, obj: RobotView)`|注册机器人视图对象（批量封装）。|
|`add_sensor(name, obj: BaseSensor)`|注册传感器对象。|
|`add_xform(name, obj: SingleXFormPrim)`|注册基础变换对象。|
|`add_xform_view(name, obj: XFormPrim)`|注册变换视图对象。|
|`add_cloth(name, obj: SingleClothPrim)`|注册布料系统对象。|
|`add_cloth_view(name, obj: ClothPrim)`|注册布料视图对象。|
|`add_particle_system(name, obj: SingleParticleSystem)`|注册粒子系统对象。|
|`add_particle_system_view(name, obj: ParticleSystem)`|注册粒子系统视图对象。|
|`add_particle_material(name, obj: ParticleMaterial)`|注册粒子材质对象。|
|`add_particle_material_view(name, obj: ParticleMaterialView)`|注册粒子材质视图对象。|
|`add_deformable(name, obj: SingleDeformablePrim)`|注册可变形体对象。|
|`add_deformable_view(name, obj: DeformablePrim)`|注册可变形体视图对象。|
|`add_deformable_material(name, obj: DeformableMaterial)`|注册可变形材质对象。|
|`add_deformable_material_view(name, obj: DeformableMaterialView)`|注册可变形材质视图对象。|
|`remove_object(name: str)`|仅从注册表中删除指定名称对象；若不存在则抛出异常。|
|`get_object(name: str) → object \| None`|按名称在所有注册字典中搜索并返回对象，找不到则返回 `None`。|

> `SceneRegistry` 的设计旨在为上层 `Scene` 类提供可靠的对象管理与查询支持，无需关心各类型对象的底层存储细节。


---
## Sensor
#### _BaseSensor_

`BaseSensor` 类继承自 `SingleXFormPrim`，为封装或创建 USD Prim 作为传感器提供统一接口，当前无新增属性/方法，面向未来扩展。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|`SingleXFormPrim` 继承|基类封装|继承自 `SingleXFormPrim`，管理 Prim 的路径、变换与可见性。|
|通用传感器接口|Unified Sensor API|后续可在此基础上扩展各种具体传感器类型的方法与属性。|
|场景注册兼容|Scene Integration|可通过 `Scene.add()` 注册到场景管理，并统一初始化与重置。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_path`|`str`|要封装或创建的 USD Prim 路径，如 `"/World/sensors/my_sensor"`。|
|`name`|`str`|在 `Scene` 中的短名称，需唯一，默认 `"base_sensor"`。|
|`position`|`Optional[Sequence[float]]`|世界坐标系位置 `(x, y, z)`；不指定则保持原状。|
|`translation`|`Optional[Sequence[float]]`|本地坐标系平移 `(x, y, z)`；不指定则保持原状。|
|`orientation`|`Optional[Sequence[float]]`|四元数方向 `(w, x, y, z)`；不指定则保持原状。|
|`scale`|`Optional[Sequence[float]]`|本地缩放 `(sx, sy, sz)`；不指定则保持原状。|
|`visible`|`Optional[bool]`|渲染时是否可见，默认保留基类行为（通常为 `True`）。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`initialize(physics_sim_view=None)`|在仿真启动前初始化 Prim，创建或使用传入的物理仿真视图。|
|`post_reset()`|仿真重置后调用，当前无行为占位，后续可在此添加传感器状态重置逻辑。|

> 虽然 `BaseSensor` 当前不增加额外功能，但为所有自定义传感器类型提供了统一的初始化和重置钩子，可根据需要进行子类扩展。



---
#### _RigidContactView_

`RigidContactView` 类用于高层封装一个或多个刚体 Prim，跟踪它们与指定过滤对象的接触信息，并提供丰富的接触力和摩擦数据接口。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|正则匹配封装|Regex-Based Wrapping|通过 `prim_paths_expr` 一次性匹配并封装多个刚体 Prim。|
|接触过滤|Contact Filtering|使用 `filter_paths_expr` 指定哪些其他 Prim 的接触应被报告。|
|物理视图|Physics Simulation View|初始化或自动创建 PhysX 张量 API 视图以获取实时接触数据。|
|多样接触数据|Rich Contact Data|支持净接触力、接触力矩阵、详细接触点/法线/分离度以及摩擦数据查询。|
|可配置性能|Performance Controls|通过 `max_contact_count` 限制最大接触数，通过 `disable_stablization` 控制稳定化参数。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`prim_paths_expr`|`Union[str, List[str]]`|匹配刚体 Prim 的路径或正则表达式列表，例如 `"/World/envs/env.*/Cube"`。|
|`filter_paths_expr`|`Union[List[str], List[List[str]]]`|对应于每组刚体的接触过滤路径表达式列表，用于筛选接触对象。|
|`name`|`str`|在 `Scene` 中的短名称，需唯一，默认 `"rigid_contact_view"`。|
|`prepare_contact_sensors`|`bool`|若为 `True`，会在封装时为每个 Prim 准备并应用接触报告器（阈值设为 0）。|
|`disable_stablization`|`bool`|是否禁用 PhysX 的接触稳定化参数，默认 `True`。|
|`max_contact_count`|`int`|详细接触数据缓冲区的最大条目数，默认为 0（无限制）。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`initialize(physics_sim_view=None)`|创建或绑定物理仿真视图，调用后可获取接触数据。|
|`is_physics_handle_valid() → bool`|检查物理视图是否已初始化有效。|
|`get_net_contact_forces(indices=None, clone=True, dt=1.0)`|返回 `(M,3)` 净接触力数组；`indices` 指定哪 M 个刚体。|
|`get_contact_force_matrix(indices=None, clone=True, dt=1.0)`|返回 `(M, num_filters,3)` 接触力矩阵。|
|`get_contact_force_data(indices=None, clone=True, dt=1.0)`|返回详细接触数据元组：法向力、接触点、法线、距离、每对接触数量与起始索引。|
|`get_friction_data(indices=None, clone=True, dt=1.0)`|返回摩擦力相关数据元组：切向力、切向点、每对数量与起始索引。|

> 在仿真时间轴每次 STOP + PLAY 后，需再次调用 `initialize()` 以重置物理视图句柄。



---
## _SimulationContext_

`SimulationContext` 类管理仿真生命周期与时序事件，封装物理与渲染步进控制、回调管理以及与 USD Stage 和 PhysX 的集成，提供统一接口来启动、暂停、重置和清理仿真环境。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|单例模式|Singleton Pattern|通过 `__new__` 和 `instance()` 确保全局唯一实例，便于跨模块访问。|
|物理与渲染时序|Physics & Rendering Timing|支持自定义物理步长 (`physics_dt`) 与渲染步长 (`rendering_dt`)，并自动配置 Carb 和 Timeline。|
|全面回调管理|Callback Management|提供 `add_*_callback` 与 `remove_*_callback` 系列方法，管理物理、舞台、时间线和渲染回调。|
|场景与物理上下文|Stage & Physics Context|自动创建与切换 USD Stage；内置 `PhysicsContext` 管理 PhysX 相关设置。|
|仿真控制接口|Simulation Control API|一键启动 (`play`/`play_async`)、暂停 (`pause`/`pause_async`)、停止 (`stop`/`stop_async`) 及步进 (`step`/`render`)。|
|资源清理|Resource Cleanup|`reset`、`clear` 和 `clear_all_callbacks` 方法，保障仿真重置和退出时资源正确释放。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`physics_dt`|`Optional[float]`|物理仿真步长（秒），默认 `1/60`。|
|`rendering_dt`|`Optional[float]`|渲染步长（秒），默认与 `physics_dt` 同或 `1/60`。|
|`stage_units_in_meters`|`Optional[float]`|USD Stage 单位（米），影响重力等物理参数，默认 `0.01`。|
|`physics_prim_path`|`str`|物理场景 Prim 路径，默认 `"/physicsScene"`。|
|`sim_params`|`dict`|传递给 `PhysicsContext` 的仿真参数字典。|
|`set_defaults`|`bool`|是否应用一系列默认设置（CCD、稳定化、GPU 动力学、MBP 广播、TGS 求解器等）。|
|`backend`|`str`|张量后端：`"numpy"`、`"torch"` 或 `"warp"`，默认 `"numpy"`。|
|`device`|`Optional[str]`|GPU 设备标识，后端为 `torch` 或 `warp` 时指定。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`instance() → SimulationContext`|获取全局单例实例。|
|`clear_instance()`|清除单例并取消所有回调。|
|`set_simulation_dt(physics_dt, rendering_dt)`|动态设置物理与渲染步长并更新 Carb 设置与 Timeline。|
|`get_physics_dt() → float`|返回当前物理步长。|
|`get_rendering_dt() → float`|返回当前渲染步长。|
|`play()` / `play_async()`|开始仿真播放，内部处理一次步进以刷新物理句柄。|
|`pause()` / `pause_async()`|暂停仿真播放。|
|`stop()` / `stop_async()`|停止仿真并重置时间线。|
|`step(render: bool=True)`|执行一次物理与/或渲染步进。|
|`render()` / `render_async()`|刷新渲染（视口、UI、PhysX Fabric 等）。|
|`reset(soft: bool=False)` / `reset_async(soft=False)`|重置仿真；`soft=True` 仅重置注册对象，无停止/播放。|
|`initialize_simulation_context_async()`|异步初始化舞台与仿真环境，适用于 Extensions 工作流。|
|`get_physics_context() → PhysicsContext`|获取底层物理上下文实例，用于高级物理参数配置。|
|`clear()`|清空 USD Stage（保留 `/World` 和 PhysicsScene），用于重建场景。|
|`add_physics_callback(name, fn)` / `remove_physics_callback(name)`|添加/移除每个物理步前调用的回调。|
|`add_stage_callback(name, fn)` / `remove_stage_callback(name)`|添加/移除舞台事件回调（如打开/关闭）。|
|`add_timeline_callback(name, fn)` / `remove_timeline_callback(name)`|添加/移除时间线事件回调（如播放/暂停）。|
|`add_render_callback(name, fn)` / `remove_render_callback(name)`|添加/移除渲染事件回调。|
|`clear_all_callbacks()`|移除所有注册的回调函数，回收资源并触发垃圾收集。|

> `SimulationContext` 将仿真时序、物理设置与 USD Stage 操作集中管理，为复杂仿真场景提供一致且可扩展的编程接口。


---
## _World_

`World` 类继承自 `SimulationContext`，在继承仿真时序与物理管理功能基础上，集成了任务（`BaseTask`）和场景（`Scene`）管理，以及数据记录器（`DataLogger`），为构建完整仿真循环提供高层接口。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|继承仿真上下文|Simulation Context Inheritance|复用 `SimulationContext` 的物理/渲染步进、回调和 USD Stage 管理。|
|任务管理|Task Management|支持通过 `add_task` 注册多个任务（`BaseTask` 子类），统一前置、后置和重置调用。|
|场景集成|Scene Integration|持有 `Scene` 实例，用于管理仿真中所有对象的注册、添加与批量复位。|
|数据记录|Data Logging|内置 `DataLogger`，可在每步调用中记录任务和场景状态数据。|
|简化循环|Simulation Loop API|提供 `initialize_physics`、`reset`、`step`、`step_async` 等一体化仿真循环方法，自动调用任务钩子和场景复位。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|继承自 `SimulationContext` 的所有参数|见 `SimulationContext`|包括 `physics_dt`、`rendering_dt`、`stage_units_in_meters`、`physics_prim_path`、`sim_params`、`set_defaults`、`backend`、`device`。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`add_task(task: BaseTask)`|注册一个任务，任务需有唯一 `name` 属性。|
|`get_current_tasks() → dict`|返回所有已注册任务的字典，键为任务名。|
|`get_task(name: str) → BaseTask`|按名称检索特定任务，名称不存在时抛出异常。|
|`is_tasks_scene_built() → bool`|检查是否已为所有任务调用过 `set_up_scene`。|
|`get_observations(task_name: Optional[str]=None) → dict`|获取一个或全部任务的观测数据。|
|`calculate_metrics(task_name: Optional[str]=None) → dict`|获取一个或全部任务的指标（如奖励）。|
|`is_done(task_name: Optional[str]=None) → bool`|查询一个或全部任务的完成状态。|
|`get_data_logger() → DataLogger`|返回当前 `DataLogger` 实例，用于自定义数据写入。|
|`initialize_physics()`|初始化物理视图并调用 `Scene._finalize`，准备所有对象和任务。|
|`reset(soft: bool=False)`|重置仿真：若首次重置则调用各任务的 `set_up_scene`，然后按顺序执行停止、清理、重置、初始化与 `post_reset`。|
|`reset_async(...)` / `reset_async_set_up_scene()` / `reset_async_no_set_up_scene()`|异步版本的重置流程，分阶段支持是否重复场景搭建。|
|`step(render: bool=True, step_sim: bool=True)`|执行仿真循环单步：调用任务 `pre_step`、更新包围盒、物理/渲染步进并记录数据。|
|`step_async(step_size: Optional[float]=None)`|仅调用任务 `pre_step` 和数据记录，用于异步循环场景。|
|`clear()`|清空场景注册表和 USD Stage（保留 PhysicsScene 与 `/World`），重置任务和数据记录器状态。|

> `World` 将仿真管理、场景管理、任务执行和数据记录紧密组合，为强化学习、机器人控制和大规模仿真提供了一站式编程接口。


---
## Task
#### _BaseTask_

`BaseTask` 类提供了任务（Task）在仿真场景中的生命周期管理接口，包括场景搭建、观测获取、指标计算、前置步骤和重置钩子，便于多任务并行与模块化开发。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|场景搭建钩子|Setup Scene Hook|`set_up_scene` 提供将任务相关资产添加到 `Scene` 的统一入口。|
|偏移管理|Offset Management|构造时可指定全局偏移 `offset`，自动应用到所有任务对象上。|
|任务对象跟踪|Task Object Tracking|通过内部 `_task_objects` 字典管理本任务创建或封装的 Prim 实例。|
|观测/指标接口|Observations & Metrics|定义 `get_observations`、`calculate_metrics`、`is_done` 抽象方法，需子类实现。|
|时序钩子|Pre-Step & Post-Reset|`pre_step` 在物理步进前调用，`post_reset` 在世界重置后调用，支持状态更新与清理。|
|参数接口|Params API|`set_params` 与 `get_params` 抽象方法，用于动态修改与查询任务参数。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`name`|`str`|任务名称，需在 `World` 中唯一。|
|`offset`|`Optional[np.ndarray]`|应用于本任务所有对象的全局位置偏移，形状 `(3,)`，默认 `[0,0,0]`。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`set_up_scene(scene: Scene)`|将任务所需资产与 Prim 添加到传入的 `Scene` 实例中。|
|`get_task_objects() → dict`|返回本任务所有注册的 Prim 对象字典。|
|`get_observations() → dict`|**抽象**：获取当前任务的观测数据，供控制层使用。|
|`calculate_metrics() → dict`|**抽象**：计算并返回任务指标（如奖励、距离等）。|
|`is_done() → bool`|**抽象**：判断任务是否完成。|
|`pre_step(time_step_index: int, simulation_time: float)`|在每次物理步进前调用，可用于执行控制或数据记录逻辑。|
|`post_reset()`|在 `World.reset()` 后调用，用于重置任务内部状态。|
|`cleanup()`|在重置前调用，用于移除运行期间创建的临时对象。|
|`get_description() → str`|返回任务的简要文本描述，可用于日志或 UI。|
|`set_params(*args, **kwargs)`|**抽象**：动态修改任务参数（如目标位置、速度等）。|
|`get_params() → dict`|**抽象**：获取当前任务参数及其可修改性描述。|

> 子类需至少实现 `get_observations`、`calculate_metrics`、`is_done`、`set_params` 与 `get_params` 五个方法，才能完成任务逻辑的定义。

---
#### _FollowTarget_

`FollowTarget` 类继承自 `BaseTask` 并实现抽象基类 `ABC`，用于定义“跟随目标”类型任务：在场景中创建机器人和目标对象，并提供目标姿态更新、可视化反馈以及障碍物管理。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|抽象机器人设置|Abstract Robot Setup|`set_robot()` 抽象方法，子类需实现机器人创建或封装逻辑。|
|目标物体管理|Target Management|通过 `set_params` 在场景中创建或更新目标 Prim（可视化立方体或已有 Prim）。|
|可视化反馈|Visual Feedback|根据是否到达目标，动态调整目标材质颜色（红/绿切换）。|
|障碍物支持|Obstacle Support|`add_obstacle` 与 `remove_obstacle` 接口，动态在任务中增删障碍物。|
|局部偏移应用|Offset Application|自动将任务对象位置应用全局偏移 `_offset`，方便批量场景移动。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`name`|`str`|任务名称，需在 `World` 中唯一。|
|`target_prim_path`|`Optional[str]`|目标 Prim 路径；若不存在则创建新的 VisualCuboid。|
|`target_name`|`Optional[str]`|目标对象在场景中的注册名称。|
|`target_position`|`Optional[np.ndarray]`|目标在本地坐标系的位置 `(x,y,z)`；缺省为 `[0,0.1,0.7]/stage_units`。|
|`target_orientation`|`Optional[np.ndarray]`|目标在本地坐标系的四元数 `(w,x,y,z)`；缺省为 `[-π,0,π]` 欧拉转四元数。|
|`offset`|`Optional[np.ndarray]`|应用于任务所有对象的全局偏移，形状 `(3,)`。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`set_up_scene(scene: Scene)`|初始化场景：添加地面、设置目标与机器人、注册到 `Scene` 并应用偏移。|
|`set_robot() → Robot`|**抽象**：子类实现机器人加载或封装逻辑，并返回机器人实例。|
|`set_params(...)`|创建或更新目标 Prim；支持已有路径或自动生成 `VisualCuboid`。|
|`get_params() → dict`|返回任务参数字典，包括目标路径、名称、位置、方向及机器人名称。|
|`get_observations() → dict`|获取当前机器人关节状态和目标局部位姿；自动防止目标穿透地面。|
|`calculate_metrics() → dict`|**抽象**：子类实现任务指标计算（如距离误差、奖励等）。|
|`is_done() → bool`|**抽象**：子类实现任务完成判定。|
|`target_reached() → bool`|判断末端执行器与目标世界坐标距离是否小于阈值。|
|`pre_step(time_step_index, simulation_time)`|每步前更新目标材质颜色：到达时设绿，否则红。|
|`post_reset()`|重置后钩子，当前无额外逻辑，可在子类扩展。|
|`add_obstacle(position: np.ndarray=None) → VisualCuboid`|在场景中创建蓝色障碍立方体并注册；返回其实例。|
|`remove_obstacle(name: Optional[str]=None)`|移除指定或最后一个障碍物。|
|`get_obstacle_to_delete() → SingleXFormPrim`|获取最后添加的障碍物对象实例。|
|`obstacles_exist() → bool`|检查当前是否存在任何障碍物。|
|`cleanup()`|在重置前移除所有障碍物，清理 `_obstacle_cubes` 字典。|

> 子类需实现 `set_robot`、`calculate_metrics` 与 `is_done` 方法，以完成具体的“跟随目标”任务逻辑。

---
#### _PickPlace_

`PickPlace` 类继承自 `BaseTask` 并实现抽象基类 `ABC`，用于定义“拾取-放置”类型的任务：在场景中创建一个可动态模拟的立方体（`DynamicCuboid`）和机器人，提供目标位置管理、观测获取与任务生命周期钩子。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|动态立方体|Dynamic Object|使用 `DynamicCuboid` 在场景中添加可动力学模拟的蓝色立方体，作为待拾取的对象。|
|抽象机器人设置|Abstract Robot Setup|`set_robot()` 抽象方法，子类需实现机器人加载或封装逻辑，并返回机器人实例。|
|位置与方向参数|Position & Orientation Params|可通过 `set_params` 动态设置立方体起始位置、方向及放置目标位置。|
|观测接口|Observations API|`get_observations` 同时返回立方体和机器人末端执行器的位姿与关节状态，便于控制层决策。|
|重置后复位|Post-Reset Hook|在 `post_reset` 中对并联夹持器（若使用 `ParallelGripper`）进行张开，保证每次试验开始时夹具处于打开状态。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`name`|`str`|任务名称，需在 `World` 中唯一。|
|`cube_initial_position`|`Optional[np.ndarray]`|立方体在本地坐标系的初始位置；缺省为 `[0.3,0.3,0.3]/stage_units`。|
|`cube_initial_orientation`|`Optional[np.ndarray]`|立方体的初始四元数方向；缺省为单位四元数 `[1,0,0,0]`。|
|`target_position`|`Optional[np.ndarray]`|放置位置坐标；缺省为 `[-0.3,-0.3, cube_size_z/2]/stage_units` 并加上全局偏移 `offset`。|
|`cube_size`|`Optional[np.ndarray]`|立方体尺寸缩放向量；缺省为 `[0.0515,0.0515,0.0515]/stage_units`。|
|`offset`|`Optional[np.ndarray]`|应用于任务所有对象的全局位置偏移，形状 `(3,)`。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`set_up_scene(scene: Scene)`|初始化场景：添加地面、动态立方体、机器人，并将它们注册到任务对象字典后应用偏移。|
|`set_robot() → Robot`|**抽象**：子类实现机器人加载或封装逻辑，并返回机器人实例。|
|`set_params(cube_position, cube_orientation, target_position)`|动态更新立方体的局部位姿和放置目标位置。|
|`get_params() → dict`|返回当前任务参数字典，包括立方体位置/方向、目标位置以及各对象名称。|
|`get_observations() → dict`|获取机器人关节状态、末端执行器位置，以及立方体的局部位姿与目标位置。|
|`pre_step(time_step_index, simulation_time)`|每步前钩子，当前空实现，可由子类扩展（如执行额外视觉反馈）。|
|`post_reset()`|重置后钩子：若机器人使用并联夹具 (`ParallelGripper`)，则将夹具设置为张开状态。|
|`calculate_metrics() → dict`|**抽象**：子类实现任务指标计算（如成功率、距离误差等）。|
|`is_done() → bool`|**抽象**：子类实现任务完成判定（如立方体放置到目标位置）。|

> 子类需至少实现 `set_robot`、`calculate_metrics` 与 `is_done` 方法，以定义具体的“拾取-放置”任务逻辑。


---
#### _Stacking_

`Stacking` 类继承自 `BaseTask` 并实现抽象基类 `ABC`，用于定义“多立方体堆叠”任务：在场景中创建多个可模拟动力学的立方体与机器人，按层级堆叠到指定目标位置。

---

_**关键特性**_

|英文特性|中文翻译|说明|
|---|---|---|
|多立方体管理|Multiple Cuboids Management|根据输入的初始位置列表(`cube_initial_positions`)，批量创建 `DynamicCuboid`，并随机赋予颜色。|
|抽象机器人设置|Abstract Robot Setup|`set_robot()` 抽象方法，子类需实现机器人加载或封装逻辑，并返回机器人实例。|
|分层目标位置|Layered Target Position|每个立方体的摆放目标高度自动计算：第 i 个方块的 z 轴目标为 cube_sizez2+i⋅cube_sizez \tfrac{\text{cube\_size}_z}{2} + i\cdot \text{cube\_size}_z2cube_sizez​​+i⋅cube_sizez​。|
|全局偏移支持|Global Offset Support|构造时可指定全局偏移 `offset`，将其加到所有初始与目标位置上，方便整体场景移动。|
|观测接口|Observations API|`get_observations` 同时返回机器人关节状态与末端位姿，以及每个方块当前位置、朝向和层级目标位置。|
|重置后复位|Post-Reset Hook|与 `PickPlace` 类似，对并联夹具(`ParallelGripper`)执行重置后自动张开操作。|

---

_**初始化参数**_

|参数|类型|说明|
|---|---|---|
|`name`|`str`|任务名称，需在 `World` 中唯一。|
|`cube_initial_positions`|`np.ndarray (N×3)`|N 个方块的初始位置列表（本地坐标）。|
|`cube_initial_orientations`|`Optional[list of np.ndarray]`|N 个方块的初始四元数列表；若为 `None`，则每个使用默认方向。|
|`stack_target_position`|`Optional[np.ndarray]`|堆叠基准位置，所有层级目标均在该 x、y 之上沿 z 分层；缺省为 `[-0.3,-0.3,0]/stage_units`。|
|`cube_size`|`Optional[np.ndarray]`|方块尺寸缩放向量；缺省为 `[0.0515,0.0515,0.0515]/stage_units`。|
|`offset`|`Optional[np.ndarray]`|全局位置偏移 `(3,)`，加到初始与目标位置上。|

---

_**主要方法**_

|方法|返回/说明|
|---|---|
|`set_up_scene(scene: Scene)`|添加地面、批量创建方块并注册到 `_task_objects`，然后创建机器人并应用全局偏移。|
|`set_robot() → Robot`|**抽象**：子类实现机器人加载或封装逻辑，并返回机器人实例。|
|`set_params(...)`|动态更新某个方块的位置/方向，或更新整个堆叠的基准目标位置。|
|`get_params() → dict`|返回可修改的 `stack_target_position` 及不可修改的 `robot_name`。|
|`get_observations() → dict`|返回机器人关节状态及末端执行器位置；对每个方块，返回其当前位置、朝向和计算出的层级目标位置。|
|`pre_step(time_step_index, simulation_time)`|每步前钩子，当前空实现，可由子类扩展。|
|`post_reset()`|重置后钩子：若机器人使用 `ParallelGripper`，则将夹具设置为张开状态。|
|`get_cube_names() → List[str]`|返回所有方块的名称列表，便于外部索引与操作。|
|`calculate_metrics() → dict`|**抽象**：子类实现任务指标计算（如堆叠成功率、误差距离等）。|
|`is_done() → bool`|**抽象**：子类实现任务完成判定（如所有方块正确堆叠）。|

> 子类需实现 `set_robot`、`calculate_metrics` 与 `is_done`，以定义具体的多方块堆叠逻辑。