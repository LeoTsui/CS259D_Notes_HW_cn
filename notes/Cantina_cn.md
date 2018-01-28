# Cantina: A content-based approach to detecting phishing websites

<!-- TOC -->

- [背景知识与启发](#背景知识与启发)
- [论文目标和贡献](#论文目标和贡献)
- [数据来源](#数据来源)
- [CANTINA](#cantina)
    - [词汇](#词汇)
    - [特征：被谷歌收录了吗？](#特征被谷歌收录了吗)
    - [其余特征](#其余特征)
- [局限性](#局限性)
- [参考资料](#参考资料)

<!-- /TOC -->

## 背景知识与启发

* TF-IDF
    * 衡量文档中词汇的重要性
    * TF，文档中词汇的频率
    * IDF，衡量词汇在语料库中的流行程度
        * Log(N/包含词汇的文档数)
    * $$tf\textrm{-}idf (t, d, D) = tf(t, d) \times idf(t, D)$$
* 增强链接（Robust Hyperlinks）
    * 用于识别URL的词汇签名
    * 使用 TF-IDF 标记词汇
    * 实验：五个词汇足以确定一个网页
* 观测结果
    * 对原始网页的极小更改
        * 可由增强链接检测到
    * 钓鱼网站经常包含品牌名称
        * 常见于品牌网站
        * 不常见于整体网络

## 论文目标和贡献

* CANTINA 使用 TF-IDF 检测钓鱼网站
* 应用增强链接反钓鱼
* 不依赖黑名单

## 数据来源

* 英文网站
* 钓鱼网站网址
    * PhishTank.com
* 合法网站网址
    * 3Sharp 的反钓鱼工具栏的研究
    * 选择经常受到钓鱼者攻击的35个网站的登录页面
    * 从 Alexa 网页搜索中前35位的页面
    * 从 random.yahoo.com/fast/ryl 中选择30个随机页面
* 从邮件中收集网址

## CANTINA

### 单词

* 单词词频列表
    * 英国国家语料库
        * 共计 67,962,112 字，词
        * 9,022 个唯一单词
* 分析网页
    * 保存的网页
    * Document Object Model (DOM)

### 特征：被谷歌收录了吗？

* 假设
    * 钓鱼页面 pagerank 排名低
        * 缺少指向钓鱼页面的链接
        * 钓鱼站点在线平均时间为4.5天
* 工作流程
    * 计算词语的 TF-IDF
    * 找到前五个的单词
    * 把单词提交到 Google 搜索查询
    * 检查域名是够包含在前N个结果中
* 减少误报
    * 在词汇标记中包括域名
    * 零结果意味着钓鱼网站（Zero results Means Phishing，ZMP）

### 其余特征

* 域名的寿命
* 已知的图像
    * 存在于众所周知的 Logo 不同的 Logo
    * 确定目标前十名：eBay, PayPal, Citibank, Bank of America, Fifth Third Bank, Barclays Bank, ANZ Bank, Chase Bank, and Wells Fargo Bank
* 可疑网址
    * 域名包含 `@` 或 `–`
* 可疑的链接
    * 与可疑网址相同
* IP地址作为域名
* 网址中的点
    * 二值特征： `.` 的数量 > 5
* 形式
    * HTML 的 `<input>` 标签，带有诸如“信用卡”，“密码”等字样

## 局限性

* 未包含 JaveSvript
* 不适用于非英文网站
* 依赖于 Google 搜索查询
    * 超时
    * 被 Google 拒绝服务
    * 搜索引擎优化 Google PageRank 排名
* TF-IDF
    * 用图片代替文字
    * 不可见的文本

## 参考资料

* Cantina: A content-based approach to detecting phishing websites, Zhang et al, 2007
* Kim Giglia, CSC 682 [CANTINA.ppt](https://kosh.nku.edu/~waldenj/classes/2008/fall/csc682/presentations/CANTINA.ppt)
* CS 259D Lecture 16
