---
name: markdown-to-pdf
description: Convert Markdown documents to beautifully formatted PDFs using Pandoc with LaTeX templates, custom styling, and professional layouts. Use when generating PDFs from technical documentation, memos, reports, or any markdown content. Triggers: markdown to PDF, pandoc, md to PDF, technical doc PDF, report from markdown.
---

# Markdown to PDF Converter (Pandoc)

Convert Markdown to professional PDFs using Pandoc with LaTeX.

## When to Use

- Convert technical documentation to PDF
- Generate memos, reports from markdown
- Need bibliography and cross-references
- Want LaTeX-quality typography
- Need table of contents, sections, lists
- Academic or professional document generation

## Installation

```bash
# Install Pandoc
# macOS
brew install pandoc

# Windows
choco install pandoc

# Linux
apt-get install pandoc

# Install LaTeX (required for PDF output)
# macOS
brew install --cask mactex
# or
brew install --cask basictex

# Windows
# Install MiKTeX from https://miktex.org/

# Linux
apt-get install texlive-full
```

## Quick Start

### Basic Conversion

```bash
pandoc input.md -o output.pdf
```

### With Table of Contents

```bash
pandoc input.md -o output.pdf --toc --toc-depth=3
```

### Custom Title Page

```bash
pandoc input.md -o output.pdf \
  --title-page \
  --title="My Report" \
  --author="John Doe" \
  --date="2026-01-15"
```

## Command-Line Options

| Option | Description |
|--------|-------------|
| `--toc` | Include table of contents |
| `--toc-depth=N` | TOC depth (default 3) |
| `--from=markdown` | Input format |
| `--to=pdf` | Output format |
| `--pdf-engine=xelatex` | PDF engine (xelatex recommended) |
| `--variable=geometry:margin=1in` | Set margins |
| `--variable=fontsize:11pt` | Font size |
| `--variable=documentclass:article` | Document class |
| `--css=style.css` | Custom CSS (HTML output) |
| `--highlight-style=zenburn` | Code highlighting |
| `--number-sections` | Number sections |
| `--bibliography=refs.bib` | Bibliography |
| `--citeproc` | Process citations |

## Document Templates

### Technical Documentation

```yaml
---
title: "CSPH GPL Tracking System"
subtitle: "Technical Documentation"
author: "DTI Solutions"
date: "January 15, 2026"
geometry: margin=2.5cm
fontsize: 11pt
mainfont: "Helvetica"
colorlinks: true
linkcolor: "blue"
urlcolor: "blue"
toccolor: "black"
documentclass: article
---

# Executive Summary

This document describes the IoT-based LPG tracking system...

## Architecture Overview

The system comprises three main components:
- IoT prototype on tank trucks
- RFID/PDA system for bottles
- Cloud platform

## Table of Contents

The TOC is auto-generated from headings.
```

```bash
pandoc document.md -o output.pdf --pdf-engine=xelatex
```

### Report with Bibliography

```markdown
---
title: "Research Report"
author: "John Doe"
bibliography: references.bib
csl: apa.csl
---

# Introduction

According to recent studies [@smith2024], the IoT market...

# Methodology

## Data Collection

We collected data from [@johnson2023]...

# References
```

```bash
pandoc report.md -o report.pdf --citeproc --bibliography=references.bib
```

### Multi-Language Support

```yaml
---
title: "Système de Traçabilité"
author: "CSPH"
lang: fr
mainfont: "DejaVu Sans"
---
```

```bash
pandoc -V lang=fr doc.md -o doc.pdf
```

## Custom Templates

### Create Custom LaTeX Template

Save as `mytemplate.tex`:

```latex
\documentclass[11pt,a4paper]{article}

\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage[french]{babel}
\usepackage{graphicx}
\usepackage{hyperref}
\usepackage{geometry}
\usepackage{xcolor}

\geometry{
  a4paper,
  left=2.5cm,
  right=2.5cm,
  top=2.5cm,
  bottom=2.5cm
}

\definecolor{primarycolor}{RGB}{30, 58, 138}

\hypersetup{
  colorlinks=true,
  linkcolor=primarycolor,
  filecolor=primarycolor,
  urlcolor=primarycolor
}

\title{$title$}
\author{$for(author)$$author$$sep$ \and $endfor$}
\date{$date$}

\begin{document}

$if(titlepage)$
\begin{titlepage}
  \begin{center}
    \vspace*{2cm}
    {\Huge\bfseries\color{primarycolor} $title$}\\[1cm]
    {\Large $subtitle$}\\[2cm]
    {\large $for(author)$$author$$sep$ \\ $endfor$}\\[1cm]
    {\large $date$}
  \end{center}
\end{titlepage}
$endif$

$if(toc)$
\tableofcontents
\newpage
$endif$

$body$

\end{document}
```

Use the template:

```bash
pandoc input.md -o output.pdf \
  --template=mytemplate.tex \
  --pdf-engine=xelatex \
  --toc
```

## Python Integration

### Simple Conversion

