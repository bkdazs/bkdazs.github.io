---
title: "NumPy Complete Tutorial 2025: Arrays, Broadcasting, and Vectorization Mastery"
description: "Master NumPy for machine learning: array creation, broadcasting rules, vectorization techniques, memory optimization, and advanced indexing with practical examples."
date: 2025-01-26
lastmod: 2025-01-26
draft: false
weight: 1
categories: ["AI & ML", "Programming"]
tags: ["numpy tutorial", "numpy broadcasting", "vectorization python", "numpy arrays", "numpy machine learning"]
contributors: ["Quantitative Research Team"]
toc: true
math: true
---

NumPy is the foundational library for numerical computing in Python and the backbone of machine learning. Whether you're implementing neural networks, processing datasets, or running scientific simulations, mastering NumPy is essential for writing efficient, production-ready code.

## Why NumPy Is Essential for Machine Learning

Consider this: a Python list operation that takes 10 seconds can often be done in NumPy in 0.01 seconds—a 1000x speedup. This isn't magic; it's the power of vectorization, contiguous memory, and optimized C/Fortran backends.

> "NumPy arrays are the universal data exchange format for numerical data in Python. Every machine learning library speaks NumPy." — Travis Oliphant, NumPy Creator

### Real-World Applications in Indian Tech

Leading Indian companies leverage NumPy extensively:
- **Flipkart:** Feature engineering for recommendation systems
- **Razorpay:** Fraud detection signal processing
- **ISRO:** Satellite image processing
- **Zerodha:** Real-time trading analytics

## Section 1: NumPy Array Fundamentals

### What Is a NumPy Array and How Is It Different from Python Lists?

A NumPy array (ndarray) is a fixed-size, homogeneous container of elements. Unlike Python lists, arrays are stored in contiguous memory blocks, enabling vectorized operations.

**Key Differences:**

| Feature | Python List | NumPy Array |
|---------|------------|-------------|
| Data type | Mixed | Homogeneous |
| Memory | Non-contiguous | Contiguous |
| Speed | Slow loops | Fast vectorization |
| Size | Dynamic | Fixed (usually) |
| Operations | Element-wise loops | Vectorized |

```python
import numpy as np
import time

# Speed comparison: Python list vs NumPy
def compare_performance():
    """Demonstrate NumPy's performance advantage."""
    size = 1_000_000
    
    # Python list approach
    python_list = list(range(size))
    start = time.time()
    result_list = [x * 2 for x in python_list]
    python_time = time.time() - start
    
    # NumPy approach
    numpy_array = np.arange(size)
    start = time.time()
    result_array = numpy_array * 2
    numpy_time = time.time() - start
    
    print(f"Python list time: {python_time:.4f} seconds")
    print(f"NumPy array time: {numpy_time:.4f} seconds")
    print(f"NumPy is {python_time/numpy_time:.1f}x faster!")

compare_performance()
```

**Output:**
```
Python list time: 0.0821 seconds
NumPy array time: 0.0012 seconds
NumPy is 68.4x faster!
```

### How Do You Create NumPy Arrays?

```python
import numpy as np

# =============================================================================
# ARRAY CREATION METHODS
# =============================================================================

# 1. From Python sequences
arr_from_list = np.array([1, 2, 3, 4, 5])
arr_from_nested = np.array([[1, 2, 3], [4, 5, 6]])

# 2. Using built-in functions
zeros = np.zeros((3, 4))           # 3x4 array of zeros
ones = np.ones((2, 3, 4))          # 2x3x4 array of ones
full = np.full((3, 3), 7)          # 3x3 array filled with 7
empty = np.empty((2, 2))           # Uninitialized (fast!)
identity = np.eye(4)               # 4x4 identity matrix
diag = np.diag([1, 2, 3, 4])       # Diagonal matrix

# 3. Ranges and sequences
range_arr = np.arange(0, 10, 2)    # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)    # 5 evenly spaced from 0 to 1
logspace = np.logspace(0, 3, 4)    # 4 log-spaced from 10^0 to 10^3

# 4. Random arrays
np.random.seed(42)  # For reproducibility
uniform = np.random.random((3, 3))          # Uniform [0, 1)
normal = np.random.randn(1000)              # Standard normal
integers = np.random.randint(1, 100, (3, 3)) # Random integers

# 5. From existing arrays
arr = np.array([1, 2, 3, 4, 5])
like_zeros = np.zeros_like(arr)     # Same shape, filled with zeros
like_ones = np.ones_like(arr)       # Same shape, filled with ones
copied = arr.copy()                  # Deep copy

print("=== Array Creation Examples ===")
print(f"From list: {arr_from_list}")
print(f"Zeros shape: {zeros.shape}")
print(f"Linspace: {linspace}")
print(f"Random uniform sample:\n{uniform}")
```

### What Are NumPy Data Types (dtypes)?

NumPy supports many numeric data types. Choosing the right dtype is crucial for memory efficiency and numerical precision.

