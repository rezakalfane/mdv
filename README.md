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
- **Code blocks:** fenced (```` ``` ```` / `~~~`) and indented, drawn as a padded dark grey panel in a full box with the language name, syntax-highlighted with Pygments when available. The language tag can be a name (`rust`), an alias (`sh`) or an extension (`mjs`, `h`). Shell blocks also colour command names and `--flags`. Long lines wrap inside the box.
- **Source files:** `mdv main.rs` (any non-Markdown file) shows the whole file highlighted, with line numbers. Files without an extension are recognised by name (`Makefile`, `Dockerfile`).
- **Tables:** box borders with a line between rows, column alignment (`:---`, `:---:`, `---:`), long cells wrap to fit the terminal.
- **Themes:** 5 colour themes, chosen automatically from the terminal background or set with `--theme`.
- **Other:** horizontal rules, YAML front matter (shown as a framed block), hard line breaks, `<br>`. HTML comments and common HTML tags are hidden.
- **Layout:** uses the full terminal width and handles wide characters (CJK, emoji).
- **Pager:** long documents open in a built-in full-screen pager that re-wraps the document when you resize the window, follows links (keyboard or mouse click), scrolls with the mouse wheel, and copies selected text to the clipboard. `less` is available as an alternative.
- **PDF:** `--pdf` writes a print-styled PDF (A4 or Letter) instead of printing: headings with accent rules, highlighted code panels with the language label, zebra tables, coloured callouts, task lists, images, links, and a running title with page numbers.
- **Colour:** off automatically when output is not a terminal, and when `NO_COLOR` is set. Uses the 256-colour palette, so it works in macOS Terminal, iTerm2 and most Linux terminals.

## Requirements

- Python 3.8 or newer
- Optional: Pygments, for syntax highlighting (`pip install pygments`)
- Optional: `less`, only if you choose `--pager less`
- For `--pdf`: Google Chrome, Chromium, Edge or Brave (found automatically, or set `MDV_CHROME` to the binary), or else [WeasyPrint](https://weasyprint.org/) (`pip install weasyprint`)

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
mdv [-h] [-w WIDTH] [-P] [-p] [--pager {builtin,less}] [--no-mouse] [--color {auto,always,never}] [-t THEME] [-l LANG] [--list-themes] [--pdf] [-o FILE] [--paper SIZE] [files ...]
```

| Option | Description |
|---|---|
| `files` | One or more Markdown files. With no file, or `-`, reads stdin. |
| `-w`, `--width WIDTH` | Fixed wrap width. Default: the full terminal width, following resizes. |
| `-P`, `--no-pager` | Print directly instead of opening a pager. |
| `-p`, `--page` | Always open the pager, even when the document fits on screen (to follow its links). |
| `--pager builtin\|less` | Pager for long documents. Default: `$MDV_PAGER` or `builtin`. |
| `--no-mouse` | Built-in pager: don't capture the mouse, so plain drag selects text. |
| `--color auto\|always\|never` | Colour mode. `auto` (default) colours only when writing to a terminal and `NO_COLOR` is not set. |
| `-t`, `--theme THEME` | Colour theme: `auto` (default), `midnight`, `paper`, `nord`, `dracula`, `solarized`. |
| `-l`, `--language LANG` | Show the input as source code in this language, with line numbers (useful with stdin). |
| `--list-themes` | Preview every theme on a sample document, then exit. |
| `--pdf` | Write a PDF instead of printing. Each input `FILE.md` becomes `FILE.pdf` next to it (stdin: `mdv.pdf`). |
| `-o`, `--output FILE` | With `--pdf`: the output file (single input only). |
| `--paper SIZE` | With `--pdf`: `a4` (default, or `$MDV_PAPER`), `letter`, `legal`, `a3`, `a5`. |

Examples:

```sh
mdv README.md               # view a file
mdv docs/*.md               # several files, each with a header line
cat notes.md | mdv          # read from stdin
mdv -w 80 file.md           # wrap at 80 columns
mdv -P file.md              # no pager
mdv --color=never file.md   # plain text
mdv --color=always file.md | less -R   # keep colours when piping
mdv -t nord file.md         # use the nord theme
mdv src/main.rs             # a source file: highlighted, with line numbers
curl -s https://api.github.com | mdv -l json   # highlight stdin as JSON
mdv --list-themes           # preview all themes
mdv --pdf README.md         # write README.pdf
mdv --pdf --paper letter -o out.pdf notes.md
cat notes.md | mdv --pdf -o notes.pdf
```

## Pager

When a document is taller than the window, `mdv` opens it in a full-screen
pager. Resizing the window re-wraps the document to the new width and keeps
your reading position. Shorter documents are printed directly; text already
printed to the terminal can't be re-wrapped.

| Keys | Action |
|---|---|
| `↓` `j` `Enter` / `↑` `k` | Scroll one line |
| `Space` `f` `PgDn` / `b` `PgUp` | Scroll one page |
| `d` / `u` | Scroll half a page |
| `g` `Home` / `G` `End` | Go to top / bottom |
| `/` text `Enter` | Search (case-insensitive) |
| `n` / `N` | Next / previous match |
| `Tab` / `Shift-Tab` | Select the next / previous link |
| `Enter` or mouse click | Open the selected / clicked link |
| `←` `Backspace` `h` | Go back to the previous document and position |
| mouse wheel | Scroll three lines |
| mouse drag | Select text and copy it to the clipboard |
| double-click / triple-click | Copy a word / a whole paragraph, list item, code block or cell |
| `Esc` | Clear the selection |
| `q` | Quit |

### Following links

- Links to Markdown files (`.md`, `.markdown`) open in the pager. Relative
  paths are resolved from the folder of the current file.
- `#section` links, and `file.md#section`, jump to the matching heading.
- A link to a folder opens its `README.md` (or `index.md`), or else a listing
  of the folder in which every entry is a link.
- Other text files (`.rs`, `.json`, `.py`, …) are shown as a highlighted code block.
- `http(s)://` and `mailto:` links open in your browser (`open` on macOS,
  `xdg-open` on Linux).
- The status line shows where the selected link goes; `←` returns to the exact
  place you left.

### Selecting and copying text

Drag with the mouse to select text; it is copied to the clipboard when you
release the button, and the status line shows how many characters were copied.

- **Double-click** selects and copies a word. Paths and URLs count as one word
  (`docs/guides/cli.md`, `https://…`); trailing punctuation is left out.
- **Triple-click** selects and copies the whole block: a paragraph, a list
  item, a whole code block, or a table cell.
- Lines that `mdv` wrapped to fit the window are copied as the original line:
  paragraphs are rejoined with spaces, long code lines and split words with
  nothing in between, and a table cell that wraps over several lines is copied
  as one value.

- Only the text is copied: borders, padding, line numbers, list bullets and
  quote bars are left out.
- A selection that starts and ends inside one code block copies just the code,
  with its indentation.
- A selection that starts and ends in the same table column copies just that
  column's cells, one per line. Across columns, cells are separated by tabs, so
  they paste into a spreadsheet as cells.
- Dragging past the top or bottom of the window scrolls.
- `Esc` clears the selection. A click without dragging follows a link.

The clipboard is written with `pbcopy` on macOS, or `wl-copy`, `xclip` or
`xsel` on Linux. Without any of these, `mdv` sends the OSC 52 escape sequence,
which many terminals (iTerm2, kitty, WezTerm, tmux) accept.

`--no-mouse` turns all of this off and leaves the mouse to the terminal, so its
own selection works as usual.

To use `less` instead (no re-wrap on resize), pass `--pager less` or set
`export MDV_PAGER=less`.

## Themes

| Theme | Designed for | Code highlighting |
|---|---|---|
| `midnight` | dark terminals | Monokai |
| `paper` | light terminals | One Dark |
| `nord` | dark terminals | Nord |
| `dracula` | dark terminals | Dracula |
| `solarized` | light or dark terminals | Solarized Dark |

The default, `auto`, uses `paper` when the terminal background is light and
`midnight` otherwise. The background is read from the `COLORFGBG` variable when
set, or by asking the terminal for its background colour (OSC 11). If your
terminal doesn't answer, `auto` falls back to `midnight`.

To choose a theme permanently, set `MDV_THEME` in `~/.zshrc` or `~/.bashrc`:

```sh
export MDV_THEME=paper
```

`--theme` on the command line overrides `MDV_THEME`.

Code blocks and inline code always use a dark grey background, so code stays
readable on both light and dark terminals.

## Limitations

- Footnotes and reference-style links (`[text][ref]`) are shown as plain text.
- Raw HTML blocks are not rendered; known tags are stripped and their text kept.
- The parser is a pragmatic approximation of CommonMark / GitHub Flavored Markdown, not a full implementation.

## License

[MIT](LICENSE)
