# Anomaly Detection Using Layered Networks Based on Eigen Co-occurrence Matrix Note

<!-- TOC -->

- [背景知识](#%E8%83%8C%E6%99%AF%E7%9F%A5%E8%AF%86)
- [启发和论文目标](#%E5%90%AF%E5%8F%91%E5%92%8C%E8%AE%BA%E6%96%87%E7%9B%AE%E6%A0%87)
- [Eigen co-occurrence matrix (ECM)](#eigen-co-occurrence-matrix-ecm)
- [参考资料](#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

<!-- /TOC -->

## 背景知识

* 很难准确建模用户行为
    * 用户行为不断变化
    * 很难完全捕捉
* 用户行为建模
    * 特征向量
        * 直方图
            * 没有次序
        * N-grams
            * N元链接的序列
        * 不相邻事件之间的相关性
    * 网络模型
        * 自动机
            * 需要明确的规则
            * 背景信息各异，难以明确规则
        * 贝叶斯网络
            * 贝叶斯网络表明相应变量之间因果关系变化的方向
            * 应先明确拓扑关系
        * 隐马尔可夫模型（HMM）
            * 很难建立合乎要求拓扑结构

## 启发和论文目标

* 假设
    * 出现在序列中的用户动态行为不仅可以通过关联的事件进行捕获，也可以通过相隔一定距离不相邻的事件进行捕获
* ECM, Eigen co-occurrence matrices
    * 受到 Eigenface 技术的启发
    * 三个主要部分
        * 动态特征序列建模
        * 提取所得模型的主要特征
        * 从提取的主要特征中自动构建分层网络

## Eigen co-occurrence matrix (ECM)

* 手写笔记 :)

## 参考资料

* Anomaly Detection Using Layered Networks Based on Eigen Co-occurrence Matrix
* Anomaly Detection Using Integration Model of Vector Space and Network Representation
