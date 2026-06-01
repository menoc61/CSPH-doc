---
name: pdf-form-filler
description: Fill PDF forms programmatically with data from JSON, CSV, or databases. Use when automating form completion, batch-filling invoices, contracts, or applications. Triggers: fill PDF form, PDF form automation, form data injection, AcroForm, batch fill forms.
---

# PDF Form Filler

Fill PDF AcroForms programmatically with data from various sources.

## When to Use

- Automate PDF form completion
- Batch fill invoices, contracts, applications
- Inject data from JSON, CSV, or database
- Generate personalized PDFs at scale
- Fill government/tax forms
- Process onboarding documents

## Quick Reference

| Tool | Best For | Language |
|------|----------|----------|
| pdf-lib | JavaScript, form modification | Node.js |
| pypdf | Python, basic form filling | Python |
| pdfrw | Python, read/write forms | Python |
| PyMuPDF | Python, advanced features | Python |

## Python with pypdf

### Read Form Fields

```python
from pypdf import PdfReader

reader = PdfReader("form.pdf")

# Get all form fields
fields = reader.get_fields()
for field_name, field_data in fields.items():
    print(f"{field_name}: {field_data.get('/V', 'empty')}")
    print(f"  Type: {field_data.get('/FT', 'unknown')}")
```

### Fill Form

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("form.pdf")
writer = PdfWriter()

# Add all pages
for page in reader.pages:
    writer.add_page(page)

# Fill form fields
writer.update_form_field_values({
    "name": "John Doe",
    "email": "john@example.com",
    "date": "2026-01-15",
    "amount": "1000.00",
    "checkbox_1": "/Yes",  # Check checkbox
    "dropdown_1": "Option A"
})

# Save filled PDF
with open("filled.pdf", "wb") as output:
    writer.write(output)
```

### Read and Fill Specific Field

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("form.pdf")
fields = reader.get_fields()

# Get current value
current_name = fields.get("name", {}).get("/V")
print(f"Current name: {current_name}")

# Fill and save
writer = PdfWriter()
for page in reader.pages:
    writer.add_page(page)

writer.update_form_field_values({
    "name": "Jane Smith"
})

with open("filled.pdf", "wb") as output:
    writer.write(output)
```

## JavaScript with pdf-lib

### Read Form Fields

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function readForm() {
  const pdfBytes = fs.readFileSync('form.pdf');
  const pdfDoc = await PDFDocument.load(pdfBytes);
  const form = pdfDoc.getForm();
  
  const fields = form.getFields();
  fields.forEach(field => {
    const name = field.getName();
    const type = field.constructor.name;
    console.log(`${name}: ${type}`);
    
    if (field.getText) {
      console.log(`  Value: ${field.getText()}`);
    }
  });
}

readForm();
```

### Fill Form

```javascript
const { PDFDocument } = require('pdf-lib');
const fs = require('fs');

async function fillForm() {
  const pdfBytes = fs.readFileSync('form.pdf');
  const pdfDoc = await PDFDocument.load(pdfBytes);
  const form = pdfDoc.getForm();
  
  // Text fields
  form.getTextField('name').setText('John Doe');
  form.getTextField('email').setText('john@example.com');
  form.getTextField('address').setText('123 Main St');
  
  // Checkboxes
  form.getCheckBox('agree_terms').check();
  form.getCheckBox('subscribe').uncheck();
  
  // Radio buttons
  form.getRadioGroup('payment_method').select('credit_card');
  
  // Dropdowns
  form.getDropdown('country').select('USA');
  
  // Multi-select
  form.getOptionList('interests').select(['tech', 'sports']);
  
  // Save
  const filledPdfBytes = await pdfDoc.save();
  fs.writeFileSync('filled.pdf', filledPdfBytes);
}

fillForm();
```

## Batch Processing from JSON

### Read Multiple Records

```python
import json
from pypdf import PdfReader, PdfWriter
from pathlib import Path

# Load data
with open('records.json') as f:
    records = json.load(f)

# Process each record
for i, record in enumerate(records):
    reader = PdfReader("template.pdf")
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    # Fill fields from record
    writer.update_form_field_values(record)
    
    # Save with unique name
    output_path = f"output/record_{i+1:04d}.pdf"
    with open(output_path, "wb") as output:
        writer.write(output)
    
    print(f"Generated: {output_path}")
```

### CSV to PDFs

```python
import csv
from pypdf import PdfReader, PdfWriter

