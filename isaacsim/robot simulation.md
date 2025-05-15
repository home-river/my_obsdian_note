
# 机器人仿真（Robot Simulation）

本节介绍了用于控制机器人运动的相关工具。最低级别的控制是**关节级控制**。更高一层的控制我们按机器人类型进行划分，因为 Isaac Sim 提供了三类控制器，每类对应一种机器人：

### 轮式机器人（Wheeled Robots）

使用基于通用公式的控制器，仅需极少的机器人特定参数作为输入。

### 操纵机器人（Manipulators）

使用基于复杂优化的控制器，因此即便是同一个机器人执行同一个任务，也可能使用多种不同的控制器，每种控制器都基于不同的优化方法。此类控制器通常在优化过程中需要使用机器人模型。

### 策略控制机器人（Policy Controlled Robots）

使用通过强化学习训练出的控制器。它们对“控制器”的定义更加宽泛，通常也包含了任务规划器和路径规划器等功能。

---

## 模块概览：

- **关节级控制（Joint Level Control）**
    
- **关节控制器（Articulation Controller）**
    
- **轮式机器人（Wheeled Robots）**
    
    - 移动机器人控制器（Mobile Robot Controllers）
        
- **操纵机器人（Manipulators）**
    
    - 运动生成（Motion Generation）
        
    - 表面夹爪扩展（Surface Gripper Extension）
        
- **策略控制机器人（Policy Controlled Robots）**
    
    - Isaac Sim 中的强化学习策略示例（Reinforcement Learning Policies Examples in Isaac Sim）
        

---

## 技巧与深入解析：

- **机器人仿真技巧（Robot Simulation Tips）**
    
- **实用链接（Useful Links）**

---

# Articulation 控制器

### 概述

Articulation 控制器是 Isaac Sim 中用于控制关节位置、关节速度和关节力矩的底层控制器。该控制器可通过 Python 和 Omnigraph 调用。

> **注意**  
> 角度单位在内部以弧度表示，而 USD 中的角度以度表示，Articulation 控制器会自动进行相应转换。

---

### Python 接口

#### 创建 Articulation 控制器

有多种方式可以创建 Articulation 控制器。通常在机器人 prim 上通过 `SingleArticulation` 类应用 Articulation 时，系统会隐式创建对应的控制器；也可以在仿真开始前直接导入控制器类并显式创建，但这种方式需要在初始化时创建或传入相应的 `Articulation` 对象。

#### Single Articulation

下面的示例将加载并在 Franka 机器人上应用 Articulation。

```python
import isaacsim.core.utils.stage as stage_utils
from isaacsim.core.prims import SingleArticulation

usd_path = "/Path/To/Robots/Franka/franka_alt_fingers.usd"
prim_path = "/World/envs/env_0/panda"

# 加载 Franka Panda 机器人 USD 文件
stage_utils.add_reference_to_stage(usd_path, prim_path)
# 将 prim 包装为 Articulation
prim = SingleArticulation(prim_path=prim_path, name="franka_panda")
```

#### Articulation 控制器

```python
from isaacsim.core.api.controllers.articulation_controller import ArticulationController

# 创建 Articulation 控制器实例
articulation_controller = ArticulationController()
```


### 初始化控制器

仿真启动后，必须先初始化机器人关节（articulation），才能向机器人发送任何命令。

#### SingleArticulation

更常见的方法是调用之前创建的 `SingleArticulation` 对象进行初始化，这将同时初始化存储在该对象中的 Articulation 控制器和 Articulation 视图。

```python
prim.initialize()
```

#### Articulation 控制器

仿真启动后，还必须使用 Articulation 视图初始化 Articulation 控制器。Articulation 视图是用于选择关节并应用关节动作的后端接口。

例如，下面的代码示例假设场景中已经有一个位于 `/World/envs/env_0/panda` 的 Franka 机器人，并已单独创建了对应的 `articulation_view`：

```python
articulation_controller.initialize(articulation_view)
```

### Articulation 操作（Articulation Action）

关节控制命令首先封装在 `ArticulationAction` 对象中，然后再发送给 Articulation 控制器。该对象允许你指定：

- `joint_positions`：关节位置命令（弧度）
    
- `joint_velocities`：关节速度命令（弧度/秒，可选）
    
- `joint_efforts`：关节力矩命令（牛·米，可选）
    
- `joint_indices`：被驱动关节的索引（可选，若省略则作用于全部关节）
    

若 `joint_indices` 为空，命令默认作用于机器人所有关节；若命令数组中某一值为 0，则视该关节为不被驱动。

#### 示例：闭合 Franka 手指

以下示例将索引 7（panda_finger_joint1）和 8（panda_finger_joint2）设为位置 0.0：

```python
import numpy as np
from isaacsim.core.utils.types import ArticulationAction

action = ArticulationAction(
    joint_positions = np.array([0.0, 0.0]),
    joint_indices   = np.array([7, 8])
)
```

### 示例：移动所有关节

以下示例将机器人所有关节移动到指定位置（共 9 个关节）：

```python
import numpy as np
from isaacsim.core.utils.types import ArticulationAction

action = ArticulationAction(
    joint_positions = np.array([0.0, -1.0, 0.0, -2.2, 0.0, 2.4, 0.8, 0.04, 0.04])
)
```

> **重要**
> 
> - 确保 `joint_positions`、`joint_velocities` 或 `joint_efforts` 的长度与 `joint_indices` 一致；若未指定 `joint_indices`，则长度必须与机器人关节总数相符。
>     
> - 同一关节只能采用一种控制方式，不能同时使用位置（position）和力矩（effort）命令。

