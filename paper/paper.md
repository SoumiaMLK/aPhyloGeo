---
title: 'aPhyloGeo: Multi-platform application for analyze phylogenetic trees with climatic parameters'
tags:
  - bioinformatics
  - consensus
  - metrics
  - multiple sequence alignment
  - phylogeny
  - phylogeography
authors:
  - name: Nadia Tahiri
    affiliation: 1
    orcid: 0000-0002-1818-208X
    corresponding: true
    email: Nadia.Tahiri@USherbrooke.ca
affiliations:
  - name: département d’Informatique, Université de Sherbrooke, 2500 Boulevard de l’Université, Sherbrooke, Québec J1K 2R1, Canada
    index: 1
date: 02 March 2024
bibliography: paper.bib

# Optional fields if submitting to a AAS journal too, see this blog post:
# https://blog.joss.theoj.org/2018/12/a-new-collaboration-with-aas-publishing
aas-doi: 10.3847/xxxxx <- update this with the DOI from AAS once you know it.
aas-journal: Astrophysical Journal <- The name of the AAS journal.
---

# Summary
The cross-platform application for phylogenetic tree analysis with climate parameters, `aPhyloGeo`, is a robust pipeline designed for comprehensive phylogenetic analyses using genetic and climate data. This Python API, available on [PyPI](https://pypi.org/project/aphylogeo/), offers a suite of analyses tailored to various scenarios, enabling the examination of datasets at three distinct levels: 1) genetic, 2) climatic, and 3) biogeography correlation, all within a unified package. Similarity at these levels, evaluated through metrics such as least squares distance in \autoref{eq:ls} and Robinson and Foulds distance, significantly influences the assumptions guiding the identification of correlations between a species' genetics and its habitat during the reconstruction of the multiple alignment necessary for phylogenetic inference.

By utilizing the `aPhyloGeo` Python API, users can programmatically implement sophisticated phylogenetic analyses without the need for a graphical interface. This API provides a powerful and flexible toolset for conducting analyses, allowing users to tailor the application to their specific research needs. Through this approach, aPhyloGeo facilitates a nuanced understanding of the interplay between genetic evolution and environmental factors in the context of species adaptation, all within the Python programming environment.

By selecting an appropriate gene list for the available data defined on a set of species to explain the adaptation of the species according to the Darwinian hypothesis, the user can be confident that these assumptions are taken into account in `aPhyloGeo`.

# Statement of need

The rapid impacts of climate change and anthropogenic variables on biodiversity and population dynamics underscore the need for more advanced tools capable of resolving the complexities of ecosystems under perturbation. Biologists use phylogeographic approaches to closely examine the interplay between the genetic structures of study populations and their geographic distributions, taking into account both current and historical geoclimatic contexts.

This software package is dedicated to advancing state-of-the-art bioinformatics tools specifically designed for detailed phylogeographic analysis. Given the urgency of the current climate crisis (COP27 - Climate Change and COP15 - Convention on Biological Diversity) and the anticipated future challenges, there is an urgent need to develop tools that not only meet but exceed bioinformatics software development standards. These tools will be designed to allow accurate characterization of genetic diversity and phenotypic traits in strict accordance with environmental conditions. By maintaining the highest standards, this research aims to make a significant contribution to our understanding of the evolving ecological landscape and provide the scientific community with robust tools for comprehensive analysis and interpretation.

# State of the field

In 2012, Tahiri initially developed the algorithm written in JAVA [@tahiri2012nouvel] with a hight time complexity. More recently, in 2021, the algorithm was converted to Python and updated by my Tahiri lab team [@nadia_tahiri-proc-scipy-2022]. This algorithm will allow finding sub-sequences of genes giving an increased topological similarity between the reference tree (obtained from gene sequences) and the phylogenetic tree (obtained from genome sequences). It can help find which genes or subparts of a gene are sensitive or favourable to a given environment. 

# Pipeline

The `aPhyloGeo` pipeline (\autoref{fig:figure1}) is written in python3.9, and the code and documentation are publicly available on GitHub  (https://github.com/tahiri-lab/aPhyloGeo). The user has the option of running the pipeline using different cparameterss, such as docker, bootstrap threshold, or least square distance threshold. 

![The workflow of the algorithm. The operations within this workflow include several blocks.\label{fig:figure1}](../img/Fig_1.png)

The blocks are highlighted by three different colors.

* The first block (the light blue color) is responsible for creating the trees based on the climate data - performs the function of input parameter validation (see YAML file) and using Neighbor-joining algorithm (see [@gascuel2006neighbor]).
* The second block (the dark yellow color) is responsible for creating the trees based on the genetic data - performs the function of input parameter validation (see YAML file).
* The third block (the light green color) allows the comparaison between the phylogenetic trees (i.e., with genetic data) and the climatic trees - denoted phylogeography step. The phylogeographic step examines how patterns of divergence within species coincide with geographic features, such as climatic features.

# Metrics

## Tree comparaison

### Robinson and Foulds

The Robinson and Foulds (\textit{RF}) distance is a symmetric difference metric defined on the set of bipartitions of a species set $S$. Each edge in $T_1$ and $T_2$ defines a bipartition of $S$. In their mathematical proof, Robinson and Foulds showed that the distance is a well-defined metric on the space of phylogenetic trees \cite{robinson1981comparison}.

The \textit{RF} distance between phylogenetic tree ($T_1$) and reference tree ($T_2$) is the number of non-trivial bipartitions of $T_1$ that are not in $T_2$ plus the number of non-trivial bipartitions of $T_2$ that are not in $T_1$. This distance \textit{RF} between $T_1$ and $T_2$ is computed by the following formula:

\begin{equation} \label{eq:rf}
RF(T_1,T_2) = \frac{|(Q \backslash P) \cup (P \backslash Q)|}{2n-6},
\end{equation}

where $Q$ is a set of all possible bipartitions in phylogenetic tree (denoted $T_1$), $P$ is a set of all possible bipartitions in reference tree (denoted $T_2$), and $n$ the number of leaves in $T_1$ (or $T_2$). It is often relevant to normalize this distance by the maximum possible value of \textit{RF} (equal to $2n-6$ for two binary trees with $n$ common leaves).


### Least Square distance
Least Square distance (see \autoref{eq:ls} and [@felsenstein1997alternating])
\begin{equation}\label{eq:ls}
ls(T_1, T_2) = \sum_{1 \le i \le j \le n} \lvert \delta(i,j) - \xi(i,j) \rvert
\end{equation}
where $T_1$ is the phylogenetic tree 1, $T_2$ is the phylogenetic tree 2, $i$ and $j$ are two species, $\delta(i,j)$ is the distance between specie $i$ and specie $j$ in $T_1$, $\xi(i,j)$ is the distance between specie $i$ and specie $j$ in $T_2$, and $n$ is the total number of species.

This is the most important block and the basis of this study, through the results of which the user receives the output data with the necessary calculations.

Moreover, our approach is optimal since it is elastic and adapts to any computer by using parallelism and available GPUs/CPUs according to the resource usage per unit of computation (i.e., to realize the processing of a single genetic window - see the worflow below).
**Multiprocessing**: Allows multiple windows to be analyzed simultaneously (recommended for large datasets)

In this work, we applied software packages of the following versions: [Biopython](https://biopython.org/) version 1.79 (BSD 3-Clause License), [Bio](https://pandas.pydata.org/) version 1.5.2 (New BSD License), and [numpy](https://numpy.org/) version 1.21.6 (BSD 3-Clause License).




# Conclusion

The `aPhyloGeo` pipeline stands as an all-encompassing solution, offering an extensive array of phylogeographic analyses tailored for diverse datasets, spanning both genetic and climatic dimensions. Consolidating these analyses into a single package eliminates the need for users to navigate and download multiple tools and programs, ensuring not only user-friendly accessibility but also enhancing the pipeline's reproducibility.

Anticipating future developments, `aPhyloGeo` envisions incorporating advanced features. Among these prospective enhancements are the integration of the Nextflow framework, facilitating seamless workflow management. Furthermore, the addition of clustering based on multiple sequence alignment similarity is on the horizon, accompanied by the introduction of an efficient alignment method. To augment the analytical toolkit, novel metrics such as Quartet metric, and bipartition will be integrated, empowering users to make informed decisions about their data by robustly assessing genetic diversity.

By consistently adhering to the highest standards in software development, this research not only aims to provide an immediate solution but also positions `aPhyloGeo` as a dynamic and evolving platform. It aspires to serve as a benchmark in the phylogeographic analysis domain, ensuring that users have access to a comprehensive suite of tools that continually evolve to meet the ever-changing demands of genetic research. Through these advancements, the pipeline aims to contribute significantly to the scholarly discourse and elevate the standards of reproducibility and usability within the scientific community.


# Acknowledgements

This work was supported by the Natural Sciences and Engineering Research Council of Canada, the University of Sherbrooke grant, and the Centre de recherche en écologie de l'UdeS (CREUS). The author would like to thank the Department of Computer Science, University of Sherbrooke, Quebec, Canada for providing the necessary resources to conduct this research. The computations were performed on resources provided by Compute Canada and Compute Quebec - the National and Provincial Infrastructure for High Performance Computing and Data Storage. The author would like to thank the students of the Université du Québec à Montréal for their great contribution to the development of the software.

# References