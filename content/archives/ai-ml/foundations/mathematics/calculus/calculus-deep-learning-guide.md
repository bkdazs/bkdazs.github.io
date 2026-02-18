---
title: "Calculus for Deep Learning: Derivatives, Gradients, and Chain Rule Explained"
description: "Master the calculus foundations for deep learning. Learn derivatives, gradients, partial derivatives, Jacobians, and chain rule with practical neural network examples."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 1
toc: true
categories: ["AI/ML", "Mathematics"]
tags: ["calculus", "deep learning", "gradients", "derivatives", "chain rule", "neural networks", "backpropagation"]
series: ["Mathematics for ML"]
---

# Calculus for Deep Learning: Derivatives, Gradients, and Chain Rule Explained

**"Calculus is the language of neural networks—every weight update speaks it fluently."** — Yann LeCun

When you train a neural network, you're essentially performing millions of calculus operations. The model learns by computing derivatives to understand how to adjust its parameters. Without calculus, deep learning as we know it wouldn't exist.

In this comprehensive guide, we'll build your calculus intuition from derivatives to gradients to the chain rule—everything you need to understand how neural networks actually learn.

---

## Why Does Deep Learning Need Calculus?

### The Optimization Perspective

Every machine learning model is an optimization problem:

$$\theta^* = \arg\min_\theta \mathcal{L}(\theta)$$

Where:
- $\theta$ = model parameters (weights and biases)
- $\mathcal{L}$ = loss function
- $\theta^*$ = optimal parameters

To find the minimum, we need to know **which direction** to move and **how far**. Calculus gives us both through derivatives.

### What Happens During Training

1. **Forward Pass**: Compute predictions using current parameters
2. **Loss Calculation**: Measure how wrong predictions are
3. **Backward Pass**: Calculate derivatives (gradients) of loss w.r.t. parameters
4. **Update**: Adjust parameters in the direction that reduces loss

```python
# Simplified training loop
for epoch in range(num_epochs):
    # Forward pass
    predictions = model(inputs)
    loss = compute_loss(predictions, targets)
    
    # Backward pass (calculus happens here!)
    gradients = compute_gradients(loss, model.parameters)
    
    # Update (gradient descent)
    for param, grad in zip(model.parameters, gradients):
        param -= learning_rate * grad
```

---

## What Are Derivatives and Why Do They Matter?

### The Intuition

A **derivative** measures how a function changes when its input changes. It's the instantaneous rate of change—the slope at any point.

$$\frac{df}{dx} = \lim_{h \to 0} \frac{f(x + h) - f(x)}{h}$$

For deep learning, this tells us: *"If I change this parameter slightly, how much will my loss change?"*

### Derivatives in Machine Learning

```python
import numpy as np
import matplotlib.pyplot as plt

# Example: Simple quadratic loss
def loss_function(w):
    """Simplified loss as function of weight w."""
    return (w - 3)**2 + 1

def loss_derivative(w):
    """Analytical derivative."""
    return 2 * (w - 3)

# Numerical derivative (finite difference)
def numerical_derivative(f, x, h=1e-5):
    """Approximate derivative using finite difference."""
    return (f(x + h) - f(x - h)) / (2 * h)

# Compare analytical and numerical
w = 5.0
analytical = loss_derivative(w)
numerical = numerical_derivative(loss_function, w)

print(f"Analytical derivative at w={w}: {analytical}")
print(f"Numerical derivative at w={w}: {numerical:.6f}")
print(f"Difference: {abs(analytical - numerical):.2e}")

# Visualization
w_values = np.linspace(-2, 8, 100)
losses = [loss_function(w) for w in w_values]
derivatives = [loss_derivative(w) for w in w_values]

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].plot(w_values, losses, 'b-', linewidth=2)
axes[0].axhline(y=1, color='r', linestyle='--', label='Minimum')
axes[0].axvline(x=3, color='r', linestyle='--')
axes[0].set_xlabel('Weight (w)')
axes[0].set_ylabel('Loss')
axes[0].set_title('Loss Function')
axes[0].legend()

axes[1].plot(w_values, derivatives, 'g-', linewidth=2)
axes[1].axhline(y=0, color='r', linestyle='--', label='Zero gradient')
axes[1].set_xlabel('Weight (w)')
axes[1].set_ylabel('Derivative')
axes[1].set_title('Derivative of Loss')
axes[1].legend()

plt.tight_layout()
plt.show()
```

