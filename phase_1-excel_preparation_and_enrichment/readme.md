
# **Data Processing & Enrichment Workflow for Researcher Excel Files**

This repository contains a **three-step workflow** for processing Excel files provided by researchers. The workflow:

1. **Validates** that each Excel file follows an expected structure (worksheets + columns)  
2. **Enriches** entities by fetching public metadata from **Wikidata**  
3. **Links** entities across sheets/files using **MENTIONS** and **HYPOTHESIS** relations  

The scripts are meant to be run **manually, one after the other**, so you can verify correctness at each stage.  
**Important:** the scripts **edit your original Excel files in place** (they add columns, standardize formats, and save back into the same `.xlsx` files).

---

## Part 1 — For Everyone (Researchers, Project Staff, Data Curators)

### What you need (in plain terms)

To run this workflow successfully, keep the following items **together in one folder**:

- Your research Excel files (e.g., `BA-EG.xlsx`, `BPC-AB.xlsx`, etc.)
- A structure template file called **`columns_trace.xlsx`**
- The three notebooks (scripts)

`columns_trace.xlsx` acts like a **blueprint**: it defines which worksheets and columns must exist in every research data file.

---

## Quick Start (Non-technical)

1. Put your Excel files and `columns_trace.xlsx` in the same folder as the notebooks.
2. Run the notebooks in this exact order:
   1. `validator.ipynb`
   2. `fetching_info_about_entities.ipynb`
   3. `joining_mentions_all_lower.ipynb`
3. When finished, your **original Excel files** will contain:
   - standardized sheet names (spaces replaced by underscores),
   - extra columns filled with Wikidata information,
   - new columns that show how entities mention each other and connect to hypotheses.

---

## How the workflow works (conceptually)

### Step 1 — Validate and Standardize File Structure (`validator.ipynb`)

This step checks whether each researcher-provided Excel file matches the expected structure defined in `columns_trace.xlsx`.

**What it does:**

- **Checks required worksheets**: verifies that each Excel file contains all the necessary worksheets.
- **Checks required columns**: for each worksheet, verifies that all required columns exist.
- **Reports errors**: prints a report if any worksheets or columns are missing.
- **Standardizes worksheet names**: replaces spaces with underscores  
  (example: `"VISUAL OBJECT"` → `"VISUAL_OBJECT"`)

**What you do:**

- Run the validator.
- If it reports items as missing, **fix the Excel file manually**, save it, and run the validator again.
- Only proceed when the validator reports no missing parts.

---

### Step 2 — Fetch and Add Wikidata Information (`fetching_info_about_entities.ipynb`)

Once files are structurally correct, this step enriches your data with metadata from **Wikidata**.

**What it does:**

- Scans specific worksheets (typically):
  - `PLACE`, `PERSON`, `ABSTRACT_CHARACTER`, `EVENT`, `INSTITUTION`
- Detects columns containing Wikidata links (e.g., `WIKIDATA_LINK_PERSON`)
- For each valid link, retrieves:
  - **description**
  - **alternative names** (“also known as”)
  - **VIAF code**
  - **Wikidata ID**
  - **coordinates** (for places and events)

**What you get:**

New columns will appear, for example:

- `DESCRIPTION_WIKIDATA_PERSON`
- `ALSO_KNOWN_AS_PERSON`
- `VIAF_CODE_PERSON`
- `WIKIDATA_ID_PERSON`
- `COORDINATES_PLACE`

---

### Step 3 — Link Mentions and Hypotheses (`joining_mentions_all_lower.ipynb`)

This step creates explicit relationships across the dataset based on MENTIONS and HYPOTHESIS sheets.

**What it does:**

- Reads all `MENTIONS` and `HYPOTHESIS` worksheets across **all Excel files** in the folder.
- Combines them into unified master tables.
- Cleans and standardizes identifiers.
- Adds linking columns to relevant worksheets, such as:
  - `*_MENTIONING`
  - `*_MENTIONED_BY`
  - `*_HYPOTHESIS_ID`

---

## Important note: Case preservation

While the `joining_mentions_all_lower.ipynb` script generally converts identifiers to lowercase for consistency, it makes an important exception for specific prefixes that are crucial for identifying items in the context of old Greek books.
Identifiers that start with `VO_`, `PO_` or `PAG_` will have their case preserved. These prefixes are unique identifiers, and changing them to lowercase would result in a loss of critical information.

To prevent corruption of meaningful identifiers, the script **does not lowercase** IDs beginning with:

