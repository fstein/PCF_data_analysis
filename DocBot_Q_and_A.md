# DocBot Q&A Test Document
# EMBL Proteomics Core Facility (PCF)
#
# This document contains 15 questions and answers for testing DocBot's ability
# to correctly retrieve and respond based on the provided context (PCF websites
# and PCF_data_analysis repository documentation).
#
# =============================================================================

--------------------------------------------------------------------------------
QUESTION 1: How do I submit samples to the PCF?
--------------------------------------------------------------------------------

ANSWER:
To submit samples to the EMBL Proteomics Core Facility, follow these four steps:

1. **Contact Early**: Email pcf@embl.de BEFORE preparing your samples. Include your full name, address, group leader affiliation, analysis type (proteome analysis, immunoprecipitations, phosphoproteomics, etc.), sample type, FASTA database availability, sample number, annotations, and biological replicates (minimum 3 required). Early contact enables support in shaping your experimental design.

2. **Prepare Samples**: Consult the FAQ section for general preparation guidelines, recommended sample amounts, and compatible buffers. Check the Protocols section for gel preparation details.

3. **Complete Service Request Form**: External users must submit the Service Request Form (available on the website). EMBL internal users register through PPMS online.

4. **Send Samples**: Notify the facility before shipping, include a printed Service Request form, and review the Shipping Guidelines. CRITICAL: Do not send any samples until you have contacted the facility and received confirmation.

**Shipping Address**: EMBL Heidelberg, Proteomics Core Facility, Meyerhofstrasse 1, 69117 Heidelberg, Germany
**Contact**: +49 6221 387-8388 or pcf@embl.de
**Turnaround**: 4 to 6 weeks for in-solution samples from receipt date.


--------------------------------------------------------------------------------
QUESTION 2: What mass spectrometers does the PCF have available?
--------------------------------------------------------------------------------

ANSWER:
The EMBL Proteomics Core Facility operates five mass spectrometers:

1. **Q Exactive Plus**: High-resolution system with quadrupole technology, Orbitrap analyzer, and HCD fragmentation. Supports DDA and DIA acquisition modes.

2. **Orbitrap Lumos Tribrid**: Advanced instrument featuring quadrupole, Orbitrap, and ion trap with SPS capability. Enables HCD, CID, and ETD fragmentation for DDA, DIA, MS3-based methods, and PTM analysis.

3. **Orbitrap Exploris 480**: High-resolution system optimized for DIA analyses with faster transit times.

4. **Orbitrap Astral**: Next-generation system with sensitive, fast Astral analyzer. Suited for ultra-deep, sensitive DDA and DIA analyses.

The facility also operates seven HPLC systems running 24/7, including the Ultimate 3000 HPLC and Vanquish Neo HPLC for online peptide separation.


--------------------------------------------------------------------------------
QUESTION 3: What is the difference between DDA-LFQ, DIA, and TMT quantification methods?
--------------------------------------------------------------------------------

ANSWER:
The three main quantification methods differ in their approach:

**DDA-LFQ (Data-Dependent Acquisition with Label-Free Quantification)**:
- Each sample is measured in separate LC-MS runs without chemical labels
- The instrument selects the most intense precursor ions for fragmentation (MS2)
- Quantification is based on MS1 chromatographic peak areas
- Advantages: Flexible, scalable, no labeling costs
- Challenges: Missing values (stochastic MS2 selection), run-to-run variability

**DIA (Data-Independent Acquisition)**:
- Systematic fragmentation of everything in predefined m/z windows
- All precursors in each window are co-isolated and fragmented together
- Relies on predicted spectral libraries and fragment-ion chromatography
- Advantages: Fewer missing values, high reproducibility across runs
- Challenges: Complex MS2 spectra require computational deconvolution

**TMT (Tandem Mass Tag)**:
- Multiple samples (6, 11, or 18) are chemically labeled with isobaric tags
- All samples are combined and measured in a single LC-MS run
- Reporter ions in MS2 spectra reveal relative abundances
- Advantages: Very few missing values, precise ratios, reduced measurement time
- Challenges: Ratio compression (10-30% typical), co-isolation interference


--------------------------------------------------------------------------------
QUESTION 4: How much protein do I need to submit for different analyses?
--------------------------------------------------------------------------------

ANSWER:
Sample requirements vary by experiment type:

