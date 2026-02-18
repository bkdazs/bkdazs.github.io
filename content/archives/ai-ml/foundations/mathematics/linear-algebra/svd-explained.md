---
title: "SVD Explained: Singular Value Decomposition for Data Science"
description: "Master Singular Value Decomposition (SVD) - understand the math, implement from scratch, and apply to recommender systems, image compression, and NLP."
date: 2025-01-15
lastmod: 2025-01-15
draft: false
weight: 3
categories: ["AI-ML", "Mathematics"]
tags: ["SVD", "singular value decomposition", "machine learning", "matrix factorization", "recommender systems", "dimensionality reduction", "linear algebra"]
author: "AI & ML Team"
toc: true
math: true
---

# SVD Explained: Singular Value Decomposition for Data Science

**Reading Time:** 16 minutes | **Word Count:** 4,000+ | **Difficulty:** Intermediate

*"SVD is arguably the most important matrix decomposition." — Gilbert Strang*

Singular Value Decomposition (SVD) is one of the most powerful tools in linear algebra. It's behind Netflix's recommendation engine, Google's latent semantic indexing, image compression, and countless ML applications.

In this guide, we'll demystify SVD, build intuition for what it does, implement it from scratch, and apply it to real-world problems.

## What Is SVD?

### The Decomposition

SVD factorizes **any** matrix $\mathbf{A}$ (even rectangular) into three matrices:

$$\mathbf{A} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^T$$

Where:
- $\mathbf{A}$ is the original $m \times n$ matrix
- $\mathbf{U}$ is an $m \times m$ orthogonal matrix (left singular vectors)
- $\mathbf{\Sigma}$ is an $m \times n$ diagonal matrix (singular values)
- $\mathbf{V}^T$ is an $n \times n$ orthogonal matrix (right singular vectors)

### Visual Intuition

SVD tells us that **any linear transformation** can be broken down into:
1. **Rotation/reflection** ($\mathbf{V}^T$)
2. **Scaling** ($\mathbf{\Sigma}$)
3. **Another rotation/reflection** ($\mathbf{U}$)

```python
import numpy as np
import matplotlib.pyplot as plt

# Any matrix can be decomposed
A = np.array([[3, 2, 2],
              [2, 3, -2]])

# SVD decomposition
U, S, Vt = np.linalg.svd(A)

print(f"Original A shape: {A.shape}")  # (2, 3)
print(f"U shape: {U.shape}")            # (2, 2)
print(f"S (singular values): {S}")      # [5., 3.]
print(f"Vt shape: {Vt.shape}")          # (3, 3)

# Reconstruct A
Sigma = np.zeros((2, 3))
np.fill_diagonal(Sigma, S)
A_reconstructed = U @ Sigma @ Vt
print(f"\nReconstructed A:\n{A_reconstructed}")
print(f"Match: {np.allclose(A, A_reconstructed)}")
```

### SVD vs Eigendecomposition

| Feature | Eigendecomposition | SVD |
|---------|-------------------|-----|
| Matrix shape | Square only ($n \times n$) | Any shape ($m \times n$) |
| Orthogonal vectors | Only if symmetric | Always orthogonal |
| Values | Eigenvalues (can be negative/complex) | Singular values (always non-negative real) |
| Use cases | PCA, spectral analysis | All of the above + more |

---

## The Mathematics Behind SVD

### Key Properties

1. **Singular values are non-negative** and typically sorted: $\sigma_1 \geq \sigma_2 \geq \ldots \geq \sigma_r > 0$

2. **U and V have orthonormal columns**:
   - $\mathbf{U}^T\mathbf{U} = \mathbf{I}$
   - $\mathbf{V}^T\mathbf{V} = \mathbf{I}$

3. **Rank equals number of non-zero singular values**: $\text{rank}(\mathbf{A}) = r$

