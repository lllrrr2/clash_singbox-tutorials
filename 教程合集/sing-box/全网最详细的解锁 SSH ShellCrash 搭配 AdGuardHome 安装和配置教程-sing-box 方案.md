# 前排提示
本教程内容较长，记得点开目录后查看  
<img src="https://user-images.githubusercontent.com/45238096/224132504-d3431fa0-c6db-4e0c-8ab2-ef6f4c99dbec.png" width="60%"/>

---
# 前言
1. 本教程基于 Redmi AX6000 [官方固件](https://www1.miwifi.com/miwifi_download.html) v1.0.70 版，[ShellCrash](https://github.com/juewuy/ShellCrash) v1.9.1 版，[AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) v0.108.0 版编写
2. 恢复 SSH，安装 ShellCrash 和 AdGuardHome 的方法也适用于其它已解锁 SSH 的路由器
3. 安装 [sing-box PuerNya 版内核](https://github.com/PuerNya/sing-box/tree/building) 内核和 AdGuardHome 时须注意路由器 CPU 架构，查看 CPU 架构可连接 SSH 后执行如下命令：  
`uname -ms`  
若执行结果是“linux aarch64”，就下载 armv8 或 arm64 版安装包；若是其它架构请下载相匹配的安装包
4. ShellCrash 和 AdGuardHome 中所有没有提到的配置保持默认即可
5. ShellCrash 和 AdGuardHome 快速安装方法请看《[ShellCrash 和 AdGuardHome 快速安装教程](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/ShellCrash%20%E5%92%8C%20AdGuardHome%20%E5%BF%AB%E9%80%9F%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B.md)》
---
# 一、 资源下载
打包下载：https://dustinwinvip.lanzoum.com/b01qd6p3a  
密码：zyxz  
注：
- 1. 没有对文件进行任何处理，请自行操作使用
- 2. 不保证实时更新，想用新版请安装后自行升级
- 3. 版本信息请查看打包文件内的 Readme.txt 文本

## 1. ShellCrash
官方下载：https://raw.githubusercontent.com/juewuy/ShellCrash/master/bin/ShellCrash.tar.gz
## 2. sing-box PuerNya 版内核
第三方下载：https://github.com/DustinWin/clash_singbox-tools/blob/main/sing-box-puernya/sing-box-linux-armv8.tar.gz
## 3. Termius
官方下载：https://autoupdate.termius.com/windows/Termius.exe
## 4. AdGuardHome
官方下载：https://github.com/AdguardTeam/AdGuardHome/releases  
下载 AdGuardHome_linux_arm64.tar.gz 文件
## 5. UPX
官方下载：https://github.com/upx/upx/releases  
下载 upx-xxx-win64.zip 文件
## 6. WinSCP
官方下载：https://winscp.net/eng/downloads.php
- 注：中文绿色版请下载打包文件

下载 WinSCP-xxx-Portable.zip 文件
# 二、 解锁 SSH
## 1. 复制 stok 值
进入路由器管理页面 http://192.168.31.1 ，登录后复制地址栏中的 stok 值  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/4951fb74-87c7-4eb6-a2e4-cb80a78587fe" width="60%"/>
## 2. 开启调试模式
将复制的 stok 值替换如下网址的{stok}并访问：
```
http://192.168.31.1/cgi-bin/luci/;stok={stok}/api/misystem/set_sys_time?timezone=%20%27%20%3B%20zz%3D%24%28dd%20if%3D%2Fdev%2Fzero%20bs%3D1%20count%3D2%202%3E%2Fdev%2Fnull%29%20%3B%20printf%20%27%A5%5A%25c%25c%27%20%24zz%20%24zz%20%7C%20mtd%20write%20-%20crash%20%3B%20
```
网页内容显示{"code":0}表示成功开启调试模式
## 3. 通过浏览器请求重启路由器
继续将复制的 stok 值替换如下网址的{stok}并访问：
```
http://192.168.31.1/cgi-bin/luci/;stok={stok}/api/misystem/set_sys_time?timezone=%20%27%20%3b%20reboot%20%3b%20
```
网页内容显示{"code":0}，此时路由器会重启
## 4. 再次复制 stok 值
重启完成后进入路由器管理页面并登录，再次复制 stok 值
## 5. 设置 Bdata 永久开启 Telnet
将复制的 stok 值替换如下网址的{stok}并访问：
```
http://192.168.31.1/cgi-bin/luci/;stok={stok}/api/misystem/set_sys_time?timezone=%20%27%20%3B%20bdata%20set%20telnet_en%3D1%20%3B%20bdata%20set%20ssh_en%3D1%20%3B%20bdata%20set%20uart_en%3D1%20%3B%20bdata%20commit%20%3B%20
```
网页内容显示{"code":0}表示成功设置 Bdata 永久开启 Telnet
## 6. 再次通过浏览器请求重启路由器
继续将复制的 stok 值替换如下网址的{stok}并访问：
```
http://192.168.31.1/cgi-bin/luci/;stok={stok}/api/misystem/set_sys_time?timezone=%20%27%20%3b%20reboot%20%3b%20
```
网页内容显示{"code":0}，此时路由器会再次重启
## 7. 连接 Telnet
显示“ARE U OK”表示成功解锁 SSH  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/65862548-dd4a-4ffb-81ec-39fbeea60125" width="60%"/>
## 8. 永久开启并固化 SSH
直接粘贴如下所有命令：
- 注：第一行命令是将 Telnet 或 SSH 登录密码设置为“12345678”，可自定义

```
echo -e '12345678\n12345678' | passwd root
nvram set telnet_en=1
nvram set ssh_en=1
nvram set uart_en=1
nvram set boot_wait=on
nvram commit
/etc/init.d/dropbear enable & /etc/init.d/dropbear start
mkdir -p /data/auto_ssh
curl -o /data/auto_ssh/auto_ssh.sh -L https://cdn.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
chmod +x /data/auto_ssh/auto_ssh.sh
/data/auto_ssh/auto_ssh.sh install
uci set system.@system[0].timezone='CST-8'
uci set system.@system[0].webtimezone='CST-8'
uci set system.@system[0].timezoneindex='2.84'
uci commit
mtd erase crash
reboot
```
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/6b3af81b-94ac-4905-b05b-b6fd98a513a2" width="60%"/>

最后一行 reboot 命令需要手动回车（下同），回车后路由器会重启  
**SSH 解锁成功！**
# 三、 恢复 SSH
若已解锁并固化过 SSH 的路由器在升级固件或恢复出厂设置后 SSH 丢失，可快速再次解锁 SSH
## 1. 计算 Telnet 登录密码
打开网站 https://miwifi.dev/ssh ，在 SN 处输入路由器背面的 SN 号，点击“Calc”按钮  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/43143002-75a2-4f23-a5c4-a120fbb8875f" width="60%"/>
## 2. 连接 Telnet
用户名为：root，密码为第 1 步中计算出的 Telnet 登录密码  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/e1694432-ec7f-46bf-b1b0-7a2fbd6ff019" width="60%"/>  
直接粘贴如下所有命令：
- 注：最后一行命令是将 Telnet 或 SSH 登录密码设置为“12345678”，可自定义

```
mkdir -p /data/auto_ssh
curl -o /data/auto_ssh/auto_ssh.sh -L https://cdn.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
chmod +x /data/auto_ssh/auto_ssh.sh
/data/auto_ssh/auto_ssh.sh install
echo -e '12345678\n12345678' | passwd root
```
## 3. 更改 Telnet 和 SSH 登录密码（可选）
执行如下命令：  
```
passwd root
```
输入密码如：12341234，回车后输入同样的密码，再次回车即可  
**SSH 恢复成功！**
# 四、 连接 SSH
## 1. 给 Windows 操作系统添加 SSH 支持（任选一）
① 启用 Telnet 客户端  
进入控制面板 -> 程序和功能 -> 启用或关闭 Windows 功能，勾选“Telnet 客户端”  
<img src="https://user-images.githubusercontent.com/45238096/224110758-b3f85378-39dc-407d-82ba-7b1faaf12753.png" width="60%"/>  
② 启用 OpenSSH 服务器  
进入设置 -> 应用 -> 可选功能 -> 查看功能 -> 搜索“ssh”，勾选“OpenSSH 服务器”并安装  
<img src="https://user-images.githubusercontent.com/45238096/224110859-c869fed4-05bb-495b-a13c-aa3f78bb0ef7.png" width="60%"/>  
重启系统  
③ 连接 Telnet  
成功解锁或恢复 SSH 后，以管理员身份运行 PowerShell 或 CMD，执行如下命令：
```
telnet 192.168.31.1
```
- 注：首次登录不需要用户名和密码，解锁或恢复 SSH 后用户名为 root，密码为 SSH 登录密码

④ 连接 SSH  
成功解锁或恢复 SSH 后，以管理员身份运行 PowerShell 或 CMD，执行如下命令：
```
ssh root@192.168.31.1
```
- 注：若当前电脑登录过 SSH，后路由器经过重新解锁或恢复 SSH，需要进入 *C:\Users\\[用户名]\\.ssh* 文件夹，删除“known_hosts”文件，否则登录会报错

首次登录需要手动输入“yes”，然后回车  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/5ca31ea0-d2f4-4cbb-8f0b-5739fa1a3ff3" width="60%"/>  
输入 SSH 登录密码（输入过程中不会显示任何字符），回车  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/9db9d0e3-759e-442d-9c96-76c2f454a047" width="60%"/>  
显示“ARE U OK”表示成功登录 SSH  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/0bcf1d7b-9923-4b75-9313-c86e8ff68122" width="60%"/>
## 2. 通过 SSH 工具添加 SSH 支持（任选一）
① 打开 Termius  
安装 Termius 并打开（登录后可一直免费试用），现暂时点击“l don't want a free trial”  
<img src="https://user-images.githubusercontent.com/45238096/231961082-1fb3b895-e42d-46d5-98a6-e7a15cdaa2cc.png" width="60%"/>  
② 添加 Host  
依次点击 ADD->New Host  
<img src="https://user-images.githubusercontent.com/45238096/224111075-edf1f8a5-d30a-4c95-823f-0a756d2a9565.png" width="60%"/>  
③ 添加 Telnet  
按图输入，选中“Telnet”，点击右上角的“→|”图标
- 注：首次登录不需要用户名和密码，解锁或恢复 SSH 后用户名为 root，密码为 SSH 登录密码  
<img src="https://user-images.githubusercontent.com/45238096/224111315-e5d6caa3-962d-4b1e-9b2d-2a917bb527c2.png" width="60%"/>

④ 添加 SSH  
同样先按照第 ② 步操作，然后按图输入，选中“SSH”，“Password”为解锁或恢复 SSH 时设置的密码，最后一步点击右上角的“→|”图标  
<img src="https://user-images.githubusercontent.com/45238096/224111529-4eab34c8-4c28-41d3-8a20-bb62ad61ab84.png" width="60%"/>  
⑤ 连接 Telnet 或 SSH  
按需双击添加的项即可  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/62b6bdd2-7e18-4697-a70e-5ff72e95f41d" width="60%"/>
- 注：首次连接 SSH 需要点击“Add and continue”  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/542d0894-ae64-4cb3-aedd-9e24635dde4a" width="60%"/>

显示“ARE U OK”表示成功登录 SSH  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/6f5ba51e-9291-4332-aea5-967a91c2b70e" width="60%"/>
## 3. 通过 WinSCP 连接路由器文件管理
将下载的 WinSCP-xxx-Portable.zip 文件解压，路径随意，打开 WinSCP，“文件协议”选择“SCP”，其它按图输入，“密码”为 SSH 登录密码，完成后点击登录  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/04f0611b-f4dd-4e49-8342-8ef873d8e286" width="60%"/>  
左侧为电脑本地文件，右侧为路由器文件  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/f9c38757-9863-4d60-863b-bf178aef64f9" width="60%"/>
# 五、 ShellCrash 安装和配置
## 1. ShellCrash 安装
① 打开 WinSCP，将下载的 ShellCrash.tar.gz 文件移动到路由器的 */tmp* 目录中  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/067c0496-20e5-45c9-acdd-5dfe3ac7c0b2" width="60%"/>  
② 连接 SSH，执行如下命令：
```
mkdir -p /tmp/SC_tmp && tar -zxf '/tmp/ShellCrash.tar.gz' -C /tmp/SC_tmp/ && source /tmp/SC_tmp/init.sh
```
③ 选择 1 安装到 /data 目录（推荐，支持软固化功能）  
④ 将 CrashCore 文件移动到路由器的 */tmp* 目录中  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/168fc27d-3423-4c73-8fe0-ae10bddadb14" width="60%"/>  
**ShellCrash 安装成功！**
## 2. ShellCrash 配置
① 连接 SSH 后执行 `crash` 命令打开 ShellCrash 配置脚本  
首次打开会进入新手引导，选择 1 路由设备配置局域网透明代理  
启用推荐的自动任务配置  
根据需要是否启用软固化（此处选择 1）
- 注：解锁 SSH 时已成功启用软固化

根据需要是否选择 1 确认导入配置文件（此处选择 0）  
根据需要是否选择 1 立即启动 clash 服务（此处选择 0）
- 注：强烈建议选择 0，待以下配置完成后，最后一步启动 clash 服务

② 此时脚本会自动“发现可用的内核文件”，选择 1 加载，后选择 5 Sing-Box-Puer 内核  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/b44eefa8-a25a-44ab-a34f-3ebf0fb90390" width="60%"/>  
③ 内核加载完成后根据需要是否保留相关数据库文件（此处选择 0）  
④ 进入主菜单 -> 2 内核功能设置 -> 1 切换防火墙运行模式，选择 3 Tproxy 模式
- 注：有“Tproxy 模式”就选“Tproxy 模式”，否则推荐选“混合模式”，宽带在 300M 内推荐 Tun 模式

进入 1 切换防火墙运行模式 -> 9 ipv6 设置，若机场节点支持 IPv6，可开启 1 ipv6 透明代理；根据自身需要开启 3 CNV6 绕过内核  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/e49bf44f-6f71-4c02-ab18-d762c67f1b41" width="60%"/>  
进入 2 切换 DNS 运行模式，选择 3 mix 混合模式  
进入 4 DNS 进阶设置，选择 7 禁用 DNS 劫持  
注：
- 1. 若单独使用 ShellCrash，请不要禁用 DNS 劫持
- 2. 推荐设置 DNS 分流（单独使用 ShellCrash 以及 ShellCrash 搭配 AdGuardHome 都适用），请看《[ShellCrash 使用 sing-box PuerNya 版内核进行 DNS 分流教程-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E8%BF%9B%E9%98%B6%E7%AF%87/ShellCrash%20%E4%BD%BF%E7%94%A8%20sing-box%20PuerNya%20%E7%89%88%E5%86%85%E6%A0%B8%E8%BF%9B%E8%A1%8C%20DNS%20%E5%88%86%E6%B5%81%E6%95%99%E7%A8%8B-geodata%20%E6%96%B9%E6%A1%88.md)》或《[ShellCrash 使用 sing-box PuerNya 版内核进行 DNS 分流教程-ruleset方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E8%BF%9B%E9%98%B6%E7%AF%87/ShellCrash%20%E4%BD%BF%E7%94%A8%20sing-box%20PuerNya%20%E7%89%88%E5%86%85%E6%A0%B8%E8%BF%9B%E8%A1%8C%20DNS%20%E5%88%86%E6%B5%81%E6%95%99%E7%A8%8B-ruleset%E6%96%B9%E6%A1%88.md)》

<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/5c929cdb-c8cd-4020-9a9f-c73b899e1857" width="60%"/>

返回到 2 内核功能设置，根据自身需要开启 8 CN_IP 绕过内核  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/dfd1d2b5-22fd-410d-b73e-a67aefea6d3e" width="60%"/>  
⑤ 进入主菜单 -> 4 内核启动设置，选择 1 允许 ShellCrash 开机启动（若重启路由器后服务没有自动运行，可“设置自启延时”为 30 秒）  
⑥ 进入主菜单 -> 9 更新/卸载 -> 7 切换安装源及安装版本，选择 b 切换至公测版-master -> 1 Jsdelivr_CDN 源，追求新版可选择 c 切换至开发版（可能不稳定）  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/a5ef2c45-e3f9-4099-9ed6-d75dcac978a8" width="60%"/>  
进入 4 安装本地 Dashboard 面板，选择 3 安装 MetaXD 面板  
注：
- 1. 启动 Clash 服务后，面板 Dashboard 访问链接为：http://192.168.31.1:9999/ui
- 2. 初次打开需要添加“后端地址”：http://192.168.31.1:9999

<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/f0b7b76a-ead0-49ce-9e7a-56cded971b37" width="60%"/>

⑦ 进入主菜单-> 6 导入配置文件  
注：
- 1. 选择 1 在线生成 singboxp 配置文件，粘贴你的订阅链接并回车，输入“1”并再次回车即可
- 2. 选择 2 在线获取完整配置文件，需要一定的 sing-box 知识储备，请查看《[生成带有自定义策略组和规则的 sing-box 配置文件直链-geodata 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BA%E7%AB%99%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20sing-box%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-geodata%20%E6%96%B9%E6%A1%88.md)》或《[生成带有自定义策略组和规则的 sing-box 配置文件直链-ruleset 方案](https://github.com/DustinWin/clash_singbox-tutorials/blob/main/%E6%95%99%E7%A8%8B%E5%90%88%E9%9B%86/sing-box/%E5%9F%BA%E7%A1%80%E7%AF%87/%E7%94%9F%E6%88%90%E5%B8%A6%E6%9C%89%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BA%E7%AB%99%E5%92%8C%E8%A7%84%E5%88%99%E7%9A%84%20sing-box%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9B%B4%E9%93%BE-ruleset%20%E6%96%B9%E6%A1%88.md)》

导入配置文件完成后，选择 1 启动/重启服务  
**ShellCrash 配置成功！**  

**拓展：**  
ShellCrash 常用命令
- 1. 打开配置：
```
crash
```
- 2. 启动服务：
```
$CRASHDIR/start.sh start
```
- 3. 停止服务：
```
$CRASHDIR/start.sh stop
```
- 4. 重启服务：
```
$CRASHDIR/start.sh restart
```
- 5. 更新订阅：
```
$CRASHDIR/start.sh update_config
```
- 6. 查看帮助和说明：
```
crash -h
```
## 3. ShellCrash 升级
进入主菜单 -> 9 更新/卸载，查看“管理脚本”、“内核文件”和“数据库文件”有无新版本，有则选择对应的数字进行升级即可  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/27c492a9-160d-45e9-92a1-5ea6d056d808" width="60%"/>
## 4. ShellCrash 卸载
① 通过脚本命令进行卸载（任选一）  
连接 SSH 后，直接粘贴如下所有命令：
```
$CRASHDIR/start.sh stop && crash -u
```
② 通过 ShellCrash 配置进行卸载（任选一）  
进入主菜单 -> 9 更新/卸载，选择 9 卸载 ShellCrash
# 六 、 AdGuardHome 安装和配置
## 1. AdGuardHome 安装
① 将下载的 upx-xxx-win64.zip 文件解压到桌面，目录结构为 *C:\Users\\[用户名]\Desktop\upx*  
② 将下载的 AdGuardHome_linux_arm64.tar.gz 文件复制到桌面，以管理员身份运行 PowerShell，依次执行如下命令：
```
cd C:\Users\[用户名]\Desktop
tar -zxvf AdGuardHome_linux_arm64.tar.gz
```
.tar.gz 压缩文件成功解压到桌面的 *AdGuardHome* 文件夹内，目录结构为 *C:\Users\\[用户名]\Desktop\AdGuardHome*  
③ 进入 *AdGuardHome* 文件夹，将里面的“AdGuardHome”文件移动到 *C:\Users\\[用户名]\Desktop\upx* 文件夹中  
依次执行如下命令：
```
cd C:\Users\[用户名]\Desktop\upx
.\upx AdGuardHome
```
④ 将压缩后的“AdGuardHome”文件移动到路由器的 */data/AdGuardHome* 目录（没有此目录就新建）中  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/23340e1f-64f0-44a6-893e-5fb48304ef61" width="60%"/>  
⑤ 进入路由器文件管理的 */data/auto_ssh* 目录，右击“auto_ssh.sh”文件  
注：
- 1. 若没有此目录和文件，可新建
- 2. 新建后连接 SSH，直接粘贴如下所有命令：

```
chmod +x /data/auto_ssh && chmod +x /data/auto_ssh/auto_ssh.sh
```
并编辑  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/a5057b0f-4cc7-4c23-81e6-913e1bc856b5" width="60%"/>  
⑥ 在最下方添加如下内容并保存：
注：
- 1. AdGuardHome 的“DNS 服务器端口”须设置为“5353”
- 2. 保留首尾的空行

```

/data/AdGuardHome/AdGuardHome -s install
/data/AdGuardHome/AdGuardHome -s start
iptables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353

```
⑦ 连接 SSH，直接粘贴如下所有命令：
- 注：AdGuardHome 的“DNS 服务器端口”须设置为“5353”
```
chmod +x /data/AdGuardHome/AdGuardHome
/data/AdGuardHome/AdGuardHome -s install
/data/AdGuardHome/AdGuardHome -s start
iptables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353
```
**AdGuardHome 安装成功！**
## 2. AdGuardHome 配置
① 打开网页 http://192.168.31.1:3000  
点击“开始配置”，**“网页管理界面端口”输入“3000”，“DNS 服务器端口”输入“5353”**  
“身份认证”设置用户名和密码  
② 点击“打开仪表盘”后输入刚才设置的用户名和密码“登入”，就可以进入 AdGuardHome 管理页面  
③ 进入设置 -> 常规设置  
取消勾选“启用日志”并点击“保存”（日志非常占用空间）  
④ 进入设置 -> DNS 设置  
“上游 DNS 服务器”设置为 `127.0.0.1:1053`，并选择“并行请求”
- 注：此时页面右下角可能会弹出报错信息，但不用理会

<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/d211440f-23f2-45d1-8319-4292ea6e85f8" width="60%"/>

“后备 DNS 服务器”设置为：
```
https://doh.pub/dns-query
https://dns.alidns.com/dns-query
```
“Bootstrap DNS 服务器”设置为：
```
https://1.12.12.12/dns-query
https://223.5.5.5/dns-query
```
直接点击“应用”即可  
<img src="https://github.com/user-attachments/assets/da7cf0d0-9844-42d1-9242-d7e8623fcebb" width="60%"/>  
“速度限制”输入“0”，勾选“启用 EDNS 客户端子网”，然后点击下方的“保存”  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/e7924612-6dbe-423c-9967-15bdc31e6ef7" width="60%"/>  
勾选“乐观缓存”，并点击“保存”  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/f857800a-ebf4-4031-994f-27cd6b3e7b53" width="60%"/>  
⑤ 进入过滤器 -> DNS 黑名单 -> 添加黑名单 -> 从列表中选择，推荐勾选“区域”里的“CHN: anti-AD”，然后点击“保存”
- 注：若下载不稳定或失败，可手动将下载地址 URL 更改为 `https://anti-ad.net/easylist.txt`

<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/a0b4dff7-df3d-4b50-9d10-3fdffe004581" width="60%"/>

添加成功  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/56c133e1-6298-4cf8-a565-f1171959f20f" width="60%"/>

⑥ 进入过滤器 -> DNS 重写 -> 添加 DNS 重写，“输入域”填写 `miwifi.com`，“输入 IP 地址或域名”填写 `192.168.31.1`，然后点击“保存”  
注：
- 1. 此步骤可解决访问 http://miwifi.com 时无法打开小米或红米路由器管理页面的问题，其它型号路由器请根据自身需要填写
- 2. 若已在 ShellCrash 配置文件自行添加了 `dns.hosts`，可跳过此步骤

添加成功  
<img src="https://github.com/DustinWin/clash_singbox-tutorials/assets/45238096/7afe4816-93d6-426f-9e5a-82ace37e4b91" width="60%"/>

**AdGuardHome 配置成功！**  

**拓展：**  
AdGuardHome 常用命令
- 1. 启动服务：
```
/data/AdGuardHome/AdGuardHome -s start
```
- 2. 停止服务：
```
/data/AdGuardHome/AdGuardHome -s stop
```
- 3. 重启服务：
```
/data/AdGuardHome/AdGuardHome -s restart
```
- 4. 显示当前服务状态：
```
/data/AdGuardHome/AdGuardHome -s status
```
## 3. AdGuardHome 升级
为了节约路由器内存，请按照如下步骤进行操作：  
① 执行《六、 1. ① ② ③ ④ ⑤（替换）》的操作步骤  
② 连接 SSH，直接粘贴如下所有命令：
```
chmod +x /data/AdGuardHome/AdGuardHome && /data/AdGuardHome/AdGuardHome -s restart
```
## 4. AdGuardHome 卸载
① 删除开机启动项  
执行《六、 1. ⑥》的操作步骤，删除添加的内容：
```
/data/AdGuardHome/AdGuardHome -s install
/data/AdGuardHome/AdGuardHome -s start
iptables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 5353
ip6tables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 5353
```
并保存  
② 卸载 AdGuardHome  
连接 SSH，直接粘贴如下所有命令：
```
/data/AdGuardHome/AdGuardHome -s stop && /data/AdGuardHome/AdGuardHome -s uninstall && rm -rf /data/AdGuardHome
iptables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 53
iptables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 53
ip6tables -t nat -A PREROUTING -p tcp --dport 53 -j REDIRECT --to-ports 53
ip6tables -t nat -A PREROUTING -p udp --dport 53 -j REDIRECT --to-ports 53
```
③ 重启路由器
# 七、 效果图
## 1. IPv6 效果
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/962cef1b-b772-4c18-8040-0a370da8be0a" width="50%"/><img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/5a26417a-cd29-4605-bafe-0458f23e19aa" width="50%"/>
## 2. BT 下载效果
UDP 连接正常，使用的是移动 500M 带宽  
<img src="https://user-images.githubusercontent.com/45238096/224113233-4d76dec2-495c-4790-a00e-538fc1469639.png" width="100%"/>
## 3. ShellCrash 效果
使用的是移动 300M 带宽  
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/695f0f5d-7757-4b65-ac30-535544c5c440" width="100%"/>
## 4. AdGuardHome 效果
<img src="https://github.com/DustinWin/clash-tutorials/assets/45238096/b2b322e4-2ac9-4d94-bb14-21eb9e3dc7dd" width="100%"/>
