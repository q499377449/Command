`subfinder` 是一个强大的被动子域名收集工具，主要通过第三方API和证书透明度日志等渠道来发现子域名，不会直接向目标发送请求。它的常用参数可以按功能分类，方便你快速上手。

### 🎯 核心参数速查表

| 功能类别 | 参数 | 说明 | 示例 |
| :--- | :--- | :--- | :--- |
| **输入控制** | `-d`, `-domain` | 指定要查询的单个域名 | `subfinder -d example.com` |
| | `-dL`, `-list` | 从文件中读取多个域名，每行一个 | `subfinder -dL domains.txt` |
| **数据源管理** | `-all` | **启用所有**可用的数据源进行收集。结果最全，但速度会变慢 | `subfinder -d example.com -all` |
| | `-s`, `-sources` | 指定**只使用**某些数据源，用逗号分隔 | `subfinder -d example.com -s crtsh,github` |
| | `-es`, `-exclude-sources` | **排除**某些数据源，不用它们 | `subfinder -d example.com -es alienvault` |
| | `-ls`, `-list-sources` | 列出所有可用的数据源 | `subfinder -ls` |
| **输出选项** | `-o`, `-output` | 将结果保存到指定文件（纯文本） | `subfinder -d example.com -o result.txt` |
| | `-oJ`, `-json` | 以 **JSONL** 格式输出结果，便于程序处理 | `subfinder -d example.com -oJ -o result.json` |
| | `-oD`, `-output-dir` | 配合 `-dL` 使用，为每个域名单独保存结果到目录 | `subfinder -dL domains.txt -oD ./output/` |
| | `-cs`, `-collect-sources` | **仅配合 `-json` 使用**，在结果中标注每个子域名来自哪个数据源，方便溯源 | `subfinder -d example.com -oJ -cs` |
| **性能与调优** | `-t` | 设置并发解析的线程数（默认10），用于 `-active` 模式 | `subfinder -d example.com -t 50` |
| | `-rl`, `-rate-limit` | 限制每秒发出的HTTP请求总数，避免被API限流 | `subfinder -d example.com -rl 20` |
| | `-timeout` | 设置单次请求的超时秒数（默认30） | `subfinder -d example.com -timeout 60` |
| | `-max-time` | 设置整个枚举过程的最大等待分钟数（默认10） | `subfinder -d example.com -max-time 5` |
| **其他实用选项** | `-silent` | **静默模式**，只输出子域名，不显示Banner和进度信息，便于管道操作 | `subfinder -d example.com -silent` |
| | `-recursive` | 启用递归枚举，可以发现更深的子域名（如`dev.api.example.com`） | `subfinder -d example.com -recursive` |
| | `-nW`, `-active` | 主动解析并**移除通配符和无效子域名**，只输出存活的 | `subfinder -d example.com -nW` |

### 🚀 实用工作流示例

1.  **基础用法**：对单个域名进行枚举，并将结果保存。
    ```bash
    subfinder -d example.com -o subdomains.txt
    ```

2.  **批量处理**：对文件中的所有域名进行枚举，并为每个域名单独保存结果。
    ```bash
    subfinder -dL targets.txt -oD ./scan_results/
    ```

3.  **追求全面**：使用全部数据源，并将结果以JSON格式输出，同时标注来源。
    ```bash
    subfinder -d example.com -all -oJ -cs -o full_result.json
    ```

4.  **管道组合**：静默枚举后，通过管道传递给`httpx`工具进行存活探测。
    ```bash
    subfinder -d example.com -silent | httpx -silent -status-code
    ```

### 💡 重要提示

*   **数据源配置**：`subfinder` 的强大依赖于第三方API。它默认的配置文件位于 `~/.config/subfinder/provider-config.yaml`。你可以在这个文件中添加 Shodan、Censys、VirusTotal、SecurityTrails 等服务的API密钥，以获得更全面的结果。
*   **被动模式**：默认情况下，`subfinder` 工作于**被动模式**，这意味着它不会主动向目标发送网络请求，隐蔽性极高。
