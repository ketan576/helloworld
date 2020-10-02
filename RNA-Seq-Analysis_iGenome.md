Bash, on clusters
-----------------

Download Mus Musculus genome from iGenome using wget, and then unzip
(reference:
<a href="https://www.interserver.net/tips/kb/extract-tar-gz-files-using-linux-command-line/" class="uri">https://www.interserver.net/tips/kb/extract-tar-gz-files-using-linux-command-line/</a>)

Run STAR genome index generator to create genome indices Warning: uses
high RAM sjdbOverhang = sequence length - 1 -n 1 -c 5 -m 24g Warning:
module purge at first, so that you can use STAR module

FIXME: look for the newest conda version of STAR

Align reads Check the input (zipped or not!) and output!! Time: \~4min
for one sample Output size: \~2GB module purge

Count features Apparently, the gtf file downloaded from iGenome is not
compatible with featureCounts, fixed the format (reference:
<a href="https://www.biostars.org/p/173233/" class="uri">https://www.biostars.org/p/173233/</a>)

FIXME: module purge, but you need conda here…

Time: \~20 seconds for one sample You don’t need clusters here, but pay
attention to threads s=0 is best here (Sarah’s criteria, but why?)

Clean up featurecount matrix for R plotting and manually remove the
information at the beginning, and change header of count… there are 448
more lines than Malay’s analysis due to transcript\_id

Comparison, using R

``` r
for (i in 8:9){
  KeArray = read.delim(paste0('/Users/tanke/RStudio/CleanFeatureCounts_Sample',i,'_S0_fixed.txt'),header = TRUE, sep = '', dec = '.')
  MalayArray = read.delim(paste0('/Users/tanke/RStudio/CleanCounts_MalayRSEM_MouseAlignment_Sample',i,'.txt'),header = TRUE, sep = '', dec = '.')
  InnerJoin <- dplyr::inner_join(KeArray,MalayArray,by='Geneid')
  KeCount = InnerJoin[,2]
  MalayCount = InnerJoin[,3]
  library("ggpubr")
  print(ggscatter(InnerJoin, x = "count.x", y = "count.y", 
          add = "reg.line", 
          add.params = list(color = "black", fill = "lightgray", linetype = 3),
          conf.int = TRUE, 
          cor.coef = TRUE, cor.method = "pearson",
          cor.coef.size = 5,
          xlab = "Analysis1_Ke", ylab = "Analysis2_Malay",
          title = 'RNASeq Analysis Comparison'))
}
```

    ## Loading required package: ggplot2

    ## `geom_smooth()` using formula 'y ~ x'

<img src="RNA-Seq-Analysis_iGenome_files/figure-markdown_github/unnamed-chunk-6-1.png" width="50" height="50" />

    ## `geom_smooth()` using formula 'y ~ x'

<img src="RNA-Seq-Analysis_iGenome_files/figure-markdown_github/unnamed-chunk-6-2.png" width="50" height="50" />