### Common Derivatives in Deep Learning

| Function | Formula | Derivative | Used In |
|----------|---------|------------|---------|
| Linear | $f(x) = ax + b$ | $f'(x) = a$ | Linear layers |
| Square | $f(x) = x^2$ | $f'(x) = 2x$ | MSE loss |
| Exponential | $f(x) = e^x$ | $f'(x) = e^x$ | Softmax |
| Logarithm | $f(x) = \ln(x)$ | $f'(x) = 1/x$ | Cross-entropy |
| Power | $f(x) = x^n$ | $f'(x) = nx^{n-1}$ | Polynomial features |

---

## How Do Partial Derivatives Extend to Multiple Variables?

### The Need for Multiple Variables

Real neural networks have millions of parameters. We need to know how changing **each** parameter affects the loss. That's where partial derivatives come in.

A **partial derivative** measures how a function changes when you change just one variable while holding others constant:

$$\frac{\partial f}{\partial x_i} = \lim_{h \to 0} \frac{f(x_1, ..., x_i + h, ..., x_n) - f(x_1, ..., x_i, ..., x_n)}{h}$$

### Example: Loss with Two Parameters

```python
import numpy as np

def loss_2d(w1, w2):
    """Loss function with two parameters."""
    return (w1 - 2)**2 + (w2 - 3)**2 + w1 * w2

def partial_w1(w1, w2):
    """Partial derivative with respect to w1."""
    return 2 * (w1 - 2) + w2

def partial_w2(w1, w2):
    """Partial derivative with respect to w2."""
    return 2 * (w2 - 3) + w1

# Example point
w1, w2 = 1.0, 1.0

print(f"Loss at ({w1}, {w2}): {loss_2d(w1, w2)}")
print(f"∂L/∂w1 = {partial_w1(w1, w2)}")
print(f"∂L/∂w2 = {partial_w2(w1, w2)}")

# Numerical verification
h = 1e-5
numerical_partial_w1 = (loss_2d(w1 + h, w2) - loss_2d(w1 - h, w2)) / (2 * h)
numerical_partial_w2 = (loss_2d(w1, w2 + h) - loss_2d(w1, w2 - h)) / (2 * h)

print(f"\nNumerical ∂L/∂w1 = {numerical_partial_w1:.6f}")
print(f"Numerical ∂L/∂w2 = {numerical_partial_w2:.6f}")
```

### Visualizing Partial Derivatives

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

# Create grid
w1_range = np.linspace(-2, 6, 50)
w2_range = np.linspace(-2, 8, 50)
W1, W2 = np.meshgrid(w1_range, w2_range)
Z = (W1 - 2)**2 + (W2 - 3)**2 + W1 * W2

# 3D plot
fig = plt.figure(figsize=(12, 5))

ax1 = fig.add_subplot(121, projection='3d')
ax1.plot_surface(W1, W2, Z, cmap='viridis', alpha=0.8)
ax1.set_xlabel('w1')
ax1.set_ylabel('w2')
ax1.set_zlabel('Loss')
ax1.set_title('Loss Surface')

# Contour plot with gradient arrows
ax2 = fig.add_subplot(122)
contour = ax2.contour(W1, W2, Z, levels=20)
ax2.clabel(contour, inline=True, fontsize=8)

