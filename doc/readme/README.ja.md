# isaac_physics_test

**[English](../../README.md)** | **[繁體中文](README.zh-TW.md)** | **[简体中文](README.zh-CN.md)** | **[日本語](README.ja.md)**

Isaac Sim の物理実験およびフォークリフト専用制御スクリプトを [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) からリポジトリ分割に基づき抽出したものです（[isaac#309](https://github.com/ycpss91255-lab/isaac/issues/309)）。

本リポジトリの内容：

- **15 種の物理実験ドライバ**（`src/script/exp_*.py`）-- L2 kinematic、L2.5 高剛性ドライブ、たわみ/垂れ下がり、軌道追従、視覚メトリック合格判定など
- **フォークリフトブロックモデルドライバ**（`forklift_blocky_driver_wip.py`）および PoC #59 のアーティキュレーションバイパス（`poc59_articulation_bypass_wip.py`）
- **SIGSEGV 再現スクリプト** -- PhysX アーティキュレーション + ループジョイントクラッシュ用（`repro_artic_kinematic_loopjoint_sigsegv.py`）
- **パレット / 衝突検証スクリプト**（`test/verify_*.py`）-- フォーク挿入、凸分解ポケット保持、実アセット衝突プローブ
- **4 件の ADR**: 0003（2 トラックシミュレーション戦略）、0004（Model A ハイブリッドフォークリフトブロックモデル）、0008（L2/L3 物理レベル用語体系）、0021（アーティキュレーションモデルにおける物理レベル実現）
- **実験結果**（JSON / ログ / PNG）および再現可能な合格判定ハーネス（`doc/experiments/REPRODUCE.md`）
- **共有レンダリングヘルパー**（`viz_render.py`）および収束レンダリング設定のユニットテスト

## 前提条件

これらのスクリプトは [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) からビルドされた **Isaac Sim コンテナ内**で実行します。コンテナには Isaac Sim 6.2.0、Isaac Lab v3.0.0、および `isaac_devkit` フレームワークが含まれています。

ロボットモデル（USD）はプライベートリポジトリ [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) に格納されています。フォークリフトブロックモデルの USD は同リポジトリの `usd/origin/forklift_blocky/` に、openbase モデルは `usd/origin/omwr/` および `usd/origin/openbase/` にあります。

## リポジトリ構成

```
isaac_physics_test/
├── README.md                           # 本ファイル
├── LICENSE                             # Apache-2.0
├── SOURCE.md                           # 出典: isaac@c068e3a における元パス
├── doc/
│   ├── readme/                         # 多言語 README
│   │   ├── README.zh-TW.md
│   │   ├── README.zh-CN.md
│   │   └── README.ja.md
│   └── experiments/
│       └── REPRODUCE.md                # 再現可能な合格判定ハーネス
├── src/
│   ├── script/                         # 実験ドライバ
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
│   │   ├── viz_render.py               # 共有 RTX レンダリング + MP4 ヘルパー
│   │   └── standalone_common.py        # 共有 standalone ヘルパー (isaac からコピー)
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
    ├── gen_review_pdf.py               # 6.0.1 物理再検証レビュー PDF
    ├── verify_decomp_pocket.py
    ├── verify_pallet_entry_map.py
    ├── verify_real_asset_collision.py
    ├── verify_real_pallet_insertion.py
    ├── .prove-*.json                   # 合格判定証明結果
    ├── .reval-6.2.0/                   # 6.2.0 再検証結果
    ├── .l2-*.json, .l25-*.json         # L2/L2.5 実験結果
    ├── .sag-*.json                     # たわみ/垂れ下がりスイープ結果
    ├── .drive-*.json                   # ドライブ制限/精度結果
    ├── .proto/                         # プロトタイプたわみ実験
    └── ...                             # その他の結果 JSON/ログ/PNG ファイル
```

## 実験の実行

全ての実験には Isaac Sim コンテナが必要です。再現可能な合格判定ハーネスの詳細は [`doc/experiments/REPRODUCE.md`](doc/experiments/REPRODUCE.md) を参照してください。

クイックスタート（isaac コンテナがビルド済みかつ実行中であることが前提）：

```bash
# 本リポジトリをコンテナの /home/<user>/work/physics_test/ にマウント
# （またはワークスペースにシンボリックリンクを作成）

# 例: たわみスイープ実験を実行
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/src/script/exp_l25_sag_sweep.py \
    --out /home/<user>/work/physics_test/test/.sag-sweep.json

# 例: レビュー PDF を生成
just docker exec -t devel /isaac-sim/python.sh \
    /home/<user>/work/physics_test/test/gen_review_pdf.py \
    --test-dir /home/<user>/work/physics_test/test \
    --out /home/<user>/work/doc/6.0.1_physics_revalidation_review.pdf
```

## ADR 一覧

| ADR | タイトル | ステータス |
|-----|---------|-----------|
| [0003](src/doc/adr/0003-two-track-simulation-strategy.md) | Two-Track Simulation Strategy (Model A vs Model B) | Accepted |
| [0004](src/doc/adr/0004-model-a-hybrid-forklift-block-model.md) | Model A-Hybrid Forklift Block Model | Accepted |
| [0008](src/doc/adr/0008-l2-l3-physics-level-vocabulary-and-coexistence-rules.md) | L2/L3 Physics Level Vocabulary and Coexistence Rules | isaac ADR-0017 により上位互換（汎用用語体系はそちらで再定義） |
| [0021](src/doc/adr/0021-physics-level-realization-on-articulation-models.md) | Physics-Level Realization on Articulation Models (L2/L2.5/L3) | Accepted（ADR-0008 を拡張） |

## 関連リポジトリ

| リポジトリ | 役割 |
|---|---|
| [`ycpss91255-lab/isaac`](https://github.com/ycpss91255-lab/isaac) | Isaac Sim Docker 環境 + `isaac_devkit` フレームワーク |
| [`ycpss91255-lab/robot_model`](https://github.com/ycpss91255-lab/robot_model) | ロボット USD/URDF モデル（プライベート） |

## License

[Apache-2.0](../../LICENSE)
