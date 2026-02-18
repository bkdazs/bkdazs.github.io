---
title: "Tensors in Deep Learning: Complete Guide from NumPy to PyTorch & TensorFlow"
description: "Master tensors for deep learning with practical examples in NumPy, PyTorch, and TensorFlow. Learn tensor operations, broadcasting, GPU acceleration, and real-world applications in neural networks."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 5
toc: true
categories: ["AI/ML", "Deep Learning"]
tags: ["tensors", "pytorch", "tensorflow", "numpy", "deep learning", "neural networks", "GPU computing"]
series: ["Mathematics for ML"]
---

# Tensors in Deep Learning: Complete Guide from NumPy to PyTorch & TensorFlow

**"Tensors are the language of deep learning."** — François Chollet, Creator of Keras

If you've worked with deep learning, you've encountered tensors everywhere. They're the fundamental data structure that powers everything from simple neural networks to transformer models like GPT-4. Yet many practitioners struggle to truly understand what tensors are and how to work with them effectively.

In this comprehensive guide, you'll learn everything about tensors—from basic concepts to advanced operations across NumPy, PyTorch, and TensorFlow. Whether you're preprocessing data, building models, or debugging tensor shape mismatches, this guide will make you confident with tensors.

---

## What is a Tensor? Understanding the Basics

### The Mathematical Definition

A **tensor** is a generalization of scalars, vectors, and matrices to higher dimensions. Think of tensors as multi-dimensional arrays that can represent data of any dimensionality.

| Tensor Rank | Name | Example | Shape |
|-------------|------|---------|-------|
| 0 | Scalar | Temperature: 25 | () |
| 1 | Vector | [1, 2, 3] | (3,) |
| 2 | Matrix | 2D array | (3, 4) |
| 3 | 3D Tensor | Image batch | (32, 224, 224) |
| 4 | 4D Tensor | Color image batch | (32, 224, 224, 3) |
| n | n-D Tensor | General data | (d₁, d₂, ..., dₙ) |

### Why Are Tensors Essential for Deep Learning?

Deep learning models process data through layers of mathematical transformations. Tensors provide:

1. **Efficient Storage**: Contiguous memory layout for fast access
2. **Parallel Processing**: GPU-optimized operations
3. **Automatic Differentiation**: Tracking computations for backpropagation
4. **Broadcasting**: Flexible element-wise operations
5. **Hardware Acceleration**: Optimized for TPUs, GPUs, and specialized hardware

### Real-World Tensor Examples in Deep Learning

```
Text Data:      [batch_size, sequence_length, embedding_dim]
                [32, 512, 768] — 32 sentences, 512 tokens, 768-dim embeddings

Images:         [batch_size, channels, height, width]  (PyTorch)
                [32, 3, 224, 224] — 32 RGB images, 224×224 pixels

Video:          [batch_size, frames, channels, height, width]
                [8, 30, 3, 224, 224] — 8 videos, 30 frames each

Audio:          [batch_size, channels, samples]
                [16, 1, 16000] — 16 mono audio clips, 1 second at 16kHz
```

---

## How Do You Create Tensors in Python?

### Creating Tensors with NumPy

NumPy provides the foundation for tensor operations in Python:

```python
import numpy as np

# Scalar (0-D tensor)
scalar = np.array(42)
print(f"Scalar: {scalar}, Shape: {scalar.shape}, ndim: {scalar.ndim}")
# Output: Scalar: 42, Shape: (), ndim: 0

# Vector (1-D tensor)
vector = np.array([1, 2, 3, 4, 5])
print(f"Vector shape: {vector.shape}, ndim: {vector.ndim}")
# Output: Vector shape: (5,), ndim: 1

# Matrix (2-D tensor)
matrix = np.array([[1, 2, 3],
                   [4, 5, 6]])
print(f"Matrix shape: {matrix.shape}, ndim: {matrix.ndim}")
# Output: Matrix shape: (2, 3), ndim: 2

# 3-D Tensor
tensor_3d = np.array([[[1, 2], [3, 4]],
                      [[5, 6], [7, 8]]])
print(f"3D Tensor shape: {tensor_3d.shape}, ndim: {tensor_3d.ndim}")
# Output: 3D Tensor shape: (2, 2, 2), ndim: 3

# Creating tensors with specific values
zeros = np.zeros((3, 4, 5))        # All zeros
ones = np.ones((2, 3))              # All ones
random = np.random.randn(4, 4)      # Random normal
identity = np.eye(5)                # Identity matrix
arange = np.arange(0, 10, 2)        # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)     # [0, 0.25, 0.5, 0.75, 1.0]
```

### Creating Tensors with PyTorch

PyTorch tensors support automatic differentiation and GPU acceleration:

