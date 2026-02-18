---
title: "Linear Algebra with NumPy: Complete Practical Guide for Machine Learning"
description: "Master linear algebra operations using NumPy with practical examples. Learn matrix operations, decompositions, solving systems, and performance optimization for ML applications."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 6
toc: true
categories: ["AI/ML", "Programming"]
tags: ["numpy", "linear algebra", "matrix operations", "python", "machine learning", "scipy"]
series: ["Mathematics for ML"]
---

# Linear Algebra with NumPy: Complete Practical Guide for Machine Learning

**"NumPy is the foundation of the Python scientific computing ecosystem."** — Travis Oliphant, NumPy Creator

Linear algebra operations are at the heart of every machine learning algorithm. Whether you're implementing gradient descent, training neural networks, or performing dimensionality reduction, you'll be using NumPy's linear algebra capabilities. This comprehensive guide covers everything you need to know to efficiently perform linear algebra with NumPy.

---

## Why Use NumPy for Linear Algebra?

### NumPy vs Pure Python Performance

NumPy isn't just convenient—it's essential for performance. Here's why:

```python
import numpy as np
import time

# Pure Python matrix multiplication
def python_matmul(A, B):
    n = len(A)
    m = len(B[0])
    k = len(B)
    result = [[0] * m for _ in range(n)]
    for i in range(n):
        for j in range(m):
            for p in range(k):
                result[i][j] += A[i][p] * B[p][j]
    return result

# Benchmark
size = 200
A_list = [[1.0] * size for _ in range(size)]
B_list = [[1.0] * size for _ in range(size)]
A_np = np.ones((size, size))
B_np = np.ones((size, size))

# Python version
start = time.time()
result_python = python_matmul(A_list, B_list)
python_time = time.time() - start

# NumPy version
start = time.time()
result_numpy = A_np @ B_np
numpy_time = time.time() - start

print(f"Pure Python: {python_time:.3f} seconds")
print(f"NumPy: {numpy_time:.6f} seconds")
print(f"Speedup: {python_time / numpy_time:.0f}x")
```

**Typical Output:**
```
Pure Python: 3.245 seconds
NumPy: 0.000891 seconds
Speedup: 3641x
```

### Why Is NumPy So Fast?

| Feature | Pure Python | NumPy |
|---------|-------------|-------|
| Memory Layout | Objects scattered in memory | Contiguous C-style arrays |
| Data Types | Dynamic typing overhead | Fixed, optimized dtypes |
| Operations | Python interpreter loops | BLAS/LAPACK C/Fortran |
| Parallelism | Single-threaded | Multi-threaded BLAS |
| Vectorization | No SIMD | Hardware SIMD support |

---

## How Do You Create Vectors and Matrices in NumPy?

### Creating Arrays

```python
import numpy as np

# 1D Array (Vector)
vector = np.array([1, 2, 3, 4, 5])
print(f"Vector: {vector}, Shape: {vector.shape}")

# 2D Array (Matrix)
matrix = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])
print(f"Matrix shape: {matrix.shape}")

# Common initialization patterns
zeros = np.zeros((3, 4))          # 3x4 matrix of zeros
ones = np.ones((2, 3))            # 2x3 matrix of ones
identity = np.eye(4)              # 4x4 identity matrix
diagonal = np.diag([1, 2, 3, 4])  # Diagonal matrix

# Ranges and sequences
arange = np.arange(0, 10, 2)      # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)  # [0, 0.25, 0.5, 0.75, 1.0]

# Random matrices
uniform = np.random.rand(3, 4)         # Uniform [0, 1)
normal = np.random.randn(3, 4)         # Standard normal
integers = np.random.randint(0, 10, (3, 4))  # Random integers

# Reproducible random
np.random.seed(42)
reproducible = np.random.randn(3, 3)

# Full control with random generator (recommended)
rng = np.random.default_rng(seed=42)
controlled = rng.standard_normal((3, 3))
```

### Understanding Data Types

