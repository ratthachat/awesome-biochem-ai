# Curated list of AI models applied to Biochemistry

This is my personal favorite list on Biochemistry + AIs, especially Deep-learning approaches.
(for all picture credits see https://github.com/ratthachat/awesome-biochem-ai/tree/main/pictures)

## Table of Contents

- [Molecule Models](#molecule-models)
  - [Molecule Representation](#molecule-representation)
  - [Property Prediction](#property-and-interaction-prediction)
  - [Enzymatic Reaction](#enzymatic-reaction)
  - [Molecule Generation](#molecule-generation)
  - [Retrosynthesis Pathways](#molecule-retrosynthesis-pathways)
  - [Atom Mapping](#atom-mapping)
  - [Molecule Similarity Metrics](#molecule-similarity-metrics)
  - [Libraries on Molecule AI](#libraries-on-molecule-ai)
- [Protein Models](#protein-models)

# Molecule Models
To get all necessary background please visit this free online book : [Deep Learning for Molecules and Materials](https://dmol.pub/). In contrast, to always update with the advanced frontier, follow [MIT's Hannes Stark](https://www.linkedin.com/in/hannes-stark/).

## Molecule Representation
Before talking about deep learning for molecules, we need to think first about "how to represent a molecule" itself.

<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/molecule_rep.png" width="57%" height="57%">

- [SMILES](https://en.wikipedia.org/wiki/Simplified_molecular-input_line-entry_system) : SMILES has been a standard on molecule representation using string since 1980s. As of 2022, SMILES is still a standard way to represent a molecule in machine learning. Since SMILES is a string, molecule machine learning using SMILES then can be converted to language-model machine learning. Therefore, state-of-the-arts language models like BERT, GPT and Langauge-translation Transformers are commonly seen in literatures.

**Limitations:** (1) In a generative setting, SMILES-string decoder needs to learn a perfect SMILES syntax, where with one wrong character can often make the whole SMILES string invalid (see SELFIES below). (2) Due to molecule branching, for one molecule, there can be many SMILES representations; however, a unique canonical-SMILES can also be used.

- [SELFIES](https://github.com/aspuru-guzik-group/selfies) : SELFIES is an alternative string-based molecule representation invented very recently in 2020. It is well-known that in molecule generation applications, generated SMILES-based strings are often invalid because of either syntactic violation (e.g. incomplete brackets) or semantic violation (e.g. an atom has more bonds than its physical capability). SELFIES is designed to be robust in token-mutation and token-permutation to solve these problems of SMILES strings. Recent application such as [ChemGPT (2022)](https://chemrxiv.org/engage/chemrxiv/article-details/627bddd544bdd532395fb4b5) also used SELFIES representation.

Similar to SMILES, due to molecule branching, there can be many SELFIES representations for a single molecule whereas a unique canonical-SELFIES can also be used.

- [Graph](https://arxiv.org/pdf/2207.04869.pdf) : Recently, the rise of Graph Neural Network models cause popularity in representing a molecule as a graph. Atom and bond connections can be naturally represented using one-hot atom-vector and adjacency bond-matrices. Even 3D-structure molecule can also be represented by a graph by either using adhoc kNN adjacency or [directly embedding 3D coordinates into a graph (EquiBIND, 2022)](https://arxiv.org/pdf/2202.05146.pdf).
  
<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/molecule_graph.jpg" width="53%">

**Limitations:** there are still some challenges to represent some molecule information such as stereochemistry using graph. Also, "deep graph generative models" are not as matured as string generative models (e.g. GPT-family). These limitations are on-going active research as of 2022.

## Property and Interaction Prediction

- **Single-Molecule Properties** : There are a lot of works in this area because by representing a molecule as a SMILES string, any existing language-model transformers can be applied directly. Examples of works are [ChemBERTa (2020)](https://arxiv.org/pdf/2010.09885.pdf), [MolBERT (2020)](https://arxiv.org/pdf/2011.13230.pdf), [ChemTransformers/MolBART (2021)](https://github.com/MolecularAI/Chemformer). These works follow large-language model self-supervised learning where [pretrained models on several millions of molecules are available](https://huggingface.co/models?sort=downloads&search=chem).

Converting a molecule's SMILES to graph and apply Graph Neural Networks (GNNs) to predict a molecule properties is also popular and straightforward, see [MolGraph (2022) paper](https://arxiv.org/ftp/arxiv/papers/2208/2208.09944.pdf) for a survey. However, only few pretrained GNNs on millions of molecules exist : I know of only three pretrained models [Grover (2020)](https://github.com/tencent-ailab/grover), [MolCLR (2021)](https://github.com/yuyangw/MolCLR) and [GeoGNN (2022)](https://github.com/PaddlePaddle/PaddleHelix/tree/dev/apps/pretrained_compound/ChemRL/GEM).

- **Drug-Target interaction (DTI)** : In this area , there are two inputs : a drug which is a small molecule (usually represented by a SMILES string) and a target which is a protein (usually represented by an amino-acid sequence string). The prediction targets are interaction scores indicating how well a drug can interact with a protein target. Usually, a molecule-string-encoder and protein-string-encoder can be applied to transform the two inputs into vectors and then concatenate them as a final input vector before using a straightforward MLPs to calculate the interaction scores. For example, in [Self-Attention DTI (2019)](https://bgshin.github.io/assets/pdf/mtdti.pdf), the author propose to use BERT-like transformers to encode drugs and use CNNs to enocode protein strings. 

## Enzymatic Reaction
Enzymatic Reaction is a chemical reaction, i.e. from substrate molecule(s) to product molecule(s), catalyzed/accelerated by an enzyme (protein).

<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/enzymatic_reaction.png" width="36%">

- [Metabolite Translator (2020)](https://pubs.rsc.org/en/content/articlelanding/2020/sc/d0sc02639e) : the authors focus on "enzymatic reaction on drug" (drug metabolism). In this work, protein information is entirely ignored and molecules are encoded with SMILES strings. Hence, the authors simplify the problem as "molecule-string translation" as perfectly analogous to "language translation".

The model is pretrained with 900,000 organic chemical reactions (reactions without a catalyze protein) before finetuning with 11,670 enzymatic reactions. However, note that, even though the finetuning dataset spans the whole spectrum of enzyme classes (indicated by [EC Numbers](https://en.wikipedia.org/wiki/Enzyme_Commission_number)), most of the enzymes are EC1, EC2 and EC3 which are quite simple enzymatic reactions.

- [Enzymatic Transformers (2021)](https://pubs.rsc.org/en/content/articlelanding/2021/sc/d1sc02362d) : Similar to [Metabolite Translator (2020)](https://pubs.rsc.org/en/content/articlelanding/2020/sc/d0sc02639e) discussed above, here the authors pretrain the molecule transformers using organic chemical datasets using the same source (USPTO streo augmented from the work of Lowe) before finetuning with 32,181 enzymatic reactions where the authors purchase and filter from [Reaxys](https://www.elsevier.com/solutions/reaxys).
 
<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/enzymatic_transformer.png" width="30%">

One aspect that improves from Metabolite Translator is the fact that *Enzymatic Transformers* employs a protein information. Nevertheless, instead of using biological information of an amino-acid sequence, protein information is encoded directly via "human language" and the authors use standard NLP encoder to learn about these protein descriptions.

- [RXNAAMapper (2021)](https://chemrxiv.org/engage/chemrxiv/article-details/61d7f0506be4200bbf24829e)[[code](https://github.com/rxn4chemistry/rxnaamapper)] : IBM's work on mapping a chemical reaction (rxn) with an enzyme or amino-acids (aa) to predict the active site of the enzyme. This work used string based for both molecules (SMILES) and enzymes (amino acid strings).

- [MolSyn Transformers (2022)](https://www.kaggle.com/code/ratthachat/molecule-synthesis-selfies-end2end-training) : this works, the authors propose an opensource framework where users have an option to encode a molecule using either SMILES or SELFIES. Moreover, protein information is extracted directly from its amino-acid sequence using state-of-the-arts [protein ESM transformers](https://github.com/facebookresearch/esm).

## Molecule Retrosynthesis Pathways

- [BioNavi NP (2022)](https://github.com/prokia/BioNavi-NP)

- [Root-aligned SMILES (2022)](https://github.com/otori-bird/retrosynthesis) : This works proposes **R-SMILES** which is adjusted SMILES representations of the input molecules (i.e. make the two molecules align by starting with the same root atom), discrepancy (e.g. edit-distance) of the product-reactant SMILES will become much closer to the default canonical SMILES. By simply apply vanilla encoder-decoder transformers with R-SMILES, the author claims to get state-of-the-arts results over standard benchmark (USPTO). Note that there in a bug in Fig.1 in the published journal paper, so readers should read [an updated arxiv version](https://arxiv.org/abs/2203.11444)

- [AiZynthFinder (2020)](https://github.com/MolecularAI/aizynthfinder)

- [RetroXpert (2020)](https://github.com/uta-smile/RetroXpert) - Information Leak as noted in the Github repo.

- [Graph-to-Graph Retrosynthesis (2020)](https://torchdrug.ai/docs/tutorials/retrosynthesis.html) - [Paper - ICML 2020](https://arxiv.org/pdf/2003.12725.pdf)

## Atom Mapping

As seen in the below picture, the task of **atom-atom mapping** is to find the most common substructure of the two input molecules.
This task can be useful in reaction prediction, molecule translation, retrosynthesis prediction, etc.
<img src="https://github.com/ratthachat/awesome-biochem-ai/blob/main/pictures/rxnmapper.jpg" width="36%">

- [RXNMapper (2021)](https://github.com/rxn4chemistry/rxnmapper) : IBM's work applied string-based encoder-decoder transformers to perform atom-atom mapping between two SMILES strings.

- [Procrustes (2022)](https://procrustes.qcdevs.org/) :  Procrustes proposes a rigorous mathematical optimization library of finding the optimal transformation(s) that makes two matrices as close as possible to each other, which is also included atom-atom mapping as can be seen in the [tutorial here](https://procrustes.qcdevs.org/notebooks/Atom_Atom_Mapping.html). Unlike, RXNMapper which works with string-based molecule, Procrustes works with Graph-based representation of molecules.

## Molecule Similarity Metrics
- MAP4

## Molecule Generation
- C5T5
- SmilesVAE / SelfiesVAE
- ChemGPT
- 3D-graph diffusion
- JunctionTree VAE

## 3D Molecule Models
- coming soon

## Libraries on Molecule AI
- [DeepPurpose](https://github.com/kexinhuang12345/DeepPurpose) provides a list of DTI datasets as well as pretrained models.
- [TorchDrug](https://torchdrug.ai/) provides a comprehensive collection of easy-to-use models and datasets on molecules graph datasets and models, molecule generative models, protein graph datasets, and protein models
- [MolGraph](https://molgraph.readthedocs.io/en/latest/index.html) a new native Keras library focus on molecule graph encoder models.
- [DIG](https://diveintographs.readthedocs.io/en/latest/index.html) provides advanced models on graph generation, self-supervised learning, explainability, and 3D-graph models
- [ChemicalX](https://github.com/AstraZeneca/chemicalx) is AstraZeneca's repository focus on Drug-pair scoring models and applications.

# Protein Models
- AlphaFold2
- ESMFold
- OmegaFold

## Protein-Protein Docking
- EquiDock

## Protein-Ligand Docking
- EquiBind

## Protein Generation

## Protein Design
