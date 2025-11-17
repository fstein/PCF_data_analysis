# **From Raw Reporter Intensities to Biological Insight**

## A Gentle Walk‑through of the Statistical Pipeline

*This document accompanies the quantitative data you receive from the Proteomics Core Facility. It focuses on the computational steps that transform reporter‑ion intensities into lists of significantly regulated proteins, heatmaps and GO enrichments.*

---

# Experimental setup

In order to explain the outputs of a typical data analysis, we use an example in which we have three different biological conditions with three replicates each. Besides the WT condition, we also have two knock-out conditions. A single (single_ko) and double knockout (double_ko) condition. Each condition was measured with three biological replicates. In the single knockout, the protein SGPL1 (Sphingosine-1-phosphate lyase 1) was knocked out. In the double knockout condition SGPL1 and another protein was knocked out for which the knockout did not work. All samples were multiplexed using tandem mass tags (TMT) and combined into a single sample. This sample was offline-fractionated and measured in 12 fractions on the mass spectrometer (Thermo Scientific Orbitrap Fusion Lumos Tribrid Mass Spectrometer).

# Pre data analysis steps

The mass-spectrometer produced raw files which contain the information of all acquired spectra (MS1 and MS2 spectra). These raw files were analyzed with FragPipe which ultimately exported one protein.tsv (tab-delimited text) file for the TMT set. This protein.tsv file was the input for the follow-up data analysis. In order to facilitate reproducible and transparent research, an R Markdown script is provided which contains all steps to enable you to reproduce the complete analysis on your own and enable you to change things as necessary. The script uses a metadata.csv file to annotate the different TMT labels to biological samples and annotates biological conditions and replicate information. Every figure and table lands inside the automatically created folder 'data_analysis_results_V1' alongside a frozen Workspace_V1.RData. Opening that RData file re‑creates the entire analysis environment, allowing advanced users to reproduce or tweak any step.

# Data analysis

## Reading and filtering protein files

The analysis script reads every file listed in `metadata.csv` and stacks them into one long table, discarding lines marked as `reverse` hits (decoy‑database matches) or `contaminants`. Only proteins supported by at least **two razor peptides** are kept. This first filter dramatically reduces false identifications. Please note that FragPipe uses the occam's razor algorithm for the protein inference step. This means that shared peptides are always annotated to only one protein with the highest number of evidence (very often this is the protein with the highest number of unique peptides).

## Protein identification overview

Figure 1 gives an overview over the number of identified proteins (after filtering for 2 razor peptides) for each sample. Since all samples in this experiment were multiplexed with TMT, the number of identified proteins is exactly the same. This is because the identification of a peptide from an MS2 spectrum happens independently of the absence or presence of a peptide in any individual sample as the MS2 spectrum is triggered if the MS1 precursor intensity was high enough. As this intensity is an accumulated intensity over all samples of a peptide, identification happens even if a peptide was missing completely in some samples.

| ![][image1] |
| :---- |
| Figure 1: ProteinIdentification_CountOverview_V1.pdf file showing a bar plot representation of number of identified proteins for each sample. |

Figure 2 gives more details on which proteins have been identified in which samples in the form of an Upset plot. In such a plot, all samples are listed in the different rows. On the right side, a bar indicates the protein number for a specific combination of identifications. When the black circle is filled, it means that this sample was identified in this combination.

| ![][image2] |
| :---- |
| Figure 2: ProteinIdentification_UpSet_plot_V1.pdf file showing an upset plot. |

In this particular example, all 8149 proteins have quantitative values for all 9 samples.

In case the identifications would be more diverse, the analysis also provides a heatmap (Figure 3) to explore the identification or missingness pattern.

| ![][image3] |
| :---- |
| Figure 3: ProteinIdentification_MissingValueOverview_V1.pdf file showing a heatmap with missing value pattern. |

All proteins are listed here in the different rows and green indicates a measured quantitative value and grey would indicate a missing value. It could also happen that a peptide was identified via an MS2 spectrum but the quantified TMT intensity for one sample was so low, that the quantification would result in a zero intensity. This case would also be reported as a missing value.

## Data transformation steps

### Log2 transformation

TMT intensities are log2 transformed which results in a normal distribution of these values. Due to this characteristic, classical statistical methods can be applied.

### Cleaning technical batch effects

Even with care, subtle day‑to‑day differences in chromatography or instrument tuning can bias reporter intensities. The script therefore applies the removeBatchEffect function from the limma package, using the replicate identifier as a batch covariate.

### Variance‑stabilising normalisation (VSN)