```python
import pypandoc

# Convert markdown to PDF
output = pypandoc.convert_file(
    'document.md',
    'pdf',
    outputfile='output.pdf',
    extra_args=['--pdf-engine=xelatex', '--toc']
)
```

### With Custom Styling

```python
import pypandoc

output = pypandoc.convert_file(
    'document.md',
    'pdf',
    outputfile='output.pdf',
    extra_args=[
        '--pdf-engine=xelatex',
        '--toc',
        '--toc-depth=3',
        '--highlight-style=tango',
        '--variable=geometry:margin=2.5cm',
        '--variable=fontsize:11pt'
    ]
)
```

### Batch Conversion

```python
import pypandoc
from pathlib import Path

# Convert all markdown files
for md_file in Path('docs').glob('**/*.md'):
    output_path = md_file.with_suffix('.pdf')
    pypandoc.convert_file(
        str(md_file),
        'pdf',
        outputfile=str(output_path),
        extra_args=['--pdf-engine=xelatex', '--toc']
    )
    print(f"Converted: {md_file} -> {output_path}")
```

## Code Highlighting

Pandoc supports syntax highlighting with various styles:

```bash
# Available styles
pandoc --list-highlight-styles

# Use a style
pandoc code.md -o code.pdf --highlight-style=zenburn
pandoc code.md -o code.pdf --highlight-style=tango
pandoc code.md -o code.pdf --highlight-style=monokai
```

Common styles:
- `zenburn` - dark, easy on eyes
- `tango` - light, classic
- `monokai` - dark, modern
- `pygments` - colorful
- `breezedark` - dark blue

## Code Blocks in Markdown

````markdown
```python
def hello():
    print("Hello, World!")
```

```javascript
const greet = (name) => `Hello, ${name}!`;
```

```bash
npm install
```
````

## Tables

### Markdown Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |
```

### Grid Tables (more control)

```markdown
+-----------+-----------+-----------+
| Column 1  | Column 2  | Column 3  |
+===========+===========+===========+
| Cell 1    | Cell 2    | Cell 3    |
+-----------+-----------+-----------+
| Cell 4    | Cell 5    | Cell 6    |
+-----------+-----------+-----------+
```

### Pipe Tables with Alignment

```markdown
| Left     | Center    | Right     |
|:---------|:---------:|----------:|
| L1       | C1        | R1        |
| L2       | C2        | R2        |
```

## Math Equations

### Inline Math

```markdown
The equation $E = mc^2$ is famous.
```

### Display Math

```markdown
$$
\begin{aligned}
\nabla \times \vec{\mathbf{B}} - \frac{1}{c}\frac{\partial\vec{\mathbf{E}}}{\partial t} &= \frac{4\pi}{c}\vec{\mathbf{j}} \\
\nabla \cdot \vec{\mathbf{E}} &= 4\pi\rho
\end{aligned}
$$
```

## Advanced Features

### Cross-References

```markdown
# Section A {#sec:a}

See section [-@sec:a] for details.

![Caption](image.png){#fig:myimage}

See figure [-@fig:myimage].
```

### Footnotes

```markdown
This text has a footnote[^1].

[^1]: This is the footnote content.
```

### Citations

```markdown
According to @smith2024, the results show...

[@smith2024; @johnson2023] discuss...
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "pdflatex not found" | Install LaTeX: MiKTeX (Win), MacTeX (Mac), texlive (Linux) |
| Unicode characters broken | Use `--pdf-engine=xelatex` with mainfont |
| Missing fonts | Install fonts or use system fonts |
| Code blocks overflow | Use `--variable=fontsize:10pt` or smaller |
| TOC not generated | Add `--toc` flag |
| Citations broken | Add `--citeproc` and bibliography |

## Common Workflows

### Technical Spec to PDF

```bash
pandoc spec.md \
  -o spec.pdf \
  --pdf-engine=xelatex \
  --toc \
  --toc-depth=3 \
  --number-sections \
  --variable=geometry:margin=2cm \
  --variable=fontsize:11pt \
  --highlight-style=tango
```

### Memo with Letterhead

```bash
pandoc memo.md \
  -o memo.pdf \
  --pdf-engine=xelatex \
  --variable=geometry:margin=1in \
  --variable=documentclass:letter \
  --variable=fontsize:11pt
```

### Academic Paper

```bash
pandoc paper.md \
  -o paper.pdf \
  --pdf-engine=xelatex \
  --bibliography=references.bib \
  --citeproc \
  --csl=ieee.csl \
  --number-sections \
  --toc
```

## Performance

- Simple doc: < 1 second
- Large doc (100+ pages): 5-10 seconds
- With bibliography: 3-5 seconds
- First run (installs packages): 30-60 seconds

## Best Practices

1. **Use YAML metadata** for title, author, date
2. **Use `--pdf-engine=xelatex`** for Unicode and font support
3. **Add `--toc`** for documents > 3 pages
4. **Use `--number-sections`** for technical docs
5. **Set explicit margins** with `--variable=geometry:margin=...`
6. **Validate output** with PDF reader
7. **Use version control** for source markdown
8. **Test with simple docs** before complex ones
