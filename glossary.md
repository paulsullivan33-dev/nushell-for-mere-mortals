# Glossary

**alias** — a shortcut name for a longer
command, defined in a configuration file:
`alias ll = ls -l`. Interactive shells
load them; scripts should not depend on
them.

**block** — the code between braces,
`{ ... }`, run by commands like `where`,
`each` and `insert` once per row, with
the row available inside it.

**closure** — Nushell's name for a block
treated as a value, the thing `each`
receives. The glossary keeps the plainer
word *block* in the chapters.

**column** — one named field across
every row of a table: `pages` in the
book catalogue. `select` chooses
columns; `where` filters rows.

**conversion (`into`, `from`, `to`)** —
the three borders where shapes change:
`into int` changes a value's type inside
a pipeline, `from json` parses text into
values, `to json` renders values as text
in a format.

**`describe`** — the command that names
a value's type, including a table's full
shape. The guide's torch: reach for it
before guessing.

**duration** — a length of time as a
value: `2hr`, `1day`, `15min`. Durations
add to datetimes and compare with each
other.

**external command** — a program that is
not part of Nushell, run from its prompt
like from any shell. The `^` prefix
forces the external meaning of a name.

**filesize** — an amount of storage as a
value: `257 B`, `1mb`, `500b`. The `size`
column of `ls` holds filesizes, which is
why `where size > 100B` compares
quantities, not text.

**`$env`** — the record holding the
environment variables, `$env.PWD` and
friends. `$env.config` inside it holds
the shell's settings.

**`$nu`** — the record of facts about
the running shell itself: version
details live under the `version`
command, paths under `$nu` — config
path, history path, and more.

**pipeline** — commands joined by `|`,
each receiving the previous command's
value. In Nushell the values are typed
data, not text, which is the whole
subject of this guide.

**record** — one set of named fields and
their values: `{name: "x"}`. A row of a
table, standing alone.

**row condition** — the short form of a
`where` test that names columns directly
(`where year > 2000`), meaning "this
row's year".

**stream** — a value arriving piece by
piece rather than all at once; `describe`
marks streamed tables with `(stream)`.
Streaming softens memory use; it does
not change what the value is.

**string** — text as a value. A string
of digits is still a string, and chapter
5 shows the shell refusing to add it to
a number.

**string interpolation** — building a
string with expressions stitched in:
`$"Total: ($total)"`. The parentheses are
evaluated as the string is built.

**table** — a list of records sharing
the same fields, drawn as rows and
columns. The value `ls` and `open` hand
you, and the centre of the design.

**type** — what kind of value something
is: `int`, `string`, `bool`, `datetime`,
`list<int>`, `table<...>`. Types are
checked when operations run, and the
refusals are chapter 5.

**value** — a single piece of typed
data: a number, a string, a record, a
table. What Nushell pipelines carry,
where older shells carry text.
