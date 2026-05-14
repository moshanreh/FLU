# FLU Zoning Extraction

This repository contains a Jupyter notebook for extracting structured zoning and development-standard data from municipal zoning code PDFs using the Gemini API batch workflow.

## Main Notebook

`Gemini_API_Batch-Copy1.ipynb` runs a batch job against PDFs stored in the `Cities` folder. For each zoning PDF, it:

1. Reads the Gemini API key from a local `key.txt` file.
2. Uploads each PDF in `Cities/*.pdf` to Gemini.
3. Sends a zoning extraction prompt to `models/gemini-2.5-pro`.
4. Polls the Gemini batch job until it finishes.
5. Parses JSON responses into a dataframe.
6. Saves the results as:
   - `all_zoning_data_batch.csv`
   - `all_zoning_data_batch.json`
   - `all_zoning_batch_raw.jsonl`

The extracted fields include jurisdiction, zone name, density, FAR, height, lot coverage, bonus availability, use flags, rural flags, and ADU notes.

## What You Need

- A Gemini API key.
- A local `key.txt` file containing only the Gemini API key.
- Zoning PDFs placed in the `Cities` folder.
- Python packages:
  - `google-genai`
  - `pandas`
  - `ipython`

Example local setup:

```powershell
pip install google-genai pandas ipython
```

Then create `key.txt` locally:

```text
YOUR_GEMINI_API_KEY
```

Do not commit `key.txt` to GitHub.

## Important Parsing Note

Full city codes can be very large and may not be parsed reliably by the API in one request. When possible, use only the zoning section or other relevant sections of the municipal code instead of uploading the entire city code. If the code is too large, truncate the source material to the zoning, land use, density, FAR, height, lot coverage, use table, overlay, bonus, and ADU sections needed for the extraction.

The notebook expects those relevant zoning PDFs to be available in the local `Cities` folder.
