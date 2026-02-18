---
title: "PCA Deep Dive: Principal Component Analysis from Theory to Python Code"
description: "Master Principal Component Analysis (PCA) - understand the mathematics, implement from scratch in Python, and apply to real datasets. Complete guide with sklearn examples."
date: 2025-01-15
lastmod: 2025-01-15
draft: false
weight: 4
categories: ["AI-ML", "Mathematics"]
tags: ["PCA", "principal component analysis", "dimensionality reduction", "machine learning", "sklearn", "feature extraction", "data science"]
author: "AI & ML Team"
toc: true
math: true
---

# PCA Deep Dive: Principal Component Analysis from Theory to Python Code

**Reading Time:** 18 minutes | **Word Count:** 4,500+ | **Difficulty:** Intermediate

*"PCA is one of the most valuable results from applied linear algebra." — Gilbert Strang, MIT*

Principal Component Analysis (PCA) is one of the most widely used techniques in data science and machine learning. Whether you're visualizing high-dimensional data, removing noise, or preparing features for a model, PCA is often your go-to tool.

In this comprehensive guide, we'll build deep intuition for PCA, implement it from scratch, and master the sklearn API—all with practical, runnable code.

## What Is PCA and Why Does It Matter?

### The Core Problem: High-Dimensional Data

Modern datasets often have hundreds or thousands of features:
- **Images**: A 64×64 image = 4,096 dimensions
- **Text**: Vocabulary of 50,000 words = 50,000 dimensions
- **Genomics**: Expression of 20,000+ genes

This creates problems:
1. **Visualization**: Can't plot more than 3 dimensions
2. **Computation**: Training time increases with dimensions
3. **Curse of Dimensionality**: Models need exponentially more data
4. **Redundancy**: Many features are correlated

### What PCA Does

PCA finds a **new coordinate system** where:
1. Each axis (principal component) captures maximum variance
2. Components are orthogonal (uncorrelated)
3. First few components often capture most information

| Original Space | PCA Space |
|---------------|-----------|
| Features may be correlated | Components are uncorrelated |
| High dimensional | Can reduce to fewer dimensions |
| Redundant information | Information compressed |

---

## How Does PCA Work? The Intuition

### Step-by-Step Visualization

Imagine 2D data shaped like an ellipse (elongated cloud):

```
    *  *  *
  * * * * * *
* * * * * * * * *
  * * * * * *
    *  *  *
```

**PCA finds:**
1. **PC1**: Direction of maximum spread (major axis of ellipse)
2. **PC2**: Perpendicular direction with second-most spread

### The Mathematical Goal

PCA solves this optimization:

$$\text{Find } \mathbf{w}_1 = \arg\max_{\|\mathbf{w}\|=1} \text{Var}(\mathbf{Xw})$$

The direction $\mathbf{w}_1$ that maximizes variance when data is projected onto it.

---

## The Mathematics Behind PCA

### Step 1: Center the Data

Subtract the mean from each feature:
$$\mathbf{X}_{centered} = \mathbf{X} - \boldsymbol{\mu}$$

where $\boldsymbol{\mu} = \frac{1}{n}\sum_{i=1}^{n} \mathbf{x}_i$

### Step 2: Compute Covariance Matrix

The covariance matrix captures relationships between features:
$$\mathbf{C} = \frac{1}{n-1}\mathbf{X}_{centered}^T \mathbf{X}_{centered}$$

### Step 3: Eigendecomposition

Find eigenvalues $\lambda_i$ and eigenvectors $\mathbf{v}_i$ of $\mathbf{C}$:
$$\mathbf{C}\mathbf{v}_i = \lambda_i\mathbf{v}_i$$

**Key Insight**: 
- Eigenvectors = principal component directions
- Eigenvalues = variance along each direction

### Step 4: Sort and Select

Sort by eigenvalue (descending) and keep top-k components:
- Higher eigenvalue = more variance explained
- Keep enough to explain desired variance (e.g., 95%)

### Step 5: Project Data

Transform data to new coordinate system:
$$\mathbf{X}_{pca} = \mathbf{X}_{centered} \mathbf{W}_k$$

where $\mathbf{W}_k$ contains the top-k eigenvectors as columns.

---

## Implementing PCA from Scratch

Let's implement PCA step-by-step in Python:

