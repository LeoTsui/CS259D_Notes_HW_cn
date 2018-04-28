# 入侵检测

<!-- TOC -->

- [入侵检测系统](#入侵检测系统)
    - [入侵检测](#入侵检测)
    - [入侵检测系统](#入侵检测系统-1)
    - [分类和优缺点](#分类和优缺点)
- [参考资料和推荐阅读](#参考资料和推荐阅读)

<!-- /TOC -->

## 入侵检测系统

### 入侵检测

* 入侵检测是监控网络或系统上恶意行为或违规操作的程序

### 入侵检测系统

![IDS](images/IDS.png)

* 入侵检测系统（Intrusion Detection System，IDS），一个软硬件结合的检测入侵的系统
* 当入侵可能发生时，发出警报

### 分类和优缺点

* 基于误用（基于签名）
    * 根据签名特征检测已知的攻击
    * 误报较少
    * 频繁地更新特征数据库
    * 无法检测 0-day 攻击
* 基于异常
    * 依据正常行为识别异常
    * 能够检测 0-day 攻击
    * 为每个系统定制正常活动的配置文件
    * 误报较多
* 混合
    * 结合基于误用与异常的检测系统
    * 提高检测率，减少误报的产生

## 参考资料和推荐阅读

* Network Anomaly Detection: Methods, Systems and Tools
* A Survey of Data Mining and Machine Learning Methods for Cyber Security Intrusion Detection
* [\[讲座\] A Survey of Data Mining and Machine Learning Methods for Cyber Security Intrusion Detection](http://www.parkjonghyuk.net/lecture/2017-2nd-lecture/forensic/s2.pdf) by Pradip Kumar Sharma
* The Use of Computational Intelligence in Intrusion Detection Systems: A Review
* [To use the concept of Data Mining and machine learning concept for Cyber security and Intrusion detection](https://www.slideshare.net/nishantmehta9849/to-use-the-concept-of-data-mining-and-machine-learning-concept-for-cyber-security-and-intrusion-detection)
