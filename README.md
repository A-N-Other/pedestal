# *pedestal*: A solid base for terminal bioinformatics

A collection of bash wrappers and Python scripts that facilitate working in the terminal with sequence-based data.

---

[**`cuti`**](cuti): ordering and selection of columnar data
```bash
cuti [-h] (-n NAMES [NAMES ...] | -f FIELDS) [-d [DELIM]] [--fill [FILL]] [input ...]
--- usage examples ---
cat *.tsv | cuti -f1-2,5,3-4 > merged.tsv
cuti file.csv -d, -n pvalue > pvalues
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
explode [-h] [-c [CHUNKS]] [-w [WRAP]] [--dir [DIR]] [--prefix [PREFIX]] [input ...]
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
cat *.fq | interleavei -u > interleaved.fq
interleavei -1 file_1.fa -2 <(zcat file_2.fa.gz) -w | pigz > interleaved.fa.gz
```

[**`linearise`**](linearise): FASTA/Q <-> TSV conversion
```bash
linearise [-h] [-v] [-w [WRAP]] [input ...]
--- usage examples ---
linearise file.fq | grep -wF "test" | linearise -v > test.fq
cat file.fq | linearise | cut -f-2 | grep -wF "test" | linearise -v > test.fa
```

[**`orf_scanner`**](orf_scanner): robust CDS prediction
```bash
orf_scanner [-h] [-m [MODEL ...]] [-l MIN_LENGTH] [-s] [-t] [--gff3] [-v] input ...
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
