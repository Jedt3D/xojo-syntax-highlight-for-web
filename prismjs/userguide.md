# Xojo Prism.js Grammar — User Guide

## Contents

1. [How it works](#1-how-it-works)
2. [Installation](#2-installation)
3. [Usage — Browser](#3-usage--browser)
4. [Usage — Node.js](#4-usage--nodejs)
5. [Usage — Markdown parsers](#5-usage--markdown-parsers)
6. [Choosing a theme](#6-choosing-a-theme)
7. [Token reference](#7-token-reference)
8. [Preprocessor directives](#8-preprocessor-directives)
9. [Code Walkthrough](#9-code-walkthrough)
10. [Troubleshooting](#10-troubleshooting)

---

## 1. How it works

`xojo.prism.js` is a self-executing function that registers `Prism.languages['xojo']` on the global `Prism` object. It must be loaded **after** `prism.js`.

The grammar uses **ordered pattern matching** — patterns earlier in the definition take priority. The order is intentional:

1. `comment` (greedy) — `//` and `'` consume their lines first
2. `string` (greedy) — double-quoted strings
3. `preprocessor` (greedy) — `#tag`/`#pragma` lines, with `inside` to sub-highlight the directive name
4. `keyword`, `operator-keyword`, `builtin`, `boolean`, `type` — word-boundary patterns
5. `number`, `operator`, `punctuation`

The grammar is **not** derived from `vb` — it is a fresh definition to avoid inheriting incorrect Visual Basic patterns.

---

## 2. Installation

### Option A — Copy the file

Download `xojo.prism.js` and place it anywhere in your project.

### Option B — npm *(coming soon)*

```sh
npm install xojo-syntax-highlight
```

---

## 3. Usage — Browser

### Automatic highlighting

Prism highlights all `code[class^="language-"]` elements on `DOMContentLoaded` automatically.

```html
<!-- 1. Stylesheet -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism-tomorrow.min.css">

<!-- 2. Prism core -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/prism.min.js"></script>

<!-- 3. Xojo grammar — must come after prism.js -->
<script src="./xojo.prism.js"></script>
```

```html
<!-- 4. Mark up code — Prism handles the rest -->
<pre><code class="language-xojo">
Var greeting As String = "Hello, World!"
</code></pre>
```

### Preprocessor theme fix

Prism's built-in themes do not style `.token.meta`. Add one CSS rule:

```css
.token.meta,
.token.preprocessor { color: #e5c07b; }   /* amber — adjust to taste */
```

### Highlighting dynamically injected code

```js
const el = document.querySelector('#myCode');
el.className = 'language-xojo';
el.textContent = sourceCode;
Prism.highlightElement(el);
```

Or highlight a string directly:

```js
const html = Prism.highlight(sourceCode, Prism.languages.xojo, 'xojo');
container.innerHTML = `<pre><code class="language-xojo">${html}</code></pre>`;
```

### Preventing auto-highlight

If you want to control when highlighting runs, add `data-manual` to the `<script>` tag that loads Prism:

```html
<script src="prism.min.js" data-manual></script>
```

Then call `Prism.highlightAll()` or `Prism.highlightElement(el)` manually.

---

## 4. Usage — Node.js

```js
import Prism from 'prismjs';
import './xojo.prism.js';     // registers Prism.languages.xojo as a side effect

const html = Prism.highlight(sourceCode, Prism.languages.xojo, 'xojo');
```

With CommonJS:

```js
const Prism = require('prismjs');
require('./xojo.prism.js');

const html = Prism.highlight(sourceCode, Prism.languages.xojo, 'xojo');
```

---

## 5. Usage — Markdown parsers

### markdown-it

```js
import MarkdownIt from 'markdown-it';
import Prism from 'prismjs';
import './xojo.prism.js';

const md = new MarkdownIt({
  highlight(str, lang) {
    if (lang && Prism.languages[lang]) {
      return Prism.highlight(str, Prism.languages[lang], lang);
    }
    return '';
  }
});
```

Then write Markdown:

````markdown
```xojo
Function Fibonacci(n As Integer) As Integer
  If n <= 1 Then Return n
  Return Fibonacci(n - 1) + Fibonacci(n - 2)
End Function
```
````

### Jekyll / Hugo / static site generators

Most static site generators that use Prism for highlighting accept custom language files via their plugin or asset pipeline. Point the pipeline to `xojo.prism.js` and add it after the core Prism bundle.

---

## 6. Choosing a theme

Any Prism theme works. Popular dark themes:

| Theme | CDN path |
|---|---|
| Tomorrow Night | `themes/prism-tomorrow.min.css` |
| Okaidia | `themes/prism-okaidia.min.css` |
| One Dark | `themes/prism-one-dark.min.css` |
| Night Owl | `themes/prism-night-owl.min.css` |
| VS Code Dark+ | `themes/prism-vsc-dark-plus.min.css` |

Popular light themes:

| Theme | CDN path |
|---|---|
| Default | `themes/prism.min.css` |
| Solarized Light | `themes/prism-solarizedlight.min.css` |
| GitHub | `themes/prism-github.min.css` |

Browse all at [prismjs.com](https://prismjs.com/).

> **Reminder:** Add `.token.meta { color: … }` to your CSS — none of the built-in themes include it.

---

## 7. Token reference

| CSS class | Xojo constructs |
|---|---|
| `.token.keyword` | `Var` `Dim` `Sub` `Function` `Class` `Module` `Interface` `Enum` `If` `Then` `Else` `ElseIf` `End` `For` `Each` `Next` `While` `Do` `Loop` `Select` `Case` `Try` `Catch` `Finally` `Return` `Raise` `RaiseEvent` `New` `Inherits` `Implements` `Extends` `AddHandler` `RemoveHandler` `Public` `Private` `Protected` `Static` `Shared` `Override` `Virtual` `Final` `Abstract` `Property` `Event` `Delegate` `As` `ByRef` `ByVal` … and also `Self` `Super` `Me` (via `builtin` alias) |
| `.token.operator` | `And` `Or` `Not` `Xor` `Mod` `In` `Is` `IsA` `AddressOf` `WeakAddressOf` and symbolic operators `<` `>` `+` `-` `*` `/` `=` `<>` … |
| `.token.boolean` | `True` `False` `Nil` |
| `.token.class-name` | `Integer` `Int8`–`Int64` `UInt8`–`UInt64` `Single` `Double` `Boolean` `String` `Variant` `Object` `Color` `Ptr` `Auto` `CString` `WString` |
| `.token.comment` | `// text` and `' text` (to end of line) |
| `.token.string` | `"double quoted"` (no multi-line) |
| `.token.number` | `42` `3.14` `1e6` `&hFF00FF` `&b10101010` |
| `.token.meta` (via `.token.preprocessor`) | `#pragma DisableBackgroundTasks` · `#tag Module, Name = Utils` · `#if` · `#else` · `#endif` · `#region` · `#endregion` |

Inside a preprocessor token, the `#directive` word gets an additional `.token.directive.keyword` class.

---

## 8. Preprocessor directives

Xojo has two categories of preprocessor lines:

| Directive | Purpose |
|---|---|
| `#pragma` | Compiler hints (e.g. `DisableBackgroundTasks`, `NilObjectChecking False`) |
| `#tag` / `#tag EndModule` etc. | IDE metadata blocks (project file format) |
| `#if` / `#elseif` / `#else` / `#endif` | Conditional compilation |
| `#region` / `#endregion` | Code folding markers |

The grammar matches the **entire line** as a single `preprocessor meta` token. Inside it:
- `#directive` → `.token.directive.keyword` (the directive word itself)
- Everything after → plain text within the `meta` context

This means words like `Module` in `#tag Module, Name = Utils` are **not** coloured as Xojo keywords — they are part of the preprocessor argument, not executable code.

Example output HTML:

```html
<span class="token preprocessor meta">
  <span class="token directive keyword">#pragma</span> DisableBackgroundTasks
</span>
```

---

## 9. Code Walkthrough

This section explains every part of `xojo.prism.js` in detail.

### Entry point — the IIFE

```js
(function (Prism) {
  Prism.languages['xojo'] = { ... };
}(Prism));
```

`xojo.prism.js` is an **immediately-invoked function expression (IIFE)** that registers the grammar on the global `Prism` object as soon as the script is evaluated. No separate registration call is needed. `Prism` must exist before this file runs — always load `prism.js` first.

### Pattern ordering

Prism processes the language object **in key order**, and the **first pattern that matches wins**. The ordering in `xojo.prism.js` is:

```
comment → string → preprocessor → keyword → operator-keyword
        → builtin → boolean → type → number → operator → punctuation
```

This ordering ensures:
- Comments consume `//` and `'` before string or keyword patterns can match inside them
- Strings consume `"..."` before keyword patterns see the content inside quotes
- Preprocessor lines consume the entire `#tag ...` line before keyword patterns can colour `Module` as a keyword

### The `greedy` flag

Any token with `greedy: true` tells Prism: **do not re-tokenize this match**. Without it, Prism would scan each matched region again against all subsequent patterns in the language object. This would cause `Module` inside `// Module example` (a comment) to also receive the `.token.keyword` class. `greedy: true` prevents this.

### Token: `comment`

```js
'comment': [
  { pattern: /\/\/.*/, greedy: true },
  { pattern: /'[^\r\n]*/, greedy: true },
],
```

An array value means Prism tries both patterns in sequence. Key detail: `'[^\r\n]*` uses a character class instead of `.*` with the `m` flag — Prism 1.29+ silently ignores the `flags` property on pattern objects, so `[^\r\n]*` is the correct way to stop at a line boundary.

### Token: `preprocessor` with `inside`

```js
'preprocessor': {
  pattern: /#(?:tag|pragma|if|elseif|else|endif|region|endregion)\b[^\r\n]*/i,
  greedy: true,
  alias: 'meta',
  inside: {
    'directive': { pattern: /^#\w+/, alias: 'keyword' },
  },
},
```

This is the most complex token. Each part:

| Part | Meaning |
|---|---|
| `/#(?:tag|...)\b[^\r\n]*/i` | Match `#directive` then the entire rest of the line |
| `greedy: true` | Consume the whole line — patterns later in the list cannot match inside it |
| `alias: 'meta'` | The whole token gets the additional CSS class `.token.meta` |
| `inside: { 'directive': ... }` | After matching, Prism runs `inside` patterns on the token's text |
| `/^#\w+/` | Matches only the `#directive` word at the start of the token (`^`) |
| `alias: 'keyword'` | The directive word also gets `.token.keyword` |

Result for `#pragma DisableBackgroundTasks`:
```html
<span class="token preprocessor meta">
  <span class="token directive keyword">#pragma</span> DisableBackgroundTasks
</span>
```

`Module` in `#tag Module, Name = Utils` is plain text inside the `meta` span — **not** a keyword token.

### Token: `type` with `alias: 'class-name'`

```js
'type': {
  pattern: /\b(?:Integer|String|Double|...)\b/i,
  alias: 'class-name',
},
```

Prism themes typically include a `.token.class-name` rule (e.g. cyan in Tomorrow Night) but do not always include `.token.type`. Using `alias: 'class-name'` ensures type names are coloured by most themes without any extra CSS.

---

## 10. Troubleshooting

**Code block is not highlighted**

- Confirm `xojo.prism.js` loads **after** `prism.js`. If Prism is not yet loaded when the grammar file runs, `Prism` will be `undefined`.
- Check the browser console for errors.
- The `<code>` element must have `class="language-xojo"`.

**`#pragma` / `#tag` lines look the same as plain text**

The token is matched correctly but no theme styles `.token.meta`. Add to your CSS:
```css
.token.meta,
.token.preprocessor { color: #e5c07b; }
```

**`'` apostrophe comment is not highlighted**

Do **not** use `flags: 'm'` in pattern objects — it is silently ignored in Prism 1.29+. The grammar uses `/'[^\r\n]*/` (no `$`, no `m` flag) which correctly matches to end of line in multiline code.

**`Module` inside `#tag Module, Name = X` is coloured as a keyword**

This would indicate the `preprocessor` pattern is not matching. Ensure the grammar file loaded without errors and that `Prism.languages.xojo` is defined before the page renders.

**Hex/binary literals show as plain text**

Verify the `number` pattern is present: `/&[hH][0-9a-fA-F]+\b|&[bB][01]+\b|.../`. The `&` character is part of the match — in HTML source, write `&amp;h` inside `<code>` elements (or let your markdown parser escape it).

**Strings spanning multiple lines are not coloured**

Xojo strings cannot span lines, so the pattern `/"[^"\n]*"/` is intentionally single-line only. Multi-line string content is a syntax error in Xojo.
