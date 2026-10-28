# Troubleshooting

Every entry here was met for real in the
lab, in roughly the order a new user
meets them. The pattern underneath all
of them: Nushell's errors are unusually
informative, and the fix is usually in
the error's second paragraph.

## "Cannot find column"

```text
$ nu -c 'open books.csv | get isbn'
Error: nu::shell::column_not_found

  × Cannot find column 'isbn'
  help: If some rows have this column, try using 'isbn?' for optional access,
        or pre-fill using the `default` command
```

(Transcript `t14-missing-column.txt`, cut
down; the drawing points at the `get`.)
The catalogue has no ISBN column, and the
shell says so, then suggests its own
remedies: `isbn?` for "the value where
it exists, nothing where it does not",
and `default` for filling the gaps with
a value of your choosing. Nine times in
ten the cause is duller — a typo, or a
column named `Title` where you typed
`title`; names are case-sensitive.
`open file | columns` lists what the
file actually has, and ends the guessing.

## The sum that refuses

Chapter 5's signature error:
`math sum` answering *only int, float,
filesize, or duration input data is
supported*, with `input type: string`
underneath. The diagnosis is always the
same shape: somewhere up the column is a
value that is not a number, so the
column's type is `oneof<int, string>` or
plain `string`, and the sum is honestly
meaningless. Find the offenders before
repairing: describe the *column*
(`open messy.csv | get pages | describe`)
to see the mixed type in writing, and
list the suspect rows with a `where`
against the known bad values, as chapter
5 did. Then
convert what remains with `into int`, in
the open.

## A conversion refuses a table

`open scores.json | first 1 | to toml`
fails with *only record input data is
supported*, because TOML's top level is
one record, and a table — even a table of
one row — is a list of records. The error
is a shape report, not a malfunction:
take a record (`get 0`) and the same
command succeeds. Other `to` targets
have their own shape rules for the same
reason. When a conversion refuses, ask
what shape the target format actually
is, and hand it that.

## A command name prints a deprecation warning

`str upcase` runs, and warns: *deprecated
in 0.114.0… use `str uppercase` instead*.
Nothing is broken; the project renamed
the command and is keeping the old name
alive for a while. The warning prints to
the error stream, so pipelines still
work, but scripts that must run quiet
should take the hint. Treat any
deprecation you meet the same way: note
the replacement, change the script, and
remember chapter 11's lesson — your
scripts are pinned to a living language,
so `nu --version` belongs in the bug
report.

## The wrong `ls` — or the wrong shell entirely

A command behaves like text when you
expected a table: you almost certainly
ran the external program, by `^`, by a
`PATH` accident, or because you are in
your old shell entirely and `nu -c` never
happened. `which --all ls` inside Nushell
lists every candidate it can see, in the
order it sees them, and the first is the
one that ran. The reciprocal trap — a
script that works at your prompt and
fails under `nu script.nu` — is usually
an interactive-only convenience (an alias
from `config.nu`) that scripts do not
load; scripts should define what they use.

## Configuration that does not take

A setting in `config.nu` seems ignored.
Read it back: `$env.config.table.mode`
asks the running shell what it actually
holds. If the answer is the default, the
line either lives in the wrong file
(chapter 8's `env.nu` versus `config.nu`
split), comes before a later line that
sets the same thing again, or sits in a
file the shell is not reading — and
`$nu.config-path` names the file it *is*
reading, which settles that in one
command. Change, restart the shell (a
running shell does not re-read its
configuration), and read back again.
