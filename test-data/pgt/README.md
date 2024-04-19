# PGT pipeline test-data

This is test data for the [pgt pipeline](https://github.com/husensofteng/pgt). It is created by reverse engineering a pgt run on mouse RNAseq and WGS data from this publication: (FIXME insert link).
Some of the files have been gzipped to save space.

## Sequence data
To create the sequence data input, a pgt db with SQL output was used to find all coordinates of transcripts containing peptides that were both in the actual result and also from chromosome 19, which is conveniently small.
This resulted in **a BED file that was split in two** (included here). The two BED files were used to filter out specific sequences from one of the BAM files (sample S20) containing RNAseq data:

```
samtools view -b -L bed_file.bed large.bam > small.bam
```
This yielded **2 smaller BAM files**, 1.bam, 2.bam. We also filtered **one VCF file** from this dataset by grep-ing for chromosome 19 on sample 102_SNV.

There is also **one GTF file**, bam1_2_merged.gtf (for VCF annotation) which is the result of merging the two BAM files, creating a GTF and annotating with quantitation data in the pgt pipeline.


## MS data
Two mzML files from dataset (FIXME PXD link) were prepared from original dataset raw file fractions 10 and 51 (of a HiRIEF 3-10 strip), using:

```
msconvert mouse_B164T1_3-10_fr10.raw --filter "peakPicking true 2" --filter "precursorRefine" --filter "scanNumber [53730,53780] [64200,64250] [66475,66520]"
msconvert mouse_B164T1_3-10_fr51.raw --filter "peakPicking true 2" --filter "precursorRefine" --filter "scanNumber [36700,36850]"
```

## Other data

- `filtered_canonical_mouse_prot.fa` is used for concatenating the novel DB for search, and filtering out peptides somehow matching to canonical sequence results
- `blast_ref.fa` is used in filtering out sequences that match this DB, and for BLAST to find variants with 1 amino acid difference
- `mouse_101_chr19_ref.gtf` is the reference GTF for chromosome 19 from ENSEMBL 101 release
- `pipeps.db` - is an SQLite database containing isoelectric-point predictions for some peptides
- `predicted_peptides.txt` - is a text file with isoelectric point predictions for remaining peptides
