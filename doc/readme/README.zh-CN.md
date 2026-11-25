# isaac_physics_test

**[English](../../README.md)** | **[繁體中文](README.zh-TW.md)** | **[简体中文](README.zh-CN.md)** | **[日本語](README.ja.md)**

Isaac Sim 物理实验及叉车专用控制脚本，从 [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) 按照仓库拆分方案（[isaac#309](https://github.com/ycpss91255-lab/isaac/issues/309)）提取而来。

本仓库包含：

- **15 个物理实验驱动脚本**（`src/script/exp_*.py`），涵盖 L2 kinematic、L2.5 高刚性驱动、下垂/挠度、轨迹追踪、视觉指标验收等
- **叉车块状模型驱动脚本**（`forklift_blocky_driver_wip.py`）及 PoC #59 关节绕过方案（`poc59_articulation_bypass_wip.py`）
- **SIGSEGV 复现脚本**，用于 PhysX articulation + loop-joint 崩溃（`repro_artic_kinematic_loopjoint_sigsegv.py`）
- **托盘/碰撞验证脚本**（`test/verify_*.py`），用于叉齿插入、凸分解口袋保留及真实资产碰撞探测
- **4 份 ADR**：0003（双轨仿真策略）、0004（Model A-hybrid 叉车块状模型）、0008（L2/L3 物理层级词汇）、0021（关节模型上的物理层级实现）
- **实验结果**（JSON / log / PNG）及可复现验收工具（`doc/experiments/REPRODUCE.md`）
- **共享渲染辅助工具**（`viz_render.py`）及收敛渲染设置单元测试

## 前置条件

这些脚本在基于 [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) 构建的 **Isaac Sim 容器内**运行。该容器提供 Isaac Sim 6.2.0、Isaac Lab v3.0.0 以及 `isaac_devkit` 框架。

机器人模型（USD）位于私有仓库 [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model)。叉车块状模型 USD 位于该仓库的 `usd/origin/forklift_blocky/`；openbase 模型位于 `usd/origin/omwr/` 和 `usd/origin/openbase/`。

## 仓库结构

```
isaac_physics_test/
├── README.md                           # 本文件
├── LICENSE                             # Apache-2.0
├── SOURCE.md                           # 来源：isaac@c068e3a 中的原始路径
├── doc/
│   ├── readme/                         # 多语言 README
│   │   ├── README.zh-TW.md
│   │   ├── README.zh-CN.md
│   │   └── README.ja.md
│   └── experiments/
│       └── REPRODUCE.md                # 可复现验收工具
├── src/
│   ├── script/                         # 实验驱动脚本
│   │   ├── exp_drive_limits.py
│   │   ├── exp_drive_precision_load.py
│   │   ├── exp_l25_dynamic_interaction.py
│   │   ├── exp_l25_sag_sweep.py
│   │   ├── exp_l2_base_carries_arm.py
│   │   ├── exp_l2_carry_speed_limit.py
│   │   ├── exp_l2_kinematic_substitution.py
│   │   ├── exp_l2_loop_joint_boundary.py
│   │   ├── exp_l2_push_dynamic.py
│   │   ├── exp_modela_usd_stability.py
│   │   ├── exp_multijoint_sag.py
│   │   ├── exp_revolute_gain_scaling.py
│   │   ├── exp_sag_livestream.py
│   │   ├── exp_traj_tracking.py
│   │   ├── exp_visual_metric_acceptance.py
│   │   ├── forklift_blocky_driver_wip.py
│   │   ├── poc59_articulation_bypass_wip.py
│   │   ├── repro_artic_kinematic_loopjoint_sigsegv.py
│   │   ├── velocity_test.py
│   │   ├── viz_render.py               # 共享 RTX 渲染 + MP4 辅助工具
│   │   └── standalone_common.py        # 共享 standalone 辅助工具（从 isaac 复制）
│   └── doc/
│       └── adr/
│           ├── 0003-two-track-simulation-strategy.md
│           ├── 0004-model-a-hybrid-forklift-block-model.md
│           ├── 0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md
│           └── 0021-physics-level-realization-on-articulation-models.md
└── test/
    ├── integration/
    │   ├── test_openbase_l2_smoke.py
    │   └── test_openbase_l2_stability.py
    ├── unit/
    │   └── pytest/
    │       └── test_render_settings.py
    ├── gen_review_pdf.py               # 6.0.1 物理重新验证审查 PDF
    ├── verify_decomp_pocket.py
    ├── verify_pallet_entry_map.py
    ├── verify_real_asset_collision.py
    ├── verify_real_pallet_insertion.py
    ├── .prove-*.json                   # 验收证明结果
    ├── .reval-6.2.0/                   # 6.2.0 重新验证结果
    ├── .l2-*.json, .l25-*.json         # L2/L2.5 实验结果
    ├── .sag-*.json                     # 下垂/挠度扫描结果
    ├── .drive-*.json                   # 驱动限制/精度结果
    ├── .proto/                         # 下垂原型实验
    └── ...                             # 其他结果 JSON/log/PNG 文件
```

## 运行实验

所有实验需要 Isaac Sim 容器。完整的可复现验收工具请参阅 [`doc/experiments/REPRODUCE.md`](doc/experiments/REPRODUCE.md)。

快速开始（假设 isaac 容器已构建并正在运行）：

```bash
# 将本仓库挂载到容器内 /home/<user>/work/physics_test/
# （或通过符号链接接入工作区）

# 示例：运行下垂扫描实验
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/src/script/exp_l25_sag_sweep.py \
    --out /home/<user>/work/physics_test/test/.sag-sweep.json

# 示例：生成审查 PDF
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/test/gen_review_pdf.py \
    --test-dir /home/<user>/work/physics_test/test \
    --out /home/<user>/work/doc/6.0.1_physics_revalidation_review.pdf
```

## ADR

| ADR | 标题 | 状态 |
|-----|------|------|
| [0003](src/doc/adr/0003-two-track-simulation-strategy.md) | Two-Track Simulation Strategy (Model A vs Model B) | 已采纳 |
| [0004](src/doc/adr/0004-model-a-hybrid-forklift-block-model.md) | Model A-Hybrid Forklift Block Model | 已采纳 |
| [0008](src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md) | L2/L3 Physics Level Vocabulary and Coexistence Rules | 已被 isaac ADR-0017 取代（通用词汇在该处重新定义） |
| [0021](src/doc/adr/0021-physics-level-realization-on-articulation-models.md) | Physics-Level Realization on Articulation Models (L2/L2.5/L3) | 已采纳，扩展 ADR-0008 |

## 相关仓库

| 仓库 | 角色 |
|------|------|
| [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) | Isaac Sim Docker 环境 + `isaac_devkit` 框架 |
| [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) | 机器人 USD/URDF 模型（私有） |

## 许可证

[Apache-2.0](../../LICENSE)
