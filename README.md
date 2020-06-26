# *pedestal*: A solid base for terminal bioinformatics

A collection of bash wrappers and Python scripts that facilitate working in the terminal with sequence-based data.

Requirements: Python >= 3.8

---

[**`cons`**](cons): intelligent consensus formation

`cons` aims to solve problems deriving accurate consensus data from alignments with uneven depths across their length. This is a frequent problem with alignments of sequences clustered by [`vsearch`](https://github.com/torognes/vsearch), [`usearch`](http://www.drive5.com/usearch/), or [`cd-hit-est`](http://weizhongli-lab.org/cd-hit/). Intuitively, a small region of low depth within a larger region of higher depth is of comparatively less relevance than if the same low depth formed a larger, independent, region at a different position within the alignment, especially towards either end. Normal methods using thresholding approaches will either remove all or include all of these regions, if the threshold is high or low, respectively. `cons` run with `-l/--local` treats the alignment as a time series, and performs changepoint detection to determine localised regions of equivalent depth. These are smoothed (`-s/--smooth`) and a consensus is derived using thresholds appropriate for each region independently. Separately, `cons` will report bases using IUPAC degeneracy codes but will also interpret these codes within alignments and use the appropraite base possibilities when calulating majority rule at the position.
```bash
cons [-h] -n NAME [-a [AMBIG]] [-m [MIN]] [-g [GLO]] [-l [LOC]]
    [-s [SMOOTH]] [-w [WRAP]] [--retaingaps] [--changepoints] [input]
--- usage examples ---
cat aligned.fa | cons -n mycons -g -l -s 10 --retaingaps >> aligned.fa
cat aligned.fa | cons -n mycons -l > aligned.cons
```

[**`cuti`**](cuti): ordering and selection of columnar data

Builds on UNIX `cut` to allow re-ordering of selected columns and selection by name
```bash
cuti [-h] (-n NAMES [NAMES ...] | -N NAMES_FILE | -f FIELDS) [-d IN_DELIM]
    [-D OUT_DELIM] [--fill FILL] [input [input ...]]
--- usage examples ---
cat *.tsv | cuti -f-2,5,3-4 > merged.tsv
cuti file.csv -d, -n pvalue > pvalues
```

[**`deinterleave`**](deinterleave): separation of interleaved FASTQ stream data

See also: `interleavei`
```bash
deinterleave [-h] out1 out2
--- usage examples ---
cat interleaved.fq | deinterleave file_1.fq file_2.fq
zcat interleaved.fq.gz | deinterleave >(pigz | file_1.fq.gz) >(pigz | file_2.fq.gz)
```

[**`edIted`**](edIted): statistical comparison of RNA editing

`edIted` performs assessments of RNA editing from samtools mpileup data, building on the Dirichlet-based models implemented in ACCUSA2 [Piechotta (2013) Bioinf, 10.1093/bioinformatics/btt268]. When run with test data alone, `edIted` detects base modification by comparing the goodness of fit of Dirchlet models of the base error (derived from the Phred quality data in the mpileup input) and the background error (derived empirically from a sample of the data) to the base frequencies recorded for the position. With an additional control dataset, `edIted` performs the above analysis to determine significantly edited sites and then tests for differential editing between the datasets by comparing the goodness of fit of Dirichlet models of the base error from the test and control datasets to their own and each other's base frequencies. Where aligners record this information in the BAM and samtools mpileup reports it (--output-extra XS), `edIted` can make use of stranding information to increase accuracy and allow the strand of the edited base to be output.
```bash
edIted [-h] -t TEST [TEST ...] [-c CONTROL [CONTROL ...]] [-s] [-o OUTPUT]
    [-e EDIT] [-n NOISE] [-z Z_SCORE] [-d DEPTH] [-a ALT_DEPTH] [-b BACKGROUND] [-q]
--- usage examples ---
samtools mpileup -Q 15 -q 30 -R -f indexedgenome.fa --output-extra XS test.bam | awk '$4 > 1' | pigz > test.mpileup.gz
edIted -e AG -s -t test.mpileup.gz > AG_edit_sites.bed
edIted -e AG -s -t test.mpileup.gz -c control.mpileup.gz -o differential_edit_sites.bed
```

[**`explode`**](explode): split FASTA/Q records to new files
```bash
explode [-h] [-c CHUNKS] [-w [WRAP]] [--dir DIR] [--prefix PREFIX] [input ...]
--- usage examples ---
cat file.fa | explode --dir splitfiles
explode *.fq -c 50 --dir splitfiles --prefix chunk_
```

[**`graph`**](graph): basic plotting in the terminal
```bash
graph [-h] [--xy [XY]] [--col COL] [--delim DELIM] [--perc] [--header [HEADER]]
    [--xmin XMIN] [--xmax XMAX] [--ymin YMIN] [--ymax YMAX] [input ...]
--- usage examples ---
samtools view file.bam -f64 \
| head -100000 \
| awk 'function abs(x) {return x<0 ? -x : x} {print abs($9)}' \
| ./graph --perc --xmax 500

    4.5                       ███
    4.2                      █   ██
    3.9                            █
    3.7                     █       █
    3.4                   ██         █
    3.2                               █
    2.9                                █ █
    2.6                                 █
    2.4                 █                 ██
    2.1                  █                   █
    1.8                █                    █ █
    1.6               █                        █
    1.3                                         ██
    1.1                                           ██
   0.79              █                              ███
   0.53                                                █████
   0.26             █                                       ████
      0      ███████                                            ████████████████
        0         69.4      139       208       278       347       417
```

[**`interleave`**](interleave): interleaving FASTQ data

See also: `interleavei`
```bash
interleave [-h] in1 in2
--- usage examples ---
interleave file_1.fq file_2.fq > interleaved.fq
interleave <(zcat file_1.fq.gz) <(zcat file_2.fq.gz) | pigz > interleaved.fq.gz
```

[**`interleavei`**](interleavei): intelligent FASTA/Q interleaving

Interleaves FASTA/Q data where both reads of a pair are not guaranteed to be present or supplied in the correct order. Alternatively, when run with `-v`, deinterleaves scrambled reads into ordered, paired, files.
```bash
interleavei [-h] [-v INVERSE INVERSE] [-w [WRAP]] [-q] [input [input ...]]
--- usage examples ---
interleavei file_1.fa <(zcat file_2.fa.gz) | pigz > interleaved.fa.gz
samtools fastq -n file.bam | interleavei -v file_1.fq file_2.fq
```

[**`linearise`**](linearise): FASTA/Q <-> TSV conversion

Conversion of FASTA/Q data to and from a columnar format to facilitate common terminal workflows.
```bash
linearise [-h] [-v] [-w [WRAP]] [input ...]
--- usage examples ---
linearise file.fq | cut -f2 | parallel "wc -c" | graph
cat file.fq | linearise | cut -f-2 | grep -wF "test" | linearise -v > test.fa
```

[**`mutator`**](mutator): comprehensive DNA mutation simulation

`mutator` simulates both substitution and indel mutation at user-defined rates, or for the human by default. `mutator` can output sequential `-c/--cycles` (generations / years) of mutation within a single run, and output a requested number of `-r/--replicates` for each cycle. Importantly, `mutator` does not sample from a pool of mutations generated to represent the cycles, but directly uses their relative probabilities, ensuring that low-frequency events can still occur in early mutation cycles.
```bash
mutator [-h] -c CYCLES [CYCLES ...] [-s [SUBSTITUTION]] [-i [INSERTION]]
    [-d [DELETION]] [-r REPLICATES] [-l INDEL_LENGTH] [--sub_sd SUB_SD]
    [--ins_sd INS_SD] [--del_sd DEL_SD] [-w [WRAP]] [-q] [input [input ...]]
--- usage examples ---
cat file.fa | mutator -c $(seq -f"%.0f" -s" " 0 1000000 10000000) -r 100 -i -d > mutated.fa
mutator -c 100000 -s 5.4e-9 -i 1.55e-10 -d 1.55e-10 file.fq > mutated.fa
```

[**`orf_scanner`**](orf_scanner): robust CDS prediction

`orf_scanner` outputs CDS predictions from input sequences. With `-m/--model`, `orf_scanner` scores codon and 3-mer residue usage frequencies for the predicted CDS regions using Markov chains built from supplied validated data (e.g. Ensembl CDS sequences) and outputs only those predictions that fit the profile.
```bash
orf_scanner [-h] [-m [MODEL [MODEL ...]]] [-l [CDS_LEN] | -p [CDS_LEN]] [--unstranded]
    [--longest] [--complete] [-w [WRAP]] [-q] [input [input ...]]
--- usage examples ---
cat *.fa | orf_scanner -l --unstranded > file.cds.fa
orf_scanner file.fa file2.fq -m ensembl_cds.fa --longest -w > cds.fa
```

[**`rc`**](rc): reverse complement
```bash
rc [-h] [-w [WRAP]] [input ...]
--- usage examples ---
rc file.fa -w 80 > file.rc.fa
zcat file.fq.gz | rc | pigz > file.rc.fq.gz
```

[**`subsample`**](subsample): subsampling of streamed FASTQ data
```bash
subsample [-h] proportion
--- usage examples ---
cat file.fq | subsample 0.25 > file2.fq
```