# Add gradient arrows at selected points
for w1 in [0, 2, 4]:
    for w2 in [0, 2, 4, 6]:
        gw1 = partial_w1(w1, w2)
        gw2 = partial_w2(w1, w2)
        ax2.arrow(w1, w2, -gw1*0.2, -gw2*0.2, 
                  head_width=0.2, head_length=0.1, fc='red', ec='red')

ax2.set_xlabel('w1')
ax2.set_ylabel('w2')
ax2.set_title('Contour Plot with Negative Gradients')

plt.tight_layout()
plt.show()
```

---

## What Is a Gradient and Why Is It Central to Training?

### Definition of Gradient

The **gradient** is the vector of all partial derivatives:

$$\nabla f = \begin{bmatrix} \frac{\partial f}{\partial x_1} \\ \frac{\partial f}{\partial x_2} \\ \vdots \\ \frac{\partial f}{\partial x_n} \end{bmatrix}$$

The gradient points in the direction of **steepest increase**. To minimize loss, we move in the **opposite direction** (negative gradient).

### Properties of Gradients

1. **Direction**: Points toward steepest ascent
2. **Magnitude**: Indicates steepness (large gradient = steep slope)
3. **At minimum**: Gradient equals zero vector

```python
import numpy as np

class GradientDemo:
    """Demonstrate gradient properties."""
    
    def __init__(self):
        self.path = []
    
    def loss(self, params):
        """Bowl-shaped loss function."""
        w1, w2 = params
        return w1**2 + 2*w2**2
    
    def gradient(self, params):
        """Compute gradient."""
        w1, w2 = params
        return np.array([2*w1, 4*w2])
    
    def gradient_descent(self, start, lr=0.1, steps=50):
        """Perform gradient descent."""
        params = np.array(start, dtype=float)
        self.path = [params.copy()]
        
        for _ in range(steps):
            grad = self.gradient(params)
            params = params - lr * grad
            self.path.append(params.copy())
        
        return params
    
    def visualize(self):
        """Visualize gradient descent path."""
        import matplotlib.pyplot as plt
        
        # Create loss surface
        x = np.linspace(-5, 5, 100)
        y = np.linspace(-5, 5, 100)
        X, Y = np.meshgrid(x, y)
        Z = X**2 + 2*Y**2
        
        plt.figure(figsize=(10, 8))
        plt.contour(X, Y, Z, levels=20, cmap='viridis')
        plt.colorbar(label='Loss')
        
        # Plot path
        path = np.array(self.path)
        plt.plot(path[:, 0], path[:, 1], 'ro-', markersize=3, linewidth=1)
        plt.plot(path[0, 0], path[0, 1], 'go', markersize=10, label='Start')
        plt.plot(path[-1, 0], path[-1, 1], 'r*', markersize=15, label='End')
        
        plt.xlabel('w1')
        plt.ylabel('w2')
        plt.title('Gradient Descent Path')
        plt.legend()
        plt.show()

# Run demonstration
demo = GradientDemo()
final_params = demo.gradient_descent(start=[4.0, 3.0], lr=0.1, steps=50)
print(f"Final parameters: {final_params}")
print(f"Final loss: {demo.loss(final_params):.6f}")
demo.visualize()
```

### Gradient Descent Update Rule

The fundamental update rule of gradient descent:

$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \mathcal{L}(\theta_t)$$

Where:
- $\theta_t$ = parameters at step t
- $\eta$ = learning rate
- $\nabla_\theta \mathcal{L}$ = gradient of loss with respect to parameters

---

## What Is the Chain Rule and Why Does Backpropagation Need It?

### The Chain Rule Explained

When functions are composed (nested), the chain rule tells us how to compute derivatives:

For $y = f(g(x))$:

$$\frac{dy}{dx} = \frac{dy}{dg} \cdot \frac{dg}{dx} = f'(g(x)) \cdot g'(x)$$

### Why Neural Networks Need the Chain Rule

A neural network is a composition of many functions:

$$\text{output} = f_L(f_{L-1}(...f_2(f_1(x))...))$$

Each layer applies a linear transformation followed by an activation:

$$a^{(l)} = \sigma(W^{(l)} a^{(l-1)} + b^{(l)})$$

To compute how the loss changes with respect to weights in early layers, we must chain derivatives through all subsequent layers.

### Chain Rule Example: Simple Network

```python
import numpy as np

