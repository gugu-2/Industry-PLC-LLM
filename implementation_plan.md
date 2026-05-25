# Dataset Refinement & Bug Fix Plan

You are absolutely right—there are several critical data quality issues and bugs in the current scripts that need to be addressed before this dataset is truly "best in class" for LLM fine-tuning.

## Open Questions
> [!IMPORTANT]
> 1. **Data Rerun:** After I update these scripts, I will need to execute the entire pipeline again (downloading, scraping, merging, and cleaning). Are you okay with the pipeline running for about 5-10 minutes to rebuild the dataset from scratch with the new rules?
> 2. **HTML in Q&A:** I will strip all HTML from Stack Overflow answers. Do you want me to try and preserve code blocks (e.g., keeping formatting for `<code>` blocks) or just extract raw text for everything?

## Proposed Changes

### 1. [MODIFY] `src/stackoverflow_scraper.py`
**Bug:** The Stack Overflow API returns raw HTML strings (`<p>`, `<code>`, `<br/>`). The current script only unescapes HTML entities but leaves the raw tags, which will confuse the LLM during training.
**Fix:** Implement `BeautifulSoup` to parse the HTML and extract clean, readable text (`soup.get_text(separator="\n")`) while preserving the structural layout of the Q&A.

### 2. [MODIFY] `src/clean_dataset.py`
**Bug:** The current cleaning heuristic is far too weak. It relies on a few negative keywords (like `bazel`) but will let a lot of junk through. 
**Fix:** 
- Implement advanced Regular Expressions (Regex) to explicitly look for valid PLC structures: `(PROGRAM|FUNCTION|FUNCTION_BLOCK).*?(END_PROGRAM|END_FUNCTION|END_FUNCTION_BLOCK)`.
- Ensure files have an appropriate ratio of code-to-text to filter out accidental text files.

### 3. [MODIFY] `src/github_scraper.py`
**Bug:** The prompt instructions are generic (`Complete the following PLC logic...`). This prevents the LLM from learning the difference between vendors.
**Fix:** Map the file extension to the specific vendor and architecture in the prompt. For example:
- `.scl` -> `Write Siemens Structured Control Language (SCL) for...`
- `.tc1po` -> `Write Beckhoff TwinCAT logic for...`
- `.smc2` -> `Write Omron Sysmac Studio code for...`

### 4. [MODIFY] `src/huggingface_ingest.py`
**Bug:** Blindly imports all `.st` files, which may include massive license headers or empty files.
**Fix:** Add a pre-filter to drop files with less than 50 characters of actual code, and refine the instruction prompt.

## Verification Plan
1. **Automated Verification:** I will write a small `verify_dataset.py` script that will scan the newly compiled `train_clean.jsonl` and assert that:
   - No HTML tags (`<p>`, `<br>`) exist in the text.
   - Every assistant response has a minimum character length.
   - No records contain the `_mk_pb2` string (verifying the cleaner works).
2. **Manual Review:** I will provide a new artifact showing a side-by-side comparison of the old data format vs. the new, highly-refined data format.