```python
# =============================================================================
# DATA TYPES (dtypes)
# =============================================================================

# Common dtypes
dtypes_demo = {
    'int8': np.int8,      # -128 to 127
    'int16': np.int16,    # -32,768 to 32,767
    'int32': np.int32,    # -2B to 2B
    'int64': np.int64,    # Default for integers
    'float16': np.float16, # Half precision (ML training)
    'float32': np.float32, # Single precision (GPU default)
    'float64': np.float64, # Double precision (default)
    'bool': np.bool_,     # True/False
    'complex64': np.complex64,  # Complex numbers
}

# Specifying dtype at creation
arr_float32 = np.array([1, 2, 3], dtype=np.float32)
arr_int8 = np.zeros((1000, 1000), dtype=np.int8)

# Memory usage comparison
print("=== Memory Usage by dtype ===")
for dtype_name, dtype in [('float64', np.float64), ('float32', np.float32), 
                          ('float16', np.float16), ('int8', np.int8)]:
    arr = np.zeros((1000, 1000), dtype=dtype)
    print(f"{dtype_name}: {arr.nbytes / 1e6:.2f} MB")

# Type conversion (casting)
arr = np.array([1.5, 2.7, 3.2])
arr_int = arr.astype(np.int32)      # Truncates to [1, 2, 3]
arr_str = arr.astype(str)           # ['1.5', '2.7', '3.2']

print(f"\nOriginal: {arr}")
print(f"As int32: {arr_int}")
print(f"As string: {arr_str}")

# Mixed precision for ML (common pattern)
def demonstrate_mixed_precision():
    """Show memory savings with float16."""
    weights_64 = np.random.randn(1000, 1000).astype(np.float64)
    weights_32 = weights_64.astype(np.float32)
    weights_16 = weights_64.astype(np.float16)
    
    print("\n=== Memory for 1000x1000 Weight Matrix ===")
    print(f"float64: {weights_64.nbytes / 1e6:.2f} MB")
    print(f"float32: {weights_32.nbytes / 1e6:.2f} MB")
    print(f"float16: {weights_16.nbytes / 1e6:.2f} MB")
    
    # Precision comparison
    print(f"\nPrecision comparison (mean of random data):")
    print(f"float64: {np.mean(weights_64)}")
    print(f"float32: {np.mean(weights_32)}")
    print(f"float16: {np.mean(weights_16)}")

demonstrate_mixed_precision()
```

## Section 2: Array Indexing and Slicing

### How Does NumPy Indexing Work?

NumPy offers powerful indexing capabilities that go far beyond Python lists.

```python
# =============================================================================
# INDEXING AND SLICING
# =============================================================================

# Create sample array
arr = np.arange(24).reshape(4, 6)
print("Original array (4x6):")
print(arr)

# Basic indexing (same as Python)
print(f"\narr[0, 0] = {arr[0, 0]}")      # First element
print(f"arr[1, 2] = {arr[1, 2]}")        # Row 1, Col 2
print(f"arr[-1, -1] = {arr[-1, -1]}")    # Last element

# Slicing [start:stop:step]
print(f"\nFirst row: {arr[0, :]}")
print(f"First column: {arr[:, 0]}")
print(f"Every other row: \n{arr[::2, :]}")
print(f"Reversed rows: \n{arr[::-1, :]}")

# Subarray (creates a VIEW, not a copy!)
subarray = arr[1:3, 2:5]
print(f"\nSubarray [1:3, 2:5]:\n{subarray}")

# Important: Views vs Copies
print("\n=== Views vs Copies ===")
original = np.array([1, 2, 3, 4, 5])
view = original[1:4]      # This is a VIEW
copy = original[1:4].copy()  # This is a COPY

view[0] = 100
print(f"After modifying view, original: {original}")  # Original changed!

original = np.array([1, 2, 3, 4, 5])
copy = original[1:4].copy()
copy[0] = 100
print(f"After modifying copy, original: {original}")  # Original unchanged
```

### What Is Fancy Indexing?

Fancy indexing allows selecting arbitrary array elements using integer or boolean arrays.

```python
# =============================================================================
# FANCY INDEXING
# =============================================================================

arr = np.arange(12).reshape(3, 4)
print("Original array:")
print(arr)

# Integer array indexing
row_indices = np.array([0, 2, 2])
col_indices = np.array([1, 0, 3])
print(f"\nFancy index [0,2,2], [1,0,3]: {arr[row_indices, col_indices]}")

# Boolean (mask) indexing
mask = arr > 5
print(f"\nMask (arr > 5):\n{mask}")
print(f"Elements > 5: {arr[mask]}")

# Combining conditions
mask = (arr > 3) & (arr < 10)
print(f"\nElements where 3 < arr < 10: {arr[mask]}")

# np.where for conditional selection
result = np.where(arr > 5, arr, 0)  # Keep if > 5, else 0
print(f"\nnp.where(arr > 5, arr, 0):\n{result}")

# Practical example: Data filtering
np.random.seed(42)
data = np.random.randn(1000)

# Filter outliers (values > 2 standard deviations)
mean, std = data.mean(), data.std()
outlier_mask = np.abs(data - mean) > 2 * std
outliers = data[outlier_mask]
clean_data = data[~outlier_mask]

print(f"\n=== Outlier Detection ===")
print(f"Total points: {len(data)}")
print(f"Outliers (>2σ): {len(outliers)}")
print(f"Clean data: {len(clean_data)}")
```

### How Do You Select and Modify Array Elements Efficiently?

