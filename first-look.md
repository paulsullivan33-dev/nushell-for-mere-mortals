# A First Look

Outcome: three habits that the rest of
the guide leans on — reading a table,
asking the shell about itself, and asking
any value what kind of value it is.

## `ls` is a table

In the lab's fixture folder:

```text
$ nu -c 'ls'
╭───┬───────────────┬──────┬───────┬──────────╮
│ # │     name      │ type │ size  │ modified │
├───┼───────────────┼──────┼───────┼──────────┤
│ 0 │ books.csv     │ file │ 257 B │ now      │
│ 1 │ greet.nu      │ file │  84 B │ now      │
│ 2 │ menu.yaml     │ file │  49 B │ now      │
│ 3 │ messy.csv     │ file │  44 B │ now      │
│ 4 │ myconfig.nu   │ file │  67 B │ now      │
│ 5 │ notes.txt     │ file │  41 B │ now      │
│ 6 │ prices.csv    │ file │  28 B │ now      │
│ 7 │ scores.json   │ file │ 204 B │ now      │
│ 8 │ settings.toml │ file │  48 B │ now      │
╰───┴───────────────┴──────┴───────┴──────────╯
```

(Transcript `nushell-lab/transcripts/t01-ls-table.txt`.)
Each row is a **record** — one file, with
named fields. Each column is one field
across all the rows. The `#` column is
the shell numbering rows for you; it is
not part of the data. The `modified`
column says `now` because the lab had
just created the files; on your machine
it will show real dates, as values of a
date-and-time type, not as text that
merely looks like one.

Because the listing is data, ordinary
questions become one-liners. Keep only
two columns:

```text
$ nu -c 'ls | select name size'
```

Keep only the rows that pass a test —
the subject of chapter 4 — or count the
rows with `ls | length`. Nothing here
parsed anybody's output. That is the
point of the design, felt for the first
time.

## Asking the shell about itself: `$nu`

Nushell keeps a record of facts about
itself in the variable **`$nu`**: where
its configuration lives, where its
history is kept, and more. Variables in
Nushell start with `$`, and a record's
fields are reached with a dot:

```text
$ nu -c '$nu.config-path'
/home/hatch/.config/nushell/config.nu
```

```text
$ nu -c '$nu.default-config-dir'
/home/hatch/.config/nushell
```

(Your paths will name your own home
folder.) The full record is one command
away — `$nu` on its own prints it as a
two-column table of names and values —
and the `version` command does the same
for the build: version, the commit it was
built from, the Rust compiler that built
it, and the list of optional features
compiled in. Neither command is a
curiosity: when an example in this guide
behaves differently on your machine,
`version` and `$nu.config-path` are the
first two facts to compare.

## Asking a value what it is: `describe`

The third habit is the one chapter 5
depends on. Pipe any value into
**`describe`** and the shell names its
type:

```text
$ nu -c '3 | describe'
int
$ nu -c '3.5 | describe'
float
$ nu -c '1mb | describe'
filesize
$ nu -c '2hr | describe'
duration
$ nu -c 'true | describe'
bool
$ nu -c '[1 2 3] | describe'
list<int>
$ nu -c '{name: "x"} | describe'
record<name: string>
```

(Each line is a separate run from the
lab.) Read the interesting ones slowly.
`1mb` is not the characters "1mb" and not
the number 1: it is a *filesize*, and it
will compare correctly against the `size`
column of `ls`. `2hr` is a *duration*.
`[1 2 3]` is a list, and `describe` says
what the list holds. And `{name: "x"}` —
curly braces build a record on the spot —
is a record with one field, `name`,
holding a string.

A table describes itself with its whole
shape. For the book catalogue the next
chapter opens:

```text
$ nu -c 'open books.csv | describe'
table<title: string, author: string, pages: int, year: int> (stream)
```

Every column with its type, in one line.
When a pipeline misbehaves later in this
guide, it is `describe` that shows why.
Learn to reach for it the way you would
reach for a torch: early, and before
guessing.

Check: in any folder, run `ls | describe`
and confirm you get a `table<...>` line
naming `name`, `type`, `size` and
`modified` with their types.
