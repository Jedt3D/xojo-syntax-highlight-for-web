# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [1.0.0] — 2026-03-13

### Added
- **Pygments lexer** (`pygments/xojo.pygments.py`) — Python `RegexLexer` subclass supporting
  case-insensitive Xojo keywords, types, preprocessor directives (`#tag`, `#pragma`, …),
  `//` and `'` comments, double-quoted strings, and decimal/hex/binary number literals.
- **Pygments test suite** (`pygments/test.py`) — covers comments, preprocessor, strings, numbers,
  keywords, types, literals, builtins, and edge cases (keywords inside `#tag` lines, strings, comments).
- **Pygments demo** (`pygments/demo/demo.py`) — generates a styled `output.html` for quick visual verification.
- **Pygments README** (`pygments/README.md`) — integration instructions for file-based and CLI usage.
- **Landing page** (`index.html`) — dark/light themed hub linking all four library demos.
- **CLAUDE.md** — project guidance file for Claude Code sessions.
- **Light/dark theme toggle** on all three JS demo pages (highlight.js, Prism.js, CodeMirror 6),
  with theme persisted in `localStorage` and applied before first paint to avoid flash.

### Changed
- `README.md` — added Pygments section with CLI usage and demo instructions.
- `codemirror/demo/index.html`, `highlightjs/demo/index.html`, `prismjs/demo/index.html` —
  refactored to CSS custom-property theme system; added dark/light toggle button.

## [0.1.0] — 2026-03-12

### Added
- Initial highlight.js grammar (`highlightjs/xojo.highlight.js`) — ES module, case-insensitive keyword arrays.
- Initial Prism.js grammar (`prismjs/xojo.prism.js`) — IIFE, `greedy: true` flags, nested `inside` patterns.
- Initial CodeMirror 6 grammar (`codemirror/xojo.codemirror.js`) — StreamParser with Set-based O(1) keyword lookup.
- CodeMirror test suite (`codemirror/test.mjs`) with `StringStream` mock.
- Demo pages for all three JS libraries.
- `README.md` with comprehensive documentation and Python Simple Web Server instructions.