```python
import numpy as np

# Default float64
float_array = np.array([1.0, 2.0, 3.0])
print(f"Float dtype: {float_array.dtype}")  # float64

# Specify dtype
float32 = np.array([1, 2, 3], dtype=np.float32)
int32 = np.array([1, 2, 3], dtype=np.int32)
complex64 = np.array([1+2j, 3+4j], dtype=np.complex64)

# Memory usage comparison
sizes = [100, 1000, 10000]
for size in sizes:
    float64_arr = np.random.randn(size, size)
    float32_arr = float64_arr.astype(np.float32)
    
    f64_mb = float64_arr.nbytes / 1e6
    f32_mb = float32_arr.nbytes / 1e6
    
    print(f"Size {size}x{size}: float64={f64_mb:.1f}MB, float32={f32_mb:.1f}MB")

# dtype for ML (float32 is usually sufficient and faster)
weights = np.random.randn(1000, 1000).astype(np.float32)
```

---

## What Are the Essential Matrix Operations?

### Basic Arithmetic Operations

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# Element-wise operations
addition = A + B
subtraction = A - B
multiplication = A * B  # Element-wise!
division = A / B

print(f"Element-wise multiplication:\n{multiplication}")

# Scalar operations
scaled = A * 3
shifted = A + 10

# Universal functions (ufuncs)
squared = np.square(A)
sqrt = np.sqrt(A.astype(float))
exp = np.exp(A)
log = np.log(A.astype(float))
sin = np.sin(A)
abs_val = np.abs(A)
```

### Matrix Multiplication

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6]])  # 2x3

B = np.array([[7, 8],
              [9, 10],
              [11, 12]])   # 3x2

# Matrix multiplication (3 equivalent ways)
C1 = A @ B                    # Recommended (Python 3.5+)
C2 = np.matmul(A, B)          # Function form
C3 = np.dot(A, B)             # Legacy (works for 2D)

print(f"Result shape: {C1.shape}")  # (2, 2)
print(f"Matrix product:\n{C1}")

# Batch matrix multiplication
batch_A = np.random.randn(10, 3, 4)  # 10 matrices of 3x4
batch_B = np.random.randn(10, 4, 5)  # 10 matrices of 4x5
batch_C = batch_A @ batch_B          # 10 matrices of 3x5
print(f"Batch result: {batch_C.shape}")

# Vector operations
v1 = np.array([1, 2, 3])
v2 = np.array([4, 5, 6])

dot_product = np.dot(v1, v2)       # Scalar: 32
outer_product = np.outer(v1, v2)   # 3x3 matrix
cross_product = np.cross(v1, v2)   # 3D vector

print(f"Dot product: {dot_product}")
print(f"Outer product:\n{outer_product}")
print(f"Cross product: {cross_product}")
```

### Transpose and Reshaping

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6]])

# Transpose
A_T = A.T
print(f"Original: {A.shape}, Transposed: {A_T.shape}")

# Reshape
reshaped = A.reshape(3, 2)
reshaped_auto = A.reshape(-1)  # Flatten
reshaped_cols = A.reshape(-1, 1)  # Column vector

# Flatten vs Ravel
flattened = A.flatten()  # Returns copy
raveled = A.ravel()      # Returns view (if possible)

# Stack operations
v1 = np.array([1, 2, 3])
v2 = np.array([4, 5, 6])

h_stacked = np.hstack([v1, v2])      # [1, 2, 3, 4, 5, 6]
v_stacked = np.vstack([v1, v2])      # [[1,2,3], [4,5,6]]
stacked = np.stack([v1, v2], axis=0)  # Same as vstack

# Concatenate
concatenated = np.concatenate([A, A], axis=0)  # Vertical
concatenated_h = np.concatenate([A, A], axis=1)  # Horizontal