```python
import torch

# Basic tensor creation
scalar = torch.tensor(42)
vector = torch.tensor([1, 2, 3, 4, 5])
matrix = torch.tensor([[1, 2, 3], [4, 5, 6]])

# Tensors with specific values
zeros = torch.zeros(3, 4, 5)
ones = torch.ones(2, 3)
random_normal = torch.randn(4, 4)       # Standard normal
random_uniform = torch.rand(4, 4)       # Uniform [0, 1)
identity = torch.eye(5)
arange = torch.arange(0, 10, 2)
linspace = torch.linspace(0, 1, 5)

# Tensors with gradient tracking (for training)
trainable = torch.randn(3, 3, requires_grad=True)
print(f"Requires grad: {trainable.requires_grad}")

# Specify data type and device
float_tensor = torch.tensor([1, 2, 3], dtype=torch.float32)
int_tensor = torch.tensor([1, 2, 3], dtype=torch.int64)

# GPU tensor (if CUDA available)
if torch.cuda.is_available():
    gpu_tensor = torch.randn(3, 3, device='cuda')
    print(f"Device: {gpu_tensor.device}")

# Create tensor like another tensor
template = torch.randn(2, 3)
zeros_like = torch.zeros_like(template)
ones_like = torch.ones_like(template)
randn_like = torch.randn_like(template)
```

### Creating Tensors with TensorFlow

TensorFlow provides similar tensor creation methods:

```python
import tensorflow as tf

# Basic tensor creation
scalar = tf.constant(42)
vector = tf.constant([1, 2, 3, 4, 5])
matrix = tf.constant([[1, 2, 3], [4, 5, 6]])

# Tensors with specific values
zeros = tf.zeros([3, 4, 5])
ones = tf.ones([2, 3])
random_normal = tf.random.normal([4, 4])
random_uniform = tf.random.uniform([4, 4])
identity = tf.eye(5)
arange = tf.range(0, 10, 2)
linspace = tf.linspace(0.0, 1.0, 5)

# Variables (mutable tensors for training)
variable = tf.Variable(tf.random.normal([3, 3]))
print(f"Variable shape: {variable.shape}")

# Specify data type
float_tensor = tf.constant([1, 2, 3], dtype=tf.float32)
int_tensor = tf.constant([1, 2, 3], dtype=tf.int64)

# GPU tensor (automatic if GPU available)
with tf.device('/GPU:0'):
    gpu_tensor = tf.random.normal([3, 3])

# Create tensor like another
template = tf.random.normal([2, 3])
zeros_like = tf.zeros_like(template)
ones_like = tf.ones_like(template)
```

### Framework Comparison: Creating Tensors

| Operation | NumPy | PyTorch | TensorFlow |
|-----------|-------|---------|------------|
| From list | `np.array([1,2,3])` | `torch.tensor([1,2,3])` | `tf.constant([1,2,3])` |
| Zeros | `np.zeros((2,3))` | `torch.zeros(2,3)` | `tf.zeros([2,3])` |
| Ones | `np.ones((2,3))` | `torch.ones(2,3)` | `tf.ones([2,3])` |
| Random normal | `np.random.randn(2,3)` | `torch.randn(2,3)` | `tf.random.normal([2,3])` |
| Random uniform | `np.random.rand(2,3)` | `torch.rand(2,3)` | `tf.random.uniform([2,3])` |
| Range | `np.arange(10)` | `torch.arange(10)` | `tf.range(10)` |
| Identity | `np.eye(3)` | `torch.eye(3)` | `tf.eye(3)` |

---

## What Are the Essential Tensor Operations?

### Indexing and Slicing

```python
import torch

# Create a 3D tensor
tensor = torch.arange(24).reshape(2, 3, 4)
print(f"Shape: {tensor.shape}")

# Basic indexing
first_matrix = tensor[0]          # Shape: (3, 4)
first_row = tensor[0, 0]          # Shape: (4,)
single_element = tensor[0, 0, 0]  # Scalar

# Slicing
first_two_rows = tensor[:, :2, :]     # Shape: (2, 2, 4)
every_other = tensor[:, ::2, :]       # Shape: (2, 2, 4)
reversed_tensor = tensor[:, :, ::-1]  # Reverse last dimension

# Advanced indexing
indices = torch.tensor([0, 2])
selected = tensor[:, indices, :]      # Shape: (2, 2, 4)

# Boolean indexing
mask = tensor > 10
filtered = tensor[mask]               # 1D tensor of values > 10

# Using where for conditional selection
result = torch.where(tensor > 10, tensor, torch.zeros_like(tensor))
```

### Reshaping Operations

Reshaping is crucial for preparing data for neural networks:

