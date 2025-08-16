# Varion

This repository documents tools and workflows for **deep learning-based variant calling**, focusing on DeepVariant and DeepTrio.

DeepVariant is a variant caller developed by Google Health. It converts aligned sequencing reads into **pileup image tensors**, classifies them using a convolutional neural network (CNN), and outputs variants in standard **VCF** or **gVCF** format.

DeepTrio extends DeepVariant to analyze **parent-child trios or duos**, leveraging family information to improve accuracy.

---

## Features

* High accuracy across multiple sequencing technologies (Illumina, PacBio HiFi, ONT, Element, Complete Genomics).
* Supports **germline variant calling in diploid organisms**.
* Flexible for WGS, WES, RNA-seq, hybrid sequencing, and pangenome-aware mapping.
* Easy-to-use via **Docker**, binaries, or source build.
* Cost-effective and scalable on both on-premise hardware and cloud platforms.

---

## Case Studies

### Germline Variant Calling

* **Illumina / Element** WGS & WES
* **PacBio HiFi** WGS
* **Oxford Nanopore R10.4.1** WGS
* **Complete Genomics T7 / G400**
* **RNA-seq** (Illumina, PacBio Iso-Seq/MAS-Seq)
* **Hybrid** PacBio HiFi + Illumina
* **Pangenome-aware mapping** (BWA / vg)

### Trio-Based Variant Calling

* **Illumina WGS/WES**
* **PacBio HiFi** WGS
* Supports **trios (child + 2 parents)** or **duos (child + 1 parent)**
* Uses **GLnexus** for joint VCF merging

---

## Limitations

* Supports **diploid organisms only** (genotypes: hom-ref, het, hom-alt).
* Models are trained on **human data**. Non-human usage may require retraining (see [Non-human Variant Calling Guide](https://github.com/google/deepvariant)).
* Not intended for **clinical or diagnostic use**.

---

## Installation & Setup

### Prerequisites

* Unix-like OS (Linux, macOS)
* Python 3.10+
* Docker (recommended)

### Run with Docker

```bash
BIN_VERSION="1.9.0"
docker run \
  -v "YOUR_INPUT_DIR:/input" \
  -v "YOUR_OUTPUT_DIR:/output" \
  google/deepvariant:"${BIN_VERSION}" \
  /opt/deepvariant/bin/run_deepvariant \
  --model_type=WGS \   # Choose from [WGS, WES, PACBIO, ONT_R104, HYBRID_PACBIO_ILLUMINA]
  --ref=/input/YOUR_REF \
  --reads=/input/YOUR_BAM \
  --output_vcf=/output/YOUR_OUTPUT.vcf.gz \
  --output_gvcf=/output/YOUR_OUTPUT.g.vcf.gz \
  --num_shards=$(nproc) \
  --vcf_stats_report=true \
  --logging_dir=/output/logs
```

### Optional Flags

* `--haploid_contigs="chrX,chrY"` → Adjust for sex chromosomes.
* `--par_regions_bed=/input/GRCh38_par.bed` → Exclude PAR regions.
* `--disable_small_model=true` → Skip small CNN model.
* `--dry_run=true` → Print commands without execution.

### GPU Acceleration

This toolkit supports experimental **hybrid CPU+GPU** mode (CPU for `make_examples`, GPU for `call_variants`).

---

## References

If you use these tools in your research, please cite:

* **DeepVariant**:

  > Poplin, R., Chang, P.-C., Alexander, D. *et al.* (2018). A universal SNP and small-indel variant caller using deep neural networks. *Nature Biotechnology*, 36, 983–987. doi: [10.1038/nbt.4235](https://doi.org/10.1038/nbt.4235)

* **DeepVariant + GLnexus**:

  > Yun, T., Li, H., Chang, P.-C., Lin, M. F., Carroll, A., & McLean, C. Y. (2021). Accurate, scalable cohort variant calls using DeepVariant and GLnexus. *Bioinformatics*. doi: [10.1093/bioinformatics/btaa1081](https://doi.org/10.1093/bioinformatics/btaa1081)

---

## Why Use This Toolkit?

* **High Accuracy** → PrecisionFDA winner for SNP & indel detection.
* **Flexibility** → Supports multiple sequencing technologies and prep methods.
* **Ease of Use** → Minimal filtering required.
* **Cost-effective** → \~\$11.8 per 30x WGS (on Google Cloud n1-standard-16).
* **Scalable** → Run on CPUs, GPUs, TPUs, locally or in the cloud.

---

## Acknowledgements

This toolkit builds on numerous open-source tools:

* TensorFlow
* Nucleus
* htslib & samtools
* Boost, abseil, pybind11
* GNU Parallel, numpy, SSW Library

License: **BSD-3-Clause**

---

⚠️ **Disclaimer**: This is research software. It is **not a medical device** and is **not intended for clinical use** (diagnosis/prognosis).