# Split
split_arrays = np.split(A, 2, axis=1)  # Split into 2 along columns
```

---

## How Do You Use NumPy's Linear Algebra Module?

NumPy provides a dedicated `linalg` submodule for linear algebra operations.

### Matrix Properties

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 10]])

# Determinant
det = np.linalg.det(A)
print(f"Determinant: {det:.4f}")

# Matrix rank
rank = np.linalg.matrix_rank(A)
print(f"Rank: {rank}")

# Trace (sum of diagonal)
trace = np.trace(A)
print(f"Trace: {trace}")

# Norms
frobenius_norm = np.linalg.norm(A)           # Frobenius (default)
frobenius_norm_explicit = np.linalg.norm(A, 'fro')
spectral_norm = np.linalg.norm(A, 2)         # Largest singular value
l1_norm = np.linalg.norm(A, 1)               # Max column sum
inf_norm = np.linalg.norm(A, np.inf)         # Max row sum
nuclear_norm = np.linalg.norm(A, 'nuc')      # Sum of singular values

print(f"Frobenius norm: {frobenius_norm:.4f}")
print(f"Spectral norm: {spectral_norm:.4f}")

# Vector norms
v = np.array([3, 4])
l1 = np.linalg.norm(v, 1)    # |3| + |4| = 7
l2 = np.linalg.norm(v, 2)    # sqrt(9 + 16) = 5
linf = np.linalg.norm(v, np.inf)  # max(|3|, |4|) = 4

# Condition number (indicates numerical stability)
cond = np.linalg.cond(A)
print(f"Condition number: {cond:.4f}")
```

### Matrix Inverse and Pseudo-Inverse

```python
import numpy as np

# Square invertible matrix
A = np.array([[4, 7],
              [2, 6]])

# Matrix inverse
A_inv = np.linalg.inv(A)
print(f"Inverse:\n{A_inv}")

# Verify: A @ A_inv = I
identity_check = A @ A_inv
print(f"A @ A_inv:\n{identity_check}")

# For numerical stability, prefer solving directly over inverting
# Instead of: x = A_inv @ b
# Use: x = np.linalg.solve(A, b)

# Moore-Penrose pseudo-inverse (for non-square or singular matrices)
B = np.array([[1, 2, 3],
              [4, 5, 6]])  # 2x3 matrix

B_pinv = np.linalg.pinv(B)
print(f"Pseudo-inverse shape: {B_pinv.shape}")  # (3, 2)

# Verify: B @ B_pinv @ B ≈ B
print(f"Reconstruction check:\n{B @ B_pinv @ B}")
```

### Solving Linear Systems

```python
import numpy as np

# System: Ax = b
# 3x + 2y = 7
# x + 4y = 9

A = np.array([[3, 2],
              [1, 4]])
b = np.array([7, 9])

# Method 1: Direct solve (recommended)
x = np.linalg.solve(A, b)
print(f"Solution: x={x[0]:.4f}, y={x[1]:.4f}")

# Verify
print(f"Verification: A @ x = {A @ x}")

# Method 2: Using inverse (less stable, slower)
x_inv = np.linalg.inv(A) @ b
print(f"Via inverse: {x_inv}")

# Multiple right-hand sides
B = np.array([[7, 14],
              [9, 18]])  # Two systems
X = np.linalg.solve(A, B)
print(f"Multiple solutions:\n{X}")

# Least squares solution (for overdetermined systems)
# More equations than unknowns
A_overdetermined = np.array([[1, 1],
                             [2, 1],
                             [3, 1],
                             [4, 1]])
b_overdetermined = np.array([2.1, 3.9, 6.2, 7.8])

# Using lstsq
x_ls, residuals, rank, s = np.linalg.lstsq(A_overdetermined, 
                                            b_overdetermined, 
                                            rcond=None)
print(f"Least squares solution: slope={x_ls[0]:.4f}, intercept={x_ls[1]:.4f}")
print(f"Residuals: {residuals}")
```

---

## What Are the Key Matrix Decompositions?

### Eigenvalue Decomposition

