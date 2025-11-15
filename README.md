
# Sky View Factor (SVF) Calculator

基于鱼眼天空掩码（0/255）自动计算街景的 Sky View Factor，并批量输出为 CSV。

---

## 功能简介

本脚本用于从 **鱼眼图像（fisheye）** 或 **已处理的天空二值图（0/255）** 中计算 **Sky View Factor (SVF)**，并将结果输出为 CSV 文件。

功能包括：

* 批量读取文件夹中的所有 PNG 图像
* 自动阈值二值化：天空=1、障碍/建筑=0
* 生成圆形鱼眼遮罩，避免角落的黑边干扰
* 在遮罩范围内计算 SVF：
  [
  SVF = \frac{\text{天空像素数量}}{\text{总有效像素}}
  ]
* 将每张图像的 SVF 写入 CSV（含表头）

该脚本适用于：

* 城市街景 SVF 批量计算
* 街道遮挡分析
* 城市微气候研究（热环境/风环境前处理）
* 光伏潜力分析（用于天空可见性数据）

---

## 输入与输出

### **输入目录（image_folder）**

包含已处理好的鱼眼天空掩码，例如：

* 白色像素（255）代表天空
* 黑色像素（0）代表建筑/地面
* 图像为 **正圆鱼眼图**（黑边部分自动剔除）

示例：

```
D:\to_zero\result\filled
 ├── 0001.png
 ├── 0002.png
 ├── ...
```

### **输出文件（csv_output）**

CSV 文件包含：

| image_name | svf_value |
| ---------- | --------- |
| 0001       | 0.5342    |
| 0002       | 0.6129    |

---

## 使用方法

### 1. 安装依赖

```bash
pip install opencv-python numpy
```

### 2. 修改路径

在脚本顶部设置你的路径：

```python
image_folder = r"二值化图像文件路径"
csv_output = r"结果保存路径"
```

### 3. 运行脚本

```bash
python svf_calculate.py
```

计算结果将输出到指定的 CSV 文件中。

---

##  计算原理说明

### 1. 二值化天空

```python
_, binary = cv2.threshold(img, 127, 1, cv2.THRESH_BINARY)
```

假设：

* 白色（>127）为天空
* 黑色为建筑或其他遮挡物

---

### 2. 生成鱼眼遮罩

由于 fisheye 图像是圆形，矩形四角为无效区域，因此生成圆形 mask：

```python
mask = (X - center[0])**2 + (Y - center[1])**2 <= radius**2
```

避免计算非鱼眼区域带来的偏差。

---

### 3. 计算 Sky View Factor

在有效圆形区域内：

```python
svf_value = sky_pixels / total_pixels
```

其中：

* sky_pixels：天空像素数量
* total_pixels：圆内总像素

---

## 输出示例

控制台输出：

```
0001: SVF = 0.4873
0002: SVF = 0.6521
0003: SVF = 0.5938
```

CSV example:

```
image_name,svf_value
0001,0.4873
0002,0.6521
0003,0.5938
```

---

## 代码结构说明

* **读取图像**
* **转换为二值天空掩码**
* **生成鱼眼圆形有效区域**
* **统计天空像素**
* **计算 SVF**
* **写入 CSV 文件**

整个流程高度可复现且适合批量处理。

---

## 适用场景

本脚本特别适用于：

* 街景图像天空开敞度计算
* Sky View Factor 分析
* 老城区日照潜力研究
* 太阳能评估（PV potential assessment）
* 城市热环境、风环境预处理
* 深度学习 street-view 分析后的后处理

---

