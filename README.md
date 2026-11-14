# Marine Robots

水下机器人相关成果的发布仓库。本仓库**不镜像完整工程**，源码包以 Release 资产形式提供。

## ROV 水下仿真 · 早期最小版 v0.1.1-early

基于 **ROS 2 Humble + Gazebo Harmonic + ArduSub SITL** 的 8 推进器 ROV 仿真环境，已剥离后续研究模块和预编译 Gazebo 插件。

- **方式 B — ROV（自研）**：本版本主推，遥控器 / MAVLink 驾驶，ArduSub 飞控闭环。
- **方式 A — AUV（自研）**：自主水下航行器方向的 ROS 2 控制链路，源码包保留，本 ROV 说明不展开。
- **方式 C**：已停用，不再提供。

- 下载：[v0.1.1-early](https://github.com/Shadowlover-wdw/marine-robots/releases/tag/v0.1.1-early)
- 包含：`rov_ws`（`rov_control` / `rov_description` / `rov_gazebo`）、`run.sh`、`README.md`、`Deploy.md`、`CONTROL_PIPELINE.md`
- 不包含：`rov_drl`、`rov_pinn`、`rov_SINS`、`rov_gz_eval`，以及预编译的 Gazebo `.so` 插件
- 自研与第三方：ROV 模型、物理参数配置、推力器布局与 SITL 联调脚本为自研；ROS 2、Gazebo，以及 ArduPilot / ArduSub SITL、ArduPilotPlugin 为第三方依赖（插件需自行编译）

### 快速运行（ROV / 方式 B）

1. 安装依赖：ROS 2 Humble、Gazebo Harmonic、ArduSub SITL、MAVProxy（详见包内 `Deploy.md`）。
2. 下载并解压到 `~/ROS2_Gazebo_Simulator`（与包内文档路径一致）：

   ```bash
   unzip ROV_simulation_early-v0.1.1.zip -d ~
   mv ~/ROV_simulation_early-v0.1.1 ~/ROS2_Gazebo_Simulator
   ```

3. 构建：

   ```bash
   cd ~/ROS2_Gazebo_Simulator/rov_ws
   source /opt/ros/humble/setup.bash
   colcon build --packages-select rov_gazebo rov_description rov_control
   source install/setup.bash
   ```

4. 运行：

   ```bash
   cd ~/ROS2_Gazebo_Simulator
   ./run.sh B          # 方式 B：ROV（ArduSub SITL + ArduPilotPlugin）
   ./run.sh stop       # 停止并清理
   ```

模式 B 所需的 `libArduPilotPlugin.so` 不在包内，编译步骤见 `Deploy.md` 第 4 节。无 GUI 加 `--headless`，软件渲染加 `--software`。

### 版本说明

- **v0.1.1-early**：文档收敛为 ROV（方式 B）；方式 A 标注为 AUV（自研）且不展开；方式 C 从文档移除。源码主体与 v0.1.0-early 相同，仅调整文档与包内说明。
- **v0.1.0-early**：首个早期最小版。