- **Full proteome / Cell lysates**: 20 micrograms of protein in 60 microliters buffer
- **Secretome / Extracellular vesicles**: 5-10 micrograms of protein
- **Immunoprecipitation eluates**: Maximum 60 microliters per sample (do not quantify eluates; submit equal volumes)
- **Phosphoproteomics**: 500-1,000 micrograms of total protein (high input required for enrichment)

Important notes:
- Always measure protein content before submission using recommended methods (Tryptophan assay, Bradford assay, or BCA assay)
- Avoid NanoDrop spectrophotometer measurements as they are not reliable
- For viscous samples, treat with benzonase or sonication to reduce viscosity


--------------------------------------------------------------------------------
QUESTION 5: What statistical analysis does the PCF perform on proteomics data?
--------------------------------------------------------------------------------

ANSWER:
The PCF performs comprehensive statistical analysis using R, including:

**Data Processing**:
- Quality filtering (removal of decoys, contaminants, proteins with <2 razor peptides)
- Log2 transformation for near-normal distribution
- Batch effect removal using limma::removeBatchEffect
- Variance-stabilizing normalization (VSN)
- Imputation of missing values when necessary

**Differential Abundance Analysis**:
- Empirical Bayes moderated t-test using limma (borrows strength across all proteins)
- FDR control via limma and fdrtool methods
- Hit classification: FDR <= 0.05 and |log2FC| >= log2(1.5)
- Candidate classification: FDR <= 0.2 and |log2FC| >= log2(1.5)

**Visualization and Exploration**:
- PCA analysis at each transformation step
- Volcano plots (log2FC vs -log10 p-value)
- MA plots (fold-change vs average abundance)
- Coefficient of variation (CV) overview by condition
- Heatmaps with hierarchical clustering
- K-means clustering for pattern discovery

**Functional Analysis**:
- Gene Ontology (GO) enrichment analysis
- Over-representation analysis using Fisher's exact test
- Three GO categories: Cellular Component (CC), Molecular Function (MF), Biological Process (BP)


--------------------------------------------------------------------------------
QUESTION 6: What output files and deliverables will I receive from the PCF?
--------------------------------------------------------------------------------

ANSWER:
Results are delivered in a data_analysis_results folder containing:

**CSV Data Files**:
- Full_dataset.csv: Wide protein x sample table with intensities at all transformation stages
- Identification_Overview.csv: Presence/absence matrix
- PCA_analysis_data.csv: PCA coordinates, variance explained, sample metadata
- Limma_results.csv: Statistics per protein per contrast (logFC, t-stat, p-values, FDR, hit annotations)
- Clustering_data.csv: Condensed matrix for clustering analysis
- Cluster_results.csv: Cluster assignments (kmeans and hclust)
- GO enrichment tables: Separate files for CC/MF/BP enrichment

**PDF Visualizations**:
- Identification_CountOverview: Protein counts per sample
- Identification_UpSet_plot: Sample intersection patterns
- Missing value overview heatmaps
- Normalization overview boxplots
- PCA_analysis plots at each step
- Volcano plots and MA plots
- Clustering heatmaps and line plots
- GO enrichment dotplots

**Documentation**:
- Methods.txt: Copy-paste ready methods narrative with software versions and parameters
- Workspace.RData: Frozen R session with all analysis objects

Raw files are archived for 10 years, and the facility supports PRIDE repository submission.


--------------------------------------------------------------------------------
QUESTION 7: What buffers are compatible with PCF sample submission?
--------------------------------------------------------------------------------

ANSWER:
Most commonly used lysis buffers are compatible with the facility's modified SP3 (Single-Pot Solid-Phase-enhanced Sample Preparation) protocol, including:

- Laemmli buffer
- SDS sample buffer
- RIPA buffer
- Other harsh detergent buffers

**Recommendations**:
- Use harsh detergent buffers like RIPA with protease inhibitors for full proteome samples
- Include Benzonase or sonication for DNA degradation (reduces viscosity from genomic DNA release)
- For secretome samples, culture cells in serum-free medium to avoid abundant serum proteins dominating the analysis
- For plasma/serum samples, the facility recommends using the ENRICH-iST kit for abundant protein depletion

Contact the facility (pcf@embl.de) if you are uncertain about buffer compatibility.


--------------------------------------------------------------------------------
QUESTION 8: What is TMT ratio compression and why does it occur?
--------------------------------------------------------------------------------

