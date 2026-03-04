# Xojo highlight.js Grammar — User Guide

## Contents

1. [How it works](#1-how-it-works)
2. [Installation](#2-installation)
3. [Usage — Browser](#3-usage--browser)
4. [Usage — Node.js](#4-usage--nodejs)
5. [Usage — Markdown parsers](#5-usage--markdown-parsers)
6. [Choosing a theme](#6-choosing-a-theme)
7. [Token reference](#7-token-reference)
8. [Language aliases](#8-language-aliases)
9. [Code Walkthrough](#9-code-walkthrough)
10. [Troubleshooting](#10-troubleshooting)

---

## 1. How it works

`xojo.highlight.js` exports a factory function that highlight.js calls with its own `hljs` instance. It returns a language definition object with:

- **`name`** — `'Xojo'`
- **`aliases`** — `['xojo']` (so ` ```xojo ` fences work)
- **`case_insensitive: true`** — Xojo is case-insensitive
- **`keywords`** — four categories: `keyword`, `literal`, `type`, `built_in`
- **`contains`** — ordered list of pattern matchers for comments, strings, numbers, and preprocessor directives

The grammar is **not** derived from `vb` — it is a fresh definition to avoid inheriting incorrect Visual Basic patterns.

---

## 2. Installation

### Option A — Copy the file

Download `xojo.highlight.js` and place it anywhere in your project.

### Option B — npm *(coming soon)*

```sh
npm install xojo-syntax-highlight
```

### Option C — CDN *(coming soon)*

```html
<script src="https://cdn.example.com/xojo.highlight.js" type="module"></script>
```

---

## 3. Usage — Browser

The grammar file is an **ES module**. You must load it with `type="module"`.

### Automatic highlighting (highlightAll)

```html
<!-- 1. Stylesheet -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/atom-one-dark.min.css">

<!-- 2. highlight.js core (UMD — exposes window.hljs) -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>

<!-- 3. Xojo grammar + register -->
<script type="module">
  import xojo from './xojo.highlight.js';
  hljs.registerLanguage('xojo', xojo);
  hljs.highlightAll();           // highlights all code[class^="language-"]
</script>
```

```html
<!-- 4. Mark up code -->
<pre><code class="language-xojo">
Var greeting As String = "Hello, World!"
</code></pre>
```

### Manual highlighting (single element)

Use `hljs.highlight()` when you control exactly which element to highlight:

```js
import xojo from './xojo.highlight.js';
hljs.registerLanguage('xojo', xojo);

const el = document.querySelector('#myCode');
const result = hljs.highlight(el.textContent, { language: 'xojo' });
el.innerHTML = result.value;
el.classList.add('hljs');   // applies the theme's background/foreground
```

### Re-highlighting dynamic content

Call `hljs.highlightElement(el)` after injecting new content:

```js
// el must have class="language-xojo" for hljs to detect the language
el.className = 'language-xojo';
hljs.highlightElement(el);
```

Or use `hljs.highlight()` and assign `innerHTML` manually (no class requirement).

---

## 4. Usage — Node.js

```js
import hljs from 'highlight.js/lib/core';
import xojo from './xojo.highlight.js';

hljs.registerLanguage('xojo', xojo);

const result = hljs.highlight(sourceCode, { language: 'xojo' });
console.log(result.value);   // HTML string with <span class="hljs-*"> tokens
```

> **Note:** `xojo.highlight.js` uses `export default` (ESM). With CommonJS, use a dynamic import:
> ```js
> const { default: xojo } = await import('./xojo.highlight.js');
> ```

---

## 5. Usage — Markdown parsers

### markdown-it

```js
import MarkdownIt from 'markdown-it';
import hljs from 'highlight.js/lib/core';
import xojo from './xojo.highlight.js';

hljs.registerLanguage('xojo', xojo);

const md = new MarkdownIt({
  highlight(str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      return hljs.highlight(str, { language: lang }).value;
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

### marked

```js
import { marked } from 'marked';
import hljs from 'highlight.js/lib/core';
import xojo from './xojo.highlight.js';

hljs.registerLanguage('xojo', xojo);

marked.use({
  renderer: {
    code(code, lang) {
      const language = hljs.getLanguage(lang) ? lang : 'plaintext';
      return `<pre><code class="hljs">${hljs.highlight(code, { language }).value}</code></pre>`;
    }
  }
});
```

---

## 6. Choosing a theme

Any highlight.js theme works. Popular dark themes:

| Theme | CDN path |
|---|---|
| Atom One Dark | `styles/atom-one-dark.min.css` |
| GitHub Dark | `styles/github-dark.min.css` |
| VS 2015 | `styles/vs2015.min.css` |
| Night Owl | `styles/night-owl.min.css` |
| Tokyo Night Dark | `styles/tokyo-night-dark.min.css` |

Popular light themes:

| Theme | CDN path |
|---|---|
| GitHub | `styles/github.min.css` |
| Xcode | `styles/xcode.min.css` |
| Atom One Light | `styles/atom-one-light.min.css` |

Browse all themes at [highlightjs.org/demo](https://highlightjs.org/demo).

---

## 7. Token reference

| highlight.js class | Xojo constructs |
|---|---|
| `hljs-keyword` | `Var` `Dim` `Sub` `Function` `Class` `Module` `Interface` `Enum` `If` `Then` `Else` `ElseIf` `End` `For` `Each` `Next` `While` `Do` `Loop` `Select` `Case` `Try` `Catch` `Finally` `Return` `Raise` `RaiseEvent` `New` `Inherits` `Implements` `Extends` `AddHandler` `RemoveHandler` `Public` `Private` `Protected` `Static` `Shared` `Override` `Virtual` `Final` `Abstract` `Property` `Event` `Delegate` `As` `ByRef` `ByVal` `And` `Or` `Not` `Xor` `Mod` `In` `Is` `IsA` `AddressOf` `WeakAddressOf` … |
| `hljs-literal` | `True` `False` `Nil` |
| `hljs-type` | `Integer` `Int8`–`Int64` `UInt8`–`UInt64` `Single` `Double` `Boolean` `String` `Variant` `Object` `Color` `Ptr` `Auto` `CString` `WString` |
| `hljs-built_in` | `Self` `Super` `Me` |
| `hljs-comment` | `// text` and `' text` (to end of line) |
| `hljs-string` | `"double quoted"` (no multi-line) |
| `hljs-number` | `42` `3.14` `1e6` `&hFF00FF` `&b10101010` |
| `hljs-meta` | `#pragma` `#tag` `#if` `#elseif` `#else` `#endif` `#region` `#endregion` |

---

## 8. Language aliases

The language is registered as `'xojo'`. highlight.js resolves code fence language names through its alias table, so the following all work:

```
```xojo
```

If you need to register an additional alias:

```js
hljs.registerAliases(['xo', 'xojo'], { languageName: 'xojo' });
```

---

## 9. Code Walkthrough

This section explains every part of `xojo.highlight.js` in detail.

### Entry point — the factory function

```js
export default function(hljs) {
  // ... define keyword lists ...
  return { name, aliases, case_insensitive, keywords, contains };
}
```

highlight.js calls this function with its own `hljs` instance when you call `hljs.registerLanguage('xojo', xojo)`. The function returns the complete language definition. This pattern means the grammar has no hard dependency on any specific highlight.js version.

### Keyword lists

```js
const KEYWORDS  = ['Var', 'Dim', 'Sub', ...];
const LITERALS  = ['True', 'False', 'Nil'];
const TYPES     = ['Integer', 'String', ...];
const OPERATORS = ['And', 'Or', 'Not', ...];
const BUILTINS  = ['Self', 'Super', 'Me'];
```

These are plain arrays. highlight.js matches each word automatically using whole-word, case-insensitive comparison — no regex needed. They are split into separate constants for readability and merged into the `keywords` object:

| `keywords` key | CSS class | Examples |
|---|---|---|
| `keyword` | `hljs-keyword` | `Var`, `Sub`, `If`, `Return`, `And`, `Or` |
| `literal` | `hljs-literal` | `True`, `False`, `Nil` |
| `type` | `hljs-type` | `Integer`, `String`, `Double` |
| `built_in` | `hljs-built_in` | `Self`, `Super`, `Me` |

`OPERATORS` (word-form operators like `And`, `Or`) are merged into `keyword` because highlight.js has no separate `operator` category in the keyword system.

### The `contains` array

`contains` is an ordered list of **modes** (pattern matchers). highlight.js tries each at every position; first match wins. Order is critical:

#### Modes 1 & 2: Comments

```js
hljs.COMMENT('//', '$'),   // → hljs-comment
hljs.COMMENT("'", '$'),    // → hljs-comment
```

`hljs.COMMENT(begin, end)` is a built-in helper that builds a mode with `scope: 'comment'`. The `$` end pattern matches end-of-line (highlight.js adds the `m` regex flag internally). Comments come first so `//` or `'` inside a string cannot trigger a comment match.

#### Mode 3: Strings

```js
{
  scope: 'string',
  begin: '"',
  end: '"',
  illegal: '\\n',
}
```

`illegal: '\\n'` causes highlight.js to abort the string mode if a newline appears before the closing `"`. This matches Xojo's language rule (no multi-line strings) and prevents a missing quote from making the rest of the file appear as one giant string token.

#### Mode 4: Numbers

```js
{
  scope: 'number',
  match: /&[hH][0-9a-fA-F]+\b|&[bB][01]+\b|\b\d+(?:\.\d+)?(?:[eE][+-]?\d+)?\b/,
  relevance: 0,
}
```

The alternation order is deliberate: hex (`&h`) and binary (`&b`) branches appear before the decimal branch so that `&` is not misread as an operator. `relevance: 0` tells highlight.js not to count number matches when auto-detecting the language.

#### Mode 5: Preprocessor directives

```js
{
  scope: 'meta',
  match: /#(tag|pragma|if|else|elseif|endif|region|endregion)\b/i,
}
```

This matches only the directive token (`#tag`, `#pragma`, …), **not** the rest of the line. Arguments after the directive — such as `Module` in `#tag Module, Name = Utils` — fall through to keyword matching and may be coloured as keywords. This is intentional behaviour in the highlight.js grammar; the Prism.js and CodeMirror grammars use different strategies to consume the whole line as a single `meta` token.

---

## 10. Troubleshooting

**Code block is not highlighted at all**

- Confirm `hljs.registerLanguage('xojo', xojo)` is called before `hljs.highlightAll()`.
- The `<script type="module">` block is deferred — move it before the closing `</body>` tag or use `hljs.highlightAll()` inside it (modules execute after DOM is ready, so this is fine).
- Serving from `file://` blocks ES module imports. Use a local HTTP server: `python3 -m http.server`.

**`hljs.highlightElement(el)` produces no colours**

`highlightElement` reads the language from the element's `class` — it must include `language-xojo`. If you don't want to set a class, use `hljs.highlight(el.textContent, { language: 'xojo' })` directly and assign `innerHTML`.

**Keywords like `Module` inside `#tag Module, Name = X` are coloured as keywords**

This should not happen — the `meta` matcher only matches `#directive`, not the line content. Verify you are using the latest version of `xojo.highlight.js`.

**`'` apostrophe comments are not highlighted**

highlight.js's `COMMENT(begin, end)` helper supports string delimiters. The apostrophe comment is defined as `COMMENT("'", '$')` — `$` matches end of line in highlight.js's internal regex engine, which adds the `m` flag automatically.

**Hex/binary literals show as plain text**

Confirm the number pattern is present. In highlight.js, `match` patterns require the full token to be anchored by the pattern — the `&` in `&h` must be part of the match (it is: `/&[hH][0-9a-fA-F]+\b|.../`).
