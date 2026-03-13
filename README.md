# Xojo Syntax Highlight for Web

Syntax highlighting for the **Xojo programming language** — four independent, single-file, zero-dependency drop-ins for the most popular web highlighting libraries.

| Library | File | Language |
|---|---|---|
| [highlight.js](#1-highlightjs) | `highlightjs/xojo.highlight.js` | JavaScript (ES module) |
| [Prism.js](#2-prismjs) | `prismjs/xojo.prism.js` | JavaScript (IIFE) |
| [CodeMirror 6](#3-codemirror-6) | `codemirror/xojo.codemirror.js` | JavaScript (ES module) |
| [Pygments](#4-pygments) | `pygments/xojo.pygments.py` | Python |

Each implementation handles Xojo-specific syntax that VB-based grammars miss: `//` and `'` comments, `Var`, `Nil`, `Self`/`Super`/`Me`, `#tag`/`#pragma` preprocessor directives, and `&h`/`&b` numeric literals — all case-insensitive.

---

## Quick Start

### Run a demo server

```bash
python3 -m http.server 8000
```

Then open:
- Landing page: `http://localhost:8000/`
- highlight.js demo: `http://localhost:8000/highlightjs/demo/`
- Prism.js demo: `http://localhost:8000/prismjs/demo/`
- CodeMirror 6 demo: `http://localhost:8000/codemirror/demo/`

---

## 1. highlight.js

Copy `highlightjs/xojo.highlight.js` into your project.

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/atom-one-dark.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>
<script type="module">
  import xojo from './xojo.highlight.js';
  hljs.registerLanguage('xojo', xojo);
  hljs.highlightAll();
</script>
```

```html
<pre><code class="language-xojo">
Var greeting As String = "Hello, World!"
</code></pre>
```

See [`highlightjs/README.md`](highlightjs/README.md) and [`highlightjs/userguide.md`](highlightjs/userguide.md) for full details.

---

## 2. Prism.js

Copy `prismjs/xojo.prism.js` into your project. Load it **after** `prism.js`.

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism-tomorrow.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/prism.min.js"></script>
<script src="./xojo.prism.js"></script>
```

```html
<pre><code class="language-xojo">
Var greeting As String = "Hello, World!"
</code></pre>
```

Prism auto-highlights on `DOMContentLoaded` — no extra JavaScript needed.

See [`prismjs/README.md`](prismjs/README.md) and [`prismjs/userguide.md`](prismjs/userguide.md) for full details.

---

## 3. CodeMirror 6

Copy `codemirror/xojo.codemirror.js` into your project.

```html
<script type="importmap">
{
  "imports": {
    "codemirror":           "https://esm.sh/*codemirror@6",
    "@codemirror/language": "https://esm.sh/*@codemirror/language@6"
  }
}
</script>

<script type="module">
  import { EditorView, basicSetup } from "codemirror";
  import { StreamLanguage }         from "@codemirror/language";
  import { xojoStreamParser }       from "./xojo.codemirror.js";

  new EditorView({
    doc: 'Var greeting As String = "Hello, World!"',
    extensions: [ basicSetup, StreamLanguage.define(xojoStreamParser) ],
    parent: document.getElementById("editor"),
  });
</script>

<div id="editor"></div>
```

See [`codemirror/README.md`](codemirror/README.md) and [`codemirror/userguide.md`](codemirror/userguide.md) for full details.

---

## 4. Pygments

No install required — copy `pygments/xojo.pygments.py` into your project.

```bash
# Highlight a file to HTML from the command line
python3 -m pygments -x -l pygments/xojo.pygments.py:XojoLexer yourfile.xojo_code \
  -f html -O full,style=monokai -o output.html

# Generate the demo page
python3 pygments/demo/demo.py
# Output: pygments/demo/output.html
```

See [`pygments/README.md`](pygments/README.md) for Python API usage and full details.

---

## Directory Structure

```
xojo-syntax-highlight/
├── highlightjs/
│   ├── xojo.highlight.js      # highlight.js grammar (ES module)
│   ├── demo/index.html        # interactive demo
│   ├── README.md
│   └── userguide.md
├── prismjs/
│   ├── xojo.prism.js          # Prism.js grammar (IIFE, self-registers)
│   ├── demo/index.html
│   ├── README.md
│   └── userguide.md
├── codemirror/
│   ├── xojo.codemirror.js     # CodeMirror 6 StreamParser
│   ├── test.mjs               # Node.js test suite
│   ├── demo/index.html
│   ├── README.md
│   └── userguide.md
├── pygments/
│   ├── xojo.pygments.py       # Pygments RegexLexer
│   ├── test.py                # Python test suite
│   ├── demo/demo.py           # HTML demo generator
│   └── README.md
├── index.html                 # landing page
└── README.md
```

---

## Running Tests

```bash
# CodeMirror (Node.js)
cd codemirror && node test.mjs

# Pygments (Python)
python3 pygments/test.py
```

---

## Clone & develop

```bash
git clone https://github.com/Jedt3D/xojo-syntax-highlight-for-web.git
cd xojo-syntax-highlight-for-web
python3 -m http.server 8000
```

No build step — all four grammars are single-file libraries.

---

## License

MIT