def sigmoid(x):
    return 1 / (1 + np.exp(-np.clip(x, -500, 500)))

def sigmoid_derivative(x):
    s = sigmoid(x)
    return s * (1 - s)

# Simple 2-layer network
# Input -> Hidden -> Output
# x -> z1 -> a1 -> z2 -> a2 -> loss

class SimpleNetwork:
    def __init__(self):
        np.random.seed(42)
        # Layer 1: 2 inputs -> 3 hidden
        self.W1 = np.random.randn(2, 3) * 0.5
        self.b1 = np.zeros(3)
        # Layer 2: 3 hidden -> 1 output
        self.W2 = np.random.randn(3, 1) * 0.5
        self.b2 = np.zeros(1)
    
    def forward(self, x):
        """Forward pass with cached values for backprop."""
        # Layer 1
        self.z1 = x @ self.W1 + self.b1
        self.a1 = sigmoid(self.z1)
        
        # Layer 2
        self.z2 = self.a1 @ self.W2 + self.b2
        self.a2 = sigmoid(self.z2)
        
        return self.a2
    
    def backward(self, x, y):
        """
        Backward pass using chain rule.
        
        Loss = (1/2) * (a2 - y)^2
        """
        m = x.shape[0]  # batch size
        
        # Output layer gradient
        # dL/da2 = a2 - y
        dL_da2 = self.a2 - y
        
        # Chain rule: dL/dz2 = dL/da2 * da2/dz2
        da2_dz2 = sigmoid_derivative(self.z2)
        dL_dz2 = dL_da2 * da2_dz2
        
        # dL/dW2 = dL/dz2 * dz2/dW2 = a1.T @ dL_dz2
        dL_dW2 = self.a1.T @ dL_dz2 / m
        dL_db2 = np.mean(dL_dz2, axis=0)
        
        # Hidden layer gradient (chain through W2)
        # dL/da1 = dL/dz2 @ W2.T
        dL_da1 = dL_dz2 @ self.W2.T
        
        # dL/dz1 = dL/da1 * da1/dz1
        da1_dz1 = sigmoid_derivative(self.z1)
        dL_dz1 = dL_da1 * da1_dz1
        
        # dL/dW1 = x.T @ dL_dz1
        dL_dW1 = x.T @ dL_dz1 / m
        dL_db1 = np.mean(dL_dz1, axis=0)
        
        return {'dW1': dL_dW1, 'db1': dL_db1, 
                'dW2': dL_dW2, 'db2': dL_db2}
    
    def train_step(self, x, y, lr=0.1):
        """One training step."""
        # Forward
        pred = self.forward(x)
        loss = 0.5 * np.mean((pred - y)**2)
        
        # Backward
        grads = self.backward(x, y)
        
        # Update
        self.W1 -= lr * grads['dW1']
        self.b1 -= lr * grads['db1']
        self.W2 -= lr * grads['dW2']
        self.b2 -= lr * grads['db2']
        
        return loss

# Training example: XOR problem
X = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([[0], [1], [1], [0]])

net = SimpleNetwork()

print("Training Simple Network with Chain Rule Backprop")
print("-" * 50)

for epoch in range(1000):
    loss = net.train_step(X, y, lr=1.0)
    if epoch % 100 == 0:
        print(f"Epoch {epoch:4d}: Loss = {loss:.6f}")

print("\nFinal predictions:")
for i in range(len(X)):
    pred = net.forward(X[i:i+1])
    print(f"Input: {X[i]} -> Prediction: {pred[0,0]:.4f}, Target: {y[i,0]}")