```python
import torch

tensor = torch.arange(24)
print(f"Original: {tensor.shape}")  # (24,)

# Reshape - must preserve total elements
reshaped = tensor.reshape(2, 3, 4)
print(f"Reshaped: {reshaped.shape}")  # (2, 3, 4)

# View - same as reshape but requires contiguous memory
viewed = tensor.view(4, 6)
print(f"Viewed: {viewed.shape}")  # (4, 6)

# Flatten - convert to 1D
flattened = reshaped.flatten()
print(f"Flattened: {flattened.shape}")  # (24,)

# Squeeze - remove dimensions of size 1
squeezable = torch.randn(1, 3, 1, 4)
squeezed = squeezable.squeeze()
print(f"Squeezed: {squeezed.shape}")  # (3, 4)

# Unsqueeze - add dimension at specified position
expanded = tensor.unsqueeze(0)  # Add batch dimension
print(f"Expanded: {expanded.shape}")  # (1, 24)

# Expand dims at multiple positions
multi_expanded = tensor.unsqueeze(0).unsqueeze(-1)
print(f"Multi-expanded: {multi_expanded.shape}")  # (1, 24, 1)

# Permute - rearrange dimensions
image = torch.randn(3, 224, 224)  # CHW format
permuted = image.permute(1, 2, 0)  # HWC format
print(f"Permuted: {permuted.shape}")  # (224, 224, 3)

# Transpose - swap two dimensions
matrix = torch.randn(3, 4)
transposed = matrix.T  # or matrix.transpose(0, 1)
print(f"Transposed: {transposed.shape}")  # (4, 3)
```

### Mathematical Operations

```python
import torch

a = torch.tensor([[1, 2], [3, 4]], dtype=torch.float32)
b = torch.tensor([[5, 6], [7, 8]], dtype=torch.float32)

# Element-wise operations
addition = a + b           # or torch.add(a, b)
subtraction = a - b        # or torch.sub(a, b)
multiplication = a * b     # or torch.mul(a, b)
division = a / b           # or torch.div(a, b)
power = a ** 2             # or torch.pow(a, 2)

# Matrix multiplication
matmul = a @ b             # or torch.matmul(a, b) or torch.mm(a, b)
print(f"Matrix multiplication:\n{matmul}")

# Batch matrix multiplication
batch_a = torch.randn(10, 3, 4)
batch_b = torch.randn(10, 4, 5)
batch_matmul = torch.bmm(batch_a, batch_b)
print(f"Batch matmul shape: {batch_matmul.shape}")  # (10, 3, 5)

# Einstein summation (powerful notation)
# Matrix multiplication using einsum
einsum_matmul = torch.einsum('ij,jk->ik', a, b)

# Batch matrix multiplication using einsum
einsum_batch = torch.einsum('bij,bjk->bik', batch_a, batch_b)

# Reduction operations
tensor = torch.randn(3, 4, 5)
sum_all = tensor.sum()                    # Sum of all elements
sum_axis = tensor.sum(dim=1)              # Sum along axis 1
mean_all = tensor.mean()                  # Mean of all elements
mean_axis = tensor.mean(dim=-1)           # Mean along last axis
max_val, max_idx = tensor.max(dim=0)      # Max along axis 0
min_val, min_idx = tensor.min(dim=1)      # Min along axis 1
std = tensor.std()                        # Standard deviation
var = tensor.var()                        # Variance

# Keep dimensions for broadcasting
sum_keepdim = tensor.sum(dim=1, keepdim=True)
print(f"With keepdim: {sum_keepdim.shape}")  # (3, 1, 5)

# Common functions
sqrt = torch.sqrt(torch.abs(a))
exp = torch.exp(a)
log = torch.log(torch.abs(a) + 1e-8)
sin = torch.sin(a)
cos = torch.cos(a)
tanh = torch.tanh(a)
sigmoid = torch.sigmoid(a)
relu = torch.relu(a)
softmax = torch.softmax(a, dim=-1)
```

---

## How Does Broadcasting Work in Deep Learning?

Broadcasting is a powerful mechanism that allows operations on tensors with different shapes. Understanding broadcasting is essential for writing efficient deep learning code.

### Broadcasting Rules

1. **Compare shapes from right to left**
2. **Dimensions are compatible if they are equal OR one of them is 1**
3. **Missing dimensions are treated as 1**

```python
import torch

# Example 1: Scalar and matrix
scalar = torch.tensor(10)
matrix = torch.randn(3, 4)
result = matrix + scalar  # Broadcasts scalar to all elements
print(f"Scalar + Matrix: {result.shape}")  # (3, 4)

# Example 2: Vector and matrix
vector = torch.randn(4)      # Shape: (4,)
matrix = torch.randn(3, 4)   # Shape: (3, 4)
result = matrix + vector     # Vector broadcasts along first dim
print(f"Vector + Matrix: {result.shape}")  # (3, 4)

# Example 3: Column vector and row vector
col = torch.randn(3, 1)      # Shape: (3, 1)
row = torch.randn(1, 4)      # Shape: (1, 4)
result = col + row           # Creates 3x4 matrix
print(f"Column + Row: {result.shape}")  # (3, 4)

# Example 4: Batch normalization style
features = torch.randn(32, 64, 28, 28)  # Batch of feature maps
mean = torch.randn(1, 64, 1, 1)          # Per-channel mean
std = torch.randn(1, 64, 1, 1)           # Per-channel std
normalized = (features - mean) / std
print(f"Normalized: {normalized.shape}")  # (32, 64, 28, 28)

# Example 5: Attention scores
query = torch.randn(32, 8, 64, 64)   # [batch, heads, seq, dim]
key = torch.randn(32, 8, 64, 64)
attention = torch.matmul(query, key.transpose(-2, -1))
print(f"Attention: {attention.shape}")  # (32, 8, 64, 64)
```

