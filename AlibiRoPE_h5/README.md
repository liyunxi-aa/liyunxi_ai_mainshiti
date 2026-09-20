# alibirope - RoPE & ALiBi 位置编码交互式可视化

一个用于直观理解 **RoPE (Rotary Position Embedding)** 和 **ALiBi (Attention with Linear Biases)** 的交互式 Web 应用。

## 核心概念

两种位置编码方法都依赖于同一个量：**s<sub>h</sub> · |i−j|** —— 一个 head 特定的尺度参数 × 相对位置距离。

| 方法 | 公式 | s<sub>h</sub> 含义 | 使用方式 |
|------|------|-------------------|---------|
| **RoPE** | cos(θ<sub>h</sub> · |i−j|) | θ<sub>h</sub> = base<sup>−2h/d</sup> (频率) | 旋转向量，点积变为余弦 |
| **ALiBi** | −m<sub>h</sub> · |i−j| | m<sub>h</sub> = 2<sup>−factor·h/n</sup> (斜率) | 直接加到注意力分数上 |

### 统一视角

```
注意力偏置 = f(s_h · |i-j|)

RoPE:  f(x) = cos(x)     → 周期性，多尺度叠加
ALiBi: f(x) = −x         → 线性衰减，简单直接
```

两者都通过**多个不同的 s<sub>h</sub>** 实现从局部到全局的多尺度位置感知。

## 功能

- 🔵 **RoPE 2D 旋转**：展示单个频率下 q/k 向量如何随位置旋转
- 🔴 **ALiBi 偏置**：展示多 head 的线性偏置曲线
- 📐 **s<sub>h</sub>·|i-j| 探索器**：并排对比 RoPE 和 ALiBi 的响应函数
- 🌈 **多尺度频谱热力图**：可视化不同频率/斜率下的注意力响应

## 使用方法

```bash
# 在 alibirope 目录下启动一个本地服务器
cd alibirope
python3 -m http.server 8080

# 然后在浏览器中打开
# http://localhost:8080
```

或者直接双击 `index.html` 在浏览器中打开。

## 交互式滑杆

所有标记的参数都可以通过滑杆实时调节：
- 位置 i、j
- 频率参数 θ<sub>h</sub> (通过 h, d, base)
- ALiBi 斜率参数 (通过 head 数、slope factor)
- 通用尺度 s<sub>h</sub>
- 频谱范围 s<sub>min</sub>、s<sub>max</sub>

## 关键洞察

1. **RoPE 的低频头** (小 θ<sub>h</sub>) 能感知远距离依赖，**高频头** (大 θ<sub>h</sub>) 捕捉局部精细结构
2. **ALiBi 的小斜率头** (小 m<sub>h</sub>) 关注长距离，**大斜率头** (大 m<sub>h</sub>) 关注邻近 token
3. 两种方法的本质相同：**用多个 s<sub>h</sub> 值覆盖不同的距离尺度**