```

### Chain Rule Visualization

```
Forward Pass:
x → [W1, b1] → z1 → σ → a1 → [W2, b2] → z2 → σ → a2 → Loss

Backward Pass (Chain Rule):
dL/dW1 = dL/da2 · da2/dz2 · dz2/da1 · da1/dz1 · dz1/dW1
         ↑         ↑         ↑         ↑         ↑
       output   sigmoid    W2.T     sigmoid    x.T
       error   derivative          derivative
```

---

## What Are Jacobians and When Do You Need Them?

### Jacobian Matrix Definition

When a function maps vectors to vectors, the **Jacobian** is the matrix of all partial derivatives:

For $\mathbf{f}: \mathbb{R}^n \rightarrow \mathbb{R}^m$:

$$\mathbf{J} = \begin{bmatrix} 
\frac{\partial f_1}{\partial x_1} & \cdots & \frac{\partial f_1}{\partial x_n} \\
\vdots & \ddots & \vdots \\
\frac{\partial f_m}{\partial x_1} & \cdots & \frac{\partial f_m}{\partial x_n}
\end{bmatrix}$$

### Jacobian in Neural Networks

Consider a layer that transforms $\mathbf{x} \in \mathbb{R}^n$ to $\mathbf{y} \in \mathbb{R}^m$:

```python
import numpy as np

def layer_forward(x, W, b):
    """Linear layer: y = Wx + b"""
    return W @ x + b

def jacobian_layer(W):
    """Jacobian of linear layer w.r.t. input is just W."""
    return W

# Example
np.random.seed(42)
W = np.random.randn(3, 4)  # 3 outputs, 4 inputs
b = np.zeros(3)
x = np.random.randn(4)

y = layer_forward(x, W, b)
J = jacobian_layer(W)

print(f"Input shape: {x.shape}")
print(f"Output shape: {y.shape}")
print(f"Jacobian shape: {J.shape}")  # (3, 4)

# Verify: small change in x should cause J @ dx change in y
dx = np.random.randn(4) * 0.001
y_new = layer_forward(x + dx, W, b)
dy_actual = y_new - y
dy_predicted = J @ dx

print(f"\nActual dy: {dy_actual}")
print(f"Predicted dy (J @ dx): {dy_predicted}")
print(f"Difference: {np.linalg.norm(dy_actual - dy_predicted):.2e}")
```

### Jacobian of Softmax

```python
import numpy as np

def softmax(x):
    """Compute softmax."""
    exp_x = np.exp(x - np.max(x))
    return exp_x / exp_x.sum()

def softmax_jacobian(x):
    """
    Compute Jacobian of softmax.
    
    J[i,j] = s[i] * (delta[i,j] - s[j])
    where delta[i,j] = 1 if i==j else 0
    """
    s = softmax(x)
    n = len(s)
    J = np.zeros((n, n))
    
    for i in range(n):
        for j in range(n):
            if i == j:
                J[i, j] = s[i] * (1 - s[i])
            else:
                J[i, j] = -s[i] * s[j]
    
    return J

# Vectorized version
def softmax_jacobian_vectorized(x):
    """Efficient Jacobian computation."""
    s = softmax(x).reshape(-1, 1)
    return np.diagflat(s) - s @ s.T

# Example
x = np.array([2.0, 1.0, 0.1])
s = softmax(x)
J = softmax_jacobian_vectorized(x)

print(f"Input: {x}")
print(f"Softmax: {s}")
print(f"\nJacobian:\n{J}")
print(f"Sum of each row: {J.sum(axis=1)}")  # Should be 0
```

### Vector-Jacobian Products (VJP)

In practice, we don't compute full Jacobians. Instead, we compute **Vector-Jacobian Products**:

$$\mathbf{v}^T \mathbf{J}$$

This is what backpropagation computes efficiently:

```python
import numpy as np

