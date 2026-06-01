---
name: pdf-review
description: Review, validate, and quality-check PDF documents before delivery. Use when verifying PDF rendering, checking for issues, or doing visual QA. Triggers: review PDF, validate PDF, PDF QA, render PDF, check PDF, verify PDF, PDF inspection.
---

# PDF Review & Validation

Review and validate PDF documents for quality, rendering issues, and compliance.

## When to Use

- Verify PDF rendering quality
- Check for visual issues (clipping, overlapping)
- Validate accessibility (PDF/UA, WCAG)
- Inspect before delivery
- QA batch-generated PDFs
- Compare versions
- Check metadata and security

## Visual Review Process

### Step 1: Render to PNG

```bash
# Using Poppler
pdftoppm -png -r 150 input.pdf page

# Higher resolution for detailed review
pdftoppm -png -r 300 input.pdf page_hires
```

Options:
- `-r N` - Resolution (DPI)
- `-png` - PNG format (also: jpeg, tiff)
- `-f N` - First page
- `-l N` - Last page
- `-W` - Width in pixels
- `-H` - Height in pixels
- `-aa` - Anti-aliasing
- `-aaVector` - Vector antialiasing

### Step 2: Inspect Pages

```python
# Generate all pages
from pdf2image import convert_from_path

images = convert_from_path('input.pdf', dpi=150)
for i, image in enumerate(images):
    image.save(f'page_{i+1:03d}.png', 'PNG')
    print(f"Saved page_{i+1:03d}.png")
```

### Step 3: Quality Checklist

For each page, verify:

- [ ] No clipped text
- [ ] No overlapping elements
- [ ] No broken tables
- [ ] No rendering artifacts
- [ ] Consistent typography
- [ ] Proper margins
- [ ] Headers/footers aligned
- [ ] Page numbers present
- [ ] No blank pages unexpectedly
- [ ] Images sharp and clear
- [ ] Colors match design

## Automated Validation

### Python Validation Script

```python
import pdfplumber
from pypdf import PdfReader
import sys

def validate_pdf(pdf_path):
    issues = []
    
    # Check if file exists and is valid PDF
    try:
        reader = PdfReader(pdf_path)
    except Exception as e:
        return [f"Invalid PDF: {e}"]
    
    num_pages = len(reader.pages)
    print(f"Pages: {num_pages}")
    
    # Check metadata
    meta = reader.metadata
    if meta:
        print(f"Title: {meta.title}")
        print(f"Author: {meta.author}")
        print(f"Producer: {meta.producer}")
    
    # Check each page
    with pdfplumber.open(pdf_path) as pdf:
        for i, page in enumerate(pdf.pages):
            print(f"\n--- Page {i+1} ---")
            print(f"Size: {page.width} x {page.height}")
            
            # Check for text overflow
            text = page.extract_text()
            if not text or not text.strip():
                issues.append(f"Page {i+1}: No text content")
            
            # Check for tables
            tables = page.extract_tables()
            if tables:
                print(f"Tables: {len(tables)}")
                for j, table in enumerate(tables):
                    if not table or len(table) == 0:
                        issues.append(f"Page {i+1}, Table {j+1}: Empty table")
            
            # Check images
            if hasattr(page, 'images'):
                img_count = len(page.images)
                print(f"Images: {img_count}")
                for img in page.images:
                    if img['width'] < 10 or img['height'] < 10:
                        issues.append(f"Page {i+1}: Suspicious small image")
    
    # Check for encryption
    if reader.is_encrypted:
        issues.append("PDF is encrypted")
    
    return issues

if __name__ == "__main__":
    issues = validate_pdf(sys.argv[1])
    if issues:
        print("\n=== ISSUES FOUND ===")
        for issue in issues:
            print(f"  - {issue}")
    else:
        print("\n=== NO ISSUES ===")
```

## PDF/A Compliance Check

```bash
# Using verapdf
verapdf --flavour 1b input.pdf

# Using GhostPDL
gs -dPDFA -dBATCH -dNOPAUSE -sDEVICE=pdfwrite \
   -sOutputFile=output.pdf input.pdf
```

```python
# Using python
import pikepdf

with pikepdf.open('input.pdf') as pdf:
    # Check if PDF/A
    if '/Metadata' in pdf.trailer:
        meta = pdf.trailer['/Metadata']
        print(f"Has metadata: {meta}")
    
    # Check version
    print(f"PDF version: {pdf.pdf_version}")
```

