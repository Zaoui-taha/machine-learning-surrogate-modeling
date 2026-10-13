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

## 4. Proposed Methodology & Experimental Design

The development of the surrogate model involved several stages, from experimental design and finite element data generation to mesh standardization, dimensionality reduction, and Gaussian Process Regression.

Particular attention was given to the methodological choices affecting the quality of the physical field representations and the predictive performance of the surrogate models.

### 4.1. Input Parameters and Experimental Design

The numerical model depends on a set of physical and geometrical parameters describing the manufacturing process.

For this study, **eight input parameters** were considered:

- **Six physical parameters** governing the behavior of the material and the physical conditions of the process.
- **Two geometrical parameters** controlling the shape of the computational domain.

The remaining parameters were held constant throughout the experiments.

Because generating high-fidelity simulations is computationally expensive, an efficient sampling strategy was required to construct a representative training dataset.

A **Latin Hypercube Sampling (LHS) design with optimized discrepancy** was selected to improve the coverage of the multidimensional parameter space.

The experimental dataset consisted of:

- **350 training simulations**
- **25 independent test simulations**

The training and test sets were generated using separate experimental designs to promote adequate coverage of the parameter space in both datasets.

The resulting samples were inspected through their marginal distributions and two-dimensional projections to assess their spatial distribution across the input domain.

### 4.2. High-Fidelity Simulation Data

For each sampled parameter configuration, a finite element simulation was performed to compute the corresponding physical fields.

The quantities of interest included:

- Temperature fields
- Pressure fields
- Velocity fields

These numerical solutions provided the reference data for training and evaluating the surrogate models.

However, the simulations were performed on geometrically varying computational domains, resulting in meshes with different spatial discretizations.

A geometrical standardization procedure was therefore required before applying dimensionality reduction and supervised learning.

### 4.3. Mesh Morphing Strategies and Reference Mesh Selection

One of the main methodological investigations concerned the choice of the reference domain used to standardize the computational meshes.

Two strategies were considered:

**Strategy A — Tutte Mapping to the Unit Disk**

This approach maps each computational mesh onto a common canonical domain using Tutte-based parametrization.

The unit disk provides a simple and standardized reference geometry, but the transformation may introduce substantial geometrical deformation relative to the original physical domain.

**Strategy B — RBF Mesh Morphing to a Representative Geometry**

This approach uses Radial Basis Function interpolation to map the computational meshes toward a reference domain selected from the available simulation geometries.

By choosing a geometrically representative domain, the transformation aims to limit geometrical distortion and preserve the spatial organization of the physical fields.

#### Reference Geometry Selection

Several candidate reference domains were evaluated through a quantitative comparison.

Five candidate geometries were selected from the training set and evaluated using approximately twenty representative computational meshes.

The comparison focused on criteria related to:

- Preservation of geometrical properties.
- Quality of the transformed meshes.
- Preservation of physical field regularity.
- Distortion introduced during field transport.

In particular, the spatial gradients of the physical fields were examined through an H1-based seminorm to evaluate how the transformations affected their regularity.

Since no candidate geometry consistently outperformed the others across all criteria, a compromise-based selection was adopted.

A **median representative geometry** was retained as the reference domain for further experiments.

#### Comparison of the Two Morphing Strategies

The unit-disk approach and the representative-geometry approach were subsequently compared in terms of physical field preservation and compatibility with dimensionality reduction.

The analysis showed that both strategies provided comparable overall preservation of the transported physical information.

However, **RBF morphing toward the median reference geometry produced fields that were more efficiently compressed using POD**, particularly for the geometrical coordinate fields.

This observation motivated the selection of the median reference geometry for the subsequent surrogate modeling experiments.

### 4.4. Physical Field Transport and Interpolation

Following mesh morphing, the physical fields associated with each simulation were transferred onto the transformed meshes and interpolated onto a common reference discretization.

The selected reference mesh contained **13,125 nodes**.

This procedure provided a consistent representation of all simulation outputs, allowing comparisons between physical fields and enabling dimensionality reduction.

Visual inspections were performed to verify that the interpolation and geometrical transformations preserved the main physical structures of the temperature, pressure, and velocity fields.

### 4.5. Dimensionality Reduction Using POD

Once the numerical fields were represented on the common reference mesh, Proper Orthogonal Decomposition was applied to reduce their dimensionality.

Two categories of information were compressed:

- **Geometrical information:** The spatial coordinate fields describing the original computational geometries.
- **Physical information:** The temperature, pressure, and velocity fields obtained from the finite element simulations.

The number of retained POD modes was investigated using two complementary criteria:

1. Cumulative explained variance.
2. Reconstruction error, evaluated using the Relative Root Mean Square Error (RRMSE).

Although explained variance provides an initial indication of the amount of information retained, reconstruction errors were also examined to avoid selecting an insufficient number of modes.

For the geometrical coordinate fields, **six POD modes were retained for each coordinate**, corresponding to the point beyond which the reconstruction errors and their variability tended to stabilize.

