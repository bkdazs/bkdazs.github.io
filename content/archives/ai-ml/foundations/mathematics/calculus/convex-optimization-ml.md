---
title: "Convex Optimization for Machine Learning: From Lagrange to KKT Conditions"
description: "Master convex optimization fundamentals for machine learning. Learn convex functions, Lagrange multipliers, KKT conditions, and their applications in SVMs, regularization, and more."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 5
toc: true
categories: ["AI/ML", "Mathematics"]
tags: ["convex optimization", "lagrange multipliers", "KKT conditions", "machine learning", "constrained optimization", "SVM"]
series: ["Mathematics for ML"]
---

# Convex Optimization for Machine Learning: From Lagrange to KKT Conditions

**"Machine learning is convex optimization in disguise."** — Stephen Boyd, Stanford

Many machine learning algorithms are optimization problems. Understanding convex optimization gives you powerful tools to analyze, improve, and create new algorithms. From SVMs to regularized regression, convex optimization is everywhere in ML.

In this comprehensive guide, you'll learn the theory and practice of convex optimization for machine learning.

---

## Why Does Convexity Matter in Machine Learning?

### The Guarantee of Global Optima

For convex problems:
- **Every local minimum is a global minimum**
- Gradient descent always converges to optimal solution
- No saddle points to get stuck in

For non-convex problems (deep learning):
- Many local minima and saddle points
- No guarantee of finding global optimum
- Requires careful initialization and hyperparameters

### Convex ML Algorithms

| Algorithm | Convex? | Why It Matters |
|-----------|---------|----------------|
| Linear Regression | ✅ Yes | Closed-form solution exists |
| Logistic Regression | ✅ Yes | Guaranteed convergence |
| SVM | ✅ Yes | Unique optimal hyperplane |
| Lasso/Ridge | ✅ Yes | Sparse/stable solutions |
| Neural Networks | ❌ No | Multiple local minima |
| K-Means | ❌ No | Results depend on initialization |

---

## What Makes a Function Convex?

### Definition of Convexity

A function $f$ is **convex** if for all $x, y$ and $\lambda \in [0, 1]$:

$$f(\lambda x + (1-\lambda)y) \leq \lambda f(x) + (1-\lambda) f(y)$$

Geometrically: the line segment between any two points lies **above** the function.

```python
import numpy as np
import matplotlib.pyplot as plt

def visualize_convexity():
    """Visualize convex vs non-convex functions."""
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    x = np.linspace(-3, 3, 100)
    
    # Convex function: x²
    y_convex = x ** 2
    
    # Non-convex function: x⁴ - 2x²
    y_nonconvex = x**4 - 2*x**2
    
    # Plot convex
    ax = axes[0]
    ax.plot(x, y_convex, 'b-', linewidth=2, label='f(x) = x²')
    
    # Show line segment above curve
    x1, x2 = -2, 1.5
    y1, y2 = x1**2, x2**2
    ax.plot([x1, x2], [y1, y2], 'r--', linewidth=2, label='Line segment')
    ax.plot([x1, x2], [y1, y2], 'ro', markersize=8)
    
    # Show convexity inequality
    lam = 0.6
    x_mid = lam * x1 + (1-lam) * x2
    y_line = lam * y1 + (1-lam) * y2
    y_func = x_mid ** 2
    ax.plot([x_mid, x_mid], [y_func, y_line], 'g-', linewidth=2)
    ax.plot(x_mid, y_func, 'go', markersize=8, label='f(mid) ≤ line')
    ax.plot(x_mid, y_line, 'g^', markersize=8)
    
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('CONVEX: f(λx + (1-λ)y) ≤ λf(x) + (1-λ)f(y)')
    ax.legend()
    ax.grid(True)
    
    # Plot non-convex
    ax = axes[1]
    ax.plot(x, y_nonconvex, 'b-', linewidth=2, label='f(x) = x⁴ - 2x²')
    
    # Show line segment below curve in some regions
    x1, x2 = -1.5, 1.5
    y1, y2 = x1**4 - 2*x1**2, x2**4 - 2*x2**2
    ax.plot([x1, x2], [y1, y2], 'r--', linewidth=2, label='Line segment')
    ax.plot([x1, x2], [y1, y2], 'ro', markersize=8)
    
    # Show violation
    x_mid = 0
    y_line = (y1 + y2) / 2
    y_func = x_mid**4 - 2*x_mid**2
    ax.plot([x_mid, x_mid], [y_func, y_line], 'g-', linewidth=2)
    ax.plot(x_mid, y_func, 'go', markersize=8, label='f(mid) > line (violation!)')
    ax.plot(x_mid, y_line, 'g^', markersize=8)
    
    ax.set_xlabel('x')
    ax.set_ylabel('f(x)')
    ax.set_title('NON-CONVEX: Multiple local minima')
    ax.legend()
    ax.grid(True)
    
    plt.tight_layout()
    plt.show()

visualize_convexity()
```