Reporter intensities span several orders of magnitude. VSN rescales them so that technical variance becomes independent of signal size. Furthermore, the TMT intensity distributions are centered around the same median. Both points are pre‑conditions for valid p‑values.These values are the starting point for every statistical test that follows.

### Imputation

With bigger TMT sets or other methods such as label-free quantification or data independent analysis (DIA) missing values are typically observed. There are two types of missing values observed. Missing at random is the typical observed missing value pattern when measuring replicates. Picking a precursor in the MS1 spectrum for fragmentation is always depending on the intensity of the co-eluting ions and therefore a stochastic process. For these cases missing values would be imputed by similarity (e.g. by using the k-nearest neighbor (knn) method). The script would impute missing values with the knn method when a protein was identified in at least two thirds of the replicates (of a single condition for label-free or DIA data).

Another observed case would be missing at low abundance, e.g. if a protein is missing completely in one condition and observed in another condition. In this case, the abundance of the missing protein needs to be imputed with a low value. In case a protein was identified in less than two thirds of the replicates in a single condition, missing values are imputed with a method that estimates low abundance e.g. by imputing from left-censored distribution.

### Setting every sample relative to its own control

Many experiments revolve around a 'treated‑versus‑control' contrast. The script therefore divides each normalised intensity by the median of its corresponding control channels, then takes log₂. This generates the ctrl.ratio columns

## Inspecting the effects of data transformation

### Boxplot of TMT reporter ion intensities

Figure 4 shows a box plot representation of the log2 TMT reporter ion intensities for each sample and how the data transformation step influenced the distributions.

| ![][image4] |
| :---- |
| Figure 4: Normalization_overview_V1.pdf file showing boxplot of protein abundances for each data transformation step. |

On the left side with the heading "raw_reporter_intensity" the distribution of the original TMT intensities is shown. The height of the box directly correlates with the protein abundance in the respective sample.

In this particular example, one could clearly see a batch-effect. The protein abundance increases from replicate 1 to replicate 3 in a similar way for each condition. This effect was removed after cleaning for batch-effects and the impact of this effect can be estimated in the middle with the heading "batchcl_reporter_intensity".

Finally, the TMT distributions of all samples were normalized using variance stabilization normalization resulting in the box plot on the right under the heading "norm_reporter_intensity". Here one can clearly see that the median of each sample is nicely aligned.

In case an imputation strategy would have been applied on the data, this would be visible as an additional subplot.

Figure 5 shows a box plot overview of the control ratios.

| ![][image5] |
| :---- |
| Figure 5: Normalization_overview_ratios_V1.pdf showing boxplot representation of control ratios. |

The side‑by‑side boxplots confirm that the median of every control distribution centres at zero.

The data transformations have been performed on the population level. In order to be able to estimate their effects on the single protein level, a all_proteins_V1.pdf was produced. This number of pages of this pdf equals the number of filtered proteins in the data set. Figure 6 shows the respective page for SGPL1, the protein which was knocked out.

| ![][image6] |
| :---- |
| Figure 6: all_proteins_V1.pdf file showing abundance level of each identified protein for each data transformation step. |

The plot mirrors the boxplot normalization overview and shows the effect of each data transformation step on the TMT reporter ion intensity for the respective protein starting at the top with the "raw_reporter_intensity" to the bottom "norm_reporter_intensity" and the "ctrl.ratio". The conditions are indicated by different colours and replicate information is encoded into the shape of the data points. In this particular example, one could see how the variance decreases with every step. Also the decrease in the knock-out conditions is visible.

For downstream statistics a single line per protein is required. Reporter intensities belonging to identical proteins are summed, and duplicate Gene symbols are resolved automatically. The result is written to "Full_dataset_V1.csv" – an Excel‑friendly spreadsheet where each column corresponds to one TMT channel and each row to a protein. The batch‑corrected intensities are stored in the wide spreadsheet with the prefix "batchcl_reporter_intensity_",  the normalised values with the prefix "norm_reporter_intensity_" and the control ratios with the prefix "ctrl.ratio_".

### PCA analysis

A principal component analysis is performed to judge the similarity of samples and the effect of the various data transformation steps on the raw data. Figure 7 shows a PCA plot for each of the steps that visualizes how samples cluster at each step.

| ![][image7] |
| :---- |
| Figure 7: PCA_analysis_V1.pdf file showing results of a principal component analysis (PCA) for each data transformation step. |

The subheading indicates how much variability is explained with each principal component (PC) giving a hint about the weight of each axis. The closer the points cluster together, the higher the similarity of protein abundances. On the left, the biggest variability was observed by the batch (replicate) and the second principal component (only 3.1 % of the variability is  explained) could be neglected. After removing batch-effects, samples cluster already much better by colour which is even increased after normalization.
The individual principal components are also saved in the table "PCA_analysis_data_V1.csv".

