# Executable Markdown with [Gemini CLI](https://geminicli.com/)

This guide explains how to transform standard Markdown files into executable scripts powered by the [Gemini CLI](https://geminicli.com/). This allows you to create AI-driven automation, pipelines, and autonomous "bots" using simple text files.

### Quick Look
Create a file named `weather.md`:
```markdown
#!/usr/bin/env gemini-run
What is the weather like in Paris right now?
```
Then run it:
```bash
chmod +x weather.md
./weather.md
```

## 1. The Wrapper Script (`gemini-run`)

To handle the shebang and pipe the markdown content correctly into the [Gemini CLI](https://geminicli.com/), use the following wrapper script.

### Create the script:
Save this content as `gemini-run`:

```bash
#!/bin/bash
# gemini-run: A wrapper to run markdown files with gemini

# Parse Arguments: Last argument is the file, others are flags
ARGS=("$@")
LEN=${#ARGS[@]}

if [ $LEN -lt 1 ]; then
    echo "Usage: gemini-run [options] <file.md>"
    exit 1
fi

LAST_IDX=$((LEN - 1))
FILE="${ARGS[$LAST_IDX]}"
FLAGS=("${ARGS[@]:0:$LAST_IDX}")

if [ ! -f "$FILE" ]; then
    echo "Error: File '$FILE' not found."
    exit 1
fi

# Read content skipping the first line (the shebang)
PROMPT=$(tail -n +2 "$FILE")

# Run gemini with passed flags and the prompt content
gemini "${FLAGS[@]}" "$PROMPT"
```

### Install the script:
Make it executable and move it to your system path:
```bash
chmod +x gemini-run
sudo mv gemini-run /usr/local/bin/
```

---

## 2. Usage & Examples

### Basic Executable Prompt
Create a file named `hello.md`, make it executable, and run it.

**`hello.md`**
```markdown
#!/usr/bin/env gemini-run
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
List all files in the current directory and rename any file with a '.txt' 
extension to have a '.bak' extension instead.
```

### Interactive/Safe Mode
If you omit the `--yolo` flag, Gemini will prompt you for approval before running any tools or modifying your system.

**`audit.md`**
```markdown
#!/usr/bin/env gemini-run
Search the codebase for hardcoded API keys. If you find any, 
propose a fix but do not apply it without my approval.
```

### Piping Pipelines
Because `gemini-run` supports stdin, you can chain multiple markdown scripts together or mix them with standard Unix tools.

**`step1_extract.md`**
```markdown
#!/usr/bin/env gemini-run
Extract all email addresses from the input text and list them one per line.
```

**`step2_analyze.md`**
```markdown
#!/usr/bin/env gemini-run
For each email address provided, guess if it belongs to a corporate or 
personal domain.
```

**Execution:**
```bash
cat customers.log | ./step1_extract.md | ./step2_analyze.md
```

### Advanced Configuration
You can pass **any** [Gemini CLI](https://geminicli.com/) argument via the shebang line. This allows you to specify models, output formats, or system prompts for specific scripts.

**`json_output.md`**
```markdown
#!/usr/bin/env -S gemini-run --model gemini-2.0-flash --output-format json
List 3 distinct colors.
```

---

## 3. How it Works

1.  **Shebang Execution**: When you run `./script.md`, the OS sees the `#!` line and executes `/usr/local/bin/gemini-run` with your script as an argument.
2.  **`env -S`**: Used in the shebang to allow passing flags like `--yolo` on systems like macOS and modern Linux.
3.  **Prompt Extraction**: The wrapper uses `tail -n +2` to strip the shebang line from the markdown file before sending it to Gemini, ensuring the LLM only sees your instructions.
4.  **Stdin Integration**: The [Gemini CLI](https://geminicli.com/) automatically appends any data received from a pipe to the prompt provided as an argument, enabling powerful multi-stage workflows.

---

## Inspiration

This project was inspired by a discussion on [HackerNews](https://news.ycombinator.com/item?id=46549444) regarding [claude-switcher](https://github.com/andisearch/claude-switcher) and the idea of executable markdown scripts for AI CLIs.

---

**Disclaimer**: This is not an official Google project.
