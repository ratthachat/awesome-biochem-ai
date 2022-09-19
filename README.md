# Awesome BioChem Transformers
Favorite list on Transformers and related Deep-learning approaches applied to Chemistry and Biology
(for all picture credits see https://github.com/ratthachat/awesome-biochem-transformers/tree/main/pictures)

## Table of Contents

- [Molecule Transformers](#molecule-transformers)
  - [Molecule Representation](#molecule-representation)
  - [Enzymatic Reaction](#enzymatic-reaction)
  - [Molecule Generation](#molecule-generation)
  - [Retrosynthesis Pathways](#molecule-retrosynthesis-pathways)
  - [Molecule Similarity Metrics](#molecule-similarity-metrics)

- [Protein Transformers](#protein-transformers)

# Molecule Transformers

## Molecule Representation
Before talking about deep learning for molecules, we need to think first about "how to represent a molecule" itself.

<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/molecule_rep.png" width="57%" height="57%">

- [SMILES](https://en.wikipedia.org/wiki/Simplified_molecular-input_line-entry_system) : SMILES has been a standard on molecule representation using string since 1980s. As of 2022, SMILES is still a standard way to represent a molecule in machine learning. Since SMILES is a string, molecule machine learning using SMILES then can be converted to language-model machine learning. Therefore, state-of-the-arts language models like BERT, GPT and Langauge-translation Transformers are commonly seen in literatures.
 
- [SELFIES](https://github.com/aspuru-guzik-group/selfies) : SELFIES is an alternative string-based molecule representation invented very recently in 2020. It is well-known that in molecule generation applications, generated SMILES-based strings are often invalid because of either syntactic violation (e.g. incomplete brackets) or semantic violation (e.g. an atom has more bonds than its physical capability). SELFIES is designed to be robust in token-mutation and token-permutation to solve these problems of SMILES strings. Recent application such as [ChemGPT (2022)](https://chemrxiv.org/engage/chemrxiv/article-details/627bddd544bdd532395fb4b5) also used SELFIES representation.

- [Graph](https://arxiv.org/pdf/2207.04869.pdf) : Recently, the rise of Graph Neural Network models cause popularity in representing a molecule as a graph. Atom and bond connections can be naturally represented using one-hot atom-vector and adjacency bond-matrices. Even 3D-structure molecule can also be represented by a graph by either using adhoc kNN adjacency or [directly embedding 3D coordinates into a graph (EquiBIND, 2022)](https://arxiv.org/pdf/2202.05146.pdf).
  
<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/molecule_graph.jpg" width="53%">

Nevertheless, there are still some challenges to represent some molecule information such as stereochemistry using graph. Also, "deep graph generative models" are not as matured as string generative models (e.g. GPT-family). These limitations are on-going active research as of 2022.


## Enzymatic Reaction
- ...

## Molecule Generation
- C5T5

## Molecule Retrosynthesis Pathways
- BioNavi NP

## Molecule Similarity Metrics
- MAP4

# Protein Transformers
- AlphaFold2
- ESMFold
- OmegaFold

## Protein-Protein Docking
- EquiDock

## Protein-Ligand Docking
- EquiBind

## Protein Generation

## Protein Design
