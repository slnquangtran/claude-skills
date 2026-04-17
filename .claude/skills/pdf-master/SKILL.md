---
name: pdf-master
description: "PDF intelligence for editing, generation, and manipulation. Includes 15+ operations covering creation, editing, merging, splitting, conversion, form handling, security, and optimization. Actions: create, generate, edit, merge, split, convert, extract, compress, encrypt, decrypt, watermark, sign, fill, and optimize PDFs. Formats: PDF, Word, Excel, PowerPoint, images (PNG, JPG, TIFF), HTML, Markdown. Libraries: PyPDF2, pypdf, reportlab, pdf-lib, pdfkit, weasyprint, PyMuPDF, pikepdf. Tools: Python, Node.js, CLI utilities."
---

# PDF Master - Document Intelligence

Comprehensive guide for PDF editing, generation, and manipulation. Contains 15+ operations, 8 conversion formats, security features, form handling, and optimization strategies across Python, Node.js, and CLI tools.

## When to Apply

This Skill should be used when the task involves **PDF creation, editing, conversion, or manipulation**.

### Must Use

This Skill must be invoked in the following situations:

- Creating new PDF documents from scratch or templates
- Editing existing PDFs (text, images, pages, metadata)
- Merging multiple PDFs into one document
- Splitting PDFs into separate files
- Converting PDFs to/from other formats (Word, images, HTML)
- Extracting text, images, or data from PDFs
- Adding watermarks, headers, footers, or page numbers
- Handling PDF forms (filling, extracting, creating)
- Encrypting, decrypting, or password-protecting PDFs
- Compressing or optimizing PDF file size

### Recommended

This Skill is recommended in the following situations:

- Automating document workflows
- Generating reports or invoices programmatically
- Processing bulk PDF operations
- Building document management systems
- Creating PDF-based export features

### Skip

This Skill is not needed in the following situations:

- Working with other document formats only (Word, Excel)
- Image processing without PDF involvement
- Web scraping without PDF output
- Database operations unrelated to documents

**Decision criteria**: If the task involves creating, reading, modifying, or converting PDF files, this Skill should be used.

## Library Selection Guide

| Task | Python | Node.js | CLI |
|------|--------|---------|-----|
| **Read/Extract** | pypdf, PyMuPDF | pdf-parse, pdf-lib | pdftotext, pdfimages |
| **Create from scratch** | reportlab, fpdf2 | pdfkit, pdf-lib | wkhtmltopdf |
| **Edit existing** | pypdf, pikepdf | pdf-lib | qpdf, pdftk |
| **Merge/Split** | pypdf, PyMuPDF | pdf-lib | pdftk, qpdf |
| **Convert to images** | pdf2image, PyMuPDF | pdf-poppler | pdftoppm, convert |
| **HTML to PDF** | weasyprint, pdfkit | puppeteer, playwright | wkhtmltopdf |
| **Forms** | pypdf, pdfrw | pdf-lib | pdftk |
| **OCR** | pytesseract + pdf2image | tesseract.js | tesseract |
| **Compress** | pikepdf, PyMuPDF | - | ghostscript, qpdf |
| **Security** | pikepdf, pypdf | pdf-lib | qpdf |

## Rule Categories by Priority

| Priority | Category | Impact | Key Checks | Anti-Patterns |
|----------|----------|--------|------------|---------------|
| 1 | Library Choice | CRITICAL | Match library to task, check dependencies | Using wrong library for task, mixing incompatible libs |
| 2 | Memory Management | CRITICAL | Stream large files, close file handles | Loading entire PDF into memory, memory leaks |
| 3 | Error Handling | HIGH | Validate PDF structure, handle corrupted files | Silent failures, no validation |
| 4 | Security | HIGH | Sanitize inputs, handle encrypted PDFs | Exposing sensitive data, weak encryption |
| 5 | Performance | HIGH | Batch operations, use appropriate compression | Processing page-by-page unnecessarily |
| 6 | Font Handling | MEDIUM | Embed fonts, use standard fonts, handle unicode | Missing fonts, encoding issues |
| 7 | Image Quality | MEDIUM | Appropriate DPI, compression settings | Over-compression, wrong color space |
| 8 | Metadata | LOW | Preserve or update metadata appropriately | Leaking sensitive metadata |

## Quick Reference

### 1. Library Choice (CRITICAL)

