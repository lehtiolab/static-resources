# ddamsproteomics test data

This is the test data for [the ddamsproteomics pipeline](https://github.com/lehtiolab/ddamsproteomics)

## mzML files
Spectra files are the for the following conditions:
- Labelfree
- TMT16
- TMT18
- TIMSTof instrument

## Re-numbering scans
Spectra files are generally filtered so only ~500 scans are included. Due to [an issue](https://github.com/dfermin/lucXor/issues/13) where Luciphor
cannot read in mzMLs that dont start with scan=1, the scans are renumbered to instead of 10000-10500
contain 1-501. The first scan thus gets scan=1. This can be used to that effect, where the first scan number is 9989:

```
perl -pe 's/scan=([0-9]+)/"scan=@{[$1-9988]}"/e' tmp.mzML
```
For some reason on `lf_phos_fr10` this did not work if I included the precursor scan 9988, but it did if I did not, even if references to that precursor then are to a negative scan of -11. Also, for hardklor (tested in tmt18 phos) the file then has to be re-indexed since the offsets will be wrong, so afterwards run:

```
msconvert infile.mzML --outfile reindexed.mzML
```

## Special characters
Some spectra files have special characters in them which are to be handled correctly by the pipeline. Nextflow escapes those
but still some are not allowed in XML and will end up in percolator output (notably `<`, `&`, etc). So we strip those in-pipeline.
Others like `?`, `%`, and `*` are used by Nextflow as wildcards, we don't yet have a way to deal with those.
