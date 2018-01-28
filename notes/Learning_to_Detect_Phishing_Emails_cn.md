# Learning to Detect Phishing Emails

<!-- TOC -->

- [背景知识和洞见](#背景知识和洞见)
- [本文目标和贡献](#本文目标和贡献)
- [数据来源](#数据来源)
- [特征](#特征)
- [网页分类的特征](#网页分类的特征)
- [机器学习实践](#机器学习实践)
- [参考资料](#参考资料)

<!-- /TOC -->

## 背景知识和洞见

* 网络钓鱼
    * 目标
        * 帐户信息
        * 登录凭证
        * 身份信息
    * 攻击路径
        * 看起来合规的邮件
        * 看起来合规的网站
* 检测
    * 工具栏
        * 例子
            * Spoofguard
            * Netcraft
        * 缺点
            * 上下文信息少
            * 用户要参与决策
    * Email 过滤
        * 例子
            * SpamAssassin
            * Spamato
        * 有点
            * 更完整的上下文信息（内容，标题等）
            * 判别工程对用户屏蔽

## 本文目标和贡献

* 基于机器学习方法的 e-mail 过滤器

## 数据来源

* SpamAssassin ham 语料库
    * 大约6950封非钓鱼非垃圾邮件
* 公开可获得的 phishingcorpus
    * 大约860封钓鱼邮件
    * WHOIS 查询带来的问题
        * 只从870个域名中提取了505个域名
        * 提高了假阴性率

## 特征

* 基于 IP 的 URL 地址
    * 例如：`http://192.168.0.1/paypal.cgi?fix_account`
    * 被入侵的电脑没有 DNS 条目
    * 不流行
    * 二值特征
* 链接到的域名的寿命
    * 注册看起来合法的域名
        * 例如：`playpal.com`，`paypal-update.com`
    * 通常寿命较短
        * 使用被盗的信用卡注册，由注册者取消
        * 域名反钓鱼监控者捕获
        * 通常只存在 48 小时
    * 通过 WHOIS 查询获得
    * 二值特征: 生存时间 < 60 天
* 不匹配的 URL
    * 例如：`<a href="badsite.com"> paypal.com</a>`
    * 二值特征: URL 文本不同于 `href`
* “点击这里”链接到“非期望中”的域名
    * 例如：点击这里保存账户
    * “期望中”的域名：域名经常被链接到
    * 二值特征：文本包含“点击这里”的链接链接到的域名不是“期望中”的域名
* HTML 邮件
    * 二值特征：电子邮件部分有 MIME 类型的 text/html
* 链接数量
    * 连续特征（数值特征）：邮件中 HTML 部分的连接数
    * 由 `href` 属性的 `href` 标签来定义链接
        * 包含 `mailto:` 链接
* 域名数量
    * 以 http/https 开头的网址域名
    * 只包含域名的“主要部分”
        * 注册商从哪获得回报
            * 不要拘泥于顶级域名和二级域名
        * 例如：
            * `university.edu` 之于 `www.cs.university.edu`
            * `company.co.jp` 之于 `www.company.co.jp`
                * 顶级域名：`.jp`
                * 二级域名`.co`
    * 数值特征：不同域名的数量
* 点的数量
    * 子域名：`http://www.my-bank.update.data.com`
    * 重定向脚本：`http://www.google.com/url?q=http://www.badsite.com`
        * 对于无戒心的用户而言，网址看似来自 `google.com` 
        * 浏览器重定向到 `badsite.com`
    * 数值特征：电子邮件中任何链接的最大点数
* 包含的 javascript
    * 二值特征: "javascript"字串出现在邮件中
    * 在 `<script>` 或 `<a>` 标签中
* 垃圾邮件过滤器的结果
    * 二值特征: SpamAssassin 识别出的类别

## 网页分类的特征

* 浏览器历史记录中的网站
    * 一个没有被访问过的网站（不在历史记录中）很有可能是钓鱼网站
* 网站重定向
* tf-idf
    * 定一个页面的关键词

## 机器学习实践

* 10-fold 交叉验证
* 分类器:随机森林
    * 10个决策树
    * 基于随机的属性做决定
    * 剪枝

## 参考资料

* Learning to Detect Phishing Emails, Fette et al, 2007
* CS 259D Lecture 16
