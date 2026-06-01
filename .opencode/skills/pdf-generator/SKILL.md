---
name: pdf-generator
description: Generate professional, polished PDF documents like Kimi - reports, invoices, contracts, certificates with beautiful typography, layouts, and styling. Use when creating high-quality PDFs from any source (Markdown, HTML, data, templates). Triggers: generate PDF, professional PDF, report PDF, beautiful PDF, invoice PDF, certificate, contract PDF, polished document.
---

# Professional PDF Generator (Kimi-Quality)

Generate stunning, professional PDF documents with Kimi-level quality.

## When to Use

- Create beautiful reports, invoices, contracts
- Generate certificates, diplomas, official documents
- Produce marketing materials, brochures
- Create data-driven PDFs from JSON/CSV
- Generate batch PDFs (invoices, reports)
- Need polished typography and professional layout

## Tool Selection

| Source | Best Tool | Why |
|--------|-----------|-----|
| Markdown | pandoc | Native support, TOC, templates |
| HTML/CSS | weasyprint | Best CSS support, no LaTeX |
| Data/JSON | reportlab | Programmatic, precise control |
| Simple text | fpdf2 | Lightweight, fast |
| High-fidelity | Puppeteer | Pixel-perfect, JS rendering |

**Default recommendation: WeasyPrint for HTML-based documents**

## Quick Start

### WeasyPrint (HTML to Beautiful PDF)

```bash
weasyprint input.html output.pdf
```

```python
from weasyprint import HTML, CSS

html_content = """
<!DOCTYPE html>
<html>
<head>
<style>
  @page { size: A4; margin: 2cm; }
  body { font-family: 'Helvetica', sans-serif; }
  .cover { page-break-after: always; }
  h1 { color: #2563eb; }
</style>
</head>
<body>
  <div class="cover">
    <h1>Annual Report 2026</h1>
  </div>
  <h2>Executive Summary</h2>
  <p>Content here...</p>
</body>
</html>
"""

HTML(string=html_content).write_pdf('output.pdf')
```

### WeasyPrint with Custom CSS

```python
from weasyprint import HTML, CSS

HTML(string=html_content).write_pdf(
    'output.pdf',
    stylesheets=[CSS(string='@page { size: A4; }')]
)
```

## Professional Templates

### Cover Page Template

```html
<!DOCTYPE html>
<html>
<head>
<style>
  @page { size: A4; margin: 0; }
  @page :first { margin: 0; }
  
  .cover {
    width: 210mm;
    height: 297mm;
    background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
    color: white;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    page-break-after: always;
  }
  
  .cover h1 { font-size: 48pt; margin: 0; }
  .cover .subtitle { font-size: 18pt; opacity: 0.9; margin-top: 20px; }
  .cover .date { margin-top: 40px; font-size: 12pt; opacity: 0.7; }
</style>
</head>
<body>
  <div class="cover">
    <h1>ANNUAL REPORT</h1>
    <div class="subtitle">Fiscal Year 2025-2026</div>
    <div class="date">January 2026</div>
  </div>
  
  <!-- Content pages -->
  <div class="page">
    <h1>Executive Summary</h1>
    <p>This report presents the key accomplishments...</p>
  </div>
</body>
</html>
```

### Invoice Template (Kimi-Quality)

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<style>
  @page { size: A4; margin: 1.5cm; }
  
  * { box-sizing: border-box; }
  
  body {
    font-family: 'Helvetica Neue', Arial, sans-serif;
    font-size: 10pt;
    color: #1f2937;
    line-height: 1.4;
  }
  
  .header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 40px;
    padding-bottom: 20px;
    border-bottom: 2px solid #2563eb;
  }
  
  .company-info h1 {
    color: #2563eb;
    margin: 0 0 5px 0;
    font-size: 24pt;
  }
  
  .invoice-meta {
    text-align: right;
  }
  
  .invoice-meta h2 {
    color: #6b7280;
    margin: 0;
    font-size: 18pt;
  }
  
  .invoice-number {
    font-size: 14pt;
    font-weight: bold;
    color: #1f2937;
  }
  
  .parties {
    display: flex;
    justify-content: space-between;
    margin-bottom: 30px;
  }
  
  .party {
    width: 45%;
  }
  
  .party h3 {
    color: #6b7280;
    font-size: 9pt;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin: 0 0 8px 0;
  }
  
  .party p { margin: 2px 0; }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin: 20px 0;
  }
  
  thead {
    background: #1e3a8a;
    color: white;
  }
  
  th, td {
    padding: 12px 8px;
    text-align: left;
  }
  
  tbody tr:nth-child(even) {
    background: #f9fafb;
  }
  
  tbody tr:hover {
    background: #f3f4f6;
  }
  
  .totals {
    margin-top: 20px;
    margin-left: auto;
    width: 300px;
  }
  
  .totals .row {
    display: flex;
    justify-content: space-between;
    padding: 8px 0;
  }
  
  .totals .total {
    border-top: 2px solid #1f2937;
    margin-top: 8px;
    padding-top: 12px;
    font-size: 14pt;
    font-weight: bold;
    color: #2563eb;
  }
  
  .footer {
    margin-top: 60px;
    padding-top: 20px;
    border-top: 1px solid #e5e7eb;
    font-size: 9pt;
    color: #6b7280;
    text-align: center;
  }