### 应用操作（Apply Action）

`SingleArticulation` 和 `ArticulationController` 类中的 `apply_action` 方法会将之前创建的 `ArticulationAction` 应用到机器人上。

#### SingleArticulation

```python
prim.apply_action(action)
```

#### ArticulationController

```python
articulation_controller.apply_action(action)
```

---

### Omnigraph 接口

Articulation 控制器也可通过 Omnigraph 节点访问。在此方式中，需要填入机器人 prim 的信息和关节控制命令。

**输入端口说明：**

- **execIn**  
    输入执行触发
    
- **targetPrim**  
    机器人 Articulation 根节点的 prim，若填入 `robotPath` 可留空
    
- **robotPath**  
    机器人 Articulation 根路径，若填入 `targetPrim` 可留空
    
- **jointIndices**  
    要控制的关节索引，若控制全部关节或填入 `jointNames` 可留空
    
- **jointNames**  
    要控制的关节名称，若控制全部关节或填入 `jointIndices` 可留空
    
- **positionCommand**  
    关节位置命令，若不使用位置控制则留空
    
- **velocityCommand**  
    关节速度命令，若不使用速度控制则留空
    
- **effortCommand**  
    关节力矩命令，若不使用力矩控制则留空
    

> **重要**
> 
> - 确保命令的顺序及长度与传入的 `jointIndices` 或 `jointNames` 一致；若未传入这两者，命令长度必须与机器人关节数量相符。
>     
> - 同一关节只能采用一种控制方式，例如不能同时使用位置和力矩命令。
>     

> **示例资源**  
> 可在 Asset Browser 的 **Samples** > **Rigging** > **MockRobot** 中找到 `mock_robot_rigged.usd` 示例。

[[65b51f556b4a9687c2db80e538183513_MD5.jpeg|Open: Pasted image 20250515081906.png]]
![[65b51f556b4a9687c2db80e538183513_MD5.jpeg]]

---
# Mobile Robot Controllers

## OmniGraph 节点

移动机器人控制器 – *差动控制器

差动控制器利用左右车轮速度的差值来控制机器人在平面内的线速度和角速度。该控制器可实现原地转向，已应用于 NVIDIA Nova Carter 机器人。
[[b3259624d9c21692b189c97382979f74_MD5.jpeg|Open: Pasted image 20250515082708.png]]
![[b3259624d9c21692b189c97382979f74_MD5.jpeg]]
### 数学公式

车轮的角速度由下式计算：

$$
\omega_R = \frac{1}{2r}\bigl(2V + \omega_{tw}\bigr) 
$$
$$
\omega_L = \frac{1}{2r}\bigl(2V - \omega_{tw}\bigr)
$$

其中  
•   $\omega$ 表示期望角速度  
• V 表示期望线速度  
• r 表示车轮半径  
•  $l_{tw}$  表示左右车轮间距  
•  $\omega_R$  表示右轮的期望角速度  
• $\omega_L$ 表示左轮的期望角速度


其中 $\omega$ 是期望的角速度，$V$ 是期望的线速度，$r$ 是车轮的半径，$l_{tw}$ 是车轮之间的距离，$\omega_R$ 是期望的右轮角速度，$\omega_L$ 是期望的左轮角速度。

---

### OmniGraph 节点 

差动控制器 输入端口

|输入端口|含义|
|---|---|
|execIn|执行触发|
|wheelRadius|车轮半径|
|wheelDistance|车轮间距|
|dt|时间步长|
|maxAcceleration|最大线加速度（前进和后退），0.0 表示不设置|
|maxDeceleration|最大线减速度（制动），0.0 表示不设置|
|maxAngularAcceleration|最大角加速度，0.0 表示不设置|
|maxLinearSpeed|最大线速度，0.0 表示不设置|
|maxAngularSpeed|最大角速度，0.0 表示不设置|
|maxWheelSpeed|最大车轮转速|
|Desired Linear Velocity|期望线速度|
|Desired Angular Velocity|期望角速度|

*差动控制器 输出端口

|输出端口|含义|
|---|---|
|VelocityCommand|左右车轮的速度命令|


### Python  
下面的代码片段为轮半径 3 cm、轴距 11.25 cm 的机器人初始化了差动控制器，并设置了线速度 0.3 m/s 和角速度 1.0 rad/s。

```python
from isaacsim.robot.wheeled_robots.controllers.differential_controller import DifferentialController

wheel_radius = 0.03        # 轮半径（米）
wheel_base   = 0.1125      # 轮距（米）
controller = DifferentialController("test_controller", wheel_radius, wheel_base)

linear_speed  = 0.3        # 期望线速度（m/s）
angular_speed = 1.0        # 期望角速度（rad/s）
command = [linear_speed, angular_speed]
actions = controller.forward(command)
```

---

## Holonomic Controller（全向控制器）  
全向控制器用于在全向移动机器人上计算各个关节的驱动命令，使机器人产生期望的前向、侧向和偏航速度。全向机器人示例：NVIDIA Kaya 机器人。该控制问题被构建为一个二次规划，以最小化作用在质心上的“剩余合力”。
[[b70b2c6130ae5662650c507ce52413f5_MD5.jpeg|Open: Pasted image 20250515084501.png]]
![[b70b2c6130ae5662650c507ce52413f5_MD5.jpeg]]
> **注意**  
> 机器人 Prim 下的轮毂关节必须额外定义麦克纳姆轮的滚轮半径和倾斜角度属性，才能正确进行全向运动学计算。

