# RG-EG105G-P-L 路由器增强 · 二进制发布

锐捷 RG-EG105G-P-L（MT7621A / mipsel_24kc **无 FPU** / ReyeeOS 2.340.0.2014 / Linux 4.4.198 / musl）
自管理平台（adm5544）配套的 **mipsle 二进制发布仓**。

源码、部署脚本、网页与完整改造教程在私有仓库 `RG-EG105G-P-L-src`（不公开）。

> 本仓库仅供个人对自己拥有设备的软件改造学习研究之用，请遵守当地法律法规，禁止用于任何违法违规用途。

## 二进制清单

| 文件 | 用途 | 大小 | SHA-256 |
|---|---|---|---|
| `bin/trojan-bundle.tar.gz` | Trojan 代理服务端 trojand（Go 自研，TLS+SOCKS5，UPX 压缩，mipsle softfloat） | 1.05 MB | `c9fc3c1ac0940f444998dd76efc9e61a1670371a426f1e5b8d9f6bdb5c648119` |
| `bin/wg-bundle.tar.gz` | WireGuard 用户态单二进制 `wg`（wireguard-go v0.0.20230223 + wgctrl 配置工具合并，UPX） | 853 KB | `fb912067544b4a4ba25580b367c69a35b3c443b339f9dafbf728b3595d9355ba` |
| `bin/xfrpc-bundle-509982.tar.gz` | xfrpc 5.09.982（OpenWrt 官方编译版 + OpenSSL3/libevent2/json-c 依赖，支持 frps≥0.69 wireProtocol v2） | 2.03 MB | `a2338007cb974bfa7e0bca8718f23d1215a7a139624aae0024c327c7105050bd` |

> 说明：项目早期使用过官方 frpc 0.58.1 / 0.71.0（约 15~17 MB），后因内存（121 MB）与
> frps 0.71 的 wireProtocol v2 兼容要求改用 xfrpc 5.09.982，官方 frpc 不再内置，
> 需要时请自行从 [fatedier/frp releases](https://github.com/fatedier/frp/releases) 下载
> `linux_mipsle` 版本。

## 下载方式（必须走 API，raw 直连不通）

国内网络对 `raw.githubusercontent.com` / `objects.githubusercontent.com` 基本不可达，
但 `api.github.com` 通常**直连可用**。本仓库统一使用 **Contents API** 取文件：

```sh
# 示例：下载 trojan-bundle.tar.gz
curl -H "Accept: application/vnd.github.raw" -o trojan-bundle.tar.gz \
  "https://api.github.com/repos/air5210/RG-EG105G-P-L/contents/bin/trojan-bundle.tar.gz"

# 校验
sha256sum -c checksums.txt
```

要点：

- **必须带** `-H "Accept: application/vnd.github.raw"`，否则 Contents API 默认返回
  JSON 包装（含 base64 的 JSON 文本），而不是文件本体；
- 该方式对 >1 MB 文件同样有效（raw 媒体类型上限 100 MB）；
- 匿名访问限速 60 次/小时/IP，自动化下载请低频使用（本管理台仅在二进制缺失时下载一次）。

## 配套管理台

所有二进制由路由器上的独立管理台（lighttpd + Lua CGI，端口 5544）统一管理：
trojan / wg / xfrpc 均支持「上传 / URL 下载 / 开机自动拉取」，下载地址填本仓库的
Contents API 链接即可（管理台已适配 `Accept: application/vnd.github.raw` 头）。
