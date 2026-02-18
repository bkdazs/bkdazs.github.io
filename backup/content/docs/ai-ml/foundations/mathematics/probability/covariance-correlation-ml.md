---
title: "Covariance, Correlation, and Multivariate Distributions in ML"
description: "Master covariance matrices, correlation analysis, and multivariate distributions for machine learning. Learn practical applications with Python examples."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 5
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["covariance", "correlation", "multivariate gaussian", "PCA", "machine learning"]
series: ["Mathematics for ML"]
---

# Covariance, Correlation, and Multivariate Distributions in ML

**"In high dimensions, relationships between variables matter as much as the variables themselves."**

Understanding how features relate to each other is crucial for machine learning. Covariance and correlation capture these relationships, while multivariate distributions model them probabilistically. This guide covers everything from basics to advanced applications.

---

## Why Covariance Matters in ML

### Where Covariance Appears

| ML Application | Role of Covariance |
|----------------|-------------------|
| PCA | Eigendecomposition of covariance matrix |
| Gaussian Processes | Covariance kernel defines similarity |
| Kalman Filter | State covariance for uncertainty |
| Mahalanobis Distance | Accounts for variable correlations |
| Multivariate Gaussian | Defines the shape of the distribution |
| Feature Selection | Detect redundant correlated features |

```
Feature Correlation in ML:
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   Uncorrelated Features    vs    Correlated Features   │
│                                                         │
│         * *                          *  *              │
│       *   * *                      *   *               │
│      *  *  *                      *   *                │
│       * *   *                    *   *                 │
│         *                       *   *                  │
│                                                         │
│   Independent info              Redundant info          │
│   Good for models               May need reduction      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Covariance: Measuring Linear Relationships

### Definition

For two random variables X and Y:

$$\text{Cov}(X, Y) = E[(X - \mu_X)(Y - \mu_Y)] = E[XY] - E[X]E[Y]$$

**Intuition**: Covariance measures how much X and Y vary together.

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats

def covariance_intuition():
    """Visualize covariance intuition."""
    
    np.random.seed(42)
    n = 200
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    scenarios = [
        ("Positive Covariance", np.array([[1, 0.8], [0.8, 1]])),
        ("Zero Covariance", np.array([[1, 0], [0, 1]])),
        ("Negative Covariance", np.array([[1, -0.8], [-0.8, 1]])),
    ]
    
    for ax, (title, cov_matrix) in zip(axes, scenarios):
        data = np.random.multivariate_normal([0, 0], cov_matrix, n)
        
        cov_xy = np.cov(data.T)[0, 1]
        
        ax.scatter(data[:, 0], data[:, 1], alpha=0.5)
        ax.axhline(0, color='gray', linestyle='--', alpha=0.3)
        ax.axvline(0, color='gray', linestyle='--', alpha=0.3)
        
        # Quadrant annotations
        ax.text(1.5, 1.5, '+×+', fontsize=12, ha='center')
        ax.text(-1.5, 1.5, '−×+', fontsize=12, ha='center')
        ax.text(-1.5, -1.5, '−×−', fontsize=12, ha='center')
        ax.text(1.5, -1.5, '+×−', fontsize=12, ha='center')
        
        ax.set_xlabel('X')
        ax.set_ylabel('Y')
        ax.set_title(f'{title}\nCov(X,Y) = {cov_xy:.2f}')
        ax.set_xlim(-3, 3)
        ax.set_ylim(-3, 3)
        ax.set_aspect('equal')
    
    plt.tight_layout()
    plt.show()
    
    print("Covariance Intuition:")
    print("• Positive: When X is above mean, Y tends to be above mean")
    print("• Zero: No linear relationship")
    print("• Negative: When X is above mean, Y tends to be below mean")

covariance_intuition()
```

### Computing Covariance