### Testing for Convexity

For twice-differentiable functions:

**1D**: $f''(x) \geq 0$ for all $x$

**nD**: Hessian $\nabla^2 f(x) \succeq 0$ (positive semidefinite)

```python
import numpy as np

def check_convexity_1d(f, x_range, num_points=1000):
    """Check if 1D function is convex using second derivative."""
    x = np.linspace(x_range[0], x_range[1], num_points)
    h = (x_range[1] - x_range[0]) / num_points
    
    # Numerical second derivative
    f_values = f(x)
    second_deriv = np.diff(f_values, 2) / h**2
    
    is_convex = np.all(second_deriv >= -1e-10)  # Small tolerance
    
    return is_convex, second_deriv

# Test common functions
functions = {
    'x²': lambda x: x**2,
    'exp(x)': lambda x: np.exp(x),
    'x⁴ - 2x²': lambda x: x**4 - 2*x**2,
    'log(x)': lambda x: np.log(np.abs(x) + 1e-10),
    'sin(x)': lambda x: np.sin(x),
    '|x|': lambda x: np.abs(x)
}

print("Convexity Check Results:")
print("-" * 40)
for name, f in functions.items():
    is_convex, _ = check_convexity_1d(f, (-2, 2))
    status = "✓ Convex" if is_convex else "✗ Non-convex"
    print(f"{name:15s}: {status}")
```

### Common Convex Functions in ML

| Function | Formula | Use in ML |
|----------|---------|-----------|
| Squared error | $(y - \hat{y})^2$ | Regression loss |
| Cross-entropy | $-y\log(\hat{y})$ | Classification loss |
| Hinge loss | $\max(0, 1 - y\hat{y})$ | SVM |
| L1 norm | $\|x\|_1$ | Lasso regularization |
| L2 norm squared | $\|x\|_2^2$ | Ridge regularization |
| Log-sum-exp | $\log(\sum e^{x_i})$ | Softmax approximation |

---

## Convex Sets and Feasibility

### What Is a Convex Set?

A set $C$ is convex if for any $x, y \in C$ and $\lambda \in [0, 1]$:

$$\lambda x + (1-\lambda)y \in C$$

```python
import numpy as np
import matplotlib.pyplot as plt

def visualize_convex_sets():
    """Visualize convex vs non-convex sets."""
    
    fig, axes = plt.subplots(1, 3, figsize=(15, 5))
    
    # Convex set: Circle
    theta = np.linspace(0, 2*np.pi, 100)
    x_circle = np.cos(theta)
    y_circle = np.sin(theta)
    
    ax = axes[0]
    ax.fill(x_circle, y_circle, alpha=0.3, color='blue')
    ax.plot(x_circle, y_circle, 'b-', linewidth=2)
    
    # Show line segment inside
    x1, y1 = -0.5, 0.5
    x2, y2 = 0.7, -0.3
    ax.plot([x1, x2], [y1, y2], 'r-', linewidth=2)
    ax.plot([x1, x2], [y1, y2], 'ro', markersize=8)
    
    ax.set_title('CONVEX: Circle\nAll line segments stay inside')
    ax.set_aspect('equal')
    ax.grid(True)
    
    # Convex set: Polytope
    vertices = np.array([[0, 1], [1, 0.5], [0.8, -0.5], [-0.5, -0.7], [-1, 0.3], [0, 1]])
    
    ax = axes[1]
    ax.fill(vertices[:, 0], vertices[:, 1], alpha=0.3, color='green')
    ax.plot(vertices[:, 0], vertices[:, 1], 'g-', linewidth=2)
    
    # Show line segment inside
    x1, y1 = -0.3, 0.2
    x2, y2 = 0.5, -0.1
    ax.plot([x1, x2], [y1, y2], 'r-', linewidth=2)
    ax.plot([x1, x2], [y1, y2], 'ro', markersize=8)
    
    ax.set_title('CONVEX: Polytope\nAll line segments stay inside')
    ax.set_aspect('equal')
    ax.grid(True)
    
    # Non-convex set: Star shape
    theta = np.linspace(0, 2*np.pi, 100)
    r = 1 + 0.5 * np.sin(5*theta)
    x_star = r * np.cos(theta)
    y_star = r * np.sin(theta)
    
    ax = axes[2]
    ax.fill(x_star, y_star, alpha=0.3, color='red')
    ax.plot(x_star, y_star, 'r-', linewidth=2)
    
    # Show line segment going outside
    x1, y1 = -1.2, 0.3
    x2, y2 = 1.2, 0.3
    ax.plot([x1, x2], [y1, y2], 'k-', linewidth=2)
    ax.plot([x1, x2], [y1, y2], 'ko', markersize=8)
    
    ax.set_title('NON-CONVEX: Star\nLine segment goes outside!')
    ax.set_aspect('equal')
    ax.grid(True)
    
    plt.tight_layout()
    plt.show()

visualize_convex_sets()
```

