# Environment-Data
# Context-Aware PII Anonymization Tool for Financial Prospectuses

## 1. Project Overview
This project provides an automated, enterprise-grade Data Engineering pipeline designed to scan financial disclosures (specifically **Red Herring Prospectuses**) and redact Personally Identifiable Information (PII). 

Rather than relying on static regular expressions (which frequently fail or over-redact in complex financial contexts), this tool utilizes **Microsoft Presidio** backed by **spaCy (`en_core_web_lg`)** for context-aware Named Entity Recognition (NER). All identified PII entities are dynamically replaced with contextually realistic synthetic data via the **Faker** library.

---

## 2. Technical Stack & Architecture
* **NLP & NER Pipeline:** `Microsoft Presidio Analyzer` powered by spaCy's large language model (`en_core_web_lg`).
* **Anonymization Engine:** `Microsoft Presidio Anonymizer`.
* **Synthetic Data Generation:** `Faker`.
* **Document Parsing Engine:** `python-docx` (with full traversal support for both document paragraphs and nested table cells).

---

## 3. Installation & Setup Instructions

### Prerequisites
* Python 3.9 or higher

### Step 1: Install Dependencies
```bash
pip install presidio-analyzer presidio-anonymizer faker python-docx spacy
python -m spacy download en_core_web_lg
python redact_pii.py

 Technical Tradeoffs & Error Analysis
A. False Positives (Over-Redaction)
Observed Behavior: Standard corporate terminology, legal clauses, or village/district place names (e.g., "Village Birdewadi", "Chakan Taluka") are occasionally misclassified by the spaCy model as PERSON or ORGANIZATION.

Root Cause: In financial prospectuses, locations and boilerplate legal phrasing often appear in capitalized list formats immediately following officer titles or promoter disclosures, leading the transformer/statistical context parser to misjudge entity boundaries.

B. False Negatives & Model Bias (Under-Redaction)
Observed Behavior: Western names (e.g., "John Doe", "Peter Parker") are consistently detected with high confidence scores (>0.85), whereas non-Western / regional Indian names (e.g., "Kushal Subbayya Hegde", "Rakhi Girija Shetty") are occasionally missed or partially matched.

Root Cause: The base en_core_web_lg model is trained predominantly on news datasets (OntoNotes 5.0) featuring Western naming structures. Complex Indian naming conventions containing regional middle names, family surnames, or patronymics are underrepresented in default training embeddings.
