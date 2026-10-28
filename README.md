# Nushell for Mere Mortals

Every ordinary shell speaks one language:
text. A command prints lines, the next
command reads lines, and if you want the
third field of the fifth line you count
characters and hope the spacing never
changes. **Nushell** (its friends call it
**Nu**) asks a different question: what if
a pipeline carried **structured data** —
values with names and types — instead of
text? Then `ls` would not print filenames
for you to scrape; it would hand you a
**table** with columns called `name`,
`type`, `size` and `modified`, and you
could ask for the rows where `size` is
bigger than a hundred bytes, by name, the
way you would ask a spreadsheet.

That one change — tables instead of text —
is the whole idea of this guide. Everything
else follows from it. A CSV file stops
being lines with commas and becomes a
table whose `pages` column holds numbers
you can add up. A JSON file becomes the
same kind of table, so skills transfer
between formats. Converting results to
JSON, CSV or YAML is one short command,
because the data was never flattened into
text in the first place. And the shell
keeps track of **types** — it knows a
string from a number — and refuses, out
loud and with a diagram, to add the two
together.

This guide explains how that works, walks
through opening real files of data and
filtering, sorting and reshaping them,
shows where ordinary external programs
fit in (they do; Nushell runs them like
any shell), covers small scripts and the
configuration files, and states the
limits plainly — including the honest one
that a great deal of the world's tooling
still speaks text, and Nushell has to
meet it halfway.

The lab behind this guide ran the real
thing: **Nushell 0.116.1**, the official
release build for Linux, exercised
against small fixture files written for
the purpose — a five-row catalogue of
invented books, a four-row table of game
scores, a deliberately messy CSV whose
number column contains the word
"unknown". Against those fixtures the lab
listed directories as tables and filtered
them by size, opened CSV, JSON, TOML and
YAML files and filtered and sorted their
rows by column name, grouped scores by
player, built new columns with `each` and
`insert`, converted tables to JSON, CSV,
YAML and Markdown, ran external commands
and mixed them into pipelines, wrote and
ran a small script with its own command
in it, loaded a configuration file and
read a setting back — and provoked the
type system into refusing, twice, to do
arithmetic on words, then fixed the data
and watched the same sum succeed.

What the lab could not do, it did not
fake. Every session here was driven
non-interactively, one command at a time,
so the interactive side of Nushell — its
completions, menus and line editing while
you type — is taught from the project's
documentation and labelled as such
wherever it appears. Installing on
Windows and macOS, and through package
managers, is likewise documented rather
than demonstrated: the one install route
proven here is the official release
tarball on Linux. `criteria.md` keeps the
full ledger of what was run and what was
only read.

## What this guide covers

- [**What Nushell is**](what-nushell-is.md) —
  the mental model: pipelines of values
  instead of lines of text, and what a
  table really is
- [**Installing Nushell**](installing-nushell.md) —
  the official release tarball proven in
  the lab, the other routes named, and
  the version check that proves the
  install worked
- [**A first look**](first-look.md) —
  `ls` as a table, asking `$nu` about the
  shell itself, and `describe`, the
  command that tells you what kind of
  value you are holding
- [**Working with files of data**](working-with-files.md) —
  `open` on CSV, JSON, TOML and YAML, and
  the row tools: `where`, `select`,
  `sort-by`, `get` and `first`
- [**Pipelines and types**](pipelines-and-types.md) —
  the type system: why a string column
  refuses arithmetic, how `describe`
  shows you the problem, and how `into`
  fixes the data instead of the command
- [**Reshaping data**](reshaping-data.md) —
  `each` and `insert` for new columns,
  `group-by` for buckets, and `to` for
  converting a table into JSON, CSV, YAML
  or Markdown, plus `save` to keep it
- [**External commands**](external-commands.md) —
  ordinary programs inside a Nushell
  pipeline: the `^` escape hatch, text
  coming back in, and the `str` family
  for working with it
- [**Scripts and configuration**](scripts-and-config.md) —
  `def` for your own commands, string
  interpolation, and the two configuration
  files, `env.nu` and `config.nu`
- [**Recipes**](recipes.md) — the biggest
  files in a folder, totals from a CSV, a
  quick format swap, and a report row for
  every file
- [**The neighbours**](the-neighbours.md) —
  Bash, PowerShell, jq and spreadsheets:
  what each does with data, and when each
  is the better tool
- [**Limits**](limits.md) — text is still
  the world's default, the syntax is
  still young and changes between
  releases, and the interactive comforts
  this guide could not demonstrate
- [**Troubleshooting**](troubleshooting.md) —
  the missing column, the column of mixed
  types, the conversion that refuses a
  table, and the deprecated command name
- [**Glossary**](glossary.md) — the
  terms, collected in one place

## What it doesn't cover

Writing Nushell **plugins** — separate
programs that extend the shell with new
commands — is pointed at, not taught.
Using Nushell as the login shell for a
whole organisation, and the deployment
questions that come with that, are out of
scope; this guide stays with one person
and their own machine. Deep coverage of
the data formats themselves (what makes
valid JSON, how YAML anchors work)
belongs to the guides for those formats;
here they appear only as things Nushell
opens. And the guide does not argue that
everyone should switch shells: chapter 10
says plainly when the older tools are the
right ones.

## How to use this guide

Read chapter 1 for the model before
running anything — knowing that a
pipeline carries values turns later
surprises into named problems. Chapter 3
is the five-minute tour: if the tables do
not click there, they will not click
later, and you will have lost five
minutes instead of an afternoon. The
fixture files live in
`nushell-lab/fixtures/` and every command
in chapters 4 to 8 runs against them, so
you can reproduce each output exactly;
the lab's transcripts are in
`nushell-lab/transcripts/`. Chapter 5 is
not the optional chapter: the type system
is where Nushell most differs from the
shell you know, and meeting it on a
deliberately broken file here beats
meeting it on your own data later.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Bash for Mere Mortals](https://paulsullivan33-dev.github.io/bash-for-mere-mortals/)
- [PowerShell for Mere Mortals](https://paulsullivan33-dev.github.io/powershell-for-mere-mortals/)
- [Text Processing for Mere Mortals](https://paulsullivan33-dev.github.io/text-processing-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/nushell-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/nushell-for-mere-mortals/blob/main/LICENSE-CODE).
