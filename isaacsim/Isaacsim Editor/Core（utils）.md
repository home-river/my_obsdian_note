| 子模块                  | 功能简介                                                    |
| -------------------- | ------------------------------------------------------- |
| **Articulations**    | 操作和查询关节系统（Articulation）的便捷函数；如 FK/IK、关节极限、动力学批量计算等      |
| **Bounds**           | 计算与操作 Axis‑Aligned / Oriented Bounding Box、包围球等空间包围体的工具 |
| **Camera utils**     | 相机与传感器相关工具：投影/去投影、生成视图矩阵、深度与 RGB 读写等                    |
| **Carb**             | 对 NVIDIA Carb (日志、配置、事件系统) 的轻量封装，便于在脚本中调用               |
| **Collisions**       | 生成/检测碰撞形体、查询最近点、接触信息和距离场等辅助函数                           |
| **Constants**        | 常量与枚举集合（物理/渲染/几何的默认值、标志位、单位换算因子等）                       |
| **Distance Metrics** | 两物体/姿态/向量的距离、误差、相似度指标的统一计算接口                            |
| **Interops**         | 与外部库（如 NumPy ↔ Torch、USD ↔ Numpy）的数据互操作与格式转换            |
| **Extensions**       | 用于安装、启用、检查 Omniverse 扩展的辅助 API                          |
| **Math**             | 矢量、矩阵、四元数、数值稳定性工具，随机取样与线性代数基础实现                         |
| **Mesh**             | 网格加载、采样、法线/切线计算、拓扑信息解析与修改                               |
| **Nucleus**          | 连接、浏览、上传/下载到 Omniverse Nucleus 服务器的便捷包装                 |
| **Numpy**            | 针对 NumPy 数组的额外实用函数（批量坐标变换、装配结构化 dtype 等）                |
| **Physics**          | 物理属性读写、参数归一化、力/惯量/关节驱动计算等通用工具                           |
| **Prims**            | USD Prim 查询、创建、遍历、属性增删改的高阶封装                            |
| **Random**           | 各类概率分布采样、随机变换生成、可重复随机种子管理                               |
| **Render Product**   | 创建和操纵“渲染产物”(Render Product)；控制分辨率、AOV、输出路径等             |
| **Rotations**        | 欧拉角、四元数、旋转矩阵之间的互转及插值、批量姿态处理                             |
| **Semantics**        | 读写 USD 语义标签、实例/类别 ID、分割掩码等语义信息                          |
| **Stage**            | 对当前 USD Stage 的全局操作：时间码、单位比例、层管理等                       |
| **String**           | 字符串与路径处理工具：正则、命名规范、URI 拼接等                              |
| **Transformations**  | 位姿/缩放/齐次矩阵的组合拆分、局部↔世界变换、批量运算                            |
| **Torch**            | PyTorch 张量与 USD/NumPy 数据交互、GPU 加速批量计算工具                 |
| **Types**            | 常用自定义类型与 TypedDict、数据结构定义，统一类型声明                        |
| **Viewports**        | 创建、配置、捕获 Viewport 视图（分辨率、相机、AOV）及图像导出                   |