# Limits

An honest chapter, in the series'
tradition: what Nushell cannot do, does
awkwardly, or has not finished doing.
None of these are reasons to avoid it.
All of them are reasons to adopt it with
your eyes open.

## Text is still the world's default

Most programs print lines. Chapter 7
showed the bridge — `lines`, `split`,
`parse` — and named it a seam: each
crossing is manual, and each manual step
can fail on a filename with a space, a
column that moved, a locale that changed
a decimal point. A shell whose native
citizens are tables lives in a country
whose imports are text. You will feel
this daily, in proportion to how much of
your work is other programs' output
rather than files of data.

## The language is young, and it shows

Nushell has not promised the deep
stability of a forty-year-old shell, and
its releases still rename and retire
things. The lab met a small, friendly
example: `str upcase` works but prints a
deprecation warning — *deprecated in
0.114.0, use `str uppercase` instead* —
in a shell whose version is 0.116.1.
That is a healthy project's growing
pains, and the warning itself is well
mannered. It also means a script written
against one release can need touches for
the next in a way a Bash script from
2009 does not, and that the version skew
the distribution packages carry (chapter
2) can change an example's behaviour.
Pin versions in scripts that matter, the
way this guide pinned its own.

## The interactive face is a separate skill

This guide verified Nushell the way a
script uses it: one command at a time,
non-interactively. The interactive shell
— completions that understand command
arguments, menus, the line editor, the
`explore` table browser — is a large part
of why people love living in it, and
none of that love is evidence this guide
gathered. The project's documentation
describes it well; treat this guide's
silence there as an untested area, not
a verdict either way.

## Big data is not its promise

`open` reads a file into values. A CSV
with millions of rows will be a table
with millions of rows, in memory, at
whatever speed your machine reads at —
there is no index, no query planner, no
server deciding what to scan. (Nushell
does stream many operations, which the
`(stream)` in `describe`'s answers was
quietly reporting; streaming softens the
memory story, it does not rewrite it.)
For data at that scale, the database
guides in this series are the right
country, and Nushell is a fine way to
visit their borders with exports.

## The ecosystem is smaller

Fewer decades means fewer answers.
Questions about Bash have been asked and
answered so many times that a search
finds your exact error; questions about
Nushell increasingly have, and sometimes
have not, and the honest fallback is the
project's own book and source. Plugins —
the extension mechanism chapter 1 of the
README declined to teach — exist and
work, but the catalogue is a village
next to the text tools' city.

## One shell, held honestly

The last limit is a temptation rather
than a flaw. A uniform language across
`ls`, CSV and JSON can make the older
tools feel shabby by comparison, and
they are not: they are the right tools
on their own ground (chapter 10), and
your colleagues' scripts, your servers'
defaults and your own muscle memory all
live there too. Nushell earns its place
the way this guide tried to — question
by structured question — not by asking
you to forget how the rest of the world
works.