### Broadcasting Gotchas and Best Practices

```python
import torch

# GOTCHA 1: Unintended broadcasting
a = torch.randn(3, 4)
b = torch.randn(4, 3)
# result = a + b  # Error! (3,4) and (4,3) not compatible

# GOTCHA 2: Memory implications
small = torch.randn(1)
large = torch.randn(1000, 1000)
result = small + large  # Creates 1M element tensor

# BEST PRACTICE: Explicit expansion
vector = torch.randn(4)
matrix = torch.randn(3, 4)

# Implicit broadcasting
result1 = matrix + vector

# Explicit expansion (clearer, but same result)
expanded_vector = vector.unsqueeze(0).expand(3, 4)
result2 = matrix + expanded_vector

# BEST PRACTICE: Use view for controlled broadcasting
weights = torch.randn(64)  # Per-channel weights
features = torch.randn(32, 64, 28, 28)

# Reshape weights for broadcasting: [1, 64, 1, 1]
weights_reshaped = weights.view(1, -1, 1, 1)
scaled = features * weights_reshaped
```

---

## How Do You Handle GPU Acceleration with Tensors?

### PyTorch GPU Operations

```python
import torch

# Check GPU availability
print(f"CUDA available: {torch.cuda.is_available()}")
if torch.cuda.is_available():
    print(f"GPU count: {torch.cuda.device_count()}")
    print(f"GPU name: {torch.cuda.get_device_name(0)}")
    print(f"Current device: {torch.cuda.current_device()}")

# Device-agnostic code pattern
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"Using device: {device}")

# Create tensor on GPU
gpu_tensor = torch.randn(1000, 1000, device=device)
print(f"Tensor device: {gpu_tensor.device}")

# Move tensor to GPU
cpu_tensor = torch.randn(1000, 1000)
gpu_tensor = cpu_tensor.to(device)
# or: gpu_tensor = cpu_tensor.cuda()

# Move tensor back to CPU
cpu_tensor = gpu_tensor.cpu()

# Operations on GPU (automatic when tensors are on GPU)
a = torch.randn(1000, 1000, device=device)
b = torch.randn(1000, 1000, device=device)
c = torch.matmul(a, b)  # Computed on GPU

# Mixed device error (common mistake)
cpu_tensor = torch.randn(1000, 1000)
gpu_tensor = torch.randn(1000, 1000, device='cuda')
# result = cpu_tensor + gpu_tensor  # Error! Tensors on different devices

# Memory management
torch.cuda.empty_cache()  # Clear unused cached memory
print(f"Memory allocated: {torch.cuda.memory_allocated() / 1e9:.2f} GB")
print(f"Memory cached: {torch.cuda.memory_reserved() / 1e9:.2f} GB")
```

### TensorFlow GPU Operations

```python
import tensorflow as tf

# Check GPU availability
print(f"GPUs available: {tf.config.list_physical_devices('GPU')}")

# TensorFlow automatically uses GPU when available
# But you can specify device explicitly
with tf.device('/GPU:0'):
    a = tf.random.normal([1000, 1000])
    b = tf.random.normal([1000, 1000])
    c = tf.matmul(a, b)

with tf.device('/CPU:0'):
    cpu_tensor = tf.random.normal([1000, 1000])

# Memory growth (prevents TF from allocating all GPU memory)
gpus = tf.config.list_physical_devices('GPU')
if gpus:
    for gpu in gpus:
        tf.config.experimental.set_memory_growth(gpu, True)

# Mixed precision training (faster on modern GPUs)
tf.keras.mixed_precision.set_global_policy('mixed_float16')
```

### GPU Performance Comparison

```python
import torch
import time

def benchmark_matmul(size, device, iterations=100):
    """Benchmark matrix multiplication on specified device."""
    a = torch.randn(size, size, device=device)
    b = torch.randn(size, size, device=device)
    
    # Warmup
    for _ in range(10):
        c = torch.matmul(a, b)
    
    if device.type == 'cuda':
        torch.cuda.synchronize()
    
    start = time.time()
    for _ in range(iterations):
        c = torch.matmul(a, b)
    
    if device.type == 'cuda':
        torch.cuda.synchronize()
    
    elapsed = time.time() - start
    return elapsed / iterations

# Run benchmark
sizes = [100, 500, 1000, 2000, 5000]
cpu_device = torch.device('cpu')

print("Matrix Multiplication Benchmark (ms)")
print("-" * 50)
print(f"{'Size':<10} {'CPU':<15} {'GPU':<15} {'Speedup':<10}")
print("-" * 50)

for size in sizes:
    cpu_time = benchmark_matmul(size, cpu_device) * 1000
    
    if torch.cuda.is_available():
        gpu_device = torch.device('cuda')
        gpu_time = benchmark_matmul(size, gpu_device) * 1000
        speedup = cpu_time / gpu_time
        print(f"{size:<10} {cpu_time:<15.2f} {gpu_time:<15.2f} {speedup:<10.1f}x")
    else:
        print(f"{size:<10} {cpu_time:<15.2f} {'N/A':<15} {'N/A':<10}")
```

