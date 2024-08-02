# [Clash Verge](https://github.com/clash-verge-rev/clash-verge-rev) 使用 [mihomo 内核](https://github.com/MetaCubeX/mihomo)进行 DNS 分流教程-ruleset 方案
注：
- 1. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则搭配 `rule-providers` 配置项
- 2. 只有 **DNS 模式选用 `reidir-host`（`fake-ip-filter: ['+.*']` 也算 redir-host 模式）** 时才需要进行 DNS 分流
- 3. DNS 分流简单来说就是**指定国内域名走国内 DNS 解析，国外域名走国外 DNS 解析**
- 4. 此方案自定义规则参考 [DustinWin/ruleset_geodata/ruleset](https://github.com/DustinWin/ruleset_geodata/tree/master#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
---

# 一、 DNS 模式为 `fake-ip`
- 注：该模式不需要进行 DNS 分流，推荐导入我生成的 fakeip-user.yaml（集成 [fake-ip 地址过滤列表](https://github.com/juewuy/ShellClash/blob/dev/public/fake_ip_filter.list)，提高了兼容性）

以管理员身份运行 CMD，执行如下命令：
```
taskkill /f /t /im "Clash Verge*"
taskkill /f /t /im Clash-Verge*
taskkill /f /t /im clash-meta*
curl -o "%APPDATA%\io.github.clash-verge-rev.clash-verge-rev\profiles\Merge.yaml" -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tutorials@clash/fakeip-user.yaml
```
# 二、 DNS 模式为 `redir-host`
进入 Clash Verge -> 订阅，右击“全局扩展配置”，选择“编辑文件”，将原配置全部删除后粘贴如下内容并“保存”：
```
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
  fake-ip-filter: ['+.*']
  nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query
  nameserver-policy:
    'rule-set:ads': rcode://success
    'rule-set:proxy': [https://dns.google/dns-query, https://cloudflare-dns.com/dns-query]
    'rule-set:cn': [https://doh.pub/dns-query, https://dns.alidns.com/dns-query]
```
