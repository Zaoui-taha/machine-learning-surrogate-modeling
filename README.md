## Abstract

High-fidelity numerical simulations play a crucial role in understanding and optimizing complex industrial manufacturing processes. However, finite element simulations involving coupled physical phenomena can be computationally expensive, particularly when repeated evaluations are required for optimization, uncertainty quantification, or parametric studies.

This research project focuses on the development and evaluation of **Machine Learning-based surrogate models** to approximate high-dimensional physical fields obtained from finite element simulations while reducing computational costs.

The proposed approach builds upon the **Mesh Morphing Gaussian Process (MMGP)** framework, which combines mesh standardization, dimensionality reduction, and Gaussian Process Regression to predict physical fields under varying geometric and physical conditions.

The study investigates several methodological choices, including mesh morphing strategies, reference mesh selection, dimensionality reduction techniques, and surrogate model configurations. Particular attention is given to the limitations of **Proper Orthogonal Decomposition (POD)** in reconstructing strongly localized physical phenomena.

To address these limitations, a **nonlinear dimensionality reduction approach based on autoencoders** is investigated, with a particular focus on improving the representation of localized structures and reducing reconstruction errors.

The results demonstrate that the developed surrogate modeling approaches can reproduce the global behavior of several physical fields. Nonlinear dimensionality reduction improves prediction quality in certain configurations, although accurately capturing highly localized phenomena remains challenging and the targeted accuracy has not yet been fully achieved.

## 1. Introduction & Research Problem

### 1.1. Background and Motivation

High-fidelity numerical simulations are essential for understanding and optimizing industrial manufacturing processes involving complex multiphysics phenomena, such as fluid flow, heat transfer, and material deformation.

In particular, the **Finite Element Method (FEM)** is widely used to solve systems of partial differential equations (PDEs) governing these physical processes. While FEM provides detailed numerical approximations, its computational cost can become prohibitive when numerous simulations are required, particularly for process optimization, parametric studies, and uncertainty quantification.

This limitation motivates the development of **Machine Learning-based surrogate models**, which aim to approximate the relationship between physical and geometrical input parameters and the corresponding simulation outputs, while significantly reducing the computational cost of repeated predictions.

### 1.2. Industrial Application

This research was conducted within the Data Science team at Michelin's Research and Development Center in Ladoux, France.

The industrial application concerns a manufacturing process involving coupled fluid mechanics and heat transfer phenomena. The objective is to predict physical fields, particularly **temperature, pressure, and velocity**, under varying operating and geometrical conditions.

These fields are computed using high-fidelity finite element simulations and provide essential information for understanding the physical behavior of the material throughout the manufacturing process.

However, the computational expense of these simulations limits their use in applications requiring a large number of evaluations. Developing efficient surrogate models therefore represents a promising approach to accelerating numerical predictions while maintaining an acceptable level of accuracy.

### 1.3. Scientific and Technical Challenges

The development of reliable surrogate models for multiphysics simulations presents several important challenges:

- **High-dimensional physical outputs:** Numerical solutions are represented by fields discretized over computational meshes containing thousands of degrees of freedom.

- **Variable computational geometries:** Different simulation configurations may involve meshes with different geometries, numbers of nodes, and spatial discretizations, making direct comparisons and supervised learning difficult.

- **Limited training data:** Generating high-fidelity simulation data is computationally expensive, which restricts the number of available training samples.

- **Preservation of physical field structures:** Surrogate predictions must reproduce the spatial behavior and regularity of the underlying physical quantities.

- **Localized physical phenomena:** Strong spatial gradients and localized variations can be particularly difficult to reconstruct accurately using conventional dimensionality reduction approaches.

Addressing these challenges requires combining appropriate mesh processing techniques, dimensionality reduction methods, and statistical learning models.

### 1.4. Research Objectives

The main objective of this project is to **develop, adapt, and evaluate a surrogate modeling approach capable of approximating high-dimensional finite element solutions under varying physical and geometrical conditions**.

More specifically, the research aims to:

