# *pedestal*: A solid base for terminal bioinformatics

Bash and Python scripts that facilitate working in the shell with a variety of sequence-based data.

---

[**`deinterleave`**](deinterleave): separation of interleaved FASTQ stream data

```bash
deinterleave [-h] <out1> <out2>
---
cat file.fq | deinterleave file_1.fq file_2.fq
zcat file.fq.gz | deinterleave >(pigz | file_1.fq.gz) >(pigz | file_2.fq.gz)
```