```python
# =============================================================================
# ADVANCED SELECTION PATTERNS
# =============================================================================

# np.take - faster than fancy indexing for repeated selections
arr = np.arange(10)
indices = [2, 5, 7, 3]
result = np.take(arr, indices)
print(f"np.take result: {result}")

# np.put - in-place assignment
arr = np.arange(10, dtype=float)
np.put(arr, [0, 3, 5], [100, 200, 300])
print(f"After np.put: {arr}")

# np.choose - select from multiple arrays
choices = [np.arange(5), np.arange(5) * 10, np.arange(5) * 100]
selector = np.array([0, 1, 2, 0, 1])  # Which array to pick from
result = np.choose(selector, choices)
print(f"np.choose result: {result}")

# np.select - multiple conditions
arr = np.arange(-5, 6)
conditions = [arr < -2, arr > 2, True]  # Last is default
choices = [-1, 1, 0]
result = np.select(conditions, choices)
print(f"\nnp.select (negative=-1, positive=1, else=0): {result}")

# Practical: One-hot encoding
def one_hot_encode(labels, num_classes):
    """Convert integer labels to one-hot encoding."""
    n = len(labels)
    one_hot = np.zeros((n, num_classes))
    one_hot[np.arange(n), labels] = 1
    return one_hot

labels = np.array([0, 2, 1, 0, 3])
one_hot = one_hot_encode(labels, 4)
print(f"\n=== One-Hot Encoding ===")
print(f"Labels: {labels}")
print(f"One-hot:\n{one_hot}")
```

## Section 3: Broadcasting Rules

### What Is Broadcasting and Why Is It Powerful?

Broadcasting is NumPy's ability to perform operations on arrays with different shapes. It eliminates the need for explicit loops and temporary arrays.

**Broadcasting Rules:**
1. If arrays have different dimensions, prepend 1s to the smaller array's shape
2. Arrays with size 1 along a dimension act as if they had the size of the larger array
3. Arrays are compatible if dimensions are equal or one of them is 1

```python
# =============================================================================
# BROADCASTING FUNDAMENTALS
# =============================================================================

# Simple example: scalar broadcast
arr = np.array([1, 2, 3, 4, 5])
result = arr * 2  # 2 is broadcast to [2, 2, 2, 2, 2]
print(f"arr * 2 = {result}")

# 1D array with 2D array
arr_2d = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])
arr_1d = np.array([10, 20, 30])

print("\n2D array:")
print(arr_2d)
print(f"\n1D array: {arr_1d}")

# Broadcasting along rows
result = arr_2d + arr_1d
print(f"\n2D + 1D (broadcast along rows):\n{result}")

# Broadcasting along columns (need reshape)
col_vector = np.array([[100], [200], [300]])
result = arr_2d + col_vector
print(f"\n2D + column vector (broadcast along cols):\n{result}")

# Visualize broadcasting
def visualize_broadcast(shape1, shape2):
    """Show how shapes are broadcast together."""
    # Pad shapes to same length
    ndim = max(len(shape1), len(shape2))
    shape1 = (1,) * (ndim - len(shape1)) + tuple(shape1)
    shape2 = (1,) * (ndim - len(shape2)) + tuple(shape2)
    
    result_shape = []
    compatible = True
    
    print(f"Shape 1: {shape1}")
    print(f"Shape 2: {shape2}")
    
    for s1, s2 in zip(shape1, shape2):
        if s1 == s2:
            result_shape.append(s1)
        elif s1 == 1:
            result_shape.append(s2)
        elif s2 == 1:
            result_shape.append(s1)
        else:
            compatible = False
            break
    
    if compatible:
        print(f"Result shape: {tuple(result_shape)}")
    else:
        print("Shapes are incompatible!")

print("\n=== Broadcasting Shape Analysis ===")
visualize_broadcast((3, 4), (4,))       # Common: row broadcast
visualize_broadcast((3, 4), (3, 1))     # Column broadcast
visualize_broadcast((3, 4, 5), (4, 1))  # 3D broadcast
visualize_broadcast((3, 4), (2, 3))     # Incompatible!
```

### How Do You Use Broadcasting for Machine Learning?