```python
import numpy as np
import matplotlib.pyplot as plt

class PCAFromScratch:
    """
    Principal Component Analysis implementation from scratch.
    
    Follows sklearn API conventions for easy comparison.
    """
    
    def __init__(self, n_components=None):
        """
        Initialize PCA.
        
        Args:
            n_components: Number of components to keep. 
                          If None, keep all components.
        """
        self.n_components = n_components
        self.components_ = None  # Principal component directions
        self.explained_variance_ = None  # Variance of each component
        self.explained_variance_ratio_ = None  # Proportion of variance
        self.mean_ = None  # Feature means for centering
        
    def fit(self, X):
        """
        Fit PCA on training data.
        
        Args:
            X: array of shape (n_samples, n_features)
        
        Returns:
            self
        """
        n_samples, n_features = X.shape
        
        # Step 1: Center the data
        self.mean_ = np.mean(X, axis=0)
        X_centered = X - self.mean_
        
        # Step 2: Compute covariance matrix
        # Using (n-1) for unbiased estimate
        cov_matrix = np.cov(X_centered.T)
        
        # Step 3: Eigendecomposition
        # np.linalg.eigh is optimized for symmetric matrices
        eigenvalues, eigenvectors = np.linalg.eigh(cov_matrix)
        
        # Step 4: Sort by eigenvalue (descending)
        sorted_indices = np.argsort(eigenvalues)[::-1]
        eigenvalues = eigenvalues[sorted_indices]
        eigenvectors = eigenvectors[:, sorted_indices]
        
        # Step 5: Store results
        n_components = self.n_components or n_features
        
        self.components_ = eigenvectors[:, :n_components].T  # (n_components, n_features)
        self.explained_variance_ = eigenvalues[:n_components]
        self.explained_variance_ratio_ = eigenvalues[:n_components] / eigenvalues.sum()
        
        return self
    
    def transform(self, X):
        """
        Project data onto principal components.
        
        Args:
            X: array of shape (n_samples, n_features)
            
        Returns:
            X_pca: array of shape (n_samples, n_components)
        """
        X_centered = X - self.mean_
        return X_centered @ self.components_.T
    
    def fit_transform(self, X):
        """Fit and transform in one step."""
        self.fit(X)
        return self.transform(X)
    
    def inverse_transform(self, X_pca):
        """
        Reconstruct original data from PCA representation.
        
        Args:
            X_pca: array of shape (n_samples, n_components)
            
        Returns:
            X_reconstructed: array of shape (n_samples, n_features)
        """
        return X_pca @ self.components_ + self.mean_


# Test our implementation
np.random.seed(42)

# Generate correlated 2D data
n_samples = 300
mean = [0, 0]
cov = [[3, 2], [2, 2]]  # Correlated features
X = np.random.multivariate_normal(mean, cov, n_samples)

# Apply our PCA
pca = PCAFromScratch(n_components=2)
X_pca = pca.fit_transform(X)

print("=== PCA from Scratch Results ===")
print(f"Original data shape: {X.shape}")
print(f"Transformed shape: {X_pca.shape}")
print(f"\nPrincipal Components (directions):")
print(pca.components_)
print(f"\nExplained Variance: {pca.explained_variance_}")
print(f"Explained Variance Ratio: {pca.explained_variance_ratio_}")
print(f"Total variance explained: {sum(pca.explained_variance_ratio_):.2%}")
```

**Output:**
```
=== PCA from Scratch Results ===
Original data shape: (300, 2)
Transformed shape: (300, 2)

Principal Components (directions):
[[ 0.81  0.59]
 [-0.59  0.81]]

Explained Variance: [4.72 0.53]
Explained Variance Ratio: [0.90 0.10]
Total variance explained: 100.00%
```

---

## PCA with Scikit-learn

Now let's compare with sklearn and explore its full API:

```python
from sklearn.decomposition import PCA
from sklearn.datasets import load_iris, load_digits
from sklearn.preprocessing import StandardScaler

# Load Iris dataset
iris = load_iris()
X, y = iris.data, iris.target
feature_names = iris.feature_names

print("=== Iris Dataset ===")
print(f"Shape: {X.shape}")  # 150 samples, 4 features
print(f"Features: {feature_names}")

# Apply PCA
pca = PCA()  # Keep all components initially
X_pca = pca.fit_transform(X)

# Analyze results
print(f"\n=== PCA Results ===")
print(f"Explained variance ratio: {pca.explained_variance_ratio_}")
print(f"Cumulative variance:")
cumsum = np.cumsum(pca.explained_variance_ratio_)
for i, cv in enumerate(cumsum):
    print(f"  PC1-{i+1}: {cv:.1%}")
```

