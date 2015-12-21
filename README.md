# PPM1G Project

## Overview

Generate the number of target genes similar to the venn diagram generated for **(Ryan's Figure E)** but including PPM1G and then calculate enrichment of PPM1G at KAP1-Pol II / Pol II-KAP1-7SK target genes. 

The following must be answered:

1. Get # of target genes including PPM1G
2. Get # of target genes of PPM1G at KAP1-Pol II
3. Get # of target genes of PPM1G at Pol II-KAP1-7SK


Steps taken must be to calculate number of target genes containing Pol II summits (-250, +1000) from TSS using bedtools and then find TF overlaps (-250, +250) from the Pol II summit.

    slopBed -i TSS.txt -g hg.19.chrom.sizes -l 250 -r 1000 > TSS-Sloped.bed
    
Find number of Pol II summits at genes

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/TSS-Sloped.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_Pol-II-Chip-N20/Peaks/narrow/Pol-II-Chip.MACS2_summits.bed -u > Pol-II-Summits-atTSS.bed

*Identified 16,576 genes that contain Pol II -250 +1000 from TSS as opposed to Ryan's 17,316 for a difference of 740 genes*

Extend Pol II summits (Pol-II-Summits-atTSS.bed) -250 +250 for further analysis.
    
    slopBed -i /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-atTSS.bed -g /Users/Carlos/Desktop/Projects/PPM1G_Project/hg19.chrom.sizes -b 250 > Pol-II-Summits-slop250.bed
    
Find number of KAP1 overlaps at genes (Pol-II-Summits-slop250.bed)

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-slop250.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_KAP1-Chip/Peaks/narrow/KAP1-Chip.MACS2_peaks.narrowPeak -u > KAP1-targetgenes.bed
    
*Identified 13,513 genes that contain KAP1 -250 +250 from Pol II summits as opposed to Ryan's 13,565 for a difference of 52 genes*

Find number of 7SK overlaps at genes at promoter proximal regions (TSS-Sloped.bed). This requires three individual intersects in the order of: **Hexim, Cdk9, Larp7**.

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/TSS-Sloped.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_Hexim-Chip/Peaks/narrow/Hexim-Chip.MACS2_peaks.narrowPeak -u > Hexim-targetgenes.bed
    
*20,658 vs Ryan's 20,811*

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/TSS-Sloped.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_Cdk9-Chip/Peaks/narrow/Cdk9-Chip.MACS2_peaks.narrowPeak -u > Cdk9-targetgenes.bed
    
*15,830 vs Ryan's 16,189*

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/TSS-Sloped.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_Larp7-Chip/Peaks/narrow/Larp7-Chip.MACS2_peaks.narrowPeak -u > Larp7-targetgenes.bed
    
*18,798 vs Ryan's 19,021*

**7SK**

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/Cdk9-targetgenes.bed -b /Users/Carlos/Desktop/Projects/PPM1G_Project/Hexim-targetgenes.bed -u | intersectBed -a - -b /Users/Carlos/Desktop/Projects/PPM1G_Project/Larp7-targetgenes.bed -u > 7SK-targetgenes.bed
    
*15,512 vs Ryan's 15,850*

Find KEC target genes (all three target gene files)

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/7SK-targetgenes.bed -b /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-slop250.bed -u | intersectBed -a - -b /Users/Carlos/Desktop/Projects/PPM1G_Project/KAP1-targetgenes.bed -u > KEC-targetgenes.bed
    
*13,092 vs Ryan's 12,211*

**Numbers seem to be similar, analysis looks solid so continuing with new analysis**

Find PPM1G target genes (Pol-II-Summits-slop250.bed)

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-slop250.bed -b /Users/Carlos/Dropbox/ChIP-Seq/hg19/old_data/Sample_PPM1G-Chip/Peaks/narrow/PPM1G-ChIp.MACS2_peaks.narrowPeak -u > PPM1G-targetgenes.bed
    
14,488 PPM1G target genes

Find Pol + PPM1G + KAP1 target genes

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-slop250.bed -b /Users/Carlos/Desktop/Projects/PPM1G_Project/PPM1G-targetgenes.bed -u | intersectBed -a - -b /Users/Carlos/Desktop/Projects/PPM1G_Project/KAP1-targetgenes.bed -u > Pol-PPM1G-KAP1-targetgenes.bed
    
13,222 Pol + PPM1G + KAP1 target genes

Find Pol + PPM1G + KAP-7SK (KEC) snRNP target genes

    intersectBed -a /Users/Carlos/Desktop/Projects/PPM1G_Project/Pol-II-Summits-slop250.bed -b /Users/Carlos/Desktop/Projects/PPM1G_Project/PPM1G-targetgenes.bed -u | intersectBed -a - -b /Users/Carlos/Desktop/Projects/PPM1G_Project/KEC-targetgenes.bed -u > Pol-PPM1G-KAP7SK-targetgenes.bed
    
12,277 Pol + PPM1G + KEC target genes