### CV calculation

The coefficient of variation (CV) gives an idea about the reproducibility of the signal across samples. It is defined as the ratio between the standard deviation to the mean. Figure 8 shows the CV in % for each data transformation step as a violin plot with a slimmed boxplot in the middle of each distribution.

| ![][image8] |
| :---- |
| Figure 8: CV_overview_V1.pdf file showing violin plot with coefficients of variations (CVs) for each data transformation step. |

The x-axis shows the different biological conditions as well as "all" which shows a combined CV after mixing all conditions into a single one. The biggest impact was observed after the batch-effect removal.

## Differential abundance analysis

The heart of the pipeline is an **empirical Bayes moderated t‑test from the limma package** that compares predefined contrasts (e.g. `double‑KO vs WT`). The model accounts for biological replicates and controls false‑discovery rate across thousands of proteins simultaneously. The t-test is moderated as it does not only take into account the standard deviation of a particular protein and the tested condition, but also the variability in the whole data set. With this, limma can make much better estimates about the probability of the observed effect.

### Volcano plot

Figure 9 shows a volcano plot for the various statistical tests.

| ![][image9] |
| :---- |
| Figure 9: Volcano_plot_V1.pdf file. |

A volcano-plot displays the fold-change on the x-axis against the negative log10 of the pvalue on the yaxis.In the example "single_ko vs wt", proteins on the right side of the plot are more abundant in the single_ko condition and proteins on the left side are more abundant in the wt condition. A statistical test could be seen as calculating a ratio (single_ko / wt) and giving each observed ratio a probability to be observed if one would expect no difference. Proteins are annotated according to their statistical significance and fold-change into three classes: hits, candidates and no hits. They are labelled **hit** if the false discovery rate (FDR) ≤ 0.05 and |log₂FC| ≥ 1.0 (two‑fold); **candidate** if FDR ≤ 0.2 and |log₂FC| ≥ 0.58 (1.5‑fold). These thresholds are also printed in the lower right corner of the plot.

The comparison "(double_ko vs wt) against (single_ko vs wt)" is a statistical comparison of two ratios (double_ko / wt and single_ko / wt). As the wt condition appears two times in the denominator it cancels out mathematically and essentially the ratio of ratio comparison boils down to double_ko vs single_ko. Therefore, this volcano plot looks identical to the volcano plot of "double_ko vs single_ko".

### Fold-change correlation plot

Since comparisons of two ratios are difficult to imagine, the analysis also added a correlation plot of these fold-changes. Figure 10 shows such a correlation plot.

| ![][image10] |
| :---- |
| Figure 10: Fold_change_correlation_V1.pdf file. |

Now the two ratios of the are plotted on the different axis on the plot. The hit annotation is identical to the one shown in the volcano plot. The fold-change shown in the volcano-plot corresponds to the distance to the diagonal in this correlation plot. Such a correlation plot makes it much easier to compare the effect of the two ko conditions compared to the wt condition. Proteins on the diagonal show a similar change between the two ratios. Proteins on the different axis show a specific effect with only one ko condition. In this particular example, one could observe even an anti-correlation. When a protein is upregulated in one ko condition, it would be downregulated in the other ko condition.

Figure 11 shows the same fold-change correlation plot. However, now the proteins are coloured by the observed hit annotation for each comparison separately.

| ![][image11] |
| :---- |
| Figure 11: Fold_change_correlation_alt_hit_class_V1.pdf file. |

For example the purple dots would show a significant difference in the single_ko vs wt comparison as well as in the double_ko vs wt comparison. On the lower left, one could observe SGPL1, the protein which was knocked-down in both ko conditions.

### MA-plot

Figure 12 shows an MA plot that displays the log2 of the fold-change on the y-axis vs the average TMT reporter ion intensity on the x-axis.

| ![][image12] |
| :---- |
| Figure 12: MA_plot_V1.pdf file. |

The MA-plot enables checking for dependencies of the fold-changes on the protein abundances. A protein on the right side is more abundant compared to a protein on the left side of the plot. MA-plots are particularly helpful for IP experiments. Here a protein with a high abundance might also have a high pulldown efficiency. Therefore, one could potentially use the observed intensity of a protein also as a filtering criterion in case the analysis would reveal too many significantly regulated proteins.

### t-value vs fdr

A good way to see how well the statistical test was able to identify regulated proteins is the plot below. It shows the absolute t-value on the x-axis and the corresponding false discovery rate on the y-axis.

