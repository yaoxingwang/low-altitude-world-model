# 低空世界模型数据调研核验记录

核验日期：2026-10-10。范围：官方项目页、数据文档、仓库、论文及持久化元数据。未下载大型数据集、未安装或运行仿真平台。以下研究用途是本次分析，不是原作者对本项目的背书。

## UrbanScene3D

- 官方来源：https://vcc.tech/UrbanScene3D/
- Liqiang Lin 等，Capturing, Reconstructing, and Simulating: the UrbanScene3D Dataset，ECCV 2022。
- 官方页列出超过 128k 图像、16 个真实或合成场景、高精度 LiDAR、仿真资产和评价工具；全量约 1.43 TB。
- 页面说明 UE / AirSim 场景可以生成深度、包围框及分割等数据；不代表全部模态已配套下载。
- 用途：城市几何、场景重建、可通行性标签构建候选。未核实可直接用于动作条件训练的连续控制日志。
- 使用条款：非商业，需引用，禁止再分发或再分发修改版本。未下载任何整包。

## TartanAir V2

- 官方主页：https://tartanair.org/
- 字段文档：https://tartanair.org/modalities.html
- 文档明确：12 个同步视点的 RGB、深度、语义及相机位姿；LiDAR 可由深度采样；IMU 由相机位姿插值与求导生成；另有占据地图。
- 相机位姿为平移与四元数，采用 NED 坐标；具体接口接入仍需核验相机到机体外参。
- 文档未列出配套飞行控制动作。合成运动和派生 IMU 不等于真实飞行动力学测量。
- 用途：几何、语义、遮挡、视觉状态估计。不能独立证明交互控制有效。
- 数据许可 CC BY 4.0，工具 MIT；未下载整包。

## EuRoC MAV

- 官方新入口：https://ethz-asl.github.io/datasets/euroc-mav/
- 数据 DOI：https://doi.org/10.3929/ethz-b-000690084
- Burri 等，The EuRoC micro aerial vehicle datasets，IJRR 2016；论文 DOI：10.1177/0278364915620033。
- 官方描述包括双目图像 20 FPS、IMU 200 Hz、标定、运动与结构真值。数据页没有列控制指令。
- 部分真值源是 6D 动捕，另一些来自激光跟踪；使用时逐序列核查，不假定所有序列监督完全相同。
- 用途：真实视觉惯性状态估计与观测误差校核。不能代替动作分支试验。
- 数据迁移至 ETH 长期存储，未下载、未核验整包许可证细节。

## UZH-FPV

- 官方主页：https://fpv.ifi.uzh.ch/
- 字段与下载页：https://fpv.ifi.uzh.ch/datasets/
- Delmerico 等，Are We Ready for Autonomous Drone Racing? The UZH-FPV Drone Racing Dataset，ICRA 2019。
- 官方提供 rosbag 与文本格式。列明图像、IMU、事件和位姿真值消息；未列控制指令。
- 部分序列没有公开真值，需选择标记 Ground Truth Public = yes 的序列。
- 许可 CC BY-NC-SA 3.0；单个图像包通常为数百 MB 至 GB。
- 用途：高速状态估计及真实观测泛化，不是多主体交互决策数据。
- 小样本已成功读取：官方列出的 indoor_forward_3 原始真值 ZIP，75,706 字节。ZIP 内是 indoor_forward_3/leica.txt，1,667 行，属于 Leica 仪器原始日志；不是已经对齐图像的标准位姿序列。
- 样本地址：https://rpg.ifi.uzh.ch/datasets/uzh-fpv-newer-versions/raw/indoor_forward_3.zip
- 仅在内存读取 ZIP 并检查文件结构，没有下载图像整包，也没有完成轨迹解析或评测。

## Blackbird

