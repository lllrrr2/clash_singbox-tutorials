# [mihomo 内核](https://github.com/MetaCubeX/mihomo)配置 DNS 不泄露教程-ruleset 方案
注：
- 1. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则搭配 `rule-providers` 配置项
- 2. 此方案彻底防止了 DNS 泄露（针对不在规则集内的域名和 IP 全部走 `fake-ip` 或国外 DNS 解析），配置简单粗暴，兼容性无法保证，请慎用
- 3. 可进入 https://ipleak.net 测试 DNS 是否泄露，“DNS Addresses” 栏目下没有中国国旗（因 `ipleak.net` 域名默认走代理），即代表 DNS 没有发生泄露
- 4. 此方案自定义规则参考[DustinWin/ruleset_geodata/ruleset](https://github.com/DustinWin/ruleset_geodata#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
- 5. 所有步骤完成后，请连接 SSH 执行命令 `$CRASHDIR/start.sh restart` 后生效
---
# 一、 导入 mihomo 内核和路由规则文件
可参考《[ShellCrash 配置-ruleset 方案/导入 mihomo 内核](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md#%E4%B8%80-%E5%AF%BC%E5%85%A5-mihomo-%E5%86%85%E6%A0%B8)》里的步骤进行操作
# 二、 额外编辑配置文件
在《[生成带有自定义策略组和规则的 Clash 配置文件直链-ruleset 方案/添加模板](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-ruleset%20%E6%96%B9%E6%A1%88.md#%E4%BA%8C-%E6%B7%BB%E5%8A%A0%E6%A8%A1%E6%9D%BF)》编辑 .yaml 配置文件时，将 `rules` 里所有 IP 相关的规则末尾加上 `no-resolve`，即修改为：
```
  - RULE-SET,telegramip,📲 电报消息,no-resolve
  - RULE-SET,privateip,🔒 私有网络,no-resolve
  - RULE-SET,cnip,🇨🇳 直连 IP,no-resolve
```
# 三、 ShellCrash 设置
1. 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 运行模式 -> 4 DNS 进阶设置，将“当前基础 DNS”和“PROXY-DNS”都设置为 `null`  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/4ac7a9ce-2c04-4adc-bbaf-8a1281886f5e" width="60%"/>

2. 其它设置可参考《[ShellCrash 配置-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md)》
# 四、 user.yaml 配置
## 1. DNS 模式为 `fake-ip`
连接 SSH 后执行 `vi $CRASHDIR/yamls/user.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
dns:
  enable: true
  prefer-h3: true
  ipv6: true
  listen: 0.0.0.0:1053
  fake-ip-range: 198.18.0.1/16
  enhanced-mode: fake-ip
  fake-ip-filter:
    - '*'
    - '+.lan'
    - '+.local'
  nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
## 2. DNS 模式为 `redir-host`
连接 SSH 后执行 `vi $CRASHDIR/yamls/user.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
dns:
  enable: true
  prefer-h3: true
  ipv6: true
  listen: 0.0.0.0:1053
  fake-ip-range: 198.18.0.1/16
  enhanced-mode: fake-ip
  fake-ip-filter: ['+.*']
  nameserver:
    - https://dns.google/dns-query
    - https://cloudflare-dns.com/dns-query
  proxy-server-nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query
  nameserver-policy:
    'rule-set:category-ads-all': rcode://success
    'rule-set:microsoft-cn,apple-cn,google-cn,games-cn,cn,private': [https://doh.pub/dns-query, https://dns.alidns.com/dns-query]
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