```python
# PyPDF2/pypdf - Reading, merging, splitting, basic editing
from pypdf import PdfReader, PdfWriter

# reportlab - Creating PDFs from scratch with full control
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

# PyMuPDF (fitz) - Fast reading, rendering, text extraction
import fitz

# pikepdf - Low-level editing, encryption, optimization
import pikepdf

# weasyprint - HTML/CSS to PDF (best for styled documents)
from weasyprint import HTML

# pdfkit - wkhtmltopdf wrapper (simpler HTML to PDF)
import pdfkit
```

```javascript
// pdf-lib - Create, modify, fill forms (pure JS, no native deps)
import { PDFDocument } from 'pdf-lib';

// pdfkit - Create PDFs from scratch (Node.js)
import PDFDocument from 'pdfkit';

// puppeteer/playwright - HTML to PDF via browser
import puppeteer from 'puppeteer';
```

### 2. Memory Management (CRITICAL)

```python
# GOOD: Stream processing for large files
from pypdf import PdfReader, PdfWriter

def merge_large_pdfs(pdf_paths, output_path):
    writer = PdfWriter()
    for path in pdf_paths:
        reader = PdfReader(path)
        for page in reader.pages:
            writer.add_page(page)
    with open(output_path, 'wb') as f:
        writer.write(f)

# GOOD: Context manager for file handling
with pikepdf.open('input.pdf') as pdf:
    # Process PDF
    pdf.save('output.pdf')

# BAD: Loading everything into memory
# pdf_bytes = open('huge.pdf', 'rb').read()  # Memory explosion
```

```javascript
// GOOD: Incremental saving in pdf-lib
const pdfDoc = await PDFDocument.load(existingPdfBytes);
const pdfBytes = await pdfDoc.save();

// GOOD: Streaming with pdfkit
const doc = new PDFDocument();
doc.pipe(fs.createWriteStream('output.pdf'));
doc.text('Hello');
doc.end();
```

### 3. Error Handling (HIGH)

```python
from pypdf import PdfReader
from pypdf.errors import PdfReadError

def safe_read_pdf(path):
    try:
        reader = PdfReader(path)
        if reader.is_encrypted:
            raise ValueError("PDF is encrypted")
        return reader
    except PdfReadError as e:
        raise ValueError(f"Invalid or corrupted PDF: {e}")
    except FileNotFoundError:
        raise ValueError(f"PDF file not found: {path}")

# Validate PDF structure before processing
def validate_pdf(path):
    try:
        with open(path, 'rb') as f:
            header = f.read(5)
            if header != b'%PDF-':
                return False
        reader = PdfReader(path)
        _ = len(reader.pages)  # Force page count
        return True
    except Exception:
        return False
```

```javascript
// Validate PDF before processing
async function loadPdfSafely(bytes) {
  try {
    const pdfDoc = await PDFDocument.load(bytes, {
      ignoreEncryption: false,
      updateMetadata: false,
    });
    return pdfDoc;
  } catch (error) {
    if (error.message.includes('encrypted')) {
      throw new Error('PDF is password protected');
    }
    throw new Error(`Invalid PDF: ${error.message}`);
  }
}
```

### 4. Security (HIGH)

```python
# Encrypt PDF with password
import pikepdf

with pikepdf.open('input.pdf') as pdf:
    pdf.save('encrypted.pdf',
             encryption=pikepdf.Encryption(
                 owner='owner_password',
                 user='user_password',
                 R=6  # AES-256
             ))

# Decrypt PDF
with pikepdf.open('encrypted.pdf', password='user_password') as pdf:
    pdf.save('decrypted.pdf')

# Remove sensitive metadata
with pikepdf.open('input.pdf') as pdf:
    with pdf.open_metadata() as meta:
        meta.clear()
    pdf.save('clean.pdf')
```

```javascript
// Encrypt with pdf-lib
import { PDFDocument } from 'pdf-lib';

const pdfDoc = await PDFDocument.load(existingBytes);
const encryptedBytes = await pdfDoc.save({
  userPassword: 'user123',
  ownerPassword: 'owner456',
  permissions: {
    printing: 'lowResolution',
    modifying: false,
    copying: false,
  },
});
```

### 5. Performance (HIGH)

```python
# Batch page operations
from pypdf import PdfReader, PdfWriter

def extract_pages(input_path, page_ranges, output_path):
    reader = PdfReader(input_path)
    writer = PdfWriter()
    
    for start, end in page_ranges:
        for i in range(start, min(end + 1, len(reader.pages))):
            writer.add_page(reader.pages[i])
    
    with open(output_path, 'wb') as f:
        writer.write(f)

# Use PyMuPDF for fast text extraction
import fitz

def fast_extract_text(pdf_path):
    doc = fitz.open(pdf_path)
    text = ""
    for page in doc:
        text += page.get_text()
    doc.close()
    return text

# Compress PDF
import pikepdf

with pikepdf.open('large.pdf') as pdf:
    pdf.save('compressed.pdf',
             compress_streams=True,
             object_stream_mode=pikepdf.ObjectStreamMode.generate)
```