The physical fields were similarly represented through reduced modal coefficients, which subsequently served as the target outputs of the regression models.

### 4.6. Input Correlation Analysis

Before training the surrogate models, a correlation analysis was performed to investigate potential dependencies and redundancies between geometrical descriptors.

In particular, Pearson and Spearman correlation matrices were examined to assess relationships between the original geometrical parameters and the reduced POD coefficients.

The analysis revealed a strong redundancy between the first reduced coefficients of the two coordinate fields.

This observation motivated the removal of one redundant input variable from the geometry-based representation.

The analysis also highlighted that important geometrical information was not necessarily concentrated in the first POD modes, reinforcing the need to consider more than explained variance when selecting reduced descriptors.

### 4.7. Gaussian Process Surrogate Model Configurations

Two surrogate modeling formulations were developed and compared.

**Model A — Parametrized MMGP**

The first formulation directly uses the eight original input parameters:

- Six physical parameters.
- Two explicit geometrical parameters.

This representation exploits the fact that the computational geometries are fully described by a small number of known parameters.

Its main advantage is a compact input space, which is particularly relevant for Gaussian Process Regression.

**Model B — Classical MMGP**

The second formulation follows the original geometry-based MMGP approach more closely.

Instead of relying directly on the explicit geometrical parameters, the model uses reduced POD coefficients of the coordinate fields to describe geometrical variability.

This representation is more flexible and can potentially be extended to geometries without an explicit low-dimensional parametrization.

For both formulations, Gaussian Process models were trained to predict the reduced coefficients of the physical quantities of interest.

A **Matérn 5/2 covariance kernel** was adopted for the regression models.

Each output modal coefficient was predicted independently, and the corresponding physical fields were reconstructed using the POD basis.

The two formulations were subsequently evaluated to investigate the influence of geometrical representation on prediction accuracy.

The comparative results and limitations of these models are discussed in the following section.

## 5. Results & Model Comparison

The predictive performance of the developed surrogate models was evaluated using independent test simulations.

Two Gaussian Process-based surrogate modeling approaches were compared:

- **Parametrized MMGP:** Using six physical parameters and two explicit geometrical parameters as model inputs.
- **Classical MMGP:** Using physical parameters and POD-based geometrical descriptors extracted from the computational meshes.

The evaluation aimed to assess not only the ability of the models to reproduce the global behavior of the physical fields, but also their accuracy in regions characterized by strong spatial gradients and localized physical phenomena.

### 5.1. Evaluation Metrics

Three complementary metrics were considered to evaluate the quality of the predicted physical fields.

**Relative Root Mean Square Error (RRMSE)**

RRMSE measures the global reconstruction error relative to the magnitude of the reference field. It provides a normalized indicator for comparing predictions across physical quantities with different scales.

**Coefficient of Determination (R²)**

R² evaluates the ability of the surrogate model to reproduce the variability observed in the reference numerical solutions.

Values close to 1 indicate strong agreement between predicted and reference fields.

**Maximum Absolute Error**

The maximum absolute error measures the largest pointwise difference between the predicted and reference fields.

This metric is particularly important for the present application because localized prediction errors may be critical even when the overall reconstruction error remains relatively low.

Using both global and local metrics provides a more complete assessment of surrogate model reliability.

### 5.2. Temperature Field Prediction

Both surrogate models successfully reproduced the main spatial trends of the temperature field.

In particular, the predictions captured:

- The temperature increase near the upper boundary of the computational domain.
- The progressive temperature decrease toward the downstream region.
- The relatively colder regions located in the central and lower parts of the domain.

However, prediction errors were more pronounced in regions characterized by strong thermal gradients.

For the representative test simulation illustrated in the report, local discrepancies exceeding **20°C** were observed, despite the satisfactory reproduction of the overall temperature distribution.

The parametrized MMGP formulation provided a more accurate reconstruction of local thermal structures than the classical MMGP approach.

These observations highlight an important limitation: reproducing the global behavior of a physical field does not necessarily guarantee sufficient local accuracy for industrial decision-making.

### 5.3. Pressure Field Prediction

Pressure prediction represented a particularly challenging aspect of the study.

Unlike temperature and velocity, the pressure field exhibits strongly localized variations, with significant compression occurring primarily in a narrow region between the rotating cylinders.

Both models reproduced the general spatial behavior of the pressure field, but substantial prediction errors appeared in the region of maximum compression.

In the representative simulation presented in the report, local pressure errors reached approximately **100 bar**.

The classical MMGP model significantly underestimated the pressure in this critical region, while the parametrized MMGP provided better predictions with smaller local residuals.

Nevertheless, the remaining errors were still too large for reliable prediction of localized pressure phenomena.

This result demonstrates the difficulty of learning physical fields whose behavior is dominated by concentrated spatial structures.

### 5.4. Velocity Field Prediction

The surrogate models demonstrated comparatively strong performance in predicting the velocity field.

Both approaches successfully captured the main characteristics of the flow, including:

- Boundary-layer behavior near the rotating cylinders.
- Strong velocity gradients near the upper boundary.
- Relatively homogeneous low-velocity regions in the central domain.
- The progressive decrease in velocity toward the outlet.

