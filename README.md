# *pedestal*: A solid base for terminal bioinformatics

A collection of bash wrappers and Python scripts that facilitate working in the terminal with sequence-based data.

---

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

[**`graph`**](graph): split FASTA/Q records to new files
```bash
graph [-h] [--xy [XY]] [--col COL] [--delim DELIM] [--perc] [--header [HEADER]] [--xmin XMIN] [--xmax XMAX] [--ymin YMIN] [--ymax YMAX] [--area AREA] [input ...]
--- usage examples ---
linearise file.fa | awk '{print length($2)}' | graph

 9.56               █
    9
 8.44
 7.87
 7.31
 6.75
 6.19             █
 5.62
 5.06
  4.5                █ █
 3.94                           █
 3.37                    █   █
 2.81                         █     █
 2.25                      █         █ █        ██
 1.69            █    █   █       █      █
 1.12              █               █  █ █  █ ██   █    ██     █
0.562                   █        █        █         ██   ██ ██ █ ██ █   █ ██
    0 ███████████           █  █            █  █   █  █    █    █  █ ███ █  ████
      0         16.9      33.8      50.7      67.6      84.5      101
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
orf_scanner [-h] [-m [MODEL ...]] [-l [MIN_LENGTH]] [-s] [--gff3] [-v] input ...
--- usage examples ---
orf_scanner file.fa -l 100 > file.cds.fa
orf_scanner file.fa file2.fq -m ensembl_cds.fa --gff3 > cds.gff3
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
