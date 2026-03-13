# Xojo Syntax Highlighting — Pygments

A Pygments lexer for the [Xojo](https://xojo.com) programming language.

## Quick Start (2 minutes)

### Command line (no install needed)

```bash
# Highlight to terminal
python3 -m pygments -x -l xojo.pygments.py:XojoLexer yourfile.xojo_code

# Generate HTML file
python3 -m pygments -x -l xojo.pygments.py:XojoLexer yourfile.xojo_code \
  -f html -O full,style=monokai -o output.html
```

### In Python code

```python
import importlib.util
from pygments import highlight
from pygments.formatters import HtmlFormatter

# Load the lexer from file (no pip install required)
spec = importlib.util.spec_from_file_location("xojo_pygments", "xojo.pygments.py")
mod  = importlib.util.module_from_spec(spec)
spec.loader.exec_module(mod)

code = '''
Var x As Integer = 42
If x > 0 Then
  Return x
End If
'''

html = highlight(code, mod.XojoLexer(), HtmlFormatter(style="monokai"))
```

### Demo

```bash
python3 pygments/demo/demo.py
# Opens demo/output.html with sample Xojo code highlighted
```

## Token Mapping

| Xojo element | Pygments token |
|---|---|
| `//` and `'` comments | `Comment.Single` |
| `#tag`, `#pragma`, `#if` … (whole line) | `Comment.Preproc` |
| `"..."` strings | `String.Double` |
| `&hFF` hex numbers | `Number.Hex` |
| `&b1010` binary numbers | `Number.Bin` |
| `3.14`, `1.5e-3` floats | `Number.Float` |
| `42`, `1e6` integers | `Number.Integer` |
| `Var`, `Sub`, `Class`, `If` … | `Keyword` |
| `Integer`, `String`, `Double` … | `Keyword.Type` |
| `True`, `False`, `Nil` | `Keyword.Constant` |
| `And`, `Or`, `Not`, `Is`, `IsA` … | `Operator.Word` |
| `Self`, `Super`, `Me` | `Name.Builtin` |

## Run Tests

```bash
python3 pygments/test.py
```
