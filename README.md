# invoice-automator

Creating a Python program like `invoice-automator` requires several steps, such as performing Optical Character Recognition (OCR) to extract data from invoices, validating this extracted data, and handling potential errors. A common choice for OCR in Python is the Tesseract OCR engine, which can be accessed via the `pytesseract` library. For invoice data validation, several techniques can be applied depending on the specific business rules and criteria.

Here's a simplified version of such a program. This example demonstrates how to integrate OCR with basic data validation and error handling. We will assume invoices are in image format and use regular expressions for basic data validation.

```python
import pytesseract
from pytesseract import Output
from PIL import Image
import re
import os

# Path to the Tesseract OCR executable
pytesseract.pytesseract.tesseract_cmd = r'/usr/bin/tesseract'  # Update this path with the correct path to your tesseract installation

def extract_text_from_image(image_path):
    """Extract text from an image using OCR."""
    try:
        with Image.open(image_path) as img:
            text = pytesseract.image_to_string(img, output_type=Output.STRING)
            return text
    except Exception as e:
        print(f"Error processing image {image_path}: {e}")
        return ""

def validate_invoice_data(extracted_text):
    """Validate extracted text to ensure that it meets expected format."""
    # Example validations (Assume invoices contain invoice number and date)
    invoice_number_pattern = r'\bInvoice Number:\s?(\d+)\b'
    date_pattern = r'\bDate:\s?(\d{2}/\d{2}/\d{4})\b'

    invoice_number_match = re.search(invoice_number_pattern, extracted_text)
    date_match = re.search(date_pattern, extracted_text)

    is_valid = True
    if not invoice_number_match:
        print("No valid invoice number found.")
        is_valid = False
    if not date_match:
        print("No valid date found.")
        is_valid = False
    
    if is_valid:
        invoice_number = invoice_number_match.group(1)
        invoice_date = date_match.group(1)
        return {
            "invoice_number": invoice_number,
            "invoice_date": invoice_date,
            "status": "Valid"
        }
    else:
        return {
            "status": "Invalid data"
        }

def process_invoice(image_path):
    """Process a single invoice image, extracting and validating its data"""
    extracted_text = extract_text_from_image(image_path)
    return validate_invoice_data(extracted_text)

def main():
    """Main function to process all images in the specified directory."""
    directory = './invoices'  # Directory containing invoice images
    if not os.path.isdir(directory):
        print("Directory not found!")
        return

    for filename in os.listdir(directory):
        if filename.endswith(('.png', '.jpg', '.jpeg', '.tiff', '.bmp', '.gif')):
            image_path = os.path.join(directory, filename)
            print(f"Processing {filename}...")
            result = process_invoice(image_path)
            print(f"Result for {filename}: {result}")

if __name__ == "__main__":
    main()
```

### Key Points and Considerations:

1. **Installation**:
   - Make sure you have Tesseract OCR installed on your system. You can download it from [Tesseract OCR GitHub](https://github.com/tesseract-ocr/tesseract) and update the `pytesseract.pytesseract.tesseract_cmd` with your Tesseract executable path.
   - Install Python libraries: `pytesseract`, `PILLOW` using `pip install pytesseract pillow`.

2. **Image Handling**:
   - Images should be placed in the folder specified by the `directory` variable. This example assumes images are in `.png`, `.jpg`, `jpeg`, or other common image formats.

3. **Data Extraction**:
   - The OCR process (`pytesseract.image_to_string`) reads the image and extracts textual content. This implementation assumes invoices contain data like "Invoice Number" and "Date" in a predictable format.

4. **Validation**:
   - Regular expressions are used for basic validation of invoice numbers and dates in the extracted text. Customize these patterns based on your invoice formats and validation requirements.

5. **Error Handling**:
   - Basic error handling is done when loading images and processing text. This can be expanded based on specific failure points observed in practice (e.g., file not found, OCR processing errors).

This program is a foundation upon which more sophisticated features (e.g., database integration, comprehensive error logs, advanced validation checks) can be added based on specific business needs.