| ![][image13] |
| :---- |
| Figure 13: t_vs_fdr_limma_vs_fdrtool_V1.pdf file. |

The t-value is an output of the limma analysis and is an effect size telling how many standard deviations a fold-change lies above the mean fold-change. The quicker the curve approaches zero, the easier it was to distinguish significant proteins in the test. Limma uses the t-values to estimate the fdr. In this process limma assumes that the t-value distribution follows a normal distribution. However, this might not always be the case, especially for ratio of ratio comparisons. Therefore, also an alternative method is used to estimate the fdr from the t-value outputs which is a package called 'fdrtool'. The 'fdrtool' package renormalizes the t-value distribution and does the fdr estimation again. Therefore, it's fdr estimates might are sometimes better suitable to identify significantly changing proteins. In this particular case, the blue curve (limma) approaches the zero fdr line quicker than the orange (fdrtool) curve and limma gives lower fdr estimates for a given t-value. Both fdr estimates of limma and fdrtool are reported in the limma_results output csv. The analysis would use the limma estimate for normal comparisons and the decides to use either fdrtool or limma fdr estimates for ratio of ratio comparisons depending on the method with the higher number of hits.

### p-value histogram

It might also be very informative to have a look at the p-value histogram of a statistical test as shown Figure 14.

| ![][image14] |
| :---- |
| Figure 14: p-value_histogram_limma_vs_fdrtool_V1.pdf file. |

The following blog post gives a nice overview of the different learnings one can get when looking at these histograms: http://varianceexplained.org/statistics/interpreting-pvalue-histogram/

## Heatmaps

Figure 15 shows a heatmap of all proteins that have been classified as either hit or candidate in the differential abundance analysis. The heatmap displays the median 'ctrl-ratio' which has been calculated earlier as the fold-change of each observed normalized TMT reporter ion intensity divided by the median value in the control condition (in this case the 'wt' condition).

| ![][image15] |
| :---- |
| Figure 15: Heatmap_hits_165_proteins_V1.pdf |

The heatmap groups proteins with a similar abundance pattern and displays them in close proximity. When zooming into the plot, it is possible to read the Gene names at the y-axis. In order to also cluster the proteins into different groups and identify which proteins behave similarly, the first step is to identify the number of clustering groups.

A very simple helper plot to identify the optimal number of clusters is the Elbow plot displayed in Figure 16. Here the unexplained variability (y-axis) in the data set is visualized for different choices of the clustering number (x-axis).

| ![][image16] |
| :---- |
| Figure 16: Clustering_Elbow_plot_165_proteins_V1.pdf file. |

It's called 'elbow'-plot as it tries to identify the elbow point when an increase in the number of clusters does not lead to a big reduction of the unexplained variability.

Once the preferred number of clusters is identified, two clustering algorithms are used to cluster the proteins displayed in the heatmap. These algorithms are either hierarchical clustering ('hclust') or k-means clustering ('kmeans').

Figure 17 shows a PCA plot in which all proteins from the heatmap are coloured by their cluster for the two clustering methods separately.

| ![][image17] |
| :---- |
| Figure 17: PCA_clustering_data_10_cluster_165_proteins_V1.pdf file. |

It is also possible to zoom into this plot as each dot is labeled by their Gene name. One can also see how the two different clustering methods make different choices for certain proteins and one method might be more suitable for certain applications then the other.

For each method a separate heatmap is plotted. Figure 18 shows the heatmap with the kmeans clustering output.

| ![][image18] |
| :---- |
| Figure 18: Clustering_heatmap_hits_kmeans_10_cluster_165_proteins_V1.pdf |

The heatmap not not only close similar proteins close to each other but also the clustering group. The clustering group for each protein is also summarizes in the 'clustering_groups' csv file.

For longitudinal data it might be helpful to display the clustered data also in a line plot. Figure 19 shows this line plot for data clustered by the kmeans algorithm.

| ![][image19] |
| :---- |
| Figure 19: Clustering_line_plot_kmeans_10_cluster_165_proteins_V1.pdf file. |

The blue line is the average of the cluster and the grey zone the standard error. Each cluster member is also displayed as a grey line. The subheading indicate the clustering group.

## GO term analysis

In order to foster biological interpretation of the data, a GO analysis is performed on multiple levels. Each GO analysis is performed for cellular compartment (CC), molecular function (MF) and biological process (BP). All results which are shown in the various plots are also listed in specific csv spreadsheet outputs with details about the involved Genes, the fdr and other metrics.

### GO for differential abundance analysis.