```python
import numpy as np

# Symmetric matrix (guaranteed real eigenvalues)
A = np.array([[4, 2, 1],
              [2, 5, 3],
              [1, 3, 6]])

# Compute eigenvalues and eigenvectors
eigenvalues, eigenvectors = np.linalg.eig(A)

print(f"Eigenvalues: {eigenvalues}")
print(f"Eigenvectors:\n{eigenvectors}")

# For symmetric matrices (faster, more stable)
eigenvalues_sym, eigenvectors_sym = np.linalg.eigh(A)
print(f"\nSymmetric eigenvalues: {eigenvalues_sym}")

# Eigenvalues only (faster if eigenvectors not needed)
eigenvalues_only = np.linalg.eigvals(A)
eigenvalues_only_sym = np.linalg.eigvalsh(A)

# Verify: A @ v = λ * v
for i in range(len(eigenvalues)):
    v = eigenvectors[:, i]
    lam = eigenvalues[i]
    Av = A @ v
    lam_v = lam * v
    print(f"λ={lam:.4f}: ||Av - λv|| = {np.linalg.norm(Av - lam_v):.2e}")

# Reconstruct matrix from eigendecomposition
A_reconstructed = eigenvectors @ np.diag(eigenvalues) @ np.linalg.inv(eigenvectors)
print(f"\nReconstruction error: {np.linalg.norm(A - A_reconstructed):.2e}")
```

### Singular Value Decomposition (SVD)

```python
import numpy as np

# Any matrix (doesn't need to be square)
A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9],
              [10, 11, 12]])

# Full SVD
U, S, Vt = np.linalg.svd(A)

print(f"U shape: {U.shape}")     # (4, 4)
print(f"S shape: {S.shape}")     # (3,) - singular values
print(f"Vt shape: {Vt.shape}")   # (3, 3)

print(f"\nSingular values: {S}")

# Reconstruct full matrix
S_full = np.zeros((4, 3))
np.fill_diagonal(S_full, S)
A_reconstructed = U @ S_full @ Vt
print(f"Reconstruction error: {np.linalg.norm(A - A_reconstructed):.2e}")

# Reduced SVD (more efficient for tall/wide matrices)
U_reduced, S_reduced, Vt_reduced = np.linalg.svd(A, full_matrices=False)
print(f"\nReduced U shape: {U_reduced.shape}")   # (4, 3)
print(f"Reduced Vt shape: {Vt_reduced.shape}")   # (3, 3)

# Low-rank approximation (for compression/denoising)
def low_rank_approx(A, k):
    """Approximate matrix A using top k singular values."""
    U, S, Vt = np.linalg.svd(A, full_matrices=False)
    return U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]

# Example: Image compression
image = np.random.randn(100, 100)
for k in [5, 10, 20, 50]:
    approx = low_rank_approx(image, k)
    error = np.linalg.norm(image - approx) / np.linalg.norm(image)
    compression = k * (100 + 100 + 1) / (100 * 100)
    print(f"k={k}: relative error={error:.4f}, compression={compression:.2%}")
```

### QR Decomposition

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 10]])

# QR decomposition
Q, R = np.linalg.qr(A)

print(f"Q (orthogonal):\n{Q}")
print(f"\nR (upper triangular):\n{R}")

# Verify orthogonality: Q.T @ Q = I
print(f"\nQ.T @ Q:\n{Q.T @ Q}")

# Verify: A = Q @ R
print(f"\nReconstruction error: {np.linalg.norm(A - Q @ R):.2e}")

# QR for solving least squares
A_ls = np.array([[1, 1],
                 [2, 1],
                 [3, 1]])
b_ls = np.array([2, 4, 5.5])

Q, R = np.linalg.qr(A_ls)
# Solve R @ x = Q.T @ b
x_qr = np.linalg.solve(R, Q.T @ b_ls)
print(f"\nLeast squares via QR: {x_qr}")

# Reduced QR for tall matrices
A_tall = np.random.randn(100, 5)
Q_full, R_full = np.linalg.qr(A_tall, mode='complete')
Q_reduced, R_reduced = np.linalg.qr(A_tall, mode='reduced')

print(f"\nFull Q shape: {Q_full.shape}")       # (100, 100)
print(f"Reduced Q shape: {Q_reduced.shape}")   # (100, 5)
```

### Cholesky Decomposition

```python
import numpy as np

# Positive definite matrix
A = np.array([[4, 2, 1],
              [2, 5, 2],
              [1, 2, 6]])

# Cholesky decomposition: A = L @ L.T
L = np.linalg.cholesky(A)
print(f"L (lower triangular):\n{L}")

