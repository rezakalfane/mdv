# mdv

Display Markdown files nicely in the terminal, with colours and styles.

`mdv` is a single Python 3 script with no required dependencies. If
[Pygments](https://pygments.org/) is installed, code blocks are
syntax-highlighted.

```
mdv README.md
```

## Features

- **Headings:** a colour per level, with an underline on levels 1 and 2. Setext headings (`===` / `---`) are supported.
- **Inline styles:** **bold**, *italic*, ~~strikethrough~~, `inline code` with a background, links (underlined, with the URL in grey), images shown as `[image: alt]`.
- **Lists:** nested bullets (`•` `◦` `▪`), numbered lists, task lists (`✔` done, `☐` to do).
- **Block quotes:** a side bar. GitHub callouts (`> [!NOTE]`, `[!TIP]`, `[!IMPORTANT]`, `[!WARNING]`, `[!CAUTION]`) get their own colour and title.
- **Code blocks:** fenced (```` ``` ```` / `~~~`) and indented, drawn in a frame with the language name, syntax-highlighted with Pygments when available.
- **Tables:** box borders, column alignment (`:---`, `:---:`, `---:`), long cells wrap to fit the terminal.
- **Other:** horizontal rules, YAML front matter (shown as a framed block), hard line breaks, `<br>`. HTML comments and common HTML tags are hidden.
- **Layout:** wraps text to the terminal width (max 100 columns), handles wide characters (CJK, emoji), and opens long output in `less -R`.
- **Colour:** off automatically when output is not a terminal, and when `NO_COLOR` is set.

## Requirements

- Python 3.8 or newer
- Optional: Pygments, for syntax highlighting (`pip install pygments`)
- Optional: `less`, for paging (present on macOS and most Linux systems)

## Installation

Clone the repository and link the script into a directory on your `PATH`:

```sh
git clone https://github.com/rezakalfane/mdv.git
mkdir -p ~/.local/bin
ln -s "$PWD/mdv/mdv" ~/.local/bin/mdv
```

Or download the script directly:

```sh
mkdir -p ~/.local/bin
curl -fsSL https://raw.githubusercontent.com/rezakalfane/mdv/main/mdv -o ~/.local/bin/mdv
chmod +x ~/.local/bin/mdv
```

If `~/.local/bin` is not on your `PATH`, add this line to `~/.zshrc` or `~/.bashrc`:

```sh
export PATH="$HOME/.local/bin:$PATH"
```

## Usage

```
mdv [-h] [-w WIDTH] [-P] [--color {auto,always,never}] [files ...]
```

| Option | Description |
|---|---|
| `files` | One or more Markdown files. With no file, or `-`, reads stdin. |
| `-w`, `--width WIDTH` | Wrap width. Default: terminal width, at most 100. |
| `-P`, `--no-pager` | Print directly instead of paging long output through `less`. |
| `--color auto\|always\|never` | Colour mode. `auto` (default) colours only when writing to a terminal and `NO_COLOR` is not set. |

Examples:

```sh
mdv README.md               # view a file
mdv docs/*.md               # several files, each with a header line
cat notes.md | mdv          # read from stdin
mdv -w 80 file.md           # wrap at 80 columns
mdv -P file.md              # no pager
mdv --color=never file.md   # plain text
mdv --color=always file.md | less -R   # keep colours when piping
```

## Limitations

- Footnotes and reference-style links (`[text][ref]`) are shown as plain text.
- Raw HTML blocks are not rendered; known tags are stripped and their text kept.
- Code lines longer than the width are not wrapped.
- The parser is a pragmatic approximation of CommonMark / GitHub Flavored Markdown, not a full implementation.

## License

[MIT](LICENSE)
