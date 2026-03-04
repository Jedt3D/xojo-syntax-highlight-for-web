# xojo-syntax-highlight · Prism.js

Xojo language definition for [Prism.js](https://prismjs.com/) — covers everything the built-in `vb` grammar misses.

| What's added | Why it matters |
|---|---|
| `//` line comments | Xojo's primary comment style |
| `'` apostrophe comments | Alternative comment style |
| `Var` keyword | Modern Xojo variable declaration |
| `Nil` literal | Xojo null reference (not `Nothing`) |
| `Self` / `Super` / `Me` | Built-in object references |
| `#tag` / `#pragma` directives | Highlighted as preprocessor, arguments not mis-coloured as keywords |
| `&h` hex / `&b` binary literals | Xojo numeric literal syntax |

## Install

Copy `xojo.prism.js` into your project. No build step required.

```sh
# or via npm (coming soon)
npm install xojo-syntax-highlight
```

## Quick Start

Load `xojo.prism.js` **after** `prism.js`:

```html
<link rel="stylesheet" href="prism-tomorrow.min.css">
<script src="prism.min.js"></script>
<script src="xojo.prism.js"></script>
```

Then use `language-xojo` on any `<code>` element:

```html
<pre><code class="language-xojo">
  Var greeting As String = "Hello, World!"
</code></pre>
```

Prism auto-highlights on `DOMContentLoaded` — no extra JavaScript needed.

Or in Markdown (markdown-it, etc.):

````markdown
```xojo
Var greeting As String = "Hello, World!"
```
````

## Tokens

| Token class | Examples |
|---|---|
| `keyword` | `Var` `Dim` `Sub` `Function` `Class` `If` `Return` `Raise` `New` … |
| `boolean` | `True` `False` `Nil` |
| `class-name` (type) | `Integer` `String` `Double` `Boolean` `Variant` `Object` … |
| `keyword` (builtin) | `Self` `Super` `Me` |
| `operator` | `And` `Or` `Not` `Xor` `Mod` `Is` `IsA` … |
| `comment` | `// line comment` `' apostrophe comment` |
| `string` | `"double quoted"` |
| `number` | `42` `3.14` `&hFF00FF` `&b10101010` |
| `meta` (preprocessor) | `#pragma DisableBackgroundTasks` — directive highlighted, arguments plain |

All patterns are **case-insensitive** (Xojo is case-insensitive).

## Preprocessor Note

`#tag` and `#pragma` lines are matched as a single preprocessor token. The `#directive` word is sub-highlighted as a `keyword`; everything after it is left as plain text within the `meta` context. This prevents words like `Module` in `#tag Module, Name = Utils` from being coloured as Xojo keywords.

## Theme Note

Prism's built-in themes (including Tomorrow) have no CSS rule for `.token.meta`. Add this to your stylesheet:

```css
.token.meta,
.token.preprocessor { color: #e5c07b; }
```

## Demo

Open `demo/index.html` via a local server:

```sh
python3 -m http.server 8000
# visit http://localhost:8000/prismjs/demo/
```

## See Also

- [highlight.js grammar](../highlightjs/) — companion grammar for highlight.js
- [User Guide](userguide.md) — detailed integration guide

## License

MIT