**Typical Results:**

| Size | CPU (ms) | GPU (ms) | Speedup |
|------|----------|----------|---------|
| 100 | 0.02 | 0.01 | 2x |
| 500 | 1.5 | 0.05 | 30x |
| 1000 | 12 | 0.15 | 80x |
| 2000 | 95 | 0.6 | 158x |
| 5000 | 1500 | 8 | 187x |

---

## How Are Tensors Used in Neural Networks?

### Building a Neural Network Layer by Layer

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

# Input tensor: batch of 32 images, 3 channels, 224x224 pixels
x = torch.randn(32, 3, 224, 224)
print(f"Input shape: {x.shape}")

# Convolutional layer
conv = nn.Conv2d(in_channels=3, out_channels=64, kernel_size=3, padding=1)
x = conv(x)
print(f"After Conv2d: {x.shape}")  # (32, 64, 224, 224)

# Batch normalization
bn = nn.BatchNorm2d(64)
x = bn(x)
print(f"After BatchNorm: {x.shape}")  # (32, 64, 224, 224)

# ReLU activation
x = F.relu(x)
print(f"After ReLU: {x.shape}")  # (32, 64, 224, 224)

# Max pooling
pool = nn.MaxPool2d(kernel_size=2, stride=2)
x = pool(x)
print(f"After MaxPool: {x.shape}")  # (32, 64, 112, 112)

# Flatten for fully connected layer
x = x.view(x.size(0), -1)
print(f"After Flatten: {x.shape}")  # (32, 802816)

# Fully connected layer
fc = nn.Linear(802816, 1000)
x = fc(x)
print(f"After Linear: {x.shape}")  # (32, 1000)

# Softmax for classification
x = F.softmax(x, dim=-1)
print(f"After Softmax: {x.shape}")  # (32, 1000)
```

### Tensor Operations in Attention Mechanism

```python
import torch
import torch.nn.functional as F
import math

def scaled_dot_product_attention(query, key, value, mask=None):
    """
    Compute scaled dot-product attention.
    
    Args:
        query: (batch, heads, seq_q, d_k)
        key: (batch, heads, seq_k, d_k)
        value: (batch, heads, seq_k, d_v)
        mask: Optional mask for padding/causal attention
    
    Returns:
        output: (batch, heads, seq_q, d_v)
        attention_weights: (batch, heads, seq_q, seq_k)
    """
    d_k = query.size(-1)
    
    # Compute attention scores
    # (batch, heads, seq_q, d_k) @ (batch, heads, d_k, seq_k)
    # -> (batch, heads, seq_q, seq_k)
    scores = torch.matmul(query, key.transpose(-2, -1)) / math.sqrt(d_k)
    
    # Apply mask if provided
    if mask is not None:
        scores = scores.masked_fill(mask == 0, float('-inf'))
    
    # Softmax to get attention weights
    attention_weights = F.softmax(scores, dim=-1)
    
    # Apply attention to values
    # (batch, heads, seq_q, seq_k) @ (batch, heads, seq_k, d_v)
    # -> (batch, heads, seq_q, d_v)
    output = torch.matmul(attention_weights, value)
    
    return output, attention_weights

# Example usage
batch_size = 2
num_heads = 8
seq_len = 10
d_model = 64

query = torch.randn(batch_size, num_heads, seq_len, d_model)
key = torch.randn(batch_size, num_heads, seq_len, d_model)
value = torch.randn(batch_size, num_heads, seq_len, d_model)

output, weights = scaled_dot_product_attention(query, key, value)
print(f"Attention output shape: {output.shape}")      # (2, 8, 10, 64)
print(f"Attention weights shape: {weights.shape}")    # (2, 8, 10, 10)
```

### Tensor Flow Through a Transformer Block

```python
import torch
import torch.nn as nn

class TransformerBlock(nn.Module):
    """
    Single transformer block demonstrating tensor shapes through each operation.
    """
    def __init__(self, d_model=512, num_heads=8, d_ff=2048, dropout=0.1):
        super().__init__()
        self.attention = nn.MultiheadAttention(d_model, num_heads, dropout=dropout)
        self.norm1 = nn.LayerNorm(d_model)
        self.norm2 = nn.LayerNorm(d_model)
        self.ffn = nn.Sequential(
            nn.Linear(d_model, d_ff),
            nn.ReLU(),
            nn.Dropout(dropout),
            nn.Linear(d_ff, d_model),
            nn.Dropout(dropout)
        )
        
    def forward(self, x, mask=None):
        """
        Args:
            x: Input tensor of shape (seq_len, batch, d_model)
            mask: Optional attention mask
        
        Returns:
            Output tensor of shape (seq_len, batch, d_model)
        """
        print(f"Input: {x.shape}")
        
        # Self-attention with residual connection
        attn_output, _ = self.attention(x, x, x, attn_mask=mask)
        print(f"After attention: {attn_output.shape}")
        
        x = self.norm1(x + attn_output)
        print(f"After norm1: {x.shape}")
        
        # Feed-forward network with residual connection
        ffn_output = self.ffn(x)
        print(f"After FFN: {ffn_output.shape}")
        
        x = self.norm2(x + ffn_output)
        print(f"After norm2: {x.shape}")
        
        return x

