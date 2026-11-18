# Marine Robots

水下机器人相关成果的发布仓库。源码包以 Release 资产形式提供。

## ROV 水下仿真 · 早期最小版 v0.1.3-early

基于 **ROS 2 Humble + Gazebo Harmonic + ArduSub SITL** 的 8 推进器 ROV 仿真环境，聚焦水下仿真与公版 ArduPilot / QGC 联调。飞控使用**公版 ArduPilot（ArduSub SITL）**，地面站使用**公版 QGroundControl**，无需定制飞控或地面站。

- 下载：[v0.1.3-early](https://github.com/Shadowlover-wdw/marine-robots/releases/tag/v0.1.3-early)
- 包含：`rov_ws`（`rov_control` / `rov_description` / `rov_gazebo`）、`run.sh`、`README.md`、`Deploy.md`、`CONTROL_PIPELINE.md`
- 自研与第三方：ROV 模型、物理参数配置、推力器布局与 SITL 联调脚本为自研；ROS 2、Gazebo、ArduPilot / ArduSub SITL、ArduPilotPlugin、QGroundControl 为公版第三方依赖（插件需自行编译）

### 快速运行

1. 安装依赖：ROS 2 Humble、Gazebo Harmonic、MAVProxy（详见包内 `Deploy.md`）。
2. 下载并解压到 `~/ROS2_Gazebo_Simulator`（与包内文档路径一致）：

   ```bash
   unzip ROV_simulation_early-v0.1.3.zip -d ~
   mv ~/ROV_simulation_early-v0.1.3 ~/ROS2_Gazebo_Simulator
   ```

3. 构建：

   ```bash
   cd ~/ROS2_Gazebo_Simulator/rov_ws
   source /opt/ros/humble/setup.bash
   colcon build --packages-select rov_gazebo rov_description rov_control
   source install/setup.bash
   ```

4. 安装公版 ArduPilot 与插件：

   ```bash
   git clone https://github.com/ArduPilot/ardupilot.git ~/my_ardupilot
   cd ~/my_ardupilot
   git submodule update --init --recursive
   ./waf configure --board sitl
   ./waf sub
   ```

   `libArduPilotPlugin.so` 编译步骤见包内 `Deploy.md` 第 5 节。

5. 运行：

   ```bash
   cd ~/ROS2_Gazebo_Simulator
   ./run.sh B          # 启动 ROV（ArduSub SITL + ArduPilotPlugin）
   ./run.sh stop       # 停止并清理
   ```

6. 启动公版 QGroundControl，连接 `udp://127.0.0.1:14550`。安装与遥控校准见 `Deploy.md` 第 6 节。

无 GUI 加 `--headless`，软件渲染加 `--software`。

### 已知问题：QGC 遥控升沉时 ROV 翻覆（公版 ArduSub 默认机架）

**现象**：QGC 虚拟手柄前后、横移、左旋/右旋都正常，但一给上升/下潜，ROV 就翻。

**原因**：该仿真环境使用 8 推进器（4 水平 + 4 垂直），而公版 ArduSub 的 `FRAME_CONFIG` 默认是 `1`（VECTORED，6 电机），只启用 `SERVO1..6`。升沉时只驱动 5/6 两个前置垂直桨，推力线不过 CoG，满推约 `2×29.43 N × 0.136 m ≈ 8 N·m` 俯仰力矩；VECTORED 所有电机的 pitch 因子都是 `0`，飞控无法反打，所以一升沉就翻。浮心只比 CoG 高 8.8 mm（最大恢复力矩 0.76 N·m），会加重空载沉底侧躺，但不是本次翻覆的主因。

**修复**：QGC → Parameters，把 `FRAME_CONFIG` 改为 `2`（Vectored_6DOF），并确认 `SERVO7_FUNCTION=39`、`SERVO8_FUNCTION=40`，重启 SITL。如需固化，可在 SITL 的 `--defaults` 参数文件（`ardusub_modeb.parm`）中加入：

```ini
FRAME_CONFIG 2
SERVO7_FUNCTION 39
SERVO8_FUNCTION 40
```

**自查方式**：

1. QGC → Parameters 搜索 `FRAME_CONFIG`，应为 `2`；再查 `SERVO7_FUNCTION`/`SERVO8_FUNCTION`，应分别为 `39`/`40`。若仍是 `1`/`0`，说明参数没写入或 SITL 没重启。
2. 解锁后给很小的升沉指令，或用 QGC Motor Test 单独测试 5–8 号电机：4 个垂直推进器都应参与输出。若只有 5/6 转、7/8 不转，说明机架配置仍未生效。
3. 若 5–8 都参与但升沉仍明显低头/抬头，再查 `MOT_5_DIRECTION`～`MOT_8_DIRECTION` 和桨叶方向；若只是上升/下潜方向相反，改 QGC 摇杆反向或 `RC3_REVERSED`，不要改电机方向。

修复只改公版 ArduSub 参数。

### 版本说明

- **v0.1.3-early**：文档保持 ROV 单一路径，补充公版 ArduPilot / QGroundControl 完整部署与运行步骤。
- **v0.1.2-early**：文档收敛为 ROV。
- **v0.1.1-early**：早期文档修订版。
- **v0.1.0-early**：首个早期最小版。