def vjp_linear_layer(v, W):
    """
    Vector-Jacobian product for linear layer.
    
    v: gradient from next layer (shape: m)
    W: weight matrix (shape: m x n)
    
    Returns: gradient w.r.t. input (shape: n)
    """
    return v @ W

# Example
np.random.seed(42)
W = np.random.randn(3, 4)  # 3 outputs, 4 inputs
v = np.random.randn(3)     # Upstream gradient

# VJP gives us gradient w.r.t. input
grad_input = vjp_linear_layer(v, W)
print(f"Upstream gradient shape: {v.shape}")
print(f"Input gradient shape: {grad_input.shape}")

# This is the same as computing full Jacobian then multiplying
J = W  # Jacobian of linear layer is W
grad_input_full = v @ J
print(f"VJP result: {grad_input}")
print(f"Full Jacobian result: {grad_input_full}")
```

---

## How Does Automatic Differentiation Work?

### The Magic Behind PyTorch and TensorFlow

Modern deep learning frameworks use **automatic differentiation (autodiff)** to compute gradients. They build a computational graph and apply the chain rule automatically.

```python
import numpy as np

class Value:
    """Simple autodiff implementation inspired by micrograd."""
    
    def __init__(self, data, _children=(), _op=''):
        self.data = data
        self.grad = 0.0
        self._backward = lambda: None
        self._prev = set(_children)
        self._op = _op
    
    def __repr__(self):
        return f"Value(data={self.data:.4f}, grad={self.grad:.4f})"
    
    def __add__(self, other):
        other = other if isinstance(other, Value) else Value(other)
        out = Value(self.data + other.data, (self, other), '+')
        
        def _backward():
            self.grad += out.grad
            other.grad += out.grad
        out._backward = _backward
        
        return out
    
    def __mul__(self, other):
        other = other if isinstance(other, Value) else Value(other)
        out = Value(self.data * other.data, (self, other), '*')
        
        def _backward():
            self.grad += other.data * out.grad
            other.grad += self.data * out.grad
        out._backward = _backward
        
        return out
    
    def __pow__(self, other):
        out = Value(self.data ** other, (self,), f'**{other}')
        
        def _backward():
            self.grad += other * (self.data ** (other - 1)) * out.grad
        out._backward = _backward
        
        return out
    
    def __neg__(self):
        return self * -1
    
    def __sub__(self, other):
        return self + (-other)
    
    def __truediv__(self, other):
        return self * (other ** -1)
    
    def tanh(self):
        x = self.data
        t = (np.exp(2*x) - 1) / (np.exp(2*x) + 1)
        out = Value(t, (self,), 'tanh')
        
        def _backward():
            self.grad += (1 - t**2) * out.grad
        out._backward = _backward
        
        return out
    
    def backward(self):
        """Compute gradients using reverse-mode autodiff."""
        topo = []
        visited = set()
        
        def build_topo(v):
            if v not in visited:
                visited.add(v)
                for child in v._prev:
                    build_topo(child)
                topo.append(v)
        
        build_topo(self)
        
        self.grad = 1.0
        for node in reversed(topo):
            node._backward()

# Example: gradient of (w1 * x1 + w2 * x2)^2
x1 = Value(2.0)
x2 = Value(3.0)
w1 = Value(0.5)
w2 = Value(-0.3)

# Forward pass
y = w1 * x1 + w2 * x2
loss = y ** 2

print(f"y = {y.data:.4f}")
print(f"loss = {loss.data:.4f}")

# Backward pass
loss.backward()

print(f"\nGradients:")
print(f"dL/dw1 = {w1.grad:.4f}")
print(f"dL/dw2 = {w2.grad:.4f}")
print(f"dL/dx1 = {x1.grad:.4f}")
print(f"dL/dx2 = {x2.grad:.4f}")

