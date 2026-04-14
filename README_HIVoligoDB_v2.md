# HIVoligoDB — SQLite Database

**HIVoligoDB** (`HIVoligoID.sqlite`) is a curated database of oligonucleotides — primers and probes — designed for the molecular detection and characterisation of HIV-1 and HIV-2. It provides sequence data, genomic annotations, conservation metrics, and ranked primer pair combinations to support the design and evaluation of nucleic acid-based diagnostic assays.

**Publication:** Carneiro J, Resende A, Pereira F. *The HIV oligonucleotide database (HIVoligoDB)*. Database (2017) Vol. 2017: bax005. https://doi.org/10.1093/database/bax005  
**Web interface:** http://portugene.com/HIVoligoDB  
**Format:** SQLite 3 · UTF-8 · ~2.2 MB

---

## Contents

| Table | Description | Rows |
|---|---|---|
| `HIVoligoDB_RefGenomes` | Reference genome metadata (HIV-1: K03455.1; HIV-2: M15390) | 2 |
| `HIVoligoDB_SeqAlignments` | MSA metadata (HIV-1: 170 seqs, 11,334 nt; HIV-2: 95 seqs, 10,914 nt) | 2 |
| `HIVoligoDB_PrimersProbesHIV1` | Curated primer/probe catalogue for HIV-1 | 380 |
| `HIVoligoDB_PrimersProbesHIV2` | Curated primer/probe catalogue for HIV-2 | 65 |
| `HIVoligoDB_SelectTargetsHIV1` | HIV-1 catalogue extended with MSA conservation scores | 380 |
| `HIVoligoDB_SelectTargetsHIV2` | HIV-2 catalogue extended with MSA conservation scores | 65 |
| `HIVoligoDB_SelectTargets2HIV1` | Scored primer pair combinations for HIV-1 | 2,676 |
| `HIVoligoDB_SelectTargets2HIV2` | Scored primer pair combinations for HIV-2 | 198 |
| `HIVoligoDB_GenomeRegionMax` | Sliding-window (30 nt) conservation profiles across both genomes | 23,870 |
| `HIVoligoDB_Articles` | Source literature (54 peer-reviewed publications) | 54 |

---

## Key fields

The primer/probe tables (`PrimersProbes*`) store sequence, genomic coordinates, target gene region, associated technique, and links to source publications. The extended tables (`SelectTargets*`) add three conservation metrics calculated from the multiple sequence alignments:

- **PIS** — percentage of identical (fully conserved) alignment columns within the oligonucleotide window
- **3′PIS** — PIS restricted to the last 5 nucleotides at the 3′ end, which are critical for polymerase extension
- **PPI** — mean pairwise identity across all sequence pairs within the oligonucleotide window
- **Score (HIV score)** — mean of PIS, 3′PIS and PPI; used to rank oligonucleotides and primer pairs

The `SelectTargets2*` tables enumerate all forward–reverse primer combinations with their mean conservation scores. The `GenomeRegionMax` table stores the maximum PIS and PPI values per 30 nt sliding window across each genome alignment, enabling identification of the most conserved genomic regions for new oligonucleotide design.

---

## Reference genomes and alignments

| Virus | Reference | GenBank | Length |
|---|---|---|---|
| HIV-1 | HXB2 | K03455.1 | 9,719 nt |
| HIV-2 | ROD | M15390 | 9,671 nt |

Alignments were retrieved from the Los Alamos National Laboratory HIV database. The HIV-1 alignment (alig01) covers all described subtypes including non-M groups; the HIV-2 alignment (alig02) includes a curated subset of HIV-2 and SIV/SMM sequences.

---

## Quick start

```bash
sqlite3 HIVoligoID.sqlite

-- Top 10 HIV-1 oligonucleotides by conservation score
SELECT "Database reference", "Original name", "Sequence (5'-3')",
       "Genomic region", Score
FROM HIVoligoDB_SelectTargetsHIV1
ORDER BY CAST(Score AS REAL) DESC LIMIT 10;

-- Top 10 HIV-1 primer pairs by mean conservation score
SELECT "Primer 1", "Primer 2", "Amplicon length", Score
FROM HIVoligoDB_SelectTargets2HIV1
ORDER BY CAST(Score AS REAL) DESC LIMIT 10;

-- Most conserved 30 nt windows in the HIV-2 genome (PIS)
SELECT "Position start", "Position end", Value
FROM HIVoligoDB_GenomeRegionMax
WHERE Alignment = 'HIV2' AND Measure = 'PIS'
ORDER BY Value DESC LIMIT 10;
```

---

## Abbreviations

| | |
|---|---|
| PIS | Percentage of identical sites |
| 3′PIS | PIS at the 3′ end of the oligonucleotide |
| PPI | Percentage of pairwise identity |
| MSA | Multiple sequence alignment |
| NAT | Nucleic acid technique |
| LTR | Long terminal repeat |
