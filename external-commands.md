# External Commands

Outcome: run the ordinary programs of
your system from inside Nushell —
because most of them are not going away —
and handle the text they hand back
without giving up the table tools.

## The world outside is still there

Nushell has its own `ls`, `open`, `cd`
and friends, but it launches external
programs exactly the way other shells do:
type the program's name and its
arguments. `git status`, `ssh`, a build
tool, a compiler — if it ran from your
old shell, it runs here, with its output
appearing as text, because text is what
it produces. Nothing about adopting
Nushell asks you to abandon your tools;
the structured world and the text world
share one prompt.

When a name could be either — an external
program whose name matches a Nushell
command — the caret is the escape hatch.
**`^`** before a command means "the
external one, definitely":

```text
$ nu -c '^echo hello-from-external'
hello-from-external
```

(Transcript `t13-external.txt`.)
`^ls` likewise runs the system's `ls`
rather than Nushell's table version. The
caret is also how you spell certainty in
a script that must behave the same on a
machine whose Nushell version orders
things differently.

## Text coming back in

An external program's output arrives as
a string (or a stream of text). It is not
a table, and Nushell does not pretend
otherwise — but the bridge is short.
**`lines`** splits text into a list of
lines, and from there the table tools
apply:

```text
$ nu -c '^ls -la | lines | first 3'
╭───┬────────────────────────────────────────────╮
│ 0 │ total 20                                   │
│ 1 │ drwxrwx--- 1 root root  96 Oct 28 17:30 .  │
│ 2 │ drwxrwx--- 1 root root  94 Oct 28 17:30 .. │
╰───┴────────────────────────────────────────────╯
```

Read that output honestly: each line is
still a line — one string, internal
spaces and all. `lines` gives you the
list; it does not give you columns.
Splitting a line into fields is a second,
deliberate step (`split column` or
`split row` on a separator, or `parse`
with a pattern naming the pieces), and
each of those steps can fail on real
output in the old familiar ways. This is
the seam between the two worlds, and
chapter 11 returns to it: the seam is
real, and it is where most of the
friction of living in Nushell lives.

A happier case: when the external
program speaks a format `open` knows,
you do not bridge by hand at all. A
program that prints JSON can be piped
straight into table-hood by saving its
output and opening it, or — for the
programs that accept it — by asking for
their JSON mode and letting Nushell parse
the stream with `from json`. The lab
verified the pattern in reverse in
chapter 6 (Nushell data out as JSON);
the inward direction is the same `from`
family: `from json`, `from csv`, `from
yaml`, each turning text in that format
into values.

## Working on text: the `str` family

Strings in Nushell are not second-class.
The **`str`** family does the everyday
text jobs — `str uppercase` and
`str lowercase` (the lab noted `str
upcase`, the older spelling, now prints
a deprecation warning pointing at the
new name; chapter 12 keeps that story),
`str trim`, `str replace`, `str join`
(which turned the fixture folder's file
names into one comma-separated line in
the lab), `str length`, and `str
contains` for testing. A string is a
value with a type and tools of its own;
it simply is not a number, and chapter 5
showed the shell declining to confuse
the two.

Environment variables, the other place
shells traffic in text, live in **`$env`**,
itself a record: `$env.PWD` is the
working directory as a string, and
`$env | columns` lists the variable names
as data. Chapter 8 sets variables of your
own in `env.nu`.

## Which `ls` did I just run?

A closing habit for this chapter: when a
command behaves like your old shell's,
that is probably because it *is* your old
shell's — run with `^`, or an external
program found on `PATH`. When it behaves
like a table, it is Nushell's own. `which
ls` (and `which --all` when several
candidates exist) tells you which kind a
name resolves to on your machine; in
cases of doubt, that one command ends
the argument.

Check: run `^echo` and `ls` back to back
and confirm the first hands you a line
of text while the second hands you a
table — same prompt, two worlds, both
yours.