# Example usage
seq_len = 20
batch_size = 4
d_model = 512

x = torch.randn(seq_len, batch_size, d_model)
block = TransformerBlock()
output = block(x)
```

---

## What Are Common Tensor Debugging Techniques?

### Debugging Shape Mismatches

```python
import torch

def debug_tensor(tensor, name="tensor"):
    """Print comprehensive tensor information for debugging."""
    print(f"\n=== {name} ===")
    print(f"Shape: {tensor.shape}")
    print(f"Dtype: {tensor.dtype}")
    print(f"Device: {tensor.device}")
    print(f"Requires grad: {tensor.requires_grad}")
    print(f"Is contiguous: {tensor.is_contiguous()}")
    
    if tensor.numel() > 0:
        print(f"Min: {tensor.min().item():.4f}")
        print(f"Max: {tensor.max().item():.4f}")
        print(f"Mean: {tensor.float().mean().item():.4f}")
        print(f"Std: {tensor.float().std().item():.4f}")
        
        # Check for NaN or Inf
        nan_count = torch.isnan(tensor).sum().item()
        inf_count = torch.isinf(tensor).sum().item()
        if nan_count > 0:
            print(f"⚠️  NaN count: {nan_count}")
        if inf_count > 0:
            print(f"⚠️  Inf count: {inf_count}")

# Usage
x = torch.randn(32, 64, 224, 224)
debug_tensor(x, "Feature Map")

# Common shape mismatch scenarios and fixes
def fix_batch_dimension(x):
    """Ensure tensor has batch dimension."""
    if x.dim() == 3:  # Missing batch dimension
        x = x.unsqueeze(0)
    return x

def ensure_channel_last(x):
    """Convert from NCHW to NHWC format."""
    if x.dim() == 4:
        x = x.permute(0, 2, 3, 1)
    return x

def ensure_channel_first(x):
    """Convert from NHWC to NCHW format."""
    if x.dim() == 4:
        x = x.permute(0, 3, 1, 2)
    return x
```

### Using Hooks for Debugging

```python
import torch
import torch.nn as nn

class ShapeDebugger:
    """Debug tensor shapes through a neural network."""
    
    def __init__(self, model):
        self.shapes = {}
        self.hooks = []
        self._register_hooks(model)
    
    def _register_hooks(self, model):
        for name, module in model.named_modules():
            hook = module.register_forward_hook(
                lambda m, inp, out, name=name: self._hook_fn(name, inp, out)
            )
            self.hooks.append(hook)
    
    def _hook_fn(self, name, inp, out):
        inp_shape = inp[0].shape if isinstance(inp, tuple) else inp.shape
        out_shape = out.shape if hasattr(out, 'shape') else 'N/A'
        self.shapes[name] = {'input': inp_shape, 'output': out_shape}
    
    def print_shapes(self):
        print("\nTensor shapes through network:")
        print("-" * 60)
        for name, shapes in self.shapes.items():
            if name:  # Skip empty names
                print(f"{name}: {shapes['input']} -> {shapes['output']}")
    
    def remove_hooks(self):
        for hook in self.hooks:
            hook.remove()

# Example usage
model = nn.Sequential(
    nn.Conv2d(3, 64, 3, padding=1),
    nn.BatchNorm2d(64),
    nn.ReLU(),
    nn.MaxPool2d(2),
    nn.Conv2d(64, 128, 3, padding=1),
    nn.BatchNorm2d(128),
    nn.ReLU(),
    nn.AdaptiveAvgPool2d(1),
    nn.Flatten(),
    nn.Linear(128, 10)
)

debugger = ShapeDebugger(model)
x = torch.randn(4, 3, 32, 32)
output = model(x)
debugger.print_shapes()
debugger.remove_hooks()
```

---

## How Do You Convert Between Tensor Frameworks?

### NumPy ↔ PyTorch Conversion

```python
import numpy as np
import torch

# NumPy to PyTorch
numpy_array = np.random.randn(3, 4).astype(np.float32)

# Method 1: torch.from_numpy (shares memory!)
torch_tensor = torch.from_numpy(numpy_array)
numpy_array[0, 0] = 100  # This also changes torch_tensor!
print(f"Shared memory: {torch_tensor[0, 0]}")  # 100

# Method 2: torch.tensor (copies data)
torch_tensor = torch.tensor(numpy_array)  # Independent copy

