# 项目修改说明

## 修改日期
2025年10月18日

## 修改目的
配置PKU数据集本地运行环境，并添加Ground Truth可视化功能（无需模型权重）

---

## 📝 详细修改内容

### 1. 数据集路径配置
**文件**: `config/dataset/pku_fusion.yaml`

**修改前**:
```yaml
path: '/data2/zht/fusion_detection/PKU-H5-Process/freq_1_1'
```

**修改后**:
```yaml
path: 'E:/dataset/freq_1_1/freq_1_1'
```

**说明**: 更新为本地PKU数据集实际存放路径

---

### 2. Demo可视化脚本增强
**文件**: `demo.py`

**主要修改**:
1. **添加GT模式支持** (第53-70行)
   - 设置 `mode = 'gt'` 默认为Ground Truth可视化模式
   - 只在 `mode = 'pre'` 时才加载模型权重
   - 添加checkpoint为null的错误检查

2. **更新数据集路径** (第55行)
   ```python
   h5_file = 'E:/dataset/freq_1_1/freq_1_1/test/001_test_low_light'
   ```

3. **修改输出目录** (第78-79行)
   ```python
   images_out_put_dir = 'gt/images'  # GT模式输出
   video_out_put_dir = 'gt/video'
   ```

**功能**: 允许在没有训练好的模型权重的情况下，直接可视化数据集中的Ground Truth标注

---

### 3. 验证配置文件修改
**文件**: `config/val.yaml`

**修改前**:
```yaml
checkpoint: ???  # 必需参数
```

**修改后**:
```yaml
checkpoint: null  # 修改为null，GT模式不需要checkpoint
```

**说明**: 使checkpoint成为可选参数，支持GT模式运行

---

### 4. 添加Git忽略文件
**文件**: `.gitignore` (新建)

**内容包括**:
- Python缓存文件 (`__pycache__/`, `*.pyc`)
- 虚拟环境
- IDE配置文件
- **生成的可视化输出** (`gt/`, `predictions/`, `pre_gt/`)
- 模型权重 (`*.ckpt`, `*.pth`)
- 日志文件
- Wandb日志
- Hydra输出

**目的**: 防止将临时文件、大型文件和敏感文件上传到GitHub

---

### 5. 依赖包版本修复
**问题**: `ModuleNotFoundError: No module named 'torchdata.datapipes'`

**解决方案**:
```bash
pip uninstall torchdata -y
pip install torchdata==0.7.1
```

**说明**: 项目需要 `torchdata==0.7.1`，较新版本API已变更导致不兼容

---

## 🚀 运行方式

### Ground Truth可视化（当前配置）
```bash
conda activate transformers
python demo.py dataset=pku_fusion +experiment/pku_fusion=base.yaml
```

**输出**:
- 图片: `./gt/images/`
- 视频: `./gt/video/output_video.avi`

### 恢复模型预测模式
修改 `demo.py` 第53行:
```python
mode = 'pre'  # 改为预测模式
```

然后运行:
```bash
python demo.py dataset=pku_fusion checkpoint={权重文件路径} +experiment/pku_fusion=base.yaml
```

### 训练模型
```bash
python train.py dataset=pku_fusion dataset.path=E:/dataset/freq_1_1/freq_1_1 +experiment/pku_fusion=base.yaml
```

---

## 📦 GitHub仓库信息

**仓库地址**: https://github.com/chenluohan666/FAOD-PKU-Dataset.git

**分支**: main

**提交信息**:
```
Initial commit with PKU dataset configuration and GT visualization support

主要修改内容：
1. 配置PKU数据集路径：更新 config/dataset/pku_fusion.yaml 为本地数据集路径 E:/dataset/freq_1_1/freq_1_1
2. 修改demo.py：添加GT模式支持，允许不使用checkpoint进行ground truth可视化
3. 修改config/val.yaml：将checkpoint从必需参数改为可选(null)，支持GT模式
4. 添加.gitignore：排除缓存、输出文件、模型权重等不必要文件
5. 修复torchdata版本兼容性：确保使用torchdata==0.7.1
```

---

## 📌 注意事项

1. **数据集路径**: 确保 `E:/dataset/freq_1_1/freq_1_1` 目录结构正确
   ```
   freq_1_1/
   ├── train/
   ├── val/
   └── test/
       └── 001_test_low_light/
           ├── event_representations_v2/
           └── labels_v2/
   ```

2. **可视化模式**: 
   - `show_mode = 'mixed'`: 混合显示event和RGB（推荐）
   - `show_mode = 'event'`: 仅显示event
   - `show_mode = 'rgb'`: 仅显示RGB

3. **Git管理**: 
   - 已初始化本地Git仓库
   - 已推送到GitHub远程仓库
   - 生成的可视化文件不会被提交（已在.gitignore中排除）

4. **环境要求**:
   - Conda环境: `transformers`
   - Python版本: 3.11
   - 关键依赖: `torchdata==0.7.1`, `pytorch_lightning==1.8.6`

---

---

## 📦 DSEC数据集配置 (新增)

### 配置文件修改
**文件**: `config/dataset/dsec.yaml`

**修改内容**:
```yaml
path: 'E:/dataset/freq_1_2/freq_1_1'  # DSEC数据集路径
```

### 新建文件
1. **`demo_dsec.py`**: 专门用于DSEC数据集的GT可视化脚本
   - 默认模式：`mode = 'gt'`
   - 测试序列路径：`E:/dataset/freq_1_2/freq_1_1/test/zurich_city_11_a`
   - 输出目录：`gt_dsec/images/` 和 `gt_dsec/video/`

2. **`README_DSEC.md`**: DSEC数据集使用说明文档
   - 包含完整的运行命令
   - 数据集目录结构说明
   - 与PKU数据集的对比

### 运行命令
```bash
# DSEC Ground Truth可视化
python demo_dsec.py dataset=dsec +experiment/dsec=base.yaml

# 带路径参数
python demo_dsec.py dataset=dsec dataset.path=E:/dataset/freq_1_2/freq_1_1 +experiment/dsec=base.yaml
```

### DSEC vs PKU对比

| 特性 | PKU数据集 | DSEC数据集 |
|------|----------|-----------|
| 脚本 | `demo.py` | `demo_dsec.py` |
| 路径 | `E:/dataset/freq_1_1/freq_1_1` | `E:/dataset/freq_1_2/freq_1_1` |
| 输出 | `gt/` | `gt_dsec/` |
| 分辨率 | 260x346 | 480x640 |
| 序列长度 | 11 | 5 |

---

## 🔄 未来工作

- [x] 配置PKU数据集并实现GT可视化
- [x] 配置DSEC数据集并实现GT可视化
- [ ] 下载并测试预训练权重
- [ ] 尝试训练自己的模型
- [ ] 测试其他数据集序列
- [ ] 调整可视化参数优化效果

---

**修改人员**: AI Assistant (Claude)
**用户**: Ross Chen
**最后更新**: 2025年10月18日