```python
def compute_covariance_example():
    """Different ways to compute covariance."""
    
    np.random.seed(42)
    
    # Sample data
    n = 1000
    x = np.random.randn(n)
    y = 0.7 * x + 0.3 * np.random.randn(n)  # y depends on x
    
    # Method 1: From definition
    cov_manual = np.mean((x - x.mean()) * (y - y.mean()))
    
    # Method 2: Using E[XY] - E[X]E[Y]
    cov_formula = np.mean(x * y) - np.mean(x) * np.mean(y)
    
    # Method 3: NumPy (unbiased, uses n-1)
    cov_numpy = np.cov(x, y)[0, 1]
    
    # Method 4: Biased (uses n)
    cov_biased = np.cov(x, y, bias=True)[0, 1]
    
    print("Covariance Computation")
    print("=" * 50)
    print(f"Manual (biased):     {cov_manual:.6f}")
    print(f"Formula (biased):    {cov_formula:.6f}")
    print(f"NumPy (unbiased):    {cov_numpy:.6f}")
    print(f"NumPy (biased):      {cov_biased:.6f}")
    
    print("\nNote: Unbiased estimator uses n-1 (Bessel's correction)")

compute_covariance_example()
```

### Covariance Properties

| Property | Formula |
|----------|---------|
| Symmetry | $\text{Cov}(X, Y) = \text{Cov}(Y, X)$ |
| Variance | $\text{Cov}(X, X) = \text{Var}(X)$ |
| Scaling | $\text{Cov}(aX, bY) = ab \cdot \text{Cov}(X, Y)$ |
| Addition | $\text{Cov}(X + Y, Z) = \text{Cov}(X, Z) + \text{Cov}(Y, Z)$ |
| Independence | If X, Y independent: $\text{Cov}(X, Y) = 0$ |

**Warning**: $\text{Cov}(X, Y) = 0$ does NOT imply independence!

```python
def zero_cov_not_independent():
    """Show that zero covariance doesn't mean independence."""
    
    np.random.seed(42)
    n = 1000
    
    # X is standard normal
    x = np.random.randn(n)
    
    # Y = X² has zero covariance with X but is clearly dependent!
    y = x ** 2
    
    cov_xy = np.cov(x, y)[0, 1]
    
    fig, ax = plt.subplots(figsize=(8, 6))
    
    ax.scatter(x, y, alpha=0.5)
    ax.set_xlabel('X')
    ax.set_ylabel('Y = X²')
    ax.set_title(f'Zero Covariance but NOT Independent!\nCov(X, X²) = {cov_xy:.4f}')
    
    plt.tight_layout()
    plt.show()
    
    print("Cov(X, X²) ≈ 0, but Y is completely determined by X!")
    print("Zero covariance only means no LINEAR relationship.")

zero_cov_not_independent()
```

---

## Correlation: Standardized Covariance

### Pearson Correlation Coefficient

$$\rho_{X,Y} = \frac{\text{Cov}(X, Y)}{\sigma_X \sigma_Y}$$

**Key properties**:
- Always between -1 and +1
- Scale-invariant (unitless)
- Measures **linear** relationship strength

```python
def correlation_examples():
    """Different correlation scenarios."""
    
    np.random.seed(42)
    n = 200
    
    fig, axes = plt.subplots(2, 4, figsize=(16, 8))
    
    correlations = [
        (1.0, "Perfect Positive"),
        (0.8, "Strong Positive"),
        (0.4, "Weak Positive"),
        (0.0, "No Correlation"),
        (-0.4, "Weak Negative"),
        (-0.8, "Strong Negative"),
        (-1.0, "Perfect Negative"),
        (0.0, "Nonlinear (ρ=0)"),  # Special case
    ]
    
    for ax, (target_corr, title) in zip(axes.flat, correlations):
        if title == "Nonlinear (ρ=0)":
            # Quadratic relationship
            x = np.random.uniform(-2, 2, n)
            y = x ** 2 + np.random.randn(n) * 0.2
        else:
            # Generate correlated data
            if abs(target_corr) == 1:
                x = np.random.randn(n)
                y = target_corr * x
            else:
                cov_matrix = [[1, target_corr], [target_corr, 1]]
                data = np.random.multivariate_normal([0, 0], cov_matrix, n)
                x, y = data[:, 0], data[:, 1]
        
        actual_corr = np.corrcoef(x, y)[0, 1]
        
        ax.scatter(x, y, alpha=0.5, s=20)
        ax.set_title(f'{title}\nρ = {actual_corr:.2f}')
        ax.set_xlabel('X')
        ax.set_ylabel('Y')
    
    plt.tight_layout()
    plt.show()

correlation_examples()
```