```python
# =============================================================================
# BROADCASTING IN ML APPLICATIONS
# =============================================================================

# 1. Normalize features (subtract mean, divide by std)
np.random.seed(42)
X = np.random.randn(100, 5) * [1, 10, 100, 0.1, 50] + [0, 5, -10, 2, 30]

# Column-wise statistics
mean = X.mean(axis=0)  # Shape: (5,)
std = X.std(axis=0)    # Shape: (5,)

# Broadcasting: X (100, 5) - mean (5,) -> (100, 5)
X_normalized = (X - mean) / std

print("=== Feature Normalization ===")
print(f"Original means: {X.mean(axis=0).round(2)}")
print(f"Normalized means: {X_normalized.mean(axis=0).round(6)}")
print(f"Original stds: {X.std(axis=0).round(2)}")
print(f"Normalized stds: {X_normalized.std(axis=0).round(6)}")

# 2. Compute pairwise distances (L2)
def compute_distances_broadcasting(X1, X2):
    """
    Compute pairwise L2 distances using broadcasting.
    
    X1: (n, d) - n points, d dimensions
    X2: (m, d) - m points, d dimensions
    
    Returns: (n, m) distance matrix
    """
    # X1: (n, 1, d) - X2: (1, m, d) = (n, m, d)
    diff = X1[:, np.newaxis, :] - X2[np.newaxis, :, :]
    distances = np.sqrt(np.sum(diff ** 2, axis=2))
    return distances

X1 = np.random.randn(5, 3)   # 5 points
X2 = np.random.randn(4, 3)   # 4 points

distances = compute_distances_broadcasting(X1, X2)
print(f"\n=== Pairwise Distances ===")
print(f"X1 shape: {X1.shape}, X2 shape: {X2.shape}")
print(f"Distance matrix shape: {distances.shape}")
print(f"Sample distances:\n{distances.round(2)}")

# 3. Softmax function
def softmax(x, axis=-1):
    """Numerically stable softmax using broadcasting."""
    x_max = x.max(axis=axis, keepdims=True)
    exp_x = np.exp(x - x_max)  # Subtract max for stability
    return exp_x / exp_x.sum(axis=axis, keepdims=True)

logits = np.random.randn(3, 5)  # 3 samples, 5 classes
probs = softmax(logits)
print(f"\n=== Softmax ===")
print(f"Logits:\n{logits.round(2)}")
print(f"Probabilities:\n{probs.round(3)}")
print(f"Row sums: {probs.sum(axis=1)}")  # Should all be 1.0

# 4. Batch matrix operations
def batch_dot_product(A, B):
    """
    Batch dot product: multiply corresponding matrices.
    
    A: (batch, n, m)
    B: (batch, m, k)
    Returns: (batch, n, k)
    """
    return np.einsum('bnm,bmk->bnk', A, B)

batch_size = 4
A = np.random.randn(batch_size, 3, 5)
B = np.random.randn(batch_size, 5, 2)
result = batch_dot_product(A, B)
print(f"\n=== Batch Matrix Multiply ===")
print(f"A shape: {A.shape}")
print(f"B shape: {B.shape}")
print(f"Result shape: {result.shape}")
```

## Section 4: Vectorization Techniques

### What Is Vectorization and How Does It Speed Up Code?

Vectorization replaces explicit Python loops with array operations that are executed in optimized C code.

```python
# =============================================================================
# VECTORIZATION FUNDAMENTALS
# =============================================================================

import time

def time_function(func, *args, n_runs=10):
    """Time a function averaged over n_runs."""
    times = []
    for _ in range(n_runs):
        start = time.time()
        result = func(*args)
        times.append(time.time() - start)
    return np.mean(times), result

# Example: Compute sum of squares

# Slow: Python loop
def sum_squares_loop(arr):
    total = 0
    for x in arr:
        total += x ** 2
    return total

# Fast: Vectorized
def sum_squares_vectorized(arr):
    return np.sum(arr ** 2)

# Even faster: Use np.dot (internally optimized)
def sum_squares_dot(arr):
    return np.dot(arr, arr)

# Benchmark
arr = np.random.randn(100000)

print("=== Sum of Squares Benchmark ===")
for name, func in [('Python loop', sum_squares_loop), 
                   ('Vectorized', sum_squares_vectorized),
                   ('Using dot', sum_squares_dot)]:
    time_taken, result = time_function(func, arr)
    print(f"{name:<15}: {time_taken*1000:.3f} ms, result={result:.2f}")
```

### How Do You Vectorize Common Machine Learning Operations?