```python
import omni.usd
from pxr import Sdf

stage = omni.usd.get_context().get_stage()
joint_prim = stage.GetPrimAtPath("/path/to/wheel_joint")
# 设置麦克纳姆轮半径（米）
joint_prim.CreateAttribute("isaacmecanumwheel:radius", Sdf.ValueTypeNames.Float).Set(0.12)
# 设置麦克纳姆轮滚轮倾角（度）
joint_prim.CreateAttribute("isaacmecanumwheel:angle",  Sdf.ValueTypeNames.Float).Set(10.3)
```

`HolonomicRobotUsdSetup` 类可以自动完成上述属性设置并创建控制器实例。


### **数学公式**

代价函数被定义为机器人关节的控制输入。通过最小化控制输入，可以减少过大的加速度。

$$
J = \min(X^T \cdot X)
$$

等式约束由线性和角度目标速度输入来设置：

$$
v_{\text{input}} = V^T \cdot X 
$$

$$
\omega_{\text{input}} = (V \times D_{\text{wheeldisttoCOM}}) \cdot X
$$

---
### **OmniGraph 节点**


*全向控制器 输入端口

|输入端口|含义|
|---|---|
|execIn|执行触发|
|wheelRadius|各个轮子的半径数组|
|wheelPositions|各个轮子相对于底盘质心的位置信息|
|wheelOrientations|各个轮子在质心坐标系下的朝向|
|mecanumAngles|各个麦克纳姆轮相对于轮子旋转轴的倾斜角|
|wheelAxis|轮子的旋转轴|
|upAxis|向上方向轴（默认 z 轴）|
|Velocity Commands for the vehicle|车辆在 x、y 方向上的速度以及旋转速度|
|maxLinearSpeed|车辆允许的最大线速度|
|maxAngularSpeed|车辆允许的最大角速度|
|maxWheelSpeed|车轮关节允许的最大旋转速度|
|linearGain|线速度输入的增益|
|angularGain|角速度输入的增益|

---

**全向控制器 输出端口**

|输出端口|含义|
|---|---|
|jointVelocityCommand|各轮关节的速度命令|
### python
下面的代码片段计算了一个三轮全向机器人在给定速度命令 `[1.0, 1.0, 0.1]` 下，各轮关节的速度输出：

```python
from isaacsim.robot.wheeled_robots.controllers.holonomic_controller import HolonomicController

# 三个轮子的参数
wheel_radius       = [0.04, 0.04, 0.04]   # 每个轮子的半径（米）
wheel_orientations = [
    [0,     0, 0,  1],    # 第 1 个轮子的朝向（四元数）
    [0.866, 0, 0, -0.5],  # 第 2 个轮子的朝向
    [0.866, 0, 0,  0.5],  # 第 3 个轮子的朝向
]
wheel_positions    = [
    [-0.0980432,  0.000636773, -0.050501],  # 第 1 个轮子相对于质心的位置（米）
    [ 0.0493475, -0.084525,    -0.050501],  # 第 2 个轮子相对于质心的位置
    [ 0.0495291,  0.0856937,   -0.050501],  # 第 3 个轮子相对于质心的位置
]
mecanum_angles     = [90, 90, 90]           # 每个麦克纳姆轮滚轮相对于轮子旋转轴的倾斜角（度）

# 期望机器人速度 [前向速度 Vx, 侧向速度 Vy, 偏航角速度 ω]
velocity_command = [1.0, 1.0, 0.1]

# 创建全向控制器实例
controller = HolonomicController(
    "test_controller",
    wheel_radius,
    wheel_positions,
    wheel_orientations,
    mecanum_angles
)

# 计算各轮的速度命令（角速度）
actions = controller.forward(velocity_command)
```


## **Ackermann 控制器**  
Ackermann 控制器常用于具有可转向车轮的机器人，例如 NVIDIA Leatherback 机器人。Isaac Sim 中的 Ackermann 控制器假定已提供期望的转向角和线速度，并基于机器人几何结构进行计算。

### 数学公式

计算左侧和右侧转向轮之间的转向角偏差：

$$
R_{icr} = \frac{l_{wb}}{\tan(\theta_{steer})} 
$$

$$
\theta_L = \arctan\left(\frac{l_{wb}}{R_{icr} - 0.5 \times l_{tw}}\right) 
$$


$$
\theta_R = \arctan\left(\frac{l_{wb}}{R_{icr} + 0.5 \times l_{tw}}\right)
$$

其中，$R_{icr}$ 是瞬时旋转中心的半径，$\theta_{steer}$ 是期望的转向角，$l_{wb}$ 是后轴和前轴之间的距离（车轮基距），$l_{tw}$ 是车轮宽度。

计算各个轮子的速度（前向转向情况）：

第一步是找到轮子与瞬时旋转中心的距离。

$$
D_{front} = \sqrt{(R_{icr} \pm 0.5 l_{tw})^2 + (l_{wb})^2} 
$$

$$
D_{rear} = R_{icr} \pm 0.5 l_{tw}
$$

> [!NOTE] **备注**
> 对于“±”，使用“−”表示离 $R_{icr}$ 更近的轮子，使用“+”表示离 $R_{icr}$ 更远的轮子。

然后，可以计算期望的轮子速度：

$$
\omega_{\text{front}} = \frac{V_{\text{desired}} \cdot D_{\text{front}}}{R_{\text{icr}} \cdot r_{\text{front}}} 
$$

$$
\omega_{\text{rear}} = \frac{V_{\text{desired}} \cdot D_{\text{rear}}}{R_{\text{icr}} \cdot r_{\text{rear}}}
$$

其中，$V_{\text{desired}}$ 是期望的线速度，$r_{\text{front}}$ 是期望的前轮半径，$r_{\text{rear}}$ 是期望的后轮半径。

