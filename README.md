# circseq-cup
circseq-cup: a program to assemble full-length sequences of circular RNAs

circseq_cup is a combined tool to identify full-length circular RNAs (circRNAs).
We refer to the CIRCexplorer algorithm from Yang Lab for Tophat-Fusion pipeline and the way to deal with BAM file and ref.txt.

Version: 1.0

Last Modified: 2016-4-18

Authors: Xingchen Zhang (zhangxc_bio@foxmail.com), Chu-Yu Ye (yecy@zju.edu.cn)

##Prerequisites

###Software / Package

####TopHat / STAR / segemehl

* TopHat & TopHat-Fusion
    + [TopHat](http://ccb.jhu.edu/software/tophat/index.shtml) 2.1.0
    + [TopHat-Fusion](http://ccb.jhu.edu/software/tophat/fusion_index.html) included in TopHat 2.1.0
* STAR
    + [STAR](https://github.com/alexdobin/STAR) 2.4.2a
* segemehl
    + [segemehl](http://www.bioinf.uni-leipzig.de/Software/segemehl) 0.1.9

####Others
* [bedtools](https://github.com/arq5x/bedtools2)
* [SAMtools](http://samtools.sourceforge.net)
* [pysam](https://github.com/pysam-developers/pysam)
* [interval](https://github.com/kepbod/interval) (:bangbang: Note: it is not the interval package in PyPI, please do not install it using `pip install interval`)
* [docopt](https://github.com/docopt/docopt)
* [biopython](https://pypi.python.org/pypi/biopython/1.65)
* [cap3](http://seq.cs.iastate.edu/cap3.html)


###Aligner

circseq_cup was originally developed as a circRNA analysis toolkit supporting TopHat & TopHat-Fusion, STAR, segemehl and other aligners.


####TopHat & TopHat-Fusion

To obtain junction reads for circRNAs, two-step mapping strategy was exploited:

* Multiple mapping with TopHat

```bash
tophat2 -a 6 --microexon-search -m 2 -p 10 -G knownGene.gtf -o tophat hg19_bowtie2_index RNA_seq.fastq
```

* Convert unmapped reads (using bamToFastq from [bedtools](https://github.com/arq5x/bedtools2))

```bash
bamToFastq -i tophat/unmapped.bam -fq tophat/unmapped.fastq
```

* Unique mapping with TopHat-Fusion

```bash
tophat2 -o tophat_fusion -p 15 --fusion-search --keep-fasta-order --bowtie1 --no-coverage-search hg19_bowtie1_index tophat/unmapped.fastq
```

####STAR

To detect fusion junctions with STAR, `--chimSegmentMin` should be set to a positive value. For more details about STAR, please refer to [STAR manual](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf).

```bash
star_parse.py Chimeric.out.junction fusion_junction.txt
```

* parse `fusion_junction.txt`


####segemehl

To activate the split read mapping, it is only required to give the option [-S, --splits]. For more details about segemehl, please refer to [segemehl manual](http://www.bioinf.uni-leipzig.de/Software/segemehl/segemehl_manual_0_1_7.pdf).

```bash
grep :C: splitmap.txt > splitmap_c.txt
segemehl_parse.pl splitmap_c.txt fusion_info.txt
```

* parse `fusion_info.txt`

##Usage

```bash

circseq_cup -- circular RNA analysis toolkits.

Usage: RunMe.py [options]

Options:
    -h --help                        Show this screen.
    --version                        Show version.
    -o PREFIX --output=PREFIX        Output prefix [default: circ].
    -t TYPE --type=TYPE              The type of upstream aligners.(tophat_fusion or others) [default: others]
    -f FILE --file=FILE              The input file the upstream aligner made.\
TopHat-Fusion fusion BAM file (used in TopHat-Fusion mapping) dir for tophat_fusion, \
the fusion site file for others.
    -l LENGHT --length=LENGHT        The maximal length between candidate start and end [default: 5000].
    -r REF --ref=REF                 Gene annotation.
    -g GENOME --genome=GENOME        Genome FASTA file.    
    -1 FASTQ_1 --fastq_1=FASTQ_1     Reads1.(*.fastq or *.fastq.gz)
    -2 FASTQ_2 --fastq_2=FASTQ_2     Reads2.
```


# Please cite
Ye et al., Full length sequence assembly reveals circular RNAs with diverse non GT AG splicing signals in rice. RNA Biology. 2016
http://www.tandfonline.com/doi/full/10.1080/15476286.2016.1245268
