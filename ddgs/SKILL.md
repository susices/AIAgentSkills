---
name: ddgs
description: Use when the agent needs web search, recent news/image/video/book lookup, or readable content extraction from a URL via the ddgs CLI.
---

# ddgs — Web Search for Agents

## Use This Skill For

- general public web search
- recent news lookup
- image, video, or book discovery
- extracting readable content from a known URL

This skill is for **direct CLI usage**. Do not switch to Python, MCP, or server setup unless the user explicitly asks.

## Quick Check

```bash
ddgs version || pip install -U ddgs
ddgs --help
```

Available subcommands can vary by installed ddgs version. Verify them with `ddgs --help`.

## Agent Rules

- **CLI only** — do not write Python code unless the user explicitly asks.
- **Start small** — use `--max_results 3` to `5` first.
- **Search first, then extract** — use `extract` only after finding a relevant URL.
- **Refine before expanding** — improve query keywords, `--region`, `--timelimit`, or `--backend` before increasing result count.
- **Leave backend on default** unless results are poor or the user asks for a specific one.
- **Avoid downloads** (`-d`) unless the user explicitly wants files saved locally.
- **Temp files go to the system temp directory** — when you need `-o json`, `-o csv`, or any other temporary output file for inspection, write it under the OS temp dir (for example `$TMPDIR`, `$TEMP`, or a `mktemp` path), not the current working directory, and remove it after use unless the user asked to keep it.
- **Use help instead of guessing** — run `ddgs <subcommand> --help`.

## Fast Workflow

1. Run a focused search with a small result count.
2. Review titles and snippets in stdout.
3. If deeper reading is needed, run `extract` on the best URL.
4. If results are weak, refine query / region / timelimit / backend.
5. If you need stable machine-friendly output, save to JSON in the system temp directory, inspect it, then clean it up unless the user asked to keep it.

## Common Commands

### Web search

```bash
ddgs text -q "python asyncio taskgroup" --max_results 5
```

### News search

```bash
ddgs news -q "ai regulation" --timelimit w --max_results 5
```

### Image search

```bash
ddgs images -q "butterfly" --max_results 5
```

### Video search

```bash
ddgs videos -q "rust programming" --max_results 5
```

### Book search

```bash
ddgs books -q "machine learning" --max_results 5
```

### Extract readable page content

```bash
ddgs extract -u https://example.com
ddgs extract -u https://example.com -f text_plain
```

### Save structured output

```bash
ddgs text -q "python async" --max_results 3 -o json
ddgs text -q "python async" --max_results 3 -o results.json
ddgs news -q "ai regulation" --timelimit w --max_results 3 -o results.csv
```

### Temporary output file examples

#### Windows PowerShell

```powershell
$tmpJson = Join-Path $env:TEMP ("ddgs-" + [guid]::NewGuid().ToString() + ".json")
ddgs text -q "python async" --max_results 3 -o $tmpJson
Get-Content $tmpJson | Select-Object -First 20
Remove-Item $tmpJson -Force
```

#### POSIX shell

```bash
tmp_json="${TMPDIR:-/tmp}/ddgs-$(date +%s)-$$.json"
ddgs text -q "python async" --max_results 3 -o "$tmp_json"
head -n 20 "$tmp_json"
rm -f "$tmp_json"
```

## Output Guidance

- Default stdout is **human-readable terminal output**, suitable for quick inspection.
- `-o json` or `-o csv` saves results to an auto-generated file.
- `-o <name>.json` or `-o <name>.csv` saves to a specific file.
- `extract` supports `-o json` or `-o <name>.json`.
- When saving output only for temporary inspection, prefer an explicit file path in the system temp directory instead of the current working directory, and delete it afterward unless the user asked to keep it.
- If exact field names matter, save a small result set to JSON and inspect the file instead of assuming keys from memory.

## Common Options

Supported options vary by subcommand. Check `ddgs <subcommand> --help` for the exact command you are using.

| Option | Use |
|---|---|
| `-q, --query` | Search query |
| `-m, --max_results` | Number of results |
| `-t, --timelimit` | Recent results: `d`, `w`, `m`, `y` |
| `-r, --region` | Region such as `us-en`, `uk-en`, `de-de` |
| `-s, --safesearch` | `on`, `moderate`, `off` |
| `-b, --backend` | Choose backend when supported |
| `-o, --output` | Save to `json`, `csv`, or explicit filename |
| `-nc, --no-color` | Disable color output if needed |

## Troubleshooting

| Problem | What to do |
|---|---|
| `ddgs: command not found` | Run `pip install -U ddgs` |
| Results are noisy | Tighten keywords, add context, or change `--region` / `--backend` |
| No or few recent results | Relax `--timelimit` or remove it |
| Timeout / network error | Retry once; if it persists, tell the user it appears to be an environment or network issue |
| Need machine-friendly output | Use `-o <temp-file>.json` in the system temp directory, inspect it, then remove it |
| Unsure which flags exist | Run `ddgs <subcommand> --help` |