1. Investigate existing surrogate modeling approaches and select an appropriate framework based on the computational and physical constraints of the problem.
2. Develop a consistent representation of physical fields defined on varying computational meshes through mesh morphing and interpolation techniques.
3. Apply dimensionality reduction methods to obtain compact representations of high-dimensional physical fields.
4. Evaluate and compare surrogate model configurations using quantitative prediction and reconstruction error metrics.
5. Investigate the limitations of linear dimensionality reduction and explore nonlinear representations based on autoencoders to improve the reconstruction of localized physical structures.

The following sections describe the literature review, the selected modeling framework, the methodological choices, and the experimental investigations conducted throughout the project.

## 2. Literature Review & Model Selection

### 2.1. Model Selection Criteria

Selecting an appropriate surrogate modeling approach requires accounting for both the physical characteristics of the problem and the computational constraints associated with high-fidelity simulations.

In this study, four main requirements guided the selection of the modeling framework:

1. **Preservation of spatial regularity:** The target quantities are continuous physical fields, including temperature, pressure, and velocity. The surrogate model should preserve their spatial coherence and avoid introducing artificial discontinuities or uncorrelated prediction errors between neighboring mesh nodes.

2. **Accurate interpolation of deterministic simulations:** The finite element solver produces deterministic outputs for fixed input parameters. Therefore, interpolation-based learning approaches are particularly relevant for approximating these numerical solutions.

3. **Handling high-dimensional outputs:** The physical fields are discretized on computational meshes containing approximately 10,000 nodes. Directly predicting these high-dimensional outputs would be computationally demanding, motivating the use of dimensionality reduction techniques.

4. **Learning from limited simulation data:** Generating training samples through finite element simulations is computationally expensive. The selected approach should therefore be suitable for relatively small training datasets.

These requirements formed the basis for reviewing and comparing several families of surrogate modeling methods.

### 2.2. Review of Existing Approaches

Several machine learning approaches have been proposed in the literature to approximate solutions of partial differential equations and reduce the computational burden of numerical simulations.

**Linear Regression**

Linear regression provides a simple and computationally inexpensive modeling approach. However, the governing equations considered in this project are nonlinear, making a purely linear regression model insufficiently expressive for the expected complexity of the input-output relationships.

**Neural Network-Based Approaches**

Neural networks, particularly Convolutional Neural Networks (CNNs) and Graph Neural Networks (GNNs), have been investigated in the literature for learning complex physical relationships and approximating numerical solutions.

Although these models offer considerable representational flexibility, several challenges were identified in relation to the requirements of this study:

- Potentially substantial training data requirements.
- Computational and memory costs associated with large meshes.
- Scalability challenges when processing high-dimensional spatial data.
- Possible oversmoothing effects in graph-based architectures.
- Lack of an exact interpolation property in standard neural network formulations.

These considerations motivated the investigation of alternative regression approaches better suited to the available simulation data and computational constraints.

**Radial Basis Function Regression**

Radial Basis Function (RBF) methods provide an interpolation-based approach that can approximate nonlinear relationships between input parameters and physical outputs.

Exact interpolation can be obtained under suitable conditions, making RBF methods relevant for deterministic simulation data.

However, Gaussian Process Regression offers an additional property of particular interest for surrogate modeling: the ability to quantify predictive uncertainty.

**Gaussian Process Regression (GPR)**

Gaussian Process Regression provides a flexible probabilistic framework for approximating nonlinear functions.

For noise-free observations, an appropriately configured Gaussian Process can interpolate the training data while also providing a predictive variance.

This makes GPR particularly attractive for the present application, as it combines:

- Nonlinear regression capabilities.
- Interpolation of deterministic training observations.
- Predictive uncertainty estimation.
- Suitability for relatively small datasets.

Nevertheless, directly applying Gaussian Process Regression to full-resolution physical fields remains challenging because of their high dimensionality and the variability of the computational meshes.

### 2.3. Geometrical Variability and Dimensionality Reduction

Beyond the choice of regression model, two additional challenges must be addressed.

**Handling Geometrical Variability**

The finite element solutions are defined on meshes that may differ in geometry, discretization, and number of nodes.