# Verify with manual calculation
# y = w1*x1 + w2*x2 = 0.5*2 + (-0.3)*3 = 0.1
# loss = y^2 = 0.01
# dL/dy = 2y = 0.2
# dL/dw1 = dL/dy * dy/dw1 = 0.2 * x1 = 0.2 * 2 = 0.4
print(f"\nManual verification: dL/dw1 = 2 * y * x1 = 2 * {y.data:.2f} * {x1.data:.2f} = {2 * y.data * x1.data:.4f}")
```

### Computational Graph Visualization

```
Input Layer          Hidden Layer         Output
     x1 ─────┐
             ├──> z1 = w1*x1 + w2*x2 ──> y = z1² ──> Loss
     x2 ─────┘
      ↑   ↑
     w1   w2

Backward flow:
dL/dw1 = dL/dy · dy/dz1 · dz1/dw1
       =   1   ·  2*z1  ·   x1
       = 2 * z1 * x1
```

---

## Real-World Examples: Derivatives of Common Functions

### Activation Function Derivatives

```python
import numpy as np
import matplotlib.pyplot as plt

def plot_activation_and_derivative(name, func, deriv, x_range=(-5, 5)):
    """Plot activation function and its derivative."""
    x = np.linspace(x_range[0], x_range[1], 200)
    y = func(x)
    dy = deriv(x)
    
    fig, axes = plt.subplots(1, 2, figsize=(12, 4))
    
    axes[0].plot(x, y, 'b-', linewidth=2)
    axes[0].axhline(y=0, color='k', linewidth=0.5)
    axes[0].axvline(x=0, color='k', linewidth=0.5)
    axes[0].set_xlabel('x')
    axes[0].set_ylabel('f(x)')
    axes[0].set_title(f'{name} Function')
    axes[0].grid(True, alpha=0.3)
    
    axes[1].plot(x, dy, 'r-', linewidth=2)
    axes[1].axhline(y=0, color='k', linewidth=0.5)
    axes[1].axvline(x=0, color='k', linewidth=0.5)
    axes[1].set_xlabel('x')
    axes[1].set_ylabel("f'(x)")
    axes[1].set_title(f'{name} Derivative')
    axes[1].grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()

# Sigmoid
sigmoid = lambda x: 1 / (1 + np.exp(-np.clip(x, -500, 500)))
sigmoid_deriv = lambda x: sigmoid(x) * (1 - sigmoid(x))
plot_activation_and_derivative('Sigmoid', sigmoid, sigmoid_deriv)

# Tanh
tanh_deriv = lambda x: 1 - np.tanh(x)**2
plot_activation_and_derivative('Tanh', np.tanh, tanh_deriv)

# ReLU
relu = lambda x: np.maximum(0, x)
relu_deriv = lambda x: (x > 0).astype(float)
plot_activation_and_derivative('ReLU', relu, relu_deriv)

# Leaky ReLU
alpha = 0.01
leaky_relu = lambda x: np.where(x > 0, x, alpha * x)
leaky_relu_deriv = lambda x: np.where(x > 0, 1, alpha)
plot_activation_and_derivative('Leaky ReLU', leaky_relu, leaky_relu_deriv)

# GELU (used in transformers)
gelu = lambda x: 0.5 * x * (1 + np.tanh(np.sqrt(2/np.pi) * (x + 0.044715 * x**3)))
gelu_deriv = lambda x: 0.5 * (1 + np.tanh(np.sqrt(2/np.pi) * (x + 0.044715 * x**3))) + \
                        0.5 * x * (1 - np.tanh(np.sqrt(2/np.pi) * (x + 0.044715 * x**3))**2) * \
                        np.sqrt(2/np.pi) * (1 + 3 * 0.044715 * x**2)
plot_activation_and_derivative('GELU', gelu, gelu_deriv)
```

### Loss Function Derivatives

| Loss | Formula | Derivative |
|------|---------|------------|
| MSE | $\frac{1}{n}\sum(y - \hat{y})^2$ | $\frac{2}{n}(\hat{y} - y)$ |
| Cross-Entropy | $-\sum y \log(\hat{y})$ | $-\frac{y}{\hat{y}}$ |
| Binary CE | $-[y\log(\hat{y}) + (1-y)\log(1-\hat{y})]$ | $\frac{\hat{y} - y}{\hat{y}(1-\hat{y})}$ |

```python
import numpy as np

