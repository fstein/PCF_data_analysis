# PCF_data_analysis
Documentation on standard data analysis provided by the Proteomics Core Facility (PCF) at the European Molecular Biology Laboratory (EMBL) Heidelberg

## What you will find in this repository

This repository is intended as a **customer-facing tutorial** to understand proteomics acquisition concepts (DDA‑LFQ, DIA, TMT) and to interpret the **standard data analysis reports** provided by this core facility.

### Workflows (conceptual introductions + figures)

- **TMT (isobaric labeling)**: `TMT/README.md`  
  - Explains experimental design, Tandem Mass Tag (TMT) labeling, MS1/MS2 logic, missing values vs background, ratio compression, and practical implications.  
  - Figures are in `TMT/images/`.
- **DDA‑LFQ (label‑free, data-dependent acquisition)**: `DDA_LFQ/README.md`  
  - Explains DDA selection, MS1‑based quantification, missing values, and common protein quantification strategies (raw intensity, iBAQ, razor intensity, MaxLFQ, Top3).  
  - Figures are in `DDA_LFQ/images/`.
- **DIA (data-independent acquisition)**: `DIA/README.md`  
  - Explains systematic windowed fragmentation, FASTA‑based spectral library generation by prediction, peptide‑centric scoring, and MaxLFQ‑style protein quantities.  
  - Figures are in `DIA/images/`.

### Data analysis examples (report-style results)

- **TMT example analysis**: `TMT/Analysis_example_1/README.md`  
  - This folder contains an end‑to‑end example, including result figures under `TMT/Analysis_example_1/data_analysis_results_V1/`.

### Work in progress

I will add **data analysis explanations and example analyses** for **DDA_LFQ** and **DIA** in their respective folders (similar in spirit to the TMT example).

## License

The documentation in this repository is licensed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

The underlying analysis code (e.g. the R Markdown templates used at the PCF)
is **not** part of this repository and is **not** covered by this license.