</style>
</head>
<body>
  <div class="header">
    <div class="company-info">
      <h1>ACME Corp</h1>
      <p>123 Business St, City, Country</p>
      <p>contact@acme.com</p>
    </div>
    <div class="invoice-meta">
      <h2>INVOICE</h2>
      <div class="invoice-number">#INV-2026-001</div>
      <p>Date: 2026-01-15</p>
      <p>Due: 2026-02-15</p>
    </div>
  </div>
  
  <div class="parties">
    <div class="party">
      <h3>Bill To</h3>
      <p><strong>Client Corp</strong></p>
      <p>456 Client Ave</p>
      <p>City, Country</p>
    </div>
    <div class="party">
      <h3>Ship To</h3>
      <p><strong>Client Corp</strong></p>
      <p>456 Client Ave</p>
      <p>City, Country</p>
    </div>
  </div>
  
  <table>
    <thead>
      <tr>
        <th>Description</th>
        <th>Qty</th>
        <th>Unit Price</th>
        <th>Total</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Service A</td>
        <td>10</td>
        <td>$100</td>
        <td>$1,000</td>
      </tr>
      <tr>
        <td>Service B</td>
        <td>5</td>
        <td>$200</td>
        <td>$1,000</td>
      </tr>
    </tbody>
  </table>
  
  <div class="totals">
    <div class="row">
      <span>Subtotal:</span>
      <span>$2,000</span>
    </div>
    <div class="row">
      <span>Tax (10%):</span>
      <span>$200</span>
    </div>
    <div class="row total">
      <span>Total:</span>
      <span>$2,200</span>
    </div>
  </div>
  
  <div class="footer">
    <p>Thank you for your business!</p>
    <p>Payment terms: Net 30 days</p>
  </div>
</body>
</html>
```

### Report Template (Multi-Page)

```python
from weasyprint import HTML, CSS
from datetime import datetime

def generate_report(title, sections, data, output_path):
    html = f"""
    <!DOCTYPE html>
    <html>
    <head>
    <style>
      @page {{
        size: A4;
        margin: 2.5cm 2cm;
        @top-left {{
          content: "CSPH GPL Tracking";
          font-size: 9pt;
          color: #6b7280;
        }}
        @top-right {{
          content: "{title}";
          font-size: 9pt;
          color: #6b7280;
        }}
        @bottom-center {{
          content: "Page " counter(page) " of " counter(pages);
          font-size: 9pt;
          color: #6b7280;
        }}
      }}
      
      @page :first {{
        margin: 0;
        @top-left {{ content: none; }}
        @top-right {{ content: none; }}
        @bottom-center {{ content: none; }}
      }}
      
      body {{
        font-family: 'Helvetica Neue', Arial, sans-serif;
        font-size: 10pt;
        color: #1f2937;
        line-height: 1.5;
      }}
      
      .cover {{
        page-break-after: always;
        height: 100vh;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%);
        color: white;
        text-align: center;
      }}
      
      .cover h1 {{
        font-size: 48pt;
        margin: 0 0 20px 0;
      }}
      
      h1 {{
        color: #1e3a8a;
        font-size: 24pt;
        page-break-after: avoid;
      }}
      
      h2 {{
        color: #3b82f6;
        font-size: 18pt;
        margin-top: 30px;
        page-break-after: avoid;
      }}
      
      .kpi-grid {{
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 20px;
        margin: 30px 0;
      }}
      
      .kpi {{
        background: #f3f4f6;
        padding: 20px;
        border-radius: 8px;
        text-align: center;
        page-break-inside: avoid;
      }}
      
      .kpi .value {{
        font-size: 32pt;
        font-weight: bold;
        color: #1e3a8a;
        margin: 10px 0;
      }}
      
      .kpi .label {{
        font-size: 9pt;
        color: #6b7280;
        text-transform: uppercase;
        letter-spacing: 1px;
      }}
      
      table {{
        width: 100%;
        border-collapse: collapse;
        margin: 20px 0;
        page-break-inside: avoid;
      }}
      
      thead {{
        background: #1e3a8a;
        color: white;
      }}
      
      th, td {{
        padding: 10px;
        text-align: left;
        border-bottom: 1px solid #e5e7eb;
      }}
      
      .chart {{
        background: #f9fafb;
        padding: 20px;
        border-radius: 8px;
        margin: 20px 0;
        text-align: center;
      }}
    </style>
    </head>
    <body>
      <div class="cover">
        <h1>{title}</h1>
        <p>Generated: {datetime.now().strftime('%B %d, %Y')}</p>
      </div>
      
      <h1>Executive Summary</h1>
      <p>{sections['summary']}</p>
      
      <div class="kpi-grid">
        <div class="kpi">
          <div class="label">Active Trucks</div>
          <div class="value">{data['trucks']}</div>
        </div>
        <div class="kpi">
          <div class="label">Deliveries</div>
          <div class="value">{data['deliveries']}</div>
        </div>
        <div class="kpi">
          <div class="label">Revenue</div>
          <div class="value">${data['revenue']:,}</div>
        </div>
      </div>
      
      <h1>Detailed Analysis</h1>
      {sections['details']}
    </body>
    </html>
    """
    
    HTML(string=html).write_pdf(output_path)
    return output_path