### Choosing Number of Components

**Method 1: Variance Threshold**
```python
def choose_n_components(pca, threshold=0.95):
    """Find minimum components to explain threshold variance."""
    cumsum = np.cumsum(pca.explained_variance_ratio_)
    n_components = np.argmax(cumsum >= threshold) + 1
    return n_components

n_components_95 = choose_n_components(pca, 0.95)
print(f"\nComponents for 95% variance: {n_components_95}")
```

**Method 2: Elbow Method (Visual)**
```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Scree plot
axes[0].bar(range(1, len(pca.explained_variance_ratio_) + 1), 
            pca.explained_variance_ratio_, alpha=0.7, label='Individual')
axes[0].plot(range(1, len(pca.explained_variance_ratio_) + 1),
             np.cumsum(pca.explained_variance_ratio_), 'ro-', label='Cumulative')
axes[0].axhline(y=0.95, color='g', linestyle='--', label='95% threshold')
axes[0].set_xlabel('Principal Component')
axes[0].set_ylabel('Explained Variance Ratio')
axes[0].set_title('Scree Plot')
axes[0].legend()

# 2D visualization
pca_2d = PCA(n_components=2)
X_2d = pca_2d.fit_transform(X)

for target, color, name in zip([0, 1, 2], ['r', 'g', 'b'], iris.target_names):
    mask = y == target
    axes[1].scatter(X_2d[mask, 0], X_2d[mask, 1], c=color, label=name, alpha=0.7)
axes[1].set_xlabel(f'PC1 ({pca_2d.explained_variance_ratio_[0]:.1%})')
axes[1].set_ylabel(f'PC2 ({pca_2d.explained_variance_ratio_[1]:.1%})')
axes[1].set_title('Iris Data in PCA Space')
axes[1].legend()

plt.tight_layout()
plt.savefig('pca_iris_analysis.png', dpi=150, bbox_inches='tight')
print("\nPlot saved: pca_iris_analysis.png")
```

---

## Real-World Application: MNIST Digit Recognition

Let's apply PCA to the MNIST digits dataset—a classic ML benchmark:

```python
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score
import time

# Load digits data
digits = load_digits()
X, y = digits.data, digits.target

print("=== MNIST Digits Dataset ===")
print(f"Shape: {X.shape}")  # 1797 samples, 64 features (8x8 images)
print(f"Classes: {len(np.unique(y))} (digits 0-9)")

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Baseline: Train on all 64 features
print("\n--- Without PCA (64 features) ---")
start = time.time()
clf_full = LogisticRegression(max_iter=5000, random_state=42)
clf_full.fit(X_train, y_train)
train_time_full = time.time() - start
acc_full = accuracy_score(y_test, clf_full.predict(X_test))
print(f"Accuracy: {acc_full:.4f}")
print(f"Training time: {train_time_full:.4f}s")

# With PCA: Find optimal components
print("\n--- Analyzing PCA components ---")
pca_analysis = PCA()
pca_analysis.fit(X_train)

cumsum = np.cumsum(pca_analysis.explained_variance_ratio_)
n_90 = np.argmax(cumsum >= 0.90) + 1
n_95 = np.argmax(cumsum >= 0.95) + 1
n_99 = np.argmax(cumsum >= 0.99) + 1

print(f"Components for 90% variance: {n_90}")
print(f"Components for 95% variance: {n_95}")
print(f"Components for 99% variance: {n_99}")

# Train with PCA-reduced features
for n_components in [10, 20, 30, 40]:
    pca = PCA(n_components=n_components)
    X_train_pca = pca.fit_transform(X_train)
    X_test_pca = pca.transform(X_test)
    
    start = time.time()
    clf = LogisticRegression(max_iter=5000, random_state=42)
    clf.fit(X_train_pca, y_train)
    train_time = time.time() - start
    
    acc = accuracy_score(y_test, clf.predict(X_test_pca))
    var_explained = sum(pca.explained_variance_ratio_)
    
    print(f"\n{n_components} components ({var_explained:.1%} variance):")
    print(f"  Accuracy: {acc:.4f} (vs {acc_full:.4f} full)")
    print(f"  Training time: {train_time:.4f}s (vs {train_time_full:.4f}s)")
    print(f"  Speedup: {train_time_full/train_time:.1f}x")
```

