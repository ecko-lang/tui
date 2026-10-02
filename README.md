# TUI - Ecko Std Lib Package

Terminal styling and UI composition for [Ecko](https://ecko.sh), written in
Ecko: colours, attributes, hyperlinks and cursor control, plus width-aware
padding, bordered boxes and aligned tables - so you stop hand-rolling escape
codes, border math and column layout in every TUI.

The styling functions are the ones `std.term` had until Ecko 0.59, under the
same names: `term.red(s)` becomes `tui.red(s)`. They write escape codes only
when `term.color_enabled()` says to - `CLICOLOR_FORCE` forces them on,
`NO_COLOR` off, otherwise they follow whether stdout is a terminal - so piped
output stays plain. The layout helpers are deterministic, and **widths count
what the eye sees** (escape codes take no columns), so coloured content still
lines up. Needs Ecko 0.59 or later.

## Install

```bash
ecko get github.com/ecko-lang/tui
```

```ecko
import tui
```

## Usage

### Styling

```ecko
import tui

print(tui.bold("Deployed") + " " + tui.green("ok"))
print(tui.style("warning", bold: true, fg: "yellow", bg: "black"))
print(tui.rgb("brand", 251, 166, 166))     # 24-bit colour
print(tui.color("palette", 208))           # 256-colour palette
print(tui.link("https://ecko.sh", "docs")) # a clickable hyperlink

tui.strip(tui.red("hi"))   # "hi" - for writing to a log
tui.width(tui.red("hi"))   # 2
```

Cursor and screen control - `goto`, `up`/`down`/`left`/`right`,
`save_cursor`/`restore_cursor`, `hide_cursor`/`show_cursor`, `clear`,
`clear_line`, `clear_down`, `alt_screen` - return the escape sequence as a
string to `print`, and an empty string when colour is off.

### Moving from `std.term`

`ecko check` flags each `term.<name>` call that moved, from 0.59 on. Change the
import and the prefix; the arguments and the output are the same. `term.size`,
`term.is_tty`, `term.color_enabled` and keyboard input stay in `std.term`.

### Width-aware primitives

`string.pad_*` count characters, which miscounts escape codes. These count
visible columns:

```ecko
import tui

tui.pad_end("hi", 5)          # "hi   "
tui.pad_start("42", 5)        # "   42"
tui.center("hi", 6)           # "  hi  "
tui.truncate("hello world", 5) # "hell…"

tui.pad_end(tui.red("hi"), 5) # pads by the 2 VISIBLE columns, not the bytes
```

### Boxes

```ecko
tui.box(["Ready to ship."], { title: "Status", border: "rounded" })
# ╭─ Status ───────╮
# │ Ready to ship. │
# ╰────────────────╯
```

`opts`: `border` (`"rounded"` | `"square"` | `"double"` | `"heavy"`), `title`,
`padding` (spaces inside the verticals, default 1), `min_width` (total box
width). The box grows to fit its content, title, and `min_width`.

### Tables

```ecko
tui.table(
    [["City", "Temp"], ["Reykjavik", "4"], ["Nairobi", "26"]],
    { align: ["left", "right"], header: true },
)
# City       Temp
# ─────────  ────
# Reykjavik     4
# Nairobi      26
```

`opts`: `align` (per-column `"left"` | `"right"` | `"center"`, default left),
`header` (a rule after the first row), `gap` (column separator, default two
spaces). Columns auto-size to their widest visible cell - colored cells
included.

## API

Every width here is a **visible** width: escape codes take no columns, so
coloured content lines up with plain content. Each styling function takes any
value (a non-string is stringified) and returns its plain text when colour is
off.

| Function | Description |
|---|---|
| `black` `red` `green` `yellow` `blue` `magenta` `cyan` `white` `(text)` | Foreground colour |
| `gray` / `grey` / `bright_black`, `bright_red` `bright_green` `bright_yellow` `bright_blue` `bright_magenta` `bright_cyan` `bright_white` `(text)` | Bright foreground colour |
| `bold` `dim` `italic` `underline` `blink` `reverse` `strikethrough` `(text)` | Attribute |
| `rgb(text, r, g, b)` | 24-bit colour; channels clamp to 0-255 |
| `color(text, n)` | 256-colour palette entry, clamped to 0-255 |
| `style(text, bold:, dim:, italic:, underline:, blink:, reverse:, strikethrough:, fg:, bg:)` | Several at once, as named arguments; an unknown `fg`/`bg` name throws `kind: "bug"` |
| `link(url, text)` | An OSC 8 hyperlink |
| `goto(row, col)` | Move the cursor (1-based) |
| `up(n?)` `down(n?)` `left(n?)` `right(n?)` | Move the cursor `n` cells, default 1 |
| `save_cursor()` `restore_cursor()` `hide_cursor()` `show_cursor()` | Cursor state |
| `clear()` `clear_line()` `clear_down()` | Clear the screen, the line, or below the cursor |
| `alt_screen(on?)` | Enter the alternate screen, or leave it with `false` |
| `strip(s)` | `s` without escape sequences (colours, cursor moves, hyperlinks) |
| `width(s)` | The visible width of `s` |
| `pad_end(s, w)` | Pad on the right to `w` columns; wider text is left alone |
| `pad_start(s, w)` | Pad on the left - right-aligns `s` in `w` columns |
| `center(s, w)` | Centre in `w` columns; an odd leftover goes right |
| `truncate(s, w)` | Cut to `w` columns, marking the cut with an ellipsis |
| `box(lines, opts?)` | A bordered box as one string |
| `table(rows, opts?)` | Rows as an aligned table, auto-sized to the widest cell |

## Testing

```bash
ecko test    # offline: styling, pad/center/truncate, box, table (15 cases)
```

## License

MIT