# Verify: A = L @ L.T
print(f"\nReconstruction:\n{L @ L.T}")
print(f"Reconstruction error: {np.linalg.norm(A - L @ L.T):.2e}")

# Solving linear systems with Cholesky (faster for SPD matrices)
b = np.array([1, 2, 3])

# Step 1: Solve L @ y = b
y = np.linalg.solve(L, b)

# Step 2: Solve L.T @ x = y
x = np.linalg.solve(L.T, y)

print(f"\nSolution via Cholesky: {x}")
print(f"Verification: {A @ x}")

# Create positive definite matrix from any matrix
random_matrix = np.random.randn(4, 4)
positive_definite = random_matrix @ random_matrix.T + np.eye(4) * 0.1
L_random = np.linalg.cholesky(positive_definite)
```

---

## How Can You Use SciPy for Advanced Operations?

SciPy extends NumPy with more specialized linear algebra functions.

### SciPy vs NumPy Comparison

```python
import numpy as np
from scipy import linalg as scipy_linalg

A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 10]])

# Both work similarly for basic operations
np_inv = np.linalg.inv(A)
scipy_inv = scipy_linalg.inv(A)

print(f"Difference: {np.linalg.norm(np_inv - scipy_inv):.2e}")
```

### Sparse Matrices

```python
from scipy import sparse
from scipy.sparse import linalg as sparse_linalg
import numpy as np

# Create sparse matrix
# Compressed Sparse Row (CSR) format
row = np.array([0, 0, 1, 2, 2, 2])
col = np.array([0, 2, 2, 0, 1, 2])
data = np.array([1, 2, 3, 4, 5, 6])
sparse_matrix = sparse.csr_matrix((data, (row, col)), shape=(3, 3))

print(f"Sparse matrix:\n{sparse_matrix.toarray()}")
print(f"Non-zero elements: {sparse_matrix.nnz}")
print(f"Sparsity: {1 - sparse_matrix.nnz / sparse_matrix.shape[0]**2:.2%}")

# Memory comparison
size = 1000
dense = np.eye(size)  # Dense identity matrix
sparse_eye = sparse.eye(size)  # Sparse identity matrix

print(f"\nDense memory: {dense.nbytes / 1e6:.2f} MB")
print(f"Sparse memory: {sparse_eye.data.nbytes / 1e6:.2f} MB")

# Sparse operations
A_sparse = sparse.random(1000, 1000, density=0.01, format='csr')
b = np.random.randn(1000)

# Solve sparse system (iterative methods)
x, info = sparse_linalg.cg(A_sparse.T @ A_sparse, A_sparse.T @ b)
print(f"\nSparse solve converged: {info == 0}")

# Sparse eigenvalues (find top k)
A_symmetric = (A_sparse + A_sparse.T) / 2
eigenvalues, eigenvectors = sparse_linalg.eigsh(A_symmetric, k=5)
print(f"Top 5 eigenvalues: {eigenvalues}")
```

### LU Decomposition

```python
from scipy import linalg
import numpy as np

A = np.array([[2, 1, 1],
              [4, 3, 3],
              [8, 7, 9]])

# LU decomposition with partial pivoting
P, L, U = linalg.lu(A)

print(f"P (permutation):\n{P}")
print(f"\nL (lower triangular):\n{L}")
print(f"\nU (upper triangular):\n{U}")

# Verify: A = P @ L @ U
print(f"\nReconstruction error: {np.linalg.norm(A - P @ L @ U):.2e}")

# Efficient solving with LU factorization
lu_factorization = linalg.lu_factor(A)

# Solve for multiple right-hand sides efficiently
b1 = np.array([1, 2, 3])
b2 = np.array([4, 5, 6])

x1 = linalg.lu_solve(lu_factorization, b1)
x2 = linalg.lu_solve(lu_factorization, b2)

print(f"\nSolution 1: {x1}")
print(f"Solution 2: {x2}")
```

---

## What Are Best Practices for Performance?

### Memory Efficiency

```python
import numpy as np

# Use views instead of copies
A = np.random.randn(1000, 1000)