- 官方仓库：https://github.com/mit-fast/Blackbird-Dataset
- 论文：https://arxiv.org/abs/1810.01987
- IJRR 版本 DOI：https://doi.org/10.1177/0278364920908331
- 论文描述真实飞行 IMU、动捕与电机转速，图像由 FlightGoggles 根据真实轨迹合成。
- 仓库说明可以按序列下载，并明确部分预渲染序列当前不可用。
- 已读取仓库消息定义：MotorPWM 含 header、uint16[] pwm；MotorRPM 含 header、time[] sample_stamp、float32[] rpm。
- 字段证明消息格式存在，不等于已核实每个包包含连续完整的动作与状态。
- 原下载域名 blackbird-dataset.mit.edu 本次 DNS 解析失败。全量说明约 4.9 TB，不建议作为启动依赖。
- 用途：在确认可下载序列和同步关系后，用作动力学及动作相关响应校核候选。

## WindSeer

- 论文：https://doi.org/10.1038/s41467-024-47778-4
- 全文核验：https://europepmc.org/articles/PMC11045725
- 数据 DOI：https://doi.org/10.3929/ethz-b-000658323
- DataCite 元数据：https://api.datacite.org/dois/10.3929/ethz-b-000658323
- 代码：https://github.com/ethz-asl/WindSeer
- 正确论文题名：WindSeer: real-time volumetric wind prediction over complex terrain aboard a small uncrewed aerial vehicle。
- Achermann 等，Nature Communications，2024。不是 Nature 主刊。ETH 汇总页的简称及占位 BibTeX 不作为正式引文依据。
- 已查全文：训练基于稳态 RANS CFD，输出时间平均三维风和湍动能；使用测风塔数据及多架固定翼 sUAV 飞行观测评价。
- 论文明确讨论动态风与训练静态流的差异，不支持直接宣称完整瞬态阵风预测；部分飞行数据上风速幅值未优于平均风基线。
- 跨地形验证与稀疏观测重建可作为实验依据。不能把该风数据直接与其他城市图像配对为真实联合记录。
- DataCite 元数据登记了 2024、ETH Zurich、ZIP / HDF5 等以及约 219.3 GB。数据站本次返回限流页面，未核验整包或样本。
- 代码中存在风预测训练工具、CFD 生成流程和基于 OMPL 的规划比较工具。未运行。

## FlightBench

- 文档：https://thu-uav.github.io/FlightBench/
- 仓库：https://github.com/thu-uav/FlightBench
- README 引用：https://arxiv.org/abs/2406.05687；未将其未经核实的正式会刊归属写入报告。
- 基于 Flightmare，支持 RGBD、状态与多级动作接口，包含 Fast-Planner、EGO-Planner、TGK-Planner 等对照方法。
- 文档明确提供状态型和图像型训练接口，含 collective thrust and body rates 动作；也有速度和轨迹控制接口。
- 自定义 Unity 脚本可实现运动障碍物；示例为轨道运动，不代表已有响应式多主体基准。
- 文档说明可记录 ROS bags，但本文要求的完整分支状态恢复与全部日志字段仍需实现和测试。
- 环境要求包括 Ubuntu 20.04、ROS Noetic、NVIDIA GPU；代码 GPLv3。未安装、未执行。

## AvoidBench

- 仓库：https://github.com/tudelft/AvoidBench
- 官方 README 提供森林、室内场景，障碍分布与复杂度设置，双目、深度、真值状态，以及位置、速度、参考状态、body rates / thrust 控制接口。
- 状态含机体系速度；对接时需与 NED、ENU 或世界坐标约定一致。
- 文档建议使用带噪或双目估计深度，不应把无噪真值深度的结果直接解释为真实感知效果。
- 基于 Flightmare，代码 MIT，文档使用 Ubuntu 20.04 / ROS Noetic / Unity / NVIDIA GPU。未安装、未执行。
- 适合基础障碍复杂度扫描。与 FlightBench 共享组件，不构成天然独立的物理真值。

## 研究路线判断

1. 先用公开图像、几何与状态数据验证表征能力。
2. 用可控仿真补充同步状态—动作—后继状态，并实现可重复动作分支。
3. 加入响应式主体后才能检验预测—规划耦合。
4. WindSeer 作为风场分模块实验来源，真实动态阵风与机体响应仍有数据缺口。
5. 用真实日志校核观测和动力学，另行设计载体示范；不能用单类多旋翼试验推断全部 eVTOL 有效。