---

## Lagrange Multipliers: Handling Equality Constraints

### The Problem

Minimize $f(x)$ subject to $g(x) = 0$

### The Lagrangian

$$\mathcal{L}(x, \lambda) = f(x) + \lambda g(x)$$

At the optimum, gradients of $f$ and $g$ are parallel:
$$\nabla f(x^*) = -\lambda \nabla g(x^*)$$

```python
import numpy as np
from scipy.optimize import minimize
import matplotlib.pyplot as plt

def lagrange_example():
    """
    Example: Minimize x² + y² subject to x + y = 1
    
    Lagrangian: L = x² + y² + λ(x + y - 1)
    
    ∂L/∂x = 2x + λ = 0  →  x = -λ/2
    ∂L/∂y = 2y + λ = 0  →  y = -λ/2
    ∂L/∂λ = x + y - 1 = 0
    
    From x = y = -λ/2 and x + y = 1:
    -λ = 1  →  λ = -1
    x = y = 0.5
    """
    
    # Objective function
    def f(xy):
        return xy[0]**2 + xy[1]**2
    
    # Constraint: x + y = 1
    def constraint(xy):
        return xy[0] + xy[1] - 1
    
    # Solve using scipy
    from scipy.optimize import minimize
    
    result = minimize(
        f, 
        x0=[0, 0],
        constraints={'type': 'eq', 'fun': constraint}
    )
    
    print("Lagrange Multiplier Example")
    print("=" * 40)
    print(f"Problem: min x² + y² s.t. x + y = 1")
    print(f"\nAnalytical solution:")
    print(f"  x* = 0.5, y* = 0.5, λ* = -1")
    print(f"\nNumerical solution:")
    print(f"  x* = {result.x[0]:.4f}, y* = {result.x[1]:.4f}")
    print(f"  min f = {result.fun:.4f}")
    
    # Visualization
    x = np.linspace(-1, 2, 100)
    y = np.linspace(-1, 2, 100)
    X, Y = np.meshgrid(x, y)
    Z = X**2 + Y**2
    
    plt.figure(figsize=(10, 8))
    
    # Contours of objective
    contours = plt.contour(X, Y, Z, levels=20, cmap='viridis')
    plt.clabel(contours, inline=True, fontsize=8)
    
    # Constraint line
    plt.plot(x, 1 - x, 'r-', linewidth=2, label='x + y = 1')
    
    # Optimal point
    plt.plot(0.5, 0.5, 'r*', markersize=20, label='Optimal (0.5, 0.5)')
    
    # Gradient of f at optimal
    scale = 0.3
    plt.arrow(0.5, 0.5, scale*1, scale*1, head_width=0.05, color='blue', label='∇f')
    
    # Gradient of g (normal to constraint)
    plt.arrow(0.5, 0.5, scale*1, scale*1, head_width=0.05, color='green', label='∇g')
    
    plt.xlabel('x')
    plt.ylabel('y')
    plt.title('Lagrange Multiplier: ∇f parallel to ∇g at optimum')
    plt.legend()
    plt.axis('equal')
    plt.xlim(-0.5, 1.5)
    plt.ylim(-0.5, 1.5)
    plt.grid(True)
    plt.show()
    
    return result

lagrange_example()
```

