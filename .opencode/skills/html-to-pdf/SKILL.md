---
name: html-to-pdf
description: Convert HTML to PDF with pixel-perfect rendering using Puppeteer/Chrome headless. Full CSS3/HTML5 support, RTL language support, custom headers/footers. Use when user wants pixel-perfect PDF from HTML, web page export, or styled document generation. Triggers: HTML to PDF, convert HTML, export webpage, headless Chrome, Puppeteer, RTL PDF, Hebrew PDF.
---

# HTML to PDF Converter

Convert HTML to PDF with pixel-perfect rendering using headless Chrome.

## When to Use

- Pixel-perfect PDF from HTML/CSS
- High-fidelity web page exports
- Documents requiring CSS3/HTML5 features
- Multi-page reports with complex layouts
- Hebrew/Arabic RTL content
- Web fonts and backgrounds
- JavaScript-rendered content (charts, dynamic data)
- Custom headers/footers

## Quick Usage

### Node.js Script (Recommended)

```bash
# Basic conversion
node html-to-pdf.js input.html output.pdf

# With options
node html-to-pdf.js input.html output.pdf \
  --format=A4 \
  --landscape \
  --margin="20mm" \
  --background
```

### Command Line Options

| Option | Description | Default |
|--------|-------------|---------|
| `--format=` | A4, Letter, Legal, A3, A5 | A4 |
| `--landscape` | Landscape orientation | false |
| `--margin=` | All margins (e.g., "20mm") | 20mm |
| `--margin-top=` | Top margin | 20mm |
| `--margin-right=` | Right margin | 20mm |
| `--margin-bottom=` | Bottom margin | 20mm |
| `--margin-left=` | Left margin | 20mm |
| `--scale=` | Scale factor 0.1-2.0 | 1 |
| `--background` | Print background graphics | true |
| `--no-background` | Don't print backgrounds | - |
| `--header=` | Header HTML template | - |
| `--footer=` | Footer HTML template | - |
| `--wait=` | Wait time for fonts/JS (ms) | 1000 |
| `--rtl` | Force RTL direction | auto |
| `--expect-pages=` | Expected page count | 1 |
| `--no-page-check` | Disable page count check | - |

## Node.js Script

```javascript
// html-to-pdf.js
const puppeteer = require('puppeteer');
const fs = require('fs');
const path = require('path');

async function htmlToPdf(input, output, options = {}) {
  const browser = await puppeteer.launch({
    headless: 'new',
    args: ['--no-sandbox', '--disable-setuid-sandbox']
  });
  
  const page = await browser.newPage();
  
  // Load HTML content
  let html;
  if (input === '-') {
    html = await new Promise((resolve) => {
      let data = '';
      process.stdin.on('data', (chunk) => data += chunk);
      process.stdin.on('end', () => resolve(data));
    });
  } else {
    html = fs.readFileSync(input, 'utf8');
  }
  
  // Set content with base URL for relative resources
  await page.setContent(html, {
    waitUntil: ['networkidle0', 'domcontentloaded'],
    timeout: 30000
  });
  
  // Wait for fonts
  await page.evaluateHandle('document.fonts.ready');
  
  // Detect RTL
  const isRtl = options.rtl || await page.evaluate(() => {
    const text = document.body.innerText;
    const rtlChars = text.match(/[\u0591-\u07FF\uFB1D-\uFDFD\uFE70-\uFEFC]/g);
    return rtlChars && rtlChars.length > text.length * 0.3;
  });
  
  if (isRtl) {
    await page.evaluate(() => {
      document.documentElement.setAttribute('dir', 'rtl');
      document.body.style.direction = 'rtl';
      document.body.style.textAlign = 'right';
    });
  }
  
  // Generate PDF
  const pdf = await page.pdf({
    format: options.format || 'A4',
    landscape: options.landscape || false,
    margin: {
      top: options.marginTop || options.margin || '20mm',
      right: options.marginRight || options.margin || '20mm',
      bottom: options.marginBottom || options.margin || '20mm',
      left: options.marginLeft || options.margin || '20mm'
    },
    printBackground: options.background !== false,
    scale: options.scale || 1,
    displayHeaderFooter: !!(options.header || options.footer),
    headerTemplate: options.header || '',
    footerTemplate: options.footer || `
      <div style="font-size: 9px; width: 100%; text-align: center; padding: 5px;">
        Page <span class="pageNumber"></span> of <span class="totalPages"></span>
      </div>
    `
  });
  
  await browser.close();
  
  // Write output
  fs.writeFileSync(output, pdf);
  
  // Verify page count
  if (options.expectPages && !options.noPageCheck) {
    const actualPages = (pdf.toString('binary').match(/\/Type\s*\/Page[^s]/g) || []).length;
    if (actualPages !== options.expectPages) {
      console.warn(`Warning: Expected ${options.expectPages} pages, got ${actualPages}`);
    }
  }
}

htmlToPdf(process.argv[2], process.argv[3], parseArgs(process.argv.slice(4)))
  .catch(err => {
    console.error('Error:', err.message);
    process.exit(1);
  });

function parseArgs(args) {
  const options = {};
  for (let i = 0; i < args.length; i++) {
    if (args[i].startsWith('--')) {
      const key = args[i].slice(2);
      if (key === 'landscape' || key === 'background' || key === 'no-background' || key === 'rtl' || key === 'no-page-check') {
        options[key.replace(/-([a-z])/g, (m, c) => c.toUpperCase())] = true;
      } else if (args[i + 1] && !args[i + 1].startsWith('--')) {
        options[key.replace(/-([a-z])/g, (m, c) => c.toUpperCase())] = args[i + 1];
        i++;
      }
    }
  }
  return options;
}
```

