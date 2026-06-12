# Xray Xinchen Script

基于 233boy/Xray 修改的一键安装与管理脚本，当前分支重点增强了「按配置单独设置链式代理」能力。

默认安装后会创建一个 `VLESS-REALITY` 配置。脚本支持多配置同时运行，每个配置都可以单独查看、修改、删除，也可以单独添加或删除链式代理。

## 主要特性

- 一键安装 Xray core、管理脚本、jq 等依赖
- 默认添加 `VLESS-REALITY`
- 默认 REALITY SNI: `video-caps.wetvinfo.com`
- 新生成的每个配置默认走 `direct`
- 可为某一个配置单独导入链式代理
- 删除某个配置的链式代理后自动恢复 `direct`
- 支持通过节点链接导入链式代理
- 支持 VMess、VLESS、Trojan、Shadowsocks、Socks 等常用协议
- 支持 VMess TCP/mKCP 动态端口
- 支持 Caddy 自动 TLS 与伪装网站
- 支持 DNS、日志、BBR、更新、重装、卸载等管理命令

## 支持系统

- Debian
- Ubuntu
- CentOS
- Alpine Linux

需要 root 权限运行。

## 一键安装

```bash
bash <(wget -qO- https://raw.githubusercontent.com/xinxinxiaochenchen/xray-xinchen/main/install.sh)
```

安装完成后使用：

```bash
xray
```

查看版本：

```bash
xray version
```

当前脚本版本：`v1.41`

## 默认出口逻辑

本分支的出口逻辑是：

- 默认情况下，每个新生成的配置都显式走 `direct`
- 如果某个配置需要链式代理，可以单独给它添加
- 删除该配置的链式代理后，该配置恢复 `direct`
- 一个配置的出口设置不会影响其他配置

例如服务器上有两个配置：

```text
VLESS-REALITY-23233.json
VLESS-REALITY-45885.json
```

你可以让 `VLESS-REALITY-23233.json` 直连，同时让 `VLESS-REALITY-45885.json` 单独走一个 Shadowsocks 或 SOCKS 上游。

## 链式代理

进入菜单：

```bash
xray
```

选择：

```text
链式代理
```

菜单包含：

```text
1. 导入节点链接
2. 手动设置链式代理
3. 查看链式代理
4. 删除链式代理
```

### 通过节点链接导入

```bash
xray chain import 配置名 '节点链接'
```

示例：

```bash
xray chain import VLESS-REALITY-45885.json 'ss://...'
xray chain import VLESS-REALITY-45885.json 'socks://...'
xray chain import VLESS-REALITY-45885.json 'vless://...'
xray chain import VLESS-REALITY-45885.json 'vmess://...'
xray chain import VLESS-REALITY-45885.json 'trojan://...'
```

也可以省略配置名，脚本会让你选择：

```bash
xray chain import 'ss://...'
```

支持导入的链接类型：

- `ss://`
- `socks://`
- `socks5://`
- `http://`
- `vless://`
- `vmess://`
- `trojan://`

### 手动设置链式代理

SOCKS:

```bash
xray chain set 配置名 socks 地址 端口
xray chain set 配置名 socks 地址 端口 用户名 密码
```

HTTP:

```bash
xray chain set 配置名 http 地址 端口
xray chain set 配置名 http 地址 端口 用户名 密码
```

Shadowsocks:

```bash
xray chain set 配置名 shadowsocks 地址 端口 加密方式 密码
```

VMess:

```bash
xray chain set 配置名 vmess 地址 端口 UUID
```

VLESS:

```bash
xray chain set 配置名 vless 地址 端口 UUID
```

Trojan:

```bash
xray chain set 配置名 trojan 地址 端口 密码
```

### 查看链式代理

```bash
xray chain list
```

### 删除链式代理

```bash
xray chain del 配置名
```

删除后该配置会恢复为 `direct`。

## 添加配置

默认添加 `VLESS-REALITY`：

```bash
xray add reality
```

常用协议示例：

