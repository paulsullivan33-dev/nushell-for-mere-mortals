# Reshaping Data

Outcome: build new columns from old ones,
group rows into buckets, and turn a
finished table into whatever the next
tool wants — JSON, CSV, YAML, Markdown —
or save it to a file.

## New columns: `each` and `insert`

A table often needs a column it does not
have. Two commands cover the two shapes
of that need.

**`each`** runs a block once per row and
collects what the blocks return. The
block is the text between braces; inside
it, `|row|` names the current row, and
`$row.title` reaches into it:

```text
$ nu -c 'open books.csv | each { |row| $"($row.title) by ($row.author)" }'
╭───┬──────────────────────────────────────────────────╮
│ 0 │ The Left Hand of Darkness by Ursula K. Le Guin   │
│ 1 │ A Wizard of Earthsea by Ursula K. Le Guin        │
│ 2 │ The Making of Prince of Persia by Jordan Mechner │
│ 3 │ Piranesi by Susanna Clarke                       │
│ 4 │ The City & The City by China Mieville            │
╰───┴──────────────────────────────────────────────────╯
```

(The `$"..."` form is **string
interpolation**: text with expressions
in parentheses evaluated as the string is
built. Chapter 8 uses it in scripts.)
That pipeline returns a list of strings —
the table shape is gone, replaced by the
blocks' answers. When you want the table
*plus* the new column, that is **`insert`**:

```text
$ nu -c 'open books.csv | insert est_words { |row| $row.pages * 250 } | select title est_words'
╭───┬────────────────────────────────┬───────────╮
│ # │             title              │ est_words │
├───┼────────────────────────────────┼───────────┤
│ 0 │ The Left Hand of Darkness      │     76000 │
│ 1 │ A Wizard of Earthsea           │     45750 │
│ 2 │ The Making of Prince of Persia │     80000 │
│ 3 │ Piranesi                       │     68000 │
│ 4 │ The City & The City            │     78000 │
╰───┴────────────────────────────────┴───────────╯
```

(Transcript `t06-each-insert.txt`.) The
guess of 250 words per page is crude and
says so in its name (`est_words`) — a new
column is a claim, and naming it like one
is part of the craft. Its sibling from
chapter 5, `update`, is `insert`'s twin
for a column that already exists.

## Grouping: `group-by`

**`group-by`** gathers rows that share a
value. For the lab's scores — two games,
three players — grouping by player with
`--to-table` gives one row per player,
each holding a small table of that
player's rows:

```text
$ nu -c 'open scores.json | group-by --to-table player'
╭───┬────────┬────────────────╮
│ # │ player │     items      │
├───┼────────┼────────────────┤
│ 0 │ Ana    │ [table 2 rows] │
│ 1 │ Ben    │ [table 1 row]  │
│ 2 │ Cleo   │ [table 1 row]  │
╰───┴────────┴────────────────╯
```

(Transcript `t05-open-json-groupby.txt`.)
The `items` column holds whole tables —
values inside values, which surprises
nobody once chapter 1's model has sunk
in — and each of those tables can be
opened up in turn: `get items.0` is Ana's
two rows, ready for a `math sum` of their
points. Grouping is the end of most
"summary" questions: group, then do
arithmetic inside each bucket.

## Converting: the `to` family

A table's whole advantage is that it was
never text, so handing it to a text-shaped
world is a deliberate, one-word act.
**`to json`**:

```text
$ nu -c 'open books.csv | where year >= 2020 | to json'
[
  {
    "title": "The Making of Prince of Persia",
    "author": "Jordan Mechner",
    "pages": 320,
    "year": 2020
  },
  {
    "title": "Piranesi",
    "author": "Susanna Clarke",
    "pages": 272,
    "year": 2020
  }
]
```

(Transcript `t11-conversions.txt` —
`to json` prints numbers as numbers and
strings as strings, because the types
were real all along.) **`to csv`** goes
the other way, back to comma rows:

```text
$ nu -c 'open scores.json | to csv'
player,game,points
Ana,chess,12
Ben,chess,9
Ana,go,7
Cleo,go,15
```

**`to yaml`** and **`to md`** (a Markdown
table, for pasting into documents) work
the same way; the family also covers
TOML, XML, HTML and Nushell's own `nuon`
format. One honest limit, met for real in
the lab and repeated in chapter 12:
`to toml` refused a whole table, because
TOML's top level must be a single record
— `open scores.json | get 0 | to toml`
works, and prints one row as TOML
key-and-value lines. A conversion that
refuses is, once again, the type system
describing a real shape mismatch instead
of producing a file that lies.

## Keeping the result: `save`

Printing a conversion shows it; **`save`**
writes it. `open books.csv | select title
pages | to csv | save shortlist.csv`
leaves a new file beside the original,
and opening it gives the table back,
round trip complete. Nushell can also
`save` a table directly to a name ending
in `.json` or `.csv` and infers the
format from the ending, the same way
`open` did. Either way the rule from
chapter 4 applies in reverse: the format
is chosen at the border, once, in
writing — everything inside the pipeline
stayed data.

Check: from `scores.json`, produce the
JSON for just the `go` games
(`where game == "go" | to json`) and
confirm Cleo's 15 is in it and no chess
row survived.