```

## ReportLab Templates (Python)

### Certificate Template

```python
from reportlab.lib.pagesizes import landscape, A4
from reportlab.pdfgen import canvas
from reportlab.lib.colors import HexColor
from reportlab.lib.units import cm

def create_certificate(name, course, date, output_path):
    c = canvas.Canvas(output_path, pagesize=landscape(A4))
    width, height = landscape(A4)
    
    # Decorative border
    c.setStrokeColor(HexColor('#1e3a8a'))
    c.setLineWidth(3)
    c.rect(1*cm, 1*cm, width-2*cm, height-2*cm)
    
    c.setLineWidth(1)
    c.rect(1.5*cm, 1.5*cm, width-3*cm, height-3*cm)
    
    # Header
    c.setFillColor(HexColor('#1e3a8a'))
    c.setFont("Helvetica-Bold", 36)
    c.drawCentredString(width/2, height-4*cm, "CERTIFICATE")
    
    c.setFont("Helvetica", 18)
    c.setFillColor(HexColor('#6b7280'))
    c.drawCentredString(width/2, height-5*cm, "OF COMPLETION")
    
    # Body
    c.setFillColor(HexColor('#1f2937'))
    c.setFont("Helvetica", 14)
    c.drawCentredString(width/2, height-8*cm, "This is to certify that")
    
    c.setFont("Helvetica-Bold", 32)
    c.setFillColor(HexColor('#1e3a8a'))
    c.drawCentredString(width/2, height-10*cm, name)
    
    c.setFont("Helvetica", 14)
    c.setFillColor(HexColor('#1f2937'))
    c.drawCentredString(width/2, height-12*cm, "has successfully completed")
    
    c.setFont("Helvetica-Bold", 18)
    c.drawCentredString(width/2, height-14*cm, course)
    
    # Date
    c.setFont("Helvetica", 12)
    c.drawCentredString(width/2, 3*cm, f"Awarded on {date}")
    
    # Signature lines
    c.line(3*cm, 4*cm, 8*cm, 4*cm)
    c.line(width-8*cm, 4*cm, width-3*cm, 4*cm)
    
    c.setFont("Helvetica", 10)
    c.drawString(3*cm, 3.5*cm, "Director")
    c.drawString(width-8*cm, 3.5*cm, "Instructor")
    
    c.save()
    return output_path
```

## Batch Generation

### Generate from JSON Data

```python
import json
from weasyprint import HTML

# Load data
with open('invoices.json') as f:
    invoices = json.load(f)

# Generate PDF for each
for invoice in invoices:
    html = render_invoice_template(invoice)
    HTML(string=html).write_pdf(f"output/{invoice['number']}.pdf")
```

### CSV to Reports

```python
import csv
from weasyprint import HTML

with open('data.csv') as f:
    reader = csv.DictReader(f)
    for row in reader:
        html = render_report(row)
        HTML(string=html).write_pdf(f"output/{row['id']}.pdf")
```

## Best Practices (Kimi-Quality)

1. **Use web fonts** for better typography (Google Fonts, local fonts)
2. **Consistent color scheme** - stick to 2-3 primary colors
3. **White space** - generous margins and padding
4. **Hierarchy** - clear heading hierarchy with size/weight
5. **Tables** - striped rows, hover states, clear headers
6. **Page breaks** - control with CSS, avoid orphaned content
7. **Headers/footers** - via @page CSS for multi-page docs
8. **Cover pages** - gradient backgrounds, large typography
9. **Charts** - use Chart.js or matplotlib with embedded images
10. **Validation** - always render and inspect output

## Installation

```bash
# Python
pip install weasyprint reportlab

# Node.js (for Puppeteer/Playwright)
npm install puppeteer

# CLI tools
brew install weasyprint pandoc
# or
pip install weasyprint
```

## Performance Tips

- WeasyPrint: ~50ms per page
- Puppeteer: ~500ms overhead + 100ms per page
- ReportLab: ~10ms per page (fastest)
- Pandoc: ~200ms for typical documents

## Quality Checklist

Before delivery, verify:
- [ ] Consistent typography across all pages
- [ ] No clipped text or overflowing content
- [ ] No broken tables or missing rows
- [ ] Page numbers in footer
- [ ] Headers consistent across pages
- [ ] Colors match design system
- [ ] No rendering artifacts
- [ ] File size reasonable (<10MB for typical docs)
