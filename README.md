# d2SBin
d2sBin is easy-to-use contig-binning improving tool, which adjusted the contigs among bins based on the output of any existing binning tools. The tool is taxonomy-free only on the k-tuples for single metagenomic sample.
d2sBin is based on the mechanism that relative sequence compositions are similar across different regions of the same genome, but differ between genomes. Current tools generally used the normalized frequency of k-tuple directly, which actually is the absolute instead of relative sequence composition. Therefore, we attempted to model the relative sequence composition and to measure the dissimilarity between contigs with d_2^S. We applied d2sBin to adjust the outputs of five widely-used contig-binning tools on six datasets. The experiments showed that d2sBin can improve the contig binning performance significantly. 
The d2sBin pipeline was developed with Python and run on the Unix and Linux platform, and the detail description of running is provided in the manual. The demo of running on the testing dataset is provided here.

# Version Release Notes
Version XXX
This is the first version of d2sBin pipeline. (Source code Download )
An demo of d2sBin running is given here.
Development Team
The whole source code was developed by Ying Wang's group, Automation Department, Xiamen University, P.R.China. All the suggestions and questions are welcome to wangying AT xmu.edu.cn.
 
# Package installation and configuration
	         Pre-install running environment
	     Unix or Linux operating system.
	     Python 2.7 or above.
	         Detailed steps
	     Download the source code to your directory,e.g: ’/home/user/d2SBin’.
	     Enter your specified directory:  bash-3.2$ cd /home/user/d2SBin
	     Extract the tar file:  bash-3.2$ tar -xvf d2SBin_SourceCode.tar.gz
	     Enter the directory:  bash-3.2$ cd /home/user/d2SBin/d2SBin_SourceCode
	     If your operating system has multiple Python version , please be sure your Python version at least 2.7 or above.
# Running of d2sBin Pipeline
(1) Input: The output of existing contig-binning tools.
The current output of contig-binning have the following two formats:
Format1: fasta files with contigs sequence from the same bins.
For example, myout.001.fasta
>contig-1.0
GACACTTTTAGTGGGCGTAAACTTCATTTACTCTGTTTTTTGCTTATTGCAAATAATCCCTATC
AGCCATACTTCTAGTGGATCT
>contig-1.1
CCATGTCAGAAGAAGTTGGTAGAAAGCCTATTTATGTGGTCACCTTATTTATTGCAGTGGTAT
TCCTTGTGGCGCCGCTAAAAACATCGCCACATTAATTGTTTGTCGTTTGATCGA
…
Format2: fasta files only with contig name from the same bins.
For example, myout.001.fasta
>contig-1.0
>contig-1.1
…
d2sBin is compatible to the two formats as the following options and commands: 

         Options	Arguments	Meaning
-s/--inputList_withSeq	 .txt	List of path and file name of contig files with sequence( Format1).
-c/--contig_Seq	 .fasta/.fastq	The original contig sequences file.
-n/--inputList_noSeq	.txt	List of path and name of files with contig name ( Format2).
-k/--kofKTuple	integer	the value k of KTuple
-r/--order	integer	the order of markov model(0,1,2,3,k-2)
-o/ --output	output Path	The output path of d2SBin results

>ls /home/…/input file path/*.fasta > input_list.txt

>python ./ d2SBin.py --inputList_withSeq input_list.txt -k 6 -r 0 --output ../data/output /
> python ./d2SBin.py --inputList_noSeq input_list.txt --contig_Seq contigs_c10k_f1K.fasta -k 6 -r 0 --output ../data/output
(2)Output : d2sBin also has the output of two formats: 
Format 1: .txt file with contig name and binning result label. eg. d2SBin.out.k6.r0.txt
contig-1.1 ,3
contig-1.2,0
Format 2: fasta files with sequence from same bins. eg. d2SBin.k6.r0.out0.fasta
>contig-1.0
GACACTTTTAGTGGGCGTAAACTTCATTTACTCTGTTTTTTGCTTATTGCAAATAATCCC
AGCCATACTTCTAGTGGATCT
>contig-1.1
CCATGTCAGAAGAAGTTGGTAGAAAGCCTATTTATGTGGTCACCTTATTTATTGCAGTG
TCCTTGTGGCGCCGCTAAAAACATCGCCACATTAATTGTTTGTCGTTTGATCGA

# The demo of d2sBin on testing dataset is available here. 

One demo of d2sBin
Dataset1: 10 genomes: 20×  The dataset was generated by MaxBin1.0[].For the 10 genomes metagenomes, the 5 millian paired-end reads were samples as 20×average coverage. The short reads were simulated by MetaSim and assembled to contigs by Velvet. 

1. The assembled contigs 20x.fasta were binned by any contig-binnning tools, such as MaxBin or MetaCluster3.0 as followed commands.
	> perl run_MaxBin.pl -contig 20x.fasta -abund 20x.abund -out myout
> ./bin/metaCluster 20x.fasta

The contig-binning results from Maxbin is myout.XXX.fasta. 
The contig-binning results from MetaCluster3.0 is 20x.fasta-outXXX.fa.
XX are numbers, e.g. myout.001.fasta, 20x.fasta-out001.fa
They are two types of output formats.

2.Get list file
>ls /home/…/MaxBin/myout*.fasta > MaxBin_input_list.txt
>ls /home/…/MetaCluster3/20x.fasta-out*.fasta > MetaCluster_input_list.txt

3. d2sBin running
(1)For Maxbin, d2sBin is applied to adjust the contig binning,
>python ./ d2SBin.py --inputList_withSeq MaxBin_input_list.txt -k 6 -r 0 --output ../MaxBin_output
  The output are ./MaxBin_output/d2SBin.out.k6.r0.txt and d2SBin.k6.r0.outXX.fasta
(2)For XX, d2sBin is applied to adjust the contig binning,
 >python ./d2SBin.py --inputList_noSeq MetaCluster_input_list.txt --contig_Seq 20x.fasta -k 6 -r 0 --output ../MetaCluster_output  
The output are are ./MetaCluster_output/d2SBin.out.k6.r0.txt and d2SBin.k6.r0.outXX.fasta

4. The calculation of performance of d2sBin binning results 
	> python evaluation.py -c d2SBin.out.k6.r0.txt –t contigs_c10k_f1K_ASSIGNMENTS .csv –e ./output/eva 
	Output结果
contig_num    genome_num    bin_num    Recall    Precision    ARI
40812    10    3    0.969054628921    0.828655394032    0.692188586938

