# Comparative genomics of oxidative-stress and DNA-repair repertoires in three probiotic lactobacilli

Analysis code and derived data for the manuscript *"Comparative Genomics of
Oxidative-Stress and DNA-Repair Repertoires in Three Probiotic Lactobacilli
Reveals Species-Level Organization and Elevated DNA-Repair dN/dS in
Lactobacillus delbrueckii"* (G. Karapetyan, A. Pepoyan; Armenian National
Agrarian University).

The repository contains one notebook that runs the whole analysis end to end
from three focal genome assemblies, together with the tables and figures it
produces.

---

## What the analysis does

Three focal probiotic strains are compared against 30 conspecific RefSeq
reference genomes each (93 genomes in total):

| Focal strain | Species | GenBank |
|---|---|---|
| Vahe | *Lacticaseibacillus rhamnosus* | VRTQ01 |
| IAHAHI | *Lactobacillus delbrueckii* subsp. *bulgaricus* | VRTP01 |
| ZPZ | *Lactiplantibacillus plantarum* | VRTR01 |

The pipeline downloads a fixed, checksummed cohort, annotates every genome with
a single Prokka version and parameter set, and then evaluates:

- uniform assembly and CheckM2 completeness/contamination QC;
- species assignment by ANI, plus a descriptive subspecies placement for IAHAHI;
- a curated 13-target oxidative-stress and DNA-repair panel (`strict13`), scored
  from sequence evidence with competitive orthology resolution for paralogous
  regulators (Fur/Zur/PerR, Spx/MgsR, OhrR), and an alternative 14-target module
  as a sensitivity check;
- pangenome architecture and openness, before and after 99.5% ANI dereplication;
- pairwise NG86 dN/dS for single-copy families, with genome-bootstrap intervals,
  dS diagnostics and an ORF-integrity screen;
- a GC-compositional screen for horizontally acquired candidates.

Two results drive the paper: the focal strains fall inside their conspecific
prevalence pattern at every evaluable target, and *L. delbrueckii* shows a
consistently higher ω across all ten shared DNA-repair genes.

---

## Repository layout

```
.
├── paper1_publication_v4_5.ipynb         # the complete analysis
├── genomes/                              # three focal assemblies (see below)
│   ├── Vahe_VRTQ01.fasta
│   ├── IAHAHI_VRTP01.fasta
│   └── ZPZ_VRTR01.fasta
└── paper1_results/
    └── paper1_publication_final/         # everything the notebook writes
        ├── figures/                      # PDF + editable SVG + 600-dpi PNG
        └── *.csv                         # tables, manifests, statistics
```

The three focal FASTA files are the only local biological input. Everything
else is downloaded or derived. Outputs are written beside the notebook; set
`PAPER1_PROJECT_ROOT` before launching Jupyter to place them elsewhere.

---

## Requirements

Command-line tools resolved from `PATH`: NCBI `datasets`, Prokka, Panaroo,
CheckM2 (with its database), fastANI, BLAST+, MAFFT, FastTree.

Versions used for the reported run (`software_versions.csv`):

| | |
|---|---|
| Python | 3.11.15 |
| Prokka | 1.14.6 |
| Panaroo | 1.7.0 |
| CheckM2 | 1.1.0 |
| fastANI | 1.34 |
| BLAST+ (tblastn) | 2.12.0+ |
| MAFFT | 7.526 |
| FastTree | 2.2.0 (double precision) |
| numpy / pandas / matplotlib / biopython | 1.23.5 / 1.5.3 / 3.5.2 / 1.79 |

Internet access is needed only for the first download of the pinned RefSeq
accessions and the reviewed UniProt query proteins.

---

## Running it

1. Put the three focal assemblies in `genomes/`.
2. Open the notebook and run the cells in order. Section M0 downloads the fixed
   cohort and records a SHA-256 manifest of every input.
3. Long stages (Prokka, Panaroo, dN/dS) checkpoint to disk and resume, so an
   interrupted run continues from its last completed unit rather than starting
   over. Panaroo results are accepted only when a sidecar marker matches the
   exact species-specific Prokka fingerprint, so a changed cohort forces a
   rebuild instead of silently reusing stale output.
4. Thread counts are set in the configuration cell at the top.

Expect hours rather than minutes on a laptop; the dN/dS stage dominates.