## Accessibility Check

### Using pdfua-tools

```bash
# Check PDF/UA compliance
pdfua-tools check input.pdf

# Extract structure
pdfua-tools extract input.pdf -o structure.xml
```

### Manual Accessibility Check

```python
from pypdf import PdfReader

def check_accessibility(pdf_path):
    reader = PdfReader(pdf_path)
    issues = []
    
    for i, page in enumerate(reader.pages):
        # Check for tagged content
        if '/StructTreeRoot' not in page:
            issues.append(f"Page {i+1}: Not tagged (no structure tree)")
        
        # Check for alt text on images
        if '/Resources' in page and '/XObject' in page['/Resources']:
            xobjects = page['/Resources']['/XObject']
            for name, obj in xobjects.items():
                if obj.get('/Subtype') == '/Image':
                    if '/Alt' not in obj:
                        issues.append(f"Page {i+1}: Image missing alt text")
    
    return issues
```

## Metadata Inspection

```python
from pypdf import PdfReader
from datetime import datetime

def inspect_metadata(pdf_path):
    reader = PdfReader(pdf_path)
    meta = reader.metadata
    
    if not meta:
        return "No metadata"
    
    info = {
        "Title": meta.title,
        "Author": meta.author,
        "Subject": meta.subject,
        "Creator": meta.creator,
        "Producer": meta.producer,
        "Created": meta.creation_date,
        "Modified": meta.modification_date,
    }
    
    for key, value in info.items():
        if value:
            print(f"{key}: {value}")
    
    return info

# Strip sensitive metadata
def clean_metadata(pdf_path, output_path):
    from pypdf import PdfWriter
    
    reader = PdfReader(pdf_path)
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    # Remove all metadata
    writer.add_metadata({})
    
    with open(output_path, "wb") as output:
        writer.write(output)
```

## Render Comparison

### Compare Two PDF Versions

```python
from pdf2image import convert_from_path
from PIL import Image, ImageChops
import sys

def compare_pdfs(pdf1_path, pdf2_path, diff_path):
    images1 = convert_from_path(pdf1_path)
    images2 = convert_from_path(pdf2_path)
    
    if len(images1) != len(images2):
        print(f"Different page counts: {len(images1)} vs {len(images2)}")
        return
    
    total_diff = 0
    for i, (img1, img2) in enumerate(zip(images1, images2)):
        if img1.size != img2.size:
            print(f"Page {i+1}: Different sizes")
            continue
        
        diff = ImageChops.difference(img1, img2)
        bbox = diff.getbbox()
        
        if bbox:
            # Save diff image
            diff_path_full = f"{diff_path}_page_{i+1}.png"
            diff.save(diff_path_full)
            
            # Calculate diff area
            diff_pixels = sum(1 for pixel in diff.getdata() if any(p > 10 for p in pixel))
            total_diff += diff_pixels
            print(f"Page {i+1}: Differences found ({diff_pixels} pixels)")
        else:
            print(f"Page {i+1}: Identical")
    
    print(f"\nTotal different pixels: {total_diff}")

compare_pdfs(sys.argv[1], sys.argv[2], sys.argv[3])
```

## Security Audit

### Check for Sensitive Data

```python
from pypdf import PdfReader
import re

def audit_security(pdf_path):
    reader = PdfReader(pdf_path)
    issues = []
    
    # Check encryption
    if not reader.is_encrypted:
        issues.append("PDF is not encrypted")
    
    # Extract text and check for sensitive patterns
    full_text = ""
    for page in reader.pages:
        full_text += page.extract_text() + "\n"
    
    # Email pattern
    emails = re.findall(r'[\w\.-]+@[\w\.-]+', full_text)
    if emails:
        issues.append(f"Contains {len(emails)} email addresses")
    
    # Credit card pattern
    cc = re.findall(r'\b(?:\d[ -]*?){13,16}\b', full_text)
    if cc:
        issues.append(f"Possible credit card numbers: {len(cc)}")
    
    # SSN pattern (US)
    ssn = re.findall(r'\b\d{3}-\d{2}-\d{4}\b', full_text)
    if ssn:
        issues.append(f"Possible SSNs: {len(ssn)}")
    
    # Phone numbers
    phones = re.findall(r'\b\d{3}[-.]?\d{3}[-.]?\d{4}\b', full_text)
    if phones:
        issues.append(f"Contains {len(phones)} phone numbers")
    
    return issues
```