- `VO_`
- `PO_`
- `PAG_`

Everything else may be standardized to lowercase.

**Example input:**

- `VO_123_Some_Image`
- `PO_456_Physical_Object`
- `PAG_789_A_Specific_Page`
- `some_other_id`

**After processing:**

- `VO_123_Some_Image` (preserved)
- `PO_456_Physical_Object` (preserved)
- `PAG_789_A_Specific_Page` (preserved)
- `some_other_id` (normalized)

---

# Part 2 — Technical Details (For Programmers)

## Requirements

- Python 3
- Jupyter Notebook / JupyterLab
- Libraries:
  - `pandas`
  - `openpyxl`
  - `requests`

Install dependencies:

```bash
pip install pandas openpyxl requests
```

---

## Expected Project Structure

All scripts and Excel files must be placed **in one folder**:

```
/your_project_folder/
│
├── validator.ipynb
├── fetching_info_about_entities.ipynb
├── joining_mentions_all_lower.ipynb
│
├── columns_trace.xlsx          # schema definition file (blueprint)
├── BA-EG.xlsx                  # researcher's data file 1
├── BPC-AB.xlsx                 # researcher's data file 2
└── (any other data files).xlsx
```

`columns_trace.xlsx` defines the canonical set of worksheets and required columns per sheet.

---

## Running Order (Must be followed)

1. `validator.ipynb`  
2. `fetching_info_about_entities.ipynb`  
3. `joining_mentions_all_lower.ipynb`

Each step prepares the data for the next one.

---

## Step 1 (Technical) — Validate and Standardize File Structure

**Script:** `validator.ipynb`  
**Purpose:** Ensures every researcher-provided Excel file matches the schema in `columns_trace.xlsx`.

### What it does

- Iterates through all `.xlsx` research files.
- Validates:
  - required worksheets exist,
  - required columns exist.
- Prints a report of missing worksheets/columns.
- Renames worksheets containing spaces (e.g., `"VISUAL OBJECT"` → `"VISUAL_OBJECT"`).

### How to run

1. Open `validator.ipynb` in Jupyter.
2. Run all cells.
3. Fix any reported issues manually.
4. Rerun until clean.

**Do not continue** to Step 2 until all files validate.

---

## Step 2 (Technical) — Fetch Information from Wikidata

**Script:** `fetching_info_about_entities.ipynb`  
**Purpose:** Enriches the dataset using Wikidata based on `WIKIDATA_LINK_*` columns.

### What it does

Targets sheets such as:

- `PLACE`, `PERSON`, `ABSTRACT_CHARACTER`, `EVENT`, `INSTITUTION`

Adds enrichment columns:

- `DESCRIPTION_WIKIDATA_*`
- `ALSO_KNOWN_AS_*`
- `VIAF_CODE_*`
- `WIKIDATA_ID_*`
- `COORDINATES_*` (place/event only)

### How to run

1. Ensure Step 1 completed successfully.
2. Close Excel files to avoid file locking.
3. Run all cells in `fetching_info_about_entities.ipynb`.
4. Look for success messages like:

```
--- Successfully saved the updated data to 'BA-EG.xlsx' ---
```

---

## Step 3 (Technical) — Join Mentions to Link Entities

**Script:** `joining_mentions_all_lower.ipynb`  
**Purpose:** Builds cross-entity links using MENTIONS and HYPOTHESIS.

### What it does

- Reads all MENTIONS and HYPOTHESIS sheets from all Excel files.
- Merges them into consolidated tables.
- Normalizes identifiers (lowercase except special prefixes).
- Writes relationship columns such as:

```
{SHEET}_MENTIONING
{SHEET}_MENTIONED_BY
{SHEET}_HYPOTHESIS_ID
```

Some sheets have special handling (ex: `MANIFESTATION` uses `MANIFESTATION_VOLUME_ID`).

### Case preservation rule

Preserves case for IDs starting with:

- `VO_`
- `PO_`
- `PAG_`

### How to run

1. Ensure Step 2 completed successfully.
2. Close Excel files.
3. Run all cells in `joining_mentions_all_lower.ipynb`.
4. You should see messages such as:

```
Successfully saved updates to BA-EG.xlsx
```

---

# Final Outcome

After completing all three steps, your original Excel files will contain:

1. **Validated, standardized structure**  
2. **Wikidata enrichment columns** (descriptions, aliases, VIAF, coordinates…)  
3. **Cross-entity linking columns**, showing:
   - what each entity mentions,
   - what mentions it,
   - which hypotheses relate to it.

