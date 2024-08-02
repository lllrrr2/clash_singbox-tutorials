# 分享自己使用 [ShellCrash](https://github.com/juewuy/ShellCrash) 搭配 geodata 方案的一套配置
# 声明：
1. 此方案适用于 [sing-box](https://github.com/SagerNet/sing-box)，采用 `geosite` 和 `geoip` 规则搭配 geosite.db 和 geoip.db [路由规则文件](https://github.com/DustinWin/ruleset_geodata?tab=readme-ov-file#%E4%B8%80-geodata-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)，**属高度定制，仅供参考**
2. 自定义规则参考 [DustinWin/ruleset_geodata/geodata](https://github.com/DustinWin/ruleset_geodata?tab=readme-ov-file#%E4%B8%80-geodata-%E8%A7%84%E5%88%99%E9%9B%86%E6%96%87%E4%BB%B6%E8%AF%B4%E6%98%8E)
3. 请根据自身情况进行修改，**适合自己的方案才是最好的方案**，如无特殊需求，可以照搬
4. 此方案适用于 ShellCrash（以 arm64 架构为例，且安装路径为 */data/ShellCrash*）
5. 在导入配置前，连接 SSH 后执行命令 `mkdir -p $CRASHDIR/providers/`
6. 此方案已摒弃 [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome)，但拦截广告效果依然强劲
---
# 一、 生成配置文件 .json 文件直链
具体方法此处不再赘述，请看《[生成带有自定义出站和规则的 sing-box 配置文件直链-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BA%E7%AB%99%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20sing-box%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-geodata%20%E6%96%B9%E6%A1%88.md)》，贴一下我使用的配置：
- 注：`route.rules` 部分的 `geosite` 和 `geoip` 内容须与 `route.geosite` 和 `route.geoip` 中的路由规则文件相匹配

```
{
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
      "path": "./providers/airport.yaml",
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
      { "geosite": [ "ads" ], "outbound": "🛑 广告拦截" },
      { "geosite": [ "private" ], "outbound": "🔒 私有网络" },
      { "geosite": [ "microsoft-cn" ], "outbound": "🪟 微软服务" },
      { "geosite": [ "apple-cn" ], "outbound": "🍎 苹果服务" },
      { "geosite": [ "google-cn" ], "outbound": "🇬 谷歌服务" },
      { "geosite": [ "games-cn" ], "outbound": "🎮 游戏服务" },
      { "geosite": [ "ai" ], "outbound": "🤖 人工智能" },
      { "geosite": [ "networktest" ], "outbound": "📈 网络测试" },
      { "geosite": [ "proxy" ], "outbound": "🪜 代理域名" },
      { "geosite": [ "cn" ], "outbound": "🇨🇳 直连域名" },
      { "geoip": [ "telegram" ], "outbound": "📲 电报消息", "skip_resolve": true },
      { "geoip": [ "private" ], "outbound": "🔒 私有网络", "skip_resolve": true },
      { "geoip": [ "cn" ], "outbound": "🇨🇳 直连 IP" }
    ],
    "geosite": {
      "path": "./geosite.db",
      "download_url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box/geosite.db"
    },
    "geoip": {
      "path": "./geoip.db",
      "download_url": "https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box/geoip-lite.db"
    },
    "final": "🐟 漏网之鱼",
    "auto_detect_interface": true,
    "concurrent_dial": true
  }
}
```
# 二、 导入 [sing-box PuerNya 版内核](https://github.com/PuerNya/sing-box/tree/building)
连接 SSH 后运行如下命令：
```
curl -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@main/sing-box-puernya/sing-box-linux-armv8.tar.gz | tar -zx -C /tmp/ && crash
```
此时脚本会自动“发现可用的内核文件”，选择 1 加载，后选择 5 Sing-Box-Puer 内核
# 三、 导入路由规则文件和 dns.json
连接 SSH 后运行如下命令：
```
curl -o $CRASHDIR/geosite.db -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@sing-box/geosite.db
curl -o $CRASHDIR/geoip.db -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@sing-box/geoip-lite.db
curl -o $CRASHDIR/jsons/dns.json -L https://cdn.jsdelivr.net/gh/DustinWin/ruleset_geodata@sing-box-config/geodata-mix-dns.json
```
# 四、 添加定时任务
- 注：须确保 sing-box 服务正常运行

1. 连接 SSH 后运行 `vi $CRASHDIR/task/task.user`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
201#curl -o /data/ShellCrash/CrashCore.tar.gz -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/clash_singbox-tools/main/sing-box-puernya/sing-box-linux-armv8.tar.gz && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新sing-box_PuerNya版内核
202#curl -o /data/ShellCrash/geosite.db -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box/geosite.db && curl -o /data/ShellCrash/geoip.db -L https://mirror.ghproxy.com/https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box/geoip-lite.db && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新geodata路由规则文件
```
2. 按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
3. 执行 `crash`，进入 ShellCrash -> 5 配置自动任务 -> 1 添加自动任务，可以看到末尾就有添加的定时任务，输入对应的数字并回车后可设置执行条件  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/98f77746-9257-4cf4-9bbc-4c47fcb35898" width="60%"/>

# 五、 设置部分
1. 设置可参考《[ShellCrash 配置-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-geodata%20%E6%96%B9%E6%A1%88.md)》，此处只列举配置的不同之处
2. 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 模式，选择 1 fake-ip 模式（不会与导入 mix 模式的 dns.json 冲突）
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/840b277f-ec9c-4b7f-8e50-6fab86346be3" width="60%"/>

3. 返回到 2 切换 DNS 运行模式，进入 4 DNS 进阶设置，设置如下：
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/d620860f-2319-4274-a7a8-13502e28a049" width="60%"/>

4. 进入主菜单 -> 7 内核进阶设置 -> 5 自定义端口及秘钥，设置如下：  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/fea803f3-8cad-4962-be77-83ed506a9347" width="60%"/>

5. 进入主菜单 -> 6 导入配置文件 -> 2 在线获取完整配置文件，粘贴第《一》步中生成的配置文件 .json 文件直链，启动服务即可
# 六、 在线 Dashboard 面板
推荐使用在线 Dashboard 面板 [metacubexd](https://github.com/metacubex/metacubexd)，访问地址：https://metacubex.github.io/metacubexd
1. 需要设置该网站“允许不安全内容”，以 Chrome 浏览器为例，进入设置 -> 隐私和安全 -> 网站设置 -> 更多内容设置 -> 不安全内容（或者直接打开 `chrome://settings/content/insecureContent` 进行设置），在“允许显示不安全内容”内添加 `https://metacubex.github.io`  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/e6d6d92f-130d-474e-867e-9e66c39706b2" width="60%"/>

2. 首次进入 https://metacubex.github.io/metacubexd 需要添加“后端地址”，输入 `http://192.168.31.1:9090` 并点击“添加”即可访问 Dashboard 面板  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/56eaeb14-f5e3-4245-b04b-680be7f01b3e" width="60%"/>
