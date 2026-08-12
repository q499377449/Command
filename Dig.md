好的，整理一份 `dig` 的**常用命令和参数速查表**，这是 DNS 信息收集阶段最核心的工具。

---

## 📖 Dig 基础语法

```bash
dig [@DNS服务器] [目标域名] [查询类型] [选项]
```

---

## 🔧 核心查询类型

| 类型 | 说明 | 用途 |
|:---|:---|:---|
| `A` | IPv4 地址（默认） | 获取域名解析的 IP |
| `AAAA` | IPv6 地址 | 获取 IPv6 地址 |
| `CNAME` | 别名记录 | 查看域名是否有别名（如 CDN） |
| `MX` | 邮件交换记录 | 获取邮件服务器地址 |
| `NS` | 域名服务器记录 | 获取 DNS 服务器列表 |
| `TXT` | 文本记录 | 获取 SPF、DKIM、验证信息 |
| `SOA` | 授权起始记录 | 获取 DNS 区域信息 |
| `ANY` | 所有记录 | 获取所有 DNS 记录（通常被限制） |
| `AXFR` | 区域传输 | 尝试获取整个 DNS 区域（高风险） |

---

## 📂 常用命令示例

### 1️⃣ 基本查询（默认 A 记录）
```bash
dig target.com
```

### 2️⃣ 查询特定类型的记录
```bash
# IPv4 地址
dig target.com A

# IPv6 地址
dig target.com AAAA

# CNAME 别名
dig target.com CNAME

# MX 邮件记录
dig target.com MX

# NS 域名服务器
dig target.com NS

# TXT 文本记录
dig target.com TXT

# SOA 授权起始记录
dig target.com SOA

# 所有记录
dig target.com ANY
```

### 3️⃣ 使用特定 DNS 服务器
```bash
# 使用 Google DNS
dig @8.8.8.8 target.com

# 使用 Cloudflare DNS
dig @1.1.1.1 target.com

# 使用本地 DNS
dig @127.0.0.1 target.com

# 使用目标域名的 NS（用于 AXFR）
dig @ns1.target.com target.com AXFR
```

### 4️⃣ 反向查询（PTR 记录）
```bash
# 根据 IP 查询域名
dig -x 8.8.8.8

# 使用特定 DNS 服务器
dig @8.8.8.8 -x 192.168.1.1
```

### 5️⃣ 区域传输（AXFR）
```bash
# 尝试从主 DNS 获取所有记录
dig @ns1.target.com target.com AXFR

# 自动查找可用 NS 并尝试 AXFR
dig target.com AXFR
```

### 6️⃣ 跟踪 DNS 解析路径
```bash
# 显示解析过程（从根服务器开始）
dig target.com +trace
```

### 7️⃣ 批量查询（多个域名）
```bash
# 从文件读取域名列表
dig -f domains.txt

# 从文件读取并查询特定类型
dig -f domains.txt MX
```

---

## ⚙️ 输出控制选项

| 选项 | 说明 | 示例 |
|:---|:---|:---|
| `+short` | 仅显示简要结果（最常用） | `dig target.com +short` |
| `+noall` | 不显示任何内容 | `dig target.com +noall` |
| `+answer` | 仅显示回答部分 | `dig target.com +noall +answer` |
| `+authority` | 仅显示权威部分 | `dig target.com +noall +authority` |
| `+additional` | 仅显示额外部分 | `dig target.com +noall +additional` |
| `+stats` | 显示统计信息 | `dig target.com +stats` |
| `+nsid` | 显示名称服务器标识 | `dig target.com +nsid` |
| `+ttlid` | 显示 TTL 值 | `dig target.com +ttlid` |
| `+multiline` | 多行格式输出 | `dig target.com +multiline` |
| `+timeout` | 设置超时时间（秒） | `dig target.com +timeout=5` |
| `+tries` | 设置重试次数 | `dig target.com +tries=2` |

---

## 🧪 实战组合命令

