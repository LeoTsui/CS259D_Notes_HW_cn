# On the Infeasibility of Modeling Polymorphic Shellcode

<!-- TOC -->

- [背景知识和启发](#背景知识和启发)
    - [Shellcode](#shellcode)
    - [签名特征匹配](#签名特征匹配)
    - [解码器和解码器检测器](#解码器和解码器检测器)
- [本文目标和贡献](#本文目标和贡献)
- [多态引擎分析](#多态引擎分析)
    - [标记](#标记)
    - [问题定义](#问题定义)
    - [衡量尺度](#衡量尺度)
        - [光谱图像](#光谱图像)
        - [最小欧几里得距离](#最小欧几里得距离)
        - [变化能力](#变化能力)
        - [传播能力](#传播能力)
        - [整体能力](#整体能力)
- [混合引擎：结合多态性和混合](#混合引擎结合多态性和混合)
- [参考资料](#参考资料)

<!-- /TOC -->

## 背景知识和启发

### Shellcode

* 传统 shellcode 结构
    * [nop][**payload**][retaddr]
* 加密的 shellcode 结构
    * [nop][**decoder**][**encpayload**][retaddr]
* 现代混淆技术
    * 自动重写代码
        * 难以（NP完全）分解成图
    * 代码混淆
        * 加密
        * 在运行时动态解密
        * 在运行时保持代码混淆

### 签名特征匹配

* 基于字符串的签名
    * Snort
* 启发式检测
    * 各种数据包类型的频率
* NOP sled 识别
* 来自实际 exploit 代码的签名
* 数据包内容的统计度量

### 解码器和解码器检测器

* 寻找解码器而不是有效载荷
* 解码器
    * 组件
        * 修改操作
            * `add`，`sub`，`xor`，等等
        * 循环
            * `jmpz`
    * 不仅仅是“解码”，维护行为
        * 清除寄存器
        * 多重解密操作
        * 计算可执行代码的位置
* **解码器能够隐藏得多好**
    * 重排以及随机化单一加密组件的次序
    * 随机选择密钥
    * 注入垃圾指令

## 本文目标和贡献

* 描述 shellcode 和代码混淆技术
* 衡量多态引擎的能力
* 简介混合动力引擎
* 证明：给定任意常见统计模型。攻击者有很大的概率攻击成功

## 多态引擎分析

### 标记

* $$n$$，字符串字节数量
* $$N$$，样本数量
* $$\mathbf{x}$$（$$\mathbf{y}$$），列向量（样本）集合
* $$\mathbf{x}_i$$（$$\mathbf{x}_j$$），$$i, j  = 1 ... N$$，集合 $$\mathbf{x}$$ 的第 $$i$$（$$j$$）个向量（样本）
* $$\mathbf{x}(i)$$，向量 $$\mathbf{x}$$ 中的第 $$i$$ 个分量

### 问题定义

* 给定 $$n$$ 个字节，存在 $$256^n$$ 种可能的字符串
* 长度为 $$n$$ 的 *x86* 代码是其中的子空间 
* 对这个子空间建模有多困难？

### 衡量尺度

####  光谱图像

![可视化 shellcode 变体](images/visualization_of_shellcode_variations.png)

* $$D$$ 个长度为 $$N$$ 的解码器
* 转换成 $$D \times N$$ 的矩阵
* 将矩阵显示为图像
    * `0x00`-`0xFF`：黑色-白色

#### 最小欧几里得距离

* 直觉：解码器可以改变操作顺序
* 字符串 $$\mathbf{x}$$ 是 $$n$$-维欧几里德空间中的一个点
    * 例子（2维）："ab" $$\rightarrow (97, 98)$$
* 最小欧几里德距离：
    * 任意字节层面旋转后的两点之间的最小标准化距离
    * $$rot(\mathbf{y}, r)$$，依字节 $$r$$ 左旋字符串 $$\mathbf{y}$$

<p align="center">$$\delta(\mathbf{x}, \mathbf{y}) = \min_{1 \leq r \leq n}\{\frac{\parallel \mathbf{x} = rot(\mathbf{y}, r) \parallel}{\parallel \mathbf{x} \parallel + \parallel \mathbf{y} \parallel}\}$$</p>

#### 变化能力

* $$n$$维中空间中和解码器对应的点分布的空间大小
* 解码器 $$x_1, x_2, ..., x_N$$ 处于 $$n$$ 维中空间中
* $$\lambda_1, \lambda_2, ..., \lambda_n$$ ，协方差矩阵的特征值
* 变化能力：

<p align="center">$$\Psi(\text{engine}) = \frac{1}{d}\sum_{i = 1}^{d}{\sqrt{\lambda_i}}$$</p>

#### 传播能力

* 变化强度衡量的最坏情况
    * 解码器分布在 $$n$$ 维空间中的大半径空心球体上
* 有效于使得样本对不同
* 考虑一个以解码器为节点的完全连通图
    * 边权重 $$=$$ 最小欧几里德距离
    * 平均边权重 $$=$$ 传播强度
* $$\eta$$，样本中重要字节的数量
* $$\delta(\mathbf{x}, \mathbf{y})$$，两个样本间的距离
    * 距离函数 $$\delta$$ 可灵活选择
* 默认：均一先验，$$p(\delta(\cdot)) = 1$$
* 传播能力：

<p align="center">$$\Phi(engine) = (1 - \frac{\eta}{n})\int\int p(\delta(\mathbf{x}, \mathbf{y}))\delta(\mathbf{x}, \mathbf{y})d\mathbf{x} d\mathbf{y}$$</p>

#### 整体能力

* 衡量多态引擎：

<p align="center">$$\Pi(\text{engine}) = \Psi(\text{engine}) \cdot \Phi(\text{engine})$$</p>

## 混合引擎：结合多态性和混合

* CLET：字节分布混合
* ADMutate：多态性
    * 解码器看起来随机，NOP sled 递归
* 结合 CLET 与 ADMutate
    * 融入正常流量
    * 混合字节可以随机替换
    * 可以使用随机偏移量添加 RETADDR
    * 4字节的重要数据太小以至于无法作为签名
    * 几乎无法建模

## 参考资料

* On the Infeasibility of Modeling Polymorphic Shellcode, Song et al, 2007
* On the Infeasibility of Modeling Polymorphic Shellcode Re-thinking the role of learning in intrusion detection systems, Song et al, 2009
* CS 259D Session 14
