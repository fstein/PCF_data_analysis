## **Data‑Independent Acquisition (DIA) Workflow**

## From samples to quantitative protein profiles (with systematic MS2 sampling)

In **DIA**, each sample is still measured in its own LC‑MS run, but the key difference is *how MS2 spectra are acquired*: instead of choosing a few precursors (as in DDA), the instrument fragments **everything in a predefined m/z scheme**. This makes DIA highly reproducible and typically reduces missing values, but it produces **complex, mixed MS2 spectra** that require dedicated computational analysis.

## Experimental design

Even though DIA is more consistent than DDA, it is still a label‑free workflow acquired across multiple runs, so good design remains essential.

- **Biological replicates**: We recommend at least **three** biological replicates per condition; for subtle effects or noisy systems, **four to five** replicates can markedly increase power.
- **Randomization / blocking**: Randomize run order (or block intentionally) so conditions are distributed across the acquisition sequence.
- **Batch awareness**: If acquisition spans multiple days, distribute conditions across days and include QC samples to monitor stability.

The goal is again to ensure that **biology, not run order**, explains the differences in the final protein matrix.

## Protein preparation and enzymatic digestion

Samples are processed into peptides using standard bottom‑up proteomics steps: protein solubilization/linearisation, reduction of disulphide bridges (commonly DTT), alkylation of cysteines (commonly iodoacetamide; +57.021 Da carbamidomethyl), and enzymatic digestion. **Trypsin** is frequently used because it yields peptides that ionise well and fragment predictably (Figure 2).

| ![][image2] |
| :---- |
| Figure 2: Protein digestion into peptides using a protease e.g. trypsin |

## Liquid‑chromatography and mass‑spectrometry acquisition

Each digested sample is injected onto a nano‑flow C18 column connected to the mass spectrometer inlet (Figure 3). Peptides separate over the gradient and elute at characteristic retention times, forming chromatographic peaks (Figure 4).

| ![][image3] |
| :---- |
| Figure 3: Mass spectrometer with an HPLC column. |

| ![][image4] |
| :---- |
| Figure 4: HPLC chromatogram with a specific peptide eluting from the C18 column at retention time x. |

## DIA: systematic fragmentation across m/z windows

As in other LC‑MS workflows, the instrument records an **MS1** survey scan (Figure 5), which contains all ions present at that retention time.

| ![][image5] |
| :---- |
| Figure 5: MS1 scan (survey scan) at retention time x |

In **data‑independent acquisition**, the instrument then cycles through a set of **predefined isolation windows** that collectively cover a broad precursor m/z range. Within each window, *all* precursors are co‑isolated and fragmented, producing an **MS2 spectrum** (Figure 6) that contains fragment ions from **multiple peptides at once**.

| ![][image6] |
| :---- |
| Figure 6: DIA MS2 spectrum (mixed fragments from all precursors in the isolation window) |

This is the core trade‑off of DIA:

- **Pro**: every cycle measures the same m/z windows, so peptides are sampled more consistently across runs.
- **Con**: MS2 spectra are more complex, so peptide identification/quantification relies on computational deconvolution and scoring.

## How DIA turns complex MS2 data into peptide quantities

DIA data analysis is often described as “peptide‑centric”:

1. **Define expected peptides and fragments**  
   This commonly starts from the protein **FASTA database**: expected peptides are generated in silico, and fragment ions / retention time can be **predicted** to build a **spectral library**. (Some workflows use measured libraries, but FASTA‑based libraries are a very common and practical starting point.)
2. **Extract fragment‑ion chromatograms**  
   For each candidate peptide, the software extracts chromatographic traces for a set of characteristic fragment ions across retention time.
3. **Score evidence and control false discovery**  
   The consistency of fragment patterns, co‑elution, mass accuracy and retention time is scored; results are filtered to control FDR at peptide and protein levels.
4. **Summarize to proteins**  
   Multiple peptide quantities are aggregated to yield a protein‑level abundance matrix.

In practice, DIA quantification often relies more on **MS2 fragment‑ion peak areas** than on MS1 features, because fragment‑ion traces can be more selective when many precursors overlap.

### Protein quantities in DIA

Many DIA reports provide protein quantities using a **MaxLFQ‑style** summarization at the protein level (conceptually similar to LFQ approaches described above), because it often yields stable comparisons across many samples. To keep result tables approachable, we typically provide **one primary protein quantity** of this type in the customer-facing report.

## Missing values, interference and why DIA is often more complete

Because DIA fragments everything in a systematic way, it typically shows **fewer missing values** than DDA:

- Peptides do not need to “win” a top‑N selection to be fragmented.
- The same window scheme is repeated across the entire run and across all samples.

However, DIA is not immune to challenges:

- **Interference**: co‑fragmented peptides can contribute fragments at similar m/z, especially in complex samples.
- **Deconvolution limits**: when many peptides share fragments or co‑elute tightly, separating them computationally becomes harder.
- **Chromatography still matters**: stable retention times and peak shapes are crucial; poor LC performance directly reduces quantitative quality.

## Practical implications for data interpretation

For customers, a few practical takeaways are especially important:

- **Reproducibility is a major strength**: DIA often yields a more complete protein matrix across many samples, which simplifies downstream statistics.
- **Method relies on computation**: results depend on robust scoring/FDR control and on library quality (if using a spectral library).
- **Selectivity comes from fragments**: quantification is frequently performed on carefully chosen MS2 fragment ions to reduce interference.
- **Between‑run normalization still applies**: DIA is label‑free across separate runs, so global differences between samples are expected and must be normalized appropriately.

## Complete workflow

| ![][image7] |
| :---- |
| Complete DIA workflow: systematic windowed fragmentation and peptide‑centric quantification |

[image1]: images/samples_tubes.png
[image2]: images/digestion.png
[image3]: images/mass_spectrometer.png
[image4]: images/chromatogram.png
[image5]: images/MS1_spectra.png
[image6]: images/MS2_spectrum.png
[image7]: images/DIA_workflow.png