---
### OmniGraph Node
**OmniGraph 节点 —— Ackermann 控制器 输入端口**

|输入端口|含义|
|---|---|
|execIn|执行触发|
|acceleration|期望的前向加速度（m/s²）|
|speed|期望的前向速度（m/s）|
|steeringAngle|期望的转向角（弧度），默认情况下前轮驱动时正值表示向左转|
|currentLinearVelocity|机器人当前的线速度（m/s）|
|wheelBase|机器人前后轴之间的距离（米）|
|trackWidth|机器人左右后轮之间的距离（米）|
|turningWheelRadius|机器人前轮半径（米）|
|maxWheelVelocity|机器人车轮的最大角速度（rad/s）|
|invertSteeringAngle|翻转转向角符号，设置为 `true` 用于后轮转向|
|useAcceleration|是否使用加速度作为输入，设置为 `false` 则使用速度输入|
|maxWheelRotation|前轮的最大转动角度（弧度）|
|dt|仿真步长的时间增量（秒）|

---

**OmniGraph 节点 —— Ackermann 控制器 输出端口**

|输出端口|含义|
|---|---|
|execOut|输出触发|
|leftWheelAngle|左侧转向轮的转角（弧度）|
|rightWheelAngle|右侧转向轮的转角（弧度）|
|wheelRotationVelocity|转向轮的角速度（rad/s）|

### python
下面的 Python 代码示例创建了一个 Ackermann 控制器，用于具有 1.65 m 轴距、1.25 m 轮距和 0.25 m 轮半径的机器人，并向其发送期望前进速度 1.1 rad/s 和转向角 0.1 rad：

```python
from isaacsim.robot.wheeled_robots.controllers.ackermann_controller import AckermannController

# 机器人几何参数
wheel_base             = 1.65   # 轴距（前后车轴间距，米）
track_width            = 1.25   # 轮距（左右后轮间距，米）
wheel_radius           = 0.25   # 前轮半径（米）

# 期望运动指令
desired_forward_vel    = 1.1    # 期望前进速度（rad/s）
desired_steering_angle = 0.1    # 期望转向角（rad）

# 为了立即达到目标速度和转向角，将加速度、转向速度和仿真步长设为 0
acceleration           = 0.0    # 加速度输入（m/s²）
steering_velocity      = 0.0    # 转向角速度输入（rad/s）
dt                     = 0.0    # 仿真时间步长（秒）

# 创建 Ackermann 控制器实例
controller = AckermannController(
    "test_controller",
    wheel_base=wheel_base,
    track_width=track_width,
    front_wheel_radius=wheel_radius
)

# 通过 forward() 方法计算转向角和车轮速度命令
actions = controller.forward([
    desired_steering_angle,  # 转向角
    steering_velocity,       # 转向角速度
    desired_forward_vel,     # 前进速度
    acceleration,            # 加速度
    dt                       # 时间步长
])
```

---

# 运动生成（Motion Generation）

Lula 是一款用于机器人操控的高性能运动生成库。其主要功能包括：

1. **RMPflow**  
    实时、耦合式的局部策略，用于引导机械臂到达任务空间目标，同时避开动态障碍物。
    
2. **Rapidly-exploring Random Tree (RRT) 系列算法**  
    包括 RRT-Connect 和 JT-RRT，提供静态环境下的全局路径规划解决方案。
    
3. **轨迹生成工具**  
    支持在 C 空间与任务空间中描述的路径上生成时间最优（time-optimal）轨迹。
    
4. **运动学求解器接口**  
    暴露了高性能的正／逆运动学求解器，作为上述高层运动生成工具的底层支持。
    

---

NVIDIA Isaac Sim 还集成了 **cuRobo**，一个基于 GPU 加速的高性能运动生成库，其特色包括：

- 批量的无碰撞逆运动学
    
- 无碰撞运动规划
    
- 基于网格或 nvblox 地图的动态障碍物下的反应式控制
    

更多详情请参见 cuRobo 教程。

---

### 主要模块一览

|模块|功能简介|
|---|---|
|Lula Robot Description & XRDF Editor|编辑和管理机械臂的机器人描述及 XRDF 文件|
|Lula RMPflow|实时局部运动策略，用于动态避障|
|Lula RRT|静态环境下的全局路径规划（RRT 系列算法）|
|Lula Kinematics Solver|高性能正／逆运动学求解器|
|Lula Trajectory Generator|时间最优轨迹生成器|
|cuRobo & cuMotion|GPU 加速的批量逆运动学、碰撞检测及反应式规划|

---

### 示例

#### 交互式示例

在 Isaac Sim 界面中，依次打开 `Windows > Examples > Robotics Examples`，在浏览器中选择并阅读右侧 “Information” 标签页的运行说明。常用示例包括：

- **Follow Target**：Manipulation > Follow Target
    
- **RoboFactory**：Multi-Robot > RoboFactory
    
- **RoboParty**：Multi-Robot > RoboParty
    

> ***提示***：按下 **STOP** 再 **PLAY** 可能不会正确重置场景，请使用 **RESET** 按钮。

#### 独立示例

在 `<isaac_sim_root_dir>` 下使用 `./python.sh`（Linux）或 `python.bat`（Windows）运行脚本：

- `standalone_examples/api/isaacsim.robot.manipulators/franka/follow_target_with_rmpflow.py`
    
- `standalone_examples/api/isaacsim.robot.manipulators/franka/follow_target_with_ik.py`


---

## **运动生成（Motion Generation）**