### 6. Font Handling (MEDIUM)

```python
# reportlab - Embed custom fonts
from reportlab.pdfgen import canvas
from reportlab.pdfbase import pdfmetrics
from reportlab.pdfbase.ttfonts import TTFont

pdfmetrics.registerFont(TTFont('CustomFont', 'path/to/font.ttf'))
c = canvas.Canvas('output.pdf')
c.setFont('CustomFont', 12)
c.drawString(100, 750, "Custom font text")
c.save()

# Standard fonts (always available)
STANDARD_FONTS = [
    'Helvetica', 'Helvetica-Bold', 'Helvetica-Oblique',
    'Times-Roman', 'Times-Bold', 'Times-Italic',
    'Courier', 'Courier-Bold', 'Courier-Oblique',
]
```

```javascript
// pdf-lib - Embed fonts
import { PDFDocument, StandardFonts } from 'pdf-lib';
import fontkit from '@pdf-lib/fontkit';

const pdfDoc = await PDFDocument.create();
pdfDoc.registerFontkit(fontkit);

// Standard font
const helvetica = await pdfDoc.embedFont(StandardFonts.Helvetica);

// Custom font
const fontBytes = fs.readFileSync('custom-font.ttf');
const customFont = await pdfDoc.embedFont(fontBytes);
```

### 7. Image Quality (MEDIUM)

```python
# pdf2image - Convert PDF to images
from pdf2image import convert_from_path

# High quality for print
images = convert_from_path('document.pdf', dpi=300)

# Web quality (smaller files)
images = convert_from_path('document.pdf', dpi=150, fmt='jpeg', jpegopt={'quality': 85})

# Insert image into PDF
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib.units import inch

c = canvas.Canvas('with_image.pdf', pagesize=letter)
c.drawImage('photo.jpg', 1*inch, 7*inch, width=4*inch, height=3*inch)
c.save()
```

```javascript
// pdf-lib - Embed images
import { PDFDocument } from 'pdf-lib';

const pdfDoc = await PDFDocument.create();
const page = pdfDoc.addPage();

// Embed JPG or PNG
const imageBytes = fs.readFileSync('image.png');
const image = await pdfDoc.embedPng(imageBytes);

const { width, height } = image.scale(0.5);
page.drawImage(image, {
  x: 50,
  y: page.getHeight() - height - 50,
  width,
  height,
});
```

### 8. Metadata (LOW)

```python
# Read metadata
from pypdf import PdfReader

reader = PdfReader('document.pdf')
meta = reader.metadata
print(f"Title: {meta.title}")
print(f"Author: {meta.author}")
print(f"Creator: {meta.creator}")

# Write metadata
from pypdf import PdfWriter

writer = PdfWriter()
writer.append('input.pdf')
writer.add_metadata({
    '/Title': 'Document Title',
    '/Author': 'Author Name',
    '/Subject': 'Subject',
    '/Keywords': 'pdf, document',
    '/Creator': 'PDF Master',
})
with open('output.pdf', 'wb') as f:
    writer.write(f)
```

## Common Operations

### Create PDF from HTML

```python
# weasyprint (best CSS support)
from weasyprint import HTML, CSS

html_content = """
<html>
<body>
    <h1>Report Title</h1>
    <p>Content here...</p>
</body>
</html>
"""

HTML(string=html_content).write_pdf(
    'report.pdf',
    stylesheets=[CSS(string='body { font-family: Arial; }')]
)

# pdfkit (uses wkhtmltopdf)
import pdfkit
pdfkit.from_string(html_content, 'report.pdf')
pdfkit.from_url('https://example.com', 'webpage.pdf')
pdfkit.from_file('template.html', 'report.pdf')
```

```javascript
// puppeteer (Chrome rendering)
import puppeteer from 'puppeteer';

const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.setContent(htmlContent);
await page.pdf({
  path: 'report.pdf',
  format: 'A4',
  margin: { top: '1cm', right: '1cm', bottom: '1cm', left: '1cm' },
  printBackground: true,
});
await browser.close();
```

### Merge PDFs

```python
from pypdf import PdfWriter

writer = PdfWriter()
for pdf_path in ['doc1.pdf', 'doc2.pdf', 'doc3.pdf']:
    writer.append(pdf_path)

with open('merged.pdf', 'wb') as f:
    writer.write(f)
```