### Correlation vs Causation

```python
def correlation_not_causation():
    """Famous spurious correlations."""
    
    print("Correlation ≠ Causation")
    print("=" * 50)
    
    spurious = [
        ("Ice cream sales", "Drowning deaths", "Both caused by hot weather"),
        ("Shoe size", "Reading ability", "Both caused by age"),
        ("# of firefighters", "Fire damage", "Both caused by fire severity"),
    ]
    
    print("\nSpurious Correlations:")
    for var1, var2, explanation in spurious:
        print(f"  • {var1} ↔ {var2}")
        print(f"    Explanation: {explanation}\n")
    
    print("Always look for confounding variables!")

correlation_not_causation()
```

### Types of Correlation

```python
def correlation_types():
    """Compare Pearson, Spearman, and Kendall correlations."""
    
    np.random.seed(42)
    n = 100
    
    # Linear relationship
    x_linear = np.random.randn(n)
    y_linear = 2 * x_linear + np.random.randn(n) * 0.5
    
    # Monotonic but nonlinear
    x_mono = np.random.uniform(0, 10, n)
    y_mono = np.exp(0.3 * x_mono) + np.random.randn(n) * 2
    
    # With outliers
    x_outlier = np.random.randn(n)
    y_outlier = 0.8 * x_outlier + np.random.randn(n) * 0.3
    # Add outliers
    x_outlier[:5] = [3, -3, 4, -4, 5]
    y_outlier[:5] = [-3, 3, -4, 4, -5]
    
    datasets = [
        ("Linear", x_linear, y_linear),
        ("Monotonic Nonlinear", x_mono, y_mono),
        ("With Outliers", x_outlier, y_outlier),
    ]
    
    print("Comparison of Correlation Coefficients")
    print("=" * 60)
    print(f"{'Dataset':<20} {'Pearson':>10} {'Spearman':>10} {'Kendall':>10}")
    print("-" * 60)
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    for ax, (name, x, y) in zip(axes, datasets):
        pearson = stats.pearsonr(x, y)[0]
        spearman = stats.spearmanr(x, y)[0]
        kendall = stats.kendalltau(x, y)[0]
        
        print(f"{name:<20} {pearson:>10.3f} {spearman:>10.3f} {kendall:>10.3f}")
        
        ax.scatter(x, y, alpha=0.6)
        ax.set_xlabel('X')
        ax.set_ylabel('Y')
        ax.set_title(f'{name}\nPearson={pearson:.2f}, Spearman={spearman:.2f}')
    
    plt.tight_layout()
    plt.show()
    
    print("\nKey differences:")
    print("• Pearson: Measures LINEAR relationship")
    print("• Spearman: Measures MONOTONIC relationship (rank-based)")
    print("• Kendall: Also rank-based, more robust to outliers")

correlation_types()
```

---

## The Covariance Matrix

### Definition

For a random vector $\mathbf{X} = [X_1, X_2, ..., X_p]^T$:

$$\Sigma = \text{Cov}(\mathbf{X}) = E[(\mathbf{X} - \boldsymbol{\mu})(\mathbf{X} - \boldsymbol{\mu})^T]$$

$$\Sigma = \begin{bmatrix} 
\text{Var}(X_1) & \text{Cov}(X_1, X_2) & \cdots & \text{Cov}(X_1, X_p) \\
\text{Cov}(X_2, X_1) & \text{Var}(X_2) & \cdots & \text{Cov}(X_2, X_p) \\
\vdots & \vdots & \ddots & \vdots \\
\text{Cov}(X_p, X_1) & \text{Cov}(X_p, X_2) & \cdots & \text{Var}(X_p)
\end{bmatrix}$$