4. **Connection to eigenvalues**:
   - Singular values of $\mathbf{A}$ = square roots of eigenvalues of $\mathbf{A}^T\mathbf{A}$ (or $\mathbf{A}\mathbf{A}^T$)

```python
# Verify SVD properties
A = np.array([[3, 2, 2],
              [2, 3, -2]])

U, S, Vt = np.linalg.svd(A)

# Property 1: U has orthonormal columns
print("U^T @ U (should be identity):")
print(np.round(U.T @ U, 10))

# Property 2: Connection to eigenvalues
AtA = A.T @ A  # (3, 3)
eigenvalues_AtA = np.linalg.eigvalsh(AtA)
eigenvalues_AtA = np.sort(eigenvalues_AtA)[::-1]  # Sort descending

print(f"\nSingular values: {S}")
print(f"sqrt(eigenvalues of A^T A): {np.sqrt(eigenvalues_AtA[:2])}")
```

### Full vs Reduced SVD

**Full SVD**: Complete decomposition (may have zero singular values)
```python
U_full, S_full, Vt_full = np.linalg.svd(A, full_matrices=True)
```

**Reduced (Economy) SVD**: Only keeps non-zero parts (more efficient)
```python
U_reduced, S_reduced, Vt_reduced = np.linalg.svd(A, full_matrices=False)
print(f"U reduced shape: {U_reduced.shape}")  # (2, 2) instead of (2, 2)
print(f"Vt reduced shape: {Vt_reduced.shape}")  # (2, 3) instead of (3, 3)
```

---

## Low-Rank Approximation: The Power of SVD

### Truncated SVD

The key insight: we can **approximate** a matrix using only the top-k singular values:

$$\mathbf{A}_k = \sum_{i=1}^{k} \sigma_i \mathbf{u}_i \mathbf{v}_i^T$$

This is the **best rank-k approximation** (in terms of Frobenius norm).

```python
def low_rank_approximation(A, k):
    """
    Compute rank-k approximation of matrix A using SVD.
    """
    U, S, Vt = np.linalg.svd(A, full_matrices=False)
    
    # Keep only top-k components
    U_k = U[:, :k]
    S_k = S[:k]
    Vt_k = Vt[:k, :]
    
    # Reconstruct
    A_k = U_k @ np.diag(S_k) @ Vt_k
    
    return A_k, S

# Example: Approximating a matrix
np.random.seed(42)
A = np.random.randn(100, 50)

for k in [1, 5, 10, 25, 50]:
    A_k, S = low_rank_approximation(A, k)
    error = np.linalg.norm(A - A_k, 'fro') / np.linalg.norm(A, 'fro')
    print(f"Rank-{k:2d} approximation: {100*(1-error):.1f}% of energy preserved")
```

### Energy/Variance Explained

Singular values tell us how much "energy" each component captures:

$$\text{Energy from top-}k = \frac{\sum_{i=1}^{k} \sigma_i^2}{\sum_{i=1}^{r} \sigma_i^2}$$

```python
def explained_variance_ratio(S):
    """Calculate variance explained by each singular value."""
    variance = S ** 2
    return variance / variance.sum()

# Example
A = np.random.randn(100, 20)
_, S, _ = np.linalg.svd(A, full_matrices=False)

var_ratio = explained_variance_ratio(S)
cumulative = np.cumsum(var_ratio)

print("Variance explained by each component:")
for i, (single, cum) in enumerate(zip(var_ratio[:5], cumulative[:5])):
    print(f"  Component {i+1}: {100*single:.1f}% (cumulative: {100*cum:.1f}%)")
```

---

## Application 1: Image Compression

SVD naturally compresses images by keeping only significant singular values:

