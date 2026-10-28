# The Neighbours

Nushell did not invent its neighbourhood.
Four older tools work the same ground,
and each is better at something. This
chapter characterises them — it does not
benchmark them — so you can tell which
job belongs to which tool.

## Bash and the text tradition

Bash (and its relations) is the shell
this guide's reader most likely already
has: pipelines of text, a universe of
small programs that read and write lines,
fifty years of scripts and muscle memory.
On text's home ground — log files,
compiler output, the output of programs
that print reports — Bash plus the text
tools is not a legacy choice, it is the
native one, and the *Text Processing for
Mere Mortals* guide teaches it properly.
Nushell's honest disadvantage there is
chapter 7's seam: external output arrives
as strings and must be split and parsed
by hand. Where Nushell pulls ahead is
the ground this guide stood on: files of
structured data, where Bash's answer is
to scrape and Nushell's is to ask.

The two also coexist without drama.
Nushell runs Bash scripts as external
programs; Bash runs `nu -c '...'` the
same way. Trying Nushell costs one
install, not a migration.

## PowerShell

PowerShell had the structured idea
first, on the Windows side: its pipelines
carry objects — live .NET objects, with
methods as well as fields — and its
`Get-ChildItem` is `ls`-as-data years
before Nushell. If your work lives among
Windows servers, Active Directory and
.NET, PowerShell is not the neighbour,
it is the landlord, and the *PowerShell
for Mere Mortals* guide covers it. The
differences are of shape and soil.
PowerShell's objects are rich and
platform-flavoured, and its tables are
mostly a display you get at the end;
Nushell's values are plain data — records,
lists, tables — in a smaller, more
uniform language that behaves the same
on Linux, macOS and Windows. Which
matters more is a property of your
fleet, not of the tools' virtue.

## jq

jq is not a shell; it is a superb filter
for one format. If the whole job is
"this JSON, reshaped", jq is shorter to
type and bottomless in depth, and the
*jq for Mere Mortals* guide teaches it.
Nushell's counter is breadth: the same
`where` that filters JSON filters `ls`
and a CSV, and the answer can stay in
your shell as a value instead of leaving
as text. Plenty of people keep both —
jq inside scripts that must run anywhere,
Nushell at the prompt where the data is
being explored.

## A spreadsheet

For a file a human will keep editing,
share with non-programmers, and print,
the spreadsheet (the *LibreOffice for
Mere Mortals* guide covers a real one)
is the right home: formulas live with
the data, and the grid *is* the
interface. Nushell meets spreadsheets at
the border — a CSV export opens as a
table, gets filtered, grouped and
totalled, and can be saved back as CSV —
and that border is exactly where it
shines, because the questions in chapter
9's recipes are the ones a spreadsheet
answers slowly and a text shell answers
fragilely. It is a workbench next to the
spreadsheet, not a bid to replace it.