**Typical Output:**
```
--- Without PCA (64 features) ---
Accuracy: 0.9694
Training time: 0.3521s

--- Analyzing PCA components ---
Components for 90% variance: 21
Components for 95% variance: 29
Components for 99% variance: 41

30 components (95.3% variance):
  Accuracy: 0.9667 (vs 0.9694 full)
  Training time: 0.1245s (vs 0.3521s)
  Speedup: 2.8x
```

### Visualizing Principal Components as Images

```python
# Visualize what PCA components look like for digit images
fig, axes = plt.subplots(2, 5, figsize=(12, 5))

# First row: First 5 principal components
for i, ax in enumerate(axes[0]):
    component = pca_analysis.components_[i].reshape(8, 8)
    ax.imshow(component, cmap='RdBu_r')
    ax.set_title(f'PC{i+1}')
    ax.axis('off')

# Second row: Reconstructions with increasing components
pca_recon = PCA()
pca_recon.fit(X_train)

sample_idx = 0
original = X_test[sample_idx].reshape(8, 8)

for i, (ax, n_comp) in enumerate(zip(axes[1], [1, 5, 15, 30, 64])):
    if n_comp == 64:
        reconstructed = original
        title = 'Original'
    else:
        pca_temp = PCA(n_components=n_comp)
        X_reduced = pca_temp.fit_transform(X_train)
        sample_reduced = pca_temp.transform(X_test[sample_idx:sample_idx+1])
        reconstructed = pca_temp.inverse_transform(sample_reduced).reshape(8, 8)
        title = f'{n_comp} PCs'
    
    ax.imshow(reconstructed, cmap='gray')
    ax.set_title(title)
    ax.axis('off')

plt.suptitle('Top: Principal Components | Bottom: Reconstructions')
plt.tight_layout()
plt.savefig('pca_digit_components.png', dpi=150, bbox_inches='tight')
```

---

## When to Use PCA (and When Not To)

### ✅ Use PCA When:

| Scenario | Why PCA Helps |
|----------|---------------|
| **Visualization** | Reduce to 2D/3D for plotting |
| **Noise reduction** | Lower components filter noise |
| **Speeding up training** | Fewer features = faster models |
| **Removing multicollinearity** | PCs are uncorrelated |
| **Feature extraction** | Create interpretable features |

### ❌ Avoid PCA When:

| Scenario | Why PCA Fails |
|----------|---------------|
| **Non-linear relationships** | PCA is linear; use t-SNE, UMAP, autoencoders |
| **Categorical data** | PCA assumes continuous; use MCA or embeddings |
| **Interpretability required** | PCs are combinations of original features |
| **All features informative** | Don't reduce if all features matter |

### PCA vs Other Methods

| Method | Type | Best For | Preserves |
|--------|------|----------|-----------|
| **PCA** | Linear | General dimensionality reduction | Global variance |
| **t-SNE** | Non-linear | Visualization | Local structure |
| **UMAP** | Non-linear | Visualization + clustering | Local + some global |
| **LDA** | Linear | Classification | Class separability |
| **Autoencoders** | Non-linear | Complex patterns | Learned features |

```python
# Quick comparison: PCA vs t-SNE for visualization
from sklearn.manifold import TSNE

# Apply both methods
pca_2d = PCA(n_components=2)
X_pca = pca_2d.fit_transform(X)

tsne = TSNE(n_components=2, random_state=42, perplexity=30)
X_tsne = tsne.fit_transform(X)

fig, axes = plt.subplots(1, 2, figsize=(12, 5))

for ax, X_reduced, title in zip(axes, [X_pca, X_tsne], ['PCA', 't-SNE']):
    scatter = ax.scatter(X_reduced[:, 0], X_reduced[:, 1], 
                         c=y, cmap='tab10', alpha=0.7, s=20)
    ax.set_title(f'{title} - Digits Dataset')
    ax.set_xlabel('Component 1')
    ax.set_ylabel('Component 2')

plt.colorbar(scatter, ax=axes, label='Digit')
plt.tight_layout()
plt.savefig('pca_vs_tsne.png', dpi=150, bbox_inches='tight')
```