# This creates a view (shares memory)
A_view = A[:500, :500]
print(f"View shares base: {A_view.base is A}")

# This creates a copy (new memory)
A_copy = A[:500, :500].copy()
print(f"Copy shares base: {A_copy.base is A}")

# Check if contiguous (important for performance)
A_slice = A[::2, ::2]  # Non-contiguous slice
print(f"Original contiguous: {A.flags['C_CONTIGUOUS']}")
print(f"Slice contiguous: {A_slice.flags['C_CONTIGUOUS']}")

# Make contiguous if needed
A_contiguous = np.ascontiguousarray(A_slice)

# Pre-allocate arrays instead of growing
# BAD: Growing array
results_bad = []
for i in range(1000):
    results_bad.append(np.random.randn(100))
results_bad = np.array(results_bad)

# GOOD: Pre-allocated
results_good = np.empty((1000, 100))
for i in range(1000):
    results_good[i] = np.random.randn(100)

# In-place operations (no new memory)
A = np.random.randn(1000, 1000)
A += 5            # In-place
A *= 2            # In-place
np.multiply(A, 3, out=A)  # Explicit in-place
```

### Vectorization Patterns

```python
import numpy as np
import time

# Example: Compute pairwise distances

def slow_pairwise_distance(X):
    """Slow: nested loops."""
    n = len(X)
    D = np.zeros((n, n))
    for i in range(n):
        for j in range(n):
            D[i, j] = np.sqrt(np.sum((X[i] - X[j])**2))
    return D

def fast_pairwise_distance(X):
    """Fast: vectorized using broadcasting."""
    # X shape: (n, d)
    # X[:, np.newaxis, :] shape: (n, 1, d)
    # X[np.newaxis, :, :] shape: (1, n, d)
    diff = X[:, np.newaxis, :] - X[np.newaxis, :, :]  # (n, n, d)
    return np.sqrt(np.sum(diff**2, axis=2))  # (n, n)

def fastest_pairwise_distance(X):
    """Fastest: using einsum and avoiding sqrt."""
    # ||a - b||^2 = ||a||^2 + ||b||^2 - 2 * a.b
    sq_norms = np.einsum('ij,ij->i', X, X)
    D_squared = sq_norms[:, np.newaxis] + sq_norms[np.newaxis, :] - 2 * X @ X.T
    return np.sqrt(np.maximum(D_squared, 0))  # Avoid negative due to numerical errors

# Benchmark
X = np.random.randn(500, 10)

start = time.time()
D1 = slow_pairwise_distance(X)
print(f"Slow: {time.time() - start:.3f}s")

start = time.time()
D2 = fast_pairwise_distance(X)
print(f"Fast: {time.time() - start:.3f}s")

start = time.time()
D3 = fastest_pairwise_distance(X)
print(f"Fastest: {time.time() - start:.3f}s")

print(f"Max difference: {np.max(np.abs(D1 - D3)):.2e}")
```

### Using BLAS Backend

```python
import numpy as np

# Check BLAS configuration
np.show_config()

# Typical output shows which BLAS is being used:
# - OpenBLAS (common on Linux)
# - Intel MKL (fastest on Intel CPUs)
# - Apple Accelerate (Mac)

# Threading control (for multi-threaded BLAS)
import os
os.environ['OMP_NUM_THREADS'] = '4'  # Set before importing numpy
os.environ['MKL_NUM_THREADS'] = '4'  # For Intel MKL
os.environ['OPENBLAS_NUM_THREADS'] = '4'  # For OpenBLAS

# Or dynamically using threadpoolctl
try:
    from threadpoolctl import threadpool_limits
    
    with threadpool_limits(limits=1, user_api='blas'):
        # Single-threaded operations here
        result = np.linalg.inv(np.random.randn(1000, 1000))
except ImportError:
    print("Install threadpoolctl for thread control")
```

---

## Real-World ML Applications

### Principal Component Analysis (PCA)

```python
import numpy as np

