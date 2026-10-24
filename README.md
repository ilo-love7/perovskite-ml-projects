# Perovskite ML Projects · 钙钛矿 × 机器学习 项目集

本科学训练项目集：围绕**钙钛矿太阳能电池 / SCLC 载流子迁移率测量**，
从实验数据流水线到开源数据机器学习，共 5 个可复现模块。
数据全部来自公开渠道（实验原始数据不入库），每个模块独立可跑、附结果图。

English below · 作者：福建师范大学 新能源材料与器件 本科生

| # | 模块 | 内容 | 数据 | 核心结果 |
|---|---|---|---|---|
| 01 | [SCLC 测量流水线](01-sclc-pipeline/) | 仪器数据解析 → log-log 分区判定 → Mott-Gurney 迁移率；数据质量不达标时拒绝出数 | 合成演示数据（随仓库） | 8 个真实样品复核：全部欧姆区，拒绝硬算迁移率 |
| 02 | [PINN 拟合 SCLC 迁移率](02-pinn-sclc/) | 物理约束参数辨识 + DeepXDE 逆问题及其病态性分析 | 自生成合成数据（真值已知） | 陷阱场景下传统窗口 V² 法高估 52%，物理约束拟合仅偏 2% |
| 03 | [工艺→效率回归](03-pce-regression/) | 4.2 万器件全工艺参数 → PCE 随机森林回归 | [Perovskite Database](https://www.perovskitedatabase.com)（CC-BY，84.7MB） | R²=0.657 / MAE=2.19%；识别出数据质量混杂因子 |
| 04 | [贝叶斯优化寻优](04-bayesian-optimization/) | 以 03 的模型为虚拟实验室，Optuna TPE 搜索最优工艺窗口 | 复用 03 数据 | TPE 18.91% vs 随机 18.84%；最优解有 632 个真实样本支撑 |
| 05 | [缺陷视觉检测](05-defect-detection/) | YOLOv8 光伏组件 5 类缺陷检测（数据→训练→mAP→部署全链） | [PV_Multi_Defect](https://huggingface.co/datasets/KeenForgeAI/PV_Multi_Defect-corrected)（HF 直接下载） | mAP50=0.762；混淆矩阵对角 0.82–0.99 |

## 项目主线（为什么这样组织）

```
实验测得暗态 J-V ──► 01 流水线判定（能否提取迁移率）
        │                     │ 判定"未进 SCLC 区"
        ▼                     ▼
02 PINN：小样本/陷阱场景下的稳健估计    03 开源数据库回归：工艺窗口长什么样
                              │
                              ▼
                     04 贝叶斯优化：用代理模型反推最优工艺
05 缺陷检测：器件质量的另一只眼（视觉）
```

## 快速开始

每个目录独立成篇，`README` 里有各自的运行方式；通用依赖见根目录
`requirements.txt`（Python ≥3.10，全程 CPU 可跑）。

```bash
pip install -r requirements.txt
# 例：跑 01 的演示
cd 01-sclc-pipeline && python make_demo_data.py && python sclc_pipeline.py
```

国内网络说明：大数据集的下载脚本已内置镜像通道（hf-mirror / 分段并行），
见各目录 `download_data.py`。

## 数据与隐私

- 实验室原始数据、器件工艺记录**不随仓库分发**；01 的演示数据由
  `make_demo_data.py` 现场生成
- 03/05 的开源数据体量大，不入库，用 `download_data.py` 获取
- 结果图随仓库分发以复现 README 结论

## License

代码 MIT；所引用数据集许可见各目录说明（Perovskite Database: CC-BY 4.0）。

---

# English (brief)

A collection of 5 reproducible modules around **perovskite solar cells /
SCLC mobility measurement + machine learning**, built as undergraduate
research training:

1. **SCLC measurement pipeline** — parse instrument exports, log-log regime
   classification, V_TFL detection, Mott-Gurney mobility *only when the data
   supports it* (refuses otherwise).
2. **PINN for SCLC** — physics-constrained parameter identification (2%
   error vs 52% bias for naive windowed V² fitting under traps), plus a
   DeepXDE inverse-problem study showing its degeneracy for the trap-free
   Mott-Gurney problem.
3. **Process→PCE regression** on the 42k-device Perovskite Database
   (R²=0.657, MAE=2.19%), with an explicit data-quality confounder analysis.
4. **Bayesian optimization** of process windows on top of (3) — TPE vs
   random search under a fixed virtual-lab budget.
5. **YOLOv8 defect detection** on open PV inspection data (mAP50=0.762).

Each folder is self-contained with its own README (Chinese), code and
result figures; heavy datasets are fetched by `download_data.py` scripts.
Lab-acquired raw data is deliberately excluded from this repository.
