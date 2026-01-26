# **Tandem Mass Tag (TMT) Workflow**

## From Samples to Quantitative Protein Profiles

TMT is an **isobaric labeling** strategy: peptides from multiple samples are chemically tagged, **combined**, and measured in a **single LC‑MS run**. Identification comes from peptide fragments, while quantification comes from **low‑m/z reporter ions** released from the tags during MS2.

## Experimental design

Every TMT project begins with a carefully balanced design. In the scheme shown in Figure 1 three experimental conditions are represented by three biological replicates each.

| ![][image1] |
| :---- |
| Figure 1: Simple experimental design of a TMT experiment. |

In total, this example contains **9 samples**: **3 biological conditions** (different colours) with **3 biological replicates** per condition. The sections below explain how these same 9 samples are processed, measured, and turned into a quantitative protein matrix in a TMT workflow.

Balanced replicate numbers give every condition identical statistical weight and ensure that any observed change reflects biology rather than unequal group sizes. As a rule of thumb we recommend at least three biological replicates; however, when the system is noisy or the anticipated effect sizes are subtle, four or even five replicates can markedly increase statistical power. Additional conditions - extra time‑points, drug doses or genotypes - may be included as long as the total sample count stays within the multiplexing capacity of the chosen tag set (6, 11 or 18 channels). If your project outgrows a single set, simply divide the replicates across multiple TMT sets, but make sure that each set contains at least one complete replicate of every condition to preserve unbiased comparisons and simplify downstream normalisation.

## Protein preparation and enzymatic digestion

When the samples arrive, intact proteins are first linearised. We reduce disulphide bridges with dithiothreitol (DTT) and irreversibly cap the resulting thiols with iodoacetamide, ensuring that no disulphide bridges reform and that all cysteines carry a uniform +57.021 Da carbamidomethyl modification. Because bottom‑up proteomics analyses peptides rather than full‑length proteins, the denatured proteins are digested. Trypsin is the protease of choice because it cuts with high specificity after lysine and arginine, generating peptides in a mass range that ionises efficiently and yields predictable MS/MS fragmentation patterns (Figure 2).

| ![][image2] |
| :---- |
| Figure 2: Protein digestion into peptides using a protease e.g. trypsin |

## Chemistry of TMT labelling

The digested peptide mixture is subsequently labelled with isobaric Tandem Mass Tags. Figure 3 illustrates the two main chemistries you will encounter in the core facility: the original TMT 6/11‑plex reagents and the newer TMTpro 18‑plex set. 

| ![][image3] |
| :---- |
| Figure 3: Different Tandem Mass Tag (TMT) formulas for TMT6plex / TMT11plex and TMT18plex technology. |

Both reagents carry an N‑hydroxysuccinimide (NHS) ester group that reacts spontaneously with primary amines—namely the peptide N‑terminus and ε‑amines of lysine side chains—forming a stable amide bond. Each tag is split conceptually into a reporter region, a balancer region and a reactive group. Heavy isotopes (¹³C, ¹⁵N) are distributed across reporter and balancer in a mirror‑symmetric fashion (dashed line in Figure 3) so that every tag has the same total mass even though each reporter mass is unique. This ingenious design ensures that peptides from different samples co‑elute and appear as a single peak in the first MS scan, yet remain distinguishable once the tag is fragmented.

A separate tag is assigned to every biological replicate; unused channels can be filled with a pooled internal reference or left empty for future expansion. After labelling the reaction is quenched and the individual samples are combined into a single tube (Figure 4) so that all subsequent processing and MS acquisition steps affect every sample identically—a built‑in normalisation that boosts quantitative precision.

| ![][image4] |
| :---- |
| Figure 4: Mixed digested samples after TMT labeling. |

## Liquid‑chromatography and mass‑spectrometry acquisition

The multiplexed peptide mixture is injected onto a nano‑flow C18 column that is bonded directly to the mass spectrometer inlet (Figure 5).

| ![][image5] |
| :---- |
| Figure 5: Mass spectrometer with an HPLC column. |

During the chromatographic gradient peptides interact with the stationary phase according to their hydrophobicity and elute at characteristic retention times, producing a series of Gaussian‑like peaks (Figure 6).

| ![][image6] |
| :---- |
| Figure 6: HPLC chromatogram with a specific peptide eluting from the C18 column at retention time x. |

When a particular peptide reaches the electrospray tip it is ionised, and the mass spectrometer records a full‑scan spectrum of every ion present at that retention time. This first survey scan is called MS1 (Figure 7).

| ![][image7] |
| :---- |
| Figure 7: MS1 scan at retention time x |

Because all tags are isobaric, the same peptide labelled with different tags produces one undifferentiated peak in MS1. The peak intensity is the sum of the contributions from every tagged replicate; a highly abundant peptide therefore produces a high MS1 intensity regardless of which sample it originated from. The instrument software continuously evaluates the MS1 spectrum and selects the most intense precursor ions for fragmentation via higher‑energy collisional dissociation (HCD). The selected precursor is isolated in a narrow m/z window (typically ±0.4 Th), accelerated and fragmented. The resulting fragment masses are recorded in an MS2 spectrum (Figure 8).

