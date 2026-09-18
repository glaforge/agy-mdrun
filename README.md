# Executable Markdown with [Antigravity CLI](https://antigravity.google/docs/cli/reference)

> [!NOTE]
> **Migration note**: This project was originally created for [Gemini CLI](https://geminicli.com/). With Gemini CLI now deprecated, the project has migrated to the [Antigravity CLI (`agy`)](https://antigravity.google/docs/cli/reference). For backward compatibility with existing workflows, a [`gemini-run`](./scripts/gemini-run) forwarder is provided and legacy flags like `--yolo` are automatically translated.

This guide explains how to transform standard Markdown files into executable scripts powered by the [Antigravity CLI (`agy`)](https://antigravity.google/docs/cli/reference).
This allows you to create AI-driven automation, pipelines, and autonomous "bots" using simple Markdown text files.

### Quick Look
Create a file named `weather.md`:
```markdown
#!/usr/bin/env agy-run
Use web search to find the answer to the question below:
What is the weather like in Paris right now?
```
Then run it:
```bash
chmod +x weather.md
./weather.md
```

## 1. The Wrapper Script (`agy-run`)

To handle the shebang and pipe the markdown content correctly into the Antigravity CLI (`agy`), use the wrapper script.

### Prerequisites:
Make sure the Antigravity CLI (`agy`) is installed and authenticated on your system:
```bash
which agy
agy --version
```
> [!NOTE]
> Unlike older tools that required exporting an API key manually, Antigravity CLI handles its own authenticated sessions automatically.

### Download the `agy-run` script:

The `agy-run` script is available in this repository in the `scripts` directory:
[agy-run](./scripts/agy-run)

*(For backward compatibility, a legacy [`gemini-run`](./scripts/gemini-run) forwarder is also provided).*

### Install the script:
Make it executable and move it to your system path:
```bash
chmod +x agy-run
sudo mv agy-run /usr/local/bin/
```
*(Or move it to `~/.local/bin/` if that is in your `PATH`).*

---

## 2. Usage & Examples

You'll find examples in the [examples](./examples) directory.

### Basic Executable Prompt
Create a file named `hello.md`, make it executable, and run it.

**`hello.md`**
```markdown
#!/usr/bin/env -S agy-run
What is the current phase of the moon?
```
```bash
chmod +x hello.md
./hello.md
```

### Autonomous Tool Execution
By using the `--dangerously-skip-permissions` flag in the shebang, Antigravity CLI will auto-approve tool execution and commands without prompting for confirmation. **Use with caution.**

**`auto_clean.md`**
```markdown
#!/usr/bin/env -S agy-run --dangerously-skip-permissions
List all files in the current directory and rename any file with a '.txt' extension to have a '.bak' extension instead.
```

> [!TIP]
> For convenience and backwards compatibility with scripts written for Gemini CLI, `agy-run` automatically translates `--yolo` to `--dangerously-skip-permissions`.

### Interactive / Safe Mode
If you omit `--dangerously-skip-permissions`, Antigravity CLI will prompt you for approval or enforce safety settings before executing tools or modifying your system.

**`audit.md`**
```markdown
#!/usr/bin/env -S agy-run
Search the codebase for hardcoded API keys. If you find any,
propose a fix but do not apply it without my approval.
```

### Piping Pipelines
Because `agy-run` supports stdin, you can chain multiple markdown scripts together or mix them with standard Unix tools.

**`step1_extract.md`**
```markdown
#!/usr/bin/env -S agy-run
Extract all email addresses from the input text and list them one per line.
```

**`step2_analyze.md`**
```markdown
#!/usr/bin/env -S agy-run
For each email address provided, guess if it belongs to a corporate or personal domain.
```

**Execution:**
```bash
cat customers.log | ./step1_extract.md | ./step2_analyze.md
```

### Advanced Configuration
You can pass **any** [Antigravity CLI](https://antigravity.google/docs/cli/reference) argument via the shebang line. This allows you to specify models, reasoning effort, or output formats for specific scripts.

**`json_output.md`**
```markdown
#!/usr/bin/env -S agy-run --model gemini-3.8-flash-high --output-format json
List 3 distinct colors.
```

Available models can be inspected anytime with:
```bash
agy models
```

---

## 3. How it Works

1. **Shebang Execution**: When you run `./script.md`, the OS sees the `#!` line and executes `/usr/local/bin/agy-run` with your script as an argument.
2. **`env -S`**: Used in the shebang to allow passing flags like `--dangerously-skip-permissions` or `--model` on systems like macOS and Linux.
3. **Prompt Extraction**: The wrapper uses `tail -n +2` to strip the shebang line from the markdown file before sending it to `agy`, ensuring the model only sees your instructions.
4. **Stdin Integration**: The wrapper automatically detects piped input and combines it with the markdown prompt, enabling powerful multi-stage workflows.
5. **Tool & MCP Execution**: Antigravity CLI includes built-in agentic tools (`run_command`, `search_web`, `read_url_content`, etc.) and seamlessly connects to any Model Context Protocol (MCP) servers configured in `agy` (`agy mcp list`).

---

## Other Examples

You can find working examples in the [examples](./examples) directory:

- **[git-log-summary.md](./examples/git-log-summary.md)**: Inspects recent git commits using `run_command` to automatically generate structured release notes.
- **[google-search.md](./examples/google-search.md)**: Uses real-time web search to find and return up-to-date information.
- **[nano-banana.md](./examples/nano-banana.md)**: Leverages an MCP server or visual generation tool to create stylized infographics and sketchnotes from web articles.
- **[nano-banana-chain.md](./examples/nano-banana-chain.md)**: Demonstrates script chaining by piping weather data from `google-search.md` into an image generation script (`./google-search.md | ./nano-banana-chain.md`).

---

## Inspiration

This project was inspired by a discussion on [HackerNews](https://news.ycombinator.com/item?id=46549444) regarding [claude-switcher](https://github.com/andisearch/claude-switcher) and the idea of executable markdown scripts for AI CLIs.

---

**Disclaimer**: This is not an official Google project.
