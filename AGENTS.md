# ChatPPT-CLI Agent 使用指南

> **面向读者：** AI Agent 开发者  
> **最后更新：** 2026-04-13

ChatPPT-CLI 是专为 AI Agent 设计的命令行工具，允许 Agent 通过标准 CLI 协议完成 PPT 的生成、编辑、导出等全流程操作。本文档旨在帮助 Agent 开发者快速理解 ChatPPT-CLI 的能力边界、调用方式、输出解析及异常处理。

---

## 一、概述

ChatPPT-CLI 将复杂的 PPT 操作封装为分类清晰的命令集，Agent 无需关心底层 API 细节，只需根据用户自然语言意图选择合适的命令并解析标准 JSON 输出即可。

**核心设计原则：**
- **SSOT 输出（Single Source of Truth）**：所有命令默认输出 `json` 格式，字段稳定，便于解析。
- **智能轮询**：对于耗时任务（如 PPT 生成），CLI 内置 `--poll` 轮询机制，Agent 只需等待最终结果，无需重复查询。
- **自愈指引**：当出现认证失败、参数错误、文件格式不支持等问题时，CLI 输出明确的中文错误信息和修复建议，Agent 可直接反馈给用户或自动重试。

**开源地址：** [https://github.com/YOOTeam/ChatPPT-CLI](https://github.com/YOOTeam/ChatPPT-CLI)

---

## 二、安装与认证

### 2.1 安装

Agent 运行环境需具备 Go 工具链（或直接使用发布版二进制）。通常由用户或运行环境预装。

```bash
git clone https://github.com/YOOTeam/ChatPPT-CLI.git
cd ChatPPT-CLI
go build -o chatppt .
```

成功后可执行 `chatppt version show` 验证。

### 2.2 认证（用户必经）

首次使用需完成设备码授权，Agent 应引导用户执行：

```bash
chatppt auth login
```

认证成功后，Token 自动存入系统密钥链。Agent 可用 `chatppt auth status` 随时检查登录状态。若返回未登录，应提示用户执行上述命令。

---

## 三、全量 CLI 能力映射

### 3.1 能力一览

| 能力域 | CLI 命令（核心） | 说明 |
|--------|------------------|------|
| **导入** | `ppt import_file`、`ppt import_file_banana` 等 | 上传本地文件或模板，返回文档 ID |
| **查询** | `keyword_search`、`file_list`、`doc_list` | 按关键字或列表检索文件和文档 |
| **生成** | `ppt generate`、`ppt generate_banana` | 根据主题生成经典或 Banana 风格 PPT |
| **编辑** | `beautify_all`、`beautify_page`、`add_page`、`delete_page`、`change_font`、`change_theme_color`、`animate` | 美化、增删页、换字体/主题色、动效 |
| **创作** | `script_gen`、`ai_roadshow`、`voice_list` | 演讲稿生成、AI 路演创建、音色读取 |
| **导出** | `get_editor_url`、`download_file`、`export_script`、`export_content` | 获取在线编辑链接或下载 pptx/pdf/png |
| **商业化** | `auth check`、`quota_check`、`points_record` | 鉴权、权益、消耗查询 |

> 📌 实际 CLI 命令名可能略有调整，请以 `chatppt --help` 输出为准。Agent 应优先读取命令帮助文本，而非硬编码。

### 3.2 通用标志

所有命令均可携带以下全局标志，便于调试：

| 标志 | 简写 | 说明 |
|------|------|------|
| `--config` | | 指定配置文件路径（默认 `~/.chatppt/config.yaml`） |
| `--debug` | `-d` | 输出调试日志，帮助定位问题 |
| `--format` | `-f` | 输出格式，默认为 `json`，也可选 `pretty` |

Agent 在调试阶段可添加 `--debug` 以获取更详细错误信息。

---

## 四、典型交互流程

### 4.1 用户：“帮我生成一份关于新能源汽车的PPT，字体用微软雅黑，蓝色主题”

Agent 执行步骤如下：

1. **校验认证**  
   `chatppt auth status` 若未登录，提示用户执行 `chatppt auth login`。

2. **调用生成命令**  
   ```bash
   chatppt ppt generate "新能源汽车发展趋势" \
     --language zh \
     --font-name "微软雅黑" \
     --theme-color "blue" \
     --output json
   ```

3. **解析返回**  
   标准 JSON 返回示例：
   ```json
   {
     "code": 200,
     "msg": "success",
     "data": {
       "ppt_id": "ppt_1234567890",
       "request_id": "req_abcdef123456"
     }
   }
   ```
   Agent 提取 `ppt_id`。

4. **轮询结果（若未禁用 `--poll`，CLI 会自动等待，最终直接返回结果）**  
   若 Agent 手动查询，可调用：  
   ```bash
   chatppt ppt result ppt_1234567890
   ```
   当 `task_status` 为 `SUCCESS` 时，从 `images` 或 `download_url` 获取最终资源。

5. **反馈用户**  
   将下载链接或在线编辑器 URL 呈现给用户。

---

### 4.2 用户：“把这份 Word 文档做成 PPT，用香蕉模板的高级商务风”

Agent 执行步骤：

1. 确认文件路径（用户在对话中可能直接提供或由 Agent 询问）。  
2. 调用文件导入 + Banana 生成命令：
   ```bash
   chatppt ppt import_file_banana "根据文档生成商务PPT" \
     --file-path "/path/to/report.docx" \
     --banana-doc-type business \
     --banana-style-id "style_001"
   ```
3. 解析返回的 `ppt_id`，轮询最终状态，反馈下载地址。

---

### 4.3 用户：“在我的PPT第3页后插入这张图片，并删除第5页”

此任务涉及文档整合与页面删除，Agent 应顺序执行：

1. 先通过 `doc_list` 或上下文获取目标文档 ID（若用户未给出，可列出文档列表让用户选择）。
2. 调用文档整合命令（假设 CLI 已暴露 `merge_file`）：
   ```bash
   chatppt ppt merge_file --doc-id xxx --file "/path/image.png" --position "after_3"
   ```
3. 调用删除页面命令：
   ```bash
   chatppt ppt delete_page --doc-id xxx --pages "5"
   ```
4. 返回更新后的编辑器链接或文件下载链接。

---

## 五、输出解析规范

### 5.1 标准返回结构

所有命令的输出均为 JSON，顶层包含 `code`、`msg`、`data` 三个字段。

- `code`：200 表示成功；非 200 表示失败，`msg` 含具体错误描述。
- `data`：任务相关数据，常见字段有：
  - `ppt_id` / `Document_id`：文档唯一标识
  - `editor_url`：在线编辑链接
  - `download_url`：文件下载链接（视导出类型而定）
  - `task_status`：生成任务状态（INIT / RUNNING / SUCCESS / FAILED）
  - `Version_id`：文档修改后产生的新版本 ID

Agent 应始终先判断 `code`，再读取 `data`。

### 5.2 错误处理建议

| 错误场景 | CLI 输出特征 | Agent 处理方式 |
|----------|-------------|----------------|
| 未登录 | `code` 为非 200，`msg` 包含“认证失败”或“未登录” | 引导用户执行 `chatppt auth login` |
| 文件格式不支持 | `msg` 标明“不支持的文件格式” | 告知用户支持的格式列表，要求重新提供 |
| 文件过大 | `msg` 包含“文件大小超过限制” | 提示用户文件最大 100MB |
| 配额不足 | `msg` 包含“剩余点数不足” | 提示用户充值或稍后再试 |

---

## 六、最佳实践

1. **惰性查询**：优先使用 `--poll` 模式，减少轮询代码。
2. **上下文记忆**：Agent 应在对话中缓存 `Document_id`，方便用户后续编辑时直接复用。
3. **组合能力**：面对复杂需求时，分解为多个 CLI 命令顺序执行，并向用户实时反馈进度。
4. **帮助探索**：若不确定参数可选项，Agent 可先执行 `chatppt ppt generate --help` 获取实时参数说明，再构造命令。

---

## 七、命令帮助速查

```bash
chatppt --help               # 全局命令列表
chatppt ppt --help           # PPT 相关子命令
chatppt ppt generate --help  # 具体命令参数详解
```

Agent 应定期执行 `chatppt version check` 检查更新，确保使用最新能力。

---

## 八、相关资源

- GitHub 仓库：[https://github.com/YOOTeam/ChatPPT-CLI](https://github.com/YOOTeam/ChatPPT-CLI)
- OpenPPT 在线编辑器：[https://github.com/YOOTeam/OpenPPT](https://github.com/YOOTeam/OpenPPT)