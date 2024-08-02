# 分享自己使用 [sing-box for Android](https://github.com/PuerNya/sing-box/actions/workflows/sfa.yml) 搭配 rule_set 方案的一套配置
# 声明：
1. 此方案适用于搭载 [sing-box PuerNya 版内核](https://github.com/PuerNya/sing-box/tree/building)的 sing-box for Android，采用 `rule_set` 规则，**属高度定制，仅供参考**
2. 规则参考 [DustinWin/ruleset_geodata/ruleset](https://github.com/DustinWin/ruleset_geodata/tree/master#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
3. 请根据自身情况进行修改，**适合自己的方案才是最好的方案**，如无特殊需求，可以照搬
---
# 一、 生成配置文件 .json 文件直链
具体方法请参考《[生成带有自定义出站和规则的 sing-box 配置文件直链-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BA%E7%AB%99%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20sing-box%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-ruleset%20%E6%96%B9%E6%A1%88.md)》，贴一下我使用的配置：
```
{
  "log": { "level": "error", "timestamp": true },
  "dns": {
    "hosts": {
      "miwifi.com": [ "192.168.31.1" ],
      "doh.pub": [ "1.12.12.12", "120.53.53.53", "2402:4e00::" ],
      "dns.alidns.com": [ "223.5.5.5", "223.6.6.6", "2400:3200::1", "2400:3200:baba::1" ],
      "dns.google": [ "8.8.8.8", "8.8.4.4", "2001:4860:4860::8888", "2001:4860:4860::8844" ],
      "cloudflare-dns.com": [ "1.1.1.1", "1.0.0.1", "2606:4700:4700::1111", "2606:4700:4700::1001" ]
    },
    "servers": [
      { "tag": "dns_block", "address": "rcode://success" },
      { "tag": "dns_direct", "address": [ "https://doh.pub/dns-query", "https://dns.alidns.com/dns-query" ], "detour": "DIRECT" },
      { "tag": "dns_proxy", "address": [ "https://dns.google/dns-query", "https://cloudflare-dns.com/dns-query" ] },
      { "tag": "dns_fakeip", "address": "fakeip" }
    ],
    "rules": [
      { "outbound": "any", "server": "dns_direct" },
      { "clash_mode": "Direct", "query_type": [ "A", "AAAA" ], "server": "dns_direct" },
      { "clash_mode": "Global", "query_type": [ "A", "AAAA" ], "server": "dns_proxy" },
      { "rule_set": [ "ads" ], "server": "dns_block", "disable_cache": true, "rewrite_ttl": 0 },
      { "rule_set": [ "proxy" ], "query_type": [ "A", "AAAA" ], "server": "dns_fakeip", "rewrite_ttl": 0 },
      { "rule_set": [ "cn" ], "query_type": [ "A", "AAAA" ], "server": "dns_direct" },
      { "fallback_rules": [ { "rule_set": [ "cnip" ], "server": "dns_direct" }, { "match_all": true, "server": "dns_fakeip", "rewrite_ttl": 0 } ], "allow_fallthrough": true, "server": "dns_proxy" }
    ],
    "final": "dns_direct",
    "strategy": "prefer_ipv4",
    "independent_cache": true,
    "lazy_cache": true,
    "reverse_mapping": true,
    "mapping_override": true,
    "fakeip": { "enabled": true, "inet4_range": "198.18.0.0/15", "inet6_range": "fc00::/18", "exclude_rule": { "rule_set": [ "fakeip-filter", "private" ] } }
  },
  "inbounds": [
    // 启动服务时如果出现 `tun-in` 报错，可将 `"stack": "mixed"` 修改为 `"stack": "system"`
    { "tag": "tun-in", "type": "tun", "interface_name": "sing-box", "inet4_address": "172.19.0.1/30", "inet6_address": "fdfe:dcba:9876::1/126", "auto_route": true, "strict_route": true, "stack": "mixed", "sniff": true }
  ],
  "outbounds": [
    { "tag": "🚀 节点选择", "type": "selector", "outbounds": [ "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点", "🆓 免费节点" ] },
    { "tag": "🐟 漏网之鱼", "type": "selector", "outbounds": [ "🚀 节点选择", "🎯 全球直连" ] },
    { "tag": "📈 网络测试", "type": "selector", "outbounds": [ "🎯 全球直连", "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点", "🆓 免费节点" ] },
    { "tag": "🤖 人工智能", "type": "selector", "outbounds": [ "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点" ] },
    { "tag": "🎮 游戏服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🪟 微软服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇬 谷歌服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🍎 苹果服务", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇨🇳 直连域名", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🇨🇳 直连 IP", "type": "selector", "outbounds": [ "🎯 全球直连", "🚀 节点选择" ] },
    { "tag": "🪜 代理域名", "type": "selector", "outbounds": [ "🚀 节点选择", "🎯 全球直连" ] },
    { "tag": "📲 电报消息", "type": "selector", "outbounds": [ "🚀 节点选择" ] },
    { "tag": "🖥️ 直连软件", "type": "selector", "outbounds": [ "🎯 全球直连" ] },
    { "tag": "🔒 私有网络", "type": "selector", "outbounds": [ "🎯 全球直连" ] },
    { "tag": "🛑 广告拦截", "type": "selector", "outbounds": [ "REJECT" ] },
    { "tag": "🎯 全球直连", "type": "selector", "outbounds": [ "DIRECT" ] },
    { "tag": "REJECT", "type": "block" },
    { "tag": "DIRECT", "type": "direct" },
    { "tag": "GLOBAL", "type": "selector", "outbounds": [ "DIRECT", "REJECT", "🇭🇰 香港节点", "🇹🇼 台湾节点", "🇯🇵 日本节点", "🇰🇷 韩国节点", "🇸🇬 新加坡节点", "🇺🇸 美国节点", "🆓 免费节点" ] },
    { "tag": "dns-out", "type": "dns" },
    // 若没有单个出站节点，须删除所有 `🆓 免费节点` 相关内容
    {
      "tag": "🆓 免费节点",
      "type": "vless",
      "server": "example.com",
      "server_port": 443,
      "uuid": "{uuid}",
      "network": "tcp",
      "tls": { "enabled": true, "server_name": "example.com", "insecure": false },
      "transport": { "type": "ws", "path": "/?ed=2048", "headers": { "Host": "example.com" } }
    },
    { "tag": "🇭🇰 香港节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇭🇰" ] },
    { "tag": "🇹🇼 台湾节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇹🇼" ] },
    { "tag": "🇯🇵 日本节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇯🇵" ] },
    { "tag": "🇰🇷 韩国节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇰🇷" ] },
    { "tag": "🇸🇬 新加坡节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇸🇬" ] },
    { "tag": "🇺🇸 美国节点", "type": "urltest", "tolerance": 50, "use_all_providers": true, "includes": [ "🇺🇸" ] }
  ],
  "outbound_providers": [
    {
      "tag": "🛫 我的机场",
      "type": "remote",
      // 修改为你的 Clash 订阅链接
      "download_url": "https://example.com/xxx/xxx&flag=clash",
      "path": "./airport.yaml",
      "download_interval": "24h",
      "download_ua": "clash.meta",
      "includes": [ "🇭🇰|🇹🇼|🇯🇵|🇰🇷|🇸🇬|🇺🇸" ],
      "healthcheck_url": "https://www.gstatic.com/generate_204",
      "healthcheck_interval": "10m"
    }
  ],
  "route": {
    "rules": [
      { "protocol": [ "dns" ], "outbound": "dns-out" },
      { "clash_mode": "Direct", "outbound": "DIRECT" },
      { "clash_mode": "Global", "outbound": "GLOBAL" },
      { "rule_set": [ "ads" ], "outbound": "🛑 广告拦截" },
      { "rule_set": [ "applications" ], "outbound": "🖥️ 直连软件" },
      { "rule_set": [ "private" ], "outbound": "🔒 私有网络" },
      { "rule_set": [ "microsoft-cn" ], "outbound": "🪟 微软服务" },
      { "rule_set": [ "apple-cn" ], "outbound": "🍎 苹果服务" },
      { "rule_set": [ "google-cn" ], "outbound": "🇬 谷歌服务" },
      { "rule_set": [ "games-cn" ], "outbound": "🎮 游戏服务" },
      { "rule_set": [ "ai" ], "outbound": "🤖 人工智能" },
      { "rule_set": [ "networktest" ], "outbound": "📈 网络测试" },
      { "rule_set": [ "proxy" ], "outbound": "🪜 代理域名" },
      { "rule_set": [ "cn" ], "outbound": "🇨🇳 直连域名" },
      { "rule_set": [ "telegramip" ], "outbound": "📲 电报消息", "skip_resolve": true },
      { "rule_set": [ "privateip" ], "outbound": "🔒 私有网络", "skip_resolve": true },
      { "rule_set": [ "cnip" ], "outbound": "🇨🇳 直连 IP" }
    ],
    "rule_set": [
      {
        "tag": "fakeip-filter",
        "type": "remote",
        "format": "binary",
        "path": "./fakeip-filter.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/fakeip-filter.srs"
      },
      {
        "tag": "ads",
        "type": "remote",
        "format": "binary",
        "path": "./ads.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/ads.srs"
      },
      {
        "tag": "applications",
        "type": "remote",
        "format": "binary",
        "path": "./applications.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/applications.srs"
      },
      {
        "tag": "private",
        "type": "remote",
        "format": "binary",
        "path": "./private.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/private.srs"
      },
      {
        "tag": "microsoft-cn",
        "type": "remote",
        "format": "binary",
        "path": "./microsoft-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/microsoft-cn.srs"
      },
      {
        "tag": "apple-cn",
        "type": "remote",
        "format": "binary",
        "path": "./apple-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/apple-cn.srs"
      },
      {
        "tag": "google-cn",
        "type": "remote",
        "format": "binary",
        "path": "./google-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/google-cn.srs"
      },
      {
        "tag": "games-cn",
        "type": "remote",
        "format": "binary",
        "path": "./games-cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/games-cn.srs"
      },
      {
        "tag": "ai",
        "type": "remote",
        "format": "binary",
        "path": "./ai.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/ai.srs"
      },
      {
        "tag": "networktest",
        "type": "remote",
        "format": "binary",
        "path": "./networktest.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/networktest.srs"
      },
      {
        "tag": "proxy",
        "type": "remote",
        "format": "binary",
        "path": "./proxy.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/proxy.srs"
      },
      {
        "tag": "cn",
        "type": "remote",
        "format": "binary",
        "path": "./cn.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/cn.srs"
      },
      {
        "tag": "telegramip",
        "type": "remote",
        "format": "binary",
        "path": "./telegramip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/telegramip.srs"
      },
      {
        "tag": "privateip",
        "type": "remote",
        "format": "binary",
        "path": "./privateip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/privateip.srs"
      },
      {
        "tag": "cnip",
        "type": "remote",
        "format": "binary",
        "path": "./cnip.srs",
        "url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/cnip.srs"
      }
    ],
    "final": "🐟 漏网之鱼",
    "auto_detect_interface": true,
    "override_android_vpn": true,
    "concurrent_dial": true
  },
  "experimental": {
    "cache_file": { "enabled": true },
    "clash_api": {
      "external_controller": "127.0.0.1:9090",
      "secret": "",
      "default_mode": "Rule"
    }
  }
}
```
# 二、 导入配置文件并启动 sing-box
1. 进入 sing-box for Android -> Profiles -> New Profile，“Type”选择“Remote”，在“URL”处粘贴第《一》中生成的配置文件 .json 直链，“Auto Update Interval”填写“1440”，最后点击“Create”
2. 进入 sing-box for Android -> Dashboard，点击“▶️”图标即可启动 sing-box 服务
- 注：首次启用可能会报错，重试几次即可
# 三、 在线 Dashboard 面板
推荐使用在线 Dashboard 面板 [metacubexd](https://github.com/metacubex/metacubexd)，访问地址：https://metacubex.github.io/metacubexd  
首次进入 https://metacubex.github.io/metacubexd 需要添加“后端地址”，输入 `http://127.0.0.1:9090` 并点击“添加”即可访问 Dashboard 面板  
<img src="https://github.com/user-attachments/assets/b5cc5318-4a2f-4930-bea1-6e3e6eeae236" width="60%"/>