```python
def covariance_matrix_example():
    """Visualize covariance matrix."""
    
    from sklearn.datasets import load_iris
    import seaborn as sns
    
    # Load Iris dataset
    iris = load_iris()
    X = iris.data
    feature_names = iris.feature_names
    
    # Compute covariance matrix
    cov_matrix = np.cov(X.T)
    
    # Correlation matrix (standardized)
    corr_matrix = np.corrcoef(X.T)
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 6))
    
    # Covariance heatmap
    ax = axes[0]
    im = ax.imshow(cov_matrix, cmap='coolwarm')
    ax.set_xticks(range(4))
    ax.set_yticks(range(4))
    ax.set_xticklabels([f.replace(' (cm)', '') for f in feature_names], rotation=45, ha='right')
    ax.set_yticklabels([f.replace(' (cm)', '') for f in feature_names])
    plt.colorbar(im, ax=ax)
    
    # Add values
    for i in range(4):
        for j in range(4):
            ax.text(j, i, f'{cov_matrix[i,j]:.2f}', ha='center', va='center')
    
    ax.set_title('Covariance Matrix')
    
    # Correlation heatmap
    ax = axes[1]
    im = ax.imshow(corr_matrix, cmap='coolwarm', vmin=-1, vmax=1)
    ax.set_xticks(range(4))
    ax.set_yticks(range(4))
    ax.set_xticklabels([f.replace(' (cm)', '') for f in feature_names], rotation=45, ha='right')
    ax.set_yticklabels([f.replace(' (cm)', '') for f in feature_names])
    plt.colorbar(im, ax=ax)
    
    for i in range(4):
        for j in range(4):
            ax.text(j, i, f'{corr_matrix[i,j]:.2f}', ha='center', va='center')
    
    ax.set_title('Correlation Matrix')
    
    plt.tight_layout()
    plt.show()
    
    print("Covariance Matrix Properties:")
    print(f"• Shape: {cov_matrix.shape}")
    print(f"• Symmetric: {np.allclose(cov_matrix, cov_matrix.T)}")
    print(f"• Positive Semi-Definite: All eigenvalues ≥ 0")
    print(f"  Eigenvalues: {np.linalg.eigvalsh(cov_matrix).round(2)}")

covariance_matrix_example()
```

### Covariance Matrix Properties

| Property | Description |
|----------|-------------|
| Symmetric | $\Sigma = \Sigma^T$ |
| Positive Semi-Definite | All eigenvalues ≥ 0 |
| Diagonal | Variances of individual variables |
| Off-diagonal | Pairwise covariances |
| Determinant | "Generalized variance" |
| Trace | Sum of variances |

---

## Multivariate Gaussian Distribution

### The PDF

$$p(\mathbf{x}) = \frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}} \exp\left(-\frac{1}{2}(\mathbf{x}-\boldsymbol{\mu})^T \Sigma^{-1} (\mathbf{x}-\boldsymbol{\mu})\right)$$

The **covariance matrix Σ** determines the shape and orientation of the distribution.

```python
def multivariate_gaussian_visualization():
    """Visualize multivariate Gaussian with different covariances."""
    
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    
    covariances = [
        ([[1, 0], [0, 1]], "Identity (spherical)"),
        ([[2, 0], [0, 0.5]], "Diagonal (axis-aligned)"),
        ([[1, 0.8], [0.8, 1]], "Positive correlation"),
        ([[1, -0.8], [-0.8, 1]], "Negative correlation"),
        ([[2, 1], [1, 2]], "Tilted ellipse"),
        ([[0.5, 0], [0, 2]], "Stretched vertically"),
    ]
    
    x = np.linspace(-4, 4, 100)
    y = np.linspace(-4, 4, 100)
    X, Y = np.meshgrid(x, y)
    pos = np.dstack((X, Y))
    
    mean = [0, 0]
    
    for ax, (cov, title) in zip(axes.flat, covariances):
        cov = np.array(cov)
        rv = stats.multivariate_normal(mean, cov)
        Z = rv.pdf(pos)
        
        ax.contourf(X, Y, Z, levels=20, cmap='viridis')
        ax.contour(X, Y, Z, levels=10, colors='white', alpha=0.3)
        
        # Draw eigenvectors
        eigenvalues, eigenvectors = np.linalg.eigh(cov)
        for i, (val, vec) in enumerate(zip(eigenvalues, eigenvectors.T)):
            ax.arrow(0, 0, vec[0]*np.sqrt(val)*2, vec[1]*np.sqrt(val)*2, 
                    head_width=0.1, head_length=0.1, fc='red', ec='red')
        
        ax.set_xlabel('X₁')
        ax.set_ylabel('X₂')
        ax.set_title(f'{title}\nΣ = {cov.tolist()}')
        ax.set_aspect('equal')
        ax.set_xlim(-4, 4)
        ax.set_ylim(-4, 4)
    
    plt.tight_layout()
    plt.show()

multivariate_gaussian_visualization()
```