```python
# =============================================================================
# VECTORIZING ML OPERATIONS
# =============================================================================

# 1. Vectorized gradient descent for linear regression
def linear_regression_gradient_descent(X, y, lr=0.01, n_iter=1000):
    """
    Vectorized gradient descent for linear regression.
    
    X: (n_samples, n_features)
    y: (n_samples,)
    """
    n_samples, n_features = X.shape
    
    # Initialize weights
    w = np.zeros(n_features)
    b = 0
    
    # Gradient descent (all vectorized!)
    for _ in range(n_iter):
        # Predictions: (n_samples,)
        y_pred = X @ w + b
        
        # Errors: (n_samples,)
        errors = y_pred - y
        
        # Gradients (vectorized over all samples)
        dw = (2/n_samples) * (X.T @ errors)  # (n_features,)
        db = (2/n_samples) * np.sum(errors)   # scalar
        
        # Update
        w -= lr * dw
        b -= lr * db
    
    return w, b

# Generate sample data
np.random.seed(42)
n_samples, n_features = 1000, 5
X = np.random.randn(n_samples, n_features)
true_w = np.array([1.5, -2.0, 3.0, -0.5, 1.0])
y = X @ true_w + 0.1 * np.random.randn(n_samples)

# Train
w, b = linear_regression_gradient_descent(X, y, lr=0.1, n_iter=1000)
print("=== Vectorized Linear Regression ===")
print(f"True weights: {true_w}")
print(f"Learned weights: {w.round(3)}")

# 2. Vectorized k-means clustering
def kmeans_vectorized(X, k, n_iter=100):
    """
    Vectorized k-means clustering.
    
    X: (n_samples, n_features)
    k: number of clusters
    """
    n_samples = len(X)
    
    # Initialize centroids randomly
    indices = np.random.choice(n_samples, k, replace=False)
    centroids = X[indices].copy()
    
    for _ in range(n_iter):
        # Compute distances: (n_samples, k)
        # Using broadcasting: X (n, 1, d) - centroids (1, k, d)
        diff = X[:, np.newaxis, :] - centroids[np.newaxis, :, :]
        distances = np.sum(diff ** 2, axis=2)
        
        # Assign clusters
        labels = np.argmin(distances, axis=1)
        
        # Update centroids
        new_centroids = np.array([
            X[labels == i].mean(axis=0) if np.any(labels == i) else centroids[i]
            for i in range(k)
        ])
        
        # Check convergence
        if np.allclose(centroids, new_centroids):
            break
        
        centroids = new_centroids
    
    return labels, centroids

# Test k-means
from sklearn.datasets import make_blobs
X_blobs, y_true = make_blobs(n_samples=300, centers=3, random_state=42)
labels, centroids = kmeans_vectorized(X_blobs, k=3)

print("\n=== Vectorized K-Means ===")
print(f"Cluster sizes: {[np.sum(labels == i) for i in range(3)]}")

# 3. Vectorized neural network forward pass
def neural_net_forward(X, weights, biases, activation='relu'):
    """
    Vectorized forward pass through fully connected layers.
    
    X: (batch_size, input_dim)
    weights: list of weight matrices
    biases: list of bias vectors
    """
    activations = {
        'relu': lambda x: np.maximum(0, x),
        'sigmoid': lambda x: 1 / (1 + np.exp(-np.clip(x, -500, 500))),
        'tanh': np.tanh
    }
    
    h = X
    for W, b in zip(weights, biases):
        z = h @ W + b  # Linear transform (broadcasting bias)
        h = activations[activation](z)  # Non-linearity
    
    return h

# Create random network
layer_sizes = [784, 256, 128, 10]
weights = [np.random.randn(s1, s2) * 0.01 
           for s1, s2 in zip(layer_sizes[:-1], layer_sizes[1:])]
biases = [np.zeros(s) for s in layer_sizes[1:]]

# Forward pass
X_batch = np.random.randn(32, 784)  # Batch of 32 images
output = neural_net_forward(X_batch, weights, biases)
print(f"\n=== Neural Network Forward ===")
print(f"Input shape: {X_batch.shape}")
print(f"Output shape: {output.shape}")
```

### What Are np.einsum and np.tensordot?

These are powerful tools for complex tensor operations.

```python
# =============================================================================
# EINSUM AND TENSORDOT
# =============================================================================

# np.einsum - Einstein summation notation
# Format: 'input_subscripts->output_subscripts'

A = np.random.randn(3, 4)
B = np.random.randn(4, 5)
C = np.random.randn(3, 4)

# Matrix multiplication
result1 = np.einsum('ij,jk->ik', A, B)
result2 = A @ B
print(f"Matrix multiply match: {np.allclose(result1, result2)}")

# Element-wise multiply then sum
result1 = np.einsum('ij,ij->', A, C)
result2 = np.sum(A * C)
print(f"Element-wise sum match: {np.allclose(result1, result2)}")

# Trace of matrix
M = np.random.randn(4, 4)
trace1 = np.einsum('ii->', M)
trace2 = np.trace(M)
print(f"Trace match: {np.allclose(trace1, trace2)}")

# Batch matrix multiply
batch_A = np.random.randn(10, 3, 4)
batch_B = np.random.randn(10, 4, 5)
batch_result = np.einsum('bij,bjk->bik', batch_A, batch_B)
print(f"\nBatch matmul shape: {batch_result.shape}")

# Attention mechanism (simplified)
def attention_einsum(Q, K, V):
    """
    Simplified attention using einsum.
    
    Q: (batch, seq_q, d_k)
    K: (batch, seq_k, d_k)
    V: (batch, seq_k, d_v)
    """
    d_k = Q.shape[-1]
    
    # Attention scores: Q @ K^T / sqrt(d_k)
    scores = np.einsum('bqd,bkd->bqk', Q, K) / np.sqrt(d_k)
    
    # Softmax
    scores_exp = np.exp(scores - scores.max(axis=-1, keepdims=True))
    attention_weights = scores_exp / scores_exp.sum(axis=-1, keepdims=True)
    
    # Weighted sum of values
    output = np.einsum('bqk,bkv->bqv', attention_weights, V)
    
    return output, attention_weights

# Test attention
batch, seq_len, d_model = 2, 8, 64
Q = np.random.randn(batch, seq_len, d_model)
K = np.random.randn(batch, seq_len, d_model)
V = np.random.randn(batch, seq_len, d_model)

output, weights = attention_einsum(Q, K, V)
print(f"\n=== Attention with Einsum ===")
print(f"Q, K, V shape: {Q.shape}")
print(f"Output shape: {output.shape}")
print(f"Attention weights shape: {weights.shape}")
```

## Section 5: Memory Layout and Optimization

### How Does NumPy Store Arrays in Memory?

Understanding memory layout is crucial for optimizing performance.

