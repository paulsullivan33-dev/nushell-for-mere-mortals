# Scripts and Configuration

Outcome: write a Nushell script with a
command of your own in it, and know which
of the two configuration files holds
what — and how to prove a setting took
effect.

## Your own commands: `def`

A pipeline you type twice wants to become
a command. **`def`** defines one: a name,
its inputs in brackets with their types,
and a block for its body. The lab's
script, `nushell-lab/fixtures/greet.nu`,
is the whole idea in eight lines:

```nu
def greet [name: string] {
  $"Hello, ($name)!"
}

def main [] {
  greet "reader"
}
```

Run it with `nu greet.nu` and it prints
`Hello, reader!`. Three details carry
weight. The input is declared `name:
string` — a typed input, and the typing
is real where a conversion is impossible:
declare `[a: int, b: int]` and call the
command with a word, and it is refused
before running, *parse mismatch: expected
int*. (A `string` input, honestly, is
more permissive: it accepts most values
and writes them as text, so there the
annotation is documentation first and a
gate second.) The string `$"Hello,
($name)!"` is interpolation again:
parentheses inside a `$"..."` string are
evaluated and stitched in. And **`main`**
is the script's entry point — a command
named `main`, with no inputs, is what
`nu file.nu` runs. A file of `def`s with
no `main` is a library you load from
other scripts instead.

Variables inside scripts are made with
`let`, are immutable by default (there
is `mut` for the rare counter that
genuinely changes), and hold values of
every kind this guide has met:

```text
$ nu -c 'let total = (open books.csv | get pages | math sum); $"Total pages: ($total)"'
Total pages: 1391
```

The parentheses run a pipeline and take
its value; `let` keeps it; interpolation
reports it. That is most of scripting in
one line.

## The two configuration files

Nushell's own behaviour is set in two
files, both plain Nushell code, living
where `$nu` points (chapter 3) —
`~/.config/nushell/` on the lab's Linux
machine:

- **`env.nu`** runs first and is for the
  *environment*: variables in `$env`,
  including `PATH`. It runs in contexts
  where the full configuration may not
  load, so it stays lean by design.
- **`config.nu`** runs after it and holds
  everything else in `$env.config`: how
  tables are drawn, whether the greeting
  banner appears, history behaviour,
  your own `def`s and shortcuts
  (**aliases**, `alias ll = ls -l`, work
  in either file).

The lab's fixture `myconfig.nu` is two
lines of the second kind:

```nu
$env.config.show_banner = false
$env.config.table.mode = "basic"
```

Loaded as a session's configuration, the
setting reads back as a plain value —
and reads back *differently* from the
default, which is what proves it took:

```text
$ nu -c '$env.config.table.mode'
rounded
$ nu --config myconfig.nu -c '$env.config.table.mode'
basic
```

(Transcript `t15-config.txt`; `rounded`
is Nushell's factory default, `basic`
the fixture's choice.)
That read-back is the habit to keep:
configuration in Nushell is code that
sets values in a record, so any setting
can be *asked* — `$env.config` on its own
draws the whole record as a table, and
`$env.config.table` the table-drawing
corner of it. If a setting does not seem
to apply, the cause is usually one of
two, both discoverable: the line is in
the wrong file (`env.nu` versus
`config.nu`), or the value is being set
and then set again later in the same
file by a line you have forgotten.

Editing either file has no effect on
shells already running; new shells read
the new text. And because the files are
ordinary code, a broken edit breaks
startup loudly rather than silently — the
error names the file and the line, and
commenting lines out with `#` until the
shell starts is the honest bisect.

## Where scripts live

The lab kept its script beside its data
and ran it by path, which is the right
shape for project scripts: `nu
tools/report.nu`, committed with the
project, behaving the same for whoever
clones it. Personal commands you want at
every prompt belong as `def`s in
`config.nu`. The line between the two is
the audience: a command for a project
travels with the project; a command for
you travels with your configuration.

Check: write the two-line `myconfig.nu`
above into your configuration folder as
part of `config.nu`, start a fresh `nu`,
and confirm `$env.config.table.mode`
answers `basic`.
