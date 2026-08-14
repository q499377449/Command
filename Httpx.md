好的，我直接给你整理一份 **httpx 常用参数速查表**，基于你用的 v1.10.0 版本，按功能分类方便查阅：

---

## 🎯 核心探测参数（最常用）

| 参数 | 作用 | 示例 |
|------|------|------|
| `-status-code` | 显示 HTTP 状态码 | `httpx -l urls.txt -status-code` |
| `-title` | 提取网页标题 | `httpx -l urls.txt -title` |
| `-server` | 显示服务器软件（如 nginx） | `httpx -l urls.txt -server` |
| `-content-length` | 显示响应内容大小 | `httpx -l urls.txt -content-length` |
| `-tech-detect` | 识别网站技术栈（基于 Wappalyzer） | `httpx -l urls.txt -tech-detect` |
| `-ip` | 显示解析的 IP 地址 | `httpx -l urls.txt -ip` |
| `-cdn` | 检测是否使用 CDN | `httpx -l urls.txt -cdn` |
| `-cname` | 显示 CNAME 记录 | `httpx -l urls.txt -cname` |
| `-favicon` | 计算 favicon.ico 的 mmh3 哈希值（指纹识别） | `httpx -l urls.txt -favicon` |

---

## ⚙️ 过滤与匹配参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `-mc` | 匹配指定的状态码（保留结果） | `-mc 200,302` |
| `-fc` | 过滤掉指定的状态码 | `-fc 404,403,500` |
| `-ml` | 匹配响应内容长度范围 | `-ml 1000-5000` |
| `-fl` | 过滤掉指定响应长度 | `-fl 0,100` |
| `-ms` | 匹配响应中包含的字符串 | `-ms "Login"` |
| `-fe` | 排除包含指定字符串的响应 | `-fe "Error"` |

---

## 🚀 性能与输入输出参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `-l` 或 `-list` | 从文件读取目标列表 | `httpx -l targets.txt` |
| `-t` | 设置并发线程数（默认 50） | `httpx -l urls.txt -t 100` |
| `-rl` | 限制每秒请求数（默认 150） | `httpx -l urls.txt -rl 50` |
| `-timeout` | 设置超时秒数（默认 5） | `httpx -l urls.txt -timeout 10` |
| `-retries` | 失败重试次数（默认 1） | `httpx -l urls.txt -retries 2` |
| `-o` | 将结果保存到文件 | `httpx -l urls.txt -o result.txt` |
| `-json` | 以 JSONL 格式输出 | `httpx -l urls.txt -json -o result.json` |
| `-silent` | 静默模式，只输出结果 | `httpx -l urls.txt -silent` |

---

## 🔧 端口与协议参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `-ports` | 指定扫描端口（默认 80,443） | `httpx -l domains.txt -ports 80,443,8080,8443` |
| `-path` | 指定探测路径 | `httpx -l domains.txt -path /admin` |
| `-method` | 指定 HTTP 方法（默认 GET） | `httpx -l urls.txt -method POST` |
| `-request` | 使用自定义 HTTP 请求文件 | `httpx -request req.txt` |
| `-follow-redirects` | 跟随重定向 | `httpx -l urls.txt -follow-redirects` |
| `-max-redirects` | 最大重定向次数（默认 10） | `httpx -l urls.txt -follow-redirects -max-redirects 5` |

---

## 🛡️ 网络与代理参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `-proxy` | 使用代理（支持 http/socks5） | `httpx -l urls.txt -proxy http://127.0.0.1:8080` |
| `-resolvers` | 指定自定义 DNS 解析器 | `httpx -l urls.txt -resolvers resolvers.txt` |
| `-no-color` | 禁用颜色输出 | `httpx -l urls.txt -no-color` |

---

## 💡 实战工作流示例

### 1. 基础存活探测
```bash
httpx -l domains.txt -status-code -title -server -tech-detect -o result.txt
```

### 2. 配合 subfinder 管道处理
```bash
subfinder -d example.com -silent | httpx -status-code -title -ip -cdn
```

### 3. 精准筛选 + 多端口
```bash
httpx -l domains.txt -ports 80,443,8080,8443 -mc 200,302 -fc 404,403 -status-code -title
```

### 4. JSON 输出 + 完整指纹
```bash
httpx -l urls.txt -json -status-code -title -server -tech-detect -ip -cdn -favicon -o full_result.json
```

### 5. 低速率避免封禁
```bash
httpx -l urls.txt -t 20 -rl 30 -timeout 10 -status-code -title
```

---

## 📌 v1.10.0 特别提醒

1. **`-web-server` 已弃用**，改用 `-server`
2. **默认只扫 80/443**，需要其他端口请用 `-ports` 指定
3. **默认不跟随重定向**，如需跟踪请加 `-follow-redirects`
4. **速率控制**：生产环境建议 `-t 30 -rl 50`，避免触发 WAF

---

如果你有**具体场景**（比如配合 nuclei、扫描 API 路径、或者批量检测某个端口），告诉我，我再给你针对性配置！
