# EXPOSURE: Finding Malicious Domains Using Passive DNS Analysis

<!-- TOC -->

- [背景知识与启发](#%E8%83%8C%E6%99%AF%E7%9F%A5%E8%AF%86%E4%B8%8E%E5%90%AF%E5%8F%91)
- [论文目标与贡献](#%E8%AE%BA%E6%96%87%E7%9B%AE%E6%A0%87%E4%B8%8E%E8%B4%A1%E7%8C%AE)
- [EXPOSURE 概述](#exposure-%E6%A6%82%E8%BF%B0)
- [收集训练数据](#%E6%94%B6%E9%9B%86%E8%AE%AD%E7%BB%83%E6%95%B0%E6%8D%AE)
- [特征](#%E7%89%B9%E5%BE%81)
    - [基于时间的特征](#%E5%9F%BA%E4%BA%8E%E6%97%B6%E9%97%B4%E7%9A%84%E7%89%B9%E5%BE%81)
        - [变化点检测 （Change Point Detection， CPD）](#%E5%8F%98%E5%8C%96%E7%82%B9%E6%A3%80%E6%B5%8B-%EF%BC%88change-point-detection%EF%BC%8C-cpd%EF%BC%89)
        - [检测每日近似行为](#%E6%A3%80%E6%B5%8B%E6%AF%8F%E6%97%A5%E8%BF%91%E4%BC%BC%E8%A1%8C%E4%B8%BA)
    - [基于 DNS 应答的特征](#%E5%9F%BA%E4%BA%8E-dns-%E5%BA%94%E7%AD%94%E7%9A%84%E7%89%B9%E5%BE%81)
    - [基于 TTL 的特征](#%E5%9F%BA%E4%BA%8E-ttl-%E7%9A%84%E7%89%B9%E5%BE%81)
    - [基于域名的特征](#%E5%9F%BA%E4%BA%8E%E5%9F%9F%E5%90%8D%E7%9A%84%E7%89%B9%E5%BE%81)
- [Training](#training)
    - [The Classifier](#the-classifier)
        - [C4.5 decision tree algorithm](#c45-decision-tree-algorithm)
- [Evaluation](#evaluation)
- [Limitation (Evasion)](#limitation-evasion)
- [参考资料](#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

<!-- /TOC -->

## 背景知识与启发

* 典型 Internet 攻击
    * 僵尸网络
    * 钓鱼
* 攻击者和企业一样面临同样的工程技术挑战
    * 实现可靠和灵活的服务器基础架构
    * DNS 的优势
        * 变换 IP
        * 隐藏关键服务器
        * 灵活地迁移服务器
        * 服务器比 IP 更“容错”

## 论文目标与贡献

* 检测恶意域名
    * 被动式
    * 没有先验知识
* 15 个行为特征
    * 9 个新特征

## EXPOSURE 概述

![EXPOSURE 概述](images/EXPOSURE.png)

* 五个主要部分
    * 数据收集
    * 特征提取
    * 恶意和良性域名收集（有标签数据）
        * 恶意
        * 良性
    * 学习模块
    * 分类
        * 给为标记的域名分类

## 收集训练数据

* 来自（Security Information Exchange，SIE）的 DNS 流量
    * 来自北美和欧洲的权威名称服务器的响应数据
    * 2.5个月
    * 超过1000亿 DNS 查询（平均每分钟100万次查询）
    * 480万个不同的域名
    * 过滤
        * Alexa排名前1000（减少20％）
        * 超过1年的域名（减少50％以上）
* 恶意域名
    * 3500个域名
    * 类型
        * 僵尸网络 C&C
        * 驱动下载网站
        * 网络钓鱼/诈骗页面
    * 示例
        * `malwaredomains.com``
        * Zeus 屏蔽列表
* 良性的域名
    * 3000个域名
    * 示例
        * Alexa top 1000

## 特征

* F1: 基于时间的特征
    * 存活时间短
    * 每日相似程度
    * 模式重复
    * 访问比例
* F2: 基于 DNS 应答的特征
    * 不同 IP 地址的数量
    * 不同国家的数量
    * 共享域名的数量
    * 反向 DNS 查询结果
* F3: 基于 TTL 的特征
    * TTL 的平均数
    * TTL 标准差
    * 不同 TTL 值的数量
    * TTL 的变化次数
    * 使用特定的 TTL 范围的百分比
* F4: 基于域名的特征
    * 数字字符的百分比
    * LMS 长度的百分比

### 基于时间的特征

* 启发
    * 恶意域名的请求数量往往急增急减
* 分析方法
    * 将时段分入为固定长度的间隔
    * 统计查询次数
* 全局范围
    * 存活时间短
        * DGA（域生成算法，domain generation algorithm）生成域名
    * 改变行为
* 本地范围
    * 主要思路
        * 放大域名的生命周期
        * 研究行为特征
    * 每日相似程度
        * 每日以相同的时间间隔增加或减少请求数
    * 模式重复
        * 变化点检测（change point detection，CPD）
        * 变化数量
        * 检测到的变化的持续时间的标准差
    * 访问比例
        * 空闲还是热门

#### 变化点检测 （Change Point Detection， CPD）

* 运作于时间序列之上
* 目标是找到那些数据值突然改变的点
* 检测突变
    * 对每个域名的时间序列
        * $$P(t)$$，在小时 $$t$$ 时的请求数量，并由最大值做标准化
        * 迭代时间间隔 $$t = 3600s$$
        * $$P^-(t)$$, 过去 $$8$$ 个时间间隔的平均数
        * $$P^+(t)$$, 未来 $$8$$ 个时间间隔的平均数
        * $$d(t) = |P^+(t) - P^-(t)|$$
    * 对 $$d(t)$$ 做累加求和
        * 对于检测时间 $$t$$, 当 $$d(t)$$ 够大并且是 $$local\_max$$
        * $$CUSUM(t) = Max\{0, CUSUM(t-1) + d(t) - local\_max\}$$
        * 如果 $$CUSUM(t) > cusum\_max$$，就把 $$t$$ 作为监测点报告

#### 检测每日近似行为

* 计算所有时间序列对的距离
    * 通过平均值和标准差对时间序列做标准化
    * 计算欧几里得距离
* $$d_{ij}$$，第$$i$$天和第$$j$$天之间的欧几里得距离
* $$D$$，$$d_{ij}$$所有只的平均数

### 基于 DNS 应答的特征

* Domains map to multiple IPs, and IPs be shared across different domains
* Number of distinct IP addresses
*    Resolved for a domain during the experiment
* Number of distinct countries
* Number of domains share the IP with
    * Can be large for web hosting providers as well
    * Reduce false positives by looking for reverse DNS query results on Google top 3 search results
* Reverse DNS query results
* Reduce false positives
*    Reverse DNS answer from Google

### 基于 TTL 的特征

* TTL(Time To Live): Length of time to cache a DNS response
    * Recommended between 1-5 days
* Insight
    * Sophisticated infrastructure of malicious networks cause frequent TTL changes
    * Setting lower TTL values to the less reliable hosts
* Average TTL
    * High availability systems
        * Low TTL values
        * Round Robin DNS
        * Example: CDNs, Fast Flux botnets
* Standard Deviation of TTL
    * Compromised home computers (dynamic IP) assigned much shorter TTL than compromised servers (static IP)
* Number of distinct TTL values
* Number of TTL change
    * Higher in malicious domains
* Percentage usage of specific TTL ranges
    * Considered ranges: $$[0,1), [1,10), [10,100), [100,300), [300,900), [900, inf)$$
    * Malicious domains peak at $$[0, 100)$$ ranges

### 基于域名的特征

* Insight
    * Easy-to-remember names
    * Important for benign services
        * Main purpose of DNS
    * Unimportant for attackers (e.g., DGA-generated)
* Ratio of numerical characters to name length
* Ratio of length of the longest meaningful substring to length of domain name
*    Query name on Google & check # of hits vs a threshold
* Combined to decrease false positives
* Features applied to only second-level domains(SLD)
* Other possible feature: entropy of the domain name
    * DGA-generated names more random than human-generated

## Training

* Training period
    * Initial period of 7 days (for time-based features)
    * Retraining every day

### The Classifier

* J48 decision tree
    * C4.5
* Feature selection
    * Percentage of miss-classified instances

#### C4.5 decision tree algorithm

* Check for base cases
* For each attribute
    * Compute attribute's normalized information gain
* Split over attribute with highest gain
* Recurse
* Normalized information gain = difference in entropy of class values

## Evaluation

* Evaluation of the Detection Rate
    * 216 domains reported by malwareurls.com & present in dataset
    * 5 had less than 20 queries
        * be filtered
    * 211 detected malicious
    * Detection rate: 98%
* Evaluation of the False Positives
    * Filter out domains with < 20 requests in 2.5 months (300,000 domains remaining)
    * 17,686 detected as malicious (5.9%)
    * Unlabeled domains
        * Hard to verify manually
    * Verification
        * Google searches
        * Well-known spam lists
        * Norton Safe Web
        * McAfee Site Advisor
    * False positive rate: 7.9%

## Limitation (Evasion)

* Attackers have prior information about EXPOSURE
* Assign uniform TTL values across all compromised machines
    * Reduces attacker's infrastructure reliability
* Reduce number of DNS lookups of malicious domain
    * Not trivial to implement
    * Reduces attacker's impact
    * Requires high degree of coordination

## 参考资料

* EXPOSURE: Finding Malicious Domains Using Passive DNS Analysis (2011)
* CS 259D Lecture 3
