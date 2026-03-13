# xojo-syntax-highlight · CodeMirror 6

Xojo language definition for [CodeMirror 6](https://codemirror.net) — adds accurate syntax highlighting and a fully editable Xojo experience to any CM6 editor.

| What's included | Why it matters |
|---|---|
| `//` line comments | Xojo's primary comment style |
| `'` apostrophe comments | Alternative comment style |
| `Var` keyword | Modern Xojo variable declaration |
| `Nil` literal | Xojo null reference (not `Nothing`) |
| `Self` / `Super` / `Me` | Built-in object references |
| `#tag` / `#pragma` directives | Entire line is `meta` — arguments never mis-coloured as keywords |
| `&h` hex / `&b` binary literals | Xojo numeric literal syntax |

## Install

Copy `xojo.codemirror.js` into your project. No dependencies, no build step.

```sh
# or via npm (coming soon)
npm install xojo-syntax-highlight
```

## Quick Start

### Browser (direct CDN imports)

```html
<script type="module">
  import { EditorView, basicSetup } from "https://esm.sh/codemirror@6";
  import { StreamLanguage }         from "https://esm.sh/@codemirror/language@6";
  import { xojoStreamParser }       from "./xojo.codemirror.js";

  new EditorView({
    doc: "Var greeting As String = \"Hello, World!\"",
    extensions: [
      basicSetup,
      StreamLanguage.define(xojoStreamParser),
    ],
    parent: document.querySelector("#editor"),
  });
</script>

<div id="editor"></div>
```

### npm / bundler (Vite, Webpack, Rollup)

```js
import { EditorView, basicSetup } from "codemirror";
import { StreamLanguage }         from "@codemirror/language";
import { xojoStreamParser }       from "xojo-syntax-highlight/codemirror";

new EditorView({
  doc: sourceCode,
  extensions: [
    basicSetup,
    StreamLanguage.define(xojoStreamParser),
  ],
  parent: document.querySelector("#editor"),
});
```

## Tokens

| StreamLanguage token | Highlight tag | Xojo constructs |
|---|---|---|
| `keyword` | `tags.keyword` | `Var` `Sub` `Function` `Class` `If` `Return` `Raise` `New` … |
| `operator` | `tags.operator` | `And` `Or` `Not` `Xor` `Mod` `Is` `IsA` `AddressOf` … |
| `atom` | `tags.atom` | `True` `False` `Nil` |
| `type` | `tags.typeName` | `Integer` `String` `Double` `Boolean` `Variant` … |
| `builtin` | `tags.standard(tags.name)` | `Self` `Super` `Me` |
| `comment` | `tags.lineComment` | `// text` and `' text` |
| `string` | `tags.string` | `"double quoted"` |
| `number` | `tags.number` | `42` `3.14` `&hFF00FF` `&b10101010` |
| `meta` | `tags.meta` | `#pragma` `#tag` `#if` `#else` `#endif` (whole line) |

All patterns are **case-insensitive** (Xojo is case-insensitive).

## Demo

```sh
python3 -m http.server 8000
# visit http://localhost:8000/codemirror/demo/
```

## See Also

- [highlight.js grammar](../highlightjs/) — passive highlighting for static HTML
- [Prism.js grammar](../prismjs/) — passive highlighting for static HTML
- [Pygments lexer](../pygments/) — Python-based highlighting
- [User Guide](userguide.md) — detailed integration guide

## License

MIT