### 1️⃣ 快速获取网站 IP
```bash
dig target.com +short
```

### 2️⃣ 获取完整 DNS 记录（简洁输出）
```bash
dig target.com +noall +answer ANY
```

### 3️⃣ 检查是否使用 CDN（查看 CNAME）
```bash
dig target.com CNAME +short
```

### 4️⃣ 获取邮件服务器优先级
```bash
dig target.com MX +short
```

### 5️⃣ 获取 SPF 和 DKIM 记录
```bash
dig target.com TXT +short
```

### 6️⃣ 子域名枚举（使用 NS 字典爆破）
```bash
# 手动测试单个子域名
dig admin.target.com A +short

# 批量测试
for sub in www mail admin ftp blog dev api; do
    echo -n "$sub: "
    dig $sub.target.com A +short
done
```

### 7️⃣ 完整信息收集（一次性输出）
```bash
dig target.com ANY +noall +answer
```

### 8️⃣ 使用 DNS 服务器并查看详细信息
```bash
dig @8.8.8.8 target.com ANY +multiline
```

---

## 📋 快速参考卡

| 场景 | 命令 |
|:---|:---|
| **查 IP** | `dig target.com +short` |
| **查所有记录** | `dig target.com ANY` |
| **查 MX 邮件服务器** | `dig target.com MX +short` |
| **查 NS 服务器** | `dig target.com NS +short` |
| **查 CNAME（CDN）** | `dig target.com CNAME +short` |
| **查 TXT（SPF/DKIM）** | `dig target.com TXT +short` |
| **查 SOA** | `dig target.com SOA +short` |
| **反向查询（IP→域名）** | `dig -x 8.8.8.8 +short` |
| **区域传输** | `dig @ns1.target.com target.com AXFR` |
| **使用 Google DNS** | `dig @8.8.8.8 target.com` |
| **跟踪解析路径** | `dig target.com +trace` |
| **批量查询** | `dig -f domains.txt` |

---

## 🎯 渗透测试中的常见用法

### 信息收集阶段
```bash
# 1. 获取网站所有公开 DNS 记录
dig target.com ANY +noall +answer

# 2. 尝试区域传输（高危，但偶尔成功）
dig @ns1.target.com target.com AXFR
dig @ns2.target.com target.com AXFR

# 3. 子域名发现（配合字典）
for sub in $(cat /usr/share/wordlists/subdomains.txt); do
    dig $sub.target.com +short
done

# 4. 检查是否使用云服务（查看 CNAME）
dig target.com CNAME +short
```

### 漏洞利用阶段
```bash
# 1. 检查是否存在域传送漏洞
dig @8.8.8.8 target.com AXFR

# 2. 获取邮件服务器用于钓鱼测试
dig target.com MX +short
```

---

## ⚠️ 注意事项

| 问题 | 建议 |
|:---|:---|
| **速率限制** | 不要使用 DNS 服务器的高并发查询，避免被限流 |
| **区域传输** | 现代 DNS 服务器已禁用，但偶尔成功，值得尝试 |
| **ANY 查询** | 很多 DNS 服务器限制 ANY 查询，建议用具体类型替代 |
| **通配符 DNS** | 如果 `*.target.com` 都解析到同一 IP，说明有通配符，子域名爆破会大量误报 |
| **CDN** | 如果 CNAME 指向 `cloudfront.net`、`fastly.net` 等，IP 可能是 CDN 的，不是真实服务器 |

---

## 📌 配合其他工具使用

```bash
# 1. 用 dig 确认子域名，再用 ffuf 扫描
dig sub.target.com +short
ffuf -u https://sub.target.com/FUZZ -w /path/wordlist

# 2. 用 dig 获取 NS，再尝试 AXFR
dig target.com NS +short
dig @$(dig target.com NS +short | head -1) target.com AXFR

# 3. 结合 whatweb 验证
dig target.com +short | while read ip; do
    whatweb http://$ip 2>/dev/null
done
```
