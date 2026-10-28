# Recipes

Short, complete answers to recurring
questions, each one built only from
commands the earlier chapters ran for
real. Fixtures are the lab's; the shapes
are yours to reuse.

## The biggest files in this folder

```text
ls | sort-by size --reverse | first 5 | select name size
```

`ls` is a table, `size` is a filesize, so
the sort is a sort of quantities, not of
the characters that print them. For "in
this folder and everything under it",
`ls **/*` builds the recursive table the
same way.

## How many, how much, from a CSV

```text
open books.csv | length
open books.csv | get pages | math avg
open books.csv | group-by --to-table author
```

Count rows, average a column, bucket the
rows by a field. The `math` family also
has `math sum`, `math min`, `math max`
and `math median`, all with chapter 5's
rule: the column must really be numbers,
all the way down.

## Yesterday's files only

```text
ls | where modified > (date now) - 1day
```

`modified` is a datetime and `1day` is a
duration, so "newer than a day ago" is
arithmetic on values, not a date-format
puzzle. Durations written this way —
`2hr`, `15min`, `1day` — are the same
values `describe` named in chapter 3.

## A format swap, both directions

```text
open books.csv | to json | save books.json
open scores.json | to csv | save scores.csv
```

In at one border as data, out at the
other border in the new shape. For a
one-off look rather than a file, drop the
`save` and read the conversion off the
screen, as chapter 6 did.

## A summary line per file

```text
ls | each { |f| $"($f.name): ($f.size)" }
```

`each` over the `ls` table, interpolating
two fields per row. Swap the body for a
record — `{name: $f.name, kb: ($f.size /
1kb)}` — and the recipe returns a table
instead of lines, ready for `sort-by kb`
or a `to csv`. Which shape the answer
takes is a choice you make per row, in
the open.

## The total the messy file was hiding

```text
open messy.csv
| where pages != "unknown"
| update pages { into int }
| get pages
| math sum
```

Chapter 5's repair, kept as a recipe
because real files keep needing it: set
the unrecorded rows aside *in the
pipeline, where a reader can see the
decision*, convert the rest, then add.
If the excluded rows matter, count them
first — `open messy.csv | where pages ==
"unknown" | length` — and report both
numbers.
