# TDR

本仓库用于对矢量网络分析仪测得的 `S11(f)` 数据进行时域反射分析。当前主要分析流程写在 [`TDR.ipynb`](TDR.ipynb) 中，包括 S11 频域数据读取、IFFT 得到 TDR 序列、反射峰识别、反射系数估计、线损修正。

## 主要功能

- 从 HDF5 或 CSV 文件读取频率点、`S11` 实部和虚部。
- 对复数 `S11(f)` 做反傅里叶变换，得到 TDR 时域反射结果。
- 绘制 `S11` 幅度、相位和 TDR 序列。
- 自动识别 TDR 峰，并通过窗口求和拟合估计峰面积。
- 以主反射峰为参考，计算其它阻抗不连续点的相对反射系数。



## 文件说明

```text
.
├── TDR.ipynb      # 主分析 notebook
├── README.md      # 项目说明
└── .gitignore     # Git 忽略规则，避免误传大数据和缓存文件
```

当前 notebook 默认读取根目录下的 `wuxian.h5`：

```python
with h5py.File("wuxian.h5", "r") as file:
    freq = file["data/freq"][:]
    real = file["data/real"][:]
    imag = file["data/imag"][:]
```

其中：

- `data/freq`：频率数组，单位 Hz。
- `data/real`：`S11` 实部。
- `data/imag`：`S11` 虚部。

notebook 中也保留了 CSV 读取示例，可按实际数据文件修改路径和列名。

## 环境依赖

推荐使用 Python 3.8 或更高版本。主要依赖如下：

```bash
python -m pip install numpy matplotlib h5py pandas jupyter
```

如果已经在 `py3.8` 环境中运行，只需要确认这些包已经安装即可。

## 使用方法

1. 将待分析的 S11 数据文件放在仓库根目录，或在 `TDR.ipynb` 中修改数据路径。
2. 启动 Jupyter：

```bash
jupyter notebook
```

3. 打开 `TDR.ipynb`，从上到下运行单元格。
4. 根据实验设置修改关键参数，例如：

```python
beta = 0.79
index_max = 1500
Lmax = 1
```

5. 查看输出的频域图、相位图、TDR 图以及 `Gamma`、`epsilon`、线损和 `beta` 计算结果。

## 核心函数

- `TDR_function(s11real, s11imag, freq, use_abs=False)`
  - 对复数 `S11(f)` 做 IFFT，返回时间分辨率和 TDR 序列。

- `plotTDR(...)`
  - 绘制 `S11` 幅度、相位和 TDR 结果，可选择以 index 或距离为横轴。

- `TDR_peak_area(...)`
  - 识别 TDR 峰并计算峰面积，用于估计相对反射系数。



## 数据管理

实验数据和中间结果通常体积较大，不建议提交到 GitHub。当前 `.gitignore` 已忽略常见数据和缓存文件，例如：

```text
*.csv
*.h5
*.hdf5
*.dat
*.log
__pycache__/
.ipynb_checkpoints/
```

如需共享原始数据，建议放在专门的数据存储位置，并在 README 或 notebook 中说明下载方式和存放路径。

## Git 上传建议

如果只上传分析 notebook 和说明文件，可以使用：

```bash
git add TDR.ipynb README.md .gitignore
git commit -m "docs: add TDR analysis notebook"
git push
```

上传前建议先检查：

```bash
git status
```

确认没有把 `.h5`、`.csv`、日志、图片输出目录等大文件误加入暂存区。
