
<!-- README.md is generated from README.Rmd. Please edit that file -->

# TimeSeriesAnalysis

<!-- badges: start -->
<!-- badges: end -->

TiSA: TimeSeriesAnalysis - A transcriptomic analysis tool for both RNA
sequencing and microarray data

## Overview

TimeSeriesAnalysis (TiSA) is an analysis and visualization package for
RNAseq and microarray data. TS extracts significant genes from time
course transcriptomic data by performing differential gene expression on
both the conditional and temporal axes. It then employs partitioning
algorithm based on recursive thresholding (PART) clustering to identify
small genomic clusters of relevance, followed by running the clusters
through gprofiler to reveal the biological relevance of each cluster.

## TS performs:

- Data normalization and processing
- PCA plots
- Differential gene expression (conditional and temporal)
- PART clustering
- Heatmaps for both differential expression summary and clustering
  results
- Trajectory of identified clusters
- Gprofiler (functional enrichment) analysis of clusters
- Dotplots and MDS plots of Gprofiler results
- Nearest ancestor clustering of GOs
- GO ancestor queries

## Installation

You can install the stable version of TimeSeriesAnalysis like so:

``` r
install.packages("devtools")
devtools::install_github("Ylefol/TimeSeriesAnalysis@master")
```

The development version can be installed by replacing ‘master’ with
‘dev’.