| ![][image8] |
| :---- |
| Figure 8: MS2 scan at selected MS1 mass |

The MS2 has two distinct regions:

- Peptide backbone fragments on the high‑mass side encode the amino‑acid sequence. Database search engines match these ions against an in silico tryptic digest of the reference proteome, assigning a peptide identity with statistical confidence.  
- Reporter ions on the low‑mass side reveal the relative abundance of that peptide across all multiplexed samples. Cleavage of a single, labile bond (see dashed line in Figure 3) in the tag liberates the reporter group whose mass differs by exact integer increments (126–131 Da in TMT 6‑plex, 126–134 Da in TMTpro 18‑plex). Quantifying the area of each reporter ion therefore yields a direct measurement of the peptide's abundance in every replicate.

## Building protein‑level quantities

A single protein typically yields many tryptic peptides, and each peptide can be observed multiple times across the LC gradient. To move from the peptide‑ to the protein‑level, reporter intensities from every peptide‑spectrum match (PSM) belonging to the same protein are aggregated—most commonly by summing but sometimes by taking the median or a variance‑stabilising normalised mean. The resulting protein × sample matrix is the input for bioinformatic analysis: quality control, normalisation, statistical testing and gene ontology enrichment.

## Missing values, background signal and ratio compression

An attractive feature of TMT is its low proportion of missing values. Once a peptide has been selected for fragmentation, reporter intensities are recorded for every channel, even if that peptide was completely absent from one of the biological samples. In the cartoon chromatogram of Figure 9 the peptide is missing in the blue replicates, yet its co‑eluting versions in the orange and purple samples still trigger fragmentation.

| ![][image9] |
| :---- |
| Figure 9:  |

The corresponding MS1 peak in Figure 10 is smaller—because the blue contribution is absent - but still surpasses the selection threshold.

| ![][image10] |
| :---- |
| Figure 10: |

Consequently an MS2 spectrum is registered (Figure 11). Reporter ions for the blue channels are reduced to low background counts rather than being entirely absent, which means no computational imputation is required downstream.

| ![][image11] |
| :---- |
| Figure 11:  |

Background counts can, however, arise from co‑isolation. The isolation window around the precursor often captures a second, unrelated peptide of almost identical m/z (green peak in Figures 7 and 10). Reporter ions from this contaminant add to those of the target peptide, reducing the observed fold change (see green signal in the TMT region in Figure 11). The phenomenon - referred to as ratio compression - is usually limited to 10‑30 % but can be mitigated by narrow isolation windows, offline pre-fractionation, FAIMS interfaces or mixed‑mode acquisition strategies. Interpreting very small expression changes (<1.2‑fold) therefore requires particular caution.

## Practical implications for data interpretation

Since all channels are mixed before the LC‑MS run, any variability introduced after labelling affects every sample equally and therefore cancels out in the final ratios. The two practical issues the analyst still needs to watch are precursor dominance and channel misuse.

### Precursor dominance — keep protein loads similar

Equally critical is the protein load balance across channels. If one sample enters the workflow with a markedly higher protein concentration, its peptide precursors dominate the MS1 spectrum. Because data dependent acquisition always targets the most intense ions, the over represented channel effectively decides which peptides are selected for fragmentation, diminishing sequencing depth for the remaining samples. In the subsequent MS2 scan the reporter ion of the overloaded channel can exceed the linear dynamic range of the TMT chemistry, while the corresponding reporters from low load samples hover near the noise floor. A peptide that is present only in the dominant channel nevertheless produces faint reporter traces in every other channel, meaning that comparisons between two low‑load channels amount to "noise versus noise" and can generate false positives. To avoid this, normalise protein concentration to ideally yield a 1:1 ratio across samples. 

### Channel misuse — avoid unrelated samples in the same plex

Occasionally researchers are tempted to "fill" unused channels with leftover material from a different project. An unrelated sample often has a different proteome: its unique, highly abundant peptides monopolise MS1 intensity much like an over‑loaded channel would, but with the added drawback that those peptides are biologically irrelevant to the current study. They consume sequencing time, increase background reporter counts, and inflate the false‑discovery risk when ratios are calculated. If channels remain vacant after assigning all planned replicates and controls, it is safer to leave them empty or devote them to pooled references than to introduce an unrelated sample.

Keeping the input loads balanced and the channel roster relevant to the biological question ensures that every replicate is sampled with comparable depth and that the reporter ion signals fall into the linear, low‑noise region of the TMT response curve. Interpreting the resulting protein ratio matrix then becomes a question of biology rather than artefact.

# Complete workflow

| ![][image12] |
| :---- |
| Complete TMT workflow:  |

[image1]: images/samples_tubes.png
[image2]: images/digestion.png
[image3]: images/TMT_formula.png
[image4]: images/mixed_peptides.png
[image5]: images/mass_spectrometer.png
[image6]: images/chromatogram.png
[image7]: images/MS1_spectrum.png
[image8]: images/MS2_spectrum.png
[image9]: images/chromatogram_missing_blue.png
[image10]: images/MS1_spectrum_missing_blue.png
[image11]: images/MS2_spectrum_missing_blue.png
[image12]: images/TMT_workflow.png
