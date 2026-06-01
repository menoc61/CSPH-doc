---
name: pdf
description: Comprehensive PDF manipulation toolkit for reading, creating, merging, splitting, watermarking, OCR, and form filling. Use when working with PDF files - extraction, generation, modification, or any PDF task. Triggers: PDF, .pdf, read PDF, extract text, merge PDF, split PDF, create PDF, watermark, OCR, fill form, encrypt PDF.
---

# PDF Processing Skill

Comprehensive PDF toolkit for reading, creating, and manipulating PDF files.

## When to Use This Skill

- Read or extract text/tables from PDFs
- Create PDFs programmatically (reports, invoices, certificates)
- Merge multiple PDFs into one document
- Split PDFs into separate files
- Rotate pages or add watermarks
- Fill PDF forms programmatically
- Encrypt or decrypt PDFs
- Extract images from PDFs
- Perform OCR on scanned PDFs

## Quick Reference

| Task | Best Tool | Code/Command |
|------|-----------|--------------|
| Merge PDFs | pypdf | `writer.add_page(page)` |
| Split PDFs | pypdf | One page per file |
| Extract text | pdfplumber | `page.extract_text()` |
| Extract tables | pdfplumber | `page.extract_tables()` |
| Create PDFs | reportlab | Canvas or Platypus |
| Command line merge | qpdf | `qpdf --empty --pages ...` |
| OCR scanned PDFs | pytesseract | Convert to image first |
| Fill PDF forms | pdf-lib or pypdf | See forms section |

## Python Libraries

### pypdf - Basic Operations

```python
from pypdf import PdfReader, PdfWriter

# Read a PDF
reader = PdfReader("document.pdf")
print(f"Pages: {len(reader.pages)}")

# Extract text
text = reader.pages[0].extract_text()

# Get metadata
meta = reader.metadata
print(f"Title: {meta.title}")
print(f"Author: {meta.author}")
```

### pdfplumber - Text and Table Extraction

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for i, page in enumerate(pdf.pages):
        # Extract text
        text = page.extract_text()
        
        # Extract tables
        tables = page.extract_tables()
        for table in tables:
            for row in table:
                print(row)
        
        # Get bounding boxes
        for char in page.chars[:10]:
            print(f"'{char['text']}' at ({char['x0']:.0f}, {char['top']:.0f})")
```

### reportlab - Create PDFs

#### Basic PDF Creation

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("hello.pdf", pagesize=letter)
width, height = letter

# Add text
c.drawString(100, height - 100, "Hello World!")
c.drawString(100, height - 120, "This is a PDF created with reportlab")

# Add a line
c.line(100, height - 140, 400, height - 140)

# Save
c.save()
```

#### Create PDF with Multiple Pages

```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak
from reportlab.lib.styles import getSampleStyleSheet

doc = SimpleDocTemplate("report.pdf", pagesize=letter)
styles = getSampleStyleSheet()
story = []

# Add content
title = Paragraph("Report Title", styles['Title'])
story.append(title)
story.append(Spacer(1, 12))

body = Paragraph("This is the body of the report. " * 20, styles['Normal'])
story.append(body)
story.append(PageBreak())

# Page 2
story.append(Paragraph("Page 2", styles['Heading1']))
story.append(Paragraph("Content for page 2", styles['Normal']))

# Build PDF
doc.build(story)
```

#### Professional Tables

```python
from reportlab.platypus import SimpleDocTemplate, Table, TableStyle, Paragraph
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib import colors

# Sample data
data = [
    ['Product', 'Q1', 'Q2', 'Q3', 'Q4'],
    ['Widgets', '120', '135', '142', '158'],
    ['Gadgets', '85', '92', '98', '105']
]

# Create PDF with table
doc = SimpleDocTemplate("report.pdf")
elements = []

# Add title
styles = getSampleStyleSheet()
title = Paragraph("Quarterly Sales Report", styles['Title'])
elements.append(title)

# Add table with advanced styling
table = Table(data)
table.setStyle(TableStyle([
    ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
    ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('FONTSIZE', (0, 0), (-1, 0), 14),
    ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
    ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
    ('GRID', (0, 0), (-1, -1), 1, colors.black)
]))
elements.append(table)

doc.build(elements)
```

#### Subscripts and Superscripts

**IMPORTANT**: Never use Unicode subscript/superscript characters (₀₁₂₃₄₅₆₇₈₉, ⁰¹²³⁴⁵⁶⁷⁸⁹) in ReportLab PDFs. The built-in fonts do not include these glyphs, causing them to render as solid black boxes.

