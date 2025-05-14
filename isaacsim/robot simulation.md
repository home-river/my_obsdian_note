
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
