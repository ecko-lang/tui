# Changelog

## 0.40.0

### Breaking

- `box` and `table` take their options as named arguments instead of a map,
  and the options are now optional: `box(lines)` and `table(rows)` work on
  their own. A map passed the old way throws a `bug` error saying so.
  Migration: `tui.box(lines, { title: "Status", border: "square" })` becomes
  `tui.box(lines, title: "Status", border: "square")`, and
  `tui.table(rows, { align: ["left", "right"], header: true })` becomes
  `tui.table(rows, align: ["left", "right"], header: true)`.
- `width` and everything measured with it count terminal columns, not
  characters: `width("日本")` is 4 and `width("🦀")` is 2, where they were 2
  and 1. Migration: none needed for aligning output, which now lines up; code
  that used `width` as a character count should use `len(tui.strip(s))`.

### Fixed

- Boxes, tables and padding around CJK text or emoji line up. Combining
  marks, zero-width joiners and variation selectors take no column, and an
  emoji ZWJ sequence, a skin-tone emoji or a flag counts as one glyph.
- `truncate` never cuts a wide character or a combining sequence in half, and
  a negative width throws a `bug` error instead of returning `"hell"`.
- `box`, `table`, `pad_end`, `pad_start`, `center` and `truncate` accept any
  value and stringify it, as the styling functions do, instead of throwing
  "Can't add int and string".
- A box line holding newlines becomes several rows instead of breaking the
  border.
- An unknown `border` or `align` value, a negative `padding` or `min_width`,
  or a non-Bool `header` throws a `bug` error naming the option, instead of
  being quietly ignored or drawing a broken box.
- The `style` error for an unknown colour carries `reason: "bad_option"` and
  lists the colour names.
- The tests no longer ship inside the release archive.