def pca_from_scratch(X, n_components):
    """
    Implement PCA using NumPy linear algebra.
    
    Args:
        X: Data matrix (n_samples, n_features)
        n_components: Number of principal components
    
    Returns:
        X_reduced: Transformed data
        components: Principal components
        explained_variance_ratio: Variance explained by each component
    """
    # Center the data
    X_centered = X - X.mean(axis=0)
    
    # Compute covariance matrix
    n_samples = X.shape[0]
    cov_matrix = (X_centered.T @ X_centered) / (n_samples - 1)
    
    # Eigendecomposition
    eigenvalues, eigenvectors = np.linalg.eigh(cov_matrix)
    
    # Sort by eigenvalue (descending)
    idx = np.argsort(eigenvalues)[::-1]
    eigenvalues = eigenvalues[idx]
    eigenvectors = eigenvectors[:, idx]
    
    # Select top k components
    components = eigenvectors[:, :n_components]
    
    # Transform data
    X_reduced = X_centered @ components
    
    # Explained variance ratio
    explained_variance_ratio = eigenvalues[:n_components] / eigenvalues.sum()
    
    return X_reduced, components, explained_variance_ratio

# Example usage
np.random.seed(42)
X = np.random.randn(100, 10)  # 100 samples, 10 features

X_reduced, components, variance_ratio = pca_from_scratch(X, n_components=3)

print(f"Original shape: {X.shape}")
print(f"Reduced shape: {X_reduced.shape}")
print(f"Variance explained: {variance_ratio}")
print(f"Total variance: {variance_ratio.sum():.2%}")
```

### Linear Regression with Normal Equations

```python
import numpy as np

def linear_regression_normal_eq(X, y):
    """
    Solve linear regression using normal equations.
    
    θ = (X.T @ X)^(-1) @ X.T @ y
    
    Args:
        X: Feature matrix (n_samples, n_features)
        y: Target vector (n_samples,)
    
    Returns:
        coefficients: Regression coefficients
    """
    # Add bias term (column of ones)
    X_bias = np.column_stack([np.ones(len(X)), X])
    
    # Method 1: Direct inverse (not recommended)
    # coefficients = np.linalg.inv(X_bias.T @ X_bias) @ X_bias.T @ y
    
    # Method 2: Solve normal equations (more stable)
    # coefficients = np.linalg.solve(X_bias.T @ X_bias, X_bias.T @ y)
    
    # Method 3: Least squares (most stable)
    coefficients, residuals, rank, s = np.linalg.lstsq(X_bias, y, rcond=None)
    
    return coefficients

# Generate synthetic data
np.random.seed(42)
n_samples = 100
X = np.random.randn(n_samples, 3)
true_coef = np.array([2, 3, -1])
y = X @ true_coef + 5 + np.random.randn(n_samples) * 0.5  # y = 2*x1 + 3*x2 - x3 + 5 + noise

# Fit model
coefficients = linear_regression_normal_eq(X, y)

print(f"True coefficients: bias=5, coef={true_coef}")
print(f"Estimated: bias={coefficients[0]:.4f}, coef={coefficients[1:]}")

# Predictions
X_bias = np.column_stack([np.ones(len(X)), X])
y_pred = X_bias @ coefficients
rmse = np.sqrt(np.mean((y - y_pred)**2))
print(f"RMSE: {rmse:.4f}")
```

### Neural Network Forward Pass

```python
import numpy as np

def sigmoid(x):
    return 1 / (1 + np.exp(-np.clip(x, -500, 500)))

def relu(x):
    return np.maximum(0, x)

def softmax(x):
    exp_x = np.exp(x - x.max(axis=-1, keepdims=True))
    return exp_x / exp_x.sum(axis=-1, keepdims=True)

