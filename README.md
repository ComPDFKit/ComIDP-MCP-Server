# ComPDF AI (Formerly ComIDP) for MCP Server

As part of the KDAN ecosystem, ComPDF AI for MCP Server delivers **Intelligent Document Extraction**—automatically extracting key information from unstructured documents (such as PDFs or images), converting it into structured data, and supporting batch processing to significantly boost document handling efficiency.

> If you find this library helpful, please consider giving us a ⭐ **Star** on GitHub! Have feedback or questions? Join the conversation in our [Discussions](https://github.com/ComPDFKit/ComIDP-MCP-Server/discussions).

**Why ComPDF AI for MCP Server?**

- **Batch Processing for Real Efficiency:** Handle large volumes of documents at once. Batch processing significantly speeds up your workflow and reduces operational overhead.

- **From Unstructured to Actionable:** Transform messy PDFs and scanned images into structured, machine-readable data that can be easily used in databases, analytics, or downstream applications.

- **Boost Document Handling Productivity:** Eliminate bottlenecks in document processing. Focus on insights and decisions, while ComPDF AI handles the extraction automatically and accurately.

- **Native MCP Server Architecture:** Designed as an MCP server, it seamlessly integrates with any MCP-compatible client (Claude Desktop, Cursor, Windsurf, Cline, and more), enabling standardized, context-aware document intelligence workflows and effortless scalability.

## Table of Contents

- [What is ComPDF AI for MCP Server](#what-is-compdf-ai-for-mcp-server)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Claude Desktop](#claude-desktop)
  - [Cursor](#cursor)
  - [Other MCP Clients](#other-mcp-clients)
- [API Reference](#api-reference)
- [Example Output](#example-output)
- [Free Trial and License](#free-trial-and-license)
- [FAQ](#faq)
- [Support](#support)

## What is ComPDF AI for MCP Server

**ComPDF AI for MCP Server** is a lightweight Model Context Protocol (MCP) server designed for seamless integration with [ComPDF AI](https://www.compdf.com/solutions/intelligent-document-processing?utm_source=Code&utm_campaign=github_mcpserver_20250912&utm_medium=GitHub). It connects AI chatbots and MCP-compatible clients to ComPDF AI's Intelligent Document Processing (IDP) capabilities, enabling you to extract structured data from PDFs and images directly within your AI workflow. The service returns results in structured plain-text format, enabling downstream processing or archival.

**Supported document types:** PDF, PNG, JPG, TIFF, and other common image formats.

## Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/ComPDFKit/comidp-mcp.git
cd comidp-mcp

# 2. Set up the environment
pip install uv
cd src
python -m venv .venv

# Windows
.venv\Scripts\activate

# Linux / macOS
source .venv/bin/activate

pip install -r requirements.txt

# 3. Configure your MCP client (see Configuration section below)

# 4. Restart your MCP client and start extracting!
```

## Installation

### Prerequisites

- **Python 3.10+**
- **pip** (Python package installer)
- **uv** — install with `pip install uv`
- **ComPDF MCP repository**
- **OS:** Windows, Linux, or macOS

### Steps

1. Clone the repository and navigate to the source directory:

```bash
git clone https://github.com/ComPDFKit/comidp-mcp.git
cd comidp-mcp/src
```

2. Create a virtual environment and install dependencies:

**Windows:**

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

**Linux / macOS:**

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Configuration

Add the comidp-mcp server configuration to your MCP client's config file. Replace the paths and API key with your actual values.

> **Important:** All paths must be **absolute paths**. The virtual environment Python path should point to the Python executable inside your `.venv` directory.

### Claude Desktop

1. Open Claude Desktop.
2. Click the Claude icon in the top-left corner → **File → Settings → Developer → Edit Config**.
3. Add the following to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "comidp-mcp": {
      "command": "uv",
      "args": [
        "run",
        "--directory",
        "C:/path/to/comidp-mcp/src",
        "C:/path/to/comidp-mcp/src/comidp_tools.py"
      ],
      "env": {
        "IDPKEY": "your_idp_key_here"
      }
    }
  }
}
```

**Path examples:**

| OS          | Python venv path                                 |
| ----------- | ------------------------------------------------ |
| Windows     | `C:\path\to\comidp-mcp\.venv\Scripts\python.exe` |
| Linux/macOS | `/path/to/comidp-mcp/.venv/bin/python`           |

### Cursor

Add the server to your Cursor MCP settings (`.cursor/mcp.json` in your project root, or via Cursor Settings → MCP):

```json
{
  "mcpServers": {
    "comidp-mcp": {
      "command": "uv",
      "args": [
        "run",
        "--directory",
        "/path/to/comidp-mcp/src",
        "/path/to/comidp-mcp/src/comidp_tools.py"
      ],
      "env": {
        "IDPKEY": "your_idp_key_here"
      }
    }
  }
}
```

### Other MCP Clients

For any MCP-compatible client (Windsurf, Cline, etc.), add a server entry with:

- **Command:** `uv`
- **Args:** `["run", "--directory", "/path/to/comidp-mcp/src", "/path/to/comidp-mcp/src/comidp_tools.py"]`
- **Environment variable:** `IDPKEY=your_idp_key_here`

Refer to your client's documentation for how to register MCP servers.

### Verify Installation

After restarting your MCP client, you can verify the server is running by:

1. **Claude Desktop:** Check the Developer settings — the `comidp-mcp` server should show as connected (green indicator).
2. **Cursor:** Open MCP settings — the server status should be "Active".
3. **Test it:** Ask your AI client to extract data from a sample PDF file.

## API Reference

### `data_extraction`

Extract data from specified PDF or image files and save results to TXT files.

```python
def data_extraction(
    filenames: list,
    save_dir_path: str = "output",
    key: str = "",
    err_msg_lang: str = "en"
) -> Dict[str, str]:
    """
    Params:
        filenames: A list of PDF or image file paths.
        save_dir_path: Folder where the result TXT files will be saved.
        key: The API key for IDPKEY. Required on the first call;
             subsequent calls reuse the cached key automatically.
        err_msg_lang: Language code for error messages ('en' or 'zh'). Defaults to 'en'.

    Returns:
        A dictionary mapping each input file path to its output TXT file path.
        If an error occurs, the value will be an error message.
    """
```

**Example call (via AI client):**

> "Extract data from `/documents/invoice.pdf` and save the results to `/output/`"

### `data_extraction_from_folder`

Extract data from all PDF or image files in a folder.

```python
def data_extraction_from_folder(
    folder: str,
    save_dir_path: str,
    recursive: bool = False,
    key: str = "",
    err_msg_lang: str = "en"
) -> Dict[str, str]:
    """
    Params:
        folder: Path to the folder containing PDF or image files.
        save_dir_path: Path to the folder where the result files will be saved.
        recursive: If true, recursively search subdirectories for files.
        key: The API key for IDPKEY. Required on the first call;
             subsequent calls reuse the cached key automatically.
        err_msg_lang: Language code for error messages ('en' or 'zh'). Defaults to 'en'.

    Returns:
        A dictionary mapping each input file path to its output TXT file path.
        If an error occurs, the value will be an error message.
    """
```

**Example call (via AI client):**

> "Extract data from all PDFs in `/documents/2024/` recursively, and save to `/output/2024/`"

## Example Output

Given an invoice PDF, the extracted TXT output may look like:

```
Invoice Number: INV-2024-00158
Date: 2024-03-15
Vendor: Acme Corporation
Total Amount: $2,450.00
Items:
  - Cloud Hosting Service (12 months): $1,200.00
  - SSL Certificate (1 year): $150.00
  - Technical Support (12 months): $1,100.00
```

## Free Trial and License

This project is licensed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0). Please [contact us](https://www.compdf.com/contact-sales?utm_source=github&utm_medium=comidp-mcp-server&utm_campaign=comidp_mcp_server_repo&ref_platform_id=github_compdfkit) for a trial license key.

## FAQ

**Q: How do I get an API key?**
A: [Contact us](https://www.compdf.com/contact-sales?utm_source=Code&utm_campaign=github_mcpserver_20250912&utm_medium=GitHub) to request a free trial key.

**Q: What file formats are supported?**
A: PDF, PNG, JPG, TIFF, and other common image formats.

**Q: Do I need to pass the API key on every call?**
A: No. The key is required only on the first call. It is cached automatically for subsequent calls within the same session.

**Q: Which MCP clients are supported?**
A: Any MCP-compatible client, including Claude Desktop, Cursor, Windsurf, Cline, and more.

## Support

If you encounter any issues or need support, please open an issue or [contact our team](https://www.compdf.com/support?utm_source=Code&utm_campaign=github_mcpserver_20250912&utm_medium=GitHub).
