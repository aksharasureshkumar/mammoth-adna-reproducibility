# Woolly Mammoth aDNA Reproducibility Study

Reproduction of the ancient DNA (aDNA) authentication and alignment workflow from
**Mármol-Sánchez et al. (2026), "Ancient RNA expression profiles from the extinct
woolly mammoth," *Cell* 189(1): 52–69.**
[doi:10.1016/j.cell.2025.10.025](https://doi.org/10.1016/j.cell.2025.10.025)

Team project for BINF 6310, Northeastern University.

## Goal
The original study showed that RNA and protein can be recovered from
permafrost-preserved woolly mammoth specimens, using ancient DNA to authenticate
the samples. We reproduced the aDNA alignment for **Mammoth 1 (Yuka, ~39,000
years old; SRA accession SRR31502649)**. We chose Yuka because the authors
reported the highest endogenous content (25.1%) and the lowest human
contamination (0.0075%) of all specimens.

## Workflow
1. **Data download:** raw reads from NCBI SRA using the SRA Toolkit
2. **Reference genome:** Asian elephant genome (mEleMax1, GCF_024166365.1)
   combined with the woolly mammoth mitochondrial genome (DQ188829.2),
   from the authors' figshare deposit
3. **Pipeline:** [GenErode v0.6.3](https://github.com/NBISweden/GenErode),
   a Snakemake workflow for ancient and historical DNA
   - **fastp:** merging paired-end reads and removing adapters
   - **BWA aln:** alignment with aDNA parameters (`-l 16500 -n 0.01 -o 2`)
   - **SAMtools:** sorting and filtering BAM files
   - **GATK3 IndelRealigner:** realignment around indels
   - **FastQC, MultiQC, Qualimap 2:** quality control at each stage
4. **Compute:** Northeastern University's Explorer HPC cluster

The pipeline ran successfully to completion.

## Results
We are comparing our output with the statistics the authors reported for Yuka:

| Statistic | Reported in paper |
|---|---|
| Total sequences | 2,441,754 |
| Sequences mapped (MQ ≥ 30) | 613,190 |
| Endogenous content | 25.11% |
| Average fragment length | 48.61 nt |
| Human contamination | 0.0075% |

*Our results, configuration files, and run scripts will be added soon.*

## Challenges and solutions
- **GATK3 compatibility:** the IndelRealigner step failed on the cluster, so we
  ran it inside a Singularity container.
- **Missing reference details:** the Asian elephant genome accession was not
  stated in the paper's methods. We identified it as GCF_024166365.1.
- **Missing human reference:** the human genome (hg19) used for contamination
  mapping was not in the authors' data deposit. Because Yuka's reported human
  contamination was very low (0.0075%), this had minimal impact on our analysis.

## Data availability
Raw data and reference genomes are not included in this repository because of
their size.
- Reads: [NCBI SRA SRR31502649](https://www.ncbi.nlm.nih.gov/sra/SRR31502649)
- References: [figshare](https://doi.org/10.6084/m9.figshare.29590415.v2)

## Team
Jake Francoeur, Akshara Sureshkumar, Jacqueline Caldwell

## References
- Mármol-Sánchez, E., et al. (2026). Ancient RNA expression profiles from the
  extinct woolly mammoth. *Cell*, 189(1), 52–69.
- Kutschera, V. E., et al. (2022). GenErode: a bioinformatics pipeline to
  investigate genome erosion in endangered and extinct species.
  *BMC Bioinformatics*, 23, 228.
