# Machine Learning-Based Surrogate Modeling for Multiphysics Simulations

**Research Project | Machine Learning · Gaussian Processes · Reduced-Order Modeling · Scientific Computing**

**Author:** Taha Zaoui  
**Research environment:** Michelin R&D — Data Science Team, Ladoux, France

## Overview

High-fidelity finite element simulations are essential for modeling complex industrial processes but can be computationally expensive, particularly when repeated evaluations are required for optimization or parameter studies.

This research project investigates **Machine Learning-based surrogate modeling** to approximate high-dimensional physical fields from numerical simulations while reducing the computational cost of repeated predictions.

The study builds upon the **Mesh Morphing Gaussian Process (MMGP)** framework, combining mesh standardization, dimensionality reduction, and Gaussian Process Regression to predict temperature, pressure, and velocity fields under varying physical and geometrical conditions.

A particular focus is placed on improving prediction accuracy in regions characterized by strongly localized physical phenomena.

## Research Challenges

The project addresses four main challenges:

- **High-dimensional outputs:** Finite element solutions represented on meshes containing thousands of nodes.
- **Geometrical variability:** Simulation results defined on different computational meshes.
- **Limited training data:** High computational cost of generating numerical simulations.
- **Localized prediction errors:** Difficulty accurately reproducing strong spatial gradients and localized physical structures.

## Proposed Approach

The proposed workflow combines several numerical and Machine Learning techniques:

1. **Experimental Design:** Latin Hypercube Sampling to construct representative simulation datasets.
2. **Mesh Morphing:** Geometrical standardization using Tutte mapping and RBF-based deformation.
3. **Dimensionality Reduction:** POD-based compression of geometrical and physical fields.
4. **Surrogate Learning:** Gaussian Process Regression for predicting reduced physical representations.
5. **Nonlinear Extension:** Autoencoder-based dimensionality reduction to improve local reconstruction accuracy.

Two MMGP formulations were investigated: a classical geometry-based approach and a parametrized formulation exploiting explicit geometrical parameters.

A nonlinear extension was subsequently developed using an autoencoder with a hybrid reconstruction loss.

## Key Research Contributions

- **Reference mesh selection:** Compared mesh morphing strategies using quantitative criteria related to field preservation and POD compressibility.
- **Surrogate model adaptation:** Investigated parametrized and classical MMGP formulations for geometrically varying finite element simulations.
- **Model interpretability:** Applied SHAP analysis, progressive feature selection, and ablation studies to evaluate the relevance of geometrical descriptors.
- **Error analysis:** Identified the limitations of global reconstruction metrics in assessing strongly localized physical phenomena.
- **Nonlinear dimensionality reduction:** Investigated an autoencoder with a locally penalized reconstruction loss to improve the representation of temperature fields.

## Key Results

The experimental study involved **350 training simulations and 25 independent test simulations**.

**Surrogate Model Comparison**

- The parametrized MMGP generally outperformed the classical formulation across temperature, pressure, and velocity predictions.
- The parametrized model achieved **R² greater than 0.95 for pressure** and close to 1 for velocity.
- Despite strong global performance, large localized errors remained, particularly near regions of maximum pressure and temperature gradients.

**Nonlinear Dimensionality Reduction**

- An optimized autoencoder with an **11-dimensional latent space** improved local temperature-field reconstruction compared with POD.
- The nonlinear MMGP reduced the mean maximum prediction error relative to the classical linear representation in the evaluated configurations.
- Nevertheless, mean maximum temperature prediction errors remained above **30°C**, preventing the approach from meeting the targeted industrial accuracy requirements.

These findings highlight an important challenge in scientific Machine Learning: **strong global predictive performance does not necessarily guarantee accurate reconstruction of localized physical phenomena.**

## Limitations & Future Research

The study identified several directions for further investigation:

- More expressive nonlinear reduced-order representations.
- Physics-informed Gaussian Process models.
- Quantification of mesh morphing and interpolation errors.
- Region-aware modeling of strongly localized physical phenomena.

## Detailed Research Documentation

The complete methodology, experimental investigations, and scientific analyses are available in the following documents:

- [Literature Review & Model Selection](docs/01-literature-review.md)
- [Theoretical Background](docs/02-theoretical-background.md)
- [Methodology & Experimental Design](docs/03-methodology.md)
- [Results & Model Comparison](docs/04-results.md)
- [Nonlinear Dimensionality Reduction with Autoencoders](docs/05-autoencoders.md)
- [Conclusions & Future Research](docs/06-conclusions.md)

## References

The research builds upon the MMGP framework and related methods in mesh morphing, Gaussian Process Regression, and reduced-order modeling.

[View Selected Scientific References](docs/07-references.md)



## Documentation & Availability

The original research report was written in French in accordance
with the academic requirements of my institution.

The complete report is available here:

[View Full Research Report (French, PDF)](report/internship_research_report_fr.pdf)

Detailed scientific documentation is provided in English
in the `docs/` directory.

**Source code is not publicly available in this repository.**
