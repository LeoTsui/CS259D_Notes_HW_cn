# CS 259D 《数据挖掘与网络安全》笔记说明

**本笔记是对课程[CS 259D](https://web.stanford.edu/class/cs259d)中涉及的论文和讲义的扩展，建议阅读原始论文和讲义。**

中文：[Github](https://github.com/LeoTsui/CS259D_Notes_HW_cn) [GitBook](https://www.gitbook.com/book/leotsui/cs259d-notes-cn) English: [GitHub](https://github.com/LeoTsui/CS259D_Notes_HW) [GitBook](https://www.gitbook.com/book/leotsui/cs259d-notes)

---

<!-- TOC -->

- [课程笔记](#%E8%AF%BE%E7%A8%8B%E7%AC%94%E8%AE%B0)
    - [介绍](#%E4%BB%8B%E7%BB%8D)
    - [僵尸网络](#%E5%83%B5%E5%B0%B8%E7%BD%91%E7%BB%9C)
    - [内部威胁](#%E5%86%85%E9%83%A8%E5%A8%81%E8%83%81)
    - [生物行为统计信息](#%E7%94%9F%E7%89%A9%E8%A1%8C%E4%B8%BA%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)
    - [网络安全](#%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8)
    - [网络钓鱼检测](#%E7%BD%91%E7%BB%9C%E9%92%93%E9%B1%BC%E6%A3%80%E6%B5%8B)
    - [自动警报关联](#%E8%87%AA%E5%8A%A8%E8%AD%A6%E6%8A%A5%E5%85%B3%E8%81%94)
    - [多分类器系统，入侵检测系统（IDS）](#%E5%A4%9A%E5%88%86%E7%B1%BB%E5%99%A8%E7%B3%BB%E7%BB%9F%EF%BC%8C%E5%85%A5%E4%BE%B5%E6%A3%80%E6%B5%8B%E7%B3%BB%E7%BB%9F%EF%BC%88ids%EF%BC%89)
    - [深度包检测](#%E6%B7%B1%E5%BA%A6%E5%8C%85%E6%A3%80%E6%B5%8B)
    - [多态性](#%E5%A4%9A%E6%80%81%E6%80%A7)
    - [在安全领域使用机器学习](#%E5%9C%A8%E5%AE%89%E5%85%A8%E9%A2%86%E5%9F%9F%E4%BD%BF%E7%94%A8%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0)
    - [机器学习的敌人](#%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0%E7%9A%84%E6%95%8C%E4%BA%BA)
    - [工业界做客讲座报告](#%E5%B7%A5%E4%B8%9A%E7%95%8C%E5%81%9A%E5%AE%A2%E8%AE%B2%E5%BA%A7%E6%8A%A5%E5%91%8A)
- [作业](#%E4%BD%9C%E4%B8%9A)
    - [作业1](#%E4%BD%9C%E4%B8%9A1)
    - [作业2](#%E4%BD%9C%E4%B8%9A2)
    - [作业3](#%E4%BD%9C%E4%B8%9A3)
    - [作业4](#%E4%BD%9C%E4%B8%9A4)

<!-- /TOC -->

## 课程笔记

### 介绍

* 信息安全概述，当前的安全形势，安全领域数据挖掘案例

### 僵尸网络

* 僵尸网络拓扑结构， 使用 NetFlow 检测僵尸网络
    * Lecture 2
    * BotMiner: Clustering Analysis of Network Traffic for Protocol- and Structure- Independent Botnet Detection
    * BotFinder: Finding Bots in Network Traffic Without Deep Packet Inspection
* 使用 DNS 检测僵尸网络
    * Lecture 3
    * EXPOSURE: Finding Malicious Domains Using Passive DNS Analysis (2011)

### 内部威胁

* 内部威胁简介，伪装检测策略
    * Lecture 3
    * One-class Training for Masquerade Detection (2003)

### 生物行为统计信息

* 基于行为和认知生物特征的主动认证
    * Lecture 4
    * An examination of user behavior for re-authentication (M. Pusara’s PhD thesis,2007)
* 基于鼠标动态信息分析的主动认证
    * Lecture 5
    * Continuous authentication for mouse dynamics: A pattern-growth approach (Shen C, Cai Z, Guan X. 2012)
    * Lecture 7
    * An Efficient User Verification System via Mouse Movements, 2011
* 基于触摸和滑动模式分析的移动端主动认证
    * Lecture 7
    * Touchalytics: On the Applicability of Touchscreen Input as a Behavioral Biometric for Continuous Authentication, 2013

### 网络安全

* 基于Web服务器日志分析的Web威胁检测
    * Lecture 8
    * A multi-model approach to the detection of web-based attacks, 2005
* 网络安全警报聚类
    * Lecture 12
    * Using Generalization and Characterization Techniques in the Anomaly-based Detection of Web Attacks, Robertson et al., 2006

### 网络钓鱼检测

* 钓鱼邮件和钓鱼网站检测
    * Lecture 16
    * Learning to Detect Phishing Emails, Fette et al, 2007
    * Cantina: A content-based approach to detecting phishing websites, Zhang et al, 2007

### 自动警报关联

* Building attack scenarios from individual alerts correlation
    * Lecture 20
    * A Comprehensive Approach to Intrusion Detection Alert Correlation, Valeur et al, 2004

### 多分类器系统，入侵检测系统（IDS）

* 多分类系统（MCS）概述，MCS在安全分析中的优势
    * Lecture 10
    * Adaptive Intrusion Detection System via Online Learning, 2012

### 深度包检测

* 基于数据包载荷建模的网络入侵检测
    * Lecture 12
    * PAYL: Anomalous payload-based network intrusion detection, Wang-Stolfo 2004
* 把多个 One-class 分类器用于数据包有效载荷建模和网络检测
    * Lecture 15
    * McPAD : A Multiple Classifier System for Accurate Payload-based Anomaly Detection, Perdisci et al, 2009

### 多态性

* 多态混合攻击，无法为多态攻击建模
    * Lecture 14
    * Polymorphic Blending Attacks, Fogla et al, 2006
    * On the Infeasibility of Modeling Polymorphic Shellcode, Song et al, 2007

### 在安全领域使用机器学习

* 在安全领域使用机器学习的挑战与实践指导
    * Lecture 13
    * Outside the closed world: On using machine learning for network intrusion detection, Sommer-Paxson, 2010
    * Challenging the Anomaly Detection Paradigm: A Provocative Discussion, Gates-Taylor, 2007
    * The Base-Rate Fallacy and Its Implications for the Difficulty of Intrusion Detection, Axelsson, 1999

### 机器学习的敌人

* 机器学习的安全问题
    * Lecture 10
    * The security of machine learning, 2010

### 工业界做客讲座报告

* Wells Fargo
    * Lecture 6
    * Guest speaker Avi Avivi, VP Enterprise Information Security Architecture at Wells Fargo
* Union Bank
    * Lecture 9
    * Guest speaker Gary Lorenz, Chief Information Security Officer (CISO) and Managing Director at MUFG Union Bank
* 安全数据挖掘在Google（流传较广的报告）
    * Lecture 11
    * Guest speaker Massimiliano Poletto, head of Google Security Monitoring Tools group
* Industry Perspectives
    * Q&A with guest speaker Michael Fey, EVP and CTO of Intel Security Group (aka McAfee)

## 作业

### 作业1

* 作业 1. 僵尸网络
* 作业 2. Anomaly Detection via "Eigenface" of Command History
* 作业 3. Continuous Authentication via Biometric Behavior

### 作业2

* 作业 1. 测验
* 作业 2. 触摸信息识别
* 作业 3. 熵在攻击检测/诊断中的使用

### 作业3

* 电子邮件抓包分析

### 作业4

* 测验
