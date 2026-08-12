# Nuclei 常用命令速查（精简版）

## 一、最常用命令（日常90%场景）

### 1. 快速扫描WordPress
```bash
nuclei -u http://目标.com -tags wordpress -severity high,critical -stats
```

### 2. 生成扫描报告
```bash
nuclei -u http://目标.com -tags wordpress -severity high,critical -o result.txt
```

### 3. 完整扫描（推荐）
```bash
nuclei -u http://目标.com -t /root/.local/nuclei-templates/ -severity low,medium,high,critical -stats -o result.txt
```

---

## 二、参数速查表

| 参数 | 作用 | 常用值 |
|------|------|--------|
| `-u` | 目标URL | `http://example.com` |
| `-tags` | 按标签筛选 | `wordpress`, `cve`, `xss` |
| `-severity` | 严重程度 | `high,critical` / `low,medium,high,critical` |
| `-o` | 输出文件 | `result.txt` |
| `-stats` | 显示统计 | 不加值 |
| `-json` | JSON格式 | 不加值 |
| `-rl` | 每秒请求限制 | `10`（防WAF） |
| `-c` | 并发数 | `5`（防WAF） |

---

## 三、三种常用场景

### 场景1：快速检查（1-2分钟）
```bash
nuclei -u http://目标.com -tags wordpress -severity critical
```

### 场景2：标准扫描（5-10分钟）
```bash
nuclei -u http://目标.com -tags wordpress -severity high,critical -stats
```

### 场景3：全面扫描（15-30分钟）
```bash
nuclei -u http://目标.com -t /root/.local/nuclei-templates/ -severity low,medium,high,critical -stats -o full-scan.txt
```

---

## 四、针对你的目标的命令

```bash
# 快速扫描
nuclei -u http://43.163.121.175 -tags wordpress -severity high,critical -stats

# 完整扫描并保存
nuclei -u http://43.163.121.175 -tags wordpress,cve -severity low,medium,high,critical -stats -o nuclei-result.txt
```

---

## 五、记住这5个就够了！

| # | 用途 | 命令 |
|---|------|------|
| 1 | **快速扫描** | `nuclei -u URL -tags wordpress -severity critical` |
| 2 | **标准扫描** | `nuclei -u URL -tags wordpress -severity high,critical -stats` |
| 3 | **完整扫描** | `nuclei -u URL -t /root/.local/nuclei-templates/ -severity low,medium,high,critical -stats` |
| 4 | **保存结果** | 加 `-o 文件名.txt` |
| 5 | **更新模板** | `nuclei -update-templates` |

---

## 六、常用标签

| 标签 | 用途 |
|------|------|
| `wordpress` | WordPress漏洞 |
| `wp-plugin` | WordPress插件漏洞 |
| `cve` | CVE漏洞 |
| `xss` | XSS漏洞 |
| `sql` | SQL注入 |

---

## 七、防WAF配置

```bash
nuclei -u http://目标.com -rl 10 -c 5 -tags wordpress
```
- `-rl 10` = 每秒10个请求
- `-c 5` = 5个并发

---

**记住：日常就用 `-tags wordpress -severity high,critical -stats`，90%的情况够用了！** 🚀
