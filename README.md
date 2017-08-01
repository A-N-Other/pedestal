# *pedestal*: A solid base for terminal bioinformatics

A collection of bash wrappers and Python scripts that facilitate working in the terminal with sequence-based data.

---

[**`deinterleave`**](deinterleave): separation of interleaved FASTQ stream data
```bash
deinterleave [-h] out1 out2
--- usage examples ----
cat interleaved.fq | deinterleave file_1.fq file_2.fq
zcat interleaved.fq.gz | deinterleave >(pigz | file_1.fq.gz) >(pigz | file_2.fq.gz)
```

[**`interleave`**](interleave): interleaving FASTQ data
```bash
interleave [-h] in1 in2
--- usage examples ----
interleave file_1.fq file_2.fq > interleaved.fq
interleave <(zcat file_1.fq.gz) <(zcat file_2.fq.gz) | pigz > interleaved.fq.gz
```

[**`interleavei`**](interleavei): intelligent FASTA/Q interleaving
```bash
interleavei [-h] [-u [UNPAIRED ...]] [-1 [FIRSTREAD ...]] [-2 [SECONDREAD ...]] [-w [WRAP]]
--- usage examples ----
cat *.fq | interleavei -u > interleaved.fq
interleavei -1 file_1.fa -2 <(zcat file_2.fa.gz) -w | pigz > interleaved.fa.gz
```

[**`linearise`**](linearise): FASTA/Q <-> TSV conversion
```bash
linearise [-h] [-v] [-w [WRAP]] [input ...]
--- usage examples ----
linearise file.fq | grep -wF "test" | linearise -v > test.fq
cat file.fq | linearise | cut -f-2 | grep -wF "test" | linearise -v > test.fa
```

[**`orf_scanner`**](orf_scanner): robust CDS prediction
```bash
orf_scanner [-h] [-m [MODEL ...]] [-l [MIN_LENGTH]] [-s] [--gff3] [-v] input ...
--- usage examples ----
orf_scanner file.fa -l 100 > file.cds.fa
orf_scanner file.fa file2.fq -m ensembl_cds.fa --gff3 > cds.gff3
```

[**`rc`**](rc): reverse complement
```bash
rc [-h] [-w [WRAP]] [input ...]
--- usage examples ----
rc file.fa > file.rc.fa
zcat file.fq.gz | rc | pigz > file.rc.fq.gz
```

[**`subsample`**](subsample): subsampling of streamed FASTQ data
```bash
subsample [-h] proportion
--- usage examples ----
cat file.fq | subsample 0.25 > file2.fq
```