```python
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt

# Load a grayscale image (or convert)
# Using a simple example
np.random.seed(42)

# Create a simple image with structure
x = np.linspace(0, 4*np.pi, 200)
y = np.linspace(0, 4*np.pi, 200)
X, Y = np.meshgrid(x, y)
image = np.sin(X) * np.cos(Y) + 0.1 * np.random.randn(200, 200)

# SVD
U, S, Vt = np.linalg.svd(image, full_matrices=False)
print(f"Original image: {image.shape}, requires {image.size} values")

# Compress with different ranks
fig, axes = plt.subplots(2, 3, figsize=(12, 8))
ranks = [1, 5, 10, 25, 50, 100]

for ax, k in zip(axes.flatten(), ranks):
    # Rank-k approximation
    compressed = U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]
    
    # Compression ratio
    original_size = image.size
    compressed_size = k * (U.shape[0] + Vt.shape[1] + 1)  # U_k, V_k, S_k
    ratio = compressed_size / original_size
    
    ax.imshow(compressed, cmap='gray')
    ax.set_title(f'Rank {k}\nCompression: {100*ratio:.1f}%')
    ax.axis('off')

plt.tight_layout()
plt.savefig('svd_image_compression.png', dpi=100, bbox_inches='tight')
print("Image saved: svd_image_compression.png")
```

---

## Application 2: Recommender Systems

Netflix's famous recommendation challenge used SVD for matrix factorization.

### The Problem

User-item rating matrix with many missing values:
```
        Item1  Item2  Item3  Item4
User1     5      3      ?      1
User2     4      ?      ?      1
User3     1      1      ?      5
User4     ?      1      5      4
```

### SVD for Collaborative Filtering

```python
import numpy as np

def svd_recommender(ratings, k=2):
    """
    Simple SVD-based recommender system.
    
    Args:
        ratings: User-item matrix (NaN for unknown ratings)
        k: Number of latent factors
    
    Returns:
        Predicted ratings matrix
    """
    # Replace NaN with mean for initial SVD
    mask = ~np.isnan(ratings)
    ratings_filled = ratings.copy()
    ratings_filled[~mask] = np.nanmean(ratings)
    
    # SVD
    U, S, Vt = np.linalg.svd(ratings_filled, full_matrices=False)
    
    # Truncate to k components
    U_k = U[:, :k]
    S_k = np.diag(S[:k])
    Vt_k = Vt[:k, :]
    
    # Predict all ratings
    predictions = U_k @ S_k @ Vt_k
    
    return predictions, U_k, Vt_k

# Example: Movie ratings
np.random.seed(42)

# Create user-item matrix (5 users, 8 movies)
ratings = np.array([
    [5, 3, np.nan, 1, 4, np.nan, 2, np.nan],
    [4, np.nan, np.nan, 1, 5, np.nan, np.nan, 2],
    [1, 1, np.nan, 5, np.nan, 4, 5, 5],
    [np.nan, 1, 5, 4, np.nan, 4, 4, np.nan],
    [2, np.nan, 4, np.nan, 1, 5, 5, 4],
])

predictions, U_k, Vt_k = svd_recommender(ratings, k=3)

print("Original ratings (NaN = unknown):")
print(np.round(ratings, 1))
print("\nPredicted ratings:")
print(np.round(predictions, 1))

# Find recommendations for user 0
user_0_predictions = predictions[0]
user_0_known = ~np.isnan(ratings[0])
user_0_unknown = np.isnan(ratings[0])

print(f"\nRecommendations for User 0:")
unknown_indices = np.where(user_0_unknown)[0]
unknown_predictions = [(i, predictions[0, i]) for i in unknown_indices]
unknown_predictions.sort(key=lambda x: x[1], reverse=True)

for movie_idx, pred_rating in unknown_predictions:
    print(f"  Movie {movie_idx}: predicted rating {pred_rating:.2f}")
```

### Latent Factors Interpretation

The decomposition $\mathbf{R} \approx \mathbf{U}\mathbf{\Sigma}\mathbf{V}^T$ gives us:
- **U**: User latent factors (user preferences in k-dimensional space)
- **V**: Item latent factors (item characteristics in k-dimensional space)