Figure 20 shows a GO analysis for the differential abundance analysis for the GO ontology molecular function. All hit and candidate proteins from each statistical test of the limma analysis are grouped into upregulated or downregulated proteins. The GO enrichment analysis is performed on each group using the clusterProfiler R package.

| ![][image20] |
| :---- |
| Figure 20: GO_enrichment_DE_MF_dotplot_limma_results_V1.pdf file. |

The dotplot indicates a significant upregulation of a specific GO category displayed in the y-axis. The colour indicates the significance and their **size the** odds ratio. This is ratio is the Gene ratio (e.g. 5/15 if 5 proteins of a group of 15 proteins belong to the respective GO term) divided by the background ratio (e.g. 50 / 6000 50 proteins out of all 6000 proteins in the data set belong to the respective GO term) and indicates how much the GO term is enriched in the specific group over background.

### GO for clustering results

Figure 21 gives an overview of the enriched GO terms for the specific clustering groups from kmeans clustering.

| ![][image21] |
| :---- |
| Figure 21: GO_enrichment_cluster_MF_dotplot_kmeans_clustering_V1.pdf file. |

The dotplot has a similar structure as explained for Figure 20, except that the clustering group is displayed at the x-axis. If a particular clustering group is missing (e.g. cluster 5), means that there are not significant GO terms for proteins in this group.

**Files produced at this stage**
 • `ProteinIdentification_CountOverview_V1.pdf` – a bar plot showing how many proteins were detected in each replicate.
 • `ProteinIdentification_MissingValueOverview_V1.pdf` – a heat‑map that highlights missing values across samples.
 Both PDFs let you verify that each channel contributed a comparable number of identifications.

### 7  Visualising global structure: PCA and clustering

Principal‑component analysis reduces thousands of proteins to two axes that explain most variance. The PCA plot mentioned earlier lets you judge whether replicates cluster by biology rather than by batch.

For a higher‑resolution view, all `hit` and `candidate` proteins are median‑collapsed per condition and subjected to both **hierarchical** and **k‑means** clustering. The optimal number of clusters is chosen by the `Elbow` method (`Clustering_Elbow_plot_…pdf`). Results are saved as:

• `Heatmap_hits_hclust_…pdf` and `Heatmap_hits_kmeans_…pdf` – red/blue tiles that show how each cluster behaves across conditions.
 • `Clustering_line_plot_…pdf` – smoothed trend lines for every cluster.
 • `Cluster_results_…csv` – a lookup table that assigns every protein to a hclust and k‑means cluster.

---

### 8  Functional annotation by Gene Ontology (GO)

Lists of regulated proteins are informative, yet their biological meaning often lies in coordinated pathways. The script uses **clusterProfiler** to run over‑representation analysis against the Gene Ontology universe.

Separate dot plots are generated for the three GO sub‑ontologies:

• `GO_enrichment_DE_CC_dotplot_…pdf` – Cellular Component.
 • `GO_enrichment_DE_MF_dotplot_…pdf` – Molecular Function.
 • `GO_enrichment_DE_BP_dotplot_…pdf` – Biological Process.

Analogous plots (`GO_enrichment_cluster_` …) summarise enrichment per expression cluster. Each dot's colour encodes statistical significance (‑log₁₀ p‑value) while its size reflects the odds ratio of enrichment.

---

### 10  Interpreting the outputs

- **CSV tables** hold the numerical answers (intensities, fold‑changes, cluster memberships). You can load them into Excel or any statistics package.

- **PDF figures** provide intuitive quality checks and graphical summaries – the quickest way to convince yourself (or reviewers) that the data are sound.

- **Methods_V1.txt** is a plain‑text narrative of the parameters and software versions used, ready to copy‑paste into the Methods section of a manuscript.

Taken together these artefacts turn raw reporter intensities into a transparent, statistically robust map from peptide ions to biological pathways. If any step feels opaque, the core facility team is happy to walk you through the corresponding figure or code snippet.

[image1]: images/image21.png
[image2]: images/image1.png
[image3]: images/image2.png
[image4]: images/image15.png
[image5]: images/image16.png
[image6]: images/image3.png
[image7]: images/image20.png
[image8]: images/image12.png
[image9]: images/image7.png
[image10]: images/image18.png
[image11]: images/image6.png
[image12]: images/image8.png
[image13]: images/image11.png
[image14]: images/image17.png
[image15]: images/image19.png
[image16]: images/image5.png
[image17]: images/image4.png
[image18]: images/image9.png
[image19]: images/image10.png
[image20]: images/image14.png
[image21]: images/image13.png

