# sing-box-mod

chenzai666 定制维护的 sing-box 一键安装与管理脚本。

## 定制内容

### 1. 支持手动指定服务器 IP（`-i / --ip` 参数）

安装时可通过 `-i` 参数手动指定服务器 IP，避免自动探测失败：

```bash
bash install.sh -i 1.2.3.4
bash install.sh -i 2001:db8::1       # IPv6
bash install.sh -i 1.2.3.4 -v v1.11.0 -p http://127.0.0.1:7890
```

### 2. 持久化自定义 IP（`sing-box ip set/clear`）

安装后修改配置时，IP 会被自动探测覆盖。定制版支持将 IP 持久化到配置文件：

```bash
sing-box ip set 1.2.3.4          # 设置固定 IP
sing-box ip clear                 # 恢复自动探测
sing-box ip                        # 查看当前 IP
```

### 3. Reality Short ID 随机生成

- 使用 `sing-box generate rand 4 --hex` 生成 4 字节 8 位十六进制 Short ID（与主流客户端兼容性最佳）
- 配置文件中不再使用空字符串 `[""]`，改为随机值
- 分享链接 URL 中包含正确的 `sid=` 参数
- 查看配置 (`sing-box info`) 时会显示 Short ID

### 4. 菜单支持"更改 Short ID"

在"更改配置"菜单中新增选项（选项 13），支持随时修改 Short ID：

```bash
sing-box change                    # 选择 13. 更改 Short ID
sing-box change 13                 # 交互式输入新值
sing-box change 13 auto            # 自动随机生成新值
sing-box change 13 abc12345        # 指定具体值
```

### 5. 支持 anytls-reality

协议菜单新增 `anytls-reality`，序号为 21。可用命令别名直接添加：

```bash
sing-box add anytls-reality [port] [password] [sni]
```

生成的分享链接格式为 `anytls://...?security=reality&...`，并包含 `pbk` 与 `sid` 参数。

### 6. anytls 自签场景对齐甬哥 argosbx 风格

无域名时使用自签证书的 anytls，默认注入伪装 SNI 并双参数跳过证书验证，导入客户端即开即用：

- 分享链接：`anytls://密码@IP:端口?sni=www.bing.com&insecure=1&allowInsecure=1#...`
- 服务端 TLS 配置自动补 `server_name: www.bing.com` + `insecure: true`
- `insecure` 兼容 sing-box 系客户端，`allowInsecure` 兼容 v2rayN 系，无需手动开启跳过证书验证
- Reality 伪装握手端口可用 `SB_HANDSHAKE_PORT` 环境变量自定义（默认 443）

### 7. 环境变量一键安装（批量部署）

`install.sh` 默认安装 reality；可通过环境变量一条命令批量安装任意协议组合：

```bash
# 默认安装 reality（向后兼容）
bash <(curl -Ls https://raw.githubusercontent.com/chenzai666/sing-box-mod/main/install.sh)

# 额外安装 anytls
SB_ANYTLS=1 bash <(curl -Ls .../install.sh)

# 额外安装 anytls-reality
SB_ANYTLS_REALITY=1 bash <(curl -Ls .../install.sh)

# 自定义协议列表 + 伪装端口（空格分隔）
SB_PROTOCOLS="reality anytls-reality" SB_HANDSHAKE_PORT=8443 bash <(curl -Ls .../install.sh)
```

批量安装时末尾统一重启服务，不再每个协议重启一次。

## 安装

```bash
wget -qO- https://raw.githubusercontent.com/chenzai666/sing-box-mod/main/install.sh | bash
```

或下载后本地执行：

```bash
git clone https://github.com/chenzai666/sing-box-mod.git
cd sing-box-mod
bash install.sh
```

## 与原版的主要差异

| 功能 | 上游原版 | 本定制版 |
|------|------------|---------|
| 安装时指定 IP | 不支持 | `-i <IP>` |
| IP 持久化 | 无，每次探测覆盖 | `sing-box ip set` |
| Reality short_id | 硬编码空字符串 `[""]` | 随机 4 字节 hex |
| 更改 short_id | 无 | `sing-box change 13` |
| anytls-reality | 无 | `sing-box add anytls-reality`，菜单序号 21 |
| anytls 自签 SNI | 无 | 默认 `www.bing.com` 伪装 |
| anytls 跳过证书验证 | 仅 `allowInsecure=1` | `insecure=1&allowInsecure=1` 双参数 |
| 批量安装 | 无 | `SB_PROTOCOLS` / `SB_ANYTLS` / `SB_ANYTLS_REALITY` |
| Reality 伪装端口 | 固定 443 | `SB_HANDSHAKE_PORT` 可配 |

## 原版特性

继承上游脚本的主要功能，包括 VLESS Reality、anytls-reality、Shadowsocks、Trojan、VMess 等协议的一键配置和管理。

## License

MIT
