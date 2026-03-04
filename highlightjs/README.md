# xojo-syntax-highlight · highlight.js

Xojo language definition for [highlight.js](https://highlightjs.org/) — covers everything the built-in `vb` grammar misses.

| What's added | Why it matters |
|---|---|
| `//` line comments | Xojo's primary comment style |
| `'` apostrophe comments | Alternative comment style |
| `Var` keyword | Modern Xojo variable declaration |
| `Nil` literal | Xojo null reference (not `Nothing`) |
| `Self` / `Super` / `Me` | Built-in object references |
| `#tag` / `#pragma` directives | Compiler & IDE metadata |
| `&h` hex / `&b` binary literals | Xojo numeric literal syntax |

## Install

Copy `xojo.highlight.js` into your project. No build step required.

```sh
# or via npm (coming soon)
npm install xojo-syntax-highlight
```

## Quick Start

```html
<link rel="stylesheet" href="highlight.js/styles/atom-one-dark.min.css">
<script src="highlight.min.js"></script>
<script type="module">
  import xojo from './xojo.highlight.js';
  hljs.registerLanguage('xojo', xojo);
  hljs.highlightAll();
</script>
```

Then use `language-xojo` on any `<code>` element:

```html
<pre><code class="language-xojo">
  Var greeting As String = "Hello, World!"
</code></pre>
```

Or in Markdown (markdown-it, marked, etc.):

````markdown
```xojo
Var greeting As String = "Hello, World!"
```
````

## Tokens

| Token class | Examples |
|---|---|
| `keyword` | `Var` `Dim` `Sub` `Function` `Class` `If` `Return` `Raise` `New` … |
| `literal` | `True` `False` `Nil` |
| `type` | `Integer` `String` `Double` `Boolean` `Variant` `Object` … |
| `built_in` | `Self` `Super` `Me` |
| `comment` | `// line comment` `' apostrophe comment` |
| `string` | `"double quoted"` |
| `number` | `42` `3.14` `&hFF00FF` `&b10101010` |
| `meta` | `#pragma` `#tag` `#if` `#else` `#endif` |

All patterns are **case-insensitive** (Xojo is case-insensitive).

## Demo

Open `demo/index.html` in a browser via a local server:

```sh
python3 -m http.server 8000
# visit http://localhost:8000/highlightjs/demo/
```

## See Also

- [Prism.js grammar](../prismjs/) — companion grammar for Prism.js
- [User Guide](userguide.md) — detailed integration guide

## License

MIT