### Lagrange Multipliers in Machine Learning

**Application: Maximum Entropy Distribution**

Find probability distribution $p$ that maximizes entropy subject to constraints:

$$\max_p -\sum_i p_i \log p_i \quad \text{s.t.} \quad \sum_i p_i = 1, \quad \mathbb{E}[f(x)] = c$$

---

## KKT Conditions: Handling Inequality Constraints

### The General Problem

$$\min_x f(x) \quad \text{s.t.} \quad g_i(x) \leq 0, \quad h_j(x) = 0$$

### The Lagrangian

$$\mathcal{L}(x, \mu, \lambda) = f(x) + \sum_i \mu_i g_i(x) + \sum_j \lambda_j h_j(x)$$

### KKT Conditions

For a point $x^*$ to be optimal, these conditions must hold:

1. **Stationarity**: $\nabla_x \mathcal{L}(x^*, \mu^*, \lambda^*) = 0$
2. **Primal feasibility**: $g_i(x^*) \leq 0$, $h_j(x^*) = 0$
3. **Dual feasibility**: $\mu_i^* \geq 0$
4. **Complementary slackness**: $\mu_i^* g_i(x^*) = 0$

```python
import numpy as np
from scipy.optimize import minimize

def kkt_example():
    """
    Example: min x² + y²  s.t.  x + y ≥ 1
    
    Convert to: min x² + y²  s.t.  1 - x - y ≤ 0
    
    KKT conditions:
    1. 2x - μ = 0  →  x = μ/2
    2. 2y - μ = 0  →  y = μ/2
    3. 1 - x - y ≤ 0 (primal feasibility)
    4. μ ≥ 0 (dual feasibility)
    5. μ(1 - x - y) = 0 (complementary slackness)
    
    Case 1: μ = 0 (constraint inactive)
    Then x = y = 0, but this violates x + y ≥ 1
    
    Case 2: μ > 0 (constraint active, x + y = 1)
    From x = y = μ/2 and x + y = 1: μ = 1
    So x* = y* = 0.5, μ* = 1
    """
    
    # Objective
    def f(xy):
        return xy[0]**2 + xy[1]**2
    
    # Inequality constraint: x + y >= 1 → -(x + y - 1) <= 0
    def ineq_constraint(xy):
        return xy[0] + xy[1] - 1  # Must be >= 0
    
    result = minimize(
        f,
        x0=[0, 0],
        constraints={'type': 'ineq', 'fun': ineq_constraint}
    )
    
    print("KKT Example")
    print("=" * 40)
    print("Problem: min x² + y² s.t. x + y ≥ 1")
    print(f"\nSolution: x* = {result.x[0]:.4f}, y* = {result.x[1]:.4f}")
    print(f"Constraint value: x + y = {sum(result.x):.4f} ≥ 1 ✓")
    
    # Verify KKT
    x_opt = result.x
    
    # Compute gradients at optimum
    grad_f = 2 * x_opt  # [2x, 2y]
    grad_g = np.array([-1, -1])  # gradient of -(x+y-1)
    
    # From stationarity: grad_f + μ * grad_g = 0
    # 2x - μ = 0 → μ = 2x
    mu = 2 * x_opt[0]
    
    print(f"\nKKT Verification:")
    print(f"  μ* = {mu:.4f} ≥ 0 ✓ (dual feasibility)")
    print(f"  Constraint active: x + y = 1 ✓")
    print(f"  μ * (1 - x - y) = {mu * (1 - sum(x_opt)):.6f} ≈ 0 ✓ (complementary slackness)")
    
    # Visualization
    x = np.linspace(-0.5, 1.5, 100)
    y = np.linspace(-0.5, 1.5, 100)
    X, Y = np.meshgrid(x, y)
    Z = X**2 + Y**2
    
    plt.figure(figsize=(10, 8))
    
    # Contours of objective
    contours = plt.contour(X, Y, Z, levels=15, cmap='viridis')
    plt.clabel(contours, inline=True, fontsize=8)
    
    # Constraint: x + y >= 1 (shade feasible region)
    plt.fill_between(x, 1-x, 2, alpha=0.2, color='green', label='Feasible region')
    plt.plot(x, 1 - x, 'r-', linewidth=2, label='x + y = 1 (boundary)')
    
    # Optimal point
    plt.plot(0.5, 0.5, 'r*', markersize=20, label='Optimal (0.5, 0.5)')
    
    plt.xlabel('x')
    plt.ylabel('y')
    plt.title('KKT: Optimal point on constraint boundary')
    plt.legend()
    plt.xlim(-0.5, 1.5)
    plt.ylim(-0.5, 1.5)
    plt.grid(True)
    plt.show()
    
    return result

kkt_example()
```