---

## Advanced PCA Techniques

### Incremental PCA (For Large Datasets)

When data doesn't fit in memory:

```python
from sklearn.decomposition import IncrementalPCA

# Simulate large dataset that doesn't fit in memory
n_samples = 100000
n_features = 1000
n_components = 50
batch_size = 1000

# Generate data in batches
np.random.seed(42)

# Initialize Incremental PCA
ipca = IncrementalPCA(n_components=n_components, batch_size=batch_size)

# Fit in batches
for i in range(0, n_samples, batch_size):
    batch = np.random.randn(batch_size, n_features)
    ipca.partial_fit(batch)
    if (i + batch_size) % 10000 == 0:
        print(f"Processed {i + batch_size} samples...")

print(f"\nIncremental PCA fitted!")
print(f"Explained variance (first 5): {ipca.explained_variance_ratio_[:5]}")
```

### Kernel PCA (For Non-Linear Data)

When data has non-linear structure:

```python
from sklearn.decomposition import KernelPCA
from sklearn.datasets import make_moons

# Create non-linear data
X_moons, y_moons = make_moons(n_samples=500, noise=0.05, random_state=42)

# Standard PCA (linear - won't separate well)
pca_linear = PCA(n_components=2)
X_pca_linear = pca_linear.fit_transform(X_moons)

# Kernel PCA (non-linear - better separation)
kpca = KernelPCA(n_components=2, kernel='rbf', gamma=15)
X_kpca = kpca.fit_transform(X_moons)

fig, axes = plt.subplots(1, 3, figsize=(14, 4))

# Original data
axes[0].scatter(X_moons[:, 0], X_moons[:, 1], c=y_moons, cmap='coolwarm', alpha=0.7)
axes[0].set_title('Original Data (Moons)')

# Linear PCA
axes[1].scatter(X_pca_linear[:, 0], X_pca_linear[:, 1], c=y_moons, cmap='coolwarm', alpha=0.7)
axes[1].set_title('Linear PCA')

# Kernel PCA
axes[2].scatter(X_kpca[:, 0], X_kpca[:, 1], c=y_moons, cmap='coolwarm', alpha=0.7)
axes[2].set_title('Kernel PCA (RBF)')

plt.tight_layout()
plt.savefig('kernel_pca.png', dpi=150, bbox_inches='tight')
```

### Sparse PCA (For Interpretability)

Get components that use few original features:

```python
from sklearn.decomposition import SparsePCA

# Apply Sparse PCA
spca = SparsePCA(n_components=4, alpha=1, random_state=42)
X_spca = spca.fit_transform(X)

# Compare sparsity
print("Component sparsity (fraction of zero weights):")
for i in range(4):
    sparsity = np.mean(spca.components_[i] == 0)
    print(f"  Component {i+1}: {sparsity:.1%} zeros")
```

---

## PCA in Production: Best Practices

### 1. Always Scale Your Data

PCA is sensitive to feature scales:

```python
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline

# Bad: Features with different scales
X_bad = np.column_stack([
    np.random.randn(100),           # scale ~1
    np.random.randn(100) * 1000     # scale ~1000
])
pca_bad = PCA(n_components=2)
pca_bad.fit(X_bad)
print(f"Without scaling - PC1 variance ratio: {pca_bad.explained_variance_ratio_[0]:.1%}")

# Good: Use StandardScaler
pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('pca', PCA(n_components=2))
])
X_good = pipeline.fit_transform(X_bad)
pca_good = pipeline.named_steps['pca']
print(f"With scaling - PC1 variance ratio: {pca_good.explained_variance_ratio_[0]:.1%}")
```

### 2. Handle New Data Correctly

```python
# Fit on training data only
pca = PCA(n_components=10)
pca.fit(X_train)  # Learn transformation

# Transform both train and test
X_train_pca = pca.transform(X_train)
X_test_pca = pca.transform(X_test)  # Use same transformation!

# DON'T do this:
# pca.fit_transform(X_test)  # Wrong! Leaks test info and uses different basis
```