# MSE Loss and derivative
def mse_loss(y_true, y_pred):
    return np.mean((y_true - y_pred)**2)

def mse_derivative(y_true, y_pred):
    return 2 * (y_pred - y_true) / len(y_true)

# Cross-entropy loss and derivative (with softmax)
def cross_entropy_loss(y_true, y_pred):
    """y_pred should be probabilities from softmax."""
    return -np.sum(y_true * np.log(y_pred + 1e-10))

def softmax_cross_entropy_derivative(y_true, y_pred):
    """Combined softmax + cross-entropy derivative."""
    return y_pred - y_true  # Simplified!

# Example
y_true = np.array([1.0, 0.0, 0.0])  # One-hot
y_pred = np.array([0.7, 0.2, 0.1])  # Softmax output

loss = cross_entropy_loss(y_true, y_pred)
grad = softmax_cross_entropy_derivative(y_true, y_pred)

print(f"Cross-entropy loss: {loss:.4f}")
print(f"Gradient: {grad}")
```

---

## FAQs

### What's the difference between gradient and derivative?

- **Derivative**: Rate of change of a function with one variable
- **Gradient**: Vector of all partial derivatives (multi-variable functions)

A gradient is essentially a collection of derivatives for functions with multiple inputs.

### Why do vanishing/exploding gradients happen?

When gradients are repeatedly multiplied through layers:
- **Vanishing**: Gradients < 1 multiply to ~0 (sigmoid, tanh)
- **Exploding**: Gradients > 1 multiply to infinity

Solutions: ReLU activations, residual connections, batch normalization, gradient clipping.

### How do you verify gradient computations?

Use **gradient checking** with finite differences:

```python
def gradient_check(f, x, epsilon=1e-7):
    """Check analytical gradient against numerical gradient."""
    numerical_grad = np.zeros_like(x)
    
    for i in range(len(x)):
        x_plus = x.copy()
        x_plus[i] += epsilon
        x_minus = x.copy()
        x_minus[i] -= epsilon
        numerical_grad[i] = (f(x_plus) - f(x_minus)) / (2 * epsilon)
    
    return numerical_grad
```

---

## Key Takeaways

1. **Derivatives measure change**: They tell us how to adjust parameters to reduce loss

2. **Gradients extend to multiple variables**: The gradient vector points toward steepest ascent

3. **Chain rule enables backpropagation**: Composed functions require chained derivatives

4. **Jacobians generalize to vector functions**: Matrix of all partial derivatives

5. **Autodiff automates everything**: Modern frameworks handle calculus automatically

6. **Numerical stability matters**: Use appropriate activations and gradient clipping

---

## Next Steps

Continue mastering the mathematics behind neural networks:

1. **[Backpropagation Explained](/docs/ai-ml/foundations/mathematics/calculus/backpropagation-explained/)** - Deep dive into how networks learn
2. **[Gradient Descent Optimizers](/docs/ai-ml/foundations/mathematics/calculus/gradient-descent-optimizers/)** - SGD, Adam, and beyond
3. **[Linear Algebra for ML](/docs/ai-ml/foundations/mathematics/linear-algebra/linear-algebra-machine-learning-complete-guide/)** - Matrix operations for neural networks

---

## References

1. Goodfellow, I., et al. "Deep Learning" (2016) - Chapter 6: Deep Feedforward Networks
2. Ruder, S. "An overview of gradient descent optimization algorithms" (2016)
3. Karpathy, A. "Micrograd" - https://github.com/karpathy/micrograd
4. Stanford CS231n: "Backpropagation, Intuitions" - https://cs231n.github.io/optimization-2/

---

*Last updated: January 2024. This guide is part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
