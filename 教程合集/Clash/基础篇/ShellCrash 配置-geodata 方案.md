# [ShellCrash](https://github.com/juewuy/ShellCrash) 配置-geodata 方案
注：
- 1. 此方案适用于 [Clash](https://github.com/Dreamacro/clash)，采用 `GEOSITE` 和 `GEOIP` 规则搭配 geosite.dat 和 geoip.dat（或 Country.mmdb） [路由规则文件](https://github.com/MetaCubeX/meta-rules-dat)
- 2. 本教程中的下载链接以 CPU 架构 ARMv8 为例，若为别的 CPU 架构，请注意修改链接后缀
- 3. 查看 CPU 架构可连接 SSH 后执行命令 `uname -ms`，若执行结果是“linux aarch64”，就是搭载的 ARMv8 架构
---
# 一、 导入 [mihomo 内核](https://github.com/MetaCubeX/mihomo)
**mihomo 内核下载链接后缀和 CPU 架构对应关系如下表：**
|CPU 架构|AMD64|ARMv5|ARMv6|ARMv7|ARMv8&ARM64&AArch64|mips-softfloat|mipsle-hardfloat|mipsle-softfloat|
|-----|-----|-----|-----|-----|:---:|-----|-----|-----|
|**链接后缀**|`amd64`|`armv5`|`armv6`|`armv7`|`armv8`|`mips-softfloat`|`mipsle-hardfloat`|`mipsle-softfloat`|

连接 SSH 后执行如下命令：
```
curl -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@main/mihomo-meta/mihomo-linux-armv8.tar.gz | tar -zx -C /tmp/
```
# 二、 导入路由规则文件
连接 SSH 后执行如下命令：
```
curl -o $CRASHDIR/GeoSite.dat -L https://cdn.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geosite.dat
curl -o $CRASHDIR/Country.mmdb -L https://cdn.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/country.mmdb
```
# 三、 添加定时任务
连接 SSH 后执行 `vi $CRASHDIR/task/task.user`，按一下 Ins 键（Insert 键），粘贴（快捷键 Ctrl+Shift+V）如下内容：
- 注：ShellCrash 安装路径为 */data/ShellCrash*

```
201#curl -o /data/ShellCrash/CrashCore.tar.gz -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@main/mihomo-meta/mihomo-linux-armv8.tar.gz && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新mihomo内核
202#curl -o /data/ShellCrash/GeoSite.dat -L https://cdn.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geosite.dat && curl -o /data/ShellCrash/Country.mmdb -L https://cdn.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/country.mmdb && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新geodata路由规则文件
203#curl -L https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@main/Clash-dashboard/metacubexd.tar.gz | tar -zx -C $CRASHDIR/ui/ && /data/ShellCrash/start.sh restart >/dev/null 2>&1#更新metacubexd面板
```
按一下 Esc 键（退出键），输入英文冒号 `:`，继续输入 `wq` 并回车
# 四、 设置部分
1. 连接 SSH 后执行 `crash` 命令打开 ShellCrash 配置脚本  
首次打开会进入新手引导，路由设备配置局域网透明代理（此处选择 1）  
启用推荐的自动任务配置  
根据需要是否启用软固化（此处选择 1）  
根据需要是否选择 1 确认导入配置文件（此处选择 0）  
根据需要是否选择 1 立即启动 clash 服务（此处选择 0）  
输入 0 回车可返回到上级菜单（下同）  
2. 此时脚本会自动“发现可用的内核文件”，选择 1 加载，后选择 3 Clash-Meta 内核
3. 内核加载完成后根据需要是否保留相关数据库文件（此处选择 0）
4. 进入主菜单 -> 9 更新/卸载 -> 7 切换安装源及安装版本，选择 b 切换至公测版 -> 1 Jsdelivr_CDN源（推荐）
5. 进入主菜单 -> 9 更新/卸载 -> 4 安装本地 Dashboard 面板，选择 3 安装 MetaXD 面板（也可跳过此步，使用第《五》步中的在线 Dashboard 面板）
6. 进入主菜单 -> 2 内核功能设置，设置如下：
- 注：有“Tproxy 模式”就选“Tproxy 模式”，否则推荐选“混合模式”，宽带在 300M 内推荐选 Tun 模式  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/4dc6dea5-3715-4c2a-ba83-3537c6797625" width="60%"/>

7. 进入主菜单 -> 2 内核功能设置 -> 2 切换 DNS 运行模式 -> 4 DNS 进阶设置，选择 4 一键配置加密（推荐）
8. 进入主菜单 -> 4 内核启动设置，选择 1 允许 ShellCrash 开机启动（若重启路由器后服务没有自动运行，可“设置自启延时”为 30 秒）
9. 进入主菜单 -> 5 配置自动任务 -> 1 添加自动任务，可以看到末尾就有第《三》步添加的定时任务，输入对应的数字并回车后可设置执行条件
10. 进入主菜单 -> 6 导入配置文件 -> 2 在线获取完整配置文件，粘贴《[生成带有自定义策略组和规则的 Clash 配置文件直链-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/Clash/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E7%AD%96%E7%95%A5%E7%BB%84%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20Clash%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-geodata%20%E6%96%B9%E6%A1%88.md)》中生成的 .yaml 配置文件直链，启动服务即可
11. 访问 Dashboard 面板 [http://192.168.31.1:9999/ui](http://192.168.31.1:9999/ui)，首次打开需要添加“后端地址”，输入 `http://192.168.31.1:9999` 并点击“添加”即可  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/91ebae62-df79-4d1d-9998-d47adb69cf43" width="60%"/>

12. 进入 Dashboard 面板 -> 代理 -> 代理提供者，点击“转圈图标”（Update），也可手动更新节点
# 五、 在线 Dashboard 面板
推荐使用在线 Dashboard 面板 [metacubexd](https://github.com/metacubex/metacubexd)，访问地址：https://metacubex.github.io/metacubexd
1. 需要设置该网站“允许不安全内容”，以 Chrome 浏览器为例，进入设置 -> 隐私和安全 -> 网站设置 -> 更多内容设置 -> 不安全内容（或者直接打开 `chrome://settings/content/insecureContent` 进行设置），在“允许显示不安全内容”内添加 `metacubex.github.io`
<img src="https://github.com/user-attachments/assets/8f8c358a-8f6c-45b0-96d9-3f03ef514dbc" width="60%"/>

2. 首次进入 https://metacubex.github.io/metacubexd 需要添加“后端地址”，输入 `http://192.168.31.1:9090` 并点击“添加”即可访问 Dashboard 面板
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/bb27d6e2-d72b-4a4a-a038-0fd6d085a573" width="60%"/>
