`amass` 的常用操作主要通过 `enum` 子命令完成，其核心参数能帮你灵活控制**被动/主动**扫描模式、数据源、爆破字典和性能。

我将常用参数整理成了下面的速查表，方便你快速查阅：

### 🎯 核心参数速查表

| 功能类别 | 参数 | 说明 | 示例 |
| :--- | :--- | :--- | :--- |
| **目标指定** | `-d` | 指定单个目标域名 | `amass enum -d example.com` |
| | `-df` | 从文件中读取多个目标域名（一行一个） | `amass enum -df domains.txt` |
| **扫描模式** | `-passive` | **纯被动模式**，只从公开数据源收集，不直接接触目标，是最隐蔽的方式 | `amass enum -passive -d example.com` |
| | `-active` | **主动模式**，会进行DNS查询、尝试域传送等，能发现更多资产，但可能触发告警 | `amass enum -active -d example.com` |
| | `-brute` | **启用字典爆破**，用组合猜测的方式发现子域名，通常和`-w`一起使用 | `amass enum -brute -d example.com` |
| **数据源管理** | `-include` | 指定**只使用**某些数据源 | `amass enum -include crtsh -d example.com` |
| | `-exclude` | **排除**某些数据源 | `amass enum -exclude crtsh -d example.com` |
| | `-list` | 列出所有可用的数据源名称 | `amass enum -list` |
| **结果输出** | `-o` | 将结果保存到文本文件 | `amass enum -o results.txt -d example.com` |
| | `-oA` | 将结果输出到所有格式的文件（如TXT, JSON），并指定文件前缀 | `amass enum -oA scan_output -d example.com` |
| | `-ip` | 在结果中同时显示解析出的IP地址 | `amass enum -ip -d example.com` |
| **性能调优** | `-timeout` | 设置扫描的最大运行时间（单位：分钟），避免任务卡死 | `amass enum -timeout 30 -d example.com` |
| | `-w` | 指定自定义的爆破字典文件路径 | `amass enum -brute -w /path/to/wordlist.txt -d example.com` |
| | `-r` | 指定自定义的DNS解析器列表，可以提高枚举速度 | `amass enum -r 8.8.8.8,1.1.1.1 -d example.com` |

---

### 💡 实战命令组合

*   **基础信息收集（隐匿）**：最常用，从证书透明度日志等公开渠道获取子域名，不触碰目标。
    ```bash
    amass enum -passive -d example.com
    ```

*   **全面扫描（可能触发告警）**：结合DNS主动探测和字典爆破，发现更多深层次子域名。
    ```bash
    amass enum -active -brute -d example.com
    ```

*   **自定义数据源与输出**：仅使用指定数据源（如`crtsh`），并输出带IP地址的结果文件。
    ```bash
    amass enum -include crtsh -d example.com -ip -o result_with_ip.txt
    ```

> **请注意**：`-active` 和 `-brute` 模式会产生大量的DNS查询，可能导致你的IP被目标或网络服务商暂时限制。建议在获得授权后进行，并可配合 `-r` 参数使用公共DNS服务器来分散请求。

关于 `amass` 还有一个很有用的`viz`子命令，可以用来生成子域名的可视化关系图。需要我为你展开讲讲这个用法吗？