### Reproducibility contract

The accession set is immutable: an unexpected or missing accession stops the
run. Every panel hit is bound to the SHA-256 of the query that produced it, and
input fingerprints propagate into the derived tables, so a result can always be
traced back to the exact inputs that generated it.

---

## Notebook map

Sections are labelled by module. The numbering carries over from the analysis
history and is not gapless — M6 and M7 do not exist, and the M8 series was
added later in response to review; the order in the notebook is the execution
order.

| Section | What it produces |
|---|---|
| M0a–M0c | Fixed cohort download, input manifest, Panaroo rebuild guard |
| M1, M1a | Assembly QC and uniform CheckM2 QC with a sensitivity analysis |
| M1b, M1c | ANI to conspecifics; IAHAHI subspecies placement |
| M2, M2a, M2b | Panel evidence, regulator orthology resolution, SOD execution control |
| M3, M3a | Pangenome architecture, openness, fragmentation sensitivity |
| M4, M4b–M4d | dN/dS, family and paralogy audit, within-species estimates, dS diagnostics |
| M5 | GC-compositional screen for HGT candidates |
| M8.0–M8.5 | Environment, ANI dereplication and refitted γ, paired tests, Rex locus, ORF screen |
| M8.6 | Dereplicated dN/dS sensitivity; *L. delbrueckii* subspecies composition |
| M8.7 | Supplementary table index; runs last, after every table exists |
| Figures | Main Figures 1–6 and Supplementary Figures S1–S9 |

---

## Supplementary tables

`SUPPLEMENTARY_TABLES_INDEX.csv` maps table numbers to files:

| | |
|---|---|
| S1–S3 | Input manifest, assembly QC, analysis-cohort QC |
| S4 | HGT candidates |
| S5–S7 | Curated queries, panel definition, regulator resolution |
| S8 | Dereplicated γ |
| S9–S10 | Descriptive focal/background calls, paired Wilcoxon tests |
| S11–S13 | Rex locus position, fragment-level hits, adjacent hit pairs |
| S14–S16 | ORF-integrity screen, dS diagnostics, ω–dS diagnostic |
| S17 | Within-species oxidative-defence dN/dS |
| S18–S20 | Dereplicated dN/dS, paired tests, robustness audit |
| S21–S22 | *L. delbrueckii* nearest-reference and subspecies composition |

---

## Interpretation notes

These constrain how the outputs should be read, and are stated here because the
tables alone do not carry them:

- `NE` means not evaluated, never zero. Grey cells in the panel figures and `NE`
  rows in the dN/dS grid are deliberate abstentions, not absences.
- Panel totals are not comparable across species. Panel content is confounded
  with genome size and species-level repertoire; only within-species
  comparisons are interpretable.
- The SOD result is evidence of absence under stated thresholds, verified by a
  synthetic execution control. It is not an absolute absence claim, and the
  control is computational rather than an independent positive-control genome.
- Rex in IAHAHI is reported as present-but-disrupted and not scored. A
  frameshift in a draft assembly may be genuine or a sequencing artefact; the
  two readings cannot be separated without targeted resequencing.
- ω–dS correlations are descriptive diagnostics, not tests for pseudogenisation.
- HGT candidates are compositional outliers only, a recognised but
  non-definitive signal.

---

## What is not in this notebook

The reported package also contains an audit layer that compares recomputed
values against those stated in the manuscript and emits
`publication_gate_report.csv`, `manuscript_current_values.csv`,
`reference_results_consistency.csv` and `README_FINAL_OUTPUTS.md`. Those files
were produced by an earlier notebook and are **not** regenerated by
`paper1_publication_v4_5.ipynb`. Either port that audit section into this
notebook before release, or describe the audit as a separate step rather than
part of the end-to-end run.

---

## Data availability

Focal assemblies: GenBank VRTQ01, VRTP01, VRTR01. The 90 conspecific
references are public RefSeq assemblies; every accession is listed with its
SHA-256 checksum in `input_manifest_all93.csv`, so the exact input set can be
reconstructed.

## Citation

Citation details will be added on publication.

## Licence

Choose and add a licence file before making the repository public. For analysis
code accompanying a paper, MIT or BSD-3-Clause are the usual choices; without
one, others have no legal right to reuse the code.
