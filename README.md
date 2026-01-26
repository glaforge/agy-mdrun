# Executable Markdown with [Gemini CLI](https://geminicli.com/)

This guide explains how to transform standard Markdown files into executable scripts powered by the [Gemini CLI](https://geminicli.com/).
This allows you to create AI-driven automation, pipelines, and autonomous "bots" using simple Markdown text files.

### Quick Look
Create a file named `weather.md`:
```markdown
#!/usr/bin/env gemini-run
Use only the Google Search tool to find the answer to the question below:
What is the weather like in Paris right now?
```
Then run it:
```bash
chmod +x weather.md
./weather.md
```

## 1. The Wrapper Script (`gemini-run`)

To handle the shebang and pipe the markdown content correctly into the [Gemini CLI](https://geminicli.com/), use the following wrapper script.

### Download the `gemini-run` script:

The `gemini-run` script is available in this repository in the `scripts` directory:
[gemini-run](./scripts/gemini-run)

### Install the script:
Make it executable and move it to your system path:
```bash
chmod +x gemini-run
sudo mv gemini-run /usr/local/bin/
```

---

## 2. Usage & Examples

You'll find a few examples in the [examples](./examples) directory.

### Basic Executable Prompt
Create a file named `hello.md`, make it executable, and run it.

**`hello.md`**
```markdown
#!/usr/bin/env -S gemini-run
What is the current phase of the moon?
```
```bash
chmod +x hello.md
./hello.md
```

### YOLO Mode (Autonomous Execution)
By using the `--yolo` flag in the shebang, Gemini will execute tools and commands automatically without asking for confirmation. **Use with caution.**

**`auto_clean.md`**
```markdown
#!/usr/bin/env -S gemini-run --yolo
List all files in the current directory and rename any file with a '.txt' extension to have a '.bak' extension instead.
```

### Interactive/Safe Mode
If you omit the `--yolo` flag, Gemini will prompt you for approval before running any tools or modifying your system.

**`audit.md`**
```markdown
#!/usr/bin/env -S gemini-run
Search the codebase for hardcoded API keys. If you find any,
propose a fix but do not apply it without my approval.
```

### Piping Pipelines
Because `gemini-run` supports stdin, you can chain multiple markdown scripts together or mix them with standard Unix tools.

**`step1_extract.md`**
```markdown
#!/usr/bin/env -S gemini-run
Extract all email addresses from the input text and list them one per line.
```

**`step2_analyze.md`**
```markdown
#!/usr/bin/env -S gemini-run
For each email address provided, guess if it belongs to a corporate or personal domain.
```

**Execution:**
```bash
cat customers.log | ./step1_extract.md | ./step2_analyze.md
```

### Advanced Configuration
You can pass **any** [Gemini CLI](https://geminicli.com/) argument via the shebang line. This allows you to specify models, output formats, or system prompts for specific scripts.

**`json_output.md`**
```markdown
#!/usr/bin/env -S gemini-run --model gemini-2.5-flash --output-format json
List 3 distinct colors.
```

---

## 3. How it Works

1.  **Shebang Execution**: When you run `./script.md`, the OS sees the `#!` line and executes `/usr/local/bin/gemini-run` with your script as an argument.
2.  **`env -S`**: Used in the shebang to allow passing flags like `--yolo` on systems like macOS and modern Linux.
3.  **Prompt Extraction**: The wrapper uses `tail -n +2` to strip the shebang line from the markdown file before sending it to Gemini, ensuring the LLM only sees your instructions.
4.  **Stdin Integration**: The [Gemini CLI](https://geminicli.com/) automatically appends any data received from a pipe to the prompt provided as an argument, enabling powerful multi-stage workflows.
5.  **Tool Execution**: The [Gemini CLI](https://geminicli.com/) automatically executes any tools specified in the shebang line, allowing you to run commands and tools directly from your markdown file. You can restrict the tools available in the shebang line using the `--allowed-tools` or `--allowed-mcp-server-names` flags, to avoid using the dangerously powerful `--yolo` flag that allows any tool to be executed!

---

## Other Examples

You can find a few more examples in the [examples](./examples) directory.

- **[git-log-summary.md](./examples/git-log-summary.md)**: Summarizes recent git commits to automatically generate structured release notes.
- **[google-search.md](./examples/google-search.md)**: Uses the Google Search tool to find and return real-time information from the web.
- **[nano-banana.md](./examples/nano-banana.md)**: Leverages image generation tools to create stylized illustrations from text prompts.
- **[nano-banana-chain.md](./examples/nano-banana-chain.md)**: Demonstrates script chaining by piping the weather results from `google-search.md` into this script to generate a contextual illustration of the current conditions. You can chain them with `./google-search.md | ./nano-banana-chain.md`.


---

## Inspiration

This project was inspired by a discussion on [HackerNews](https://news.ycombinator.com/item?id=46549444) regarding [claude-switcher](https://github.com/andisearch/claude-switcher) and the idea of executable markdown scripts for AI CLIs.

---

**Disclaimer**: This is not an official Google project.