## Redaction Verification

```python
from pypdf import PdfReader

def verify_redaction(pdf_path):
    """Verify that redactions are actually removed, not just visually hidden"""
    reader = PdfReader(pdf_path)
    issues = []
    
    for i, page in enumerate(reader.pages):
        text = page.extract_text()
        
        # Check for common redaction patterns
        if "[REDACTED]" in text:
            issues.append(f"Page {i+1}: Found [REDACTED] marker (not actually removed)")
        
        # Check for redaction annotations
        if '/Annots' in page:
            annots = page['/Annots']
            for annot in annots:
                if annot.get_object().get('/Subtype') == '/Redact':
                    issues.append(f"Page {i+1}: Redaction annotation found (text may still be extractable)")
    
    return issues
```

## Font and Encoding Check

```python
import pdfplumber

def check_fonts(pdf_path):
    with pdfplumber.open(pdf_path) as pdf:
        fonts = set()
        
        for page in pdf.pages:
            for char in page.chars:
                fonts.add(char.get('fontname', 'unknown'))
        
        print(f"Fonts used: {len(fonts)}")
        for font in sorted(fonts):
            print(f"  - {font}")
        
        # Check for missing glyphs
        for i, page in enumerate(pdf.pages):
            for char in page.chars:
                # Check for replacement characters
                if char.get('text') == '\ufffd':
                    print(f"Page {i+1}: Missing glyph for character")
                    break

check_fonts('document.pdf')
```

## Page Count Validation

```python
from pypdf import PdfReader

def verify_page_count(pdf_path, expected_pages=None):
    reader = PdfReader(pdf_path)
    actual = len(reader.pages)
    
    print(f"Actual pages: {actual}")
    if expected_pages and actual != expected_pages:
        print(f"WARNING: Expected {expected_pages} pages, got {actual}")
        return False
    return True
```

## Batch QA

### Quality Check Multiple PDFs

```python
import os
from pathlib import Path
from pypdf import PdfReader

def batch_qa(directory):
    results = []
    
    for pdf_file in Path(directory).glob('*.pdf'):
        try:
            reader = PdfReader(str(pdf_file))
            num_pages = len(reader.pages)
            
            # Extract first page text (basic check)
            first_page_text = reader.pages[0].extract_text()
            
            results.append({
                'file': pdf_file.name,
                'pages': num_pages,
                'has_text': bool(first_page_text and first_page_text.strip()),
                'size_mb': pdf_file.stat().st_size / (1024 * 1024)
            })
        except Exception as e:
            results.append({
                'file': pdf_file.name,
                'error': str(e)
            })
    
    # Report
    print(f"Total files: {len(results)}")
    print(f"Files with errors: {sum(1 for r in results if 'error' in r)}")
    print(f"Total pages: {sum(r.get('pages', 0) for r in results)}")
    print(f"Total size: {sum(r.get('size_mb', 0) for r in results):.1f} MB")
    
    for r in results:
        if 'error' in r:
            print(f"  ERROR: {r['file']}: {r['error']}")
        elif not r['has_text']:
            print(f"  WARNING: {r['file']}: No text content")

batch_qa('output/')
```

## Common Issues & Solutions

| Issue | Detection | Solution |
|-------|-----------|----------|
| Clipped text | Visual inspection | Reduce content, increase margins |
| Overlapping elements | Visual inspection | Adjust CSS/layout, add spacing |
| Missing fonts | Font check | Embed fonts, use system fonts |
| Broken tables | Visual inspection | Fix column widths, row heights |
| Page count mismatch | Page count check | Adjust content, use page-break-* |
| Image artifacts | Visual inspection | Use higher resolution images |
| Unicode issues | Encoding check | Use --pdf-engine=xelatex |
| Sensitive data | Security audit | Redact, encrypt, strip metadata |

## Best Practices

1. **Always render and inspect** before delivery
2. **Use version control** for source files (markdown/HTML)
3. **Automated QA** for batch generation
4. **Compare to previous versions** for regression testing
5. **Check accessibility** for public documents
6. **Strip metadata** for privacy when needed
7. **Use consistent file naming** with version/date
8. **Document changes** between versions
9. **Test on multiple PDF readers** (Adobe, browser, mobile)
10. **Verify file size** is reasonable for delivery method
