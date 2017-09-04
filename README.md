# *pedestal*: A solid base for terminal bioinformatics

A collection of bash wrappers and Python scripts that facilitate working in the terminal with sequence-based data.

---

[**`cuti`**](cuti): ordering and selection of columnar data
```bash
cuti [-h] (-n NAMES [NAMES ...] | -f FIELDS) [-d DELIM] [--fill FILL] [input ...]
--- usage examples ---
cat *.tsv | cuti -f1-2,5,3-4 > merged.tsv
cuti file.csv -d, -n pvalue > pvalues
```

[**`cons`**](cons): intelligent consensus formation

`cons` aims to solve problems deriving accurate consensus data from alignments with uneven depths across their length. This is a frequent problem with alignments of sequences clustered by [`vsearch`](https://github.com/torognes/vsearch), [`usearch`](http://www.drive5.com/usearch/), or [`cd-hit-est`](http://weizhongli-lab.org/cd-hit/). Intuitively, a small region of low depth within a larger region of higher depth is of comparatively less relevance than if the same low depth formed a larger, indepentent, region at a different position within the alignment. Normal methods using thresholding approaches will either remove all or include all of these regions, if the threshold is high or low, respectively. `cons` run with `-l/--local` treats the alignment as a time series, and performs changepoint detection to determine regions of equivalent depth. These are smoothed (`-s/--smooth`) to remove small regions present due to indels, for example, and a consensus is derived using thresholds appropriate for each region independently.
```bash
cons [-h] -n NAME [-a [AMBIG]] [-m [MIN]] [-g [GLOB]] [-l [LOCAL]]
    [-s [SMOOTH]] [-w] [--retaingaps] [--changepoints] [input]
--- usage examples ---
cat aligned.fa | cons -n mycons -g -l -s 10 > aligned.cons
```

[**`deinterleave`**](deinterleave): separation of interleaved FASTQ stream data
```bash
deinterleave [-h] out1 out2
--- usage examples ---
cat interleaved.fq | deinterleave file_1.fq file_2.fq
zcat interleaved.fq.gz | deinterleave >(pigz | file_1.fq.gz) >(pigz | file_2.fq.gz)
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
```bash
interleave [-h] in1 in2
--- usage examples ---
interleave file_1.fq file_2.fq > interleaved.fq
interleave <(zcat file_1.fq.gz) <(zcat file_2.fq.gz) | pigz > interleaved.fq.gz
```

[**`interleavei`**](interleavei): intelligent FASTA/Q interleaving
```bash
interleavei [-h] [-u [UNPAIRED ...]] [-1 [FIRSTREAD ...]] [-2 [SECONDREAD ...]] [-w [WRAP]]
--- usage examples ---
samtools fastq -n file.bam | interleavei -u | deinterleave file_1.fq file_2.fq
interleavei -1 file_1.fa -2 <(zcat file_2.fa.gz) -w | pigz > interleaved.fa.gz
```

[**`linearise`**](linearise): FASTA/Q <-> TSV conversion
```bash
linearise [-h] [-v] [-w [WRAP]] [input ...]
--- usage examples ---
linearise file.fq | grep -wF "test" | linearise -v > test.fq
cat file.fq | linearise | cut -f-2 | grep -wF "test" | linearise -v > test.fa
```

[**`mutator`**](mutator): comprehensive DNA mutation simulation

`mutator` simulates both substitution and indel mutation at user-defined rates, or for the human by default. `mutator` can output sequential rounds (generations / years) of mutation within a single run, and output a requested number of replicates at each point.
```bash
mutator [-h] -c CYCLES [CYCLES ...] [-s [SUBSTITUTION]] [-i [INSERTION]] [-d [DELETION]]
    [-r REPLICATES] [-l INDELLENGTH] [-w [WRAP]] [--verbose] [input ...]
--- requirements ---
Python >= 3.6
--- usage examples ---
cat file.fa | mutator -c $(seq -s" " 25000 25000 10000000) -r 100 > mutated.fa
mutator -c 100000 -s 5.4e-9 -i 1.55e-10 -d 1.55e-10 file.fq > mutated.fa
```

[**`orf_scanner`**](orf_scanner): robust CDS prediction

`orf_scanner` outputs ORFs (as AAs with `-t/--translate`) or their positions (with `--gff3`) derived from input sequences. With `-m/--model`, `orf_scanner` will build a hexamer model using supplied validated data (e.g. Ensembl CDSes) and output only ORFs that fit this model.
```bash
orf_scanner [-h] [-m [MODEL ...]] [-l MIN_LENGTH] [-s] [-t] [--longest_only] [--gff3] [--verbose] input ...
--- usage examples ---
orf_scanner file.fa -l 100 > file.cds.fa
orf_scanner file.fa file2.fq -m ensembl_cds.fa --gff3 > cds.gff3
orf_scanner *.fa -t > file.peps
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
