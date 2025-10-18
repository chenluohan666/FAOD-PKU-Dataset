# DSEC数据集Ground Truth可视化说明

## 📁 数据集配置

**数据集路径**: `E:/dataset/freq_1_2/freq_1_1`

配置文件已更新：`config/dataset/dsec.yaml`

## 🚀 运行Ground Truth可视化

### 方法1：使用专用脚本（推荐）

```bash
conda activate transformers
python demo_dsec.py dataset=dsec +experiment/dsec=base.yaml
```

### 方法2：指定数据集路径

```bash
python demo_dsec.py dataset=dsec dataset.path=E:/dataset/freq_1_2/freq_1_1 +experiment/dsec=base.yaml
```

## 📊 输出结果

运行后会在项目根目录生成：
- **图片输出**: `./gt_dsec/images/` - 每一帧的可视化图片（带标注框）
- **视频输出**: `./gt_dsec/video/output_video.avi` - 合成的视频文件

## ⚙️ 可视化模式设置

在 `demo_dsec.py` 第54行可以修改显示模式：

```python
show_mode = 'mixed'  # 可选: 'event', 'rgb', 'mixed'
```

- **`'event'`**: 只显示event数据
- **`'rgb'`**: 只显示RGB图像
- **`'mixed'`**: 混合显示event和RGB（推荐）

## 🔧 修改可视化序列

在 `demo_dsec.py` 第59行修改要可视化的测试序列：

```python
h5_file = 'E:/dataset/freq_1_2/freq_1_1/test/zurich_city_11_a'
```

DSEC常见测试序列：
- `zurich_city_11_a`
- `zurich_city_11_b`
- `zurich_city_11_c`
- 等等...

根据你的数据集实际包含的序列进行修改。

## 📋 数据集目录结构

确保你的DSEC数据集目录结构如下：

```
E:/dataset/freq_1_2/freq_1_1/
├── train/
│   └── [训练序列]
├── val/
│   └── [验证序列]
└── test/
    ├── zurich_city_11_a/
    │   ├── event_representations_v2/
    │   │   └── stacked_histogram_dt=50_nbins=10/
    │   │       ├── event_representations.h5
    │   │       └── objframe_idx_2_repr_idx.npy
    │   └── labels_v2/
    │       ├── images.h5
    │       └── labels.npz
    └── [其他测试序列]
```

## 🔄 切换到预测模式

如果你有训练好的模型权重，可以修改 `demo_dsec.py` 第53行：

```python
mode = 'pre'  # 改为预测模式
```

然后运行：

```bash
python demo_dsec.py dataset=dsec checkpoint={权重文件路径} +experiment/dsec=base.yaml
```

## 🆚 与PKU数据集的区别

| 特性 | PKU数据集 | DSEC数据集 |
|------|----------|-----------|
| 脚本文件 | `demo.py` | `demo_dsec.py` |
| 数据集路径 | `E:/dataset/freq_1_1/freq_1_1` | `E:/dataset/freq_1_2/freq_1_1` |
| 配置文件 | `config/dataset/pku_fusion.yaml` | `config/dataset/dsec.yaml` |
| 输出目录 | `gt/` | `gt_dsec/` |
| 分辨率 | 260x346 | 480x640 |
| 序列长度 | 11 | 5 |

## ⚠️ 注意事项

1. **确保数据集路径正确**: 检查 `E:/dataset/freq_1_2/freq_1_1` 下有 `test/` 目录
2. **测试序列名称**: 修改 `h5_file` 变量为你实际的测试序列名称
3. **显存要求**: GT模式不需要加载模型，对显存要求很低
4. **输出文件**: 生成的可视化文件不会被Git追踪（已在.gitignore中排除）

## 🎯 快速开始

1. 确认数据集路径正确
2. 修改 `demo_dsec.py` 中的测试序列路径
3. 运行可视化命令
4. 查看 `gt_dsec/` 目录中的输出结果

---

**创建日期**: 2025年10月18日  
**数据集**: DSEC-Detection  
**配置**: Ground Truth可视化模式