---

## Duality: A Powerful Optimization Tool

### The Dual Problem

For the primal problem:
$$\min_x f(x) \quad \text{s.t.} \quad g_i(x) \leq 0$$

The **dual problem** is:
$$\max_\mu d(\mu) = \min_x \mathcal{L}(x, \mu) \quad \text{s.t.} \quad \mu \geq 0$$

### Weak and Strong Duality

- **Weak duality**: $d^* \leq p^*$ (dual optimal ≤ primal optimal)
- **Strong duality**: $d^* = p^*$ (for convex problems under mild conditions)

### Why Duality Matters in ML

1. **SVM dual formulation** is easier to solve and reveals support vectors
2. **Lower bounds** on optimal value
3. **Kernel trick** enabled by dual formulation

```python
import numpy as np
from scipy.optimize import minimize

def svm_dual_example():
    """
    SVM Dual Formulation Example
    
    Primal: min (1/2)||w||² s.t. y_i(w·x_i + b) ≥ 1
    
    Dual: max Σα_i - (1/2)ΣΣ α_i α_j y_i y_j (x_i·x_j)
          s.t. α_i ≥ 0, Σα_i y_i = 0
    """
    
    # Simple 2D dataset (linearly separable)
    X = np.array([
        [1, 2],
        [2, 3],
        [3, 3],
        [6, 5],
        [7, 8],
        [8, 8]
    ])
    y = np.array([-1, -1, -1, 1, 1, 1])
    
    n_samples = len(X)
    
    # Compute Gram matrix
    K = X @ X.T
    
    # Dual objective (to maximize, so we negate for minimization)
    def dual_objective(alpha):
        return -(np.sum(alpha) - 0.5 * np.sum(np.outer(alpha * y, alpha * y) * K))
    
    def dual_gradient(alpha):
        return -(np.ones(n_samples) - K @ (alpha * y) * y)
    
    # Constraints
    constraints = [
        {'type': 'eq', 'fun': lambda a: np.dot(a, y)},  # Σα_i y_i = 0
    ]
    bounds = [(0, None) for _ in range(n_samples)]  # α_i ≥ 0
    
    # Solve dual
    result = minimize(
        dual_objective,
        x0=np.zeros(n_samples),
        method='SLSQP',
        bounds=bounds,
        constraints=constraints
    )
    
    alpha = result.x
    
    # Recover w from α: w = Σα_i y_i x_i
    w = np.sum(alpha[:, np.newaxis] * y[:, np.newaxis] * X, axis=0)
    
    # Find support vectors (α > 0)
    sv_mask = alpha > 1e-5
    support_vectors = X[sv_mask]
    
    # Compute b using support vector
    sv_idx = np.where(sv_mask)[0][0]
    b = y[sv_idx] - np.dot(w, X[sv_idx])
    
    print("SVM Dual Problem Example")
    print("=" * 40)
    print(f"Optimal α: {alpha.round(4)}")
    print(f"Support vectors (α > 0): {np.where(sv_mask)[0]}")
    print(f"w = {w.round(4)}")
    print(f"b = {b:.4f}")
    print(f"\nDecision boundary: {w[0]:.2f}x₁ + {w[1]:.2f}x₂ + {b:.2f} = 0")
    
    # Visualization
    plt.figure(figsize=(10, 8))
    
    # Plot points
    plt.scatter(X[y==-1, 0], X[y==-1, 1], c='red', s=100, label='Class -1', edgecolors='k')
    plt.scatter(X[y==1, 0], X[y==1, 1], c='blue', s=100, label='Class +1', edgecolors='k')
    
    # Mark support vectors
    plt.scatter(support_vectors[:, 0], support_vectors[:, 1], 
                s=200, facecolors='none', edgecolors='green', linewidths=2,
                label='Support Vectors')
    
    # Plot decision boundary
    x_line = np.linspace(0, 10, 100)
    y_line = -(w[0] * x_line + b) / w[1]
    plt.plot(x_line, y_line, 'g-', linewidth=2, label='Decision boundary')
    
    # Plot margins
    margin = 1 / np.linalg.norm(w)
    y_margin_pos = -(w[0] * x_line + b - 1) / w[1]
    y_margin_neg = -(w[0] * x_line + b + 1) / w[1]
    plt.plot(x_line, y_margin_pos, 'g--', linewidth=1)
    plt.plot(x_line, y_margin_neg, 'g--', linewidth=1)
    plt.fill_between(x_line, y_margin_neg, y_margin_pos, alpha=0.1, color='green')
    
    plt.xlabel('x₁')
    plt.ylabel('x₂')
    plt.title(f'SVM: Found via Dual Problem (margin = {margin:.2f})')
    plt.legend()
    plt.xlim(0, 10)
    plt.ylim(0, 10)
    plt.grid(True)
    plt.show()
    
    return alpha, w, b

svm_dual_example()
```