ANSWER:
TMT ratio compression is a phenomenon where the measured fold-changes between conditions appear smaller than the true biological differences, typically resulting in 10-30% compression of the expected ratios.

**Why it occurs**:
Ratio compression happens due to co-isolation interference during MS2 acquisition. When a precursor ion is selected for fragmentation, nearby precursor ions within the isolation window can also be co-isolated and fragmented together. These co-isolated peptides contribute their own reporter ion signals, which dilutes the true signal ratios between samples.

**Contributing factors**:
- Isolation window width (wider windows = more co-isolation)
- Sample complexity (more peptides = higher chance of co-isolation)
- Chromatographic co-elution of peptides

**Mitigation strategies**:
- MS3-based methods on Tribrid instruments (SPS-MS3)
- Offline fractionation (high-pH C18) to reduce sample complexity per injection
- Narrower isolation windows (at the cost of sensitivity)

Despite compression, TMT still provides highly reproducible relative quantification with very few missing values.


--------------------------------------------------------------------------------
QUESTION 9: How many proteins can I expect to identify and quantify?
--------------------------------------------------------------------------------

ANSWER:
Typical protein identification and quantification numbers depend on sample type:

- **HEK293T full proteome**: ~8,000 protein groups identified, ~7,000 quantified
- **E. coli lysate**: ~2,200 protein groups identified, ~2,000 quantified
- **Tissue lysates**: Typically ~4,000 quantified proteins

**Factors affecting coverage**:
- Sample complexity and protein concentration
- Measurement time and instrument used
- Fractionation strategy (offline fractionation increases depth)
- Acquisition method (DIA typically provides more consistent quantification)

**Why a protein of interest might not be detected**:
- Small proteins produce fewer tryptic peptides
- Uneven tryptic cleavage site distribution
- Low expression levels
- Protein localization effects (membrane proteins can be challenging)
- Absence from the FASTA database used for searching


--------------------------------------------------------------------------------
QUESTION 10: What is the difference between MaxLFQ, iBAQ, and raw intensity quantification?
--------------------------------------------------------------------------------

ANSWER:
These are different protein quantification strategies used in label-free proteomics:

**Raw Intensity**:
- Direct sum or mean of selected peptide intensities
- Simple but sensitive to missing data
- Best for basic comparisons when data is complete

**iBAQ (Intensity-Based Absolute Quantification)**:
- Intensity divided by the number of theoretical tryptic peptides
- Normalizes for protein size
- Useful for within-sample protein abundance ranking
- Allows rough comparison of different proteins' abundances

**MaxLFQ**:
- Infers protein quantities from peptide ratio networks
- Uses the median of pairwise peptide ratios between samples
- Improves between-run comparability
- Robust to missing values and noise
- Standard approach for DIA and DDA-LFQ comparative analyses

**Top3 / Hi-3**:
- Uses only the three most intense peptides per protein
- Based on the observation that the top peptides correlate with protein amount
- Robust to noise but assumes the top peptides are consistently detected

**Razor Intensity**:
- Shared peptides are assigned to the protein with the strongest evidence
- Resolves peptide-to-protein ambiguity in a principled way


--------------------------------------------------------------------------------
QUESTION 11: What are the current costs for PCF services?
--------------------------------------------------------------------------------

ANSWER:
Current rates (as of August 2025) for external academic users:

**Measurement Time**: 100 EUR per hour (150 EUR/hour for industry)

**Sample Processing**:
- Basic sample processing: 3 EUR per sample
- Desalting: 20 EUR per sample
- TMT labeling: 100-280 EUR (depending on plex size)

**Example calculation**:
An 18-sample TMT experiment costs approximately 3,044 EUR total.

**Important notes**:
- The facility does NOT offer discounts for higher sample numbers
- Costs depend on consumables, labeling method (TMT or DIA), measurement time, and complexity
- Final pricing is provided after initial consultation based on your specific experimental design

Contact pcf@embl.de for a detailed quote for your project.


--------------------------------------------------------------------------------
QUESTION 12: What protocols does the PCF provide for sample preparation?
--------------------------------------------------------------------------------

ANSWER:
The PCF provides four main protocols available as downloadable PDFs:

1. **Colloidal Coomassie Staining**:
   - For gel visualization before mass spectrometry analysis
   - Stains must be MS-compatible (check manufacturer datasheets)
   - CRITICAL: Do not boil or microwave gels as this will impair analysis by fixing proteins in the gel

