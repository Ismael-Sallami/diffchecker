# diffchecker

A text comparison tool that runs entirely in the browser: compare two files, merge
them block by block and download the result, with nothing leaving the machine.

## Context

Built for [El Blog de Ismael](https://elblogdeismael.github.io), an archive of
university coursework, alongside two other browser-only tools. It exists because
comparing two versions of a set of notes meant either pasting them into a website
or installing something, and neither was acceptable for coursework that is not
mine to upload. Solo work.

## The problem

A diff viewer is easy to get half right. The hard parts are three:

- **Long files.** A naïve edit-distance algorithm is quadratic and stops being
  usable well before the sizes people actually compare.
- **Moved blocks.** A minimal diff is not the same as a readable one. When a
  paragraph moves, the shortest edit script tends to shred it into unrelated
  insertions and deletions.
- **Merging.** Showing a difference is one thing; letting someone pick sides block
  by block and hand back a file is another, and that file has to be exactly right.

## The solution

The engine is **Myers (1986)**, greedy with a trace, run over the lines after the
options have normalised them. When the edit distance blows up it stops, splits the
work at **anchors of unique common lines** — the idea behind patience diff — and
recurses. That keeps it usable on long inputs and, as a side effect, produces far
more readable blocks when text has been moved around.

The decision that shapes the rest of the code is this one:

> What the engine returns is not only what gets painted. It is also what the
> download button writes.

So `merge()` works over the same rows that are displayed, rather than
recomputing anything, and the test suite checks that taking one whole side back
returns that side **byte for byte**. A defect here is the worst kind: the screen
looks right and the downloaded file is not.

The engine is exposed as `window.DIFF` in the browser and through
`module.exports` under Node. That second half is not decoration — it is what
allows the tests to run the real thing instead of parsing it.

## Layout

```
src/index.html        the page
src/js/diff.js        the engine: Myers with a trace, anchors when it blows up
src/js/app.js         the interface: panels, merging, files, theme
src/assets/           favicon, social image and the stylesheet
tests/                the engine's property checks
docs/usage.md         what each control does
```

`src/assets/css/tool.css` comes from the blog's design system, where it is
concatenated from three layers. It is copied here so the tool runs from a clean
clone; it is edited in the blog, not here.

## Requirements

Node 20 or newer to run the tests. Nothing to install: **there are no
dependencies**, no CDN and no build step.

## Build and run

```bash
git clone https://github.com/Ismael-Sallami/diffchecker.git
cd diffchecker

# Use it: any static server, or just open the file
python3 -m http.server -d src 8000    # then http://localhost:8000

# Test it
node tests/check-diff-engine.mjs
```

## Results

```
Diff engine: 16 fixed cases, 200 random pairs (seed 20260814),
plus options and word level checks.
Every property holds.
```

The cases come from a seeded pseudo-random source, so a failure is reproducible:
rerun with the seed the report prints. Every property is checked by comparing
whole strings, never counts — two outputs with the same number of lines are not
the same output.

The properties checked are the ones that would let a wrong file be written:
taking all of the left side returns the left input, the same for the right, an
unchanged pair produces no blocks, and the row list and the merge always agree.

## What I learned

- **A minimal diff and a readable diff are different goals.** Myers gives the
  first. The anchors give the second, and the two only coexist because the
  algorithm falls back to them instead of being replaced by them.
- **Testing the thing that ships beats testing a copy of it.** Making the engine
  loadable from Node was a few lines and turned the test suite from a
  reimplementation into a check of the real code.
- **The limitation**: comparison is line-based, with word-level highlighting
  inside a changed line. It has no notion of syntax, so moving a function in a
  source file reads as a deletion and an insertion in different places even when
  the anchors keep the blocks tidy. Nor does it handle binary input or files
  large enough to matter for memory — everything is held as strings.

## Author and licence

Ismael Sallami Moreno. Released under the MIT licence (see `LICENSE`).

Deployed at [elblogdeismael.github.io/diffchecker](https://elblogdeismael.github.io/diffchecker/),
which is why the canonical URL and the navigation in `src/index.html` point
there. That site keeps its own copy of this code: **if the engine changes, both
have to change.**
