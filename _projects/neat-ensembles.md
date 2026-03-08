---
title: "NEAT-ensembles - NEAT-based Multiclass Classification"
collection: projects
permalink: /projects/neat-ensembles
date: 2022-01-01
description: Research project applying ECOC-NEAT for improved multiclass classification using neural evolution.
github: https://github.com/lafengxiaoyu/NEAT-ensembles
img: assets/img/projects/neat-ensembles/ECOCNEAT.png
importance: 3
---

A research project supporting my master's thesis on neural evolution and multiclass classification.

**Repository:** https://github.com/lafengxiaoyu/NEAT-ensembles

## Description

This repository provides the code and experimental results for my master's thesis: **"NEAT-based Multiclass Classification with Class Binarization"**. The project proposes a novel method called **ECOC-NEAT** that applies Error-Correcting Output Codes (ECOC) to improve multiclass classification performance of NEAT (NeuroEvolution of Augmenting Topologies).

## Background

### Problem Statement

Multiclass classification is a fundamental and challenging task in machine learning. Traditional approaches include:
- **Class Binarization**: Converting multiclass classification to multiple binary classifications
- **NeuroEvolution**: Generating Artificial Neural Networks using evolutionary algorithms like NEAT

### Our Solution: ECOC-NEAT

We propose a new method that combines Error-Correcting Output Codes (ECOC) with NEAT to enhance multiclass classification performance.

**Key Advantages:**
- High classification accuracy with a considerable number of binary classifiers
- Flexible number of binary classifiers
- Strong robustness against errors

## Datasets

The project was evaluated on three datasets:
1. **Digit** - From Scikit-learn package
2. **Ecoli** - From UCI Machine Learning Repository
3. **Satellite** - From UCI Machine Learning Repository

## Results

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/neat-ensembles/ECOCNEAT.png" title="ECOC-NEAT Architecture" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.html path="assets/img/projects/neat-ensembles/Results.png" title="Experimental Results" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## Key Features

- 🧠 **NEAT Integration** - Uses NeuroEvolution of Augmenting Topologies for neural network generation
- 📊 **Multi-Dataset Evaluation** - Tested on three different datasets from diverse domains
- 🎯 **Error Correction** - Applies ECOC for improved classification accuracy
- 📈 **Robust Performance** - Demonstrates strong robustness against classification errors
- 🔧 **Cluster Support** - Includes SLURM files for running experiments in computing clusters

## Technical Stack

- **Language**: Python, Scilab
- **Machine Learning**: Scikit-learn
- **NeuroEvolution**: NEAT-Python
- **Computing**: SLURM (for cluster execution)

## Usage

### Installation

```bash
pip install -r requirements.txt
```

### Running Experiments

Each dataset has its own directory:
- `Digit/` - Digit classification experiments
- `Ecoli/` - Ecoli classification experiments
- `Satellite/` - Satellite image classification experiments

### Cluster Execution

The project includes SLURM scripts for running experiments in HPC clusters. These files are named with `.slurm` extension.

## Architecture

The ECOC-NEAT architecture follows these principles:

1. **Code Generation**: Generate error-correcting output codes for multiclass problems
2. **Binary Classifiers**: Train multiple binary NEAT classifiers using the codes
3. **Decoding**: Combine binary classifier outputs using error-correcting decoding
4. **Final Prediction**: Determine the final class based on the decoded output

## Publications

This work was published as part of my master's thesis and led to the following paper:

> Gao, Z., & Lan, G. (2022). NEAT-based Multiclass Classification with Class Binarization. *Neural Computing and Applications*.

Please cite this paper if this work is helpful to your research:

```bibtex
@article{gao2022neat,
  title={NEAT-based Multiclass Classification with Class Binarization},
  author={Gao, Zhenyu and Lan, Gongjin},
  journal={Neural Computing and Applications},
  year={2022}
}
```

## Acknowledgments

- Supervisor: Gongjin Lan
- Vrije Universiteit Amsterdam

## License

This research project is provided for academic use.

## Contact

For questions about this project, please open an issue on GitHub or contact me via [LinkedIn](https://linkedin.com/in/gaozhenyu/).
