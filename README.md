# ANEVA-DOT
ANEVA Dosage Outlier Test

Developed by: Pejman Mohammadi (firstname@Scripps.edu), Christina Sousa (sousa.19@osu.edu)

This test is designed to detect if ASE data reveals sufficient imbalance to induce an outlier in total gene expression. The test takes in in two vectors of ASE count data from an R data frame, as well as a vector of population estimates for standard deviations (sample estimates are provided). The output is a data frame containing raw and adjusted p-values using Benjamini-Hoschberg method, and a plot showing outlier data points in red.

To install the R package, you can use the devtools package:

`install.packages("devtools")`

`library(devtools)`

`install_github("PejLab/ANEVA-DOT")`

Once installed, you can type `?ANEVADOT` to view package documentation and `?ANEVADOT_test` to view test function documentation.

Here is an example to get started with:

```r
library(ANEVADOT)

# Define the output columns
output_columns <- c("GENE_ID", "TISSUE_ID",  "REF_COUNT", "ALT_COUNT", "TOTAL_COUNT", "NULL_RATIO")

# re organize the tables by:
# 1: Selecting only genes that have Vg scores available
# 2: Reordering ASE data and Vg scores so they align
tiss <- "MSCLSK" # The data comes from a skeletal muscle sample
covered_genes <- intersect(Vg_GTEx_v7$IDs, sample_ASE$GENE_ID)
covered_gene_Vgs <- Vg_GTEx_v7[match(covered_genes, Vg_GTEx_v7$IDs), tiss] 
covered_gene_ASE_data <- sample_ASE[match(covered_genes, sample_ASE$GENE_ID),]

# Take the square root of the Vg scores to the get the Standard Deviation (SDg)
covered_gene_SDgs <- sqrt(covered_gene_Vgs) 

# Run ANEVA-DOT
ANEVADOT_scores <- ANEVADOT_test(covered_gene_ASE_data, output_columns = output_columns, 
                          eh1 = "REF_COUNT", eh2 = "ALT_COUNT", coverage = 10, 
                          r0 = covered_gene_ASE_data$NULL_RATIO,
                          Eg_std = covered_gene_SDgs, plot = TRUE)
```

## Further notes:
- If you don't know how to **generate ASE data**, you can start [here](https://stephanecastel.wordpress.com/2017/02/15/how-to-generate-ase-data-with-phaser/) and [here](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-015-0762-6).

- You can download our pre-calculated Vg estimates for available datasets at [Datasets/Reference_Vg_Estimates](https://github.com/PejLab/Datasets/tree/master/Reference_Vg_Estimates).

- Consider excluding false positive prone genes based on our summary stats from running ANEVA-DOT on general population available at [Datasets/ANEVA_DOT_frequencies](https://github.com/PejLab/Datasets/tree/master/ANEVA_DOT_frequencies).


BugReports: `https://github.com/PejLab/ANEVA-DOT/issues`