Prediction residuals were mainly concentrated near the cylinder boundaries and other regions exhibiting significant velocity gradients.

For the illustrated test configuration, residual magnitudes were of the order of **0.2 m/s**, substantially less problematic than the large localized errors observed in the pressure field.

The parametrized MMGP consistently provided more accurate velocity predictions than the classical MMGP formulation.

### 5.5. Quantitative Comparison of Surrogate Models

The two modeling approaches were evaluated on the independent test dataset using RRMSE, R², and maximum absolute error.

The results showed that the **parametrized MMGP outperformed the classical MMGP across all three physical fields**.

**Temperature**

The parametrized formulation achieved moderately lower global reconstruction errors and a slightly higher R².

However, both models retained significant local errors, with the mean maximum absolute error reaching approximately 40°C.

**Pressure**

The difference between the two formulations was particularly pronounced for pressure prediction.

The parametrized MMGP achieved an R² greater than 0.95, whereas the classical formulation exhibited substantially weaker performance and considerable variability across test simulations.

Despite this improvement, large localized pressure errors remained, demonstrating that good global performance did not translate into sufficient accuracy in the critical compression region.

**Velocity**

The parametrized MMGP achieved an R² close to 1, together with lower global and local reconstruction errors than the classical model.

Overall, these results suggest that explicitly representing the geometry through a small number of known parameters is more effective for the investigated problem than relying on a higher-dimensional POD-based geometrical representation.

However, the greater flexibility of the classical MMGP formulation may remain useful for applications involving geometries without an explicit parametrization.

### 5.6. SHAP-Based Feature Importance and Input Selection

The relatively weak predictive performance of the classical MMGP motivated an additional investigation into the relevance and redundancy of its input variables.

A feature importance analysis based on **SHAP (SHapley Additive exPlanations)** was conducted to estimate the contribution of each input variable to the prediction of individual POD coefficients.

The analysis considered the physical parameters and the reduced geometrical coordinates.

Rather than selecting features using SHAP importance alone, a progressive feature selection procedure was implemented.

Geometrical descriptors were introduced according to their importance ranking, and the regression models were retrained to evaluate the influence of input dimensionality on predictive performance.

An initial reduced model containing **14 input variables**, including six physical parameters and eight selected geometrical descriptors, was retained.

A subsequent robustness evaluation on an additional dataset revealed that two geometrical descriptors, X̃₅ and X̃₆, negatively affected the overall generalization performance.

An ablation study was therefore performed by removing these descriptors individually and jointly.

Removing both variables improved the overall robustness of the model on the additional dataset, although some individual POD coefficient predictions deteriorated.

This analysis revealed an important limitation of feature selection based solely on average importance scores:

**A variable may be informative for certain outputs while simultaneously reducing the generalization performance of the overall model.**

The results emphasize the importance of combining interpretability methods with experimental validation when selecting surrogate model inputs.

### 5.7. Investigating the Limitations of POD Reconstruction

Despite the encouraging global prediction results, both surrogate models exhibited substantial errors in localized regions.

To investigate the origin of these errors, the quality of the POD representation was evaluated independently of the Gaussian Process regression stage.

The physical fields were reconstructed directly from their reduced POD coefficients, and reconstruction errors were analyzed as a function of the number of retained modes.

The study revealed two different behaviors.

**Global Reconstruction Accuracy**

Increasing the number of POD modes progressively reduced the global RRMSE on both training and test datasets.

However, the improvement became increasingly marginal as additional modes were included.

**Local Reconstruction Accuracy**

The maximum absolute reconstruction error exhibited a different behavior.

Although the first POD modes substantially reduced local errors, adding more modes did not consistently improve the reconstruction of strongly localized structures on unseen test simulations.

In particular, the maximum error on the test dataset tended to stabilize, while the training error continued to decrease.

For an illustrative temperature reconstruction, local errors of approximately **20°C persisted even when 30 POD modes were retained**.

These results indicate that increasing the number of POD modes is not sufficient to guarantee accurate reconstruction of localized physical phenomena.

The analysis also suggests that the limitations arise not only from the regression model but from the interaction between dimensionality reduction, reconstruction, and generalization to new simulations.

### 5.8. Main Findings and Motivation for Further Investigation

The experimental comparison led to several important conclusions:

1. Parametrized MMGP provided better predictive performance than classical MMGP for the investigated parametrized geometries.
2. Temperature and velocity fields were reconstructed with generally satisfactory global accuracy.
3. Pressure prediction remained challenging because of highly localized physical variations.
4. SHAP analysis and feature ablation highlighted the importance of controlling input redundancy and evaluating generalization performance.
5. POD-based representations effectively captured dominant spatial structures but remained limited in reproducing certain localized phenomena.

These findings motivated the investigation of **nonlinear dimensionality reduction using autoencoders**, with the objective of improving the representation of physical fields and reducing errors in critical localized regions.

The development and evaluation of this alternative approach are presented in the following section.
