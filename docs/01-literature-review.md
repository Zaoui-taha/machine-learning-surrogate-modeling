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