---

## Applications in Machine Learning

### 1. Ridge Regression (L2 Regularization)

$$\min_w \|Xw - y\|_2^2 + \lambda\|w\|_2^2$$

This is convex (sum of convex functions) with closed-form solution:
$$w^* = (X^TX + \lambda I)^{-1}X^Ty$$

```python
import numpy as np

class RidgeRegression:
    """Ridge regression - convex optimization with closed form."""
    
    def __init__(self, alpha=1.0):
        self.alpha = alpha
        self.w = None
    
    def fit(self, X, y):
        """Solve via normal equations (convex -> unique solution)."""
        n_features = X.shape[1]
        # (X'X + αI)⁻¹ X'y
        self.w = np.linalg.solve(
            X.T @ X + self.alpha * np.eye(n_features),
            X.T @ y
        )
        return self
    
    def predict(self, X):
        return X @ self.w

# Example
np.random.seed(42)
X = np.random.randn(100, 5)
w_true = np.array([1, -2, 3, 0, 0])  # Sparse ground truth
y = X @ w_true + np.random.randn(100) * 0.1

model = RidgeRegression(alpha=0.1)
model.fit(X, y)

print("Ridge Regression (Convex)")
print(f"True weights:      {w_true}")
print(f"Estimated weights: {model.w.round(3)}")
```

### 2. Lasso Regression (L1 Regularization)

$$\min_w \|Xw - y\|_2^2 + \lambda\|w\|_1$$

Convex but **not differentiable** at zero. Use subgradient or proximal methods.

```python
def soft_threshold(x, threshold):
    """Soft thresholding operator (proximal operator for L1)."""
    return np.sign(x) * np.maximum(np.abs(x) - threshold, 0)

class LassoRegression:
    """Lasso regression using coordinate descent (ISTA)."""
    
    def __init__(self, alpha=1.0, max_iter=1000, tol=1e-4):
        self.alpha = alpha
        self.max_iter = max_iter
        self.tol = tol
        self.w = None
    
    def fit(self, X, y):
        """Coordinate descent for Lasso."""
        n_samples, n_features = X.shape
        self.w = np.zeros(n_features)
        
        # Precompute X'X and X'y
        XTX = X.T @ X
        XTy = X.T @ y
        
        for iteration in range(self.max_iter):
            w_old = self.w.copy()
            
            for j in range(n_features):
                # Compute residual excluding feature j
                r_j = XTy[j] - np.dot(XTX[j], self.w) + XTX[j, j] * self.w[j]
                
                # Soft thresholding update
                self.w[j] = soft_threshold(r_j, self.alpha * n_samples) / XTX[j, j]
            
            # Check convergence
            if np.linalg.norm(self.w - w_old) < self.tol:
                print(f"Converged at iteration {iteration}")
                break
        
        return self
    
    def predict(self, X):
        return X @ self.w

# Example
model_lasso = LassoRegression(alpha=0.1)
model_lasso.fit(X, y)

print("\nLasso Regression (Convex, Sparse)")
print(f"True weights:      {w_true}")
print(f"Estimated weights: {model_lasso.w.round(3)}")
print(f"Sparsity: {np.sum(np.abs(model_lasso.w) < 0.01)}/{len(model_lasso.w)} zeros")
```

