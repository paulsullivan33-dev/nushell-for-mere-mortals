# What Nushell Is

Think about what an ordinary shell
actually moves around. You run `ls`, and
what comes back is *text* — lines of it,
arranged in columns by spaces. You run a
command to find the big files, and it
works by printing more text, which a
third command reads, splits on those same
spaces, and hopes the filenames contain
no spaces of their own. This works, after
fifty years of practice, the way a rope
bridge works: genuinely, and with a
certain amount of swaying.

**Nushell is a shell that moves values
instead of text.** A **value** is a piece
of data with a shape and a type: a
number, a piece of text (a **string**), a
list, or a **record** — a set of named
fields, like one row of a spreadsheet
with its column headings attached. When
commands pass values along a **pipeline**,
the next command receives the shape, not
a picture of the shape. It does not have
to guess where one field ends and the
next begins. It *knows*.

The value you will meet most is the
**table**: a list of records that all
share the same fields, drawn on screen as
rows and columns. `ls` in Nushell does
not print a listing. It builds a table
whose columns are `name`, `type`, `size`
and `modified`, and hands that table to
whatever comes next — including, if
nothing comes next, the part of the shell
that draws it for you to read.

## Why anyone would do this

- **Asking questions by name.** "Show me
  the files bigger than 100 KB" in a
  text shell means finding the size field
  in each line and comparing numbers as
  text. In Nushell it is a filter on the
  `size` column, written the way you
  would say it: `where size > 100kb`.
- **Files of data open as data.** CSV and
  JSON files are tables waiting to
  happen. Nushell's `open` reads them
  into tables directly, so the same
  filter-and-sort skills you used on `ls`
  work on a spreadsheet export or an API
  answer, unchanged.
- **Mistakes are caught early.** Because
  every value has a type, the shell can
  refuse nonsense at the moment you type
  it — adding a number to a column of
  words, say — and show you exactly where
  the mismatch is, instead of producing
  a wrong answer quietly.
- **Conversion is boring, in a good
  way.** Turning a table into JSON or
  CSV is one command (`to json`, `to
  csv`), because the data was never
  flattened into text and does not need
  to be re-parsed back into shape.

## A first taste

After installing (chapter 2), in a folder
with a few files in it:

```text
$ nu -c 'ls | where size > 100B'
╭───┬─────────────┬──────┬───────┬──────────╮
│ # │    name     │ type │ size  │ modified │
├───┼─────────────┼──────┼───────┼──────────┤
│ 0 │ books.csv   │ file │ 257 B │ now      │
│ 1 │ scores.json │ file │ 204 B │ now      │
╰───┴─────────────┴──────┴───────┴──────────╯
```

(From the lab's transcript
`nushell-lab/transcripts/t02-ls-where.txt`.)
Two things to notice. The comparison
`size > 100B` compares a *filesize* to a
*filesize* — `100B` is a value of its
own, not the characters "1", "0", "0".
And the output is still a table, so a
third stage could filter it further, or
`select name` could keep only the names.

Check: run `ls` on its own and confirm
you get a table with the same column
names, one row per file, before any
filtering happens.

## Who it is for

The person whose work keeps landing in
CSV exports and JSON answers, and who is
tired of scraping text to get at them.
The scripter who wants a pipeline to fail
loudly when the data is not the shape
they expected, instead of sailing on with
a wrong number. The curious user of Bash
or PowerShell who wants to see what a
shell built around data, from the start,
feels like. If that last comparison
interests you, chapter 10 puts the
neighbours side by side — including
PowerShell, which had the same structured
idea earlier, with a different accent.

## What it is not

Nushell is not a spreadsheet: it draws
tables, but it is a shell first, and its
arithmetic and statistics commands are a
toolkit, not Excel. It is not a database:
it opens files of data happily, but it
keeps nothing indexed between runs, and
a million-row file will feel like a
million-row file. It is not a replacement
for the text tools on their home ground:
log files, compiler output and the whole
long tail of programs that print lines
are still text, and chapter 7 shows how
Nushell meets them (it runs the same
external programs, and has text commands
of its own) — but nobody should pretend
the meeting is always graceful. And it
is not finished: Nushell is younger than
its rivals, its syntax still changes
between releases in ways chapter 11 names
honestly, and this guide pins everything
it teaches to the version the lab ran.
