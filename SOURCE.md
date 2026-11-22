# Source Provenance

All files in this repository were extracted from
[`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac)
at commit `c068e3a` (the `main` branch as of 2026-11-17), per the repo
split tracked in [isaac#309](https://github.com/ycpss91255-lab/isaac/issues/309).

| This repo path | Original path in `isaac@c068e3a` |
|---|---|
| `src/script/exp_*.py` (15 files) | `src/script/exp_*.py` |
| `src/script/forklift_blocky_driver_wip.py` | `src/script/forklift_blocky_driver_wip.py` |
| `src/script/poc59_articulation_bypass_wip.py` | `src/script/poc59_articulation_bypass_wip.py` |
| `src/script/viz_render.py` | `src/script/viz_render.py` |
| `src/script/repro_artic_kinematic_loopjoint_sigsegv.py` | `src/script/repro_artic_kinematic_loopjoint_sigsegv.py` |
| `src/script/velocity_test.py` | `src/script/velocity_test.py` |
| `src/script/standalone_common.py` | `src/script/standalone_common.py` (copy; original remains in isaac) |
| `src/doc/adr/0003-two-track-simulation-strategy.md` | `src/doc/adr/0003-two-track-simulation-strategy.md` |
| `src/doc/adr/0004-model-a-hybrid-forklift-block-model.md` | `src/doc/adr/0004-model-a-hybrid-forklift-block-model.md` |
| `src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md` | `src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md` |
| `src/doc/adr/0021-physics-level-realization-on-articulation-models.md` | `src/doc/adr/0021-physics-level-realization-on-articulation-models.md` |
| `doc/experiments/REPRODUCE.md` | `doc/experiments/REPRODUCE.md` |
| `test/gen_review_pdf.py` | `test/gen_review_pdf.py` |
| `test/integration/test_openbase_l2_smoke.py` | `test/integration/test_openbase_l2_smoke.py` |
| `test/integration/test_openbase_l2_stability.py` | `test/integration/test_openbase_l2_stability.py` |
| `test/unit/pytest/test_render_settings.py` | `test/unit/pytest/test_render_settings.py` |
| `test/verify_decomp_pocket.py` | `test/verify_decomp_pocket.py` |
| `test/verify_pallet_entry_map.py` | `test/verify_pallet_entry_map.py` |
| `test/verify_real_asset_collision.py` | `test/verify_real_asset_collision.py` |
| `test/verify_real_pallet_insertion.py` | `test/verify_real_pallet_insertion.py` |
| `test/.prove-*.json` (14 files) | `test/.prove-*.json` |
| `test/.reval-6.2.0/` (16 files) | `test/.reval-6.2.0/` |
| `test/.l2-*.json`, `.l25-*.json` (9 files) | same |
| `test/.modela-stability.json` | same |
| `test/.multijoint-sag.json` | same |
| `test/.sag-*.json`, `.sag-*.log` (6 files) | same |
| `test/.drive-*.json` (2 files) | same |
| `test/.revolute-gain-scaling.json` | same |
| `test/.traj-tracking.json` | same |
| `test/.visual-metric.json`, `visual_metric_frame.png` | same |
| `test/.proto/` (3 files) | same |
| `test/.pallet-entry-map*.json` (3 files) | same |
| `test/.verify-*.json` (4 files) | same |
| `test/.o3dyn-pockets.json` | same |

## Second batch

Physics-experiment and forklift content that the first extraction left behind
in `isaac`, moved one concern at a time (tracked in
[#3](https://github.com/ycpss91255-lab/isaac_physics_test/issues/3)). Extracted from
`isaac@fa158d0`. Paths are unchanged.

| This repo path | Original path in `isaac@fa158d0` |
|---|---|
| `src/doc/CONTEXT.md` | same |
| `src/doc/adr/0001-chassis-se2-slide-for-upper-layer-algorithm.md` | same |
| `src/doc/adr/0002-cmd-vel-teleop-via-in-kit-script-editor.md` | same |
| `src/doc/adr/0006-per-sensor-yaml-camera-config.md` | same |
| `src/doc/adr/0010-isaac-dev-kit-standardized-development-environment.md` | same |
| `src/script/cmd_vel_planar_move.py` | same |
| `src/script/cmd_vel_planar_standalone.py` | same |
| `src/script/cmd_vel_planar_standalone_l2.py` | same |
| `src/script/move_openbase_planar.py` | same |
| `src/script/move_openbase_planar_standalone.py` | same |
| `src/script/diagnose_inkit.py` | same |
| `src/script/diag_graph_bridge.py` | same |
| `src/script/diag_usd_loop.py` | same |
| `src/scene/warehouse_pushback.yaml` | same |
| `test/verify_collision_import.py` | same |
| `test/verify_import_fidelity.py` | same |
| `test/verify_urdf_mesh_collision_path.py` | same |
| `test/.verify-collision-import.json` | same |
| `test/.verify-import-fidelity.json` | same |
| `test/.verify-urdf-mesh-collision.json` | same |
| `test/.sag-sweep.json` | same |

`test/.sag-sweep.json` was missed by the first extraction.

Not moved: `test/fixtures/urdf/` (used by isaac framework tests; the moved
`verify_*` scripts build their own URDFs under `/tmp`) and ADR-0020, which
stays in `isaac` as the URDF-to-USD ingestion policy.
