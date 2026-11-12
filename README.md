# Marine Robots

水下机器人相关成果的发布仓库。本仓库**不镜像完整工程**，源码包以 Release 资产形式提供。

## ROV 水下仿真 · 早期最小版 v0.1.0-early

基于 **ROS 2 Humble + Gazebo Harmonic + ArduSub SITL** 的 8 推进器 ROV 仿真环境，已剥离后续研究模块和预编译 Gazebo 插件。

- 下载：[v0.1.0-early](https://github.com/Shadowlover-wdw/marine-robots/releases/tag/v0.1.0-early)
- 包含：`rov_ws`（`rov_control` / `rov_description` / `rov_gazebo`）、`run.sh`、`README.md`、`Deploy.md`、`CONTROL_PIPELINE.md`
- 不包含：`rov_drl`、`rov_pinn`、`rov_SINS`、`rov_gz_eval`，以及预编译的 Gazebo `.so` 插件

### 快速运行

1. 安装依赖：ROS 2 Humble、Gazebo Harmonic、ArduSub SITL、MAVProxy（详见包内 `Deploy.md`）。
2. 下载并解压到 `~/ROS2_Gazebo_Simulator`（与包内文档路径一致）：

   ```bash
   unzip ROV_simulation_early-v0.1.0.zip -d ~
   mv ~/ROV_simulation_early-v0.1.0 ~/ROS2_Gazebo_Simulator
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
   ./run.sh A          # 模式 A：纯 Gazebo + ROS2 控制，无需额外插件
   ./run.sh B          # 模式 B：ArduSub SITL + ArduPilotPlugin（需先编译插件）
   ./run.sh C          # 模式 C：SITL 原生物理 + 可视化（停更/备选）
   ./run.sh stop       # 停止并清理
   ```

模式 B 所需的 `libArduPilotPlugin.so` 不在包内，编译步骤见 `Deploy.md` 第 4 节。
