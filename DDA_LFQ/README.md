## **Data‑Dependent Acquisition (DDA) + Label‑Free Quantification (LFQ) Workflow**

## From samples to quantitative protein profiles (without labels)

In a **DDA‑LFQ** experiment, **each sample is measured in its own LC‑MS run**. Peptides are identified from MS/MS spectra (MS2), and peptide/protein abundance is estimated from **chromatographic signal intensities** (typically MS1 feature areas). This makes LFQ flexible and scalable, but it also means that **run‑to‑run alignment** and **missing values** are central topics in the downstream analysis.

## Experimental design

In label‑free proteomics, the mass spectrometer measures samples **one after another**, so good experimental design is mostly about avoiding confounding between biology and run order.

| ![][image1] |
| :---- |
| Figure 1: Experimental design example used throughout this documentation: 9 samples total (3 biological conditions, shown as different colours) with 3 biological replicates per condition. |

- **Biological replicates**: As a rule of thumb we recommend at least **three** biological replicates per condition; for subtle effects or noisy systems, **four to five** replicates can markedly increase power.
- **Randomization / blocking**: Randomize sample run order (or use a blocked design) so that each condition is spread across the acquisition sequence. This reduces the risk that instrument drift or column aging mimics biology.
- **Batch awareness**: If the project must be acquired over multiple days/batches, distribute conditions across batches and include common QC samples to monitor performance.

The goal is simple: **differences in the output matrix should reflect biology, not measurement order.**

## Protein preparation and enzymatic digestion

When the samples arrive, intact proteins are first solubilized/linearised. Disulphide bridges are typically reduced with dithiothreitol (DTT) and irreversibly capped with iodoacetamide, ensuring that no disulphide bridges reform and that all cysteines carry a uniform +57.021 Da carbamidomethyl modification. Because bottom‑up proteomics analyses peptides rather than full‑length proteins, the denatured proteins are digested. **Trypsin** is the protease of choice because it cuts with high specificity after lysine and arginine, generating peptides in a mass range that ionises efficiently and yields predictable MS/MS fragmentation patterns (Figure 2).

| ![][image2] |
| :---- |
| Figure 2: Protein digestion into peptides using a protease e.g. trypsin |

## Liquid‑chromatography and mass‑spectrometry acquisition

Each digested sample is injected onto a nano‑flow C18 column connected to the mass spectrometer inlet (Figure 3). During the gradient, peptides separate by hydrophobicity and elute at characteristic retention times, producing a series of Gaussian‑like peaks in the chromatogram (Figure 4).

| ![][image3] |
| :---- |
| Figure 3: Mass spectrometer with an HPLC column. |

| ![][image4] |
| :---- |
| Figure 4: HPLC chromatogram with a specific peptide eluting from the C18 column at retention time x. |

## DDA: how peptides are selected for MS2 identification

When peptides reach the electrospray tip they are ionised, and the instrument records a full‑scan spectrum of all ions present at that moment. This first survey scan is called **MS1** (Figure 5).

| ![][image5] |
| :---- |
| Figure 5: MS1 scan (survey scan) at retention time x |

In **data‑dependent acquisition (DDA)** the instrument software then selects a limited number of precursor ions—typically the **most intense peaks** in MS1—for fragmentation. Each selected precursor is isolated in a narrow m/z window, accelerated and fragmented (commonly by HCD). The resulting fragments are recorded in an **MS2 spectrum** (Figure 6).

| ![][image6] |
| :---- |
| Figure 6: MS2 spectrum of a selected MS1 precursor |

The MS2 spectrum contains:

- **Peptide backbone fragments** (higher m/z): used to infer the amino‑acid sequence by database search, yielding peptide identities with statistical confidence.
- **Other ions / noise**: co‑isolated species and chemical background can contribute, especially at low abundance.

## LFQ: where the quantitative signal comes from

In label‑free quantification, abundance is most commonly estimated from **chromatographic peak areas** of peptide features (an MS1 signal traced over retention time). Conceptually:

