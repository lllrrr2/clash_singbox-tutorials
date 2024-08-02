# 生成带有自定义策略组和规则的 Clash 配置文件直链-ruleset 方案
- 注：此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `RULE-SET` 规则搭配 `rule-providers` 配置项
# 前言：
1. 本教程可以生成扩展名为 .yaml 配置文件直链，可以**一键导入使用了 [mihomo](https://github.com/MetaCubeX/mihomo) 内核的 Clash 客户端**  
如：[ShellCrash](https://github.com/juewuy/ShellCrash)、[OpenClash](https://github.com/vernesong/OpenClash)、[Clash Verge](https://github.com/MetaCubeX/clash-verge) 和 [Clash.Meta for Android](https://github.com/MetaCubeX/ClashMetaForAndroid) 等，详见[支持 mihomo 的工具](https://wiki.metacubex.one/startup/client)
2. 生成的订阅链接地址不会改变，支持更新订阅，**支持国内访问，支持同步机场节点**
3. 生成的订阅链接**自带规则集**，规则集来源 [DustinWin/ruleset_geodata/clash](https://github.com/DustinWin/ruleset_geodata#%E4%BA%8C-ruleset-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
4. 请先**确定自己机场的订阅链接是否为 Clash 订阅链接**，若不是，需前往[肥羊在线订阅转换工具](https://suburl.v1.mk)进行转换，“生成类型”选择“Clash”，其它参数保持默认即可，转换后的订阅链接需要在末尾添加`&flag=clash`，然后添加到 .yaml 文件代理集合 `proxy-providers` 的 `url` 中
5. 推荐使用 [Visual Studio Code](https://code.visualstudio.com/Download) 等专业编辑器来修改配置文件
6. ShellCrash 支持本地导入配置文件，可以直接将下方的 .yaml 直链文件内容复制到 *$CRASHDIR/yamls/config.yaml* 文件中，可代替通过 ShellCrash 配置脚本 -> 6 -> 2 导入配置文件的方式
---
# 一、 准备编辑 .yaml 直链文件
## 1. 注册 [Gist](https://gist.github.com)
进入 https://gist.github.com 网站并注册
## 2. 打开编辑页面
登录并打开 [Gist](https://gist.github.com) 可以直接编辑文件，或者点击页面右上角头像左边的“+”图标新建文件
## 3. 输入描述和完整文件名
“Gist description...”输入描述，随意填写；“Filename including extension...”输入完整文件名**包括扩展名**，如 clashlink.yaml  
<img src="https://user-images.githubusercontent.com/45238096/219593234-64833fcd-5200-4bea-849f-a1865d341fd2.png" width="60%"/>

# 二、 添加模板
## 1. 白名单模式（没有命中规则的网络流量统统使用代理，适用于服务器线路网络质量稳定、快速，不缺服务器流量的用户）  
```
# 代理集合（获取机场订阅链接内的所有节点）
proxy-providers:
  🛫 我的机场 1:
    type: http
    # 机场订阅链接，使用 Clash 链接
    url: "https://example.com/xxx/xxx&flag=clash"
    path: ./proxies/airport1.yaml
    interval: 86400
    # 初步筛选需要的节点，可有效减轻路由器压力，支持正则表达式，不筛选可删除此配置项
    filter: "(?i)港|hk|hongkong|hong kong|台|tw|taiwan|日本|jp|japan|新|sg|singapore|美|us|unitedstates|united states"
    # 初步排除不需要的节点，支持正则表达式，若不排除可删除此配置项
    exclude-filter: "高倍|×10"
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600

  🛫 我的机场 2:
    type: http
    url: "https://example.com/xxx/xxx&flag=clash"
    path: ./proxies/airport2.yaml
    interval: 86400
    filter: "(?i)港|hk|hongkong|hong kong|台|tw|taiwan|日本|jp|japan|新|sg|singapore|美|us|unitedstates|united states"
    exclude-filter: "高倍|×10"
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600

# 单个出站代理节点（以 vless 为例）
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

# 策略组
proxy-groups:
  # 手动选择国家或地区节点；根据“国家或地区策略组”名称对 `proxies` 值进行增删改，须一一对应
  - {name: 🚀 节点选择, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  # 若机场的 UDP 质量不是很好，导致某游戏无法登录或进入房间，可以添加 `disable-udp: true` 配置项解决
  - {name: 🐟 漏网之鱼, type: select, proxies: [🚀 节点选择, 🎯 全球直连]}
  # 选择`🎯 全球直连`为测试本地网络（运营商网络速度和 IPv6 支持情况），可选择其它节点用于测试机场节点速度和 IPv6 支持情况
  - {name: 📈 网络测试, type: select, proxies: [🎯 全球直连, 🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  - {name: 🤖 人工智能, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇰🇷 韩国节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点]}
  - {name: 🎮 游戏服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🪟 微软服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇬 谷歌服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🍎 苹果服务, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇨🇳 直连域名, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🇨🇳 直连 IP, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  - {name: 🪜 代理域名, type: select, proxies: [🚀 节点选择, 🎯 全球直连]}
  - {name: 📲 电报消息, type: select, proxies: [🚀 节点选择]}
  # 若使用 ShellCrash，由于无法判断本机进程（默认 `find-process-mode: off`），需删除此条 `🖥️ 直连软件`
  - {name: 🖥️ 直连软件, type: select, proxies: [🎯 全球直连]}
  - {name: 🔒 私有网络, type: select, proxies: [🎯 全球直连]}
  - {name: 🛑 广告拦截, type: select, proxies: [REJECT]}
  - {name: 🎯 全球直连, type: select, proxies: [DIRECT]}

  # ----------------国家或地区策略组---------------------
  # 自动选择节点，即按照 url 测试结果使用延迟最低的节点；测试后容差大于 50ms 才会切换到延迟低的那个节点；筛选出“香港”节点，支持正则表达式
  - {name: 🇭🇰 香港节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)港|hk|hongkong|hong kong"}
  # 节点负载均衡，即将请求均匀分配到多个节点上，优点是更稳定，速度可能有提升；将相同顶级域名的请求分配给策略组内的同一个代理节点；推荐在节点复用比较多的情况下使用
  - {name: 🇹🇼 台湾节点, type: load-balance, strategy: consistent-hashing, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)台|tw|taiwan"}
  # 可使用 `include-all-providers: true` 代替 `use: [🛫 我的机场 1, 🛫 我的机场 2, ...]`，意思为引入所有代理集合
  - {name: 🇯🇵 日本节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "(?i)日本|jp|japan"}
  - {name: 🇸🇬 新加坡节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)新|sg|singapore"}
  - {name: 🇺🇸 美国节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)美|us|unitedstates|united states"}

# 规则集（yaml 文件每天自动更新）
rule-providers:
  ads:
    type: http
    behavior: domain
    format: text
    path: ./rules/ads.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/ads.list"
    interval: 86400

  # 若使用 ShellCrash，由于无法判断本机进程（默认 `find-process-mode: off`），需删除此条 `applications`
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

# 规则
rules:
  - RULE-SET,ads,🛑 广告拦截
  # 若使用 ShellCrash，由于无法判断本机进程（默认 `find-process-mode: off`），需删除此条 `RULE-SET`
  - RULE-SET,applications,🖥️ 直连软件
  # 为过滤 P2P 流量（BT 下载），可添加一条 `DST-PORT` 规则（ShellCrash 会默认开启“只代理常用端口”，可删除此条 `DST-PORT`）
  - DST-PORT,6881-6889,🎯 全球直连
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
将模板内容复制到自己 Gist 新建的 .yaml 文件中  
**贴一张面板效果图（举个例子：我手动选择 `🇹🇼 台湾节点` 策略组，而该策略组是将机场内所有台湾节点按照 url 测试结果自动选择延迟最低的台湾节点）：**  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/9bcbccf2-577b-43b4-b53c-e3fd911364dd" width="60%"/>

## 2. 黑名单模式（只有命中规则的网络流量才使用代理，适用于服务器线路网络质量不稳定或不够快，或服务器流量紧缺的用户。通常也是软路由用户、家庭网关用户的常用模式）
```
# 代理集合（获取机场订阅链接内的所有节点）
proxy-providers:
  🛫 我的机场 1:
    type: http
    # 机场订阅链接，使用 Clash 链接
    url: "https://example.com/xxx/xxx&flag=clash"
    path: ./proxies/airport1.yaml
    interval: 86400
    # 初步筛选需要的节点，可有效减轻路由器压力，支持正则表达式，不筛选可删除此配置项
    filter: "(?i)港|hk|hongkong|hong kong|台|tw|taiwan|日本|jp|japan|新|sg|singapore|美|us|unitedstates|united states"
    # 初步排除不需要的节点，支持正则表达式，若不排除可删除此配置项
    exclude-filter: "高倍|×10"
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600

  🛫 我的机场 2:
    type: http
    url: "https://example.com/xxx/xxx&flag=clash"
    path: ./proxies/airport2.yaml
    interval: 43200
    filter: "(?i)港|hk|hongkong|hong kong|台|tw|taiwan|日本|jp|japan|新|sg|singapore|美|us|unitedstates|united states"
    exclude-filter: "高倍|×10"
    health-check:
      enable: true
      url: https://www.gstatic.com/generate_204
      interval: 600

# 单个出站代理节点（以 vless 为例）
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

# 策略组
proxy-groups:
  # 手动选择国家或地区节点；根据“国家或地区策略组”名称对 `proxies` 值进行增删改，须一一对应
  - {name: 🚀 节点选择, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  - {name: 🐟 漏网之鱼, type: select, proxies: [🎯 全球直连, 🚀 节点选择]}
  # 选择`🎯 全球直连`为测试本地网络（运营商网络速度和 IPv6 支持情况），可选择其它节点用于测试机场节点速度和 IPv6 支持情况
  - {name: 📈 网络测试, type: select, proxies: [🎯 全球直连, 🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点, 🆓 免费节点]}
  - {name: 🤖 人工智能, type: select, proxies: [🇭🇰 香港节点, 🇹🇼 台湾节点, 🇯🇵 日本节点, 🇰🇷 韩国节点, 🇸🇬 新加坡节点, 🇺🇸 美国节点]}
  - {name: 🪜 代理域名, type: select, proxies: [🚀 节点选择, 🎯 全球直连]}
  - {name: 📲 电报消息, type: select, proxies: [🚀 节点选择]}
  - {name: 🛑 广告拦截, type: select, proxies: [REJECT]}
  - {name: 🎯 全球直连, type: select, proxies: [DIRECT]}

  # ----------------国家或地区策略组---------------------
  # 自动选择节点，即按照 url 测试结果使用延迟最低的节点；测试后容差大于 50ms 才会切换到延迟低的那个节点；筛选出“香港”节点，支持正则表达式
  - {name: 🇭🇰 香港节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)港|hk|hongkong|hong kong"}
  # 节点负载均衡，即将请求均匀分配到多个节点上，优点是更稳定，速度可能有提升；将相同顶级域名的请求分配给策略组内的同一个代理节点；推荐在节点复用比较多的情况下使用
  - {name: 🇹🇼 台湾节点, type: load-balance, strategy: consistent-hashing, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)台|tw|taiwan"}
  # 可使用 `include-all-providers: true` 代替 `use: [🛫 我的机场 1, 🛫 我的机场 2, ...]`，意思为引入所有代理集合
  - {name: 🇯🇵 日本节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "(?i)日本|jp|japan"}
  - {name: 🇸🇬 新加坡节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)新|sg|singapore"}
  - {name: 🇺🇸 美国节点, type: url-test, tolerance: 50, use: [🛫 我的机场 1, 🛫 我的机场 2], filter: "(?i)美|us|unitedstates|united states"}

# 规则集（yaml 文件每天自动更新）
rule-providers:
  ads:
    type: http
    behavior: domain
    format: text
    path: ./rules/ads.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/ads.list"
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

  telegramip:
    type: http
    behavior: ipcidr
    format: text
    path: ./rules/telegramip.list
    url: "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-ruleset/telegramip.list"
    interval: 86400

# 规则
rules:
  - RULE-SET,ads,🛑 广告拦截
  - RULE-SET,ai,🤖 人工智能
  - RULE-SET,networktest,📈 网络测试
  - RULE-SET,proxy,🪜 代理域名
  - RULE-SET,telegramip,📲 电报消息,no-resolve
  - MATCH,🐟 漏网之鱼
```
将模板内容复制到自己 Gist 新建的 .yaml 文件中
# 三、 修改模板
1. 将代理集合 `proxy-providers` 中的 `url` 链接改成自己机场的订阅链接（必须为 Clash 订阅链接，详见《前言：4》）  
2. 确定自己机场中有哪些国家或地区的节点，然后对模板文件中“**国家或地区策略组**”和 `🚀 节点选择` 策略组下的 `proxies` 里面的国家或地区进行增删改
- 注：两者中的国家或地区必须一一对应，新增就全部新增，删除就全部删除，修改就全部修改（重要）

3. 在“国家或地区策略组”中的 `filter` 支持[正则表达式](https://tool.oschina.net/regex)，可以精确地筛选出指定的国家或地区节点  
例如：我想筛选出“香港 IPLC”节点，`filter` 可以这样写：
`filter: "香港.*IPLC|IPLC.*香港"`  
- 小窍门：使用 [ChatGPT](https://chatgpt.com) 等 AI 工具查询符合自己要求的正则表达式

4. 在 `🚀 节点选择` 策略组下的 `proxies` 里，可以将最稳定的节点放在最前面，配置完成后会自动选择最稳定的节点  
5. 在“国家或地区策略组”里，`type` 为 `url-test` 就是自动选择延迟最低的节点，将 `url-test` 改成 `select` 就是手动选择节点  
举个例子：我的机场有 2 个节点，分别是香港节点和日本节点，我想让[哔哩哔哩](https://www.bilibili.com)（B 站）自动选择延迟最低的香港节点，[AcFun](https://www.acfun.cn)（A 站）手动选择日本节点，这个需求怎么写？  
① 进入 [blackmatrix7/ios_rule_script/rule/Clash](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Clash) 后按 Ctrl+F 组合键搜索“bilibili”和“acfun”，显然可以**精确搜索到结果**  
② 进入指定目录，优先使用“xxx_Classical.yaml”文件，然后点击“Raw”获取下载地址，那么就可以这样编写：
- 注：以下只是节选，请酌情套用

```
# 策略组
proxy-groups:
  # 默认选择香港节点
  - {name: 📺 哔哩哔哩, type: select, proxies: [🇭🇰 香港节点, 🎯 全球直连]}
  # 默认选择日本节点
  - {name: 📽️ AcFun, type: select, proxies: [🇯🇵 日本节点, 🎯 全球直连]}
  # 自动选择延迟最低的香港节点；容差大于 50ms 才会切换到延迟低的那个节点
  - {name: 🇭🇰 香港节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "(?i)港|hk|hongkong|hong kong"}
  # 手动选择日本节点
  - {name: 🇯🇵 日本节点, type: select, include-all-providers: true, filter: "(?i)日本|jp|japan"}
  - {name: 🎯 全球直连, type: select, proxies: [DIRECT]}

# 规则集（yaml 文件每天自动更新）
rule-providers:
  bilibili:
    type: http
    behavior: classical
    format: yaml
    path: ./rules/bilibili.yaml
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/BiliBili/BiliBili.yaml"
    interval: 86400

  acfun:
    type: http
    behavior: classical
    format: yaml
    path: ./rules/acfun.yaml
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/AcFun/AcFun.yaml"
    interval: 86400

# 规则
rules:
  # 自定义规则优先放前面
  - RULE-SET,bilibili,📺 哔哩哔哩
  - RULE-SET,acfun,📽️ AcFun
```
# 四、 生成 .yaml 文件链接
## 1. 生成链接
编辑完成后，点击右下角的“Create secret gist”按钮，然后点击右上角的“Raw”按钮  
<img src="https://user-images.githubusercontent.com/45238096/233839417-51d1ab65-3e6b-4ae8-bf01-9d342029b423.png" width="60%"/>
## 2. 生成 .yaml 文件链接
编辑完成后，点击右下角的“Create secret gist”按钮，然后点击右上角的“Raw”按钮  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/ed151cc8-1bae-4b9f-a598-2a2687e8acc5" width="60%"/>  
取出地址栏中的网址，删除后面的一串随机码，**完成后该 .yaml 文件直链才是最终生成的订阅链接**，该订阅链接地址不会改变，在不更改文件名的情况下即使编辑该 .yaml 文件并提交了 n 次也不会改变  
举例，这是原地址：  
`https://gist.githubusercontent.com/DustinWin/f5995e5002fb729380c02dbc38669149/raw/064134b248dddb3a77d85acf45d084751c57ed11/clashlink.yaml`  
删除后面的一串随机码（当前编辑该文件生成的随机码“064134b248dddb3a77d85acf45d084751c57ed11”）  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/12b17493-119d-40b7-8bf6-4d05ceaf7a8c" width="60%"/>  
删除后变成：  
`https://gist.githubusercontent.com/DustinWin/f5995e5002fb729380c02dbc38669149/raw/clashlink.yaml`
# 五、 导入订阅链接
## 1. 在 ShellCrash 中导入订阅链接
进入 ShellCrash 配置脚本 -> 6 -> 2，粘贴最终生成的订阅链接即可，具体设置请参考《[ShellCrash 配置-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md)》
## 2. 在 Clash Verge（Windows 端）中导入订阅链接
进入 Clash Verge -> 订阅，在“订阅文件链接”处粘贴最终生成的订阅链接，直接“导入”即可，具体设置请参考《[Clash Verge 配置-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/Clash%20Verge%20%E9%85%8D%E7%BD%AE-ruleset%20%E6%96%B9%E6%A1%88.md)》
# 六、 私人定制
到了这里，相信你对里面的机制已经有了一定的认识，那么我们可以对自己的需求进行定制了  
最常见的有：我购买的机场支持[奈飞](https://www.netflix.com)和[亚马逊](https://www.primevideo.com)，但仅新加坡这一个节点支持亚马逊，日本和韩国节点支持奈飞，这个规则怎么写？
1. 进入 [blackmatrix7/ios_rule_script/rule/Clash](https://github.com/blackmatrix7/ios_rule_script/tree/master/rule/Clash) 后按 Ctrl+F 组合键分别搜索“netflix”和“primevideo”
2. 可以**精确搜索**到“netflix”和“primevideo”
3. 进入指定目录，优先使用“xxx_Classical.yaml”文件，然后点击“Raw”获取下载地址，那么就可以这样编写：
- 注：以下只是节选，请酌情套用

```
# 策略组
proxy-groups:
  # 打开奈飞后手动选择日本或韩国节点
  - {name: 🎥 奈飞视频, type: select, include-all-providers: true, filter: "(?i)日本|jp|japan|韩|kr|korea"}
  # 打开亚马逊后自动选择延迟最低的新加坡节点；容差大于 50ms 才会切换到延迟低的那个节点；未选择到当前策略组时不会进行延迟测试
  - {name: 🎬 Prime Video, type: url-test, tolerance: 50, include-all-providers: true, filter: "(?i)新|sg|singapore"}

# 规则集（yaml 文件每天自动更新）
rule-providers:
  netflix:
    type: http
    behavior: classical
    format: yaml
    path: ./rules/netflix.yaml
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/Netflix/Netflix_Classical.yaml"
    interval: 86400

  primevideo:
    type: http
    behavior: classical
    format: yaml
    url: "https://raw.githubusercontent.com/blackmatrix7/ios_rule_script/master/rule/Clash/PrimeVideo/PrimeVideo.yaml"
    interval: 86400

# 规则
rules:
  # 自定义规则优先放前面
  - RULE-SET,netflix,🎥 奈飞视频,no-resolve
  - RULE-SET,primevideo,🎬 Prime Video
```
