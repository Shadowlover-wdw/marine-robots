# Marine Robots

水下机器人相关成果的发布仓库。本仓库**不镜像完整工程**，源码包以 Release 资产形式提供。

## ROV 水下仿真 · 早期最小版 v0.1.2-early

基于 **ROS 2 Humble + Gazebo Harmonic + ArduSub SITL** 的 8 推进器 ROV 仿真环境，已剥离后续研究模块和预编译 Gazebo 插件。飞控使用**公版 ArduPilot（ArduSub SITL）**，地面站使用**公版 QGroundControl**，无需定制飞控或地面站。

- 下载：[v0.1.2-early](https://github.com/Shadowlover-wdw/marine-robots/releases/tag/v0.1.2-early)
- 包含：`rov_ws`（`rov_control` / `rov_description` / `rov_gazebo`）、`run.sh`、`README.md`、`Deploy.md`、`CONTROL_PIPELINE.md`
- 不包含：`rov_drl`、`rov_pinn`、`rov_SINS`、`rov_gz_eval`，以及预编译的 Gazebo `.so` 插件
- 自研与第三方：ROV 模型、物理参数配置、推力器布局与 SITL 联调脚本为自研；ROS 2、Gazebo、ArduPilot / ArduSub SITL、ArduPilotPlugin、QGroundControl 为公版第三方依赖（插件需自行编译）

### 快速运行

1. 安装依赖：ROS 2 Humble、Gazebo Harmonic、MAVProxy（详见包内 `Deploy.md`）。
2. 下载并解压到 `~/ROS2_Gazebo_Simulator`（与包内文档路径一致）：

   ```bash
   unzip ROV_simulation_early-v0.1.2.zip -d ~
   mv ~/ROV_simulation_early-v0.1.2 ~/ROS2_Gazebo_Simulator
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

### 版本说明

- **v0.1.2-early**：文档收敛为 ROV，补充公版 ArduPilot / QGroundControl 完整部署与运行步骤。
- **v0.1.1-early**：早期文档修订版。
- **v0.1.0-early**：首个早期最小版。