Instead, use ReportLab's XML markup tags in Paragraph objects:

```python
from reportlab.platypus import Paragraph
from reportlab.lib.styles import getSampleStyleSheet

styles = getSampleStyleSheet()

# Subscripts: use <sub> tag
chemical = Paragraph("H<sub>2</sub>O", styles['Normal'])

# Superscripts: use <super> tag
squared = Paragraph("x<super>2</super> + y<super>2</super>", styles['Normal'])
```

## Command-Line Tools

### pdftotext (poppler-utils)

```bash
# Extract text
pdftotext input.pdf output.txt

# Extract text preserving layout
pdftotext -layout input.pdf output.txt

# Extract specific pages
pdftotext -f 1 -l 5 input.pdf output.txt
```

### qpdf

```bash
# Merge PDFs
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf

# Split pages
qpdf input.pdf --pages . 1-5 -- pages1-5.pdf
qpdf input.pdf --pages . 6-10 -- pages6-10.pdf

# Rotate pages
qpdf input.pdf output.pdf --rotate=+90:1

# Remove password
qpdf --password=mypassword --decrypt encrypted.pdf decrypted.pdf
```

### pdftk (if available)

```bash
# Merge
pdftk file1.pdf file2.pdf cat output merged.pdf

# Split
pdftk input.pdf burst

# Rotate
pdftk input.pdf rotate 1east output rotated.pdf
```

## Common Tasks

### Merge Multiple PDFs

```python
from pypdf import PdfReader, PdfWriter

writer = PdfWriter()
for pdf_file in ["file1.pdf", "file2.pdf", "file3.pdf"]:
    reader = PdfReader(pdf_file)
    for page in reader.pages:
        writer.add_page(page)

with open("merged.pdf", "wb") as output:
    writer.write(output)
```

### Split PDF into Pages

```python
from pypdf import PdfReader, PdfWriter
import os

reader = PdfReader("input.pdf")
os.makedirs("split_pages", exist_ok=True)

for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f"split_pages/page_{i+1:03d}.pdf", "wb") as output:
        writer.write(output)
```

### Extract Text from Scanned PDFs

```python
# Requires: pip install pytesseract pdf2image
import pytesseract
from pdf2image import convert_from_path

# Convert PDF to images
images = convert_from_path('scanned.pdf')

# OCR each page
text = ""
for i, image in enumerate(images):
    text += f"Page {i+1}:\n"
    text += pytesseract.image_to_string(image)
    text += "\n\n"

print(text)
```

### Add Watermark

```python
from pypdf import PdfReader, PdfWriter

# Create or load watermark
watermark = PdfReader("watermark.pdf").pages[0]

# Apply to all pages
reader = PdfReader("document.pdf")
writer = PdfWriter()
for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)

with open("watermarked.pdf", "wb") as output:
    writer.write(output)
```

### Extract Images

```bash
# Using pdfimages (poppler-utils)
pdfimages -j input.pdf output_prefix
# This extracts all images as output_prefix-000.jpg, output_prefix-001.jpg, etc.
```

### Password Protection

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("input.pdf")
writer = PdfWriter()
for page in reader.pages:
    writer.add_page(page)

# Add password
writer.encrypt("userpassword", "ownerpassword")

with open("encrypted.pdf", "wb") as output:
    writer.write(output)
```

### Fill PDF Form

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("form.pdf")
writer = PdfWriter()

# Update form fields
for page in reader.pages:
    writer.add_page(page)

# Add updated fields
fields = {
    "name": "John Doe",
    "date": "2026-01-15",
    "amount": "$1,000"
}

writer.update_form_field_values(fields)

with open("filled_form.pdf", "wb") as output:
    writer.write(output)
```

## Quality Standards

When creating PDFs:

- No clipped text
- No overlapping elements
- No broken tables
- No rendering artifacts
- ASCII hyphens only (avoid Unicode dashes)
- Human-readable citations (no placeholder strings)
- Consistent typography and margins
- Proper page breaks
- Clear headers/footers

## Best Practices

1. **Always work on copies** of original PDFs for destructive operations
2. **Validate output** by rendering pages to PNG and inspecting
3. **Use pdfplumber** for table extraction and layout-aware text
4. **Use pypdf** for page-level edits and merging
5. **Use reportlab Platypus** for complex formatted text
6. **Use reportlab XML tags** for subscripts/superscripts
7. **Use command-line tools** for large batches or performance-sensitive workflows
8. **Test font rendering** - built-in fonts don't support all Unicode
