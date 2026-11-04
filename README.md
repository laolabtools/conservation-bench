# Conservation Bench

**Align a protein across species and score how conserved each residue is, in one HTML file.**

[![License: PolyForm Noncommercial 1.0.0](https://img.shields.io/badge/license-PolyForm%20Noncommercial%201.0.0-orange.svg)](LICENSE)
[![Free for academic use](https://img.shields.io/badge/academic%20use-free-brightgreen.svg)](#license)
[![No install](https://img.shields.io/badge/install-none-brightgreen.svg)](#getting-started)
[![Data stays local](https://img.shields.io/badge/data-never%20uploaded-brightgreen.svg)](#privacy)

Conservation Bench fetches orthologs of a gene from UniProt (or takes your FASTA), aligns them in the browser, and scores every column with Jensen–Shannon divergence, Shannon entropy or identity to a reference. You get a conservation profile in your protein's own numbering with domains overlaid, a coloured alignment, a residue lookup and exports for figures and further analysis.

**[→ Open the live app](https://lao-labtools.org/conservation-bench/)**  ·  **[Download the file](index.html)**  ·  **[Report a bug](https://github.com/laolabtools/conservation-bench/issues)**  ·  **[More Lao LabTools](https://lao-labtools.org)**

## Why this exists

"Is this residue conserved?" is one of the most common questions before designing a point mutant, and answering it usually means juggling a database search, a separate alignment server and a scoring script. Conservation Bench does the whole chain in one page: find the orthologs, align them, score them and look up the residues you care about, with sensible defaults taken from the published methods.

## Privacy

Your files are read by JavaScript in your own browser. Nothing is uploaded, there is no server, no account and no tracking, and once the page has loaded it keeps working with the network disconnected. That makes it safe for unpublished data and for data you are not allowed to put in a cloud service. When you are online the page also fetches its typefaces from Google Fonts; no data goes with that request, and offline it falls back to your system fonts.

**One exception, by design:** when you use **Search UniProt**, your search terms (gene names, accessions or query text) are sent to the UniProt REST API (`rest.uniprot.org`) to retrieve sequences. Sequences you paste or load from FASTA are never sent anywhere, and alignment and scoring always run locally.

The hosted copy on [lao-labtools.org](https://lao-labtools.org) is covered by the site's [privacy policy](https://lao-labtools.org/privacy.html). A downloaded copy opened from your own disk makes no connection to the site at all.

## Getting started

There is nothing to install.

- **Online:** open **[lao-labtools.org/conservation-bench](https://lao-labtools.org/conservation-bench/)**.
- **Offline:** download [`index.html`](index.html) (open it on GitHub and press the download button) and double-click it. It opens in Chrome, Edge, Safari or Firefox and runs from your disk.

The page opens with a small demo set (ubiquitin from human, budding yeast and *Arabidopsis*). Replace it with your own sequences before drawing conclusions.

## How to use

1. **Add sequences.**
   - **Search UniProt → Orthologs of:** type a gene name or accession (e.g. `PCNA` or `P12004`) and choose the starting species. The app follows UniProt's **OMA** and **OrthoDB** ortholog groups and keeps the best entry per species: reviewed (Swiss-Prot) first, then the reference-proteome entry closest in length. Orthologs with different gene names are found too (PCNA is *POL30* in budding yeast). Tick **Show all candidate entries** to choose yourself.
   - **Search UniProt by gene name, protein name, accession list or a raw UniProt query**, filtered by organism.
   - **FASTA:** paste, open or drop plain or pre-aligned FASTA. UniProt headers are parsed for organism and gene.
2. **Alignment and scoring settings.**
   - Gap open and gap extend penalties.
   - Conservation score: Jensen–Shannon divergence (default), Shannon entropy (1 − H/log₂20) or identity to the reference.
   - Window smoothing (none or ±3 residues), and whether gaps are penalized by gap fraction or ignored.
   - **Refine alignment**: a guide tree re-estimated from the first alignment, then iterative refinement. Slower but more accurate.
   - **Henikoff sequence weighting**, which down-weights redundant species.
   - **Input is already aligned**, which skips alignment and scores your alignment as given.
3. Press **Align and score**.
4. **Read the results.**
   - **Conservation profile** in the reference sequence's numbering with the UniProt domain track. Drag to zoom, double-click to reset, click a position to jump to it in the alignment.
   - **Alignment** coloured by residue type, conservation or identity to the reference, with a minimum-score filter.
   - **Residue lookup:** type `R18, K48, 60-68`. The letter is optional and is checked against the reference. You see each species' residue, the score and the gap fraction.
   - **Pairwise identity** matrix.
5. **Export** aligned FASTA, Clustal (`.aln`), per-column scores (CSV) or the profile plot (PNG).

## How it works

- **Alignment.** Progressive profile alignment with BLOSUM62 and affine gap penalties (cheaper terminal gaps).
  1. A k-mer distance matrix and a UPGMA guide tree set the merge order, and ClustalW-style sequence weights balance the tree.
  2. With **Refine** on, Kimura-corrected distances from the first alignment rebuild the guide tree. The alignment is then iteratively improved by splitting it at tree edges and realigning the two halves, keeping changes that raise the weighted sum-of-pairs score.
- **Scoring.** Jensen–Shannon divergence follows Capra & Singh (2007), with BLOSUM62 background amino-acid frequencies. Optional Henikoff position-based weights and a gap-fraction penalty are applied as in that paper.

## Accuracy

On BAliBASE families from the **BALIFAM** benchmark (10-sequence subsets of 59 families), the aligner recovers 88.5% of reference residue pairs (Q score), compared with 86.0% for FAMSA on the same sets. Repetitive or low-complexity regions, such as the RNA polymerase II CTD, remain hard for any aligner. Check those by eye.

## Limitations

- Designed for tens to a few hundred sequences of one protein family; very large sets are limited by browser speed.
- Conservation scores are only as good as the ortholog set. Paralogs, fragments or mispredicted gene models in the set will lower scores. Review the sequence set before interpreting a single residue.
- UniProt search needs an internet connection; everything else works offline.

## Building from source

There is no build step. `index.html` *is* the source: one file of HTML, CSS and plain JavaScript with no dependencies. The aligner runs in a Web Worker created from the same file.

## Reporting bugs and requesting features

Please use **[GitHub Issues](https://github.com/laolabtools/conservation-bench/issues)** for bugs, wrong results and feature requests. A useful report includes:

- what you did, what you expected and what happened instead;
- your browser and operating system;
- a screenshot, and if you can, the FASTA or the search you used so the problem can be reproduced (remove anything confidential first).

## Acknowledgements and references

**Conservation scoring.**
- Capra JA, Singh M. Predicting functionally important residues from sequence conservation. *Bioinformatics* 23, 1875–1882 (2007). [doi:10.1093/bioinformatics/btm270](https://doi.org/10.1093/bioinformatics/btm270). The Jensen–Shannon divergence score, background distribution, window and gap handling.
- Shannon CE. A mathematical theory of communication. *Bell Syst Tech J* 27, 379–423 (1948). The entropy score.
- Henikoff S, Henikoff JG. Position-based sequence weights. *J Mol Biol* 243, 574–578 (1994). [doi:10.1016/0022-2836(94)90032-9](https://doi.org/10.1016/0022-2836(94)90032-9)

**Alignment.**
- Henikoff S, Henikoff JG. Amino acid substitution matrices from protein blocks. *Proc Natl Acad Sci USA* 89, 10915–10919 (1992). [doi:10.1073/pnas.89.22.10915](https://doi.org/10.1073/pnas.89.22.10915). BLOSUM62.
- Gotoh O. An improved algorithm for matching biological sequences. *J Mol Biol* 162, 705–708 (1982). [doi:10.1016/0022-2836(82)90398-9](https://doi.org/10.1016/0022-2836(82)90398-9). Affine gap penalties.
- Thompson JD, Higgins DG, Gibson TJ. CLUSTAL W: improving the sensitivity of progressive multiple sequence alignment through sequence weighting, position-specific gap penalties and weight matrix choice. *Nucleic Acids Res* 22, 4673–4680 (1994). [doi:10.1093/nar/22.22.4673](https://doi.org/10.1093/nar/22.22.4673). Tree-based sequence weights and the Clustal output format.
- Edgar RC. MUSCLE: multiple sequence alignment with high accuracy and high throughput. *Nucleic Acids Res* 32, 1792–1797 (2004). [doi:10.1093/nar/gkh340](https://doi.org/10.1093/nar/gkh340). The k-mer distance, re-estimated guide tree and tree-dependent iterative refinement strategy.
- Kimura M. *The Neutral Theory of Molecular Evolution.* Cambridge University Press (1983). The protein distance correction.

**Benchmark.**
- Thompson JD, Koehl P, Ripp R, Poch O. BAliBASE 3.0: latest developments of the multiple sequence alignment benchmark. *Proteins* 61, 127–136 (2005). [doi:10.1002/prot.20527](https://doi.org/10.1002/prot.20527)
- BALIFAM benchmark by Robert C. Edgar ([github.com/rcedgar/balifam](https://github.com/rcedgar/balifam), CC0), described with Edgar RC. Muscle5: high-accuracy alignment ensembles enable unbiased assessments of sequence homology and phylogeny. *Nat Commun* 13, 6968 (2022). [doi:10.1038/s41467-022-34630-w](https://doi.org/10.1038/s41467-022-34630-w)
- Deorowicz S, Debudaj-Grabysz A, Gudyś A. FAMSA: fast and accurate multiple sequence alignment of huge protein families. *Sci Rep* 6, 33964 (2016). [doi:10.1038/srep33964](https://doi.org/10.1038/srep33964). The comparison aligner.

**Sequence and ortholog data** (retrieved live; not redistributed in this repository).
- The UniProt Consortium. UniProt: the Universal Protein Knowledgebase in 2025. *Nucleic Acids Res* 53, D609–D617 (2025). [doi:10.1093/nar/gkae1010](https://doi.org/10.1093/nar/gkae1010). UniProt data are available under [CC BY 4.0](https://www.uniprot.org/help/license).
- Altenhoff AM *et al.* OMA orthology in 2024: improved prokaryote coverage, ancestral and extant GO enrichment, a revamped synteny viewer and more in the OMA Ecosystem. *Nucleic Acids Res* 52, D513–D521 (2024). [doi:10.1093/nar/gkad1020](https://doi.org/10.1093/nar/gkad1020)
- Kuznetsov D *et al.* OrthoDB v11: annotation of orthologs in the widest sampling of organismal diversity. *Nucleic Acids Res* 51, D445–D451 (2023). [doi:10.1093/nar/gkac998](https://doi.org/10.1093/nar/gkac998)

**Software.** No third-party code is bundled. Typefaces (Bricolage Grotesque, Schibsted Grotesk, JetBrains Mono) are served by Google Fonts under the SIL Open Font License.

## License

**Free for academic and other non-commercial use** under the [PolyForm Noncommercial License 1.0.0](LICENSE).

In plain terms (the licence text is what counts): you may use, copy, modify and share this software for research, teaching, study and any other non-commercial purpose, including at universities, hospitals, non-profit research institutes and government laboratories. **Commercial use**, such as use by or for a company, in industry R&D, contract research or a paid service, is not covered and needs a separate licence; to ask about one, open an issue in this repository.

**Data.** Sequences and annotations retrieved from UniProt, including the three ubiquitin sequences of the built-in demo set, are © UniProt Consortium and licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) ([UniProt licence](https://www.uniprot.org/help/license)). Alignments you export from UniProt sequences stay under those terms; cite UniProt when you publish results based on them.

## Disclaimer

This is an independent research tool. It is provided as is, without warranty of any kind. Cross-check key numbers against established software before you publish them. It is for research use only. It is not a medical device and must not be used for diagnostic or clinical decisions.

---

Part of **[Lao LabTools](https://lao-labtools.org)**: free, install-free browser tools for the bench, by Linjiang Lao.