class SimpleNeuralNetwork:
    """Simple feedforward neural network using NumPy."""
    
    def __init__(self, layer_sizes):
        """
        Initialize network weights.
        
        Args:
            layer_sizes: List of layer sizes, e.g., [784, 128, 64, 10]
        """
        self.weights = []
        self.biases = []
        
        for i in range(len(layer_sizes) - 1):
            # He initialization
            w = np.random.randn(layer_sizes[i], layer_sizes[i+1]) * np.sqrt(2 / layer_sizes[i])
            b = np.zeros(layer_sizes[i+1])
            self.weights.append(w)
            self.biases.append(b)
    
    def forward(self, X):
        """
        Forward pass through the network.
        
        Args:
            X: Input data (batch_size, input_size)
        
        Returns:
            Output probabilities (batch_size, num_classes)
        """
        activations = [X]
        
        # Hidden layers with ReLU
        for i in range(len(self.weights) - 1):
            z = activations[-1] @ self.weights[i] + self.biases[i]
            a = relu(z)
            activations.append(a)
        
        # Output layer with softmax
        z_out = activations[-1] @ self.weights[-1] + self.biases[-1]
        output = softmax(z_out)
        
        return output

# Example
np.random.seed(42)
nn = SimpleNeuralNetwork([784, 128, 64, 10])

# Simulated batch of 32 MNIST images (28x28 = 784)
X = np.random.randn(32, 784)
output = nn.forward(X)

print(f"Input shape: {X.shape}")
print(f"Output shape: {output.shape}")
print(f"Output sums to 1: {np.allclose(output.sum(axis=1), 1)}")
print(f"Sample prediction: {output[0].argmax()}")
```

---

## FAQs

### When should I use np.dot vs @ vs np.matmul?

- `@` (matmul operator): **Recommended** for Python 3.5+. Clear and concise.
- `np.matmul`: Function form of `@`. Use when you need to pass as argument.
- `np.dot`: Legacy. For 2D arrays, same as matmul. For vectors, gives dot product.

```python
# For matrices, all equivalent
result1 = A @ B
result2 = np.matmul(A, B)
result3 = np.dot(A, B)

# For vectors
v1, v2 = np.array([1, 2]), np.array([3, 4])
np.dot(v1, v2)  # Returns scalar: 11
v1 @ v2         # Returns scalar: 11
```

### How do I handle singular matrices?

```python
A = np.array([[1, 2], [2, 4]])  # Singular (rank 1)

# This raises LinAlgError
# np.linalg.inv(A)

# Use pseudo-inverse instead
A_pinv = np.linalg.pinv(A)

# Or check condition number first
cond = np.linalg.cond(A)
if cond > 1e10:
    print("Matrix is near-singular!")
```

### How can I make operations faster?

1. Use `float32` instead of `float64` when precision isn't critical
2. Use in-place operations when possible
3. Vectorize operations (avoid Python loops)
4. Use appropriate BLAS backend (MKL for Intel CPUs)
5. Consider sparse matrices for data with many zeros

---

## Key Takeaways

1. **NumPy provides 1000x+ speedup** over pure Python for linear algebra
2. **Use `@` operator** for matrix multiplication (clear and fast)
3. **Prefer `np.linalg.solve()`** over inverse for solving linear systems
4. **Choose the right decomposition**: SVD for general, Cholesky for SPD matrices
5. **Vectorize everything**: Avoid Python loops over array elements
6. **Consider memory**: Use views, pre-allocate, and choose appropriate dtypes
7. **Use SciPy** for sparse matrices and specialized decompositions

---

## Next Steps

Continue your linear algebra journey:

1. **[Eigenvalues and Eigenvectors](/docs/ai-ml/foundations/mathematics/linear-algebra/eigenvalues-eigenvectors-machine-learning/)** - Deep dive into spectral theory
2. **[SVD Explained](/docs/ai-ml/foundations/mathematics/linear-algebra/svd-explained/)** - Master singular value decomposition
3. **[Tensors in Deep Learning](/docs/ai-ml/foundations/mathematics/linear-algebra/tensors-deep-learning/)** - Multi-dimensional arrays for neural networks

---

## References

1. NumPy Documentation: "Linear Algebra" - https://numpy.org/doc/stable/reference/routines.linalg.html
2. SciPy Documentation: "Linear Algebra" - https://docs.scipy.org/doc/scipy/reference/linalg.html
3. Harris, C.R., et al. "Array programming with NumPy" (2020) - Nature
4. Golub, G.H., Van Loan, C.F. "Matrix Computations" (2013) - 4th Edition

---

*Last updated: January 2024. This guide is part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series. Share with fellow data scientists!*
