## 5. Results & Model Comparison

The predictive performance of the developed surrogate models is evaluated using independent test simulations.

Two Gaussian Process-based surrogate modeling approaches are compared:

- **Parametrized MMGP:** using six physical parameters and two explicit geometrical parameters as model inputs.
- **Classical MMGP:** using physical parameters and POD-based geometrical descriptors extracted from the computational meshes.

The evaluation aims to assess not only the ability of the models to reproduce the global behavior of the physical fields, but also their accuracy in regions characterized by strong spatial gradients and localized physical phenomena.

### 5.1. Evaluation Metrics

Three complementary metrics are considered to evaluate the quality of the predicted physical fields.

**Relative Root Mean Square Error (RRMSE)**

RRMSE measures the global reconstruction error relative to the magnitude of the reference field. It provides a normalized indicator for comparing predictions across physical quantities with different scales.

**Coefficient of Determination (R²)**

R² evaluates the ability of the surrogate model to reproduce the variability observed in the reference numerical solutions.

Values close to 1 indicate strong agreement between predicted and reference fields.

**Maximum Absolute Error**

The maximum absolute error measures the largest pointwise difference between the predicted and reference fields.

This metric is particularly important in the present application because localized prediction errors may remain critical even when the overall reconstruction error is relatively low.

Using both global and local metrics provides a more complete assessment of surrogate model reliability.

### 5.2. Predicted vs. Real POD Contributions for Temperature

To further assess the predictive capability of the surrogate model, the predicted POD coefficients of the temperature field are compared with their corresponding reference values.

The following figure illustrates the agreement between predicted and real values of the retained temperature POD contributions.

<p align="center">
  <img src="../figures/temperature_pod_predicted_vs_real.png"
       alt="Predicted versus real temperature POD coefficients"
       width="650">
</p>

<p align="center">
  <em>Figure 1. Predicted versus real values of the retained POD coefficients for the temperature field.</em>
</p>

This figure shows that the surrogate model captures the dominant modal contributions with good overall agreement, although some discrepancies remain for specific modes or observations.

### 5.3. Temperature Field Prediction

Both surrogate models successfully reproduce the main spatial trends of the temperature field.

In particular, the predictions capture:

- the temperature increase near the upper boundary of the computational domain,
- the progressive temperature decrease toward the downstream region,
- the relatively colder regions located in the central and lower parts of the domain.

However, prediction errors become more pronounced in regions characterized by strong thermal gradients.

For representative test simulations, local discrepancies exceed **20°C**, despite the satisfactory reproduction of the overall temperature distribution.

The parametrized MMGP formulation provides a more accurate reconstruction of local thermal structures than the classical MMGP approach.

To quantify this behavior, the three evaluation metrics are compared for the temperature field in the following figure.

<p align="center">
  <img src="../figures/temperature_metrics_comparison.png"
       alt="Comparison of RRMSE, R2, and maximum absolute error for the temperature field"
       width="650">
</p>

<p align="center">
  <em>Figure 2. Comparison of RRMSE, R², and maximum absolute error for the temperature field.</em>
</p>

These observations highlight an important limitation: reproducing the global behavior of a physical field does not necessarily guarantee sufficient local accuracy for industrial decision-making.

### 5.4. Pressure Field Prediction

Pressure prediction represents a particularly challenging aspect of the study.

Unlike temperature and velocity, the pressure field exhibits strongly localized variations, with significant compression occurring primarily in a narrow region between the rotating cylinders.

Both models reproduce the general spatial behavior of the pressure field, but substantial prediction errors appear in the region of maximum compression.

For representative simulations, local pressure errors reach approximately **100 bar**.

The classical MMGP model significantly underestimates the pressure in this critical region, while the parametrized MMGP provides better predictions with smaller local residuals.

Nevertheless, the remaining errors remain too large for reliable prediction of localized pressure phenomena.

The following figure compares the three evaluation metrics for the pressure field.

<p align="center">
  <img src="../figures/pressure_metrics_comparison.png"
       alt="Comparison of RRMSE, R2, and maximum absolute error for the pressure field"
       width="650">
</p>

<p align="center">
  <em>Figure 3. Comparison of RRMSE, R², and maximum absolute error for the pressure field.</em>
</p>

This result demonstrates the difficulty of learning physical fields whose behavior is dominated by strongly concentrated spatial structures.

### 5.5. Velocity Field Prediction

The surrogate models demonstrate comparatively strong performance in predicting the velocity field.

Both approaches capture the main characteristics of the flow, including:

- boundary-layer behavior near the rotating cylinders,
- strong velocity gradients near the upper boundary,
- relatively homogeneous low-velocity regions in the central domain,
- the progressive decrease in velocity toward the outlet.

Prediction residuals concentrate mainly near the cylinder boundaries and other regions exhibiting significant velocity gradients.

For representative test configurations, residual magnitudes are of the order of **0.2 m/s**, which is substantially less problematic than the large localized errors observed in the pressure field.

The parametrized MMGP consistently provides more accurate velocity predictions than the classical MMGP formulation.

The following figure compares the three evaluation metrics for the velocity field.

<p align="center">
  <img src="../figures/velocity_metrics_comparison.png"
       alt="Comparison of RRMSE, R2, and maximum absolute error for the velocity field"
       width="650">
