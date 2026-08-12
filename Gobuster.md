好的，整理一份 `gobuster` 的**常用命令和参数速查表**，方便你快速上手。

---

## 📖 Gobuster 基础语法

```bash
gobuster <模式> -u <目标> -w <字典> [选项]
```

---

## 🔧 三种核心模式

| 模式 | 说明 | 示例 |
|:---|:---|:---|
| `dir` | 目录/文件爆破（最常用） | `gobuster dir -u https://example.com -w /path/to/wordlist.txt` |
| `dns` | 子域名枚举 | `gobuster dns -d example.com -w /path/to/wordlist.txt` |
| `vhost` | 虚拟主机枚举 | `gobuster vhost -u https://example.com -w /path/to/wordlist.txt` |
| `fuzz` | 模糊测试（自定义位置） | `gobuster fuzz -u https://example.com/FUZZ -w /path/to/wordlist.txt` |

---

## 📂 目录爆破（dir）常用参数

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-u` | 目标 URL | `-u https://example.com` |
| `-w` | 字典文件路径 | `-w /usr/share/wordlists/dirb/common.txt` |
| `-x` | 添加扩展名（多个用逗号分隔） | `-x php,html,txt` |
| `-t` | 线程数（默认 10） | `-t 20` |
| `-k` | 忽略 SSL 证书验证 | `-k` |
| `-o` | 输出结果到文件 | `-o result.txt` |
| `-s` | 显示指定状态码 | `-s "200,301,302,403"` |
| `-b` | 排除指定状态码 | `-b "404"` |
| `-l` | 显示响应头信息 | `-l` |
| `-r` | 跟随重定向 | `-r` |
| `-c` | 显示响应体大小 | `-c` |
| `-H` | 添加请求头 | `-H "User-Agent: Mozilla/5.0"` |
| `-a` | 自定义 User-Agent | `-a "Mozilla/5.0"` |
| `--timeout` | 超时时间（秒） | `--timeout 10` |

---

## 🌐 DNS 子域名爆破（dns）常用参数

| 参数 | 说明 | 示例 |
|:---|:---|:---|
| `-d` | 目标域名 | `-d example.com` |
| `-w` | 子域名字典 | `-w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` |
| `-t` | 线程数（默认 10） | `-t 30` |
| `-o` | 输出结果到文件 | `-o subdomains.txt` |
| `-i` | 显示 IP 地址 | `-i` |
| `-c` | 显示 CNAME 记录 | `-c` |
| `-r` | 使用自定义 DNS 服务器 | `-r "8.8.8.8,1.1.1.1"` |
| `--timeout` | DNS 查询超时时间（秒） | `--timeout 5` |

---

## 🧪 常用实战命令

### 1️⃣ 基础目录爆破
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt
```

### 2️⃣ 带扩展名的目录爆破
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak
```

### 3️⃣ 过滤状态码（排除 404）
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -b "404"
```

### 4️⃣ 只显示特定状态码
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -s "200,301,302,403"
```

### 5️⃣ 忽略 SSL 证书 + 自定义 User-Agent
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -k -a "Mozilla/5.0"
```

### 6️⃣ 子域名枚举
```bash
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 30 -i
```

### 7️⃣ 虚拟主机枚举
```bash
gobuster vhost -u https://example.com -w /usr/share/wordlists/subdomains.txt -k
```

### 8️⃣ 模糊测试（自定义位置）
```bash
gobuster fuzz -u https://example.com/FUZZ/admin.php -w /usr/share/wordlists/params.txt
```

### 9️⃣ 输出结果到文件
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -o result.txt
```

### 🔟 添加 Cookie 和请求头
```bash
gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt -H "Cookie: session=abc123" -H "User-Agent: Mozilla/5.0"
```

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
| **子域名（5000）** | `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt` |
| **子域名（20000）** | `/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt` |

---

## 🧩 输出结果解读

Gobuster 默认输出格式：
```
/wordpress (Status: 301) [Size: 169] [--> https://example.com/wordpress/]
/admin (Status: 403) [Size: 199]
/login.php (Status: 200) [Size: 2456]
```

| 字段 | 说明 |
|:---|:---|
| `Status` | HTTP 状态码（200=成功，301=重定向，403=禁止访问） |
| `Size` | 响应体大小（字节），可用于过滤假阳性 |
| `-->` | 重定向目标（状态码为 301/302 时显示） |

---

## 📌 Gobuster vs FFUF 对比

| 特性 | Gobuster | FFUF |
|:---|:---|:---|
| **目录爆破** | ✅ 支持 | ✅ 支持 |
| **DNS 爆破** | ✅ 支持 | ✅ 支持 |
| **虚拟主机枚举** | ✅ 支持 | ⚠️ 需自定义 |
| **速度** | 较快 | 更快 |
| **多字典支持** | ❌ 不支持 | ✅ 支持 |
| **扩展名支持** | ✅ 支持 | ✅ 支持 |
| **正则过滤** | ❌ 不支持 | ✅ 支持 |
| **JSON 输出** | ✅ 支持 | ✅ 支持 |

---

## 🚀 针对该目标（WordPress + WAF）的推荐命令

```bash
# 目录爆破（WordPress）
gobuster dir -u https://www.hagiwara-eg.co.jp/wordpress/ \
-w /usr/share/seclists/Discovery/Web-Content/CMS/wordpress.fuzz.txt \
-x php,html,txt \
-s "200,301,302,403" \
-k -a "Mozilla/5.0" -t 15 \
-o wp_gobuster.txt

# 子域名枚举
gobuster dns -d hagiwara-eg.co.jp \
-w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
-t 30 -i -o subdomains_gobuster.txt
```
