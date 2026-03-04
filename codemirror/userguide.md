# Xojo CodeMirror 6 Grammar — User Guide

## Contents

1. [How it works](#1-how-it-works)
2. [Installation](#2-installation)
3. [Usage — Browser (CDN)](#3-usage--browser-cdn)
4. [Usage — npm / bundler](#4-usage--npm--bundler)
5. [Adding a theme](#5-adding-a-theme)
6. [Read-only viewer](#6-read-only-viewer)
7. [Dynamic language switching](#7-dynamic-language-switching)
8. [Token reference](#8-token-reference)
9. [Preprocessor directives](#9-preprocessor-directives)
10. [Code Walkthrough](#10-code-walkthrough)
11. [Troubleshooting](#11-troubleshooting)

---

## 1. How it works

`xojo.codemirror.js` exports `xojoStreamParser` — a plain JavaScript object implementing the CodeMirror 6 `StreamParser` interface. It has **no runtime dependencies**: it does not import from `@codemirror/language` or any other package.

You wrap it once with `StreamLanguage.define()`:

```js
const xojoLang = StreamLanguage.define(xojoStreamParser);
```

`StreamLanguage` is CodeMirror 6's compatibility layer for token-by-token (stream-based) parsers, similar to the mode system in CodeMirror 5. The returned `Language` object can be used as a CM6 extension directly.

The parser processes source code one token at a time via the `token(stream, state)` method:
- Reads characters from `stream` (a `StringStream`)
- Returns a token type string such as `'keyword'`, `'comment'`, `'string'`, etc.
- Returns `null` for plain identifiers and unstyled characters

Token strings are mapped to Lezer highlight tags internally by `StreamLanguage`, which allows any CM6 theme to apply colour.

---

## 2. Installation

### Option A — Copy the file

Copy `xojo.codemirror.js` into your project alongside your other source files.

### Option B — npm *(coming soon)*

```sh
npm install xojo-syntax-highlight
```

Import path: `xojo-syntax-highlight/codemirror`

---

## 3. Usage — Browser (CDN)

CodeMirror 6 is distributed as ES modules. The safest CDN approach is to use **esm.sh** with an **import map** using the `*` prefix, which pins all shared sub-packages (like `@codemirror/state`) to a single resolved version.

> Without this pinning, multiple packages might load different instances of `@codemirror/state`, causing the editor to throw a "multiple instances" error.

### Minimal setup

```html
<!DOCTYPE html>
<html>
<head>
  <script type="importmap">
  {
    "imports": {
      "codemirror":           "https://esm.sh/*codemirror@6",
      "@codemirror/language": "https://esm.sh/*@codemirror/language@6"
    }
  }
  </script>
</head>
<body>
  <div id="editor"></div>

  <script type="module">
    import { EditorView, basicSetup } from "codemirror";
    import { StreamLanguage }         from "@codemirror/language";
    import { xojoStreamParser }       from "./xojo.codemirror.js";

    new EditorView({
      doc: "Var greeting As String = \"Hello, World!\"",
      extensions: [
        basicSetup,
        StreamLanguage.define(xojoStreamParser),
      ],
      parent: document.getElementById("editor"),
    });
  </script>
</body>
</html>
```

### With One Dark theme

Extend the import map and add the theme extension:

```html
<script type="importmap">
{
  "imports": {
    "codemirror":                 "https://esm.sh/*codemirror@6",
    "@codemirror/language":       "https://esm.sh/*@codemirror/language@6",
    "@codemirror/theme-one-dark": "https://esm.sh/*@codemirror/theme-one-dark@6"
  }
}
</script>

<script type="module">
  import { EditorView, basicSetup } from "codemirror";
  import { StreamLanguage }         from "@codemirror/language";
  import { oneDark }                from "@codemirror/theme-one-dark";
  import { xojoStreamParser }       from "./xojo.codemirror.js";

  new EditorView({
    doc: sourceCode,
    extensions: [
      basicSetup,
      StreamLanguage.define(xojoStreamParser),
      oneDark,
    ],
    parent: document.getElementById("editor"),
  });
</script>
```

---

## 4. Usage — npm / bundler

```sh
npm install codemirror @codemirror/language @codemirror/theme-one-dark
```

```js
import { EditorView, basicSetup } from "codemirror";
import { StreamLanguage }         from "@codemirror/language";
import { oneDark }                from "@codemirror/theme-one-dark";
import { xojoStreamParser }       from "./xojo.codemirror.js";

const editor = new EditorView({
  doc: sourceCode,
  extensions: [
    basicSetup,
    StreamLanguage.define(xojoStreamParser),
    oneDark,
  ],
  parent: document.getElementById("editor"),
});
```

Works with Vite, Webpack 5, Rollup, Parcel, and esbuild without any additional configuration.

---

## 5. Adding a theme

CodeMirror 6 separates the editor theme (UI chrome) from the syntax highlighting style. You can mix and match:

### Built-in dark themes (require `@codemirror/theme-one-dark`)

```js
import { oneDark } from "@codemirror/theme-one-dark";
extensions: [ ..., oneDark ]
```

### Custom syntax highlighting style

Use `HighlightStyle` and `syntaxHighlighting` from `@codemirror/language`:

```js
import { HighlightStyle, syntaxHighlighting } from "@codemirror/language";
import { tags } from "@lezer/highlight";

const xojoHighlightStyle = HighlightStyle.define([
  { tag: tags.keyword,              color: "#c678dd", fontWeight: "bold" },
  { tag: tags.typeName,             color: "#56b6c2" },
  { tag: tags.atom,                 color: "#d19a66" },
  { tag: tags.standard(tags.name),  color: "#e06c75" }, // builtin
  { tag: tags.comment,              color: "#5c6370", fontStyle: "italic" },
  { tag: tags.string,               color: "#98c379" },
  { tag: tags.number,               color: "#d19a66" },
  { tag: tags.meta,                 color: "#e5c07b" },
  { tag: tags.operator,             color: "#61afef" },
]);

extensions: [
  basicSetup,
  StreamLanguage.define(xojoStreamParser),
  syntaxHighlighting(xojoHighlightStyle),
]
```

### Custom editor theme (UI chrome)

```js
import { EditorView } from "codemirror";

const myTheme = EditorView.theme({
  "&": {
    backgroundColor: "#1e1e2e",
    color: "#cdd6f4",
    fontSize: "14px",
  },
  ".cm-content": { caretColor: "#cdd6f4" },
  ".cm-cursor": { borderLeftColor: "#cdd6f4" },
  ".cm-gutters": { backgroundColor: "#1e1e2e", borderRight: "1px solid #313244" },
  ".cm-activeLineGutter": { backgroundColor: "#313244" },
  ".cm-activeLine": { backgroundColor: "#31324430" },
});

extensions: [ basicSetup, StreamLanguage.define(xojoStreamParser), myTheme ]
```

---

## 6. Read-only viewer

Use CodeMirror as a pure syntax highlighter (non-editable) by adding `EditorView.editable.of(false)` and `EditorState.readOnly.of(true)`:

```js
import { EditorView, basicSetup }  from "codemirror";
import { EditorState }             from "@codemirror/state";
import { StreamLanguage }          from "@codemirror/language";
import { xojoStreamParser }        from "./xojo.codemirror.js";

new EditorView({
  doc: sourceCode,
  extensions: [
    basicSetup,
    StreamLanguage.define(xojoStreamParser),
    EditorView.editable.of(false),
    EditorState.readOnly.of(true),
  ],
  parent: document.getElementById("viewer"),
});
```

> This still renders line numbers, active line highlighting, and correct line wrapping — ideal for documentation pages.

---

## 7. Dynamic language switching

If your app supports multiple languages, you can swap the Xojo grammar in or out using a `Compartment`:

```js
import { Compartment }      from "@codemirror/state";
import { StreamLanguage }   from "@codemirror/language";
import { xojoStreamParser } from "./xojo.codemirror.js";

const languageCompartment = new Compartment();

// Initial setup with Xojo
const editor = new EditorView({
  extensions: [
    basicSetup,
    languageCompartment.of(StreamLanguage.define(xojoStreamParser)),
  ],
  parent: document.getElementById("editor"),
});

// Switch to a different language later:
editor.dispatch({
  effects: languageCompartment.reconfigure(StreamLanguage.define(otherParser)),
});
```

---

## 8. Token reference

| `token()` return value | Lezer highlight tag | CSS class (One Dark) | Xojo constructs |
|---|---|---|---|
| `'keyword'` | `tags.keyword` | `.ͳcm-keyword` | `Var` `Dim` `Sub` `Function` `Class` `If` `Return` `Raise` `New` `Inherits` `Public` `Private` … |
| `'operator'` | `tags.operator` | `.cm-operator` | `And` `Or` `Not` `Xor` `Mod` `Is` `IsA` `AddressOf` `WeakAddressOf` |
| `'atom'` | `tags.atom` | `.cm-atom` | `True` `False` `Nil` |
| `'type'` | `tags.typeName` | `.cm-typeName` | `Integer` `Int8`–`Int64` `UInt8`–`UInt64` `Single` `Double` `Boolean` `String` `Variant` `Object` `Color` `Ptr` `Auto` |
| `'builtin'` | `tags.standard(tags.name)` | `.cm-name` | `Self` `Super` `Me` |
| `'comment'` | `tags.lineComment` | `.cm-comment` | `// text` · `' text` |
| `'string'` | `tags.string` | `.cm-string` | `"double quoted"` |
| `'number'` | `tags.number` | `.cm-number` | `42` `3.14` `1.5e3` `&hFF00FF` `&b10101010` |
| `'meta'` | `tags.meta` | `.cm-meta` | `#pragma …` `#tag …` `#if` `#else` `#endif` `#region` (whole line) |
| `null` | — | — | Plain identifiers, punctuation, operators |

---

## 9. Preprocessor directives

`#tag` and `#pragma` lines are matched when the `token()` function sees `#` followed by a known directive word. The **entire line** is consumed and returned as `'meta'`, ensuring words like `Module` in `#tag Module, Name = Utils` are never coloured as Xojo keywords.

| Directive | Purpose |
|---|---|
| `#pragma` | Compiler hints (`DisableBackgroundTasks`, `NilObjectChecking False`, …) |
| `#tag` / `#tag EndModule` | IDE metadata / project file structure markers |
| `#if` / `#elseif` / `#else` / `#endif` | Conditional compilation |
| `#region` / `#endregion` | Code folding markers |

---

## 10. Code Walkthrough

This section explains every part of `xojo.codemirror.js` in detail.

### Overview

`xojo.codemirror.js` exports a single plain object, `xojoStreamParser`, that implements CodeMirror 6's **StreamParser interface**:

```js
export const xojoStreamParser = {
  name: 'xojo',
  startState() { return {}; },
  token(stream, state) { /* ... */ },
};
```

You wrap it with `StreamLanguage.define()` from `@codemirror/language`. `StreamLanguage` is CodeMirror 6's compatibility adapter for token-by-token (stream) parsers. The file has **zero imports** — it is dependency-free.

### Token lookup Sets

```js
const KEYWORDS          = new Set(['var', 'dim', 'sub', ...]);
const OPERATOR_KEYWORDS = new Set(['and', 'or', 'not', ...]);
const TYPES             = new Set(['integer', 'string', ...]);
const LITERALS          = new Set(['true', 'false', 'nil']);
const BUILTINS          = new Set(['self', 'super', 'me']);
const PREPROCESSOR      = new Set(['tag', 'pragma', 'if', ...]);
```

All entries are **lowercase**. When an identifier is matched, it is converted to lowercase before lookup, so `Var`, `VAR`, and `var` all hit the `KEYWORDS` set. `Set` gives O(1) lookup regardless of how many entries it holds.

Six separate Sets (rather than one combined map) let each category return a distinct token type string, which maps to a different Lezer highlight tag and a different colour per theme:

| Set | Return value | Lezer tag | One Dark colour |
|---|---|---|---|
| `KEYWORDS` | `'keyword'` | `tags.keyword` | Purple `#c678dd` |
| `OPERATOR_KEYWORDS` | `'operator'` | `tags.operator` | Blue `#61afef` |
| `TYPES` | `'type'` | `tags.typeName` | Cyan `#56b6c2` |
| `LITERALS` | `'atom'` | `tags.atom` | Orange `#d19a66` |
| `BUILTINS` | `'builtin'` | `tags.standard(tags.name)` | Red `#e06c75` |
| `PREPROCESSOR` | `'meta'` | `tags.meta` | Yellow `#e5c07b` |

### The StreamParser interface

| Method | Called | Returns |
|---|---|---|
| `startState()` | Once, before parsing begins | Initial state object |
| `token(stream, state)` | Repeatedly until end of line | Token type string or `null` |

`state` is threaded through every `token()` call and can be mutated to track multi-line state. This parser returns `{}` from `startState()` and never reads `state` — it is **stateless** because Xojo has no multi-line tokens (no block comments, no multi-line strings).

### StringStream API

| Method | Description |
|---|---|
| `stream.eatSpace()` | Consumes all whitespace at current position; returns `true` if any was consumed |
| `stream.peek()` | Returns the next character without consuming it |
| `stream.next()` | Consumes and returns the next character |
| `stream.match(pattern)` | If `pattern` matches at current position: consumes it, returns the match. Otherwise returns `false`. |
| `stream.skipToEnd()` | Consumes all remaining characters on the current line |
| `stream.eol()` | Returns `true` if the stream is at end of line |
| `stream.current()` | Returns the text consumed since the last `stream.start` reset |

### `token()` logic — step by step

Each call processes **one token** and advances the stream. CodeMirror resets `stream.start` to the current position after each call, then calls `token()` again until `stream.eol()`.

```
eatSpace → // → ' → # → " → &h → &b → decimal → identifier → next()
```

#### Step 1 — Whitespace

```js
if (stream.eatSpace()) return null;
```

`eatSpace()` consumes all consecutive spaces and tabs. Returning `null` means no colour. CodeMirror immediately calls `token()` again at the new position.

#### Step 2 — `//` line comment

```js
if (stream.match('//')) {
  stream.skipToEnd();
  return 'comment';
}
```

`match('//')` consumes exactly two characters if they are `//`. `skipToEnd()` consumes the rest of the line. The entire `// ...` region becomes one `comment` token.

#### Step 3 — `'` apostrophe comment

```js
if (stream.peek() === "'") {
  stream.skipToEnd();
  return 'comment';
}
```

`peek()` reads without consuming. `skipToEnd()` consumes from `'` to end-of-line (CodeMirror has already set `stream.start` to the `'` position, so `current()` would include it).

#### Step 4 — Preprocessor `#directive`

```js
if (stream.peek() === '#') {
  if (stream.match(/#([a-zA-Z]+)/)) {
    const directive = stream.current().slice(1).toLowerCase();
    if (PREPROCESSOR.has(directive)) {
      stream.skipToEnd();
      return 'meta';
    }
  } else {
    stream.next();  // consume lone '#'
  }
  return null;
}
```

`match(/#([a-zA-Z]+)/)` consumes `#tag`, `#pragma`, etc. `stream.current()` returns e.g. `"#tag"`, `.slice(1)` removes the `#`. If the directive is recognised, `skipToEnd()` consumes the **entire remaining line** — so `Module` in `#tag Module, Name = Utils` is never seen by the keyword step. If `match()` fails (e.g. `#123`), `stream.next()` consumes the lone `#` and returns `null`.

#### Steps 5–7 — Number literals

```js
if (stream.match(/&[hH][0-9a-fA-F]+/)) return 'number';  // hex
if (stream.match(/&[bB][01]+/))         return 'number';  // binary
if (stream.match(/\d+(?:\.\d+)?(?:[eE][+-]?\d+)?/)) return 'number';
```

Hex and binary are checked before decimal so that `&` is not consumed as an operator before the `h`/`b` can disambiguate the literal.

#### Step 8 — Identifier and keyword lookup

```js
const word = stream.match(/[a-zA-Z_][a-zA-Z0-9_]*/);
if (word) {
  const w = word[0].toLowerCase();
  if (KEYWORDS.has(w))          return 'keyword';
  if (OPERATOR_KEYWORDS.has(w)) return 'operator';
  if (TYPES.has(w))             return 'type';
  if (LITERALS.has(w))          return 'atom';
  if (BUILTINS.has(w))          return 'builtin';
  return null;  // plain identifier (variable name, class name, etc.)
}
```

`match(/[a-zA-Z_][a-zA-Z0-9_]*/)` consumes a full identifier in one call. The lookup chain returns on the first Set hit. Plain identifiers (variable names, class names) fall through and return `null`.

#### Step 9 — Fallback

```js
stream.next();
return null;
```

Any character not matched above (operators, punctuation, `&` alone) is consumed one character at a time and returns `null`. CodeMirror calls `token()` again immediately from the next character.

---

## 11. Troubleshooting

**"Unrecognized extension value in system slot" / editor renders but no highlighting**

This is almost always caused by multiple instances of `@codemirror/state`. Use the `*` prefix in the esm.sh import map to pin shared dependencies:
```json
"codemirror": "https://esm.sh/*codemirror@6"
```
The `*` prefix tells esm.sh to serve the package with all peer dependencies resolved to the same versions.

**Import map not working in my browser**

Import maps require a modern browser (Chrome 89+, Firefox 108+, Safari 16.4+). For older browsers, use a bundler (Vite, webpack) instead of CDN + import maps.

**`xojo.codemirror.js` fails to load**

The file uses `export const ...` (ES module). Ensure you are serving files over HTTP (not `file://`). Use `python3 -m http.server` during development.

**Keywords inside `#tag` lines are still coloured**

This would indicate the `#` branch of the token function is not matching. Verify the directive name is in the `PREPROCESSOR` set and that `stream.match()` is consuming the word correctly. Check the browser console for any import errors.

**`'` apostrophe comments not highlighted**

The stream parser peeks for `'` and calls `skipToEnd()`. Ensure you are using `xojo.codemirror.js` unmodified. Note: Xojo uses `'` as a comment character — if your source file contains `'` inside a string literal it is correctly left as a string, not a comment (strings are matched before the apostrophe check).

**Hex/binary literals not coloured**

The number patterns are `match(/&[hH][0-9a-fA-F]+/)` and `match(/&[bB][01]+/)`. These must appear BEFORE the `&` is consumed as an operator. The parser checks for `&h`/`&b` before falling through to the general character consumer — this ordering is correct in the shipped file.

**Editor has no line numbers / no autoindent**

These come from `basicSetup`. Make sure to include `basicSetup` in your extensions array.

**How do I disable specific `basicSetup` features?**

Replace `basicSetup` with `minimalSetup` (fewer features) or import individual extensions from `@codemirror/commands`, `@codemirror/search`, etc.:

```js
import { lineNumbers, highlightActiveLine } from "@codemirror/view";
import { history }                          from "@codemirror/commands";

extensions: [
  lineNumbers(),
  history(),
  StreamLanguage.define(xojoStreamParser),
]
```
