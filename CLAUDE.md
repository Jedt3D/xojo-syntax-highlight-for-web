# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Status

**Current phase: v1.0 complete** — All four grammar implementations are working and tested.

Completed:
- highlight.js, Prism.js, CodeMirror 6 grammars (ES module / IIFE / StreamParser)
- Pygments (Python) lexer with test suite and HTML demo
- All three JS demos upgraded with light/dark theme toggle (persisted via localStorage)
- Landing page (`index.html`) linking all four demos
- CLAUDE.md, README, and changelogs in place

## Project Purpose

Syntax highlighting library for the **Xojo programming language**, providing grammar definitions for four highlighters: highlight.js, Prism.js, CodeMirror 6, and Pygments. Each implementation is a single-file, zero-dependency drop-in that correctly handles Xojo-specific syntax that VB-based grammars miss.

## Commands

**Run tests:**
```bash
# CodeMirror
cd codemirror && node test.mjs

# Pygments
python3 pygments/test.py
```

**Run demo server (all JS demos + landing page):**
```bash
python3 -m http.server 8000
# Landing page: http://localhost:8000/
# Demos at: /highlightjs/demo/, /prismjs/demo/, /codemirror/demo/
```

**Pygments demo (generates HTML):**
```bash
python3 pygments/demo/demo.py
# Output: pygments/demo/output.html
```

**Pygments command-line highlight:**
```bash
python3 -m pygments -x -l pygments/xojo.pygments.py:XojoLexer file.xojo_code -f html -O full,style=monokai -o out.html
```

No build step — all four grammars are single-file libraries.

## Architecture

Four independent implementations under `highlightjs/`, `prismjs/`, `codemirror/`, and `pygments/`, each containing:
- `xojo.<lib>.js` — the grammar definition
- `demo/index.html` — interactive demo
- `README.md` / `userguide.md` — integration docs

### Implementation patterns

| Library | Pattern | Key detail |
|---------|---------|-----------|
| `highlightjs/xojo.highlight.js` | ES module, factory function | Case-insensitive keyword arrays |
| `prismjs/xojo.prism.js` | IIFE, self-registers with `Prism.languages['xojo']` | `greedy: true` flags; nested `inside` patterns |
| `codemirror/xojo.codemirror.js` | StreamParser exported as `xojoStreamParser` | Set-based keyword lookup for O(1) performance |
| `pygments/xojo.pygments.py` | `RegexLexer` subclass `XojoLexer` | `flags = re.IGNORECASE`; `words()` helper for keyword lists |

### Shared grammar logic (across all three files)

All three grammars implement the same token categories in this priority order:

1. **Preprocessor directives** — entire lines starting with `#` tagged as `meta`/`preprocessor` (atomic so keywords inside aren't re-tokenized)
2. **Comments** — `//` line comment and `'` apostrophe comment
3. **Strings** — double-quoted, no multiline
4. **Numbers** — decimal (`42`, `3.14`, `1e6`), hex (`&hFF`), binary (`&b1010`)
5. **Keywords** — case-insensitive; main keywords, types, operators, literals (`Nil`, `True`, `False`), builtins (`Self`, `Super`, `Me`)

The preprocessor must be matched first and atomically to prevent keywords like `Module` inside `#tag Module, Name = Utils` from being highlighted as keywords.

### Testing

`codemirror/test.mjs` — Node.js; includes a `StringStream` mock.
`pygments/test.py` — Python; uses `lexer.get_tokens_unprocessed()` directly.

Both test the same categories: comments, preprocessor, strings, numbers, keywords, types, literals, builtins, and edge cases (keywords inside `#tag` lines, inside strings, inside comments).

To add a test, append an entry to the `tests` list in the respective file. Each entry needs `line`, `first` (expected token type of the first non-whitespace token), and `label`.