运动生成提供了一组可用于控制 Isaac Sim 中对象的 API。该 API 由一系列抽象接口组成，用于向 Isaac Sim 中添加运动控制算法。运动生成中的接口提供了两个基本功能：

- 简化将新的机器人算法集成到 NVIDIA Isaac Sim 中的流程。
    
- 提供一个标准化的结构，用于比较类似的机器人算法。
    

例如，如果你有一款之前未在 Isaac Sim 中描述过的机器人，可以使用这些 API 来定义该机器人以及它的运动方式。

当前，运动生成扩展（Motion Generation Extension）提供了三种接口：

1. **运动策略算法（Motion Policy Algorithm）**
    
2. **路径规划（Path Planner）**
    
3. **运动学求解器（Kinematics Solvers）**
    

在 Isaac Sim 中，机器人通常通过 USD 文件添加到舞台（stage）上进行指定。然而，我们预计各种机器人算法会有自己描述机器人运动学结构和自定义参数的方式。为了避免与具体的机器人描述格式发生冲突，运动生成扩展中的接口包含了一些函数，用于在 USD 机器人和特定算法之间进行翻译。具体而言，算法可以指定它所关心的机械关节，以及它们在列表中的顺序。本扩展提供的帮助类——Articulation Motion Policy、Path Planner Visualizer 和 Articulation Kinematics Solver——均会使用这些接口函数，将 USD 机器人关节状态与接口实现进行正确映射。

在 Isaac Sim 中，我们使用 “**Articulation**” 一词来指代通过 USD 表示的模拟机器人。在运动生成扩展中，以 “Articulation” 为前缀的实用类都负责处理算法与模拟机器人之间的接口。

此外，运动生成扩展还包含少量特殊用途的控制器，这些控制器目前并未利用 Motion Policy 或 Path Planner 接口。

---

### 运动生成扩展 API 文档

- **运动学求解器（Kinematics Solvers）**
    
- **轨迹生成（Trajectory Generation）**
    
- **路径规划算法（Path Planner Algorithm）**
    
- **Lula RRT**
    
- **运动策略算法（Motion Policy Algorithm）**
    
- **RMPflow**
    
- **RMPflow 调优指南（RMPflow Tuning Guide）**
    

### 参考文献