</p>

<p align="center">
  <em>Figure 4. Comparison of RRMSE, R², and maximum absolute error for the velocity field.</em>
</p>

### 5.6. Quantitative Comparison of Surrogate Models

The two modeling approaches are evaluated on the independent test dataset using RRMSE, R², and maximum absolute error.

The results show that the **parametrized MMGP outperforms the classical MMGP across all three physical fields**.

- For **temperature**, the parametrized formulation achieves moderately lower global reconstruction errors and a slightly higher R².
- For **pressure**, the difference between the two formulations becomes particularly pronounced, and the parametrized MMGP reaches much better predictive performance.
- For **velocity**, the parametrized MMGP achieves an R² close to 1, together with lower global and local reconstruction errors.

Overall, these results suggest that explicitly representing the geometry through a small number of known parameters is more effective for the investigated problem than relying on a higher-dimensional POD-based geometrical representation.

However, the greater flexibility of the classical MMGP formulation remains potentially useful for applications involving geometries without an explicit parametrization.

### 5.7. SHAP-Based Feature Importance and Input Selection

The relatively weak predictive performance of the classical MMGP motivates an additional investigation into the relevance and redundancy of its input variables.

A feature importance analysis based on **SHAP (SHapley Additive exPlanations)** is conducted to estimate the contribution of each input variable to the prediction of individual POD coefficients.

The analysis considers the physical parameters and the reduced geometrical coordinates.

Rather than selecting features using SHAP importance alone, a progressive feature selection procedure is implemented.

Geometrical descriptors are introduced according to their importance ranking, and the regression models are retrained to evaluate the influence of input dimensionality on predictive performance.

An initial reduced model containing **14 input variables**, including six physical parameters and eight selected geometrical descriptors, is retained.

A subsequent robustness evaluation on an additional dataset reveals that two geometrical descriptors, **X̃₅** and **X̃₆**, negatively affect the overall generalization performance.

An ablation study is therefore performed by removing these descriptors individually and jointly.

The following figure summarizes the effect of the ablation study on the predictive performance of the model.

<p align="center">
  <img src="../figures/shap_ablation_study.png"
       alt="Ablation study based on SHAP-selected variables"
       width="650">
</p>

<p align="center">
  <em>Figure 5. Effect of the ablation study on model performance after removing SHAP-selected geometrical descriptors.</em>
</p>

Removing both variables improves the overall robustness of the model on the additional dataset, although some individual POD coefficient predictions deteriorate.

This analysis reveals an important limitation of feature selection based solely on average importance scores:

**A variable may be informative for certain outputs while simultaneously reducing the generalization performance of the overall model.**

The results emphasize the importance of combining interpretability methods with experimental validation when selecting surrogate model inputs.

### 5.8. Investigating the Limitations of POD Reconstruction

Despite the encouraging global prediction results, both surrogate models exhibit substantial errors in localized regions.

To investigate the origin of these errors, the quality of the POD representation is evaluated independently of the Gaussian Process regression stage.

The physical fields are reconstructed directly from their reduced POD coefficients, and reconstruction errors are analyzed as a function of the number of retained modes.

The study reveals two different behaviors.

**Global Reconstruction Accuracy**

Increasing the number of POD modes progressively reduces the global RRMSE on both training and test datasets.

However, the improvement becomes increasingly marginal as additional modes are included.

**Local Reconstruction Accuracy**

The maximum absolute reconstruction error exhibits a different behavior.

Although the first POD modes substantially reduce local errors, adding more modes does not consistently improve the reconstruction of strongly localized structures on unseen test simulations.

In particular, the maximum error on the test dataset tends to stabilize, while the training error continues to decrease.

The following figure illustrates this stabilization of the POD reconstruction error on the test set.

<p align="center">
  <img src="../figures/pod_error_stabilization_test_set.png"
       alt="Stabilization of POD reconstruction error on the test set"
       width="650">
</p>

<p align="center">
  
  <em>Figure 6. Evolution of POD reconstruction error as a function of the number of retained modes, showing the stabilization of the test error.</em>
</p>

For illustrative temperature reconstructions, local errors of approximately **20°C** persist even when a relatively large number of POD modes is retained.

These results indicate that simply increasing the number of POD modes is not sufficient to guarantee accurate reconstruction of localized physical phenomena.

The analysis also suggests that the limitations arise not only from the regression model but from the interaction between dimensionality reduction, reconstruction, and generalization to new simulations.

### 5.9. Main Findings and Motivation for Further Investigation

The experimental comparison leads to several important conclusions:

1. Parametrized MMGP provides better predictive performance than classical MMGP for the investigated parametrized geometries.
2. Temperature and velocity fields are reconstructed with generally satisfactory global accuracy.
3. Pressure prediction remains challenging because of highly localized physical variations.
4. SHAP analysis and feature ablation highlight the importance of controlling input redundancy and evaluating generalization performance.
5. POD-based representations effectively capture dominant spatial structures but remain limited in reproducing certain localized phenomena.

These findings motivate the investigation of **nonlinear dimensionality reduction using autoencoders**, with the objective of improving the representation of physical fields and reducing errors in critical localized regions.

The development and evaluation of this alternative approach are presented in the following section.
