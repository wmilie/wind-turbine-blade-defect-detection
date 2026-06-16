由于文件太大，无法直接上传，具体文件及数据请跳转：通过网盘分享的文件：yolo_dataset.rar
链接: https://pan.baidu.com/s/1Kol1QibgkSTko3ch-vWSZw?pwd=1234 提取码: 1234 

### 📂 项目文件结构说明
这是一套**风电叶片缺陷检测与损伤分级级联系统**的完整工程文件，按功能模块可分为：数据集、训练脚本、推理脚本、结果输出与配置文件五类，以下是逐文件/文件夹的详细说明：

---

#### 一、核心文件夹说明
| 文件夹名称 | 功能说明 |
| :--- | :--- |
| `.idea` | PyCharm 项目配置文件夹，包含编辑器设置、运行配置等，无需手动修改 |
| `crops_auto` | 自动裁剪后的缺陷区域图像文件夹，由检测模型（YOLO26）输出，用于后续损伤分级任务输入 |
| `predict` / `predict-2` / `predict-4` | 不同版本/参数下的缺陷检测结果输出目录，保存带检测框的可视化图像、检测日志等 |
| `severity_data` | **损伤分级任务的标准数据集**，按损伤等级（轻度/中度/重度）划分好的训练/验证/测试集目录，是 ResNet18 训练的核心数据 |
| `severity_results` | 损伤分级任务的结果输出目录 |
| `train` / `train-2` / `train-4` | 缺陷检测模型（YOLO26）的不同训练版本目录，包含权重文件、日志、配置文件等 |

---

#### 二、核心代码文件说明
| 文件名 | 功能说明 |
| :--- | :--- |
| `cut.py` | 图像裁剪脚本：读取带标注的叶片大图，自动按缺陷框坐标裁剪出缺陷区域，生成 `crops_auto` 文件夹数据 |
| `end2end_severity.py` | 端到端级联推理脚本：**完整流程入口**，先调用 YOLO26 检测缺陷，再自动裁剪并送入 ResNet18 完成损伤分级，输出最终结果 |
| `final_detect_grade.py` | 最终版检测+分级一体化脚本，优化了推理速度与可视化逻辑，可批量处理文件夹内所有叶片图像 |
| `random_selection.py` | 数据划分脚本：从 `severity_raw` 中随机抽样，按比例生成训练/验证/测试集，构建 `severity_data` 目录结构 |
| `train_severity.py` | 损伤分级模型训练脚本：基于 ResNet18 实现迁移学习，读取 `severity_data` 训练并保存 `severity_model.pth` |

---

### ✨ 整体工作流总结
1.  **数据准备**：`random_selection.py` → 划分 `severity_raw` → 生成 `severity_data`
2.  **模型训练**：`train_severity.py` → 训练 ResNet18 → 输出 `severity_model.pth`
3.  **缺陷裁剪**：`cut.py` → 从叶片大图裁剪缺陷区域 → 生成 `crops_auto`
4.  **端到端推理**：`end2end_severity.py` / `final_detect_grade.py` → 检测+分级 → 输出至 `predict-*` / `severity_results`
