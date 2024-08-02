# [Clash Verge](https://github.com/clash-verge-rev/clash-verge-rev)（Windows 端）配置-ruleset 方案
- 注：此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则搭配 `rule-providers` 配置项
---
# 一、 导入配置
## 1. 导入配置文件
① 进入 Clash Verge -> 订阅，在“订阅文件链接”处粘贴《[生成带有自定义策略组和规则的 Clash 配置文件直链-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-ruleset%20%E6%96%B9%E6%A1%88.md)》中生成的 .yaml 配置文件直链，然后点击“导入”  
② 配置文件下载完成后，右击导入的配置文件，选择“编辑信息”，“更新间隔”设置为“1440”，然后“保存”  
③ 再次右击导入的配置文件，点击“使用”
## 2. 新建自定义配置
进入 Clash Verge -> 订阅，右击“全局扩展配置”，选择“编辑文件”，将原配置全部删除后粘贴如下内容并“保存”：
```
mode: rule
ipv6: true
log-level: error
allow-lan: true
unified-delay: false
tcp-concurrent: true
external-controller-tls: 127.0.0.1:9090
find-process-mode: strict
global-client-fingerprint: chrome

sniffer:
  enable: true
  parse-pure-ip: true
  sniff: {HTTP: {ports: [80, 8080-8880]}, TLS: {ports: [443, 8443]}, QUIC: {ports: [443, 8443]}}
  skip-domain: ['Mijia Cloud']

dns:
  enable: true
  prefer-h3: true
  ipv6: true
  listen: 0.0.0.0:1053
  fake-ip-range: 198.18.0.1/16
  enhanced-mode: fake-ip
  fake-ip-filter:
    - "*"
    - "+.lan"
    - "+.local"
  nameserver:
    - quic://dns.alidns.com:853
    - https://doh.pub/dns-query
```
# 二、 启动 Clash
1. 进入 Clash Verge -> 设置 -> 系统设置 -> 服务模式，点击右边的盾牌图标，点击“安装”，完成后启用“服务模式”
2. 进入设置 -> 系统设置 -> Tun 模式，点击右边的螺帽图标，“Tun 堆栈模式”选择“Mixed”（若无法选择，可重启一下软件），然后点击“保存”
3. 进入设置 -> Clash 设置，启用“局域网连接”和“IPv6”
4. 进入设置 -> Clash 设置 -> UWP 工具，点击“Exempt All”后再点击“Save Changes”
5. 进入设置 -> Clash 设置 -> 外部控制，将“外部控制器监听地址”修改为 `127.0.0.1:9090`，然后点击“保存”
6. 进入设置 -> Verge 设置 -> 杂项设置，将“默认测试链接”修改为 `https://www.gstatic.com/generate_204`，然后点击“保存”
7. 进入设置 -> 系统设置，启用“Tun 模式”
# 三、 在线 Dashboard 面板
推荐使用在线 Dashboard 面板 [metacubexd](https://github.com/metacubex/metacubexd)，访问地址：https://metacubex.github.io/metacubexd  
首次进入需要添加“后端地址”，输入 `http://127.0.0.1:9090` 并点击“添加”即可访问 Dashboard 面板  
<img src="https://github.com/user-attachments/assets/50f53070-dd17-4c6b-ace5-5e2a4f5bd019" width="60%"/>
