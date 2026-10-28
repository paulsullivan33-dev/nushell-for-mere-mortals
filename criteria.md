# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's fixtures are a handful of invented books, players
   and prices, written for the purpose.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 11 names what the tool
   cannot do: the text seam with external programs, a young
   language that still renames commands, big data held in
   memory, a smaller ecosystem, and the interactive face this
   guide could not demonstrate.
8. **The idea before the mechanics.** Chapter 1 states the one
   idea the whole guide stands on — pipelines of typed values
   instead of text — before any command depends on it, and
   later chapters point back to it where behaviour surprises.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a small Ubuntu Linux sandbox. Nushell
**0.116.1** was installed from the project's official release
tarball (`nu-0.116.1-x86_64-unknown-linux-gnu.tar.gz`, from the
nushell/nushell GitHub releases) into a scratch folder — no
system package, nothing installed outside it — and exercised
against fixture files written for this guide
(`nushell-lab/fixtures/`). All sessions were driven
non-interactively with `nu -c '...'` and script files.
Transcripts live in `nushell-lab/transcripts/`.

Run for real, with transcripts as evidence:

- Install by tarball and `nu --version` printing 0.116.1;
  `version`'s full record (build target, Rust version,
  features) read back (chapter 2, lab runs).
- `ls` as a table in the fixture folder, and `ls | where
  size > 100B` filtering it by filesize (t01, t02).
- `open` on CSV, JSON, TOML, YAML and plain text fixtures;
  `open --raw` returning unparsed lines (t03 and lab runs).
- Row tools on the book catalogue: `where year > 2000`,
  `where author =~ "Le Guin"`, `select`, `sort-by pages`,
  `get 0`, `get title`, `first`, `length` (t03, t04 and lab
  runs).
- `group-by --to-table player` on the scores fixture (t05).
- `each` with string interpolation and `insert` building a
  computed column; `update` replacing one (t06 and chapter
  5's repair).
- Types: `describe` on ints, floats, filesizes, durations,
  bools, lists, records and the book table; the parse-time
  refusal of `"abc" + 1` (t07, t08).
- The messy-column case end to end: `describe` reporting
  `oneof<int, string>`, `math sum` refusing, the
  filter-convert-sum repair printing 215 (t09, t10).
- Conversions: `to json`, `to csv`, `to yaml`, `to md`
  (Markdown), `to toml` succeeding on a record and refusing
  a table, and `save` writing a file that `open` reads back
  (t11, t12 and lab runs).
- External commands: `^echo`, `^ls -la | lines | first 3`,
  external output handled as text (t13 and lab runs).
- A script file with `def` and `main` run by path; typed
  parameters — an `int` parameter refusing a word at parse
  time, a `string` parameter accepting and stringifying
  (chapter 8, lab runs).
- Configuration: `$nu.config-path` and
  `$nu.default-config-dir` read back; a two-line config file
  loaded with `--config` changing `$env.config.table.mode`
  from the default `rounded` to `basic`, both reads kept
  (t15).
- The error catalogue of chapter 12: missing column with its
  `isbn?` remedy, the deprecated `str upcase` warning
  pointing at `str uppercase`, `which --all ls` separating
  built-in from external (t14 and lab runs).
- Every recipe in chapter 9 run against the fixtures,
  including `math avg`, datetime-minus-duration filtering,
  `ls **/*`, and `each` returning records for `sort-by`.

Documented only — described from the project's documentation,
not run, and the text does not claim otherwise:

- Installing through distribution packages, Homebrew, the
  Windows installer and winget, and building from source
  with cargo. The tarball route above is the one install
  this guide proves.
- The interactive face: completions, menus, line editing,
  history behaviour at the prompt, and the `explore` table
  browser. Every session in the lab was non-interactive;
  chapters say so where they touch these.
- Plugins: named as the extension mechanism, not written or
  installed.
- Using Nushell as a login shell for a machine or an
  organisation.
- The comparison tools in chapter 10 are characterised, not
  benchmarked.

Fixtures are this guide's own and deliberately generic: a
five-row catalogue of real published books used purely as
sample data, four invented game scores, a three-row CSV with
one deliberately unrecorded page count, two dishes with
prices, a two-line text file, and a two-line configuration.
No example is modelled on any real person, network, service
or organisation.
