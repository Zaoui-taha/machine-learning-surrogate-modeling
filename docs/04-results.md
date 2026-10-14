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
