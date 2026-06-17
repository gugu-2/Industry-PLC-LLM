# PLC Dataset Aggregator

A powerful, modular data ingestion pipeline designed to scrape, extract, and clean proprietary Industrial Control Systems (ICS) logic (Programmable Logic Controllers) and troubleshoot Q&A data.

## Why this exists

Industrial engineers waste countless hours debugging proprietary hardware code (Siemens, Rockwell, Beckhoff). General-purpose LLMs like ChatGPT are severely lacking in this niche because the training data is hidden behind proprietary extensions and login walls. 

This repository solves this problem by systematically hunting down, extracting, and formatting proprietary PLC data across the internet to build the ultimate fine-tuning dataset for models like Llama-3 or Gemma-2.

---
## Supported Architectures

The pipeline actively extracts and formats data for:
- **Siemens** (Structured Control Language `.scl`, Statement List `.awl`)
- **Beckhoff** (TwinCAT `.tc1po`)
- **Rockwell / Allen-Bradley** (XML Export `.L5X`)
- **Omron** (Sysmac Studio `.smc2`)
- **Schneider Electric** (Unity Pro `.xst`)
- **Mitsubishi** (GX Works `.gxw`)
- **Codesys** (Generic `.st`, `.export`)
- **CNC Systems** (`.gcode`)

## Project Architecture

- `src/huggingface_ingest.py`: Fetches academic datasets (like PLC-BEAD) and parses thousands of raw ST files.
- `src/stackoverflow_scraper.py`: Queries the StackExchange API to build a massive troubleshooting dataset.
- `src/github_scraper.py`: Hunts down proprietary raw files by extension on GitHub.
- `src/pdf_extractor.py`: Extracts proprietary error codes from 1000-page OEM manuals.
- `src/clean_dataset.py`: A highly advanced heuristic engine utilizing IEC 61131-3 regex patterns to guarantee zero "garbage" data makes it to the LLM.

## Documentation

See the following files for more details:
- [SETUP.md](SETUP.md) - How to install and run the pipeline.
- [FEATURES.md](FEATURES.md) - Features enabled by this dataset.
- [TRAINING.md](TRAINING.md) - How to fine-tune an LLM with the generated data.

### Email me on majipritam47@gmail.com For any enquiry.
