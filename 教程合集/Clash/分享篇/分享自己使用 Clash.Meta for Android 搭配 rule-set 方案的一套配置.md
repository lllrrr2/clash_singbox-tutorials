# 分享自己使用 [Clash.Meta for Android](https://github.com/MetaCubeX/ClashMetaForAndroid) 搭配 rule-set 方案的一套配置
# 声明：
1. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则，**属高度定制，仅供参考**
2. 规则参考 [DustinWin/ruleset_geodata/ruleset](https://github.com/DustinWin/ruleset_geodata/tree/master#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
3. 请根据自身情况进行修改，**适合自己的方案才是最好的方案**，如无特殊需求，可以照搬
4. 此方案适用于 Clash.Meta for Android
---
# 一、 生成配置文件 .yaml 文件直链
具体方法请参考《[生成带有自定义策略组和规则的 Clash 配置文件直链-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-ruleset%20%E6%96%B9%E6%A1%88.md)》，贴一下我使用的配置：
```
proxy-providers:
  🛫 我的机场:
    type: http
    # 修改为你的 Clash 订阅链接
    url: "https://example.com/xxx/xxx&flag=clash"
    path: ./proxies/airport.yaml
    interval: 86400
    filter: "🇭🇰|🇹🇼|🇯🇵|🇰🇷|🇸🇬|🇺🇸"
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600

mode: rule
ipv6: true
log-level: error
allow-lan: true
mixed-port: 7890
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

tun:
  enable: true
  stack: system
  dns-hijack: [any:53]
  auto-route: true
  auto-detect-interface: true
  device: mihomo
  strict-route: true

dns:
  enable: true
  prefer-h3: true
  ipv6: true
  listen: 0.0.0.0:1053
  fake-ip-range: 198.18.0.1/16
  enhanced-mode: fake-ip
  fake-ip-filter:
    - '*'
    - '*.lan'
    - '*.localdomain'
    - '*.example'
    - '*.invalid'
    - '*.localhost'
    - '*.test'
    - '*.local'
    - '*.home.arpa'
    - 'time.*.com'
    - 'time.*.gov'
    - 'time.*.edu.cn'
    - 'time.*.apple.com'
    - 'time-ios.apple.com'
    - 'time1.*.com'
    - 'time2.*.com'
    - 'time3.*.com'
    - 'time4.*.com'
    - 'time5.*.com'
    - 'time6.*.com'
    - 'time7.*.com'
    - 'ntp.*.com'
    - 'ntp1.*.com'
    - 'ntp2.*.com'
    - 'ntp3.*.com'
    - 'ntp4.*.com'
    - 'ntp5.*.com'
    - 'ntp6.*.com'
    - 'ntp7.*.com'
    - '*.time.edu.cn'
    - '*.ntp.org.cn'
    - '+.pool.ntp.org'
    - 'time1.cloud.tencent.com'
    - 'music.163.com'
    - '*.music.163.com'
    - '*.126.net'
    - 'musicapi.taihe.com'
    - 'music.taihe.com'
    - 'songsearch.kugou.com'
    - 'trackercdn.kugou.com'
    - '*.kuwo.cn'
    - 'api-jooxtt.sanook.com'
    - 'api.joox.com'
    - 'joox.com'
    - 'y.qq.com'
    - '*.y.qq.com'
    - 'streamoc.music.tc.qq.com'
    - 'mobileoc.music.tc.qq.com'
    - 'isure.stream.qqmusic.qq.com'
    - 'dl.stream.qqmusic.qq.com'
    - 'aqqmusic.tc.qq.com'
    - 'amobile.music.tc.qq.com'
    - '*.xiami.com'
    - '*.music.migu.cn'
    - 'music.migu.cn'
    - '+.msftconnecttest.com'
    - '+.msftncsi.com'
    - 'localhost.ptlogin2.qq.com'
    - 'localhost.sec.qq.com'
    - '+.qq.com'
    - '+.tencent.com'
    - '+.steamcontent.com'
    - '+.srv.nintendo.net'
    - '*.n.n.srv.nintendo.net'
    - '+.cdn.nintendo.net'
    - '+.stun.playstation.net'
    - 'xbox.*.*.microsoft.com'
    - '*.*.xboxlive.com'
    - 'xbox.*.microsoft.com'
    - 'xnotify.xboxlive.com'
    - '+.battlenet.com.cn'
    - '+.wotgame.cn'
    - '+.wggames.cn'
    - '+.wowsgame.cn'
    - '+.wargaming.net'
    - 'proxy.golang.org'
    - 'stun.*.*'
    - 'stun.*.*.*'
    - '+.stun.*.*'
    - '+.stun.*.*.*'
    - '+.stun.*.*.*.*'
    - '+.stun.*.*.*.*.*'
    - 'heartbeat.belkin.com'
    - '*.linksys.com'
    - '*.linksyssmartwifi.com'
    - '*.router.asus.com'
    - 'mesu.apple.com'
    - 'swscan.apple.com'
    - 'swquery.apple.com'
    - 'swdownload.apple.com'
    - 'swcdn.apple.com'
    - 'swdist.apple.com'
    - 'lens.l.google.com'
    - 'stun.l.google.com'
    - 'na.b.g-tun.com'
    - '+.nflxvideo.net'
    - '*.square-enix.com'
    - '*.finalfantasyxiv.com'
    - '*.ffxiv.com'
    - '*.ff14.sdo.com'
    - 'ff.dorado.sdo.com'
    - '*.mcdn.bilivideo.cn'
    - '+.media.dssott.com'
    - 'shark007.net'
    - 'Mijia Cloud'
    - '+.market.xiaomi.com'
    - '+.cmbchina.com'
    - '+.cmbimg.com'
    - 'adguardteam.github.io'
    - 'adrules.top'
    - 'anti-ad.net'
    - 'local.adguard.org'
    - 'static.adtidy.org'
    - '+.sandai.net'
    - '+.n0808.com'
    - '+.3gppnetwork.org'
    - '+.uu.163.com'
    - 'ps.res.netease.com'
    - '+.oray.com'
  nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query

# 若没有单个出站代理节点，须删除所有 `🆓 免费节点` 相关内容
proxies:
  - name: 🆓 免费节点
    type: vless
    server: example.com
    port: 443
    uuid: {uuid}
    network: ws
    tls: true
    udp: false
    sni: example.com
    client-fingerprint: chrome
    ws-opts:
      path: "/?ed=2048"
      headers:
        host: example.com

proxy-groups:
  - {name: 🚀 节点选择, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇰🇷 韩国节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  # 若机场的 UDP 质量不是很好，导致某游戏无法登录或进入房间，可以添加 `disable-udp: true` 配置项解决
  - {name: 🐟 漏网之鱼, type: select, proxies: [🚀 节点选择, 🎯 全球直连]}
  - {name: 📈 网络测试, type: select, proxies: [🎯 全球直连, 🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇰🇷 韩国节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  - {name: 🤖 人工智能, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇰🇷 韩国节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点]}
  - {name: 🎮 游戏服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🪟 微软服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇬 谷歌服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🍎 苹果服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇨🇳 直连域名, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇨🇳 直连 IP, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🪜 代理域名, type: select, proxies: [🚀 节点选择, 🎯 全球直连]}
  - {name: 📲 电报消息, type: select, proxies: [🚀 节点选择]}
  - {name: 🖥️ 直连软件, type: select, proxies: [🎯 全球直连]}
  - {name: 🔒 私有网络, type: select, proxies: [🎯 全球直连]}
  - {name: 🛑 广告拦截, type: select, proxies: [REJECT]}
  - {name: 🎯 全球直连, type: select, proxies: [DIRECT]}

  - {name: 🇭🇰 香港节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇭🇰"}
  - {name: 🇹🇼 台湾节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇹🇼"}
  - {name: 🇯🇵 日本节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇯🇵"}
  - {name: 🇰🇷 韩国节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇰🇷"}
  - {name: 🇸🇬 新加坡节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇸🇬"}
  - {name: 🇺🇸 美国节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇺🇸"}

rule-providers:
  ads:
    type: http
    behavior: domain
    format: text
    path: ./rules/ads.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/ads.list"
    interval: 86400

  applications:
    type: http
    behavior: classical
    format: text
    path: ./rules/applications.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/applications.list"
    interval: 86400

  private:
    type: http
    behavior: domain
    format: text
    path: ./rules/private.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/private.list"
    interval: 86400

  microsoft-cn:
    type: http
    behavior: domain
    format: text
    path: ./rules/microsoft-cn.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/microsoft-cn.list"
    interval: 86400

  apple-cn:
    type: http
    behavior: domain
    format: text
    path: ./rules/apple-cn.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/apple-cn.list"
    interval: 86400

  google-cn:
    type: http
    behavior: domain
    format: text
    path: ./rules/google-cn.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/google-cn.list"
    interval: 86400

  games-cn:
    type: http
    behavior: domain
    format: text
    path: ./rules/games-cn.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/games-cn.list"
    interval: 86400

  ai:
    type: http
    behavior: domain
    format: text
    path: ./rules/ai.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/ai.list"
    interval: 86400

  networktest:
    type: http
    behavior: classical
    format: text
    path: ./rules/networktest.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/networktest.list"
    interval: 86400

  proxy:
    type: http
    behavior: domain
    format: text
    path: ./rules/proxy.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/proxy.list"
    interval: 86400

  cn:
    type: http
    behavior: domain
    format: text
    path: ./rules/cn.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/cn.list"
    interval: 86400

  telegramip:
    type: http
    behavior: ipcidr
    format: text
    path: ./rules/telegramip.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/telegramip.list"
    interval: 86400

  privateip:
    type: http
    behavior: ipcidr
    format: text
    path: ./rules/privateip.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/privateip.list"
    interval: 86400

  cnip:
    type: http
    behavior: ipcidr
    format: text
    path: ./rules/cnip.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/cnip.list"
    interval: 86400

rules:
  - RULE-SET,ads,🛑 广告拦截
  - RULE-SET,applications,🖥️ 直连软件
  - RULE-SET,private,🔒 私有网络
  - RULE-SET,microsoft-cn,🪟 微软服务
  - RULE-SET,apple-cn,🍎 苹果服务
  - RULE-SET,google-cn,🇬 谷歌服务
  - RULE-SET,games-cn,🎮 游戏服务
  - RULE-SET,ai,🤖 人工智能
  - RULE-SET,networktest,📈 网络测试
  - RULE-SET,proxy,🪜 代理域名
  - RULE-SET,cn,🇨🇳 直连域名
  - RULE-SET,telegramip,📲 电报消息,no-resolve
  - RULE-SET,privateip,🔒 私有网络,no-resolve
  - RULE-SET,cnip,🇨🇳 直连 IP
  - MATCH,🐟 漏网之鱼
```
# 二、 导入配置文件并启动 Clash
1. 进入 Clash Meta for Android -> 配置 -> 创建配置 -> 从 URL 导入，“URL”输入第《一》中生成的配置文件 .yaml 直链，“自动更新”填写“1440”，最后点击右上角的“保存图标”
2. 进入 Clash Meta for Android -> 设置 -> 网络，将“系统代理”关闭
3. 返回到主界面，点击“点此启用”即可启动 Clash 服务
