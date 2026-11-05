# dbcss

A database query engine written in pure CSS. No JavaScript.

**Demo:** https://gouz.github.io/dbcss/

```sql
SELECT * FROM users
 WHERE city = 'Lyon'
   AND role = 'dev'
 ORDER BY age DESC
 LIMIT 2;
```

Pick a query with the radio buttons. CSS filters, sorts, limits and counts the rows, and writes the SQL for you.

## How it works

| SQL | CSS |
|---|---|
| table / rows | DOM: `<div class="row" data-city="Lyon" style="--age:34">` |
| query state | `.db:has(#c-lyon:checked)` |
| `WHERE` | attribute selectors + `display: none` |
| `ORDER BY` | `order: var(--age)` / `order: calc(-1 * var(--age))` |
| `LIMIT n` | fixed row height + `max-height: n rows; overflow: hidden` (so it applies *after* sorting) |
| `WHERE age > n` | `--gt: clamp(0, var(--age) - var(--min), 1)` → 0/1 mask, applied to row height and counters |
| `COUNT(*)` / `SUM(age)` | `counter-increment: n var(--keep) sum calc(var(--keep) * var(--age))` |
| `AVG(age)` | SUM and COUNT are rendered as tally marks (`@counter-style`), their widths measured with container query units, then divided with `tan(atan2(sum, count))` |
| `JOIN cities ON c.name = u.city` | each city row creates a counter *named after its key*: `counter-reset: attr(data-name type(<custom-ident>)) attr(data-pop type(<integer>))`; user rows read `counter(attr(data-city …))` |
| `UPDATE users SET role = …` | role is a `<select>`; queries read `option:checked` |
| `DELETE` | soft delete: a checkbox sets `--alive: 0`, the row is struck through and excluded from aggregates |
| SQL preview | string custom properties concatenated in `content` |

Numeric values live once in a custom property and are rendered back as text with `counter-reset: age var(--age)`.

## Not supported

- `INSERT`: CSS can't create elements. Revealing pre-allocated hidden rows would be cheating.
- Persistence: it's an in-memory database. Reload and it's gone.
- `AVG` tops out around a sum of ~15 000 (Chrome caps counter representations at ~150 symbols).

## Browser support

- Core (`SELECT`/`WHERE`/`ORDER BY`/`LIMIT`/`COUNT`/`SUM`/`UPDATE`/`DELETE`): any browser with `:has()`
- `AVG`: also needs `@property`, container query units and `atan2()`
- `JOIN`: typed `attr()`, Chromium 133+ only

## License

[MIT](LICENSE)
