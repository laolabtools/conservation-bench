# Conservation Bench

A single-file browser app for aligning a protein across species and scoring how conserved each residue is.

**Use it online:** https://lao-labtools.org/conservation-bench/

Everything runs in the browser. Sequences you paste or load stay on your computer; the only outside connection is to UniProt when you search.

## Input

- **Orthologs of a gene or accession** (e.g. `PCNA` or `P12004`): the app follows UniProt's OMA and OrthoDB ortholog groups and picks the best entry per species (reviewed first, then the reference-proteome entry closest in length). Orthologs with different gene names in other species are found too.
- **UniProt search** by gene name, protein name, accession list or a raw UniProt query, filtered by organism.
- **FASTA**: paste, open or drop files. Pre-aligned FASTA (for example from MAFFT) can be used as given.

## Features

- In-browser multiple alignment: progressive profile alignment (BLOSUM62, affine gaps, ClustalW-style sequence weights), a guide tree re-estimated from the first alignment, and tree-split iterative refinement
- Conservation scores: Jensen–Shannon divergence (Capra & Singh 2007), Shannon entropy, or identity to a chosen reference; optional Henikoff weighting, window smoothing and gap penalty
- Conservation profile in reference numbering with UniProt domain track, drag-to-zoom
- Alignment viewer coloured by residue type, conservation or identity to the reference
- Residue lookup (e.g. `R18, K48, 60-68`) showing each species' residue, score and gap fraction
- Pairwise identity matrix
- Export aligned FASTA, Clustal, per-column scores (CSV) and the profile plot (PNG)

## Accuracy

On BAliBASE families from the BALIFAM benchmark (10-sequence subsets of 59 families), the aligner recovers 88.5% of reference residue pairs (Q score), compared with 86.0% for FAMSA on the same sets. Repetitive or low-complexity regions, such as the RNA polymerase II CTD, remain hard for any aligner.

## Run locally

Download `index.html` and open it in any modern browser. No install, no server.
