# isaac_physics_test

**[English](README.md)** | **[繁體中文](doc/readme/README.zh-TW.md)** | **[简体中文](doc/readme/README.zh-CN.md)** | **[日本語](doc/readme/README.ja.md)**

Isaac Sim physics experiments and forklift-specific control scripts extracted from [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) per the repo split ([isaac#309](https://github.com/ycpss91255-lab/isaac/issues/309)).

This repo holds:

- **15 physics experiment drivers** (`src/script/exp_*.py`) covering L2 kinematic, L2.5 high-stiffness drive, sag/droop, trajectory tracking, visual metric acceptance, and more
- **Forklift block model driver** (`forklift_blocky_driver_wip.py`) and the PoC #59 articulation bypass (`poc59_articulation_bypass_wip.py`)
- **SIGSEGV reproducer** for the PhysX articulation + loop-joint crash (`repro_artic_kinematic_loopjoint_sigsegv.py`)
- **Pallet / collision verification scripts** (`test/verify_*.py`) for fork insertion, convex decomposition pocket preservation, and real-asset collision probes
- **4 ADRs**: 0003 (two-track simulation strategy), 0004 (Model A-hybrid forklift block model), 0008 (L2/L3 physics-level vocabulary), 0021 (physics-level realization on articulation models)
- **Experiment results** (JSON / log / PNG) and the reproducible acceptance harness (`doc/experiments/REPRODUCE.md`)
- **Shared render helpers** (`viz_render.py`) and the converged-render-settings unit tests

## Prerequisites

These scripts run **inside the Isaac Sim container** built from [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac). The container provides Isaac Sim 6.2.0, Isaac Lab v3.0.0, and the `isaac_devkit` framework.

Robot models (USD) live in the private [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) repo. The forklift block model USD is at `usd/origin/forklift_blocky/` in that repo; the openbase models are at `usd/origin/omwr/` and `usd/origin/openbase/`.

## Repository Structure

```
isaac_physics_test/
├── README.md                           # this file
├── LICENSE                             # Apache-2.0
├── SOURCE.md                           # provenance: original paths in isaac@c068e3a
├── doc/
│   ├── readme/                         # i18n READMEs
│   │   ├── README.zh-TW.md
│   │   ├── README.zh-CN.md
│   │   └── README.ja.md
│   └── experiments/
│       └── REPRODUCE.md                # reproducible acceptance harness
├── src/
│   ├── script/                         # experiment drivers
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
│   │   ├── viz_render.py               # shared RTX render + MP4 helpers
│   │   └── standalone_common.py        # shared standalone helpers (copy from isaac)
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
    ├── gen_review_pdf.py               # 6.0.1 physics re-validation review PDF
    ├── verify_decomp_pocket.py
    ├── verify_pallet_entry_map.py
    ├── verify_real_asset_collision.py
    ├── verify_real_pallet_insertion.py
    ├── .prove-*.json                   # acceptance proof results
    ├── .reval-6.2.0/                   # 6.2.0 re-validation results
    ├── .l2-*.json, .l25-*.json         # L2/L2.5 experiment results
    ├── .sag-*.json                     # sag/droop sweep results
    ├── .drive-*.json                   # drive limit/precision results
    ├── .proto/                         # prototype sag experiment
    └── ...                             # other result JSON/log/PNG files
```

## Running Experiments

All experiments require an Isaac Sim container. See [`doc/experiments/REPRODUCE.md`](doc/experiments/REPRODUCE.md) for the full reproducible acceptance harness.

Quick start (assuming the isaac container is already built and running):

```bash
# Mount this repo into the container at /home/<user>/work/physics_test/
# (or symlink into the workspace)

# Example: run the sag sweep experiment
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/src/script/exp_l25_sag_sweep.py \
    --out /home/<user>/work/physics_test/test/.sag-sweep.json

# Example: generate the review PDF
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/test/gen_review_pdf.py \
    --test-dir /home/<user>/work/physics_test/test \
    --out /home/<user>/work/doc/6.0.1_physics_revalidation_review.pdf
```

## ADRs

| ADR | Title | Status |
|-----|-------|--------|
| [0003](src/doc/adr/0003-two-track-simulation-strategy.md) | Two-Track Simulation Strategy (Model A vs Model B) | Accepted |
| [0004](src/doc/adr/0004-model-a-hybrid-forklift-block-model.md) | Model A-Hybrid Forklift Block Model | Accepted |
| [0008](src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md) | L2/L3 Physics Level Vocabulary and Coexistence Rules | Superseded by isaac ADR-0017 (generic vocabulary restated there) |
| [0021](src/doc/adr/0021-physics-level-realization-on-articulation-models.md) | Physics-Level Realization on Articulation Models (L2/L2.5/L3) | Accepted, extends ADR-0008 |

## Related Repositories

| Repository | Role |
|---|---|
| [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) | Isaac Sim Docker environment + `isaac_devkit` framework |
| [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) | Robot USD/URDF models (private) |

## License

[Apache-2.0](LICENSE)
