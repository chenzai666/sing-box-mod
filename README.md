# sing-box-mod

基于 [233boy/sing-box](https://github.com/233boy/sing-box) 的定制版本。

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

- 使用 `sing-box generate rand 8 --hex` 生成 16 位十六进制 Short ID
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

| 功能 | 原版 233boy | 本定制版 |
|------|------------|---------|
| 安装时指定 IP | 不支持 | `-i <IP>` |
| IP 持久化 | 无，每次探测覆盖 | `sing-box ip set` |
| Reality short_id | 硬编码空字符串 `[""]` | 随机 16 位 hex |
| 更改 short_id | 无 | `sing-box change 13` |

## 原版特性

继承 233boy/sing-box 的全部功能，包括 VLESSReality、Shadowsocks、Trojan、VMess 等协议的一键配置和管理。

## License

MIT
