---
layout: post
title: Extracting Text and Table Data from PDF Documents Using OCR and AI Models
tags:
- tech
- tools
- ai
- document analysis
- ocr
category: tech
date: 2024-12-03 12:30:00 +0530
---

In a recent project, I faced the challenge of extracting valuable information from a PDF document that contained both normal text and scanned text, along with several tables. While Optical Character Recognition (OCR) can handle text extraction, tables require a more advanced approach.  

After exploring various options, I found two libraries that stood out: **[img2table](https://github.com/xavctn/img2table){:target="_blank"}** and **[MinerU](https://github.com/opendatalab/MinerU){:target="_blank"}**. Here's a breakdown of my experience with both tools and how they performed for this use case.  

---

## img2table  

**img2table** is a lightweight library designed to extract tables from PDFs only using OCR. It focuses solely on tables and provides the output in various formats, including HTML and dataframes. I tried the tesseract library for OCR capability.

### Sample Code:  
```python  
from img2table.document import PDF, Image  
from img2table.ocr import TesseractOCR  

ocr = TesseractOCR(n_threads=1, lang="eng")  
doc = PDF("sample.pdf", detect_rotation=False, pdf_text_extraction=True)  
extracted_tables = doc.extract_tables(ocr=ocr,  
                                      implicit_rows=False,  
                                      implicit_columns=False,  
                                      borderless_tables=False,  
                                      min_confidence=50)  

for key, tables in extracted_tables.items():  
    for table in tables:  
        print(table.html)  
```  

You can also convert the tables into a dataframe using `table.df`, making it easier to analyze and manipulate the data.  

---

## MinerU  

**MinerU** leverages AI models to extract data from PDFs and convert it into markdown format. While it’s more computationally expensive compared to img2table, it provides richer insights, especially for complex documents. MinerU performs well on GPUs but also delivers decent results on CPUs.  

### Installation & Usage:  
Since MinerU requires specific versions of Python and PyTorch, I used **Devbox** to isolate the environment. Once installed, the `magic-pdf` command-line utility simplifies the extraction process:  

```bash
magic-pdf -p sample.pdf -o sample -m auto -l en  
```  

This command generates a folder (specified by the `-o` option) containing:  
- An **images** folder with extracted images.  
- Multiple **.json** and **.pdf** files analyzing the extracted content.  
- A **.md** file containing the document content in markdown format, which I found most useful.  

### Performance:  
On my mid-2015 MacBook Pro (2.5 GHz Quad-Core Intel Core i7, 16 GB RAM), it took approximately 3-5 minutes to process a PDF file. The accuracy of MinerU was impressive and well-suited for my needs.  

---

## Conclusion  
img2table is a great choice for lightweight table extraction, while MinerU shines when dealing with complex documents and comprehensive data extraction.  

If you're looking for reliable tools to extract text and tables from PDFs, these two libraries are worth exploring.  

*Attached Files:*  
- **[sample.pdf](https://priyankt.com/assets/sample.pdf)** (Input file)  
- **[sample_img2table.html](https://priyankt.com/assets/sample_img2table.html)** (Output from img2table)  
- **[sample.zip](https://priyankt.com/assets/sample.zip)** (Output folder from MinerU)  
