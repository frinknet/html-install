# HTML + Shell Polyglot Installer Template

A single file that is **both** a web page and a POSIX shell script. Host it anywhere, then either open it in a browser for install instructions or pipe the same URL into `sh` to execute the installer.

```sh
curl -fsSL https://example.com/install | sh
```

## How it works

The file is split into two halves by a heredoc:

| Lines | Part | Read by |
| --- | --- | --- |
| 1–2 | `#!/bin/sh` + `:<<"HTML_CONTENT"` | The shell; the `:` no-op swallows everything up to the terminator |
| 3–41 | The HTML page | The browser |
| 42 | `HTML_CONTENT` | The shell; ends the heredoc |
| 44+ | The actual installer script | The shell |

Because the shell discards the HTML block as a heredoc body and the browser renders the HTML while ignoring the shell lines, one file serves both audiences.

## The page

- Shows a modal with copy-able install commands (`Review Script` closes it).
- `%s` in the template is replaced at runtime with the page's own URL, so the `curl`/`wget` commands always point at the live file.
- Choosing an option copies it to the clipboard; focusing the select flashes a checkmark.
- The `<pre>` at the bottom fetches and displays the raw script source for review.

## Customizing

1. Replace the `<option>` list in the dialog with your install commands (`brew`, `npm`, etc.).
2. Put your real installer logic after the `HTML_CONTENT` line.
3. Keep the first two lines and the `HTML_CONTENT` terminator intact, or the polyglot breaks.
4. Use `%s` wherever the file's own URL should appear.

## Notes

- POSIX shell only (tested with `sh`, `dash`, `bash`, both directly and via `cat index.html | sh`).
- The shell lines before `<!DOCTYPE html>` are ignored by the HTML parser but do force quirks mode; add nothing else above them.
- Serve from a plain-text-friendly origin; `curl | sh` users should inspect the script first via the page's script preview.
- The script intentionally exits before reaching anything below `exit 0`.
