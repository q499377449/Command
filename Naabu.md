`naabu` 是 ProjectDiscovery 开发的一款高速端口扫描工具，常用于信息收集阶段，以快速发现目标主机的开放端口。它的核心参数和常用命令可以整理如下。

### 🎯 常用参数速查表

我将 `naabu` 的常用参数按功能整理成了表格，方便你查阅。

| 功能类别 | 参数 | 说明 | 示例 |
| :--- | :--- | :--- | :--- |
| **输入控制** | `-host` | 指定单个目标主机或IP | `naabu -host example.com` |
| | `-list, -l` | 从文件读取目标主机列表（每行一个） | `naabu -l hosts.txt` |
| **端口选择** | `-p, -port` | 指定要扫描的端口（支持单个、列表或范围） | `naabu -p 80,443,8080` 或 `-p 1-1000` |
| | `-top-ports` | 扫描最常见的前N个端口（如 `100`, `1000`, `full`），默认100 | `naabu -top-ports 1000` |
| | `-exclude-ports` | 从扫描中排除特定端口 | `naabu -p - -exclude-ports 22,3389` |
| **扫描类型** | `-scan-type, -s` | 扫描类型，`syn`（默认，需root）或 `connect`（无特权时使用） | `naabu -scan-type c` |
| **速率控制** | `-rate` | 每秒发送的数据包数量，默认1000 | `naabu -rate 300` |
| | `-c` | 内部工作线程数，默认25 | `naabu -c 50` |
| **性能调优** | `-timeout` | 单次探测的超时时间（**单位：毫秒**），默认1000ms | `naabu -timeout 1000` |
| | `-retries` | 端口扫描失败时的重试次数，默认3次 | `naabu -retries 1` |
| | `-Pn` | **跳过主机发现**，直接进行端口扫描，可提高速度 | `naabu -list hosts.txt -Pn` |
| **输出与验证** | `-o, -output` | 将结果保存到文件 | `naabu -o result.txt` |
| | `-json` | 以JSON Lines格式输出结果 | `naabu -host example.com -json` |
| | `-silent` | 静默模式，仅显示结果，不展示Banner和进度信息 | `naabu -host example.com -silent` |
| | `-verify` | **验证端口**，对发现的开放端口再次进行TCP连接确认，确保准确性 | `naabu -host example.com -verify` |
| **其他** | `-exclude-cdn, -ec` | 跳过对CDN/WAF资产的完整端口扫描，仅检查80和443端口 | `naabu -list hosts.txt -exclude-cdn` |
| | `-display-cdn, -cdn` | 显示目标是否使用了CDN | `naabu -host example.com -cdn` |

### 💡 常用命令示例

**1. 基础扫描**
对单个目标进行默认的Top 100端口扫描。
```bash
naabu -host example.com
```

**2. 指定端口扫描**
扫描特定端口或端口范围。
```bash
# 扫描特定端口
naabu -host example.com -p 80,443,8080,8443

# 扫描端口范围
naabu -host example.com -p 1-1000
```

**3. 批量扫描**
从文件中读取目标列表进行扫描。
```bash
naabu -list hosts.txt -top-ports 1000
```

**4. 自动化友好型扫描（推荐）**
一个配置了常用优化参数、适合集成到自动化脚本的示例。
```bash
naabu -list hosts.txt -top-ports 100 -scan-type c -Pn -rate 300 -c 25 -timeout 1000 -retries 1 -verify -silent -json -o naabu_results.jsonl
```

### ⚠️ 注意事项
*   **权限问题**：默认的 `syn` 扫描需要 `root` 或管理员权限。如果没有，需使用 `-scan-type connect` 参数。
*   **数值单位**：`-timeout` 参数的单位是**毫秒**，不是秒，设置时需注意。
*   **扫描策略**：进行大范围扫描时，应先使用较小的端口集合或较低的`-rate`/`-c`值，再逐步调整，避免网络不稳定或触发告警。

如果你有特定的扫描场景（例如，需要扫描UDP端口或集成到其他工具链），可以告诉我，我再为你提供更具体的用法。