### 3. Logistic Regression

$$\min_w \sum_i \log(1 + e^{-y_i w^T x_i}) + \lambda\|w\|_2^2$$

Convex! No local minima. Gradient descent converges to global optimum.

```python
class LogisticRegression:
    """Logistic regression - convex optimization."""
    
    def __init__(self, lr=0.1, max_iter=1000, reg=0.01):
        self.lr = lr
        self.max_iter = max_iter
        self.reg = reg
        self.w = None
    
    def sigmoid(self, z):
        return 1 / (1 + np.exp(-np.clip(z, -500, 500)))
    
    def loss(self, X, y):
        """Logistic loss (convex)."""
        z = X @ self.w
        return -np.mean(y * np.log(self.sigmoid(z) + 1e-10) + 
                        (1 - y) * np.log(1 - self.sigmoid(z) + 1e-10)) + \
               self.reg * np.sum(self.w ** 2)
    
    def fit(self, X, y):
        """Gradient descent (guaranteed to converge for convex)."""
        n_samples, n_features = X.shape
        self.w = np.zeros(n_features)
        
        losses = []
        for i in range(self.max_iter):
            # Gradient
            pred = self.sigmoid(X @ self.w)
            grad = X.T @ (pred - y) / n_samples + 2 * self.reg * self.w
            
            # Update
            self.w -= self.lr * grad
            
            loss = self.loss(X, y)
            losses.append(loss)
            
            if i > 0 and abs(losses[-1] - losses[-2]) < 1e-6:
                print(f"Converged at iteration {i}")
                break
        
        return self, losses
    
    def predict_proba(self, X):
        return self.sigmoid(X @ self.w)
    
    def predict(self, X):
        return (self.predict_proba(X) >= 0.5).astype(int)
```

---

## FAQs

### When is a problem convex?

A problem is convex when:
1. The objective function is convex
2. Equality constraints are affine (linear)
3. Inequality constraints define convex sets

### What if my problem is non-convex?

Options:
- **Convex relaxation**: Approximate with convex problem
- **Local optimization**: Gradient descent finds local optimum
- **Global methods**: Branch and bound, random restarts
- **Heuristics**: Problem-specific approaches

### Why do we care about KKT conditions?

KKT conditions:
1. Provide necessary conditions for optimality
2. Enable deriving dual problems
3. Give insight into solution structure (active constraints)
4. Are used in many optimization algorithms

### How do SVM support vectors relate to KKT?

By complementary slackness ($\mu_i g_i = 0$):
- Points with $\mu_i > 0$ are **support vectors** (on margin)
- Points with $\mu_i = 0$ have zero influence (inside margin)

---

## Key Takeaways

1. **Convex functions have no local minima** - gradient descent always succeeds
2. **Lagrange multipliers** handle equality constraints
3. **KKT conditions** extend to inequality constraints
4. **Duality** enables efficient algorithms (SVM kernel trick)
5. **Many ML algorithms are convex**: regression, classification, SVMs

---

## Next Steps

Continue your optimization journey:

1. **[Second-Order Optimization](/docs/ai-ml/foundations/mathematics/calculus/second-order-optimization/)** - Newton, L-BFGS methods
2. **[Gradient Descent Optimizers](/docs/ai-ml/foundations/mathematics/calculus/gradient-descent-optimizers/)** - SGD, Adam, and more
3. **[SVM Complete Guide](/docs/ai-ml/classical-ml/svm-complete-guide/)** - Convex optimization in action

---

## References

1. Boyd, S., Vandenberghe, L. "Convex Optimization" (2004) - The bible
2. Nocedal, J., Wright, S. "Numerical Optimization" (2006)
3. Shalev-Shwartz, S., Ben-David, S. "Understanding Machine Learning" (2014) - Chapter 13
4. Stanford EE364a: Convex Optimization - https://web.stanford.edu/class/ee364a/

---

*Last updated: January 2024. This guide is part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