```python
# =============================================================================
# MEMORY LAYOUT
# =============================================================================

# C-order (row-major) vs Fortran-order (column-major)
arr_c = np.array([[1, 2, 3], [4, 5, 6]], order='C')
arr_f = np.array([[1, 2, 3], [4, 5, 6]], order='F')

print("=== Memory Layout ===")
print(f"C-order flags:\n{arr_c.flags}")
print(f"\nFortran-order flags:\n{arr_f.flags}")

# Strides show bytes between elements
arr = np.arange(12).reshape(3, 4)
print(f"\nArray shape: {arr.shape}")
print(f"Array strides: {arr.strides}")  # (32, 8) = 4 elements * 8 bytes, 1 element * 8 bytes

# Contiguity matters for performance
def benchmark_memory_access(arr, n_iterations=100):
    """Benchmark row vs column access."""
    times = {'row': [], 'col': []}
    
    for _ in range(n_iterations):
        # Row-wise access (contiguous for C-order)
        start = time.time()
        total = 0
        for i in range(arr.shape[0]):
            total += arr[i, :].sum()
        times['row'].append(time.time() - start)
        
        # Column-wise access (non-contiguous for C-order)
        start = time.time()
        total = 0
        for j in range(arr.shape[1]):
            total += arr[:, j].sum()
        times['col'].append(time.time() - start)
    
    return np.mean(times['row']), np.mean(times['col'])

large_arr = np.random.randn(1000, 1000)
row_time, col_time = benchmark_memory_access(large_arr)
print(f"\n=== Memory Access Benchmark ===")
print(f"Row access time: {row_time*1000:.3f} ms")
print(f"Column access time: {col_time*1000:.3f} ms")
print(f"Row access is {col_time/row_time:.1f}x faster (C-contiguous)")
```

### How Do You Optimize Memory Usage?

```python
# =============================================================================
# MEMORY OPTIMIZATION
# =============================================================================

# 1. Use appropriate dtypes
print("=== Dtype Memory Optimization ===")

# Before optimization
arr_big = np.random.randn(10000, 1000)  # float64 default
print(f"float64: {arr_big.nbytes / 1e6:.1f} MB")

# After optimization
arr_small = arr_big.astype(np.float32)
print(f"float32: {arr_small.nbytes / 1e6:.1f} MB")

# 2. Views instead of copies
arr = np.arange(1000000)
view = arr.reshape(1000, 1000)  # No copy!
copy = arr.reshape(1000, 1000).copy()  # Copy

print(f"\nView shares memory: {np.shares_memory(arr, view)}")
print(f"Copy shares memory: {np.shares_memory(arr, copy)}")

# 3. In-place operations
arr = np.random.randn(10000)
arr_copy = arr.copy()

# Out-of-place (creates new array)
result1 = arr * 2 + 1

# In-place (modifies existing array)
np.multiply(arr_copy, 2, out=arr_copy)
np.add(arr_copy, 1, out=arr_copy)

# 4. np.memmap for large files
def demonstrate_memmap():
    """Show how to work with arrays larger than RAM."""
    # Create a memory-mapped file
    filename = '/tmp/large_array.dat'
    shape = (1000, 1000)
    dtype = np.float32
    
    # Write mode
    fp = np.memmap(filename, dtype=dtype, mode='w+', shape=shape)
    fp[:] = np.random.randn(*shape).astype(dtype)
    del fp  # Flush to disk
    
    # Read mode (doesn't load into RAM)
    fp = np.memmap(filename, dtype=dtype, mode='r', shape=shape)
    print(f"\n=== Memory-Mapped Array ===")
    print(f"Shape: {fp.shape}")
    print(f"First row mean: {fp[0].mean():.4f}")
    
    # Clean up
    import os
    os.remove(filename)

demonstrate_memmap()

# 5. Chunked processing for large datasets
def process_large_array_chunked(arr, chunk_size=1000):
    """Process array in chunks to limit memory usage."""
    n = len(arr)
    results = []
    
    for start in range(0, n, chunk_size):
        end = min(start + chunk_size, n)
        chunk = arr[start:end]
        
        # Process chunk (example: compute statistics)
        result = {
            'mean': chunk.mean(),
            'std': chunk.std(),
            'min': chunk.min(),
            'max': chunk.max()
        }
        results.append(result)
    
    # Aggregate results
    return {
        'mean': np.mean([r['mean'] for r in results]),
        'std': np.mean([r['std'] for r in results]),
        'min': min(r['min'] for r in results),
        'max': max(r['max'] for r in results)
    }

large_data = np.random.randn(100000)
stats = process_large_array_chunked(large_data, chunk_size=10000)
print(f"\n=== Chunked Processing ===")
print(f"Stats: {stats}")
```

## Section 6: Universal Functions (ufuncs)

### What Are NumPy ufuncs and How Do They Work?

Universal functions operate element-wise on arrays with broadcasting support.

