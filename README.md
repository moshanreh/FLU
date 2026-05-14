# FLU Zoning Extraction

This repository contains a Jupyter notebook for extracting structured zoning and development-standard data from municipal zoning code PDFs using the Gemini API batch workflow.

This work was developed as part of PSRC's FLU, or Future Land Use, work. The Puget Sound Regional Council (PSRC) provides regional data and forecasting resources used by local planners and decision-makers to understand how the region may grow and change over time. This zoning extraction workflow supports that broader land use context by helping convert municipal zoning code PDFs into structured fields that can be reviewed, cleaned, and used in planning or modeling workflows.

## Main Notebook

`Gemini_API_Batch.ipynb` runs a batch job against PDFs stored in the `Cities` folder. For each zoning PDF, it:

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

## Batch Processing

This notebook uses Gemini batch processing. Batch processing is asynchronous: instead of returning each response immediately, the code submits many PDF extraction requests together, receives a batch job ID, polls for completion, and then downloads/parses the completed responses.

The main benefit is cost. Gemini Batch API pricing is intended for high-volume, non-real-time work and provides a 50% discount compared with standard synchronous calls. The tradeoff is timing: batch jobs may take up to 24 hours to complete, although they may finish sooner.

Refer to the official Gemini Batch API and Gemini pricing documentation for current pricing details:

- [Gemini Batch API documentation](https://ai.google.dev/gemini-api/docs/batch-mode)
- [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing)

In a nutshell: batch processing is cheaper, but it is asynchronous and can take up to 24 hours.

The code can be easily reconstructed to run synchronous one-by-one calls instead. In that version, each PDF would be uploaded or attached, sent to the model with a regular `generate_content` request, parsed immediately, and appended to the output files before moving to the next PDF. That approach is simpler for debugging and gives immediate per-file responses, but it does not receive the batch-processing discount.

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

## Credit

Developed by Mohammad Mehdi Oshanreh  
Data Intern at PSRC
