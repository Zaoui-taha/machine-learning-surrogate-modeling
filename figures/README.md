# Scientific Figures

This directory contains the figures used in the project documentation. Filenames are lowercase English descriptions in `kebab-case` (words separated by hyphens). Figures retain their original visual content.

| File | Scientific content |
|---|---|
| [`autoencoder-activation-function-comparison.png`](autoencoder-activation-function-comparison.png) | Validation reconstruction error for tested autoencoder activation functions. |
| [`autoencoder-architecture-max-error-comparison.png`](autoencoder-architecture-max-error-comparison.png) | Maximum reconstruction error across autoencoder architectures and latent-space dimensions. |
| [`autoencoder-hybrid-loss-components.png`](autoencoder-hybrid-loss-components.png) | Total autoencoder training loss and its MSE and weighted LogSumExp components. |
| [`autoencoder-training-validation-convergence.png`](autoencoder-training-validation-convergence.png) | Autoencoder training and validation error curves across optimization epochs. |
| [`autoencoder-vs-pod-reconstruction-errors.png`](autoencoder-vs-pod-reconstruction-errors.png) | Distribution of maximum absolute reconstruction errors for a POD representation and an autoencoder. |
| [`heat-equation-1d-surrogate-reconstruction.png`](heat-equation-1d-surrogate-reconstruction.png) | Surrogate reconstruction compared with a reference one-dimensional heat-equation solution. |
| [`heat-equation-1d-time-evolution.png`](heat-equation-1d-time-evolution.png) | Solutions of a one-dimensional heat equation at several time increments. |
| [`heat-equation-pod-explained-variance.png`](heat-equation-pod-explained-variance.png) | Individual and cumulative explained variance used to select heat-equation POD modes. |
| [`physical-fields-after-mesh-morphing.png`](physical-fields-after-mesh-morphing.png) | Temperature, pressure, and velocity fields after mesh morphing and transfer. |
| [`pressure-surrogate-model-metrics.png`](pressure-surrogate-model-metrics.png) | Comparison of pressure-field RRMSE, R², and maximum absolute error for two MMGP formulations. |
| [`rbf-mesh-morphing-reference-domain.png`](rbf-mesh-morphing-reference-domain.png) | Example of mesh morphing toward a reference geometry using radial basis functions. |
| [`representative-median-reference-geometry.png`](representative-median-reference-geometry.png) | Location of the selected representative geometry among sampled geometry parameters. |
| [`shap-feature-importance-physical-fields.png`](shap-feature-importance-physical-fields.png) | SHAP feature importance for temperature, pressure, and velocity POD outputs. |
| [`shap-feature-selection-pod-coefficients.png`](shap-feature-selection-pod-coefficients.png) | Effect of the number of SHAP-ranked input features on POD regression error. |
| [`temperature-pod-max-error-vs-modes.png`](temperature-pod-max-error-vs-modes.png) | Training and test maximum temperature POD reconstruction errors versus mode count. |
| [`temperature-pod-residual-fields-vs-modes.png`](temperature-pod-residual-fields-vs-modes.png) | Temperature-field POD reconstruction residuals for a test simulation at several mode counts. |
| [`temperature-surrogate-model-metrics.png`](temperature-surrogate-model-metrics.png) | Comparison of temperature-field RRMSE, R², and maximum absolute error for two MMGP formulations. |
| [`temperature-three-surrogate-models-comparison.png`](temperature-three-surrogate-models-comparison.png) | Comparison of temperature-field performance across three surrogate-model formulations. |
| [`training-test-design-of-experiments.png`](training-test-design-of-experiments.png) | Pairwise projections of sampled inputs in the design of experiments. |
| [`tutte-unit-disk-reference-mesh.png`](tutte-unit-disk-reference-mesh.png) | Triangulated unit-disk mesh serving as the reference domain for Tutte mapping. |
| [`velocity-surrogate-model-metrics.png`](velocity-surrogate-model-metrics.png) | Comparison of velocity-field RRMSE, R², and maximum absolute error for two MMGP formulations. |
| [`x-coordinate-pod-rbf-vs-tutte.png`](x-coordinate-pod-rbf-vs-tutte.png) | POD reduction errors for the x-coordinate field under RBF and Tutte mappings. |
| [`y-coordinate-pod-rbf-vs-tutte.png`](y-coordinate-pod-rbf-vs-tutte.png) | POD reduction errors for the y-coordinate field under RBF and Tutte mappings. |

For the detailed scientific context, consult the relevant files in [`docs/`](../docs/).
