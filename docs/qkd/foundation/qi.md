# QKD 量子信息基础

## 引言 🚀

量子密钥分发（QKD）的核心目标是：在可验证安全性的前提下，让通信双方生成共享随机密钥。
本页聚焦 QKD 所需的量子信息学基础概念，强调“能用于工程判断”的知识框架，而不仅是公式记忆。

!!! info "核心概念"
	- **量子比特（Qubit）**：二维复希尔伯特空间中的态向量，可处于叠加态。
	- **测量扰动**：对未知量子态的测量会引入不可忽略的状态塌缩，是 QKD 安全性的物理根基之一。
	- **不可克隆定理**：未知量子态无法被完美复制，限制了窃听者的无损复制攻击。
	- **基选择与误码率（QBER）**：协议中随机基选择可暴露窃听行为，QBER 是安全评估关键指标。

!!! warning "避坑指南"
	- 不要把“量子随机”与“系统绝对安全”画等号，实际系统仍受器件缺陷与侧信道影响。
	- 不要忽略有限码长效应，实验室推导的渐近结果不能直接套用到短帧场景。
	- 不要只看平均误码率，时序漂移和偏置误差也会显著影响密钥率与安全边界。
	- 不要把仿真参数写死，建议保留可配置参数并记录每次实验配置。

## 最小知识骨架

1. 量子态表示：纯态、混态、密度矩阵。
2. 量子测量：投影测量、POVM、测量后态更新。
3. 量子信道：损耗、噪声、退相干建模。
4. 安全指标：QBER、互信息、可提取密钥率。

## 常用公式（速查）

- 二态叠加：$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle, \ |\alpha|^2 + |\beta|^2 = 1$
- 冯诺依曼熵：$S(\rho) = -\mathrm{Tr}(\rho\log_2\rho)$
- 二元熵函数：$h_2(e) = -e\log_2 e - (1-e)\log_2(1-e)$

在常见 BB84 渐近近似下，可写出示意性密钥率关系：

$$
R \approx q\left[1 - 2h_2(e)\right]
$$

其中 $q$ 为筛选因子，$e$ 为量子比特误码率（QBER）。

## 折叠内容：推导与代码

<details>
<summary><strong>展开：从互信息到密钥率近似的推导思路</strong></summary>

设 Alice 与 Bob 的误码率为 $e$，在理想纠错效率近似下，

$$
I(A:B) \approx 1 - h_2(e)
$$

在对称攻击与标准隐私放大近似下，可用

$$
\chi(E:B) \approx h_2(e)
$$

得到示意性净密钥率

$$
R \approx q\left[I(A:B) - \chi(E:B)\right] = q\left[1 - 2h_2(e)\right]
$$

注意：该式用于入门直觉，不替代严格安全证明与有限码长分析。

</details>

<details>
<summary><strong>展开：QBER 与二元熵的 Python 示例</strong></summary>

```python
import math


def h2(e: float) -> float:
	if e <= 0.0 or e >= 1.0:
		return 0.0
	return -e * math.log2(e) - (1 - e) * math.log2(1 - e)


def key_rate_bb84_asymptotic(e: float, q: float = 0.5) -> float:
	return q * (1 - 2 * h2(e))


for qber in [0.01, 0.03, 0.05, 0.08, 0.11]:
	print(f"QBER={qber:.2%}, R≈{key_rate_bb84_asymptotic(qber):.4f}")
```

</details>

## 反馈

如果这页内容对你有帮助，欢迎：

- 在仓库点一个 Star 支持持续更新。
- 在评论区或 Issue 留言你希望补充的主题（如有限码长、安全证明、器件无关 QKD）。

