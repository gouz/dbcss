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
| `COUNT(*)` | CSS counters (hidden rows don't increment) |
| SQL preview | string custom properties concatenated in `content` |

Numeric values live once in a custom property and are rendered back as text with `counter-reset: age var(--age)`.

## Not supported (yet?)

- `WHERE age > n`: needs range style queries or attribute-prefix hacks
- `JOIN`: CSS can't compare two dynamic values
- `SUM` / `AVG`: counters only increment
- `INSERT` / `UPDATE`: CSS is read-only

## Browser support

Any browser with `:has()` (Chrome 105+, Safari 15.4+, Firefox 121+).

## License

[MIT](LICENSE)