## CSS Best Practices

### Print-Optimized CSS

```css
@page {
  size: A4;
  margin: 20mm;
}

@media print {
  body {
    font-family: 'Georgia', serif;
    font-size: 11pt;
    line-height: 1.5;
    color: #000;
  }
  
  .no-print { display: none; }
  
  h1 { page-break-before: always; }
  h1:first-of-type { page-break-before: avoid; }
  
  table { page-break-inside: avoid; }
  tr { page-break-inside: avoid; }
  
  img { max-width: 100%; page-break-inside: avoid; }
}
```

### RTL Support (Hebrew, Arabic)

```html
<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
  <meta charset="UTF-8">
  <link href="https://fonts.googleapis.com/css2?family=Heebo:wght@400;700&display=swap" rel="stylesheet">
  <style>
    body {
      direction: rtl;
      text-align: right;
      font-family: 'Heebo', sans-serif;
    }
  </style>
</head>
<body>
  <h1>שלום עולם</h1>
  <p>זהו מסמך RTL בעברית</p>
</body>
</html>
```

### Avoid Common Issues

```css
/* ❌ BAD - causes orphan whitespace */
.page-break-avoid {
  page-break-inside: avoid;
}

/* ✅ GOOD - use flow-based layout */
.content {
  /* let content flow naturally between pages */
}

/* ❌ BAD - backgrounds on html/body cause extra pages */
html, body { background: white; }

/* ✅ GOOD - put backgrounds on a container */
.page-container {
  background: white;
  min-height: 297mm; /* A4 height */
  width: 210mm;       /* A4 width */
  margin: 0 auto;
}

/* ❌ BAD - content overflow */
.content { width: 100vw; }

/* ✅ GOOD - control overflow */
.content { 
  width: 100%;
  box-sizing: border-box;
  overflow: hidden;
}
```

## Hebrew/RTL Best Practices

1. **Set lang attribute**: `<html lang="he" dir="rtl">`
2. **Use UTF-8 encoding**: `<meta charset="UTF-8">`
3. **Add CSS direction**: `direction: rtl; text-align: right;`
4. **Use Hebrew fonts**: Heebo, Noto Sans Hebrew, Assistant
5. **Allow wait time** for web fonts: `--wait=2000`
6. **Test rendering** with Hebrew content

## Examples

### Basic Report

```bash
node html-to-pdf.js report.html report.pdf --format=A4
```

### Hebrew Document

```bash
node html-to-pdf.js hebrew.html output.pdf --rtl --wait=2000
```

### Landscape Presentation

```bash
node html-to-pdf.js slides.html slides.pdf --landscape --format=A4
```

### From Stdin

```bash
echo "<h1>Quick PDF</h1>" | node html-to-pdf.js - output.pdf
```

### Multi-page Invoice

```bash
node html-to-pdf.js invoice.html invoice.pdf \
  --format=A4 \
  --margin="15mm" \
  --header='<div style="font-size:9px;width:100%;text-align:right;padding:5px;">Company Name</div>' \
  --footer='<div style="font-size:9px;width:100%;text-align:center;padding:5px;">Page <span class="pageNumber"></span> of <span class="totalPages"></span></div>'
```

## Installation

```bash
npm install puppeteer
```

Or globally:
```bash
npm install -g puppeteer
```

## Auto-Fit Content (CRITICAL)

Always verify PDF output before delivery:

1. Check for horizontal overflow (text cut at sides)
2. Check for vertical overflow (empty pages, content spillover)
3. Read the PDF using the Read tool to visually inspect
4. If issues found, fix CSS and regenerate (max 5 attempts)
5. Use `scale=1.0` for multi-page PDFs

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Hebrew text not showing | Use web font (Heebo), set --rtl, --wait=2000 |
| Content cut off | Reduce --scale to 0.9, reduce font size |
| Extra blank pages | Set explicit page dimensions, use overflow:hidden |
| Fonts not loading | Increase --wait time, use system fonts as fallback |
| Images missing | Use absolute paths or embed as base64 |
| Wrong page count | Check @page CSS, remove extra body content |
