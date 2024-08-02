# 分享自己使用 [ShellCrash](https://github.com/juewuy/ShellCrash)（fake-ip 模式）搭配 [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) 的一套配置
# 声明：
1. 此方案采用 ShellCrash 作为上游，AdGuardHome 作为下游的模式
2. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 GEOSITE 和 GEOIP 规则搭配 geosite.dat 和 geoip.dat（或 Country.mmdb）[路由规则文件](https://github.com/DustinWin/ruleset_geodata?tab=readme-ov-file#%E4%B8%80-geodata-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)，属高度定制，仅供参考
3. 规则参考 [DustinWin/ruleset_geodata/geodata](https://github.com/DustinWin/ruleset_geodata?tab=readme-ov-file#%E4%B8%80-geodata-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
4. 请根据自身情况进行修改，适合自己的方案才是最好的方案，如无特殊需求，可以照搬
5. 此方案中 ShellCrash 采用的 **DNS 模式为 `fake-ip`**（仍与 AdGuardHome 配合完美）
6. 此方案适用于 ShellCrash（以 arm64 架构为例，且安装路径为 */data/ShellCrash*）
7. 此方案适用于 AdGuardHome（以 arm64 架构为例，且安装路径为 */data/AdGuardHome*）
---
# 一、 生成配置文件 .yaml 文件直链
具体方法此处不再赘述，请看《[生成带有自定义策略组和规则的 Clash 配置文件直链-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-geodata%20%E6%96%B9%E6%A1%88.md)》，贴一下我使用的配置：
- 注：`rules` 部分的 `geosite` 和 `geoip` 内容须与 `geodata-url` 中的路由规则文件相匹配

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
  - {name: 🔒 私有网络, type: select, proxies: [🎯 全球直连]}
  - {name: 🎯 全球直连, type: select, proxies: [DIRECT]}

  - {name: 🇭🇰 香港节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇭🇰"}
  - {name: 🇹🇼 台湾节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇹🇼"}
  - {name: 🇯🇵 日本节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇯🇵"}
  - {name: 🇰🇷 韩国节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇰🇷"}
  - {name: 🇸🇬 新加坡节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇸🇬"}
  - {name: 🇺🇸 美国节点, type: url-test, tolerance: 50, include-all-providers: true, filter: "🇺🇸"}

rules:
  - GEOSITE,private,🔒 私有网络
  - GEOSITE,microsoft-cn,🪟 微软服务
  - GEOSITE,apple-cn,🍎 苹果服务
  - GEOSITE,google-cn,🇬 谷歌服务
  - GEOSITE,games-cn,🎮 游戏服务
  - GEOSITE,ai,🤖 人工智能
  - GEOSITE,networktest,📈 网络测试
  - GEOSITE,proxy,🪜 代理域名
  - GEOSITE,cn,🇨🇳 直连域名
  - GEOIP,telegram,📲 电报消息,no-resolve
  - GEOIP,private,🔒 私有网络,no-resolve
  - GEOIP,cn,🇨🇳 直连 IP
  - MATCH,🐟 漏网之鱼
```
# 二、 导入 [mihomo 内核](https://github.com/MetaCubeX/mihomo)  
连接 SSH 后执行如下命令：
```
curl -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@main/mihomo-meta/mihomo-linux-armv8.tar.gz | tar -zx -C /tmp/ && crash
```
此时脚本会自动“发现可用的内核文件”，选择 1 加载，后选择 3 Clash-Meta 内核
# 三、 导入路由规则文件和 user.yaml
- 注：路由规则文件和 user.yaml 都**属高度定制，牵一发而动全身**

连接 SSH 后执行如下命令：  
注：
- 1. 由于 ShellCrash 采用的 DNS 模式为 `fake-ip`，**ShellCrash 传给 AdGuardHome 的域名对应 IP 为假 IP**，会导致 AdGuardHome 检查更新和下载更新 DNS 黑名单时失败
- 2. 定制的 user.yaml 中的 `fake-ip-filter` 列表内新增了 AdGuardHome 常用域名，包括：`adguardteam.github.io`（AdGuardHome 自带 DNS 黑名单下载域名）、`adrules.top`（常用广告拦截下载域名）、`anti-ad.net`（常用广告拦截下载域名）和 `static.adtidy.org`（AdGuardHome 检查更新域名）
- 3. 不推荐使用自带更新去更新，推荐第《四》步通过定时任务去自动更新（AdGuardHome 程序已被压缩，节省空间）
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/fa87cd47-f74b-40f1-a105-cc660e2f44ee" width="60%"/>

```
curl -o $CRASHDIR/GeoSite.dat -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@clash/geosite-lite.dat
curl -o $CRASHDIR/GeoIP.dat -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@clash/geoip-lite.dat
curl -o $CRASHDIR/Country.mmdb -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@clash/Country-lite.mmdb
curl -o $CRASHDIR/yamls/user.yaml -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@clash-config/geodata-fakeip-user-lite-noprocess.yaml
```
# 四、 添加定时任务
- 注：须确保 Clash 服务正常运行

1. 连接 SSH 后执行 vi $CRASHDIR/task/task.user，按一下 Ins 键（Insert 键），粘贴如下内容：
```
201#curl -o /data/ShellCrash/CrashCore.tar.gz -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/clash_singbox-tools/main/mihomo-meta/mihomo-linux-armv8.tar.gz && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新mihomo内核
202#curl -o /data/ShellCrash/GeoSite.dat -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash/geosite-lite.dat && curl -o /data/ShellCrash/GeoIP.dat -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash/geoip-lite.dat && curl -o /data/ShellCrash/Country.mmdb -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash/Country-lite.mmdb && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新geodata路由规则文件
203#curl -o /data/ShellCrash/yamls/user.yaml -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/clash-config/geodata-fakeip-user-lite-noprocess.yaml && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新user.yaml
204#curl -o /data/AdGuardHome/AdGuardHome -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/clash_singbox-tools/main/AdGuardHome-prerelease/AdGuardHome_linux_armv8 && /data/AdGuardHome/AdGuardHome -s restart >/dev/null 2>&1#更新AdGuardHome
```
2. 按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
3. 执行 `crash`，进入 ShellCrash -> 5 配置自动任务 -> 1 添加自动任务，可以看到末尾就有添加的定时任务，输入对应的数字并回车后可设置执行条件
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/6cd0ea27-611b-439b-9e72-6a8f1675e818" width="60%"/>

# 五、 设置部分
## 1. ShellCrash 设置
① 设置可参考《[ShellCrash 配置-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-geodata%20%E6%96%B9%E6%A1%88.md)》，此处只列举配置的不同之处  
② 进入主菜单 -> 2 内核功能设置，设置如下：  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/2c830d59-6a66-4e13-9ca4-13204a131ac7" width="60%"/>  
③ 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 运行模式 -> 4 DNS 进阶设置，设置如下：  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/d0af4a21-d653-45b4-bc1f-bceae36459c2" width="60%"/>  
④ 进入主菜单 -> 7 内核进阶设置 -> 5 自定义端口及秘钥，设置如下：  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/512e61d1-23bb-4ff6-8a29-44506c470858" width="60%"/>  
⑤ 进入主菜单 -> 6 导入配置文件 -> 2 在线获取完整配置文件，粘贴第《一》步中生成的配置文件 .yaml 文件直链，启动服务即可
## 2. AdGuardHome 设置
设置可参考《[全网最详细的解锁 SSH ShellCrash 搭配 AdGuardHome 安装和配置教程-Clash 方案/AdGuardHome 配置](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%85%A8%E7%BD%91%E6%9C%80%E8%AF%A6%E7%BB%86%E7%9A%84%E8%A7%A3%E9%94%81%20SSH%20ShellCrash%20%E6%90%AD%E9%85%8D%20AdGuardHome%20%E5%AE%89%E8%A3%85%E5%92%8C%E9%85%8D%E7%BD%AE%E6%95%99%E7%A8%8B-Clash%20%E6%96%B9%E6%A1%88.md#2-adguardhome-%E9%85%8D%E7%BD%AE)》（可跳过“添加 DNS 重写”的步骤）
# 六、 在线 Dashboard 面板
推荐使用在线 Dashboard 面板 [metacubexd](https://github.com/metacubex/metacubexd)，访问地址：https://metacubex.github.io/metacubexd
1. 需要设置该网站“允许不安全内容”，以 Chrome 浏览器为例，进入设置 -> 隐私和安全 -> 网站设置 -> 更多内容设置 -> 不安全内容（或者直接打开 `chrome://settings/content/insecureContent` 进行设置），在“允许显示不安全内容”内添加 `https://metacubex.github.io`  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/0611d9e9-7895-4b45-866c-50ed42b65c85" width="60%"/>

2. 首次进入 https://metacubex.github.io/metacubexd 需要添加“后端地址”，输入 `http://192.168.31.1:9090` 并点击“添加”即可访问 Dashboard 面板  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/5551bf56-4c3e-4b31-aed9-214074bf92e1" width="60%"/>
