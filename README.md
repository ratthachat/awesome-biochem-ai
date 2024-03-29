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
  - [Atom-Atom Alignment/Mapping](#atom-mapping)
  - [Molecule Similarity Metrics](#molecule-similarity-metrics)
  - [Libraries on Molecule AI](#libraries-on-molecule-ai)
- [Protein Models](#protein-models)
  - [Protein-Protein Docking](#protein-protein-docking)
  - [Protein-Ligand Binding](#protein-ligand-binding)
  - [Protein Generation](#protein-generation)
  - [Protein Design](#protein-design)

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

  One aspect that *Enzymatic Transformers* improves from Metabolite Translator is the fact that it employs a protein information. Nevertheless, instead of using biological information of an amino-acid sequence, protein information is encoded directly via "human language" and the authors use standard NLP encoder to learn about these protein descriptions.

<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/enzymatic_transformer.png" width="30%">
 

- [Improved Substrate Encodings and Convolutional Pooling (2022)](https://proceedings.mlr.press/v165/xu22a/xu22a.pdf) : This work focuses on classification and/or regression on enzymatic reaction using both substrate and protein/enzyme information. Therefore, the authors emphasize only on the encoder architecture and **NOT** on the prediction of the output molecule structure using a decoder.

  On the enzyme encoder part, the authors propose to use a standard protein encoder such as ESM-1b with Conv1D. Given the the sequence of embedding vectors from ESM-1b or ProtTrans, instead of standard global pooling, the authors claim that employing Conv1D layers better capture both global and local protein information.

  On the substrate encoder part, the author propose to use [ECFP6](https://github.com/rdkit/rdkit/issues/1295)-["Count" encoder](https://stackoverflow.com/questions/54809506/how-can-i-compute-a-count-morgan-fingerprint-as-numpy-array) to capture molecular substructure information.

- [RXNAAMapper (2021)](https://chemrxiv.org/engage/chemrxiv/article-details/61d7f0506be4200bbf24829e)[[code](https://github.com/rxn4chemistry/rxnaamapper)] : IBM's work on mapping a chemical reaction (rxn) with an enzyme or amino-acids (aa) to predict the active site of the enzyme. This work used string based for both molecules (SMILES) and enzymes (amino acid strings).

- [MolSyn Transformers (2022)](https://www.kaggle.com/code/ratthachat/molecule-synthesis-selfies-end2end-training) : this works, the authors propose an opensource framework where users have an option to encode a molecule using either SMILES or SELFIES. Moreover, protein information is extracted directly from its amino-acid sequence using state-of-the-arts [protein ESM transformers](https://github.com/facebookresearch/esm).

## Molecule Retrosynthesis Pathways

<img src="https://github.com/ratthachat/awesome-biochem-ai/blob/main/pictures/retrosynthesis_bionavi.png" width="44%">

- [BioNavi-NP (2022)](https://github.com/prokia/BioNavi-NP) : BioNavi-NP is based on the recent work of [Retro* (ICML 2020)](https://github.com/binghong-ml/retro_star) which is a framework for multi-step pathway predictor based on [A*-algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm). BioNavi-NP concretize Retro* in natural-product (Bio-synthesis) pathways by (1) using standard transformers as 1-step predictor, (2) pretrain from organic chemical pathway then finetune to Bio-synthesis pathways and (3) make a [web-based extension](http://biopathnavi.qmclab.com/) which connects to other systems such as [Selenzyme](http://selenzyme.synbiochem.co.uk/) to predict an enzyme for each reaction in a pathway.

- [Root-aligned SMILES (2022)](https://github.com/otori-bird/retrosynthesis) : This works proposes **R-SMILES** which is adjusted SMILES representations of the input molecules (i.e. make the two molecules align by starting with the same root atom), discrepancy (e.g. edit-distance) of the product-reactant SMILES will become much closer to the default canonical SMILES. By simply apply vanilla encoder-decoder transformers with R-SMILES, the author claims to get state-of-the-arts results over standard benchmark (USPTO). Note that there in a bug in Fig.1 in the published journal paper, so readers should read [an updated arxiv version](https://arxiv.org/abs/2203.11444)

- [AiZynthFinder (2020)](https://github.com/MolecularAI/aizynthfinder)

- [RetroXpert (2020)](https://github.com/uta-smile/RetroXpert) - Information Leak as noted in the Github repo.

- [Graph-to-Graph Retrosynthesis (2020)](https://torchdrug.ai/docs/tutorials/retrosynthesis.html) - [Paper - ICML 2020](https://arxiv.org/pdf/2003.12725.pdf)

## Atom Mapping

<img src="https://github.com/ratthachat/awesome-biochem-ai/blob/main/pictures/rxnmapper.jpg" width="40%">

The task of **atom-atom mapping** is to find the most common substructure of the two input molecules.
This task can be useful in reaction prediction, molecule translation, retrosynthesis prediction, etc.

- [RXNMapper (2021)](https://github.com/rxn4chemistry/rxnmapper) : IBM's work applied string-based encoder-decoder transformers to perform atom-atom mapping between two SMILES strings.

- [Procrustes (2022)](https://procrustes.qcdevs.org/) :  Procrustes proposes a rigorous mathematical optimization library of finding the optimal transformation(s) that makes two matrices as close as possible to each other, which is also included atom-atom mapping as can be seen in the [tutorial here](https://procrustes.qcdevs.org/notebooks/Atom_Atom_Mapping.html). Unlike, RXNMapper which works with string-based molecule, Procrustes works with Graph-based representation of molecules.

- RDKit which is a standard chemoinformatics tool can also be used to perform atom-atom mapping using [Maximum Common Substructure (MCS)](https://www.rdkit.org/docs/source/rdkit.Chem.MCS.html). See [this example](https://gist.github.com/iwatobipen/6d8708d8c77c615cfffbb89409be730d).

## Molecule Similarity Metrics
- **Exact match** : for some application where precision is needed. Exact match is the best choice. The standard approach to perform exact molecule match (e.g. used in [HGraph2Graph (2020)](https://arxiv.org/pdf/2002.03230.pdf) is by using canonical SMILES, i.e. generate the molecule *K* times and calculate the ratio of perfect matching of the generated and true canonical SMILES.

- [MAP4 (2020)](https://github.com/reymond-group/map4) : MAP4 is a recent MinHash fingerprint which comes with a big-title paper, [One molecular fingerprint to rule them all: drugs, biomolecules, and the metabolome](https://jcheminf.biomedcentral.com/articles/10.1186/s13321-020-00445-4) claimed to be an improvement to existing [standard fingerprints](https://rdkit.org/UGM/2012/Landrum_RDKit_UGM.Fingerprints.Final.pptx.pdf). Once a molecule is converted to its fingerprint, we can use [Tanimoto Similarity](https://en.wikipedia.org/wiki/Chemical_similarity) to measure the similarity for any two fingerprints.

- **String Edit Distance** : For string-based molecules, we can simply compared edit distance between (canonical) true and generated strings. Standard edit distance is [Levenshtein](https://en.wikipedia.org/wiki/Levenshtein_distance). For comprehensive library on [various distance choices](https://itnext.io/string-similarity-the-basic-know-your-algorithms-guide-3de3d7346227), see [TextDistance repository](https://github.com/life4/textdistance)

- **Maximum Common Substructure (MCS) Distance** : [MCS-distance](https://www.sciencedirect.com/science/article/abs/pii/S0167865597001797) is a proper mathematical metric defined as 

$$d_{MCS}(m_1, m_2) = 1 - \frac{|MCS(m_1, m_2)|}{max(|m_1|, |m_2|)}$$

  where $MCS(m_1, m_2)$ is the [maximum common substructure](https://greglandrum.github.io/rdkit-blog/tutorial/3d/mcs/2022/06/23/3d-mcs.html) of molecules $m_1$ and $m_2$ and $|m_i|$ is the total number of atoms of $m_i$. In the figure below, MCS between two molecules is draw as a *black* backbone where differences between the two molecules are highlight in red. MCS distance is intuitive to interpret.

<img src="https://i.imgur.com/Oxp69P2.png" width="44%">


- **(Approximate) Graph Edit Distance** : Molecules similarity can be straightforward in graph-based representation if we can map between atoms ot the two compared molecules. Hence, we can apply [atom-atom mapping](#atom-mapping) techniques above and then similarity measure can be simply based on differences of their adjacency matrices. Alternatively, we can also use [(Approximate) Graph Matching repository](https://github.com/Jacobe2169/GMatch4py). Note that this repository requires us to convert molecules into [networkx](https://github.com/networkx/networkx) format which can be done by this [guideline](https://github.com/dakoner/keras-molecules/blob/dbbb790e74e406faa70b13e8be8104d9e938eba2/convert_rdkit_to_networkx.py).

  - Note that [Graph Matching Network (ICML 2019)](https://github.com/Lin-Yijie/Graph-Matching-Networks) is a promising deep-learning model based for approximated graph matching. However, we need to train the model to perform this task.

- **Statistic Matching** : In contrast to a molecule-pair metric, if we want to compare two molecule distributions e.g. between the training and generated sets, we can use the following statistics as used by [HGraph2Graph (2020)](https://arxiv.org/pdf/2002.03230.pdf)
  - Property Statistics :  partition coefficient (logP), synthetic accessibility (SA), drug-likeness (QED) and molecular weight (MW)
  - Structure Statistics : e.g. Chemical Formula, Nearest neighbor similarity, Fragment similarity, scaffold similarity

## Molecule Generation

### String-based
- SmilesVAE / SelfiesVAE
- ChemGPT
- MolGPT
- C5T5
- MolT5


### Graph-based Atom-level
- GraphRNN
- CGVAE
- GRAN
- DGMG

### Graph-based Motif-level
- [JunctionTree VAE (ICML 2018)](https://arxiv.org/pdf/1802.04364.pdf) - A classic work in graph-based molecule generation in tree-like manner. Unlike atom-level approaches, it can generate a ring into a molecule in one-step.

- [HGraph2Graph (ICML 2020)](https://arxiv.org/pdf/2002.03230.pdf) - An improved framework over JT-VAE by the same authors. The differences are (1) HGraph2Graph allows to use large motifs where as JT-VAE uses only small motifs such as rings. (2) HGraph2Graph is cleverly designed to avoid combinatorial problem in molecule generation e.g. it remembers specific atoms in each motif vocabulary which can be connect; hence, it avoids considering all connection possibilities in each motif. For details see this [nice video clip by the author](https://www.youtube.com/watch?v=Y5ZLbJDsuEU).

- [MoLeR (ICLR 2022)](https://arxiv.org/pdf/2103.03864.pdf)[[Code](https://github.com/microsoft/molecule-generation)] from Microsoft which is claimed to be better than HGraph2Graph, e.g. HGraph2Graph cannot make arbitrary cyclic structure.

### 3D
- [Equivariant Diffusion for Molecule Generation in 3D (ICML 2022)](https://arxiv.org/pdf/2203.17003.pdf)

## Libraries on Molecule AI
- [DeepPurpose](https://github.com/kexinhuang12345/DeepPurpose) provides a list of DTI datasets as well as pretrained models.
- [TorchDrug](https://torchdrug.ai/) provides a comprehensive collection of easy-to-use models and datasets on molecules graph datasets and models, molecule generative models, protein graph datasets, and protein models
- [DIG](https://diveintographs.readthedocs.io/en/latest/index.html) provides advanced models on graph generation, self-supervised learning, explainability, and 3D-graph models
- [ChemicalX](https://github.com/AstraZeneca/chemicalx) is AstraZeneca's repository focus on Drug-pair scoring models and applications.
- [DGL-LifeSci](https://lifesci.dgl.ai/) is a python package for applying graph neural networks to various tasks in chemistry and biology, on top of PyTorch, DGL, and RDKit. It covers various applications, including Molecular property prediction, Generative models, Reaction prediction and 
Protein-ligand binding affinity prediction
- [DeepChem](https://deepchem.readthedocs.io/en/latest/) Originating from chemistry-focus models, now DeepChem is an exhausive Tensorflow library supporting several other fields e.g. material science and life science.
- [MolGraph](https://molgraph.readthedocs.io/en/latest/index.html) a new native Keras library focus on molecule graph encoder models.

# Protein Models
To see, discuss latest developments of the field, please follow [ML for protein engineering seminar series (youtube channel)](https://youtube.com/@mlforproteinengineeringsem6420)


- [AlphaFold2 (2021)](https://github.com/deepmind/alphafold) The first ground-breaking work capable of predict highly accurate 3D structure protein folding by Google's DeepMind. The official implementation is in Jax. See pytorch replication [here](https://github.com/lucidrains/alphafold2). We can run on a free Colab machine with [Colabfold](https://github.com/sokrypton/ColabFold)

- [ProtTrans (2021)](https://github.com/agemagician/ProtTrans) by Technical University of Munich is one of the earliest work to apply large language models such as BERT and T5 to protein strings. This work treat proteins as pure strings of 20 amino-acid characters without using other biochemistry knowledge (for example, AlphaFold2 uses Multiple Sequence Alignment). Pure protein strings are feeded to BERT/T5 and protein vectors are produced as a result. These protein vectors then are in turn fed to standard MLPs for protein property prediction tasks. 
  Note that these property prediction tasks are either simple classification or regression, not a complex 3D protein folding prediction like AlphaFold.

- [Ankh (2023)](https://github.com/agemagician/Ankh) - [paper](https://arxiv.org/abs/2301.06568) The 2023 protein language model by the main author of ProtTrans. Ankh, which employs T5-like architecture, claims to be superior to both ProtTrans and ESM/ESM2 on standard protein property predicitons.

- [ESM2 and ESMFold (2022)](https://github.com/facebookresearch/esm) - Amazing work from Meta AI which, similar to ProtTrans, uses only pure protein strings as input to the model. Not only property prediction, ESMFold is shown to be capablel of producing 3D protein folding structure comparable to AlphaFold2, but much faster running time.
  There is also [Huggingface official implementation of ESMfold](https://huggingface.co/facebook/esmfold_v1).

<img src="https://github.com/ratthachat/awesome-biochem-transformers/blob/main/pictures/esmfold.jpg" width="53%">

- [OmegaFold (2022)](https://github.com/HeliXonProtein/OmegaFold) - [paper](https://www.biorxiv.org/content/10.1101/2022.07.21.500999v1.full.pdf) Another work similar to ESMFold which is able to predict  3D protein folding structure from pure protein strings. 
  Interestingly, OmegaFold architecture uses Gate Attention Unit (GAU) instead of multi-head self-attention layers which is standard in Transformers.


## Protein-Protein Docking
- [EquiDock (ICLR 2022)](https://github.com/octavian-ganea/equidock_public)

## Protein-Ligand Binding

Traditional approaches samples a lot of possible binding sites for a pair of protein-ligand, then score each possible sites and return the site having the best score. Even though this method works fine, it is quite computational expensive because a lot of possible binding sites have to be considered.
 
[EquiBind (ICML 2022)](https://github.com/HannesStark/EquiBind) and [DiffDock (ICLR 2023)](https://github.com/gcorso/DiffDock) are works from the same MIT team. To solve protein-ligand binding/docking problem, these two works use "direct prediction" of binding-site instead of "sampling and score" used by traditional approaches.

- EquiBind follows EquiDock by applying SE(3)-Equivariance transformers to extract information from both protein and ligands. The final binding site is predicted directly using "keypoint" prediction technique.

- DiffDock improves further from EquiBind and instead of using keypoint prediction, the predicted binding site is generated with generative diffusion model. DiffDock also employs ESM2 to extract protein information. See overview of DiffDock on [MIT news](https://news.mit.edu/2023/speeding-drug-discovery-with-diffusion-generative-models-diffdock-0331)

## Protein Generation
[ProGen2 (2022)][https://github.com/salesforce/progen/tree/main/progen2] ([paper](https://arxiv.org/abs/2206.13517)) - ProGen: Language Modeling for Protein Engineering by SalesForce. This work employs GPT-like (decoder-only) architecture. Model weights from 151M to 6.4B are available in the repo.

## Protein Design
[RF Diffusion (2022)](https://github.com/RosettaCommons/RFdiffusion) - By Baker lab, University of Washington, a powerful new way to design proteins by combining structure prediction networks and generative diffusion models. The team demonstrated extremely high computational success and tested hundreds of A.I.-generated proteins in the lab, finding that many may be useful as medications, vaccines, or even new nanomaterials. Read more from the [blog of Baker lab](https://www.bakerlab.org/latest/)
