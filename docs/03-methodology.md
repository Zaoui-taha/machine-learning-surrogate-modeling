## Proposed Methodology & Experimental Design

The development of the surrogate model involved several stages, from experimental design and finite element data generation to mesh standardization, dimensionality reduction, and Gaussian Process Regression.

Particular attention was given to the methodological choices affecting the quality of the physical field representations and the predictive performance of the surrogate models.

### 1. Input Parameters and Experimental Design

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

### 2. High-Fidelity Simulation Data

For each sampled parameter configuration, a finite element simulation was performed to compute the corresponding physical fields.

The quantities of interest included:

- Temperature fields
- Pressure fields
- Velocity fields

These numerical solutions provided the reference data for training and evaluating the surrogate models.

However, the simulations were performed on geometrically varying computational domains, resulting in meshes with different spatial discretizations.

A geometrical standardization procedure was therefore required before applying dimensionality reduction and supervised learning.

### 3. Mesh Morphing Strategies and Reference Mesh Selection

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

### 4. Physical Field Transport and Interpolation

Following mesh morphing, the physical fields associated with each simulation were transferred onto the transformed meshes and interpolated onto a common reference discretization.

The selected reference mesh contained **13,125 nodes**.

This procedure provided a consistent representation of all simulation outputs, allowing comparisons between physical fields and enabling dimensionality reduction.

Visual inspections were performed to verify that the interpolation and geometrical transformations preserved the main physical structures of the temperature, pressure, and velocity fields.

### 5. Dimensionality Reduction Using POD

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

### 6. Input Correlation Analysis

Before training the surrogate models, a correlation analysis was performed to investigate potential dependencies and redundancies between geometrical descriptors.

In particular, Pearson and Spearman correlation matrices were examined to assess relationships between the original geometrical parameters and the reduced POD coefficients.

The analysis revealed a strong redundancy between the first reduced coefficients of the two coordinate fields.

This observation motivated the removal of one redundant input variable from the geometry-based representation.

The analysis also highlighted that important geometrical information was not necessarily concentrated in the first POD modes, reinforcing the need to consider more than explained variance when selecting reduced descriptors.

### 7. Gaussian Process Surrogate Model Configurations

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
