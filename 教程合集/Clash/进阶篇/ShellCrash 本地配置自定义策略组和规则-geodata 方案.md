# [ShellCrash](https://github.com/juewuy/ShellCrash) 本地配置自定义策略组和规则-geodata 方案
- 注：此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `GEOSITE` 和 `GEOIP` 规则搭配 geosite.dat 和 geoip.dat（或 Country.mmdb）[路由规则文件](https://github.com/MetaCubeX/meta-rules-dat)
# 前言：
1. 本教程只适用于 ShellCrash
2. 自定义规则参考 [MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat)
3. 本教程**仅适合白名单模式**（没有命中规则的网络流量统统使用代理，适用于服务器线路网络质量稳定、快速，不缺服务器流量的用户）
4. 本教程最终效果媲美《[生成带有自定义策略组和规则的 Clash 配置文件直链-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-geodata%20%E6%96%B9%E6%A1%88.md)》（策略组更直观，操作更方便），但不依赖于网络
5. 所有步骤完成后，请连接 SSH 执行命令 `$CRASHDIR/start.sh restart` 后生效
6. 推荐使用 [Visual Studio Code](https://code.visualstudio.com/Download) 等专业编辑器来修改配置文件
---
# 一、 导入 [mihomo 内核](https://github.com/MetaCubeX/mihomo)和路由规则文件
可参考《[ShellCrash 配置-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/ShellCrash%20%E9%85%8D%E7%BD%AE-geodata%20%E6%96%B9%E6%A1%88.md)》里的步骤《一、二》进行操作
# 二、 导入配置文件
1. 进入 ShellCrash -> 6 导入配置文件 -> 1 在线生成 meta 配置文件 -> 4 选取在线配置规则模版，选择 4 [ACL4SSR](https://acl4ssr-sub.github.io) 极简版（适合自建节点）  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/88b58a87-76b8-4004-b005-133d6a2bb71f" width="60%"/>  

2. 进入 ShellCrash -> 6 导入配置文件 -> 1 在线生成 meta 配置文件，输入订阅链接后回车，再输入“1”并回车即可
# 三、 自定义策略组和规则
## 1. 自定义 others.yaml（用于编写自定义的锚点、入站、`proxy-providers`、`sub-rules`、`rule-set`、script 等功能）
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/others.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
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
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
## 2. 自定义 proxies.yaml（用于添加自定义出站代理）
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/proxies.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：  
注：
- 1. 此处以“vless”节点类型为例，其它节点类型写法可参考[通用字段](https://wiki.metacubex.one/config/proxies)
- 2. 必须在 proxy-groups.yaml 里添加自定义的节点才可以正常选择和使用

```
- name: 🆓 免费节点
  # 节点类型
  type: vless
  # 代理节点服务器（域名/IP）
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
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
## 3. 自定义 proxy-groups.yaml（用于添加自定义策略组）
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/proxy-groups.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
# 策略组

# 手动选择国家或地区节点；根据“国家或地区策略组”名称对 `proxies` 值进行增删改，须一一对应
- name: 🈯 节点指定
  type: select
  proxies:
    - 🇭🇰 香港节点
    - 🇹🇼 台湾节点
    - 🇯🇵 日本节点
    - 🇸🇬 新加坡节点
    - 🇺🇸 美国节点
    # 添加 proxies.yaml 中的自定义节点
    - 🆓 免费节点

# Speedtest 测速网站：选择 `🎯 全球直连` 为测试本地网络速度（运营商网络速度），可选择其它节点用于测试机场节点速度
- name: 📈 网络测速
  type: select
  proxies:
    - 🎯 全球直连
    - 🇭🇰 香港节点
    - 🇹🇼 台湾节点
    - 🇯🇵 日本节点
    - 🇰🇷 韩国节点
    - 🇸🇬 新加坡节点
    - 🇺🇸 美国节点
    # 添加 proxies.yaml 中的自定义节点
    - 🆓 免费节点

- name: 🎮 游戏服务
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🪟 微软服务
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🇬 谷歌服务
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🍎 苹果服务
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🇨🇳 直连域名
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🇨🇳 直连 IP
  type: select
  proxies:
    - 🎯 全球直连
    - 🈯 节点指定

- name: 🪜 代理域名
  type: select
  proxies:
    - 🈯 节点指定
    - 🎯 全球直连

- name: 📲 电报消息
  type: select
  proxies:
    - 🈯 节点指定

- name: 🔒 私有网络
  type: select
  proxies:
    - 🎯 全球直连

- name: 🛑 广告拦截
  type: select
  proxies:
    - REJECT

# ----------------国家或地区策略组---------------------

# 自动选择节点，即按照 url 测试结果使用延迟最低的节点
- name: 🇭🇰 香港节点
  type: url-test
  # 测试后容差大于 50ms 才会切换到延迟低的那个节点
  tolerance: 50
  include-all-providers: true
  # 筛选出“香港”节点，支持正则表达式
  filter: "(?i)港|hk|hongkong|hong kong"

- name: 🇹🇼 台湾节点
  type: url-test
  tolerance: 50
  include-all-providers: true
  filter: "(?i)台|tw|taiwan"

- name: 🇯🇵 日本节点
  type: url-test
  tolerance: 50
  include-all-providers: true
  filter: "(?i)日本|jp|japan"

- name: 🇸🇬 新加坡节点
  type: url-test
  tolerance: 50
  include-all-providers: true
  filter: "(?i)新|sg|singapore"

- name: 🇺🇸 美国节点
  type: url-test
  tolerance: 50
  include-all-providers: true
  filter: "(?i)美|us|unitedstates|united states"
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
## 4. 自定义 rules.yaml（用于添加自定义规则）
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/rules.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
# 规则

# 自定义规则优先放前面
- GEOSITE,category-ads-all,🛑 广告拦截
- GEOSITE,private,🔒 私有网络
- GEOSITE,microsoft@cn,🪟 微软服务
- GEOSITE,apple-cn,🍎 苹果服务
- GEOSITE,google-cn,🇬 谷歌服务
- GEOSITE,category-games@cn,🎮 游戏服务
- GEOSITE,speedtest,📈 网络测速
- GEOSITE,geolocation-!cn,🪜 代理域名
- GEOSITE,cn,🇨🇳 直连域名
- GEOIP,telegram,📲 电报消息,no-resolve
- GEOIP,private,🔒 私有网络,no-resolve
- GEOIP,cn,🇨🇳 直连 IP
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车  
**贴一张面板效果图（举个例子：我手动选择 `🇹🇼 台湾节点` 策略组，而该策略组是将机场内所有台湾节点按照 url 测试结果自动选择延迟最低的台湾节点）：**  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/13d52f81-9e84-460a-99c6-a2d1ea05750c" width="60%"/>

# 四、 修改策略组或规则
**举例：我想添加一个规则，使奈飞走日本和新加坡节点**  
① 进入 [MetaCubeX/meta-rules-dat/meta/geo](https://github.com/MetaCubeX/meta-rules-dat/tree/meta/geo) 后在左侧“Go to file”搜索框内搜索“netflix”  
② 输入“netflix”可以搜索到“geo/geosite/netflix”和“geo/geoip/netflix”，那么就可以进行如下操作：  
注：
- 1. **一定要保证缩进对齐！一定要保证缩进对齐！一定要保证缩进对齐！**
- 2. 以下只是节选，请酌情套用

## 1. 修改 proxy-groups.yaml 文件
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/proxy-groups.yaml`，按一下 Ins 键（Insert 键），粘贴如下内容：
```
# 策略组

# 打开奈飞后自动选择延迟最低的日本或新加坡节点；容差大于 50ms 才会切换到延迟低的那个节点；未选择到当前策略组时不会进行延迟测试
- name: 🎥 奈飞视频
  type: url-test
  tolerance: 50
  include-all-providers: true
  filter: "(?i)日本|jp|japan|新|sg|singapore"
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
## 2. 修改 rules.yaml 文件
连接 SSH 后执行命令 `vi $CRASHDIR/yamls/rules.yaml`，按一下 Ins 键（Insert 键），**优先在最上方**粘贴如下内容：
```
# 规则

# 自定义规则优先放前面
- GEOSITE,netflix,🎥 奈飞视频
- GEOIP,netflix,🎥 奈飞视频,no-resolve
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
# 五、 添加小规则
仅添加特定网址走直连或走代理，连接 SSH 后执行命令 `vi $CRASHDIR/yamls/rules.yaml`，按一下 Ins 键（Insert 键），在**最上方**粘贴如下内容：  
注：
- 1. 以下内容只是举例，请根据自身需要进行增删改
- 2. 其它规则请参考《[mihomo Wiki](https://wiki.metacubex.one/config/rules)》

```
# 规则

# 以 googleapis.cn 为后缀（包括 googleapis.cn）的所有域名走代理
- DOMAIN-SUFFIX,googleapis.cn,🚀 节点选择
# 与哔哩哔哩相关的所有域名走直连
- GEOSITE,bilibili,DIRECT
# 含有 ipv6 关键字的所有域名走直连
- DOMAIN-KEYWORD,ipv6,DIRECT
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
