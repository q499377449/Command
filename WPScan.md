# WPScan 常用命令速查（精简版）

## 一、最常用命令（日常90%场景）

### 1. 快速安全扫描
```bash
wpscan --url http://目标.com --enumerate vp,vt,u
```
- `vp` = 有漏洞的插件
- `vt` = 有漏洞的主题  
- `u` = 用户枚举

### 2. 完整审计（推荐）
```bash
wpscan --url http://目标.com \
  --enumerate vp,vt,u \
  --api-token 你的TOKEN \
  --plugins-detection aggressive
```

### 3. 生成报告
```bash
wpscan --url http://目标.com \
  --enumerate vp,vt,u \
  --format json \
  --output scan.json
```

---

## 二、参数速查表

| 参数 | 作用 | 常用值 |
|------|------|--------|
| `--url` | 目标网址 | 必需 |
| `--enumerate` | 枚举内容 | `vp,vt,u` |
| `--api-token` | 获取完整漏洞详情 | 免费注册获取 |
| `--plugins-detection` | 插件检测方式 | `aggressive`（主动）/ `passive`（被动） |
| `--format` | 输出格式 | `json` / `markdown` |
| `--output` | 保存结果 | 文件名 |
| `--throttle` | 请求延迟(ms) | `1000`（慢速）/ `0`（快速） |

---

## 三、三种常用场景

### 场景1：快速检查（3-5分钟）
```bash
wpscan --url http://目标.com --enumerate vp,vt
```

### 场景2：标准扫描（15-20分钟）
```bash
wpscan --url http://目标.com --enumerate vp,vt,u
```

### 场景3：深度审计（25-40分钟）
```bash
wpscan --url http://目标.com \
  --enumerate vp,vt,u \
  --api-token 你的TOKEN \
  --plugins-detection aggressive \
  --wp-version-all
```

---

## 四、你的场景专用命令

针对当前扫描目标：
```bash
wpscan --url http://43.163.121.175 \
  --enumerate vp,vt,u \
  --api-token XWSsQ2fmaViX1NflIzxa5TtDCNqx9PSU2sF9qMtF6pA \
  --plugins-detection aggressive
```

---

## 五、记住这5个就够了！

| # | 用途 | 命令 |
|---|------|------|
| 1 | **快速扫描** | `wpscan --url URL --enumerate vp,vt` |
| 2 | **标准扫描** | `wpscan --url URL --enumerate vp,vt,u` |
| 3 | **深度扫描** | 加 `--api-token TOKEN --plugins-detection aggressive` |
| 4 | **爆破密码** | `wpscan --url URL --username admin --passwords 密码字典` |
| 5 | **输出报告** | 加 `--format json --output 文件名.json` |

---

## 六、常用参数解释（一句话版）

- `vp` - 只看有漏洞的插件（少看垃圾信息）
- `vt` - 只看有漏洞的主题
- `u` - 查有哪些用户（找admin）
- `aggressive` - 暴力扫描，能找到更多东西（但慢）
- `passive` - 温柔扫描，不触发警报（但可能漏报）
- `api-token` - 免费注册，能看到CVE编号和详细漏洞信息

---

**记住：日常就用 `--enumerate vp,vt,u` + `--plugins-detection aggressive`，90%的情况够用了！**
