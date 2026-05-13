# ChatPPT CLI 命令文档

本文档详细描述 ChatPPT CLI 工具的所有命令及其使用方法。

## 目录

* [全局标志](#全局标志)
* [认证命令](#认证命令)

  * [auth login](#auth-login)
  * [auth status](#auth-status)
  * [auth logout](#auth-logout)
  * [auth refresh](#auth-refresh)
* [PPT 命令](#ppt-命令)

  * [ppt generate](#ppt-generate)
  * [ppt generate\_banana](#ppt-generate_banana)
  * [ppt import\_file](#ppt-import_file)
  * [ppt import\_file\_banana](#ppt-import_file_banana)
  * [ppt result](#ppt-result)
* [配置命令](#配置命令)

  * [config show](#config-show)
  * [config reload](#config-reload)
* [版本命令](#版本命令)

  * [version show](#version-show)
  * [version check](#version-check)
  * [version update](#version-update)
* [开发命令](#开发命令)

  * [构建命令](#构建命令)
  * [测试命令](#测试命令)

\---

## 全局标志

以下标志可用于所有命令：

|标志|简写|默认值|说明|
|-|-|-|-|
|`--config`|||配置文件路径（默认：`$HOME/.chatppt/config.yaml`）|
|`--debug`|`-d`|`false`|启用调试模式，输出详细日志|
|`--format`|`-f`|`json`|输出格式（`json`/`table`/`pretty`）|

\---

## 认证命令

### auth login

登录到 ChatPPT 服务。

**语法：**

```bash
chatppt auth login \\\[flags]
```

**用途：**
通过设备码流程登录到 ChatPPT 服务，凭证将安全地存储在系统密钥链中。

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--open`|`-o`|`true`|自动打开浏览器进行授权|

**使用示例：**

```bash
# 默认登录（自动打开浏览器）
chatppt auth login

# 不自动打开浏览器
chatppt auth login --open=false

# 启用调试模式
chatppt auth login --debug
```

**预期输出：**

```
========================================
  登录到 ChatPPT 服务
========================================

ℹ 正在启动登录流程...
✓ 已获取设备码

⚠ 请在浏览器中完成授权：
  验证地址: https://chatppt.com/auth/device
  设备码: XXXX-XXXX

✓ 已自动打开浏览器
ℹ 等待授权中...

✓ 授权成功！
✓ 登录成功！凭证已保存到系统密钥链。
  用户: 微信用户
  手机号: 150\\\*\\\*\\\*\\\*\\\*\\\*\\\*\\\*
```

**注意事项：**

* 首次登录需要在浏览器中完成授权
* 凭证会自动保存到系统密钥链，无需重复登录
* 如果浏览器未自动打开，请手动复制验证地址

\---

### auth status

查看当前登录状态。

**语法：**

```bash
chatppt auth status
```

**用途：**
显示当前用户的登录状态、用户信息和凭证有效期。

**使用示例：**

```bash
chatppt auth status
```

**预期输出（已登录）：**

```
========================================
  认证状态
========================================

✓ 已登录
  用户: 微信用户
  手机号: 150\\\*\\\*\\\*\\\*XXX
  ID: 8888
```

**预期输出（未登录）：**

```
========================================
  认证状态
========================================

⚠ 未登录
ℹ 请执行 "chatppt auth login" 登录
```

\---

### auth logout

退出登录。

**语法：**

```bash
chatppt auth logout
```

**用途：**
清除本地保存的登录凭证，退出当前账号。

**使用示例：**

```bash
chatppt auth logout
```

**预期输出：**

```
========================================
  退出登录
========================================

✓ 已退出登录
ℹ 本地凭证已清除
```

**注意事项：**

* 退出后需要重新登录才能使用需要认证的命令
* 仅清除本地凭证，不会影响其他设备的登录状态

\---

### auth refresh

刷新访问令牌。

**语法：**

```bash
chatppt auth refresh
```

**用途：**
手动刷新访问令牌，延长登录有效期。

**使用示例：**

```bash
chatppt auth refresh
```

**预期输出：**

```
========================================
  刷新访问令牌
========================================

✓ 令牌刷新成功！
```

**注意事项：**

* 令牌通常会自动刷新，此命令用于手动刷新
* 需要在令牌过期前执行

\---

## PPT 命令

### ppt generate

根据主题文本生成 PPT。

**语法：**

```bash
chatppt ppt generate \\\[主题文本] \\\[flags]
```

**用途：**
使用 AI 根据主题文本自动生成 PPT 演示文稿。

**参数：**

|参数|必填|说明|
|-|-|-|
|`主题文本`|是|PPT 主题描述，长度 1-200 字符|

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--complex`||`0`|复杂度参数（固定值 0）|
|`--language`|||语言设置（如 `zh`、`en`）|
|`--font-name`|||字体名称|
|`--theme-color`|||主题颜色|
|`--user-name`|||用户名|
|`--ai-picture`||`true`|AI 图片生成开关|
|`--image-style`|||图片风格|
|`--custom-template-id`|||自定义模板 ID|
|`--custom-page-count`||`0`|自定义页数|
|`--web-search`||`true`|网络搜索开关|
|`--output`|`-o`|`json`|输出格式（`json`/`pretty`）|
|`--poll`||`true`|启用轮询模式，生成后自动查询结果|
|`--interval`||`5`|轮询间隔（秒）|
|`--max-retries`||`60`|最大轮询次数|

**使用示例：**

```bash
# 基础用法
chatppt ppt generate "生成一个地震ppt"

# 带可选参数
chatppt ppt generate "人工智能发展趋势" \\\\
  --language zh \\\\
  --font-name "微软雅黑" \\\\
  --theme-color "blue"

# 使用 pretty 格式输出
chatppt ppt generate "年度总结" -o pretty

# 禁用轮询
chatppt ppt generate "产品介绍" --poll=false
```

**预期输出：**

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "ppt\\\_id": "ppt\\\_1234567890",
    "request\\\_id": "req\\\_abcdef123456"
  }
}
```

**注意事项：**

* 主题文本长度限制为 1-200 字符
* 默认启用轮询模式，生成完成后会自动查询结果
* 复杂度和 AI 图片参数有固定值限制

\---

### ppt generate\_banana

生成 Banana PPT。

**语法：**

```bash
chatppt ppt generate\\\_banana \\\[主题文本] \\\[flags]
```

**用途：**
使用 Banana 模板风格生成 PPT 演示文稿。

**参数：**

|参数|必填|说明|
|-|-|-|
|`主题文本`|是|PPT 主题描述|

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--complex`||`0`|复杂度参数|
|`--language`|||语言设置|
|`--font-name`|||字体名称|
|`--theme-color`|||主题颜色|
|`--user-name`|||用户名|
|`--ai-picture`||`true`|AI 图片生成开关|
|`--image-style`|||图片风格|
|`--custom-template-id`|||自定义模板 ID|
|`--custom-page-count`||`0`|自定义页数|
|`--web-search`||`true`|网络搜索开关|
|`--banana-style-id`|||Banana 风格 ID|
|`--banana-doc-type`|||文档类型（`business`/`education`/`marketing`）|
|`--banana-reference-image`|||参考图片路径或 URL|
|`--requirement`|||需求说明|
|`--output`|`-o`|`json`|输出格式|
|`--poll`||`true`|启用轮询模式|
|`--interval`||`5`|轮询间隔（秒）|
|`--max-retries`||`60`|最大轮询次数|

**使用示例：**

```bash
# 基础用法
chatppt ppt generate\\\_banana "生成一个商务PPT"

# 使用 Banana 特有参数
chatppt ppt generate\\\_banana "年度总结" \\\\
  --banana-style-id "style\\\_001" \\\\
  --banana-doc-type "business" \\\\
  --requirement "需要包含封面、目录、正文三部分"
```

**注意事项：**

* 支持所有 `generate` 命令的参数
* 额外支持 Banana 特有的风格和类型参数

\---

### ppt import\_file

根据上传的文件内容生成 PPT。

**语法：**

```bash
chatppt ppt import\\\_file \\\[主题文本] \\\[flags]
```

**用途：**
上传本地文件到云服务 BOS，然后根据文件内容生成 PPT。

**参数：**

|参数|必填|说明|
|-|-|-|
|`主题文本`|否|PPT 主题描述（未提供时将交互式提示）|

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--file-path`|||本地文件路径（可选，未提供时将提示输入）|
|`--language`|||语言设置|
|`--font-name`|||字体名称|
|`--theme-color`|||主题颜色|
|`--ai-picture`||`true`|AI 图片生成开关|
|`--image-style`|||图片风格|
|`--output`|`-o`|`json`|输出格式|
|`--poll`||`true`|启用轮询模式|

**支持文件格式：**

* `.doc` - Word 文档
* `.docx` - Word 文档（新版）
* `.md` - Markdown 文档
* `.pdf` - PDF 文档
* `.txt` - 纯文本文件

**文件大小限制：** 最大 100MB

**使用示例：**

```bash
# 完整参数
chatppt ppt import\\\_file "基于文档生成PPT" \\\\
  --file-path "/path/to/document.docx"

# 只提供主题（交互式输入文件路径）
chatppt ppt import\\\_file "年度总结"

# 交互式模式（提示输入主题和文件路径）
chatppt ppt import\\\_file
```

**交互式输入示例：**

```
\\\[提示] 请输入PPT主题描述
主题: 人工智能介绍

\\\[提示] 请提供要上传的本地文件路径
支持格式: .doc, .docx, .md, .pdf, .txt
大小限制: 最大100MB

请输入文件路径: /path/to/ai\\\_doc.txt

\\\[文件上传] 正在上传文件到BOS...
\\\[文件上传] 上传成功: https://gz.XXX.com/bucket/ppt\\\_import/1234567890\\\_ai\\\_doc.txt
```

**预期输出：**

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "ppt\\\_id": "ppt\\\_1234567890",
    "request\\\_id": "req\\\_abcdef123456"
  }
}
```

**执行流程：**

1. 输入 PPT 主题（如未提供）
2. 输入本地文件路径（如未提供）
3. 验证本地文件有效性
4. 上传文件到专属云服务 BOS
5. 调用 API 生成 PPT

**注意事项：**

* 文件会先上传到云服务BOS，然后使用生成的 URL 调用 API
* 上传后的文件 URL 格式为：`https://{region}.XXX.com/{bucket}/ppt\\\_import/{timestamp}\\\_{filename}`

\---

### ppt import\_file\_banana

根据上传的文件内容生成 Banana PPT。

**语法：**

```bash
chatppt ppt import\\\_file\\\_banana \\\[主题文本] \\\[flags]
```

**用途：**
上传本地文件到云服务器，然后使用 Banana 模板生成 PPT。

**参数：**

|参数|必填|说明|
|-|-|-|
|`主题文本`|否|PPT 主题描述（未提供时将交互式提示）|

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--file-path`|||本地文件路径（可选，未提供时将提示输入）|
|`--banana-style-id`|||Banana 风格 ID|
|`--banana-doc-type`|||文档类型|
|`--banana-reference-image`|||参考图片路径或 URL|
|`--requirement`|||需求说明|
|`--output`|`-o`|`json`|输出格式|
|`--poll`||`true`|启用轮询模式|

**使用示例：**

```bash
# 完整参数
chatppt ppt import\\\_file\\\_banana "商务报告" \\\\
  --file-path "/path/to/report.docx" \\\\
  --banana-style-id "style\\\_001" \\\\
  --banana-doc-type "business"

# 交互式模式
chatppt ppt import\\\_file\\\_banana
```

**注意事项：**

* 支持所有 `import\\\_file` 命令的参数
* 额外支持 Banana 特有的风格和类型参数

\---

### ppt result

查询 PPT 生成结果。

**语法：**

```bash
chatppt ppt result \\\[ppt\\\_id] \\\[flags]
```

**用途：**
根据 PPT ID 查询生成结果和下载链接。

**参数：**

|参数|必填|说明|
|-|-|-|
|`ppt\\\_id`|是|PPT ID|

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--output`|`-o`|`json`|输出格式（`json`/`pretty`）|

**使用示例：**

```bash
# 查询结果
chatppt ppt result ppt\\\_1234567890

# 使用 pretty 格式
chatppt ppt result ppt\\\_1234567890 -o pretty
```

**预期输出：**

```json
{
  "code": 200,
  "msg": "success",
  "data": {
    "ppt\\\_id": "ppt\\\_1234567890",
    "title": "人工智能介绍",
    "task\\\_status": "SUCCESS",
    "images": {
      "urls": \\\[
        {"url": "https://example.com/ppt/1.jpg"}
      ]
    }
  }
}
```

**任务状态说明：**

|状态|说明|
|-|-|
|`INIT`|初始化中|
|`RUNNING`|生成中|
|`SUCCESS`|生成成功|
|`FAILED`|生成失败|

\---

## 配置命令

### config show

显示当前配置。

**语法：**

```bash
chatppt config show
```

**用途：**
显示当前加载的配置信息，包括 API、认证、PPT、日志等配置。

**使用示例：**

```bash
chatppt config show
```

**预期输出：**

```
```

\---

### config reload

重新加载配置。

**语法：**

```bash
chatppt config reload
```

**用途：**
重新从配置文件加载配置信息。

**使用示例：**

```bash
chatppt config reload
```

**预期输出：**

```
========================================
  重新加载配置
========================================

✓ 配置重新加载成功！
  API地址: http://localhost:9156
  日志级别: info
```

\---

## 版本命令

### version show

显示当前版本信息。

**语法：**

```bash
chatppt version show
```

**用途：**
显示当前 CLI 工具的版本信息。

**使用示例：**

```bash
chatppt version show
```

**预期输出：**

```
========================================
  版本信息
========================================

ChatPPT CLI v1.0.0
  构建时间: 2024-01-15 10:30:00
  Git提交: abc1234
  Go版本: go1.21.0
```

\---

### version check

检查是否有新版本。

**语法：**

```bash
chatppt version check
```

**用途：**
检查远程是否有新版本可用。

**使用示例：**

```bash
chatppt version check
```

**预期输出（有更新）：**

```
========================================
  检查更新
========================================

ℹ 正在检查更新...

  当前版本: v1.0.0
  最新版本: v1.1.0

⚠ 发现新版本！
  更新说明: 新增Banana PPT支持
  下载地址: https://github.com/chatppt/cli/releases

ℹ 使用 'chatppt version update' 执行自动更新
```

**预期输出（已是最新）：**

```
========================================
  检查更新
========================================

ℹ 正在检查更新...

✓ 当前已是最新版本
```

\---

### version update

更新到最新版本。

**语法：**

```bash
chatppt version update \\\[flags]
```

**用途：**
自动下载并安装最新版本。

**可选参数：**

|标志|简写|默认值|说明|
|-|-|-|-|
|`--force`|`-f`|`false`|强制更新，不询问确认|

**使用示例：**

```bash
# 检查并更新（会询问确认）
chatppt version update

# 强制更新
chatppt version update --force
```

**预期输出：**

```
========================================
  更新到最新版本
========================================

ℹ 开始检查更新...

⚠ 发现新版本！
  当前版本: v1.0.0
  最新版本: v1.1.0
  更新说明: 新增Banana PPT支持

是否立即更新 \\\[y/N]: y

ℹ 开始下载更新...
✓ 更新成功！
  新版本: v1.1.0
⚠ 请重新运行程序以使用新版本
```

\---



## 常见问题

### Q: 如何查看命令帮助？

```bash
chatppt --help
chatppt ppt --help
chatppt ppt generate --help
```

### Q: 如何启用调试模式？

```bash
# 全局调试
chatppt --debug ppt generate "主题"

# 或使用环境变量
export CHATPPT\\\_DEBUG=true
chatppt ppt generate "主题"
```

### Q: 配置文件未找到怎么办？

CLI 工具会使用默认配置运行。可以通过 `--config` 指定配置文件路径：

```bash
chatppt --config /path/to/config.yaml ppt generate "主题"
```

\---

## 相关文档

* [README.md](README.md) - 项目简介
* [configs/config.yaml](configs/config.yaml) - 配置文件示例

\---

**文档版本：** v1.0.0  
**最后更新：** 2025-04-1

