# isaac_physics_test

**[English](../../README.md)** | **[繁體中文](README.zh-TW.md)** | **[简体中文](README.zh-CN.md)** | **[日本語](README.ja.md)**

從 [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) 依照 repo 拆分計畫（[isaac#309](https://github.com/ycpss91255-lab/isaac/issues/309)）獨立出來的 Isaac Sim 物理實驗與堆高機專用控制腳本。

本 repo 包含：

- **15 個物理實驗驅動程式**（`src/script/exp_*.py`），涵蓋 L2 kinematic、L2.5 高剛性驅動、下垂/撓度、軌跡追蹤、視覺指標驗收等
- **堆高機方塊模型驅動程式**（`forklift_blocky_driver_wip.py`）及 PoC #59 articulation bypass（`poc59_articulation_bypass_wip.py`）
- **SIGSEGV 重現腳本**，用於 PhysX articulation + loop-joint 當機問題（`repro_artic_kinematic_loopjoint_sigsegv.py`）
- **棧板 / 碰撞驗證腳本**（`test/verify_*.py`），包含牙叉插入、convex decomposition 凹槽保留、真實資產碰撞探測
- **4 份 ADR**：0003（雙軌模擬策略）、0004（Model A-hybrid 堆高機方塊模型）、0008（L2/L3 物理層級詞彙）、0021（articulation 模型上的物理層級實現）
- **實驗結果**（JSON / log / PNG）及可重現驗收工具（`doc/experiments/REPRODUCE.md`）
- **共用渲染輔助工具**（`viz_render.py`）及 converged-render-settings 單元測試

## 前置需求

這些腳本需在從 [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) 建置的 **Isaac Sim 容器內**執行。該容器提供 Isaac Sim 6.2.0、Isaac Lab v3.0.0 及 `isaac_devkit` 框架。

機器人模型（USD）位於私有 repo [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model)。堆高機方塊模型 USD 在該 repo 的 `usd/origin/forklift_blocky/`；openbase 模型在 `usd/origin/omwr/` 和 `usd/origin/openbase/`。

## 目錄結構

```
isaac_physics_test/
├── README.md                           # 本檔案（英文版）
├── LICENSE                             # Apache-2.0
├── SOURCE.md                           # 來源追溯：isaac@c068e3a 中的原始路徑
├── doc/
│   ├── readme/                         # 多語系 README
│   │   ├── README.zh-TW.md
│   │   ├── README.zh-CN.md
│   │   └── README.ja.md
│   └── experiments/
│       └── REPRODUCE.md                # 可重現驗收工具
├── src/
│   ├── script/                         # 實驗驅動程式
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
│   │   ├── viz_render.py               # 共用 RTX 渲染 + MP4 輔助工具
│   │   └── standalone_common.py        # 共用 standalone 輔助工具（從 isaac 複製）
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
    ├── gen_review_pdf.py               # 6.0.1 物理再驗證審查 PDF
    ├── verify_decomp_pocket.py
    ├── verify_pallet_entry_map.py
    ├── verify_real_asset_collision.py
    ├── verify_real_pallet_insertion.py
    ├── .prove-*.json                   # 驗收證明結果
    ├── .reval-6.2.0/                   # 6.2.0 再驗證結果
    ├── .l2-*.json, .l25-*.json         # L2/L2.5 實驗結果
    ├── .sag-*.json                     # 下垂/撓度掃描結果
    ├── .drive-*.json                   # 驅動限制/精度結果
    ├── .proto/                         # 原型下垂實驗
    └── ...                             # 其他結果 JSON/log/PNG 檔案
```

## 執行實驗

所有實驗皆須在 Isaac Sim 容器內執行。完整的可重現驗收工具請參閱 [`doc/experiments/REPRODUCE.md`](../../doc/experiments/REPRODUCE.md)。

快速開始（假設 isaac 容器已建置並正在執行）：

```bash
# 將本 repo 掛載到容器內的 /home/<user>/work/physics_test/
# （或以 symlink 連入工作區）

# 範例：執行下垂掃描實驗
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/src/script/exp_l25_sag_sweep.py \
    --out /home/<user>/work/physics_test/test/.sag-sweep.json

# 範例：產生審查 PDF
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/test/gen_review_pdf.py \
    --test-dir /home/<user>/work/physics_test/test \
    --out /home/<user>/work/doc/6.0.1_physics_revalidation_review.pdf
```

## ADR

| ADR | 標題 | 狀態 |
|-----|------|------|
| [0003](../../src/doc/adr/0003-two-track-simulation-strategy.md) | Two-Track Simulation Strategy (Model A vs Model B) | Accepted |
| [0004](../../src/doc/adr/0004-model-a-hybrid-forklift-block-model.md) | Model A-Hybrid Forklift Block Model | Accepted |
| [0008](../../src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md) | L2/L3 Physics Level Vocabulary and Coexistence Rules | Superseded by isaac ADR-0017 (generic vocabulary restated there) |
| [0021](../../src/doc/adr/0021-physics-level-realization-on-articulation-models.md) | Physics-Level Realization on Articulation Models (L2/L2.5/L3) | Accepted, extends ADR-0008 |

## 相關 Repository

| Repository | 角色 |
|---|---|
| [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) | Isaac Sim Docker 環境 + `isaac_devkit` 框架 |
| [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) | 機器人 USD/URDF 模型（私有） |

## License

[Apache-2.0](../../LICENSE)