```python
# =============================================================================
# UNIVERSAL FUNCTIONS (ufuncs)
# =============================================================================

# Math ufuncs
arr = np.array([1, 4, 9, 16, 25])
print("=== Math ufuncs ===")
print(f"Original: {arr}")
print(f"sqrt: {np.sqrt(arr)}")
print(f"exp: {np.exp(arr[:3])}")
print(f"log: {np.log(arr)}")
print(f"sin: {np.sin(np.array([0, np.pi/2, np.pi]))}")

# Comparison ufuncs
a = np.array([1, 2, 3, 4, 5])
b = np.array([5, 4, 3, 2, 1])
print(f"\n=== Comparison ufuncs ===")
print(f"a: {a}")
print(f"b: {b}")
print(f"a > b: {a > b}")
print(f"np.maximum(a, b): {np.maximum(a, b)}")
print(f"np.minimum(a, b): {np.minimum(a, b)}")

# Reduction ufuncs
arr = np.arange(12).reshape(3, 4)
print(f"\n=== Reduction ufuncs ===")
print(f"Array:\n{arr}")
print(f"Sum all: {np.sum(arr)}")
print(f"Sum rows (axis=0): {np.sum(arr, axis=0)}")
print(f"Sum cols (axis=1): {np.sum(arr, axis=1)}")
print(f"Cumsum: {np.cumsum(arr.flatten())}")

# Accumulate
print(f"\n=== Accumulate ===")
arr = np.array([1, 2, 3, 4, 5])
print(f"add.accumulate: {np.add.accumulate(arr)}")  # Running sum
print(f"multiply.accumulate: {np.multiply.accumulate(arr)}")  # Running product

# reduceat - reduce at specific indices
arr = np.arange(10)
indices = [0, 3, 6]
result = np.add.reduceat(arr, indices)
print(f"\nreduceat at {indices}: {result}")  # Sum of [0:3], [3:6], [6:]
```

### How Do You Create Custom ufuncs?

```python
# =============================================================================
# CUSTOM UFUNCS
# =============================================================================

# Method 1: np.vectorize (convenience, not performance)
def my_sigmoid(x):
    """Scalar sigmoid function."""
    return 1 / (1 + np.exp(-x))

sigmoid_ufunc = np.vectorize(my_sigmoid)
arr = np.array([-2, -1, 0, 1, 2])
print("=== np.vectorize ===")
print(f"Vectorized sigmoid: {sigmoid_ufunc(arr)}")

# Method 2: np.frompyfunc
def clip_to_range(x, low, high):
    """Clip scalar to range."""
    return max(low, min(high, x))

clip_ufunc = np.frompyfunc(clip_to_range, 3, 1)
arr = np.array([-5, -2, 0, 3, 7])
result = clip_ufunc(arr, -3, 5).astype(float)
print(f"\nCustom clip: {result}")

# Method 3: Proper vectorized implementation (best performance)
def sigmoid_vectorized(x):
    """Properly vectorized sigmoid."""
    return np.where(
        x >= 0,
        1 / (1 + np.exp(-x)),
        np.exp(x) / (1 + np.exp(x))  # Numerically stable for negative x
    )

arr = np.linspace(-5, 5, 11)
print(f"\nProper vectorized sigmoid: {sigmoid_vectorized(arr).round(3)}")

# Performance comparison
large_arr = np.random.randn(100000)

print("\n=== Performance Comparison ===")
for name, func in [('np.vectorize', sigmoid_ufunc),
                   ('Vectorized impl', sigmoid_vectorized)]:
    start = time.time()
    _ = func(large_arr)
    elapsed = time.time() - start
    print(f"{name}: {elapsed*1000:.2f} ms")
```

## Section 7: Practical NumPy for Machine Learning

### Complete Example: Building a Neural Network with NumPy