### 3. Save and Load PCA Models

```python
import joblib

# Save fitted PCA
joblib.dump(pca, 'pca_model.pkl')

# Load later
pca_loaded = joblib.load('pca_model.pkl')
X_new_pca = pca_loaded.transform(X_new)
```

---

## PCA for Indian Data Science: Practical Considerations

### Computing Costs in India

| Cloud Provider | GPU Instance | Monthly Cost (INR) |
|----------------|--------------|-------------------|
| AWS (Mumbai) | p3.2xlarge | ₹2,50,000+ |
| GCP (Mumbai) | n1-standard-8 | ₹30,000 |
| **PCA Benefit** | Reduce compute by 50-80% | Save ₹15,000-₹1,00,000 |

### Indian Datasets Where PCA Helps

1. **Census Data**: Reduce 100s of socioeconomic variables
2. **Agriculture Data**: Satellite imagery dimensionality reduction
3. **Financial Data**: Stock market feature reduction (NSE/BSE)
4. **E-commerce**: Customer feature compression (Flipkart, Amazon India)

---

## Summary and Key Takeaways

### Core Concepts

| Concept | Definition | Importance |
|---------|------------|------------|
| Principal Components | Orthogonal directions of maximum variance | Foundation of PCA |
| Explained Variance | Variance captured by each PC | Component selection |
| Reconstruction | Inverse transform back to original space | Quality check |
| Cumulative Variance | Total variance from first k components | Choose n_components |

### Quick Reference

```python
# Standard workflow
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import Pipeline

# Best practice: Scale + PCA
pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('pca', PCA(n_components=0.95))  # Keep 95% variance
])

X_train_pca = pipeline.fit_transform(X_train)
X_test_pca = pipeline.transform(X_test)
```

### Decision Flowchart

```
Start
  │
  ├─ Is data continuous? 
  │   No → Use other methods (MCA, embeddings)
  │   Yes ↓
  │
  ├─ Is relationship linear?
  │   No → Consider Kernel PCA, t-SNE, UMAP
  │   Yes ↓
  │
  ├─ Need interpretability?
  │   Yes → Consider Sparse PCA
  │   No ↓
  │
  └─ Use Standard PCA with StandardScaler
```

---

## Frequently Asked Questions

### Q: How many components should I keep?
**A:** Common approaches:
- Keep components explaining 95% variance
- Use elbow method (visual)
- Cross-validate with downstream task

### Q: Should I always scale before PCA?
**A:** Yes, unless all features are already on the same scale. PCA maximizes variance, so high-scale features dominate.

### Q: Can PCA handle missing values?
**A:** No. Impute first (mean, median, or advanced methods) or use Probabilistic PCA.

### Q: PCA vs Feature Selection - which is better?
**A:** 
- **PCA**: Creates new features (combinations), loses interpretability
- **Feature Selection**: Keeps original features, maintains interpretability
- Use PCA for compression; use selection when feature meaning matters

### Q: Why is my explained variance not 100%?
**A:** It should sum to 100% if you keep all components. Check:
- n_components is set correctly
- No numerical precision issues
- Data was properly preprocessed

---

## Practice Exercises

### Exercise 1: Face Recognition with PCA (Eigenfaces)
Use PCA on the Olivetti faces dataset to:
1. Visualize principal components as "eigenfaces"
2. Reconstruct faces with different numbers of components
3. Build a classifier using PCA features

### Exercise 2: Stock Market Analysis
Apply PCA to NSE stock returns:
1. Download historical data for Nifty 50 stocks
2. Find principal components of returns
3. Interpret what each component represents

### Exercise 3: Image Compression
Implement image compression using PCA:
1. Load a color image
2. Apply PCA to each color channel
3. Plot quality vs compression ratio

---

## Next Steps

1. **Read Next**: [Tensors in Deep Learning](/docs/ai-ml/foundations/mathematics/linear-algebra/tensors-deep-learning/)
2. **Practice**: Implement eigenfaces on the LFW dataset
3. **Explore**: Try Kernel PCA on non-linear datasets

---

*Continue with our [Mathematics for ML series](/docs/ai-ml/foundations/mathematics/). Next: [Tensors in Deep Learning](/docs/ai-ml/foundations/mathematics/linear-algebra/tensors-deep-learning/).*