Cheng, C.A., Mukadam, M., Issac, J., Birchfield, S., Fox, D., Boots, B., Ratliff, N.,  
RMPflow: A computational graph for automatic motion policy generation (2018), [https://arxiv.org/abs/1811.07049](https://arxiv.org/abs/1811.07049)

---

## **运动生成扩展 API 文档**

有关使用信息，请参阅 Isaac Sim 运动生成扩展[ API 文档](https://docs.isaacsim.omniverse.nvidia.com/latest/py/source/extensions/isaacsim.robot_motion.motion_generation/docs/index.html)。该 API 内容是更广泛的 Omniverse API 文档的一部分。

---

## **运动学求解器（Kinematics Solvers）**

与运动策略算法类似，运动学求解器也是一个接口类，目前提供了单一的实现。运动学求解器能计算正运动学和逆运动学，当前实现基于 NVIDIA 开发的 Lula 库（参见 [Lula Kinematics Solver](https://docs.isaacsim.omniverse.nvidia.com/latest/manipulators/concepts/kinematics_solver.html#isaac-sim-lula-kinematics-solver)）。

---

### 包括内容

- **Kinematics Solver** —— 运动学求解器接口
    
- **Articulation Kinematics Solver** —— USD 机器人关节映射封装
    
- **Lula Kinematics Solver** —— 基于 Lula 库的具体实现
    

---

### Kinematics Solver 接口

**职责**：定义在机器人任意可用帧上计算正/逆运动学的函数。

1. **关节名称**
    
    - 必须实现 `get_joint_names()`，返回求解器所关注的关节列表及其顺序。
        
    - 传入正运动学的关节位置数组，顺序应与此函数返回值一致；逆运动学的输出亦相同。
        
    - `ArticulationKinematicsSolver` 会在 USD 机器人（Articulation）与求解器内部表示之间做映射。
        
2. **坐标帧名称**
    
    - 必须实现 `get_all_frame_names()`，返回求解器中可通过名称引用其位置的所有帧。
        
    - 这些帧名由求解器自身的配置文件定义，不必与 USD 机器人中的帧名称一一对应。
        
3. **机器人基座位姿**
    
    - 包含 `set_robot_base_pose()` 方法，用于设定机器人基座在世界坐标系中的位姿。
        
    - 在计算正运动学时，会先按基座位姿将末端执行器位置转换到世界坐标系；逆运动学输入同理，先从世界坐标系转换到基座坐标系。
        
4. **碰撞感知（可选）**
    
    - 通过 `supports_collision_avoidance() -> bool` 指定是否支持避障计算。
        
    - 若支持，求解器可接收世界状态输入，在内部考虑场景中障碍物。此时务必正确设置基座位姿，以便将障碍物位置从世界坐标系转换到机器人基座坐标系。
        

---

### Articulation Kinematics Solver

该类在 USD 机器人（Articulation）与任意 `KinematicsSolver` 实现之间提供映射封装：

- **正运动学**
    
    - 从 USD 机器人获取当前关节位置，并按 `get_joint_names()` 要求的顺序传入求解器，返回末端执行器在世界坐标系中的位姿。
        
- **逆运动学**
    
    - 使用当前 USD 机器人关节位置作为初始值（warm start），调用求解器逆运动学，输出关节位置数组，再封装为可直接作用于 USD 机器人的 `ArticulationAction`。
        

---

### Lula Kinematics Solver

基于 Lula 库的运动学求解器实现，不支持场景内物体的碰撞避障。除 `KinematicsSolver` 接口方法外，还提供若干用于调整内部求解行为的接口，例如：

- `set_max_iterations(int)` —— 设置逆运动学最大迭代次数，超过后返回失败。
    

---

### Lula 运动学求解器配置

要对新机器人使用 Lula 求解器，需要两个文件：

1. **URDF 文件**
    
    - 定义机器人运动学结构、关节及连杆名称，并指定各关节的位置/转动范围。
        
    - 文件中其他属性（如质量、惯量、网格模型等）可省略。
        
2. **YAML 辅助描述文件**
    
    - 列举所有受控关节（C 空间维度），并可指定默认的 C 空间配置。
        
    - 可在该文件中为未受控关节设定固定位置。

---

## **轨迹生成（Trajectory Generation）**

在运动生成扩展中，提供了一套定义 C 空间和任务空间轨迹的工作流。核心组件包括以下三个类：

- **Trajectory Interface** —— 轨迹接口
    
- **Articulation Trajectory** —— 机器人关节轨迹封装
    
- **Lula Trajectory Generator** —— 基于 Lula 库的轨迹生成器
    

---

### Trajectory Interface

轨迹接口定义了一个 Trajectory 对象必须具备的基本功能，用于按时间连续地返回机器人 C 空间位置。一个 Trajectory 需实现以下四个访问器：

|属性／方法|说明|
|---|---|
|`start_time`|轨迹开始生效的最早时刻（秒）|
|`end_time`|轨迹结束生效的最晚时刻（秒）|
|`active_joints`|该轨迹所控制的关节名称列表，顺序对应后续返回的关节目标|
|`joint_targets(time)`|在 `[start_time, end_time]` 范围内，返回指定时间点的关节位置／速度目标数组|

**用法示例**  
将 Trajectory 对象传入 `ArticulationTrajectory` 构造函数，即可使其对机器人关节进行控制。

---

### Articulation Trajectory

`ArticulationTrajectory` 负责将一个 Trajectory 应用于 USD “Articulation” 机器人实体。主要提供两个接口：

|方法|说明|
|---|---|
|`get_action_at_time(time)`|在指定时刻（需在轨迹时域内）返回一个 `ArticulationAction`，可直接作用于机器人关节。|
|`get_action_sequence(timestep)`|按给定的物理仿真步长 `timestep`，在 `start_time` 到 `end_time` 之间生成并返回动作序列。|

> **注意**：在让机器人跟踪生成的 `ArticulationAction` 序列之前，应先将机器人关节设置到 Trajectory 的初始状态。

---

## Lula 轨迹生成器（Lula Trajectory Generator）

Lula 提供了两种轨迹生成器：

1. **LulaCSpaceTrajectoryGenerator**
    
2. **LulaTaskSpaceTrajectoryGenerator**
    

### 配置文件要求

要对特定机器人使用上述生成器，需要两个文件：

1. **URDF 文件**
    
    - 定义机器人运动学、关节和连杆名称，以及各关节的位置／转动范围。
        
    - 质量、惯量、网格等属性可省略。
        
2. **YAML 补充描述文件**
    
    - 列举所有受控关节（C 空间维度）。
        
    - 指定默认关节配置、关节加速度或抖动（jerk）限制。
        
    - 可为不受控关节设置固定位置。
        

---

### LulaCSpaceTrajectoryGenerator

- **输入**：一系列 C 空间（关节空间）路径点（waypoints），顺序应与 YAML 文件中描述的关节列表一致。
    
- **插值**：基于样条曲线（spline）插值，并保证起末速度为 0。
    
- **时间最优**：在任何时刻，会使速度、加速度或抖动达到饱和，以缩短轨迹总时长。
    
- **输出**：满足 Trajectory 接口的轨迹对象。
    

### LulaTaskSpaceTrajectoryGenerator

- **输入**：一组任务空间（末端执行器空间）目标，以及一个末端执行器的帧名称（必须在 URDF 中定义）。
    
- **线性插值**：如果只给出位置/姿态点，则在任务空间中进行线性插值。
    
- **路径原语**：也可通过 `lula.TaskSpacePathSpec` 类生成圆弧、纯旋转、纯平移等。
    
- **内部流程**：
    
    1. 使用 Lula 运动学求解器，将任务空间轨迹转换为 C 空间轨迹。
        
    2. 交由 `LulaCSpaceTrajectoryGenerator` 生成时间最优的关节轨迹。
        
- **配置**：与 C 空间生成器共用参数，并增加控制任务空间–>关节空间转换方式的额外参数。

---

## **路径规划算法（Path Planner Algorithm）**

路径规划算法输出一系列配置空间（C 空间）路径点（waypoints），这些路径点经线性插值后可产生从起始 C 空间位姿到目标位姿（C 空间或任务空间）的无碰撞路径。Isaac Sim 中的 `PathPlanner` 接口定义了与 NVIDIA Isaac Sim 交互所需的函数。

---

### PathPlanner 接口

#### 1. 关节映射

- **Active Joints（活动关节）**  
    由 `get_active_joints()` 返回，表示规划器将**直接控制**的关节列表及其顺序。规划输出的路径点仅包含这些关节的配置。
    
- **Watched Joints（观测关节）**  
    由 `get_watched_joints()` 返回，表示规划器在构建路径时会“观察”其状态，但**不主动控制**。通常假设它们在规划过程中保持恒定。
    

> **示例**  
> 对于 Franka 机械臂，9 个自由度中只有 7 个旋转关节用于移动手臂；2 个棍柱关节（gripper）不参与移动规划。
> 
> - `get_active_joints()` → 7 个旋转关节
>     
> - `get_watched_joints()` → 空列表
>     

---

#### 2. 世界状态输入

- **Obstacle Adders（障碍物添加）**  
    `PathPlanner` 提供一系列 `add_<object>()` 方法（如 `add_sphere()`、`add_cuboid()` 等），将 USD 舞台上的障碍物包装成可供规划器内部查询的对象。
    
- **更新世界状态**  
    调用 `update_world()` 时，规划器会从已添加的物体中查询其最新位置，用于避障计算。
    

> **注意**：若规划器未实现某种障碍物的添加函数，会抛出警告并忽略该类型障碍物。目前 Lula RRT 支持球体（sphere）、胶囊体（capsule）和立方体（cuboid）。

---

#### 3. 机器人状态输入

- **基座位姿**  
    通过 `set_robot_base_pose(pose)` 设置机器人在世界坐标系中的基座位姿；若不设置，Lula RRT 默认基座置于原点。
    
- **关节状态**  
    调用 `compute_path(active_joint_positions, watched_joint_positions)` 时，需按 `get_active_joints()` 与 `get_watched_joints()` 返回的顺序，传入关节位置（及可选速度）向量。
    

---

#### 4. 输出路径

- `compute_path(...)` 返回一组仅针对活动关节的 C 空间路径点。
    
- **线性插值**：这些点直接线性插值会在 C 空间产生尖角，不宜直接执行；推荐后续配合轨迹生成器做平滑处理。
    

---

### Path Planner Visualizer

`PathPlannerVisualizer` 帮助将规划结果可视化并转换为可直接在 USD 仿真中执行的动作序列（`ArticulationAction`）：

1. **状态采集**  
    从机器人 Articulation 中读取当前关节状态，并按规划器要求排序。
    
2. **路径规划**  
    调用 `PathPlanner.compute_path()` 得到活动关节路径点。
    
3. **线性插值与动作生成**
    
    - 使用 `compute_plan_as_articulation_actions(max_c_space_dist)`，按最大 C 空间步长 `max_c_space_dist` 对路径点进行插值。
        
    - 生成的 `ArticulationAction` 列表即可逐帧下发，驱动机器人沿规划路径运动。
        

---

### NVIDIA Lula RRT 实现

- Lula 提供基于 RRT-Connect 和 JT-RRT 的全局规划算法。
    
- 支持球体、胶囊体与立方体障碍物。
    
- 可与 `PathPlanner` 接口无缝对接，在 Isaac Sim 中快速集成静态环境路径规划。


---
## 路径规划算法（Path Planner Algorithm）

### PathPlanner 接口

#### 关节映射

- **Active Joints（活动关节）**  
    由 `get_active_joints()` 返回，表示规划器将**直接控制**的关节列表及其顺序。规划输出的路径点仅包含这些关节的配置。
    
- **Watched Joints（观测关节）**  
    由 `get_watched_joints()` 返回，表示规划器在构建路径时会“观察”其状态，但**不主动控制**。通常假设它们在规划过程中保持恒定。
    

> **示例**  
> 对于 Franka 机械臂，9 个自由度中只有 7 个旋转关节用于移动手臂；2 个棍柱关节（gripper）不参与移动规划。
> 
> - `get_active_joints()` → 7 个旋转关节
>     
> - `get_watched_joints()` → 空列表
>     

### 世界状态输入

- **Obstacle Adders（障碍物添加）**  
    `PathPlanner` 提供一系列 `add_<object>()` 方法（如 `add_sphere()`、`add_cuboid()` 等），将 USD 舞台上的障碍物包装成可供规划器内部查询的对象。
    
- **更新世界状态**  
    调用 `update_world()` 时，规划器会从已添加的物体中查询其最新位置，用于避障计算。
    

> **注意**：若规划器未实现某种障碍物的添加函数，会抛出警告并忽略该类型障碍物。目前 Lula RRT 支持球体（sphere）、胶囊体（capsule）和立方体（cuboid）。

### 机器人状态输入

- **基座位姿**  
    通过 `set_robot_base_pose(pose)` 设置机器人在世界坐标系中的基座位姿；若不设置，Lula RRT 默认基座置于原点。
    
- **关节状态**  
    调用 `compute_path(active_joint_positions, watched_joint_positions)` 时，需按 `get_active_joints()` 与 `get_watched_joints()` 返回的顺序，传入关节位置（及可选速度）向量。
    

### 输出路径

- `compute_path(...)` 返回一组仅针对活动关节的 C 空间路径点。
    
- **线性插值**：这些点直接线性插值会在 C 空间产生尖角，不宜直接执行；推荐后续配合轨迹生成器做平滑处理。
    

## Path Planner 可视化（Path Planner Visualizer）

### 功能概览

`PathPlannerVisualizer` 帮助将规划结果可视化并转换为可直接在 USD 仿真中执行的动作序列（`ArticulationAction`）：

1. **状态采集**  
    从机器人 Articulation 中读取当前关节状态，并按规划器要求排序。
    
2. **路径规划**  
    调用 `PathPlanner.compute_path()` 得到活动关节路径点。
    
3. **线性插值与动作生成**
    
    - 使用 `compute_plan_as_articulation_actions(max_c_space_dist)` 按最大 C 空间步长对路径点进行插值。
        
    - 生成的 `ArticulationAction` 列表即可逐帧下发，驱动机器人沿规划路径运动。
        

## Lula RRT

### 算法实现

- **C 空间 RRT**：基于 RRT-Connect [2] 实现。
    
- **任务空间 RRT**：基于雅可比转置（Jacobian Transpose）RRT [3] 实现。
    
- **限制**：当前不支持目标姿态（Orientation）规划。
    

### 配置文件

1. **URDF 文件**  
    定义机器人运动学结构、关节与连杆名称，以及每个关节的位置/旋转范围。  
    质量、惯性、网格等属性可省略。
    
2. **机器人描述 YAML 文件**  
    列举所有受控关节（C 空间维度）。  
    指定默认的 C 空间初始配置。  
    可选：为未受控关节设定固定位置。
    
3. **RRT 算法配置 YAML 文件**  
    包含 RRT 的各种参数，如终止条件、探索权重、步长等。  
    可通过 `RRT.set_param()` 在运行时动态修改。
    

### 使用流程

1. 加载 URDF 与机器人描述 YAML，创建内部机器人模型。
    
2. 加载 RRT 配置，调用 `set_param()` 调整规划策略。
    
3. 使用 `compute_path(active_joints, watched_joints)` 生成 C 空间路径点。
    
4. 结合 PathPlannerVisualizer 或轨迹生成器，将路径点转化为平滑轨迹并执行。
    

### 参考文献

- [2] J. J. Kuffner & S. M. LaValle, “RRT-Connect: An efficient approach to single-query path planning,” ICRA 2000.
    
- [3] M. V. Weghe, D. Ferguson & S. S. Srinivasa, “Randomized path planning for redundant manipulators without inverse kinematics,” ICRA Humanoids 2007.


---
## 运动策略算法（Motion Policy Algorithm）

### MotionPolicy 接口

#### 概述

Isaac Sim 中的运动策略（MotionPolicy）是一种感知碰撞的算法，每帧输出动作，驱动单台机器人到达单一任务空间目标。`MotionPolicy` 接口设计简洁易用，可配合 `ArticulationMotionPolicy` 类，仅通过几行代码即可让仿真机器人开始运动。

#### 提供的实现

基于 NVIDIA 开发的 Lula 库中 RMPflow 实现，提供了一个灵活的 `MotionPolicy`（参见 RMPflow）。

---

### 核心概念

#### 活动关节 与 观测关节

- **Active Joints（活动关节）**
    
    - 由 `MotionPolicy.get_active_joints()` 返回，表示策略将**直接控制**的关节列表及顺序。
        
- **Watched Joints（观测关节）**
    
    - 由 `MotionPolicy.get_watched_joints()` 返回，表示策略在规划时会“观察”其状态，但**不主动控制**。
        

> **示例**：Franka 机械臂共有 9 自由度，其中 7 个旋转关节用于移动手臂；2 个棍柱关节（gripper）不参与导航。
> 
> - `get_active_joints()` → 7 个旋转关节
>     
> - `get_watched_joints()` → 空列表
>     

---

### 输入：世界状态

- Isaac Sim 提供 `isaacsim.core.api.objects` 中的一系列对象原语（如球体 `sphere`、圆锥 `cone` 等）。
    
- `MotionPolicy` 为每种对象提供 `add_<object>()` 方法（如 `add_sphere()`），将 USD 舞台上的物体包装后，策略可在 `update_world()` 时查询其位置。
    
- 若某类型添加函数未实现，调用时会发出警告并忽略该物体。当前 RMPflow 支持球体（sphere）、胶囊体（capsule）和立方体（cuboid）。
    

---

### 输入：机器人状态

1. **基座位姿**
    
    - 通过 `MotionPolicy.set_robot_base_pose(pose)` 设置机器人在世界坐标系中的基座位姿。
        
    - 若未调用，RMPflow 默认基座置于舞台原点。
        
2. **关节状态**
    
    - 调用 `MotionPolicy.compute_joint_targets(active_joint_positions, active_joints_velocities, watched_joint_positions, watched_joint_velocities, …)` 时，需按 `get_active_joints()` 和 `get_watched_joints()` 返回的顺序，传入关节位置及速度。
        

---

### 输出：机器人关节目标

- `compute_joint_targets(...)` 返回下一帧的关节位置与速度目标，仅针对活动关节，其数组维度与 `active_joint_positions` 参数一致。
    
- 通过 `ArticulationMotionPolicy` 帮助类可自动完成关节映射，无需手动处理顺序差异。
    
- 建议在标准 PD 控制器中使用：
    
    ```
    kp * (position_target - position) + kd * (velocity_target - velocity)
    ```
    
- 若仅需单一目标，可将另一目标设为零或当前关节状态以实现纯阻尼或忽略位置项。
    

---

### Articulation Motion Policy

- **初始化**：传入机器人 `Articulation` 对象与 `MotionPolicy` 实例。
    
- **主要方法**：`get_next_articulation_action()`
    
    1. 从 `Articulation` 读取当前关节状态。
        
    2. 调用 `compute_joint_targets()` 获取活动关节目标。
        
    3. 将返回的目标映射回完整关节列表，生成 `ArticulationAction`。
        
    4. 使用 `Articulation.get_articulation_controller().apply_action()` 下发动作。
        

> **Franka 示例**：
> 
> - `MotionPolicy` 控制 7 个关节，返回 7 维目标向量。
>     
> - `ArticulationMotionPolicy` 将其映射为 9 维（需动作处填值，非控制关节处填 `None`）。
>     

---

### Motion Policy Controller

`MotionPolicyController` 将 `MotionPolicy` 封装为 `isaacsim.core.api.controllers.BaseController`，供机器人扩展（如 `isaacsim.robot.manipulators.franka.controllers.RMPFlowController`）调用。

使用示例：

```python
from isaacsim.robot.manipulators.franka.controllers import RMPFlowController

controller = RMPFlowController("rmpflow", ...)
actions = controller.forward(...)
```