# PyTorch to NumPy
torch_tensor = torch.randn(3, 4)

# For CPU tensors
numpy_array = torch_tensor.numpy()  # Shares memory

# For GPU tensors or tensors with gradients
numpy_array = torch_tensor.detach().cpu().numpy()  # Safe copy
```

### NumPy ↔ TensorFlow Conversion

```python
import numpy as np
import tensorflow as tf

# NumPy to TensorFlow
numpy_array = np.random.randn(3, 4).astype(np.float32)
tf_tensor = tf.convert_to_tensor(numpy_array)

# TensorFlow to NumPy
tf_tensor = tf.random.normal([3, 4])
numpy_array = tf_tensor.numpy()
```

### PyTorch ↔ TensorFlow Conversion

```python
import torch
import tensorflow as tf
import numpy as np

# PyTorch to TensorFlow (via NumPy)
torch_tensor = torch.randn(3, 4)
numpy_array = torch_tensor.detach().cpu().numpy()
tf_tensor = tf.convert_to_tensor(numpy_array)

# TensorFlow to PyTorch (via NumPy)
tf_tensor = tf.random.normal([3, 4])
numpy_array = tf_tensor.numpy()
torch_tensor = torch.from_numpy(numpy_array)

# Helper functions for model interoperability
def torch_to_tf(tensor):
    """Convert PyTorch tensor to TensorFlow tensor."""
    return tf.convert_to_tensor(tensor.detach().cpu().numpy())

def tf_to_torch(tensor, device='cpu'):
    """Convert TensorFlow tensor to PyTorch tensor."""
    return torch.from_numpy(tensor.numpy()).to(device)
```

---

## Real-World Applications of Tensors

### Image Processing Pipeline

```python
import torch
import torch.nn.functional as F
from PIL import Image
import numpy as np

def process_image_batch(image_paths, target_size=(224, 224)):
    """
    Process a batch of images into normalized tensors.
    
    Returns tensor of shape (batch, channels, height, width)
    """
    batch = []
    
    for path in image_paths:
        # Load image
        img = Image.open(path).convert('RGB')
        img = img.resize(target_size)
        
        # Convert to tensor
        img_array = np.array(img, dtype=np.float32)  # (H, W, C)
        img_tensor = torch.from_numpy(img_array)
        img_tensor = img_tensor.permute(2, 0, 1)  # (C, H, W)
        
        batch.append(img_tensor)
    
    # Stack into batch
    batch_tensor = torch.stack(batch)  # (B, C, H, W)
    
    # Normalize (ImageNet statistics)
    mean = torch.tensor([0.485, 0.456, 0.406]).view(1, 3, 1, 1)
    std = torch.tensor([0.229, 0.224, 0.225]).view(1, 3, 1, 1)
    
    batch_tensor = batch_tensor / 255.0
    batch_tensor = (batch_tensor - mean) / std
    
    return batch_tensor

# Data augmentation with tensors
def augment_batch(batch):
    """Apply random augmentations to image batch."""
    # Random horizontal flip
    if torch.rand(1) > 0.5:
        batch = torch.flip(batch, dims=[3])
    
    # Random rotation (small angles)
    angle = (torch.rand(1) - 0.5) * 20  # -10 to +10 degrees
    # Would use torchvision.transforms.functional.rotate in practice
    
    # Color jitter (brightness)
    brightness = 0.8 + torch.rand(1) * 0.4  # 0.8 to 1.2
    batch = batch * brightness
    
    # Clamp values
    batch = torch.clamp(batch, -2.5, 2.5)
    
    return batch
```

### Text Embedding with Tensors

```python
import torch
import torch.nn as nn

class TextEmbedder(nn.Module):
    """
    Convert text tokens to embeddings with positional encoding.
    """
    def __init__(self, vocab_size, embed_dim, max_seq_len=512):
        super().__init__()
        self.token_embedding = nn.Embedding(vocab_size, embed_dim)
        self.position_embedding = nn.Embedding(max_seq_len, embed_dim)
        self.embed_dim = embed_dim
        
    def forward(self, token_ids):
        """
        Args:
            token_ids: Tensor of shape (batch, seq_len)
        
        Returns:
            Embeddings of shape (batch, seq_len, embed_dim)
        """
        batch_size, seq_len = token_ids.shape
        
        # Get token embeddings
        token_embeds = self.token_embedding(token_ids)
        print(f"Token embeddings: {token_embeds.shape}")
        
        # Create position indices
        positions = torch.arange(seq_len, device=token_ids.device)
        positions = positions.unsqueeze(0).expand(batch_size, -1)
        
        # Get position embeddings
        pos_embeds = self.position_embedding(positions)
        print(f"Position embeddings: {pos_embeds.shape}")
        
        # Combine
        embeddings = token_embeds + pos_embeds
        print(f"Combined embeddings: {embeddings.shape}")
        
        return embeddings

# Example usage
vocab_size = 30000
embed_dim = 768
embedder = TextEmbedder(vocab_size, embed_dim)

