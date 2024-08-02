# [ShellCrash](https://github.com/juewuy/ShellCrash) 使用 [mihomo 内核](https://github.com/MetaCubeX/mihomo)进行 DNS 分流教程-ruleset 方案
注：
- 1. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则搭配 `rule-providers` 配置项
- 2. 只有 **DNS 模式选用 `reidir-host`（`fake-ip-filter: ['+.*']` 也算 redir-host 模式）** 时才需要进行 DNS 分流
- 3. 搭配 [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) 并将 AdGuardHome 作为上游时不要使用该方法
- 4. DNS 分流简单来说就是**指定国内域名走国内 DNS 解析，国外域名走国外 DNS 解析**
- 5. 此方案自定义规则参考 [DustinWin/ruleset_geodata/ruleset](https://github.com/DustinWin/ruleset_geodata#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
- 6. 所有步骤完成后，请连接 SSH 执行命令 `$CRASHDIR/start.sh restart` 后生效
---
# 一、 导入 mihomo 内核
可参考《[ShellCrash 配置-ruleset 方案/导入 mihomo 内核](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md#%E4%B8%80-%E5%AF%BC%E5%85%A5-mihomo-%E5%86%85%E6%A0%B8)》里的步骤进行操作
# 二、 ShellCrash 设置
1. 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 运行模式 -> 4 DNS 进阶设置，将“当前基础 DNS”和“PROXY-DNS”都设置为“null”  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/387ab5e9-b659-4469-9e74-aee264d53944" width="60%"/>

2. 其它设置可参考《[ShellCrash 配置-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md)》
# 三、 导入 user.yaml 文件
## 1. DNS 模式为 `fake-ip`
- 注：该模式不需要进行 DNS 分流，推荐导入我生成的 fakeip-user.yaml（集成 [fake-ip 地址过滤列表](https://github.com/juewuy/ShellCrash/blob/dev/public/fake_ip_filter.list)，提高了兼容性）

连接 SSH 后执行如下命令：
```
curl -o $CRASHDIR/yamls/user.yaml -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tutorials@clash/fakeip-user.yaml && $CRASHDIR/start.sh restart
```
## 2. DNS 模式为 `redir-host`
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/user.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
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
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
