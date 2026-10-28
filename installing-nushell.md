# Installing Nushell

Outcome: a `nu` program on your machine
that answers `nu --version` with a
version number, so every later chapter
can be checked against the version it was
written against. This guide's lab ran
**0.116.1**; the commands taught here are
that version's commands.

## The route the lab proved: the release tarball

Nushell publishes finished builds on its
GitHub releases page. For a 64-bit Linux
machine the file is named after the
version and the machine kind:

```bash
curl -LO https://github.com/nushell/nushell/releases/download/0.116.1/nu-0.116.1-x86_64-unknown-linux-gnu.tar.gz
tar xf nu-0.116.1-x86_64-unknown-linux-gnu.tar.gz
```

Unpacking gives a folder with the same
long name, and the shell is the `nu`
program inside it. The lab ran it in
place:

```bash
./nu-0.116.1-x86_64-unknown-linux-gnu/nu --version
```

```text
0.116.1
```

That printed version is the check: if
yours prints a number, the shell works.
To make `nu` available everywhere, copy
the program into a folder on your `PATH`
— `~/.local/bin` is the usual private
choice — or add the unpacked folder to
`PATH` in your current shell's startup
file. On a machine with several users, a
system package (next section) is the
tidier answer; the tarball makes no
changes outside the folder it unpacks
into, which is exactly why the lab used
it: nothing to undo.

Two naming traps from the releases page.
The builds come in flavours per operating
system and processor — pick the one that
matches yours (`x86_64` for most Intel
and AMD machines, `aarch64` for ARM),
and on Linux prefer the `gnu` build
unless you have a reason to want the
`musl` one; both are the same shell. And
the version in the file name is part of
the download address, so when you read
this later, substitute the current
version number in both places — the
folder name changes with it.

## The other routes, documented only

The lab installed only the tarball, so
these are described from the project's
documentation and not demonstrated:

- **Package managers.** Many Linux
  distributions carry a `nushell` package,
  and on macOS Homebrew offers one;
  Windows has an installer and a `winget`
  package. These are the comfortable
  routes on a machine you keep, with one
  honest caveat: a distribution's package
  can lag the project's releases, and
  chapter 11 explains why the gap matters
  more for Nushell than for older shells.
- **Building from source.** Nushell is
  written in Rust and can be built with
  `cargo install nu`. It takes a while
  and a Rust toolchain; it is the route
  for people who want the newest code or
  a platform with no published build.

Whichever route you take, end with the
same check the lab used: `nu --version`,
and compare the number with the one at
the top of this chapter before trusting
an example that behaves differently on
your machine.

## Starting it

Run `nu` with no arguments and you are
in the interactive shell — the prompt,
completions and menus that chapter 11
and the project documentation describe;
the lab did not drive that face, and
this guide labels everything it says
about it. Run `nu -c 'ls'` and Nushell
runs one command line and exits, which
is how every example in this guide is
written, so each can be pasted into any
terminal, checked, and kept or discarded
on its own merits. Run `nu somefile.nu`
and it runs a script — chapter 8 writes
one.

One first-run behaviour worth knowing:
on its first interactive start, Nushell
offers to create its configuration files
(the pair chapter 8 explains, under
`~/.config/nushell` on Linux). Nothing in
chapters 3 to 7 depends on them, so you
can accept or postpone the offer without
affecting the tour.
