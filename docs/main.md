# tui

## `black(text)`

`text` in black. Every colour and attribute function takes any value
(non-strings are stringified) and returns it unchanged when colour is off.

## `red(text)`

`text` in red.

## `green(text)`

`text` in green.

## `yellow(text)`

`text` in yellow.

## `blue(text)`

`text` in blue.

## `magenta(text)`

`text` in magenta.

## `cyan(text)`

`text` in cyan.

## `white(text)`

`text` in white.

## `gray(text)`

`text` in gray (the same code as `grey` and `bright_black`).

## `grey(text)`

`text` in grey - an alias of `gray`.

## `bright_black(text)`

`text` in bright black - an alias of `gray`.

## `bright_red(text)`

`text` in bright red.

## `bright_green(text)`

`text` in bright green.

## `bright_yellow(text)`

`text` in bright yellow.

## `bright_blue(text)`

`text` in bright blue.

## `bright_magenta(text)`

`text` in bright magenta.

## `bright_cyan(text)`

`text` in bright cyan.

## `bright_white(text)`

`text` in bright white.

## `bold(text)`

`text` in bold.

## `dim(text)`

`text` dimmed.

## `italic(text)`

`text` in italics.

## `underline(text)`

`text` underlined.

## `blink(text)`

`text` blinking, where the terminal supports it.

## `reverse(text)`

`text` with foreground and background swapped.

## `strikethrough(text)`

`text` struck through.

## `rgb(text, r: Int, g: Int, b: Int)`

`text` in a 24-bit colour; each channel is clamped to 0-255.

## `color(text, n: Int)`

`text` in colour `n` of the 256-colour palette, clamped to 0-255.

## `style(text, bold = false, dim = false, italic = false, underline = false, blink = false, reverse = false, strikethrough = false, fg = null, bg = null)`

Several attributes at once, as named arguments: `style("ok", bold: true,
fg: "green", bg: "black")`. `fg` and `bg` take the colour names above; an
unknown one throws (`kind: "bug"`).

## `link(url, text)`

`text` as a clickable hyperlink to `url` (OSC 8), or plain `text` when
colour is off.

## `goto(row: Int, col: Int)`

Move the cursor to `row`, `col` (1-based; smaller values become 1).

## `up(n = 1)`

Move the cursor up `n` rows (default 1).

## `down(n = 1)`

Move the cursor down `n` rows (default 1).

## `right(n = 1)`

Move the cursor right `n` columns (default 1).

## `left(n = 1)`

Move the cursor left `n` columns (default 1).

## `save_cursor()`

Save the cursor position.

## `restore_cursor()`

Restore the cursor position saved by `save_cursor`.

## `hide_cursor()`

Hide the cursor.

## `show_cursor()`

Show the cursor.

## `clear()`

Clear the screen and move the cursor home.

## `clear_line()`

Clear the current line.

## `clear_down()`

Clear from the cursor to the end of the screen.

## `alt_screen(on = true)`

Switch to the alternate screen, or back with `alt_screen(false)`.

## `strip(s)`

`s` with its escape sequences removed - colours, cursor moves and
hyperlinks - which is what a log file wants. Works whether or not colour is on.

## `width(s)`

The visible width of `s`: its characters once escape sequences are removed.

## `pad_end(s, w)`

Pad `s` on the right (left) to a visible width of `w`; wider stays unchanged.

## `pad_start(s, w)`

Right-align `s` in `w` columns by padding on the left. Width is measured in
visible columns, so ANSI colour codes do not count.

## `center(s, w)`

Center `s` in `w` columns; an odd leftover goes on the right.

## `truncate(s, w)`

Truncate to a visible width of `w`, marking the cut with an ellipsis. (A
truncated colored string loses its trailing escapes - truncate plain text.)

## `box(lines, opts)`

box(lines, opts?) -> a bordered box as one string. opts: border (style),
title, padding (spaces inside the verticals, default 1), min_width (total).

## `table(rows, opts)`

Render rows as an aligned text table. `opts` may carry `align` (a list of
`"left"`/`"right"` per column) and `gap` (the column separator, two spaces
by default). Column widths come from the widest visible cell.