```python
# =============================================================================
# NEURAL NETWORK FROM SCRATCH WITH NUMPY
# =============================================================================

class NeuralNetworkNumPy:
    """
    Simple feedforward neural network using only NumPy.
    
    Demonstrates:
    - Weight initialization
    - Forward propagation
    - Backward propagation
    - Gradient descent
    """
    
    def __init__(self, layer_sizes, activation='relu', learning_rate=0.01):
        """
        Initialize network.
        
        Args:
            layer_sizes: List of layer dimensions [input, hidden..., output]
            activation: 'relu' or 'tanh'
            learning_rate: Learning rate for gradient descent
        """
        self.layer_sizes = layer_sizes
        self.learning_rate = learning_rate
        self.activation = activation
        
        # Xavier initialization for weights
        self.weights = []
        self.biases = []
        
        for i in range(len(layer_sizes) - 1):
            fan_in = layer_sizes[i]
            fan_out = layer_sizes[i + 1]
            
            # Xavier initialization
            std = np.sqrt(2.0 / (fan_in + fan_out))
            W = np.random.randn(fan_in, fan_out) * std
            b = np.zeros(fan_out)
            
            self.weights.append(W)
            self.biases.append(b)
    
    def _activate(self, z):
        """Apply activation function."""
        if self.activation == 'relu':
            return np.maximum(0, z)
        elif self.activation == 'tanh':
            return np.tanh(z)
    
    def _activate_derivative(self, z):
        """Derivative of activation function."""
        if self.activation == 'relu':
            return (z > 0).astype(float)
        elif self.activation == 'tanh':
            return 1 - np.tanh(z) ** 2
    
    def forward(self, X):
        """
        Forward propagation.
        
        Args:
            X: Input data (batch_size, input_dim)
        
        Returns:
            output: Network output
            cache: Intermediate values for backprop
        """
        cache = {'activations': [X], 'z_values': []}
        
        h = X
        for i, (W, b) in enumerate(zip(self.weights, self.biases)):
            z = h @ W + b
            cache['z_values'].append(z)
            
            # Apply activation (except for last layer)
            if i < len(self.weights) - 1:
                h = self._activate(z)
            else:
                h = z  # Linear output for last layer
            
            cache['activations'].append(h)
        
        return h, cache
    
    def backward(self, y_true, cache):
        """
        Backward propagation.
        
        Args:
            y_true: True labels (batch_size, output_dim)
            cache: Cache from forward pass
        
        Returns:
            gradients: Dictionary of weight and bias gradients
        """
        batch_size = y_true.shape[0]
        n_layers = len(self.weights)
        
        gradients = {'dW': [], 'db': []}
        
        # Output layer gradient (MSE loss derivative)
        y_pred = cache['activations'][-1]
        dz = (y_pred - y_true) / batch_size  # MSE gradient
        
        # Backpropagate through layers
        for i in range(n_layers - 1, -1, -1):
            a_prev = cache['activations'][i]
            
            # Gradients for this layer
            dW = a_prev.T @ dz
            db = np.sum(dz, axis=0)
            
            gradients['dW'].insert(0, dW)
            gradients['db'].insert(0, db)
            
            # Backpropagate error (except for first layer)
            if i > 0:
                dz = (dz @ self.weights[i].T) * self._activate_derivative(cache['z_values'][i-1])
        
        return gradients
    
    def update_weights(self, gradients):
        """Update weights using gradient descent."""
        for i in range(len(self.weights)):
            self.weights[i] -= self.learning_rate * gradients['dW'][i]
            self.biases[i] -= self.learning_rate * gradients['db'][i]
    
    def train(self, X, y, epochs=100, verbose=True):
        """Train the network."""
        losses = []
        
        for epoch in range(epochs):
            # Forward pass
            y_pred, cache = self.forward(X)
            
            # Compute loss (MSE)
            loss = np.mean((y_pred - y) ** 2)
            losses.append(loss)
            
            # Backward pass
            gradients = self.backward(y, cache)
            
            # Update weights
            self.update_weights(gradients)
            
            if verbose and (epoch + 1) % 100 == 0:
                print(f"Epoch {epoch + 1}/{epochs}, Loss: {loss:.6f}")
        
        return losses
    
    def predict(self, X):
        """Make predictions."""
        output, _ = self.forward(X)
        return output

# Test the neural network
print("=== NumPy Neural Network ===\n")

# Generate synthetic classification data
np.random.seed(42)
n_samples = 500
X = np.random.randn(n_samples, 2)
y = (X[:, 0] ** 2 + X[:, 1] ** 2 > 1).astype(float).reshape(-1, 1)

# Create and train network
nn = NeuralNetworkNumPy([2, 16, 8, 1], activation='relu', learning_rate=0.1)
losses = nn.train(X, y, epochs=500, verbose=True)

# Evaluate
predictions = nn.predict(X)
accuracy = np.mean((predictions > 0.5) == y)
print(f"\nFinal accuracy: {accuracy:.2%}")
```

## Comparison: NumPy vs Other Libraries

| Feature | NumPy | PyTorch | TensorFlow | JAX |
|---------|-------|---------|------------|-----|
| CPU Operations | ✅ Native | ✅ | ✅ | ✅ |
| GPU Support | ❌ | ✅ | ✅ | ✅ |
| Auto-diff | ❌ | ✅ | ✅ | ✅ |
| Eager Execution | ✅ | ✅ | ✅ | ✅ |
| JIT Compilation | ❌ | ✅ | ✅ | ✅ |
| Ecosystem | Mature | Growing | Mature | Growing |

## Frequently Asked Questions

### When should I use NumPy vs PyTorch/TensorFlow?

Use NumPy when:
- Working on CPU-only tasks
- No automatic differentiation needed
- Maximum compatibility is required
- Doing data preprocessing

Use PyTorch/TensorFlow when:
- Training neural networks
- Need GPU acceleration
- Require automatic differentiation
- Building production ML systems

### How do I speed up NumPy code?

1. **Vectorize**: Replace loops with array operations
2. **Use appropriate dtypes**: float32 instead of float64 when possible
3. **Preallocate arrays**: Avoid growing arrays in loops
4. **Use views**: Avoid unnecessary copies
5. **Consider Numba**: JIT compilation for custom functions

### Why does my NumPy code run slow?

Common issues:
- Using Python loops instead of vectorized operations
- Creating many temporary arrays
- Non-contiguous memory access
- Using inappropriate dtypes

### How do I debug NumPy shape errors?

```python
# Print shapes at each step
print(f"X shape: {X.shape}")
print(f"W shape: {W.shape}")

# Use assertions
assert X.shape[1] == W.shape[0], f"Dimension mismatch: {X.shape} vs {W.shape}"
```

## Key Takeaways

1. **NumPy arrays are fundamentally different from Python lists** - contiguous memory, homogeneous types, vectorized operations

2. **Broadcasting eliminates loops** - understand the rules to write efficient code

3. **Views vs copies matter** - views share memory, copies don't

4. **Memory layout affects performance** - row-major access is faster for C-order arrays

5. **Use appropriate dtypes** - float32 is often sufficient and uses half the memory of float64

6. **Vectorization is key** - always look for ways to replace loops with array operations

## Next Steps in Your Learning Journey

Now that you've mastered NumPy fundamentals:

1. **Learn Pandas** for data manipulation and analysis
2. **Explore SciPy** for advanced scientific computing
3. **Study PyTorch** to apply these concepts with GPU acceleration
4. **Practice optimization** by profiling and improving your code

---

*Last updated: January 2025. This article covers NumPy best practices for machine learning applications.*
