# Working with Files of Data

Outcome: open the lab's fixture files —
CSV, JSON, TOML and YAML — as tables, and
use the five row tools you will use
daily: `where`, `select`, `sort-by`,
`get` and `first`.

## `open` knows the common formats

The lab's `books.csv` is a plain
spreadsheet export: a header line naming
four columns, then five rows of invented
books. In a text shell it is five lines
with commas in them. Here:

```text
$ nu -c 'open books.csv'
╭───┬────────────────────────────────┬───────────────────┬───────┬──────╮
│ # │             title              │      author       │ pages │ year │
├───┼────────────────────────────────┼───────────────────┼───────┼──────┤
│ 0 │ The Left Hand of Darkness      │ Ursula K. Le Guin │   304 │ 1969 │
│ 1 │ A Wizard of Earthsea           │ Ursula K. Le Guin │   183 │ 1968 │
│ 2 │ The Making of Prince of Persia │ Jordan Mechner    │   320 │ 2020 │
│ 3 │ Piranesi                       │ Susanna Clarke    │   272 │ 2020 │
│ 4 │ The City & The City            │ China Mieville    │   312 │ 2009 │
╰───┴────────────────────────────────┴───────────────────┴───────┴──────╯
```

(Transcript `nushell-lab/transcripts/t03-open-csv.txt`.)
`open` looked at the `.csv` ending, parsed
the file, and — the part worth pausing on
— *worked out the types*: `pages` and
`year` came back as integers, not text.
That inference is why the next section's
filter can compare years with `>`.

The same command, unchanged, opens the
other fixtures. `open scores.json` gives
a four-row table of players, games and
points. `open menu.yaml` gives a two-row
table of dishes and prices. `open
settings.toml` gives a record holding a
nested record, because TOML's shape is a
tree rather than a grid — Nushell shows
it as one, honestly, instead of forcing
it flat. And `open notes.txt`, a file
with no structure to infer, gives back
its text as a string: the shell does not
invent structure that is not there.

If you want the raw text of a structured
file — to look at the commas themselves —
`open --raw books.csv` returns the file
unparsed, as lines of text.

## Keeping rows: `where`

**`where`** keeps the rows for which a
test is true. The test names columns
directly; in a row condition, a bare
column name means "this row's value in
that column":

```text
$ nu -c 'open books.csv | where year > 2000'
```

returns the three books from this
century. Text tests work the same way,
and `=~` asks whether a string matches a
pattern:

```text
$ nu -c 'open books.csv | where author =~ "Le Guin" | select title author'
╭───┬───────────────────────────┬───────────────────╮
│ # │           title           │      author       │
├───┼───────────────────────────┼───────────────────┤
│ 0 │ The Left Hand of Darkness │ Ursula K. Le Guin │
│ 1 │ A Wizard of Earthsea      │ Ursula K. Le Guin │
╰───┴───────────────────────────┴───────────────────╯
```

That **`select`** in the same pipeline is
the column tool: keep only the columns
named, in the order named. Together,
`where` for rows and `select` for columns
answer most everyday questions about a
file in one line.

## Ordering and reaching: `sort-by`, `get`, `first`

**`sort-by`** orders rows by a column —
`sort-by pages` puts the thinnest book
first; add `--reverse` for the other
direction. Combined with `where` and
`select`, the lab's "recent books,
thinnest first, titles and page counts
only" is a single readable pipeline
(transcript `t04-where-sort-select.txt`).

**`get`** reaches into a value. On a
table, `get title` returns that column
as a list of values. On the table itself,
`get 0` returns row zero as a record,
drawn standing up:

```text
$ nu -c 'open books.csv | get 0'
╭────────┬───────────────────────────╮
│ title  │ The Left Hand of Darkness │
│ author │ Ursula K. Le Guin         │
│ pages  │ 304                       │
│ year   │ 1969                      │
╰────────┴───────────────────────────╯
```

**`first`** takes the first rows of
whatever it receives — `first 2` after a
`sort-by` is the everyday "top two"
question — and `length` counts rows, so
`open books.csv | where year > 2000 |
length` answers 3 without showing the
books at all.

## Tables from nothing

You do not need a file to get a table.
Square brackets build a list, double
brackets with a header row build a table
inline:

```text
$ nu -c '[[name age]; [Ana 31] [Ben 27]] | where age > 28'
╭───┬──────┬─────╮
│ # │ name │ age │
├───┼──────┼─────┤
│ 0 │ Ana  │  31 │
╰───┴──────┴─────╯
```

Names and ages here, like every fixture
in this guide, are invented for the
example. This inline form is how the
recipes in chapter 9 build test data, and
how you can try any command in this
chapter in an empty folder.

Check: open `scores.json`, keep the rows
where `points` is over 10 with `where`,
and confirm you get Ana's chess score
and Cleo's go score, and nothing else.
