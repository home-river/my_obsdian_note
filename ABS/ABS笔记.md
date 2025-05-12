# 轮速测量：

## 频率法

原路为采样固定时间窗口内的脉冲个数，误差主要来源于±1字误差，高速状态下误差减小。



## 周期法

测量两个脉冲之间的上升沿时间戳间隔，低速状态下误差较小。


周期法把 **相邻两次轮速脉冲的时间间隔** tpt_p 当作核心测量量。若齿圈每转有 NsN_s 个等距齿（即每个脉冲对应机械转角 $Δθ=2π/Ns\Delta\theta = 2\pi/N_s$），则：


$$
\boxed{\; \omega \;=\; \frac{\Delta\theta}{t_p} \;=\; \frac{2\pi}{N_s\,t_p} \;} \qquad\text{(角速度, rad·s⁻¹)}
$$

$$
\boxed{\; v \;=\; r\,\omega \;=\; \frac{2\pi\,r}{N_s\,t_p} \;} \qquad\text{(线速度, m·s⁻¹，\(r\) 为轮胎有效半径)}
$$




> **k 齿平均**（降低齿距抖动）：  
> 若一次跨越 kk 个脉冲，使用时间间隔 tk ⁣= ⁣ti+k−tit_{k}\!=\!t_{i+k}-t_{i}，则
> 
> ω  =  2π kNs tk,v  =  2π r kNs tk.\omega \;=\; \frac{2\pi\,k}{N_s\,t_{k}},\qquad v \;=\; \frac{2\pi\,r\,k}{N_s\,t_{k}}.