1. **Detect features**: find peptide isotope patterns in MS1 across retention time (features = m/z + retention time + intensity).
2. **Identify peptides**: link features to peptide IDs using MS2 search results.
3. **Align runs**: match the “same” peptide feature across multiple LC‑MS runs by retention time alignment (and sometimes by MS1 mass accuracy).
4. **Summarize to proteins**: aggregate multiple peptide quantities into a protein‑level quantity (e.g., median/mean of selected peptides, sometimes after filtering to reduce interference).

### Common protein quantification strategies you may encounter

Different pipelines can report slightly different “protein quantities”. All of them aim to summarize peptide evidence into a protein‑level number, but the assumptions differ. Common strategies include:

- **Raw intensity**: a direct summary of peptide signal (often a sum or mean/median across selected peptides). This is simple and intuitive but can be sensitive to missing peptides and to how peptides are filtered.
- **iBAQ (intensity‑Based Absolute Quantification)**: protein intensity is divided by the number of theoretically observable tryptic peptides for that protein. iBAQ is often used as a proxy for **within‑sample protein abundance ranking** (it is not truly absolute without standards), and it can help compare abundance *within* a sample.
- **MaxLFQ**: a label‑free method that infers protein quantities from a network of peptide ratios across runs and aims to improve **between‑sample comparability**, especially when not every peptide is observed in every run.
- **Top3 / Hi‑3**: protein quantity is estimated from the **three most intense peptides** (or a small fixed number). This can be robust when many peptides are noisy, but it assumes the top peptides are consistently observed and behave comparably between samples.

In some pipelines you may also see **razor intensity**: peptides that could belong to multiple protein groups (“shared peptides”) are assigned to the single group with the strongest overall evidence, and the resulting intensities are summarized at the protein level. This reduces double‑counting, but it means the reported quantity is tied to protein grouping and peptide assignment rules.

### What you will typically receive in your report

To avoid overwhelming users, our reports usually provide **one primary protein quantity** column that is used for downstream statistics and plots. Depending on the analysis strategy used for your project, this primary quantity may be:

- **iBAQ** (often useful for within‑sample abundance ranking),
- **razor intensity** (a straightforward intensity summary after protein grouping), or
- **MaxLFQ** (a between‑run comparable protein quantity).

The output is a **protein × sample matrix** that is used for QC, normalization, statistical testing and enrichment analysis.

## Missing values and why they happen in DDA‑LFQ

Compared to multiplexed labeling, **DDA‑LFQ typically has more missing values**, and they are often abundance‑dependent:

- **Stochastic MS2 selection**: only a limited number of precursors are selected each cycle; low‑abundance peptides may not be chosen in every run even if they are present.
- **Run‑to‑run variability**: slight shifts in chromatography or ionization can change which peptides cross the selection threshold.
- **Identification limits**: a peptide feature may be present in MS1 but not confidently identified by MS2 in a given run.

Downstream analysis therefore spends a lot of attention on:

- **Feature alignment / “match between runs”** (to reduce missingness while controlling false matches)
- **Filtering** (e.g., requiring a peptide/protein to be observed in a minimum fraction of samples)
- **Imputation** (used cautiously; the right strategy depends on whether missingness looks random or intensity‑dependent)

## Practical implications for data interpretation

Because samples are acquired in separate runs, the analyst needs to watch a few recurring issues:

- **Run order effects / drift**: gradual changes in LC performance or source cleanliness can introduce systematic shifts. Good randomization and QC injections help detect and control this.
- **Between‑run normalization**: global intensity differences between runs are expected; normalization aims to make samples comparable without removing true biology.
- **Depth versus completeness trade‑off**: DDA can identify many peptides, but completeness across all samples can be limited—especially for low‑abundance proteins.
- **Comparable sample types**: mixing very different proteomes (e.g., tissue vs plasma) within one LFQ project can increase dynamic‑range problems and worsen missingness for the less complex/less abundant class.

## Complete workflow

| ![][image7] |
| :---- |
| Complete DDA‑LFQ workflow: from samples to a protein × sample abundance matrix |

[image1]: images/samples_tubes.png
[image2]: images/digestion.png
[image3]: images/mass_spectrometer.png
[image4]: images/chromatogram.png
[image5]: images/MS1_spectra.png
[image6]: images/MS2_spectrum.png
[image7]: images/DDA_LFQ_workflow.png
