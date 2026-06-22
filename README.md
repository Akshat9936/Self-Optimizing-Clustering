# Self-Optimal-Clustering (SOC)

A Python implementation of the **Self-Optimal Clustering (SOC)** algorithm, an advanced, mathematically optimized version of the Improved Mountain Clustering (IMC) technique. This repository translates the original MATLAB implementation into a modular, high-performance Python pipeline using `numpy`, `opencv`, and `scikit-learn`.

This algorithm is primarily designed for robust color image segmentation. It dynamically computes the optimal threshold function for clustering using Lagrange's interpolation polynomial, maximizing the Global Silhouette Index (GSI) to ensure superior cluster compactness and separation compared to traditional K-means, FCM, and standard IMC techniques.

## Mathematical Background

Unlike heuristic-based thresholding, SOC analytically optimizes the neighborhood threshold value ($\delta_m$) for each cluster. 

The potential value of each data point is calculated using the Mountain Function:
$$P_{m}^{r}=\sum_{j=1}^{n}\exp\left[-\left(\frac{d^{2}(\bar{x}^{r},\bar{x}^{j})}{\delta_{m}^{2}}\right)\right]$$

To optimize $\delta_m$, the algorithm relies on a sequence of interpolating polynomials to relate the threshold to the Silhouette Index ($S_t$) of the formed clusters. The Lagrange interpolation takes the form:
$$S_{t}=\sum_{m=1}^{M}S_{m}\prod_{k=1,k\neq m}^{M}\frac{(\delta_{t}-\delta_{k})}{(\delta_{m}-\delta_{k})}$$

By finding the roots of the derivative of this polynomial, the algorithm determines a scaling factor ($\beta_m$) that iteratively pushes the threshold toward a value that yields the maximum possible Silhouette Index (approaching 1.0).

## Repository Structure

The implementation is broken down into modular files for easy maintenance and algorithmic clarity:

* **`main.py`**: The driver script. Handles image loading (via local path or URL), matrix reshaping, and execution of the SOC and IMC pipelines. Visualizes the final segmented output using `matplotlib`.
* **`soc.py`**: Contains the core Self-Optimal Clustering logic, handling spatial normalization, mountain function potentials, and cluster center extraction.
* **`factorcal.py`**: The optimization engine. Runs the iterative loop (up to 10 iterations) to fit the Lagrange polynomial and extract the optimal scaling factors for the clustering thresholds.
* **`imc2.py`**: The baseline Improved Mountain Clustering (version 2) algorithm, utilizing a static heuristic factor for performance benchmarking against SOC.
* **`lagrangepoly.py`**: A custom mathematical utility to fit the Lagrange interpolation polynomial to a set of points and extract the roots of its derivative.
* **`slht.py`**: Computes individual cluster Silhouette values and the Global Silhouette Index (GSI) to validate cluster quality.

## Prerequisites

Ensure you have Python 3.7+ installed. Install the required dependencies using:

```bash
pip install numpy matplotlib scikit-learn opencv-python requests
```
## Usage

To run the clustering algorithm on an image, simply execute the main script. You can provide an image either via a direct web URL or a local file path.
```bash
main.py
```

Execution Flow:
* You will be prompted to enter an image URL (e.g., https://example.com/image.jpg).
* You will be prompted to enter the desired number of clusters ($M$).
* The script will sequentially run SOC, IMC1, and IMC2.
* Finally, it will print the Global Silhouette Scores (GSI) for all three methods and launch a matplotlib window displaying the original image alongside the segmented results.

Note: The time complexity of mountain clustering is heavily dependent on the number of data points $O(N^2)$. For high-resolution images, consider downsampling the image using cv2.resize() prior to clustering to optimize execution time. 

## References

This implementation is based on the framework detailed in the following IEEE paper:

Verma, N. K., & Roy, A. (2014). "Self-Optimal Clustering Technique Using Optimized Threshold Function." IEEE Systems Journal, 8(4), 1213-1226.