Devtools is required for the installation of this package. To install
Devtools, visit the [Devtools main
page](https://www.r-project.org/nosvn/pandoc/devtools.html)

Certain bioconductor packages will have to be installed before
installation of TimeSeriesAnalysis. The code snippet below gives the
method along with some of the packages that should be installed via this
method.

``` r
if (!require("BiocManager", quietly = TRUE))
  install.packages("BiocManager")
bio_pkgs <- c('DESeq2','GOSemSim','GO.db','limma','ComplexHeatmap',
'AnnotationDbi','GenomeInfoDb','GenomicRanges','Biobase','S4Vectors',
'BioCGenerics','MatrixGenerics','IRanges','BiocFileCache'
'SummarizedExperiment','org.Hs.eg.db','org.Mm.eg.db','org.Ce.eg.db')
BiocManager::install(bio_pkgs)
```

## Rmarkdown format

TimeSeriesAnalysis was developed with user-friendliness in mind, the
core script of the package is a Rmarkdown file with explanations of what
each code block performs. Computationally intensive code blocks save
their work in order to avoid loss of computation time.
TimeSeriesAnalysis comes with a test dataset. Users are recommended to
first run the package with the test data to then modify the Rmarkdown
script with the necessary information for their purposes (input files,
organism, genes of interest etc…). The rmarkdown script and the expected
result from an example run can be found and downloaded from this
repository, they are located in the ‘rmarkdown_method’ folder. If users
prefer a script approach, the equivalent of the rmarkdown report is
provided in two scripts, one for the computation tasks and a second for
the analyses. These two scripts are found in the ‘script_method’ folder.

## Example Data

Three different datasets have been used to test this pipeline, one of
which has been preserved as an example datasets for the vignettes of
this pipeline. More information on the other datasets can be viewed
through the publications listed below.

The PBMC dataset is a time series experiment with three time points that
explores and compares three AICDA/AID (Activation Induced Cytidine
DeAminase) activation cocktails. The experiment seeked to identify which
activation cocktail properly activated AID through both the expression
of the AID gene and the activation of class switch recombination.

The MURINE dataset is another AID related time series experiment, where
mice were generated and one was given an AID activation cocktail. The
experiment contains one replicate per condition and 10 timepoints. Due
to only having one replicate per condition per timepoint, subsequent
timepoints were combined to create an intermediary timepoint with two
replicates in each condition.

The Celegans dataset is an ageing related experiment. Two celegans
models were used: BY372 and N2. The first being a parkinsons model and
the second being a wild type. Each model was split into two groups, one
that would be fed krill oil and the other not (control). Each experiment
has three timepoints: Days 1, 3, and 6.

## Tutorial

A tutorial using the PBMC data can be found within the [pipelines
website](https://ylefol.github.io/TimeSeriesAnalysis/). Additionally the
documentation can be found in the references tab.

### Set-up of a sample file

In order for the pipeline to run properly, a sample file must be
provided. Certain requirements ust be met. Column names should be as
indicated below, all spaces should be avoided, timepoints should be
numerical. It is also important that the sample column contains the same
names as the count files provided. Lastly, replicates should always be
followed by a ’\_#’ as this numbering will be utilized for the
organization of the replicates within certain visualizations. Note that
in the below example the three files have the same name (ex: C_1). That
is to represent the ‘C_1’ sample at its three different time points.

| sample | group   | replicate | timepoint |
|:-------|:--------|:----------|----------:|
| s-37   | control | C_1       |         1 |
| s-38   | control | C_1       |         3 |
| s-39   | control | C_1       |         9 |
| s-52   | IgM     | IgM_1     |         1 |
| s-53   | IgM     | IgM_1     |         3 |
| s-54   | IgM     | IgM_1     |         9 |
| s-77   | control | C_2       |         1 |
| s-78   | control | C_2       |         3 |
| s-79   | control | C_2       |         9 |
| s-92   | IgM     | IgM_2     |         1 |
| s-93   | IgM     | IgM_2     |         3 |
| s-94   | IgM     | IgM_2     |         9 |

### RNAseq based data

TiSA utilizes DESeq2 to process RNAseq data. It therefore requires that
the data be in a raw count format. The pipeline can take in multiple
count files within a folder provided that the file names match the
sample names given in the sample file. These individual files should be
tab delineated, contain only two columns (gene ID and associated count),
there should be no column header. Additionally each file should end with
a ‘.counts’.

As an alternative, all counts may be within the same text file. In this
case the first column should be labeled as ‘gene_id’, and each
subsequent column should contain the same sample name as the names found
in the sample file. This file should end with ‘.txt’.

In either case, the given information should be counts (round numbers).

## Microarray based data

For microarray data, a streamlined method is in the works. Currently
microarray data needs to be inputed as a Elist, specifically an E list
saved as a rds object.

``` r
my_path_data<-'data/micro_arr/my_limma_dta.rds'
my_path_sample_dta<-'data/micro_arr/sample_file.csv'

#Set-up time series object parameters
diff_exp_type<-'limma' 
```

The rds file given to ‘my_path_data’ contains the Elist produced by
limma processing of microarray data. It is also important to set the
differential gene expression type (diff_exp_type) to ‘limma’.

## Using the pipeline

NOTE: You must still install the TimeSeriesAnalysis package as defined
above

Since the pipeline’s source code is available, individuals are free to
take the code and adapt it as needed. However for users who would like
the easiest approach to use the pipeline as is, it is recommended to
clone or download this github repository to your local computer and work
from the Rmarkdown method folder within the repository. Cloning or
downloading can be done from the [TimeSeriesAnalysis github
page](https://github.com/Ylefol/TimeSeriesAnalysis) by using the green
button called ‘code’. From there, the Rmarkdown file can be edited.

To edit the file, it is recommended to use rstudio, which can be
downloaded [here](https://www.rstudio.com/products/rstudio/download/).
Note that it is recommended to download the free version of RStudio
Desktop, not RStudio Server.

The only code block of code is the parameter set-up block (2nd) as well
as the title at the very top of the Rmarkdown document.

## Launching the pipeline

The pipeline can be launched from rstudio itself by using the ‘knit’
button, or it can be launched from a command line using the following
command from the TimeSeriesAnalysis repository:

``` bash
Rscript -e "rmarkdown::render('rmarkdown_method/TS_analysis.Rmd',output_file='TS_analysis.html')"
```

## Publication

A manuscript detailing the TimeSeriesAnalysis pipeline published with
NAR genomics and bioinformatics. [TiSA: TimeSeriesAnalysis—a pipeline
for the analysis of longitudinal transcriptomics
data](https://academic.oup.com/nargab/article/5/1/lqad020/7069286) The
version/DOI of this pipeline used within the manuscript is
10.5281/zenodo.7616032.

A ageing related study was performed in part through the use of TiSA.
[Krill oil protects dopaminergic neurons from age-related degeneration
through temporal transcriptome rewiring and suppression of several
hallmarks of aging](https://www.aging-us.com/article/204375/text)

A longitudinal COVID-19 study was performed using TiSA. [Longitudinal
whole blood transcriptomic analysis characterizes neutrophil activation
and interferon signaling in moderate and severe
COVID-19](https://www.nature.com/articles/s41598-023-37606-y)

## Additional information

The ‘clusterGenomics’ package was no longer maintained on CRAN and
therefore the necessary scripts were brought over to this package for
it’s implementation without the need to download the clusterGenomics
package from source. Within this package, in the ‘R/clusterGenomics.R’
code was written by the authors of clusterGenomics. It was imported to
TimeSeriesAnalysis by the authors of TimeSeriesAnalysis.

## Contact information

If you have encountered an error or require assistance running the
pipeline, feel free to open a github issue, or email me at
<yohanl@medisin.uio.no>
