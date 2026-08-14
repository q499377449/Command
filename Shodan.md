Shodan 的核心能力就是**服务、端口、设备**这三个维度的信息收集。我帮你系统梳理一下，如何用 Shodan 在这三个方面做信息收集。

---

## 🎯 三大核心维度

### 1. 端口探测
Shodan 会扫描公网 IP 的**常用端口**（如 22, 80, 443, 3306, 3389, 8080 等），记录哪些端口是开放的，并识别其上运行的服务。

**常用搜索语法：**
```bash
# 查找开放特定端口的资产
port:22                    # SSH 服务
port:3306                  # MySQL 数据库
port:3389                  # Windows 远程桌面
port:8888                  # 常用代理/面板端口

# 组合端口搜索
port:80,443,8080          # 开放了 80 或 443 或 8080 的资产
port:22 port:443          # 同时开放 22 和 443 的资产（AND 逻辑）
```

---

### 2. 服务指纹识别
Shodan 会抓取每个服务的**Banner 信息**（服务返回的特定文本），提取出服务名称、产品、版本、操作系统等指纹信息。

**常用搜索语法：**
```bash
# 按产品/服务类型过滤
product:"Apache httpd"               # Apache 服务器
product:"nginx"                      # Nginx 服务器
product:"MySQL"                      # MySQL 数据库
product:"OpenSSH"                    # SSH 服务
product:"Microsoft IIS httpd"        # IIS 服务器

# 按版本精准搜索
product:"Apache" version:"2.4.49"    # 特定版本的 Apache
product:"nginx" version:"1.14.0"

# 按操作系统搜索
os:"Windows"                         # Windows 系统
os:"Linux"                           # Linux 系统
os:"Windows Server 2012"
```

---

### 3. 设备类型识别
Shodan 通过综合指纹，可以判断设备的具体类型（摄像头、路由器、工控设备、打印机等）。

**常用搜索语法：**
```bash
# 按设备类型搜索
device_type:"camera"                 # 摄像头
device_type:"router"                 # 路由器
device_type:"firewall"               # 防火墙
device_type:"printer"                # 打印机
device_type:"phone"                  # 电话/VoIP 设备

# 直接搜索特定设备品牌/型号
"webcam" "Hikvision"                # 海康威视摄像头
"router" "Cisco"                    # 思科路由器
"print server" "HP"                 # HP 打印服务器
```

---

## 💡 实战组合搜索示例

### 基础信息收集
```bash
# 1. 查找目标组织的公网资产
org:"目标公司名称"
domain:"example.com"

# 2. 查找特定地域的 Web 服务
country:"CN" port:"443" product:"nginx"

# 3. 查找暴露的高危服务（如数据库）
port:"3306" product:"MySQL" country:"CN"

# 4. 查找特定漏洞影响面
vuln:CVE-2021-44228                    # Log4j 漏洞
vuln:CVE-2017-5638                     # Struts2 漏洞
```

### 典型场景组合
```bash
# 查找某公司暴露的远程桌面
org:"Alibaba" port:"3389"

# 查找北京地区的摄像头
city:"Beijing" device_type:"camera"

# 查找中国境内的工控系统
port:"502" country:"CN"                # Modbus 协议
port:"1883" "MQTT" country:"CN"        # MQTT 物联网协议

# 查找暴露的 Web 管理后台
http.title:"phpMyAdmin"
http.title:"Dashboard" "Jenkins"
http.title:"管理后台" country:"CN"

# 查找证书过期的 HTTPS 服务
ssl.cert.expired:"true" country:"CN"

# 查找使用特定 SSL 证书的资产（关联同一实体）
ssl.cert.serial:"证书序列号"
```

---

## 🔧 常用过滤器和运算符

| 过滤器 | 作用 | 示例 |
|--------|------|------|
| `port:` | 指定端口 | `port:443` |
| `product:` | 服务/产品名称 | `product:"Apache"` |
| `version:` | 版本号 | `version:"2.4.49"` |
| `os:` | 操作系统 | `os:"Windows"` |
| `device_type:` | 设备类型 | `device_type:"camera"` |
| `org:` | 所属组织 | `org:"Alibaba Cloud"` |
| `domain:` | 域名 | `domain:"example.com"` |
| `hostname:` | 主机名 | `hostname:"*.example.com"` |
| `country:` | 国家代码 | `country:"CN"` |
| `city:` | 城市名 | `city:"Beijing"` |
| `vuln:` | 漏洞编号 | `vuln:CVE-2021-44228` |
| `ssl.cert.expired:` | 证书是否过期 | `ssl.cert.expired:"true"` |
| `http.title:` | HTTP 标题 | `http.title:"Login"` |
| `http.html:` | HTTP 页面内容 | `http.html:"password"` |

**逻辑运算符：** `+`（AND）、空格（OR）、`-`（NOT）
示例：`port:443 + country:CN - product:"nginx"`

---

## 🚀 自动化工具集成

通过 Shodan API，可以集成到自动化信息收集流程中：

### Python 示例（使用 shodan 库）
```python
import shodan

api = shodan.Shodan("YOUR_API_KEY")
results = api.search('org:"Alibaba" port:443', limit=100)

for result in results['matches']:
    print(f"IP: {result['ip_str']}")
    print(f"Port: {result['port']}")
    print(f"Product: {result.get('product', 'Unknown')}")
    print(f"Org: {result.get('org', 'Unknown')}")
    print("-" * 40)
```

### 配合其他工具
```bash
# Shodan + httpx 组合
# 先用 Shodan 获取 IP 列表，再用 httpx 进行 Web 探测
shodan download --limit 1000 "org:Alibaba port:443" alibaba_assets.csv
```

---

## ⚠️ 注意事项

1. **API 限制**：免费版 Shodan 有查询次数和结果数量限制，大规模查询需要付费订阅。
2. **数据更新延迟**：Shodan 不是实时更新的，资产数据可能有数天到数周的延迟。
3. **IPv6 支持有限**：Shodan 主要覆盖 IPv4 空间，IPv6 资产相对较少。
4. **合法合规**：仅对获得授权的目标进行查询，避免触碰法律红线。

---

如果你有具体场景（比如“查找某公司的所有暴露资产”），我可以给你一组完整的搜索语法组合。需要吗？
