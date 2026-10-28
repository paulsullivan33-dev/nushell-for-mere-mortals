# Pipelines and Types

Outcome: understand why Nushell sometimes
refuses a command that looks reasonable,
read the refusal until it names the
problem, and fix the *data* — by
converting it — rather than fighting the
shell.

## Every value has a type, and the types are enforced

Chapter 3 showed `describe` naming types:
`int`, `string`, `filesize`, `duration`,
`bool`, and the rest. The point of types
is not labelling for its own sake. It is
that operations check them. Here is the
smallest possible refusal, run in the
lab:

```text
$ nu -c '"abc" + 1'
Error: nu::parser::operator_incompatible_types

  × Types 'string' and 'int' are not compatible for the '+' operator.
   ╭─[source:1:1]
 1 │ "abc" + 1
   · ──┬── ┬ ┬
   ·   │   │ ╰── int
   ·   │   ╰── does not operate between 'string' and 'int'
   ·   ╰── string
   ╰────
```

(Transcript `nushell-lab/transcripts/t08-string-plus-int.txt`;
the drawing characters are simplified
here, the words are the shell's own.)
Three things about this error are the
Nushell way. It is caught *before*
anything runs — this is a parse-time
refusal, not a crash halfway through a
pipeline. It draws a picture pointing at
the exact characters involved. And it
does not offer to guess: some tools, faced
with `"abc" + 1`, quietly turn one side
into the other and produce *something*;
Nushell's position is that a silent guess
is how wrong answers are born, so it
stops and explains instead.

## The same refusal, wearing work clothes

Real data rarely arrives as tidy as
`books.csv`. The lab's `messy.csv` is
three rows of titles and page counts,
except one count is the word `unknown` —
the sort of thing a human types into a
spreadsheet and a script inherits later:

```csv
title,pages
Alpha,120
Beta,unknown
Gamma,95
```

Watch what the type system does with it.
`open messy.csv | describe` answers:

```text
table<title: string, pages: oneof<int, string>> (stream)
```

Read that `oneof<int, string>` carefully:
the `pages` column is *mostly* numbers
and partly words, and `describe` says so,
in so many words. Nushell inferred what
it honestly could. Now ask for the total:

```text
$ nu -c 'open messy.csv | get pages | math sum'
Error: nu::shell::only_supports_this_input_type

  × Input type not supported.
   ╭─[source:1:1]
 1 │ open messy.csv | get pages | math sum
   · ─────┬─────  ───┬───
   ·      │          ╰── 'math sum' only supports int, float, filesize, or duration input data
   ·      ╰── input type: string
   ╰────
```

(Transcript `t09-messy-math.txt`.)
`math sum` will happily add a column of
numbers; it will not add a column that
*might* contain a word, because the sum
of 120, "unknown" and 95 is not a hard
question, it is a meaningless one. The
error's second line is the diagnosis:
input type, string.

Notice the division of responsibility.
The shell did not refuse to *open* the
file — mixed data is a fact of life, and
representing it honestly as `oneof` is
useful. It refused at the exact operation
where the mixture starts to matter. That
is the type system earning its keep: not
a gate at the border, but a check at the
moment of use.

## Fixing the data: `into`

There are two honest repairs, and which
is right depends on what `unknown` means
in your data. If it means "no count was
recorded", the repair is to set those
rows aside, convert what remains, and
only then add:

```text
$ nu -c 'open messy.csv | where pages != "unknown" | update pages { into int } | get pages | math sum'
215
```

(Transcript `t10-messy-fixed.txt`.)
Two new tools appear here. **`update`**
replaces a column's values: for each row
it runs the block in braces against that
row. Inside the block, **`into int`**
converts the value it receives to an
integer — the `into` family converts
between types (`into string`, `into
float`, `into filesize`, and friends),
and converting a value that cannot
convert is an error, which is why the
`where` had to come first. The sum, 215,
is 120 + 95, and the pipeline says plainly
that Beta was excluded to get it. A
reader of the script can see the decision;
nobody smuggled it inside a formula.

If `unknown` instead means "zero, for our
purposes", the honest pipeline says that
too: `update pages { if $in == "unknown"
{ 0 } else { $in | into int } }`. The
point is not the trick; it is that the
type refusal forced the question — *what
does this word in my data mean?* — to be
asked and answered in writing, where a
text pipeline would have answered it
silently, possibly wrongly, at 2 a.m.

## Types you will meet in pipelines

A short field guide to what `describe`
will tell you as you work:

- **`int`** and **`float`** — whole and
  decimal numbers. `pages + 1` is fine;
  mixing the two widens to `float`.
- **`string`** — text. It has its own
  tools (chapter 7's `str` family) and
  does not pretend to be a number, even
  when it holds digits: `"42" | describe`
  answers `string`, and `"42" + 1` is the
  refusal above with better tailoring.
- **`filesize`** and **`duration`** —
  quantities with units, written `1mb`,
  `2hr`, `500b`. They compare and add
  within their own kind.
- **`datetime`** — a moment in time, such
  as the `modified` column of `ls`.
- **`bool`** — `true` and `false`, the
  output of every comparison and the
  input `where` expects.
- **`list<...>`** and **`record<...>`** —
  the containers, with `describe` naming
  what is inside them.
- **`table<...>`** — a list of records
  sharing a shape; `describe` prints the
  shape, columns and types together.

Check: predict, then run — what does
`open prices.csv | describe` say about
its two columns, and does `open prices.csv
| get price | math sum` succeed? (It
does: `price` is all integers there, and
the sum is 10.)
