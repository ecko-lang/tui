# tui

Terminal styling and layout: colours, attributes, hyperlinks and cursor
control, plus padding, boxes and tables measured in terminal columns. Pure
computation - no capabilities.

## Install

```bash
ecko get github.com/ecko-lang/tui
```

```ecko
import tui
```

## Usage

```ecko
import tui

print(tui.bold("Deployed"), tui.green("ok"))   # coloured in a terminal; plain when piped
print(tui.width(tui.red("日本")))               # 4
print(tui.pad_end("hi", 5) + "|")              # hi   |
print(tui.truncate("hello world", 5))          # hell…
print(tui.box(["Ready to ship."], title: "Status"))
# ╭─ Status ───────╮
# │ Ready to ship. │
# ╰────────────────╯
print(tui.table([["City", "Temp"], ["Reykjavik", "4"], ["Nairobi", "26"]], align: ["left", "right"], header: true))
# City       Temp
# ─────────  ────
# Reykjavik     4
# Nairobi      26
```

### Styling

The colour and attribute functions write escape codes only when
`term.color_enabled()` says to: `CLICOLOR_FORCE` forces them on, `NO_COLOR`
off, and otherwise they follow whether stdout is a terminal, so piped output
stays plain. Each takes any value and stringifies it.

```ecko
import tui

print(tui.style("warning", bold: true, fg: "yellow", bg: "black"))
print(tui.rgb("brand", 251, 166, 166))       # 24-bit colour
print(tui.color("palette", 208))             # 256-colour palette
print(tui.link("https://ecko.sh", "docs"))   # a clickable hyperlink
print(tui.strip(tui.red("hi")))              # hi - for writing to a log
```

Cursor and screen control functions return the escape sequence as a string to
`print`, and `""` when colour is off.

### Moving from `std.term`

The styling functions are the ones `std.term` had until Ecko 0.59, under the
same names: `term.red(s)` becomes `tui.red(s)`. `ecko check` flags each
`term.<name>` call that moved. `term.size`, `term.is_tty`,
`term.color_enabled` and keyboard input stay in `std.term`.

## API

### Colours

| function | returns |
|---|---|
| `black(text)` | `text` in black |
| `red(text)` | `text` in red |
| `green(text)` | `text` in green |
| `yellow(text)` | `text` in yellow |
| `blue(text)` | `text` in blue |
| `magenta(text)` | `text` in magenta |
| `cyan(text)` | `text` in cyan |
| `white(text)` | `text` in white |
| `gray(text)` | `text` in gray (code 90) |
| `grey(text)` | `text` in grey, the same as `gray` |
| `bright_black(text)` | `text` in bright black, the same as `gray` |
| `bright_red(text)` | `text` in bright red |
| `bright_green(text)` | `text` in bright green |
| `bright_yellow(text)` | `text` in bright yellow |
| `bright_blue(text)` | `text` in bright blue |
| `bright_magenta(text)` | `text` in bright magenta |
| `bright_cyan(text)` | `text` in bright cyan |
| `bright_white(text)` | `text` in bright white |
| `rgb(text, r, g, b)` | `text` in a 24-bit colour; each channel is clamped to 0-255 |
| `color(text, n)` | `text` in colour `n` of the 256-colour palette, clamped to 0-255 |

### Attributes

| function | returns |
|---|---|
| `bold(text)` | `text` bold |
| `dim(text)` | `text` dimmed |
| `italic(text)` | `text` in italics |
| `underline(text)` | `text` underlined |
| `blink(text)` | `text` blinking, where the terminal supports it |
| `reverse(text)` | `text` with foreground and background swapped |
| `strikethrough(text)` | `text` struck through |
| `style(text, bold: false, dim: false, italic: false, underline: false, blink: false, reverse: false, strikethrough: false, fg: null, bg: null)` | `text` with several attributes and colours at once; `fg`/`bg` take the colour names above |
| `link(url, text)` | `text` as an OSC 8 hyperlink to `url` |
| `strip(s)` | `s` with its escape sequences (colours, cursor moves, hyperlinks) removed |

### Cursor and screen

| function | returns |
|---|---|
| `goto(row, col)` | the sequence that moves the cursor to `row`, `col` (1-based) |
| `up(n = 1)` | the sequence that moves the cursor up `n` rows |
| `down(n = 1)` | the sequence that moves the cursor down `n` rows |
| `right(n = 1)` | the sequence that moves the cursor right `n` columns |
| `left(n = 1)` | the sequence that moves the cursor left `n` columns |
| `save_cursor()` | the sequence that saves the cursor position |
| `restore_cursor()` | the sequence that restores the saved position |
| `hide_cursor()` | the sequence that hides the cursor |
| `show_cursor()` | the sequence that shows the cursor |
| `clear()` | the sequence that clears the screen and moves the cursor home |
| `clear_line()` | the sequence that clears the current line |
| `clear_down()` | the sequence that clears from the cursor to the end of the screen |
| `alt_screen(on = true)` | the sequence that enters the alternate screen, or leaves it with `false` |

### Layout

| function | returns |
|---|---|
| `width(s)` | the visible width of `s` in terminal columns |
| `pad_end(s, w)` | `s` padded on the right to `w` columns; wider text unchanged |
| `pad_start(s, w)` | `s` padded on the left to `w` columns (right-aligned) |
| `center(s, w)` | `s` centred in `w` columns; an odd leftover goes right |
| `truncate(s, w)` | `s` cut to `w` columns, the cut marked with `…` |
| `box(lines, border: "rounded", title: null, padding: 1, min_width: 0)` | a bordered box as one string; `border` is `"rounded"`, `"square"`, `"double"` or `"heavy"` |
| `table(rows, align: [], header: false, gap: "  ")` | rows as an aligned table; `align` per column is `"left"`, `"right"` or `"center"` |

## Notes

### Errors

| kind | reason | when |
|---|---|---|
| `bug` | `bad_argument` | `box`'s `lines` is not a list, or `table`'s `rows` is not a list of lists |
| `bug` | `bad_option` | an unknown `border`, `align`, `fg` or `bg` value; a negative or non-Int `padding` or `min_width`; a non-Bool `header`; options passed as a map |
| `bug` | `bad_width` | `w` given to `pad_end`, `pad_start`, `center` or `truncate` is not a non-negative Int |

### Widths are terminal columns

Every width is what a terminal draws. Escape sequences take no columns, so
coloured content lines up with plain content. CJK characters, fullwidth forms
and emoji take two columns; combining marks, zero-width joiners and variation
selectors take none; an emoji ZWJ sequence such as a family, a skin-tone emoji
and a flag each count as one two-column glyph. The tables follow Unicode's
East Asian Width property, as `wcwidth` does. Terminals still disagree on a
few rare sequences, such as keycaps.

### Content

Cells and lines may be any value; a non-string is stringified. A line of a box
that holds newlines becomes several rows. `truncate` drops escape sequences
from a string it cuts, so truncate before colouring.

## Testing

```bash
ecko test
```

## License

[MIT](LICENSE)
