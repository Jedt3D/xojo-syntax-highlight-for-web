# Xojo Syntax Highlight for Web

## Project Overview
This project provides a syntax highlighting solution for Xojo web applications. It allows developers to easily implement code highlighting in their applications using customizable themes and formats.

## Features
- Customizable themes for syntax highlighting
- Support for multiple programming languages
- Easy integration into Xojo web applications
- Responsive and lightweight design

## Directory Structure
```
/xojo-syntax-highlight-for-web
│
├── /src              # Source files for the library
├── /docs             # Documentation files
├── /examples          # Usage examples
└── README.md         # Project documentation
```

## Quick Start Guides

### Python Simple Web Server

0. If you have Python3, you may use this command to run a simple http web server from the git repo's directory.
```bash
python3 -m http.server
```
Then open your browser and go to `http://localhost:8000/prismjs/demo/index.html`, for example.

### Library 1: Highlight.js
1. Install Highlight.js from npm:
   ```bash
   npm install highlight.js
   ```
2. Add the library to your project:
   ```html
   <link rel="stylesheet" href="path/to/styles/default.min.css">
   <script src="path/to/highlight.min.js"></script>
   ```
3. Initialize highlighting:
   ```javascript
   hljs.highlightAll();
   ```

### Library 4: Pygments (Python)

No install required — just copy `pygments/xojo.pygments.py` into your project.

```bash
# Highlight to HTML from command line
python3 -m pygments -x -l pygments/xojo.pygments.py:XojoLexer yourfile.xojo_code \
  -f html -O full,style=monokai -o output.html

# Run demo
python3 pygments/demo/demo.py
```

See `pygments/README.md` for full usage details.

### Library 2: Prism.js
1. Install Prism.js:
   ```bash
   npm install prismjs
   ```
2. Link prism.css and prism.js in your HTML:
   ```html
   <link rel="stylesheet" href="path/to/prism.css">
   <script src="path/to/prism.js"></script>
   ```
3. Use Prism's syntax highlighting:
   ```html
   <pre><code class="language-js">const a = 1;</code></pre>
   ```

## Usage Examples
### Example 1: Highlighting JavaScript Code
```javascript
function helloWorld() {
    console.log('Hello, World!');
}
```

### Example 2: Highlighting Python Code
```python
def hello_world():
    print('Hello, World!')
```

## Development Information
- Clone the repository:
  ```bash
  git clone https://github.com/Jedt3D/xojo-syntax-highlight-for-web.git
  ```
- Install dependencies using npm:
  ```bash
  npm install
  ```
- To run the examples, use the following command:
  ```bash
  npm start
  ```

Feel free to contribute to this project and suggest improvements!
