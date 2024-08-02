# [sing-box PuerNya 版内核](https://github.com/PuerNya/sing-box)配置 DNS 不泄露教程-geodata 方案
注：
- 1. 此方案适用于 [sing-box](https://github.com/SagerNet/sing-box)，采用 `geosite` 和 `geoip` 规则搭配 geosite.db 和 geoip.db [路由规则文件](https://github.com/MetaCubeX/meta-rules-dat)
- 2. 此方案彻底防止了 DNS 泄露（针对在规则集内的 `cn` 走国内 DNS 解析；针对在规则集内的 `geolocation-!cn` 走 `fakeip`；针对不在规则集内的域名由国外 DNS 解析，如果解析结果是国内 IP 则走国内 DNS 解析，否则走 `fakeip`），兼容性高，可放心使用
- 3. 可进入 https://ipleak.net 测试 DNS 是否泄露，“DNS Addresses” 栏目下没有中国国旗（因 `ipleak.net` 域名默认走代理），即代表 DNS 没有发生泄露
- 4. 此方案自定义规则参考[MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat)
- 5. 所有步骤完成后，请连接 SSH 执行命令 `$CRASHDIR/start.sh restart` 后生效
---
# 一、 导入 sing-box PuerNya 版内核
可参考《[ShellCrash 配置-geodata 方案/导入 sing-box PuerNya 版内核](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-geodata%20%E6%96%B9%E6%A1%88.md#%E4%B8%80-%E5%AF%BC%E5%85%A5-sing-box-puernya-%E7%89%88%E5%86%85%E6%A0%B8)》里的步骤进行操作
# 二、 ShellCrash 设置
1. 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 运行模式 -> 4 DNS 进阶设置，将“当前基础 DNS”和“PROXY-DNS”都设置为 `null`  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/4ac7a9ce-2c04-4adc-bbaf-8a1281886f5e" width="60%"/>

2. 其它设置可参考《[ShellCrash 配置-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-geodata%20%E6%96%B9%E6%A1%88.md)》
# 三、 `dns` 配置
连接 SSH 后执行 `vi $CRASHDIR/jsons/dns.json`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
{
  "dns": {
    "servers": [
      { "tag": "dns_block", "address": "rcode://success" },
      { "tag": "dns_direct", "address": [ "https://1.12.12.12/dns-query", "https://223.5.5.5/dns-query" ], "detour": "DIRECT" },
      { "tag": "dns_proxy", "address": [ "https://8.8.8.8/dns-query", "https://1.1.1.1/dns-query" ] },
      { "tag": "dns_fakeip", "address": "fakeip" }
    ],
    "rules": [
      { "outbound": "any", "server": "dns_direct" },
      { "clash_mode": "Direct", "query_type": [ "A", "AAAA" ], "server": "dns_direct" },
      { "clash_mode": "Global", "query_type": [ "A", "AAAA" ], "server": "dns_proxy" },
      { "geosite": [ "category-ads-all" ], "server": "dns_block" },
      { "geosite": [ "geolocation-!cn" ], "query_type": [ "A", "AAAA" ], "server": "dns_fakeip", "rewrite_ttl": 0 },
      { "geosite": [ "cn" ], "query_type": [ "A", "AAAA" ], "server": "dns_direct" },
      { "fallback_rules": [ { "geoip": [ "cn" ], "server": "dns_direct" }, { "match_all": true, "server": "dns_fakeip", "rewrite_ttl": 0 } ], "allow_fallthrough": true, "server": "dns_proxy" }
    ],
    "final": "dns_direct",
    "strategy": "prefer_ipv4",
    "independent_cache": true,
    "lazy_cache": true,
    "reverse_mapping": true,
    "mapping_override": true,
    "fakeip": { "enabled": true, "inet4_range": "198.18.0.0/15", "inet6_range": "fc00::/18" }
  }
}
```