```javascript
import { PDFDocument } from 'pdf-lib';

async function mergePdfs(pdfPaths) {
  const mergedPdf = await PDFDocument.create();
  
  for (const path of pdfPaths) {
    const pdfBytes = fs.readFileSync(path);
    const pdf = await PDFDocument.load(pdfBytes);
    const copiedPages = await mergedPdf.copyPages(pdf, pdf.getPageIndices());
    copiedPages.forEach(page => mergedPdf.addPage(page));
  }
  
  return await mergedPdf.save();
}
```

### Split PDF

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader('document.pdf')

# Split into individual pages
for i, page in enumerate(reader.pages):
    writer = PdfWriter()
    writer.add_page(page)
    with open(f'page_{i+1}.pdf', 'wb') as f:
        writer.write(f)

# Split by page ranges
def split_pdf(input_path, ranges):
    reader = PdfReader(input_path)
    for idx, (start, end) in enumerate(ranges):
        writer = PdfWriter()
        for i in range(start-1, end):
            writer.add_page(reader.pages[i])
        with open(f'split_{idx+1}.pdf', 'wb') as f:
            writer.write(f)
```

### Extract Text

```python
# pypdf
from pypdf import PdfReader

reader = PdfReader('document.pdf')
text = ""
for page in reader.pages:
    text += page.extract_text()

# PyMuPDF (faster, better formatting)
import fitz

doc = fitz.open('document.pdf')
for page in doc:
    text = page.get_text("text")      # Plain text
    blocks = page.get_text("blocks")  # Text blocks with position
    html = page.get_text("html")      # HTML format
```

### Add Watermark

```python
from pypdf import PdfReader, PdfWriter
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import letter
from io import BytesIO

# Create watermark
def create_watermark(text):
    buffer = BytesIO()
    c = canvas.Canvas(buffer, pagesize=letter)
    c.setFont("Helvetica", 50)
    c.setFillColorRGB(0.5, 0.5, 0.5, 0.3)
    c.saveState()
    c.translate(300, 400)
    c.rotate(45)
    c.drawCentredString(0, 0, text)
    c.restoreState()
    c.save()
    buffer.seek(0)
    return PdfReader(buffer).pages[0]

# Apply watermark
reader = PdfReader('document.pdf')
writer = PdfWriter()
watermark = create_watermark("CONFIDENTIAL")

for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)

with open('watermarked.pdf', 'wb') as f:
    writer.write(f)
```

### Fill PDF Form

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader('form.pdf')
writer = PdfWriter()
writer.append(reader)

writer.update_page_form_field_values(
    writer.pages[0],
    {
        'name_field': 'John Doe',
        'email_field': 'john@example.com',
        'date_field': '2024-01-15',
    }
)

with open('filled_form.pdf', 'wb') as f:
    writer.write(f)
```

```javascript
import { PDFDocument } from 'pdf-lib';

const pdfBytes = fs.readFileSync('form.pdf');
const pdfDoc = await PDFDocument.load(pdfBytes);
const form = pdfDoc.getForm();

form.getTextField('name_field').setText('John Doe');
form.getTextField('email_field').setText('john@example.com');
form.getCheckBox('agree_checkbox').check();

const filledBytes = await pdfDoc.save();
```

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Loading entire PDF into memory | Memory overflow with large files | Use streaming/incremental processing |
| Ignoring encrypted PDFs | Silent failures or crashes | Check `is_encrypted` and handle properly |
| Using wrong library | Inefficient or impossible operations | Match library to task (see guide above) |
| Not closing file handles | Memory leaks, locked files | Use context managers (`with`) |
| Hardcoding page sizes | Breaks with non-standard documents | Read actual page dimensions |
| Ignoring font embedding | Missing text in output | Embed fonts or use standard fonts |
| Over-compressing images | Quality loss | Balance quality vs file size |
| Not validating input | Processing malformed PDFs | Validate PDF structure first |

## Pre-Delivery Checklist

- [ ] PDF opens correctly in multiple viewers (Adobe, Preview, Chrome)
- [ ] All text is selectable and searchable
- [ ] Fonts render correctly (no missing glyphs)
- [ ] Images have appropriate quality for use case
- [ ] File size is reasonable for content
- [ ] Metadata is appropriate (no sensitive info leaked)
- [ ] Encrypted PDFs handled or flagged
- [ ] Page numbers and orientation correct
- [ ] Bookmarks/TOC work if present
- [ ] Forms are fillable if applicable
