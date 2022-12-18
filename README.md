Sequencing Trait-Associated Mutations (STAM)
============================================
### A straightforward method to identify the gene for a trait with phenotyped mutants
![图1-STAM](https://user-images.githubusercontent.com/7081243/208272511-42718937-3272-4729-9759-3fcd483840e2.jpg)
-----
### 1) de novo transcriptome reconstruction
The raw sequencing reads (PacBio subreads) were processed by ccs to generate polished circular consensus sequence (CCS reads), then used for primer removal and demultiplexing using lima to generate full-length reads; The full-length reads were refined by trimming of PolyA tail and artificial concatemer removal using IsoSeq3 (https://github.com/PacificBiosciences/IsoSeq) and generated full-length, non-concatemer reads (FLNC reads); Two SMRT cells FLNC reads were merged and clustered to generate polished transcript isoforms (HQ transcripts).

-----
### 2) Construction of final transcriptome reference
HQ transcripts were mapped to the genome reference of Pst isolate 134E (DOI:10.1094/MPMI-09-21-0236-A) using Minimap2 (https://github.com/lh3/minimap2) to identify the stripe rust transcripts introduced during sampling and RNA extracting. After removing stripe rust transcripts, transposable elements were masked using Repeatmasker (http://www.repeatmasker.org) based on the repeat library Dfam3.6 (https://www.dfam.org). After filtration and masking, masked HQ transcripts were mapped to wheat Chinese Spring genome reference IWGSC RefSeq v2.1 (https://www.wheatgenome.org) using Minimap2 and generated aligned HQ transcripts. To collapse redundant isoforms caused by 5’ RNA degradation, Cupcake and Cogent (https://github.com/Magdoll) were used to generate unique transcripts for well-mapped reads and unmapped reads, respectively. Then, two parts of unique transcripts were merged to generate the final transcript set using CD-HIT-EST with the sequence identity threshold at 100% (-c 1).

---
### 3) Variant calling
Adapter trimming and quality filtration of RNA-Seq reads of seven Yr10-defective mutants and resistant cultivar Moro were firstly performed using fastp (https://github.com/OpenGene/fastp), and the clean data was then mapped to the final transcript set obtained in the previous step using STAR (https://github.com/alexdobin/STAR). Potential PCR duplicates reads were further removed using Picard (https://broadinstitute.github.io/picard) and generated analysis ready reads. SNPs were identified by the HaplotypeCaller tool of GATK v4.2 in GVCF mode (https://gatk.broadinstitute.org). Then, all the per-sample GVCFs were gathered and passed to GATK GenotypeGVCFs for joint calling. Variants were preliminarily filtered using GATK VariantFiltration with the parameter “DP < 5 || FS > 60.0 || MQ < 40.0 || QD < 2.0” and generated analysis-ready SNPs. 

-----
### 4) Mutation filtering
SNPs that meet all of the following criterias were further retained in the result of final mutations: a. read depth ≥ 5; b. homozygous sites; c. G to A and C to T mutations; d. independent mutations among all seven Yr10-defective mutants; e. No. of SNPs per transcript in each mutant = 1; f. missing rate ≤ 60%. 