# Simulate tokenized text
token_ids = torch.randint(0, vocab_size, (4, 128))  # 4 sentences, 128 tokens each
embeddings = embedder(token_ids)
```

### Time Series Processing

```python
import torch
import torch.nn as nn

def prepare_time_series(data, sequence_length, forecast_horizon):
    """
    Prepare time series data for sequence-to-sequence prediction.
    
    Args:
        data: 1D array of time series values
        sequence_length: Number of past time steps to use
        forecast_horizon: Number of future time steps to predict
    
    Returns:
        X: Input sequences (num_samples, sequence_length, features)
        y: Target sequences (num_samples, forecast_horizon)
    """
    data = torch.tensor(data, dtype=torch.float32)
    
    X, y = [], []
    for i in range(len(data) - sequence_length - forecast_horizon + 1):
        X.append(data[i:i + sequence_length])
        y.append(data[i + sequence_length:i + sequence_length + forecast_horizon])
    
    X = torch.stack(X)
    y = torch.stack(y)
    
    # Add feature dimension
    X = X.unsqueeze(-1)  # (samples, seq_len, 1)
    
    return X, y

# Example
import numpy as np
time_series = np.sin(np.linspace(0, 10 * np.pi, 1000)) + np.random.randn(1000) * 0.1

X, y = prepare_time_series(time_series, sequence_length=50, forecast_horizon=10)
print(f"Input shape: {X.shape}")   # (940, 50, 1)
print(f"Target shape: {y.shape}")  # (940, 10)
```

---

## FAQs About Tensors in Deep Learning

### What's the difference between torch.Tensor and torch.tensor?

`torch.Tensor` is the class, while `torch.tensor()` is a function that creates a tensor. Use `torch.tensor()` for creating tensors as it provides better type inference:

```python
# torch.tensor infers dtype from data
int_tensor = torch.tensor([1, 2, 3])  # torch.int64
float_tensor = torch.tensor([1.0, 2.0])  # torch.float32

# torch.Tensor defaults to float32
default_tensor = torch.Tensor([1, 2, 3])  # torch.float32
```

### Why is my tensor operation slow despite using GPU?

Common causes:
1. **Data transfer overhead**: Moving data between CPU and GPU
2. **Small operations**: GPU overhead dominates for small tensors
3. **Non-contiguous memory**: Use `.contiguous()` before operations
4. **Synchronization**: Operations are asynchronous; use `torch.cuda.synchronize()`

### How do I handle out-of-memory errors?

```python
# Gradient checkpointing
model = torch.utils.checkpoint.checkpoint_sequential(model, chunks=4)

# Mixed precision training
scaler = torch.cuda.amp.GradScaler()
with torch.cuda.amp.autocast():
    output = model(input)

# Clear cache
torch.cuda.empty_cache()

# Reduce batch size or use gradient accumulation
```

### When should I use .view() vs .reshape()?

- `.view()`: Requires contiguous memory, faster, fails if not contiguous
- `.reshape()`: Works with non-contiguous tensors, may copy data

```python
tensor = torch.randn(3, 4)
transposed = tensor.T  # Non-contiguous

# This fails:
# viewed = transposed.view(-1)

# This works:
reshaped = transposed.reshape(-1)
# Or make contiguous first:
viewed = transposed.contiguous().view(-1)
```

---

## Key Takeaways

1. **Tensors are multi-dimensional arrays** that generalize scalars, vectors, and matrices

2. **Choose the right framework**: NumPy for preprocessing, PyTorch/TensorFlow for training

3. **Master broadcasting** to write efficient, readable code

4. **Use GPU acceleration** for operations on large tensors (1000+ elements)

5. **Debug systematically**: Check shapes, dtypes, devices, and values

6. **Understand memory**: `.view()` shares memory, `.clone()` copies

7. **Convert safely**: Always use `.detach().cpu().numpy()` for PyTorch to NumPy

---

## Next Steps

Now that you understand tensors, explore these related topics:

1. **[Linear Algebra for Machine Learning](/docs/ai-ml/foundations/mathematics/linear-algebra/linear-algebra-machine-learning-complete-guide/)** - Matrix operations behind neural networks
2. **[Automatic Differentiation](/docs/ai-ml/foundations/mathematics/calculus/automatic-differentiation/)** - How tensors track gradients
3. **[Building Neural Networks from Scratch](/docs/ai-ml/deep-learning/neural-networks-from-scratch/)** - Apply tensor operations

---

## References

1. PyTorch Documentation: "Tensors" - https://pytorch.org/docs/stable/tensors.html
2. TensorFlow Guide: "Introduction to Tensors" - https://www.tensorflow.org/guide/tensor
3. NumPy Documentation: "Array objects" - https://numpy.org/doc/stable/reference/arrays.html
4. Goodfellow, I., et al. "Deep Learning" (2016) - Chapter 2: Linear Algebra

---

*Last updated: January 2024. Found this guide helpful? Share it with fellow ML practitioners and check out our complete [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