with open('data.csv', newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    for i, row in enumerate(reader):
        pdf_reader = PdfReader("template.pdf")
        writer = PdfWriter()
        
        for page in pdf_reader.pages:
            writer.add_page(page)
        
        # Clean data (remove extra whitespace)
        cleaned = {k: (v or '').strip() for k, v in row.items()}
        
        writer.update_form_field_values(cleaned)
        
        output_name = f"output/{row.get('id', f'record_{i}')}.pdf"
        with open(output_name, "wb") as output:
            writer.write(output)
```

## Advanced Features

### Conditional Fields

```python
def fill_conditional_form(data):
    reader = PdfReader("template.pdf")
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    # Always fill
    fields = {
        "name": data["name"],
        "date": data["date"]
    }
    
    # Conditional based on data
    if data.get("type") == "individual":
        fields["tax_id"] = data["ssn"]
    elif data.get("type") == "business":
        fields["tax_id"] = data["ein"]
        fields["business_name"] = data["business_name"]
    
    writer.update_form_field_values(fields)
    
    return writer
```

### Multi-Page Forms

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("multi_page_form.pdf")
writer = PdfWriter()

# Add all pages
for page in reader.pages:
    writer.add_page(page)

# Fields can be on any page
writer.update_form_field_values({
    "page1_name": "John Doe",
    "page2_address": "123 Main St",
    "page3_signature": "J. Doe",
    "page4_date": "2026-01-15"
})

with open("filled.pdf", "wb") as output:
    writer.write(output)
```

### Flatten Form (Make Non-Editable)

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("filled_form.pdf")
writer = PdfWriter()

for page in reader.pages:
    writer.add_page(page)

# Fill values
writer.update_form_field_values({...})

# Flatten - converts form fields to static text
# (Cannot be done with pypdf directly, use other tools)

# Save
with open("flattened.pdf", "wb") as output:
    writer.write(output)
```

### Flatten with pdf-lib

```javascript
const { PDFDocument } = require('pdf-lib');

async function flatten() {
  const pdfDoc = await PDFDocument.load(filledPdfBytes);
  const form = pdfDoc.getForm();
  
  // Flatten all fields
  form.flatten();
  
  const flatBytes = await pdfDoc.save();
  fs.writeFileSync('flattened.pdf', flatBytes);
}
```

## Common Field Types

| Type | pypdf | pdf-lib |
|------|-------|---------|
| Text | `update_form_field_values({name: value})` | `form.getTextField(name).setText(value)` |
| Checkbox | `"/Yes"` or `"/On"` to check | `form.getCheckBox(name).check()` |
| Radio | Selected option value | `form.getRadioGroup(name).select(option)` |
| Dropdown | Option value | `form.getDropdown(name).select(option)` |
| Multiselect | Array of values | `form.getOptionList(name).select(array)` |

## Error Handling

```python
from pypdf import PdfReader, PdfWriter

def safe_fill_form(template_path, data, output_path):
    try:
        reader = PdfReader(template_path)
        fields = reader.get_fields()
        
        # Validate all required fields exist
        required = ["name", "date", "amount"]
        missing = [f for f in required if f not in fields]
        
        if missing:
            raise ValueError(f"Missing fields in template: {missing}")
        
        # Filter data to only known fields
        valid_fields = {k: v for k, v in data.items() if k in fields}
        
        writer = PdfWriter()
        for page in reader.pages:
            writer.add_page(page)
        
        writer.update_form_field_values(valid_fields)
        
        with open(output_path, "wb") as output:
            writer.write(output)
        
        return True
    except Exception as e:
        print(f"Error: {e}")
        return False
```

## Database Integration

### PostgreSQL to PDFs

```python
import psycopg2
from pypdf import PdfReader, PdfWriter

conn = psycopg2.connect("dbname=forms user=admin")
cur = conn.cursor()

cur.execute("SELECT id, name, email, amount FROM invoices")
for row in cur.fetchall():
    record_id, name, email, amount = row
    
    reader = PdfReader("invoice_template.pdf")
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    writer.update_form_field_values({
        "invoice_number": str(record_id),
        "name": name,
        "email": email,
        "amount": f"${amount:.2f}"
    })
    
    with open(f"output/invoice_{record_id}.pdf", "wb") as output:
        writer.write(output)
```

## Templates for CSPH Project

### Delivery Receipt Form

```python
def generate_delivery_receipt(delivery_data):
    """Generate a delivery receipt PDF for a 50kg bottle delivery"""
    reader = PdfReader("templates/delivery_receipt.pdf")
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    writer.update_form_field_values({
        "delivery_id": delivery_data["id"],
        "date": delivery_data["date"],
        "truck_id": delivery_data["truck_id"],
        "driver_name": delivery_data["driver"],
        "client_name": delivery_data["client"],
        "client_address": delivery_data["address"],
        "bottles_count": str(delivery_data["count"]),
        "bottle_serial_1": delivery_data["bottles"][0],
        "bottle_serial_2": delivery_data["bottles"][1] if len(delivery_data["bottles"]) > 1 else "",
        "bottle_serial_3": delivery_data["bottles"][2] if len(delivery_data["bottles"]) > 2 else "",
        "total_volume_kg": str(delivery_data["count"] * 50),
        "client_signature": "",
        "driver_signature": delivery_data["driver"]
    })
    
    return writer
```

### Truck Inspection Form

```python
def generate_inspection_form(truck_id, inspection_data):
    """Generate truck inspection form PDF"""
    reader = PdfReader("templates/inspection.pdf")
    writer = PdfWriter()
    
    for page in reader.pages:
        writer.add_page(page)
    
    # Checkboxes for each inspection item
    fields = {
        "truck_id": truck_id,
        "inspection_date": inspection_data["date"],
        "inspector_name": inspection_data["inspector"],
        "odometer_reading": str(inspection_data["odometer"]),
    }
    
    # Add pass/fail for each check item
    for item, status in inspection_data["checks"].items():
        if status == "pass":
            fields[f"{item}_pass"] = "/Yes"
        else:
            fields[f"{item}_fail"] = "/Yes"
            fields[f"{item}_notes"] = inspection_data.get("notes", {}).get(item, "")
    
    writer.update_form_field_values(fields)
    return writer
```

## Best Practices

1. **Validate template fields** before batch processing
2. **Handle missing fields** gracefully
3. **Flatten forms** when final delivery needed (prevents editing)
4. **Use unique field names** across pages
5. **Test with sample data** before production
6. **Log errors** for debugging
7. **Backup originals** before flattening
8. **Use version control** for templates

## Common Issues

| Issue | Solution |
|-------|----------|
| Field not found | Check exact field name (case-sensitive) |
| Checkbox not working | Use `/Yes` (pypdf) or `.check()` (pdf-lib) |
| Special characters | Encode properly (UTF-8) |
| Long values overflow | Truncate or auto-fit text |
| Form not flattened | Use pdf-lib's `form.flatten()` |
| Date format wrong | Convert to expected format before filling |