2. **Gel Band Excision**:
   - Guidance for preparing gel samples for submission
   - External customers should pre-excise bands before shipping
   - EMBL Heidelberg researchers can submit destained gels directly

3. **Tryptophan Assay**:
   - Fluorescence-based protein quantification method
   - Cost-effective, user-friendly, compatible with wide range of sample types
   - Requires black microplates and fluorescence-capable plate reader

4. **Mammalian Cell Lysis**:
   - Denaturing lysis protocol for mammalian cells
   - General option when no project-specific protocol is available
   - Contact facility if uncertain about protocol suitability


--------------------------------------------------------------------------------
QUESTION 13: How does the PCF handle batch effects in data analysis?
--------------------------------------------------------------------------------

ANSWER:
Batch effects are systematic technical variations that can confound biological signal. The PCF addresses batch effects through:

**Prevention (Experimental Design)**:
- Randomization of sample run order is crucial to avoid batch confounding
- Balanced experimental designs (equal replicates per condition)
- For TMT: strategic channel assignment to avoid precursor dominance

**Detection (Quality Control)**:
- PCA analysis at each transformation step to visualize batch vs. biology separation
- If samples cluster by batch rather than biological condition, batch correction is needed

**Correction (Data Processing)**:
- limma::removeBatchEffect function removes known batch effects (e.g., replicate/day effects)
- Applied after log2 transformation but before downstream analysis
- Requires batch information in the metadata

**Monitoring**:
- CV (Coefficient of Variation) overview plots show reproducibility by condition
- PCA plots before and after batch correction confirm successful removal
- Correlation plots assess sample-to-sample consistency

The facility's analysis pipeline includes these steps systematically, with visualizations at each stage documented in the output PDF files.


--------------------------------------------------------------------------------
QUESTION 14: Who can use the PCF services and how do I get started?
--------------------------------------------------------------------------------

ANSWER:
**Eligibility**:
- Services are primarily available to EMBL researchers
- External academic institutions are also supported
- Industrial partners may be considered based on capacity

**Getting Started**:

1. **Initial Contact**: Email pcf@embl.de with:
   - Your full name, institution, and group leader
   - Type of analysis needed
   - Sample type and number
   - Biological question you're addressing

2. **Consultation**: The facility provides support in:
   - Shaping experimental design
   - Determining appropriate number of biological replicates (minimum 3)
   - Selecting controls
   - Specifying sample requirements

3. **Project Planning**: The facility handles over 600 projects annually and provides guidance on:
   - Choosing between DDA-LFQ, DIA, or TMT approaches
   - Sample preparation protocols
   - Expected protein coverage and timeline

**Contact Information**:
- Email: pcf@embl.de
- Phone: +49 6221 387-8388
- Address: EMBL Heidelberg, Meyerhofstrasse 1, 69117 Heidelberg, Germany

**Important**: You cannot request specific instruments - the facility assigns the most appropriate instrument for your analysis.


--------------------------------------------------------------------------------
QUESTION 15: What quality control steps are included in PCF data analysis?
--------------------------------------------------------------------------------

ANSWER:
The PCF performs comprehensive quality control at multiple stages:

**Identification QC**:
- Protein counts per sample (Identification_CountOverview plot)
- UpSet plots showing sample intersection patterns
- Missing value overview heatmaps
- Filtering: removal of decoys, contaminants, and proteins with fewer than 2 razor peptides

**Normalization QC**:
- Boxplot distributions at each transformation step (raw, log2, batch-corrected, VSN-normalized)
- Control ratio distributions to verify successful normalization
- Per-protein profiles showing transformation effects

**Reproducibility QC**:
- PCA analysis at each step (samples should cluster by biology, not by batch)
- Coefficient of Variation (CV) plots by condition
- Correlation plots between replicates

**Statistical QC**:
- P-value histograms comparing limma vs fdrtool FDR estimation
- T-statistic vs FDR plots to assess statistical model fit
- Volcano and MA plots for effect size visualization

**Clustering QC**:
- Silhouette analysis for optimal cluster number selection
- Heatmaps with hierarchical clustering to identify expression patterns
- Cluster trend line plots with standard error of the mean (SEM)

All QC visualizations are provided as PDF files in the results package, allowing users to assess data quality at every stage of the analysis pipeline.

=============================================================================
END OF Q&A DOCUMENT
=============================================================================
