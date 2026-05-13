# ChatPPT-CLI

<div align="center">

[![GitHub stars](https://img.shields.io/github/stars/YOOTeam/ChatPPT-CLI?style=social)](https://github.com/YOOTeam/ChatPPT-CLI)
[![GitHub license](https://img.shields.io/github/license/YOOTeam/ChatPPT-CLI)](https://github.com/YOOTeam/ChatPPT-CLI/blob/main/LICENSE)
[![Go version](https://img.shields.io/badge/Go-1.21+-00ADD8?logo=go)](https://go.dev/)

</div>

**ChatPPT-CLI** is the official open-source command-line tool by ChatPPT, purpose-built for **AI Agents**. It provides a standardized, programmable interface for the entire lifecycle of PPT creation, editing, styling, and exporting.

While large language models can understand requirements and generate text, they can't directly deliver a well-formatted, beautifully designed presentation. ChatPPT-CLI is the bridge—it allows AI Agents to **no longer just output outlines, but to directly manipulate PPT documents**, from creation to export, in one seamless workflow.

> 🧩 Together with the [OpenPPT](https://github.com/YOOTeam/OpenPPT) online PPT editor, it forms the open-source ecosystem of ChatPPT.

## ✨ Core Capabilities

ChatPPT-CLI covers the full lifecycle of PPT operations. All functionalities are exposed to AI Agents through natural language → CLI commands:

| Domain | Main Features |
|--------|---------------|
| **Import** | Local template/PPT/file import, document merging, screenshot capture |
| **Query** | Keyword search, file list, document list reading |
| **Generation** | Classic mode generation, Banana drawing style generation |
| **Editing** | Full-page/single-page beautification, add/delete slides, font change, theme color change, animation rendering |
| **Additional Creation** | Auto-generate speaker notes, create AI roadshow, voice selection |
| **Export** | Online editor URL, pptx/png/pdf download, export speaker notes/content files |
| **Commercial** | Secure authentication, identity/entitlement verification, points consumption query |

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/YOOTeam/ChatPPT-CLI.git
cd ChatPPT-CLI

# Development build
go build -o chatppt .

# Production build (optimized size)
go build -ldflags "-s -w" -o chatppt .
```

### Authentication

ChatPPT-CLI uses a secure device-code flow for user authorization. Credentials are automatically stored in the system keychain.

```bash
# Login (browser will open automatically)
chatppt auth login

# Check login status
chatppt auth status

# Logout
chatppt auth logout
```

## 📖 Usage Examples

Simply express your needs to your AI Agent in natural language, and it will call the corresponding CLI commands automatically. Below are command examples for illustration:

```bash
# Generate PPT from a topic
chatppt ppt generate "AI Development Trends" \
  --language en \
  --font-name "Arial"

# Upload a file and generate PPT
chatppt ppt import_file "Annual Summary" \
  --file-path "/path/to/document.docx"

# Generate a business-style PPT using the Banana template
chatppt ppt generate_banana "Product Launch" \
  --banana-doc-type marketing \
  --banana-style-id "style_001"

# Query generation results
chatppt ppt result ppt_1234567890
```

## 📂 Project Structure

```
ChatPPT-CLI/
├── cmd/               # CLI entry points
├── internal/
│   ├── auth/          # Authentication module
│   ├── ppt/           # PPT generation and editing
│   ├── storage/       # File upload (Baidu BOS)
│   └── config/        # Configuration management
├── configs/           # Sample configuration files
├── go.mod
└── README.md
```

## ⚙️ Configuration

Default config path: `$HOME/.chatppt/config.yaml`

You can specify a custom path using the `--config` flag. Key configurations (e.g., BCE credentials) can also be overridden via environment variables.

## 🧩 Why ChatPPT-CLI

- **Designed for AI Agents**: JSON structured output, intelligent polling, and self-healing error guidance reduce token consumption and improve task success rates.
- **End-to-End Lifecycle**: From import, generation, editing, and beautification to export and roadshow—manages the complete PPT lifecycle.
- **Open Source & Transparent**: Licensed under GNU GPL v3.0, the code is fully open for community collaboration.
- **Ecosystem Integration**: Seamlessly works with the [OpenPPT](https://github.com/YOOTeam/OpenPPT) online editor, offering both CLI power and online fine-tuning.

## 🤝 Contributing

Issues and Pull Requests are welcome!  
Please ensure you have read the contribution guide (coming soon).

## 📄 License

This project is licensed under the [GNU General Public License v3.0](LICENSE).

## 🔗 Related Links

- [OpenPPT Online Editor](https://github.com/YOOTeam/OpenPPT)
- [YOOTeam Homepage](https://github.com/YOOTeam)

---

<div align="center">
Made with ❤️ by YOOTeam
</div>