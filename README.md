# 山区短临预警的并行验收与分区换版

这些演练台账是本地合成资料，使用虚构编号。CSV 采用 UTF-8 和带表头的逗号分隔格式。同一单元内用竖线列出的值表示多项或候选集合，含义见各表说明。空值通常表示未记录或不适用，明确的零会保留为 0。

## 资料

- `data/geography.csv`  乡镇和联动组合。group 相同的乡镇在仍有跨乡联合动作时须同时采用可兼容的口径，valley 是地形类别。
  字段为 `town / group / valley`。
- `data/thresholds.csv`  各乡镇的验收界限。miss 和 nuisance 为同类事件中的最大比例，lead 为业务最低提前分钟。候选运行设置由 options 另列。
  字段为 `town / miss / nuisance / lead`。
- `data/options.csv`  运营允许比较的新版设置。score_floor 是播发所需的置信分值，作用于回放的候选稿；旧版已发消息不受此设置影响。
  字段为 `setting / score_floor`。
- `data/decisions.csv`  历次交接登记。每场天气以开始后分钟记载。signed 是正在试行的已签认范围，proposed 是当班拟签认范围。uses 为当时的证据截止分钟。
  字段为 `decision / storm / minute / state / towns / setting / uses`。
- `data/weather.csv`  完整天气过程是配对和独立性归属单位。phase 为设置选择、独立验收、实际并行三个时期。
  字段为 `storm / phase / date / weather_class`。
- `data/truth.csv`  按当时可得的核定事实。分钟以过程开始为零点。clear 表示资料完整且无险，pending 尚不能判定。incident 可在相邻乡镇共同出现。
  字段为 `storm / town / incident / start_low / start_high / label / available_minute`。
- `data/signals.csv`  各版信息在过程中的分钟。issue 首次播报，expand 增加所列地点，raise 更新所列地点级别，partial_end 解除所列地点。身份由天气、版别与稿号限定。
  字段为 `storm / build / message / prev / minute / towns / action / score`。

## 核对台账

环境为 Python 3.11.2，只使用标准库。在仓库根目录运行下列命令检查文件格式、表头和基础外键。输出为各张源表可读行数。

```sh
python3 input_audit.py
python3 -m unittest discover -v
```

可在第一条命令后指定另一个输入目录，目录中的同名 CSV 沿用上述格式。列名和基础引用清单见 `input_contract.json`。
