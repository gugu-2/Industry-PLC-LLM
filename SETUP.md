# Setup and Execution

Follow these steps to generate the PLC fine-tuning dataset.

## 1. Environment Setup

This project uses Python 3. It is highly recommended to use a virtual environment.

```bash
# Create a virtual environment
python -m venv venv

# Activate it (Windows)
.\venv\Scripts\activate
# Activate it (Mac/Linux)
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

## 2. API Keys and Configuration

To run the `github_scraper.py`, you **must** provide a GitHub Personal Access Token (PAT). Without this, GitHub's API rate limits will ban your IP almost immediately when searching for code.

Generate a Classic PAT in GitHub Settings (no special scopes required, just public repos).

## 3. Running the Pipeline

You can run the entire pipeline in sequence to generate the final dataset:

```bash
# 1. Download Open Source academic datasets
python src/huggingface_ingest.py

# 2. Scrape troubleshooting Q&A
python src/stackoverflow_scraper.py

# 3. Scrape proprietary code from GitHub (Replace YOUR_TOKEN_HERE)
python src/github_scraper.py YOUR_TOKEN_HERE

# 4. Extract PDF Error Codes (Ensure OEM manuals are in /manuals folder)
python src/pdf_extractor.py

# 5. Merge all sources into one JSONL
python src/merge_data.py

# 6. Run the advanced Regex Cleaner
python src/clean_dataset.py

# 7. Verify the dataset quality
python src/verify_dataset.py
```

## Output

The final, flawless dataset ready for LLM fine-tuning will be located at:
`data/train_clean.jsonl`