Consequently, the physical fields cannot be directly represented as consistent outputs for conventional supervised learning.

A mesh standardization procedure is therefore required to map the different simulation configurations onto a common reference domain.

Several techniques were considered, including:

- Tutte-based mesh parametrization.
- Radial Basis Function (RBF) mesh morphing.

These approaches provide mechanisms for establishing a common geometrical representation and enabling the comparison of physical fields across different simulation configurations.

**Reducing the Dimensionality of Physical Fields**

Even after mesh standardization, the resulting physical fields remain high-dimensional.

Dimensionality reduction provides a way to represent these fields through a smaller number of latent variables while preserving their most important spatial characteristics.

Two relevant approaches are:

- **Proper Orthogonal Decomposition (POD):** A linear dimensionality reduction technique that constructs a reduced basis from dominant spatial modes.
- **Autoencoders:** Neural network-based models capable of learning nonlinear low-dimensional representations.

POD provides a well-established and computationally efficient starting point for reduced-order modeling. However, nonlinear representations may offer advantages when the physical fields contain complex or strongly localized structures.

### 2.4. Selection of the MMGP Framework

Based on the literature review and the requirements identified above, the **Mesh Morphing Gaussian Process (MMGP)** framework was selected as the starting point for this study.

Originally introduced by Casenave, Staber, and Roynard (NeurIPS 2023), MMGP is designed to predict physical solutions defined on computational meshes exhibiting geometrical variability.

The framework combines three main components:

1. **Mesh Morphing:** Establishing a common geometrical representation for different simulation configurations.
2. **Dimensionality Reduction:** Compressing high-dimensional physical fields into a reduced representation.
3. **Gaussian Process Regression:** Learning the relationship between input parameters and reduced representations of the physical solutions.

An important aspect of the present study is that the original MMGP framework was developed for nonparametrized geometrical variability, whereas the industrial problem considered here involves explicit geometrical parameters controlling the computational domains.

The research therefore investigates the adaptation of the MMGP approach to this parametrized setting, along with methodological choices related to mesh processing, field representation, dimensionality reduction, and regression.

The following sections introduce the theoretical foundations of these techniques before describing their implementation and experimental evaluation.

## 3. Theoretical Background

The Mesh Morphing Gaussian Process (MMGP) framework combines several mathematical and computational techniques to construct surrogate models for physical fields defined on variable computational meshes.

This section introduces the main theoretical concepts underlying the approach: **Proper Orthogonal Decomposition (POD)**, **Mesh Morphing**, and **Gaussian Process Regression (GPR)**.

### 3.1. Proper Orthogonal Decomposition (POD)

Proper Orthogonal Decomposition is a linear dimensionality reduction technique widely used in reduced-order modeling to represent high-dimensional physical fields through a limited number of dominant spatial modes.

Consider a collection of numerical simulations arranged in a snapshot matrix:

**U ∈ ℝ^(m × n)**

where:
- **m**: Number of simulation snapshots.
- **n**: Number of spatial degrees of freedom.

POD constructs an orthonormal basis of spatial modes that captures the dominant variations in the data.


Each physical field can be approximated using a reduced number of POD modes:

**u⁽ⁱ⁾ ≈ Σₖ₌₁ʳ aₖ⁽ⁱ⁾ φₖ**

where:
- **φₖ**: The k-th POD spatial mode.
- **aₖ⁽ⁱ⁾**: Modal coefficient for simulation i.
- **r**: Number of retained POD modes.

The number of retained POD modes is selected according to the cumulative explained variance:

**ηᵣ = (λ₁ + λ₂ + ... + λᵣ) / (λ₁ + λ₂ + ... + λₙ)**

where:
- **λₖ**: Eigenvalue associated with the k-th POD mode.
- **r**: Number of retained modes.
- **n**: Total number of modes considered.

**Role in surrogate modeling**

Instead of directly predicting thousands of physical field values, a machine learning model can be trained to predict a small number of modal coefficients.

The complete physical field is subsequently reconstructed using the reduced POD basis.

