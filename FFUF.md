好的，整理一份 `ffuf` 的**常用命令和参数速查表**，方便你快速上手。

---

## 📖 FFUF 基础语法

```bash
ffuf -u <目标URL> -w <字典文件> [选项]
```

---

## 🔧 核心参数（必用）

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-u` | 目标 URL（用 `FUZZ` 标记爆破位置） | `-u https://example.com/FUZZ` |
| `-w` | 字典文件路径 | `-w /usr/share/wordlists/dirb/common.txt` |
| `-o` | 输出结果到文件 | `-o result.json` |
| `-of` | 输出格式（`json`, `html`, `md`, `csv`） | `-of json` |

---

## 🎯 匹配与过滤（结果筛选）

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-mc` | **匹配**状态码（默认 200-299,301,302,307,401,403,405,500） | `-mc 200,301,302,403` |
| `-fc` | **过滤**状态码（排除不需要的） | `-fc 404` |
| `-ms` | **匹配**响应大小（字节） | `-ms 100-200` |
| `-fs` | **过滤**响应大小 | `-fs 12345` |
| `-ml` | **匹配**行数 | `-ml 100-200` |
| `-fl` | **过滤**行数 | `-fl 50` |
| `-mw` | **匹配**单词数 | `-mw 500-1000` |
| `-fw` | **过滤**单词数 | `-fw 300` |
| `-mr` | **匹配**正则表达式 | `-mr "error\|warning"` |
| `-fr` | **过滤**正则表达式 | `-fr "login"` |

---

## 🚀 性能与速度

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-t` | 线程数（默认 40） | `-t 20` |
| `-p` | 请求延迟（秒） | `-p 0.5` 或 `-p 0.1-2.0`（随机延迟） |
| `-rate` | 每秒请求速率限制 | `-rate 30` |
| `-timeout` | 超时时间（秒，默认 10） | `-timeout 5` |

---

## 🛡️ 伪装与绕过（WAF 防护）

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-H` | 添加请求头 | `-H "User-Agent: Mozilla/5.0"` |
| `-b` | 添加 Cookie | `-b "session=abc123"` |
| `-X` | 指定 HTTP 方法 | `-X POST` |
| `-d` | POST 数据 | `-d "username=FUZZ&password=test"` |
| `-k` | 忽略 SSL 证书验证 | `-k` |
| `-x` | 使用代理 | `-x http://127.0.0.1:8080` |
| `-r` | 跟随重定向 | `-r` |

---

## 🧩 高级功能

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-e` | 添加扩展名（多个用逗号分隔） | `-e .php,.html,.txt` |
| `-mode` | 多字典工作模式（`clusterbomb`, `pitchfork`, `sniper`） | `-mode clusterbomb` |
| `-recursion` | 递归扫描 | `-recursion` |
| `-recursion-depth` | 递归深度 | `-recursion-depth 2` |
| `-ac` | 自动校准过滤选项 | `-ac` |
| `-v` | 详细输出 | `-v` |
| `-c` | 彩色输出 | `-c` |
| `-s` | 静默模式（不打印额外信息） | `-s` |
| `-json` | JSON 格式输出（每行一个 JSON 对象） | `-json` |
| `-debug-log` | 写入调试日志到文件 | `-debug-log ffuf.log` |

---

## 📂 常用字典路径（Kali）

| 字典 | 路径 |
|:---|:---|
| **Common（通用）** | `/usr/share/wordlists/dirb/common.txt` |
| **Small（精简）** | `/usr/share/wordlists/dirb/small.txt` |
| **Big（大字典）** | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` |
| **WordPress（核心）** | `/usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt` |
| **WordPress 插件** | `/usr/share/seclists/Discovery/Web-Content/CMS/wordpress-plugins.txt` |
| **WordPress 主题** | `/usr/share/seclists/Discovery/Web-Content/CMS/wordpress-themes.txt` |
| **Raft 大字典** | `/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt` |

---

## 🧪 实战命令示例

### 1️⃣ 基础目录爆破
```bash
ffuf -u https://example.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,302,403
```

### 2️⃣ WordPress 专用扫描
```bash
ffuf -u https://example.com/wordpress/FUZZ \
-w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt \
-mc 200,301,302,403 \
-e .php,.html,.txt \
-k -t 10 -p 0.5
```

### 3️⃣ 带扩展名的文件扫描
```bash
ffuf -u https://example.com/FUZZ \
-w /usr/share/wordlists/dirb/common.txt \
-e .php,.html,.txt,.bak,.old \
-fc 404 -t 20
```

### 4️⃣ 子域名爆破
```bash
ffuf -u https://FUZZ.example.com -w /usr/share/wordlists/subdomains.txt
```

### 5️⃣ POST 参数爆破
```bash
ffuf -u https://example.com/login.php -X POST \
-d "username=admin&password=FUZZ" \
-w /usr/share/wordlists/rockyou.txt \
-fc 302 -mr "Welcome"
```

### 6️⃣ 多位置爆破（Clusterbomb 模式）
```bash
ffuf -u https://example.com/FUZZ1?param=FUZZ2 \
-w /path/to/dir.txt:FUZZ1 -w /path/to/params.txt:FUZZ2 \
-mode clusterbomb -mc 200
```

### 7️⃣ 伪装浏览器（绕过 WAF）
```bash
ffuf -u https://example.com/FUZZ \
-w /usr/share/wordlists/dirb/common.txt \
-H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
-H "Referer: https://google.com" \
-k -t 10 -p 1.0
```

### 8️⃣ 输出到 JSON 文件
```bash
ffuf -u https://example.com/FUZZ \
-w /usr/share/wordlists/dirb/common.txt \
-mc 200,301,302,403 \
-o result.json -of json -v
```

---

## ⚠️ 注意事项

| 问题 | 建议 |
|:---|:---|
| **触发 WAF/IPS** | 降低线程（`-t 10`），增加延迟（`-p 1-2`），伪装 UA |
| **SSL 证书错误** | 添加 `-k` 忽略验证 |
| **字典太大** | 先用 `common.txt` 快速扫描，再用大字典深入 |
| **结果太多** | 使用 `-fs` 过滤掉常见大小，或使用 `-mc` 只关注特定状态码 |
| **找不到任何结果** | 检查是否用了 `-fc 404` 过度过滤，尝试先不加任何过滤 |

---

## 📌 总结

`ffuf` 是一个非常灵活、高效的 Web 模糊测试工具。建议从**基础目录爆破**开始，逐步调整参数，以应对该目标站点的 WAF/IPS 防护。