### Sampling from Multivariate Gaussian

```python
def sample_multivariate_gaussian():
    """Different ways to sample from multivariate Gaussian."""
    
    np.random.seed(42)
    
    mean = np.array([1, 2])
    cov = np.array([[2, 0.8], [0.8, 1]])
    n_samples = 1000
    
    # Method 1: Using numpy
    samples_np = np.random.multivariate_normal(mean, cov, n_samples)
    
    # Method 2: Using Cholesky decomposition
    L = np.linalg.cholesky(cov)
    z = np.random.randn(n_samples, 2)
    samples_chol = mean + z @ L.T
    
    # Method 3: Using eigendecomposition
    eigenvalues, eigenvectors = np.linalg.eigh(cov)
    samples_eig = mean + np.random.randn(n_samples, 2) @ (eigenvectors * np.sqrt(eigenvalues))
    
    # Verify
    print("Sampling from Multivariate Gaussian")
    print("=" * 50)
    print(f"True mean: {mean}")
    print(f"True cov:\n{cov}")
    
    for name, samples in [("NumPy", samples_np), 
                          ("Cholesky", samples_chol), 
                          ("Eigen", samples_eig)]:
        print(f"\n{name}:")
        print(f"  Sample mean: {samples.mean(axis=0).round(3)}")
        print(f"  Sample cov:\n{np.cov(samples.T).round(3)}")

sample_multivariate_gaussian()
```

---

## Applications in Machine Learning

### 1. PCA: Eigendecomposition of Covariance

```python
def pca_covariance_connection():
    """Show PCA as eigendecomposition of covariance matrix."""
    
    from sklearn.decomposition import PCA
    from sklearn.preprocessing import StandardScaler
    from sklearn.datasets import load_iris
    
    # Load data
    iris = load_iris()
    X = iris.data
    
    # Standardize
    X_centered = X - X.mean(axis=0)
    
    # Compute covariance matrix
    cov_matrix = np.cov(X_centered.T)
    
    # Eigendecomposition
    eigenvalues, eigenvectors = np.linalg.eigh(cov_matrix)
    
    # Sort by eigenvalue (descending)
    idx = np.argsort(eigenvalues)[::-1]
    eigenvalues = eigenvalues[idx]
    eigenvectors = eigenvectors[:, idx]
    
    # Compare with sklearn PCA
    pca = PCA()
    pca.fit(X_centered)
    
    print("PCA = Eigendecomposition of Covariance Matrix")
    print("=" * 60)
    print("\nEigenvalues (variance explained by each PC):")
    print(f"  Manual:  {eigenvalues.round(3)}")
    print(f"  sklearn: {pca.explained_variance_.round(3)}")
    
    print("\nVariance ratio:")
    print(f"  Manual:  {(eigenvalues / eigenvalues.sum()).round(3)}")
    print(f"  sklearn: {pca.explained_variance_ratio_.round(3)}")
    
    # Visualize
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    ax = axes[0]
    ax.bar(range(1, 5), eigenvalues, alpha=0.7)
    ax.set_xlabel('Principal Component')
    ax.set_ylabel('Eigenvalue (Variance)')
    ax.set_title('Scree Plot')
    
    ax = axes[1]
    ax.bar(range(1, 5), np.cumsum(eigenvalues) / eigenvalues.sum(), alpha=0.7)
    ax.axhline(0.95, color='red', linestyle='--', label='95% variance')
    ax.set_xlabel('Number of Components')
    ax.set_ylabel('Cumulative Variance Explained')
    ax.set_title('Cumulative Variance')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

pca_covariance_connection()
```

### 2. Mahalanobis Distance