```python
print("\nUser latent factors (preferences):")
print(U_k)

print("\nItem latent factors (characteristics):")
print(Vt_k.T)

# Users with similar latent factors have similar tastes
from scipy.spatial.distance import cosine

print("\nUser similarity (1 - cosine distance):")
for i in range(len(U_k)):
    for j in range(i+1, len(U_k)):
        sim = 1 - cosine(U_k[i], U_k[j])
        print(f"  User {i} - User {j}: {sim:.3f}")
```

---

## Application 3: Latent Semantic Analysis (LSA)

SVD powers semantic search and document similarity:

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.decomposition import TruncatedSVD

# Sample documents
documents = [
    "Machine learning is a subset of artificial intelligence",
    "Deep learning uses neural networks with many layers",
    "AI and ML are transforming technology industries",
    "Python is popular for data science and machine learning",
    "Neural networks can learn complex patterns from data",
    "TensorFlow and PyTorch are deep learning frameworks",
    "Data science involves statistics and programming",
    "Artificial intelligence mimics human intelligence",
]

# Create TF-IDF matrix
vectorizer = TfidfVectorizer(stop_words='english')
tfidf_matrix = vectorizer.fit_transform(documents)
print(f"TF-IDF matrix shape: {tfidf_matrix.shape}")  # (8 docs, vocab_size)

# Apply SVD (TruncatedSVD for sparse matrices)
n_components = 3
svd = TruncatedSVD(n_components=n_components)
doc_vectors = svd.fit_transform(tfidf_matrix)

print(f"\nDocument vectors (LSA space): {doc_vectors.shape}")
print(f"Explained variance: {svd.explained_variance_ratio_.sum():.1%}")

# Find similar documents
from sklearn.metrics.pairwise import cosine_similarity

similarity_matrix = cosine_similarity(doc_vectors)
print("\nDocument similarity matrix:")
print(np.round(similarity_matrix, 2))

# Query: find documents similar to a new query
query = "neural networks for artificial intelligence"
query_tfidf = vectorizer.transform([query])
query_lsa = svd.transform(query_tfidf)

similarities = cosine_similarity(query_lsa, doc_vectors)[0]
top_indices = np.argsort(similarities)[::-1][:3]

print(f"\nQuery: '{query}'")
print("Most similar documents:")
for idx in top_indices:
    print(f"  {similarities[idx]:.3f}: {documents[idx][:50]}...")
```

---

## SVD for Noise Reduction

Low-rank approximation naturally removes noise:

```python
np.random.seed(42)

# Create signal: low-rank structure
m, n, true_rank = 50, 30, 3
U_true = np.random.randn(m, true_rank)
V_true = np.random.randn(true_rank, n)
signal = U_true @ V_true

# Add noise
noise_level = 0.5
noisy_signal = signal + noise_level * np.random.randn(m, n)

# Denoise using SVD
U, S, Vt = np.linalg.svd(noisy_signal, full_matrices=False)

# Find elbow in singular values (signal vs noise)
print("Singular values:")
print(np.round(S[:10], 2))

# Keep only top-k (true rank)
k = true_rank
denoised = U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]

# Evaluate
print(f"\nNoise level: {noise_level}")
print(f"MSE before denoising: {np.mean((noisy_signal - signal)**2):.4f}")
print(f"MSE after denoising:  {np.mean((denoised - signal)**2):.4f}")
print(f"Improvement: {100*(1 - np.mean((denoised - signal)**2) / np.mean((noisy_signal - signal)**2)):.1f}%")
```

---

## Efficient SVD Computation

### For Large Sparse Matrices

```python
from scipy.sparse import random as sparse_random
from scipy.sparse.linalg import svds

# Large sparse matrix
m, n = 10000, 5000
density = 0.01  # 1% non-zero
A_sparse = sparse_random(m, n, density=density, format='csr')

print(f"Matrix shape: {A_sparse.shape}")
print(f"Non-zero elements: {A_sparse.nnz} ({100*A_sparse.nnz/(m*n):.2f}%)")

