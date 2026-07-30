# TUI - Ecko Std Lib Package

Terminal UI composition for [Ecko](https://ecko.sh), written in Ecko. The
composition helpers `std.term` doesn't ship: width-aware padding, bordered
boxes, and aligned tables - so you stop hand-rolling border math and column
layout in every TUI.

String composition over `std.term`'s `width`/`strip`, so every helper is
deterministic. The key idea: **widths count what the
eye sees** (`term.width` ignores ANSI escapes), so colored content still lines
up.

## Install

```bash
ecko get github.com/ecko-lang/tui
```

```ecko
import tui
```

## Width-aware primitives

`string.pad_*` count characters, which miscounts escape codes. These count
visible columns:

```ecko
import tui

tui.pad_end("hi", 5)          # "hi   "
tui.pad_start("42", 5)        # "   42"
tui.center("hi", 6)           # "  hi  "
tui.truncate("hello world", 5) # "hell…"

tui.pad_end(term.red("hi"), 5) # pads by the 2 VISIBLE columns, not the bytes
```

## Boxes

```ecko
tui.box(["Ready to ship."], { title: "Status", border: "rounded" })
# ╭─ Status ───────╮
# │ Ready to ship. │
# ╰────────────────╯
```

`opts`: `border` (`"rounded"` | `"square"` | `"double"` | `"heavy"`), `title`,
`padding` (spaces inside the verticals, default 1), `min_width` (total box
width). The box grows to fit its content, title, and `min_width`.

## Tables

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

## Testing

```bash
ecko test    # offline: pad/center/truncate, box, table (9 cases)
```

## License

MIT