```python
def mahalanobis_distance_example():
    """Mahalanobis distance accounts for covariance."""
    
    np.random.seed(42)
    
    # Generate correlated data
    mean = [0, 0]
    cov = [[1, 0.8], [0.8, 1]]
    data = np.random.multivariate_normal(mean, cov, 200)
    
    # Test points
    point_a = np.array([2, 0])  # Along correlation
    point_b = np.array([0, 2])  # Perpendicular to correlation
    
    # Euclidean distance (same for both)
    dist_eucl_a = np.sqrt(np.sum(point_a ** 2))
    dist_eucl_b = np.sqrt(np.sum(point_b ** 2))
    
    # Mahalanobis distance
    cov_inv = np.linalg.inv(cov)
    dist_maha_a = np.sqrt(point_a @ cov_inv @ point_a)
    dist_maha_b = np.sqrt(point_b @ cov_inv @ point_b)
    
    print("Mahalanobis vs Euclidean Distance")
    print("=" * 50)
    print(f"Point A (2, 0) - along correlation direction:")
    print(f"  Euclidean: {dist_eucl_a:.3f}")
    print(f"  Mahalanobis: {dist_maha_a:.3f}")
    print(f"\nPoint B (0, 2) - perpendicular to correlation:")
    print(f"  Euclidean: {dist_eucl_b:.3f}")
    print(f"  Mahalanobis: {dist_maha_b:.3f}")
    print("\nMahalanobis recognizes that point B is more unusual!")
    
    # Visualize
    fig, ax = plt.subplots(figsize=(10, 8))
    
    ax.scatter(data[:, 0], data[:, 1], alpha=0.5, label='Data')
    ax.scatter(*point_a, s=200, marker='*', c='red', label=f'A: Maha={dist_maha_a:.2f}')
    ax.scatter(*point_b, s=200, marker='*', c='blue', label=f'B: Maha={dist_maha_b:.2f}')
    
    # Draw circles
    circle_eucl = plt.Circle((0, 0), 2, fill=False, color='gray', 
                              linestyle='--', label='Euclidean r=2')
    ax.add_patch(circle_eucl)
    
    # Draw Mahalanobis ellipse
    eigenvalues, eigenvectors = np.linalg.eigh(cov)
    angle = np.degrees(np.arctan2(eigenvectors[1, 0], eigenvectors[0, 0]))
    from matplotlib.patches import Ellipse
    ellipse = Ellipse((0, 0), 2*2*np.sqrt(eigenvalues[0]), 2*2*np.sqrt(eigenvalues[1]),
                      angle=angle, fill=False, color='green', linestyle='-',
                      label='Mahalanobis r=2')
    ax.add_patch(ellipse)
    
    ax.set_xlabel('X₁')
    ax.set_ylabel('X₂')
    ax.set_title('Mahalanobis Distance Accounts for Covariance')
    ax.legend()
    ax.set_xlim(-4, 4)
    ax.set_ylim(-4, 4)
    ax.set_aspect('equal')
    
    plt.tight_layout()
    plt.show()

mahalanobis_distance_example()
```

### 3. Feature Selection: Removing Correlated Features

```python
def remove_correlated_features():
    """Remove highly correlated features."""
    
    from sklearn.datasets import load_breast_cancer
    
    # Load data
    data = load_breast_cancer()
    X = data.data
    feature_names = data.feature_names
    
    # Compute correlation matrix
    corr_matrix = np.corrcoef(X.T)
    
    # Find highly correlated pairs
    threshold = 0.9
    
    print("Highly Correlated Feature Pairs (|ρ| > 0.9)")
    print("=" * 60)
    
    correlated_pairs = []
    for i in range(len(feature_names)):
        for j in range(i + 1, len(feature_names)):
            if abs(corr_matrix[i, j]) > threshold:
                correlated_pairs.append((feature_names[i], feature_names[j], 
                                        corr_matrix[i, j]))
    
    for f1, f2, corr in sorted(correlated_pairs, key=lambda x: -abs(x[2]))[:10]:
        print(f"  {f1[:20]:20s} ↔ {f2[:20]:20s}: ρ = {corr:.3f}")
    
    print(f"\nTotal: {len(correlated_pairs)} pairs with |ρ| > {threshold}")
    
    # Remove correlated features
    def remove_correlated(X, threshold=0.9):
        """Remove features with correlation above threshold."""
        corr = np.corrcoef(X.T)
        n = corr.shape[0]
        to_remove = set()
        
        for i in range(n):
            for j in range(i + 1, n):
                if abs(corr[i, j]) > threshold and j not in to_remove:
                    to_remove.add(j)
        
        return np.delete(X, list(to_remove), axis=1), to_remove
    
    X_reduced, removed = remove_correlated(X)
    
    print(f"\nOriginal features: {X.shape[1]}")
    print(f"After removal: {X_reduced.shape[1]}")
    print(f"Removed: {len(removed)} features")

remove_correlated_features()
```