# Truncated SVD for top-k components (much faster than full SVD)
k = 10
U, S, Vt = svds(A_sparse, k=k)

print(f"\nTop {k} singular values: {S[::-1]}")  # svds returns ascending order
```

### Randomized SVD (Even Faster)

```python
from sklearn.utils.extmath import randomized_svd

# For dense but large matrices
np.random.seed(42)
A_large = np.random.randn(5000, 2000)

import time

# Standard SVD (slow for large matrices)
start = time.time()
U1, S1, Vt1 = np.linalg.svd(A_large, full_matrices=False)
print(f"Full SVD time: {time.time() - start:.2f}s")

# Randomized SVD (much faster)
k = 50
start = time.time()
U2, S2, Vt2 = randomized_svd(A_large, n_components=k, random_state=42)
print(f"Randomized SVD (k={k}) time: {time.time() - start:.2f}s")

# Quality check
print(f"\nTop 10 singular values match: {np.allclose(S1[:10], S2[:10], rtol=0.01)}")
```

---

## Summary and Key Takeaways

### What You Learned

| Concept | Formula/Definition | Application |
|---------|-------------------|-------------|
| SVD | $\mathbf{A} = \mathbf{U\Sigma V}^T$ | Factorize any matrix |
| Singular values | Diagonal of $\mathbf{\Sigma}$ | Importance of each component |
| Low-rank approximation | Keep top-k singular values | Compression, denoising |
| U columns | Left singular vectors | Row space representation |
| V columns | Right singular vectors | Column space representation |

### When to Use SVD

| Task | Why SVD? |
|------|----------|
| Image compression | Optimal low-rank approximation |
| Recommender systems | Matrix completion |
| Text analysis (LSA) | Semantic relationships |
| Noise reduction | Separate signal from noise |
| Pseudoinverse | $\mathbf{A}^+ = \mathbf{V\Sigma}^+\mathbf{U}^T$ |

### NumPy/SciPy Functions

```python
# Dense matrix
U, S, Vt = np.linalg.svd(A, full_matrices=False)

# Sparse matrix (top-k only)
from scipy.sparse.linalg import svds
U, S, Vt = svds(A_sparse, k=k)

# Large matrices (randomized)
from sklearn.utils.extmath import randomized_svd
U, S, Vt = randomized_svd(A, n_components=k)
```

---

## Practice Exercises

### Exercise 1: Image Compression
Load a real image, apply SVD compression at different ranks, and plot PSNR vs compression ratio.

### Exercise 2: Build a Movie Recommender
Use the MovieLens dataset to build an SVD-based recommender. Evaluate with RMSE on held-out ratings.

### Exercise 3: Document Clustering
Apply LSA to a text corpus and cluster documents in the reduced space.

---

## Frequently Asked Questions

### Q: SVD vs PCA - what's the difference?
**A:** PCA is SVD applied to centered data's covariance matrix. For centered data $\mathbf{X}$: PCA eigenvectors = right singular vectors of $\mathbf{X}$.

### Q: How do I choose the number of components k?
**A:** Common methods:
- Explained variance threshold (e.g., 95%)
- Elbow in singular value plot
- Cross-validation for downstream task

### Q: Can SVD handle missing values?
**A:** Standard SVD cannot. Use iterative methods like Alternating Least Squares (ALS) or matrix completion algorithms.

---

## Next Steps

1. **Read Next**: [PCA Deep Dive](/docs/ai-ml/foundations/mathematics/linear-algebra/pca-tutorial/)
2. **Practice**: Build a movie recommender with real data
3. **Explore**: Try truncated SVD on NLP tasks

---

*Continue with our [Mathematics for ML series](/docs/ai-ml/foundations/mathematics/). Next: [PCA Deep Dive: Principal Component Analysis](/docs/ai-ml/foundations/mathematics/linear-algebra/pca-tutorial/).*