```bash
xray add ss
xray add socks
xray add tcp
xray add kcp
xray add ws 域名
xray add grpc 域名
xray add xhttp 域名
xray add vws 域名
xray add vgrpc 域名
xray add tws 域名
xray add tgrpc 域名
```

只生成 JSON，不落盘：

```bash
xray gen reality
```

TLS 协议不自动配置 Caddy：

```bash
xray no-auto-tls vws 域名
```

## 查看与分享配置

查看配置：

```bash
xray info
xray info 配置名
```

输出 URL：

```bash
xray url 配置名
```

输出二维码：

```bash
xray qr 配置名
```

生成客户端参考 JSON：

```bash
xray client 配置名
xray genc 配置名
```

## 修改配置

常用修改命令：

```bash
xray change 配置名 port 新端口
xray change 配置名 id auto
xray change 配置名 path /new-path
xray change 配置名 host example.com
xray change 配置名 sni example.com
xray change 配置名 key auto
xray change 配置名 method auto
xray change 配置名 passwd auto
xray change 配置名 web example.com
xray change 配置名 new reality
```

快捷写法也可用：

```bash
xray port 配置名 auto
xray id 配置名 auto
xray sni 配置名 video-caps.wetvinfo.com
```

修复单个配置：

```bash
xray fix 配置名
```

修复全部配置：

```bash
xray fix-all
```

## 删除配置

删除单个配置：

```bash
xray del 配置名
```

删除多个配置：

```bash
xray ddel 配置名1 配置名2
```

注意：删除命令不会二次确认，请谨慎使用。

## DNS 与日志

设置 DNS：

```bash
xray dns cloudflare
xray dns google
xray dns 1111
xray dns 8888
xray dns set https://dns.example/dns-query
xray dns none
```

查看访问日志：

```bash
xray log
```

查看错误日志：

```bash
xray logerr
```

设置日志级别：

```bash
xray log debug
xray log info
xray log warning
xray log error
xray log none
```

临时删除日志文件：

```bash
xray log del
```

## 服务管理

查看状态：

```bash
xray status
```

启动、停止、重启：

```bash
xray start
xray stop
xray restart
```

管理 Caddy：

```bash
xray restart caddy
```

测试运行：

```bash
xray test
```

## 更新

更新 Xray core：

```bash
xray update core
```

更新脚本：

```bash
xray update sh
```

或：

```bash
xray update.sh
```

更新 geoip.dat 和 geosite.dat：

```bash
xray update dat
```

更新 Caddy：

```bash
xray update caddy
```

## 其他命令

返回当前服务器 IP：

```bash
xray ip
```

返回可用端口：

```bash
xray get-port
```

生成 UUID：

```bash
xray uuid
```

生成 X25519 密钥：

```bash
xray x25519
```

生成 Shadowsocks 2022 密码：

```bash
xray ss2022
```

启用 BBR：

```bash
xray bbr
```

运行原生 Xray 命令：

```bash
xray bin help
xray api --help
```

## 重装与卸载

重装脚本：

```bash
xray reinstall
```

卸载：

```bash
xray uninstall
```

## 文件位置

脚本目录：

```text
/etc/xray/sh
```

Xray core：

```text
/etc/xray/bin/xray
```

主配置：

```text
/etc/xray/config.json
```

单个入站配置：

```text
/etc/xray/conf/*.json
```

日志目录：

```text
/var/log/xray
```

Caddy 配置：

```text
/etc/caddy
```

## 说明

本分支保留原脚本的多配置管理方式，同时加入按配置单独出口控制。链式代理通过 Xray 的 `routing.rules` 按 `inboundTag` 精确匹配配置文件名，不会影响其他配置。

如果导入链式代理后配置测试失败，脚本会自动恢复原 `config.json`，避免 Xray 因错误 outbound 无法启动。

项目地址：

```text
https://github.com/xinxinxiaochenchen/xray-xinchen
```

原项目：

```text
https://github.com/233boy/Xray
```