### 4. Gaussian Mixture Models

```python
def gmm_covariance_types():
    """Different covariance types in GMM."""
    
    from sklearn.mixture import GaussianMixture
    
    np.random.seed(42)
    
    # Generate data from 2 Gaussians
    n = 300
    X1 = np.random.multivariate_normal([0, 0], [[1, 0.5], [0.5, 1]], n//2)
    X2 = np.random.multivariate_normal([3, 3], [[1, -0.5], [-0.5, 1]], n//2)
    X = np.vstack([X1, X2])
    
    covariance_types = ['full', 'tied', 'diag', 'spherical']
    
    fig, axes = plt.subplots(1, 4, figsize=(16, 4))
    
    print("GMM Covariance Types")
    print("=" * 60)
    
    for ax, cov_type in zip(axes, covariance_types):
        gmm = GaussianMixture(n_components=2, covariance_type=cov_type, 
                             random_state=42)
        gmm.fit(X)
        
        # Plot data
        ax.scatter(X[:, 0], X[:, 1], alpha=0.5, s=10)
        
        # Plot GMM ellipses
        for mean, cov in zip(gmm.means_, gmm.covariances_):
            if cov_type == 'full':
                cov_matrix = cov
            elif cov_type == 'tied':
                cov_matrix = gmm.covariances_
            elif cov_type == 'diag':
                cov_matrix = np.diag(cov)
            elif cov_type == 'spherical':
                cov_matrix = np.eye(2) * cov
            
            eigenvalues, eigenvectors = np.linalg.eigh(cov_matrix)
            angle = np.degrees(np.arctan2(eigenvectors[1, 0], eigenvectors[0, 0]))
            
            from matplotlib.patches import Ellipse
            for n_std in [1, 2]:
                ellipse = Ellipse(mean, 
                                 2*n_std*np.sqrt(eigenvalues[0]), 
                                 2*n_std*np.sqrt(eigenvalues[1]),
                                 angle=angle, fill=False, 
                                 color='red', linewidth=2)
                ax.add_patch(ellipse)
            ax.scatter(*mean, c='red', s=100, marker='x')
        
        n_params = {'full': 2*6, 'tied': 6, 'diag': 2*2, 'spherical': 2*1}
        ax.set_title(f'{cov_type}\n({n_params[cov_type]} cov params)')
        ax.set_aspect('equal')
        
        print(f"{cov_type:10s}: BIC = {gmm.bic(X):.1f}")
    
    plt.tight_layout()
    plt.show()

gmm_covariance_types()
```

---

## Covariance Estimation Challenges

### Sample Covariance Problems

```python
def sample_covariance_issues():
    """Issues with sample covariance in high dimensions."""
    
    np.random.seed(42)
    
    # High-dimensional data
    n_samples = 50
    n_features = 100
    
    # True covariance: identity
    true_cov = np.eye(n_features)
    X = np.random.multivariate_normal(np.zeros(n_features), true_cov, n_samples)
    
    # Sample covariance
    sample_cov = np.cov(X.T)
    
    print("Sample Covariance in High Dimensions")
    print("=" * 60)
    print(f"Samples: {n_samples}, Features: {n_features}")
    print(f"Ratio n/p = {n_samples/n_features:.2f}")
    
    # Eigenvalues
    true_eigs = np.linalg.eigvalsh(true_cov)
    sample_eigs = np.linalg.eigvalsh(sample_cov)
    
    print(f"\nTrue eigenvalues: all = 1")
    print(f"Sample eigenvalue range: [{sample_eigs.min():.3f}, {sample_eigs.max():.3f}]")
    
    # Condition number
    print(f"\nCondition number:")
    print(f"  True: {np.linalg.cond(true_cov):.1f}")
    print(f"  Sample: {np.linalg.cond(sample_cov):.1f}")
    
    # Is it invertible?
    print(f"\nSample cov rank: {np.linalg.matrix_rank(sample_cov)}")
    print(f"Singular? {np.linalg.matrix_rank(sample_cov) < n_features}")
    
    # Visualize eigenvalue distribution
    fig, ax = plt.subplots(figsize=(10, 5))
    
    ax.hist(sample_eigs, bins=30, alpha=0.7, label='Sample eigenvalues')
    ax.axvline(1.0, color='red', linestyle='--', linewidth=2, label='True eigenvalue')
    ax.set_xlabel('Eigenvalue')
    ax.set_ylabel('Count')
    ax.set_title(f'Eigenvalue Distribution (n={n_samples}, p={n_features})')
    ax.legend()
    
    plt.tight_layout()
    plt.show()

sample_covariance_issues()
```