In this project, a **non-intrusive POD approach** is adopted: the governing physical equations are not projected onto the reduced basis. Instead, the reduced coefficients are learned through supervised regression.

This considerably reduces the dimensionality of the model outputs.

### 3.2. Mesh Morphing and Geometrical Standardization

A major challenge in learning finite element solutions is that different simulations may be performed on meshes with varying geometries, numbers of nodes, and spatial discretizations.

As a result, the corresponding physical fields cannot be directly compared or represented as fixed-dimensional outputs for supervised learning.

**Mesh Morphing** addresses this issue by transforming computational meshes onto a common reference domain.

Two relevant mesh transformation approaches are considered in this study:

- **Tutte-based parametrization:** Maps suitable meshes onto a reference domain, such as the unit disk.
- **Radial Basis Function (RBF) Mesh Morphing:** Uses radial basis functions to deform meshes toward a selected reference geometry.

The transformation preserves the mesh connectivity while modifying the nodal coordinates.

However, mesh morphing alone does not necessarily produce identical discretizations. A further interpolation step is therefore required.

**Common Reference Mesh**

After geometrical transformation, the physical fields are transferred onto a common reference mesh using finite element interpolation.

This procedure ensures that the different simulation outputs are represented on a shared spatial discretization and have consistent dimensions.

The geometrical information is also preserved by transferring the original mesh coordinate fields onto the reference mesh.

These coordinate fields can then be compressed through POD, providing a reduced representation of the original geometry.

### 3.3. Gaussian Process Regression (GPR)

Gaussian Process Regression is a probabilistic supervised learning approach used to approximate unknown relationships between input parameters and output quantities.


A Gaussian Process is defined by a mean function and a covariance kernel:

**f(x) ~ GP(m(x), k(x, x′))**

where:
- **f(x)**: Unknown function to approximate.
- **m(x)**: Mean function.
- **k(x, x′)**: Covariance kernel.
- **GP**: Gaussian Process.

For deterministic numerical simulations, Gaussian Process Regression can provide accurate interpolation of training observations under suitable modeling assumptions.

An additional advantage is its ability to provide **predictive uncertainty estimates**, making it particularly useful for surrogate modeling.

Within the MMGP framework, Gaussian Process Regression is applied to the reduced coefficients of physical fields rather than to the complete high-dimensional simulation outputs.

### 3.4. Integration within the MMGP Framework

The MMGP methodology combines the previous techniques into a unified surrogate modeling workflow.

**Step 1 — Mesh Morphing**

Transform the computational meshes onto a common reference domain.

**Step 2 — Field Interpolation**

Transfer physical and geometrical fields onto a shared reference mesh.

**Step 3 — Dimensionality Reduction**

Apply POD to obtain compact representations of the geometric coordinate fields and physical quantities of interest.

**Step 4 — Supervised Learning**

Train Gaussian Process models to predict the reduced coefficients of the physical fields using the physical input parameters and reduced geometrical information.

**Step 5 — Physical Field Reconstruction**

Reconstruct the predicted physical fields through inverse POD and transfer them back to the target computational mesh.

The resulting framework provides a way to learn high-dimensional finite element solutions while accounting for geometrical variability.

### 3.5. Preliminary Validation on a Simple Test Case

Before applying the methodology to the industrial problem, a preliminary study was performed on a one-dimensional heat equation.

The objective was to verify the dimensionality reduction and reconstruction workflow using a simplified numerical example.

The study involved:

- Numerical resolution of the heat equation using finite differences.
- Extraction of dominant spatial modes through POD.
- Comparison of Gaussian Process and Radial Basis Function regression for predicting modal coefficients.
- Reconstruction of the physical solution using the predicted coefficients.

For this simplified problem, five POD modes captured 99.99% of the explained variance. Gaussian Process Regression provided the best performance among the regression approaches tested, while extrapolation remained challenging.

This preliminary investigation helped establish the reduced-order modeling workflow before its application to more complex multiphysics simulations.

The following section presents the development of the surrogate modeling approach and the experimental choices investigated for the industrial application.

