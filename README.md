# M4-Mojito

面向光伏功率未来 4 小时（16 × 15 分钟）的预测模型。通过多尺度历史编码、天气条件 query 和共享预测头联合建模功率与天气，不依赖站点 ID。支持站级、省级分别训练。

本仓库独立整理自 NextGenPVForecast 的 M4-Mojito 实现，保留原有导入路径。`interfaces/` 和 `pipelines/` 仅包含运行与测试所需的四个共享模块快照；模型注册器保留原框架的历史别名，但本仓库只提供 `m4_mojito`。不包含其他模型、训练数据或权重。

## 安装与验证

使用 Python 3.12 创建虚拟环境，在仓库根目录运行：

```bash
python -m pip install -r requirements-dev.txt
python -m pytest forecast_backbones/m4_mojito/tests -q
ruff check forecast_backbones/m4_mojito --select E,F,I
ruff format --check forecast_backbones/m4_mojito
```

运行环境依赖固定为本次 CPU 验证使用的版本。仅运行训练/推理时可安装 `requirements.txt`。共享框架文件保持原样，格式检查限定在新增模型代码。

## 训练

准备好已经按时间和站点要求切分的数据后运行：

```bash
python -m forecast_backbones.m4_mojito.train \
  --config forecast_backbones/m4_mojito/config/configs.yaml \
  --train /path/to/train.parquet \
  --valid /path/to/valid.parquet \
  --out /path/to/runs/m4-mojito
```

- [数据契约、训练和推理说明](forecast_backbones/m4_mojito/README.md)
- [默认配置](forecast_backbones/m4_mojito/config/configs.yaml)
- [模型实现](forecast_backbones/m4_mojito/model.py)
- [架构设计](docs/m4_mojito_ultra_short_design.md)

已验证合成数据的前向、反向、缺失处理、时间对齐、统一推理，以及短训练后的保存与重载。尚未进行真实数据效果评估或 GPU/多卡验证。当前用验证集 weighted MAE 选择权重，正式业务评分需另外接入；输入时间戳的墙钟/UTC 语义须按文档明确配置。