### Regularized Covariance Estimation

```python
def regularized_covariance():
    """Shrinkage estimators for covariance."""
    
    from sklearn.covariance import LedoitWolf, EmpiricalCovariance, ShrunkCovariance
    
    np.random.seed(42)
    
    # Generate data
    n_samples = 30
    n_features = 50
    
    true_cov = np.eye(n_features)
    X = np.random.multivariate_normal(np.zeros(n_features), true_cov, n_samples)
    
    # Different estimators
    estimators = {
        'Empirical': EmpiricalCovariance().fit(X),
        'Shrunk (0.1)': ShrunkCovariance(shrinkage=0.1).fit(X),
        'Shrunk (0.5)': ShrunkCovariance(shrinkage=0.5).fit(X),
        'Ledoit-Wolf': LedoitWolf().fit(X),
    }
    
    print("Regularized Covariance Estimation")
    print("=" * 60)
    
    for name, est in estimators.items():
        cov = est.covariance_
        error = np.mean((cov - true_cov) ** 2)
        cond = np.linalg.cond(cov)
        print(f"{name:15s}: MSE = {error:.4f}, Condition = {cond:.1f}")
    
    print("\nLedoit-Wolf automatically chooses optimal shrinkage!")
    print(f"Optimal shrinkage: {estimators['Ledoit-Wolf'].shrinkage_:.4f}")

regularized_covariance()
```

---

## FAQs

### What's the difference between covariance and correlation?

- **Covariance**: Raw measure of joint variability (units: unit_X × unit_Y)
- **Correlation**: Standardized covariance (unitless, bounded [-1, 1])

### When should I use correlation instead of covariance?

- When comparing relationships across different scales
- When you want to know the **strength** of relationship
- When variables have different units

### What does a singular covariance matrix mean?

It means features are linearly dependent—at least one feature can be expressed as a linear combination of others. This happens when:
- n < p (more features than samples)
- Features are perfectly correlated
- Features have zero variance

### How do I handle singular covariance matrices?

1. **Regularization**: Add small value to diagonal (shrinkage)
2. **Dimensionality reduction**: PCA to reduce features
3. **Feature selection**: Remove redundant features
4. **Pseudo-inverse**: Use Moore-Penrose inverse

---

## Key Takeaways

1. **Covariance** measures how variables vary together
2. **Correlation** is standardized covariance ([-1, 1])
3. **Covariance matrix** is symmetric and positive semi-definite
4. **PCA** finds eigenvectors of covariance matrix
5. **Mahalanobis distance** accounts for correlations
6. **High dimensions** require regularized covariance estimation

---

## Next Steps

Continue your statistical journey:

1. **[PCA Tutorial](/docs/ai-ml/foundations/mathematics/linear-algebra/pca-tutorial-python/)** - Dimensionality reduction
2. **[Probability Distributions](/docs/ai-ml/foundations/mathematics/probability/probability-distributions-machine-learning/)** - Foundation concepts
3. **[Information Theory](/docs/ai-ml/foundations/mathematics/probability/information-theory-entropy/)** - Entropy and mutual information

---

## References

1. Bishop, C. M. "Pattern Recognition and Machine Learning" - Chapter 2
2. Murphy, K. P. "Machine Learning: A Probabilistic Perspective" - Chapter 4
3. Ledoit, O., Wolf, M. "A Well-Conditioned Estimator for Large-Dimensional Covariance Matrices" (2004)

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
