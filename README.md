# AEESP Grand Challenges NLP Pipeline

## Overview
This project aims to explore the extent to which research directions in environmental engineering support the design of integrated, resilient, and equitable systems that sustainably support human and environmental well-being. These directions are articulated in the NASEM report on the Grand Challenges of Environmental Engineering. 

To accomplish this, the project provides reproducible Jupyter notebooks that compile a corpus of literature published by AEESP (Association of Environmental Engineering and Science Professors) members and conducts bibliometric analysis on that corpus.

## Project Architecture
The pipeline is split into two primary components:

### 1. OpenAlex Bibliographic Corpus Builder (`aseep_cleanedcorpus.ipynb`)
This notebook queries the OpenAlex API to collect publications for an inputted list of researchers and builds a structured corpus. 
* **Input:** A CSV file (`aeesp_members_short.csv`) containing the first name, last name, and institution of the authors.
* **Process:** Searches for the institution and author, handles author disambiguation using environmental engineering keywords, and extracts works excluding non-archival sources (like preprints, theses, and conference abstracts). It also reconstructs inverted abstracts into readable text.
* **Output:** Generates a zipped archive (`all_authors_works.zip`) containing individual CSV files for each author (`works_df_[lastname]_[firstname].csv`).

### 2. Guided Topic Modeling (`bertopic_modeling.ipynb`)
This notebook processes the bibliographic corpus to classify papers into one of the designated NASEM Grand Challenges.
* **Input:** The combined CSV files generated from the OpenAlex pipeline (`works_df_*.csv`).
* **Process:** * Filters the dataset to retain only papers published in or after 2000.
    * Removes entries with missing or incoherent abstracts.
    * Combines the title, keywords, and abstract into a single text document for analysis.
    * Uses `SentenceTransformer` with the `allenai/specter2_base` model to generate scientific text embeddings.
    * Applies a Guided (Zero-Shot) BERTopic model to map papers to the Grand Challenges.
* **Output:** A master classification CSV (`grand_challenges_classified_papers.csv`) and a summary table (`bertopic_summary.csv`).

## NASEM Grand Challenges Context
The zero-shot topic modeling algorithm clusters the text data based on six predefined Grand Challenges:
1.  **Energy efficient water conservation strategies and technologies:** Covers smart water systems, water reuse, greywater treatment, leak detection, and behavioral science.
2.  **Low-cost desalination and water reuse technologies:** Covers reverse osmosis, membrane distillation, zero liquid discharge, and brine valorization.
3.  **Water supply and water quality forecasting tools:** Covers sensor networks, remote sensing, digital twins, machine learning, and contamination event detection.
4.  **Energy-neutral or energy-positive cost-effective wastewater treatment technologies:** Covers anaerobic digestion, microbial fuel cells, nutrient recovery, and constructed wetlands.
5.  **Water, sanitation, and hygiene challenges in low-income countries:** Covers basic sanitation access, household water treatment, social science, and fecal sludge management.
6.  **Aging water infrastructure:** Covers asset management, pipe failure, lead pipes, corrosion control, and smart infrastructure.

## Usage and Installation
These notebooks are designed to be run in **Google Colab**.

**Step 1: Build the Corpus**
1. Open `aseep_cleanedcorpus.ipynb` in Google Colab.
2. Run the initialization cells to install `pyalex`.
3. When prompted, upload your author list (`aeesp_members_short.csv`).
4. Execute the remaining cells. The script will automatically download a `.zip` file containing your corpus.

**Step 2: Classify the Papers**
1. Open `bertopic_modeling.ipynb` in Google Colab.
2. Upload the unzipped `works_df_*.csv` files generated from Step 1 directly into the Colab environment's file system.
3. Run the cells to install `bertopic` and `sentence_transformers`.
4. Execute the pipeline. The notebook will automatically clean the data, generate the embeddings, classify the documents, and download the resulting datasets (`grand_challenges_classified_papers.csv` and `bertopic_summary.csv`).

## Future Steps and Planned Features
As the project evolves, the team plans to expand the pipeline's capabilities across data cleaning, scale, and visual analysis.

### 1. Advanced Analysis & Visualization
* **Topic Visualizations:** Leverage the BERTopic model and SPECTER2 embeddings to generate interactive visual representations of the data, such as topic probability distributions and inter-topic distance maps.
* **Keyword Trends:** Conduct deeper analyses on keyword occurrences across the corpus, drawing methodological inspiration from existing bibliometric literature (e.g., Zhu et al. and Lowry et al.).

### 2. Data Pipeline Enhancements
* **Author Disambiguation:** Integrate ORCID IDs into the OpenAlex pipeline to more accurately identify authors and resolve edge cases with affiliation mismatches or shared names. 
* **Expanded Metadata:** Update the data extraction script to pull the publisher name (`host_organization_name`) directly from the OpenAlex source object.
* **Stricter Noise Filtering:** Implement robust filters to automatically screen out non-archival entries, such as removing papers without DOIs, filtering out specific protocol domains, and dropping entries missing a journal title.

### 3. Scaling & Output Formatting
* **Full Membership Processing:** Scale the current pilot pipeline (which tested 25-26 authors) to retrieve and analyze the publication data for all ~1,500 AEESP members.
* **Accessible Data Exports:** Develop an export function to push the cleaned publication lists for each author into dedicated tabs within a Google Sheet, making it easier for researchers to manually review the data.
