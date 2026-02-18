---
title: "Second-Order Optimization Methods: Newton, Quasi-Newton, and L-BFGS for Machine Learning"
description: "Master second-order optimization methods for machine learning. Learn Newton's method, Quasi-Newton, L-BFGS, and when to use them over gradient descent."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 6
toc: true
categories: ["AI/ML", "Mathematics"]
tags: ["newton method", "L-BFGS", "quasi-newton", "hessian", "optimization", "machine learning"]
series: ["Mathematics for ML"]
---

# Second-Order Optimization Methods: Newton, Quasi-Newton, and L-BFGS

**"First-order methods ask 'which direction?' Second-order methods ask 'which direction and how far?'"**

While gradient descent (first-order) only uses gradient information, second-order methods leverage curvature via the **Hessian matrix** for faster, smarter optimization. This guide covers when and how to use these powerful techniques.

---

## First-Order vs Second-Order: What's the Difference?

### The Intuition

```
First-Order (Gradient Descent):
- Uses gradient (slope) only
- Same step size in all directions
- Like walking downhill blindfolded

Second-Order (Newton's Method):
- Uses Hessian (curvature) too
- Adapts step size by curvature
- Like walking downhill with full vision
```

### Mathematical Comparison

| Aspect | First-Order | Second-Order |
|--------|-------------|--------------|
| Information | Gradient $\nabla f$ | Gradient + Hessian $\nabla^2 f$ |
| Update | $x_{k+1} = x_k - \alpha \nabla f$ | $x_{k+1} = x_k - [\nabla^2 f]^{-1} \nabla f$ |
| Convergence | Linear | Quadratic (near optimum) |
| Per-iteration cost | $O(n)$ | $O(n^3)$ |
| Memory | $O(n)$ | $O(n^2)$ |
| Best for | Large-scale, deep learning | Small-medium convex problems |

---

## The Hessian Matrix: Capturing Curvature

### Definition

For $f: \mathbb{R}^n \to \mathbb{R}$, the **Hessian** is the matrix of second partial derivatives:

$$H = \nabla^2 f = \begin{bmatrix} \frac{\partial^2 f}{\partial x_1^2} & \frac{\partial^2 f}{\partial x_1 \partial x_2} & \cdots \\ \frac{\partial^2 f}{\partial x_2 \partial x_1} & \frac{\partial^2 f}{\partial x_2^2} & \cdots \\ \vdots & \vdots & \ddots \end{bmatrix}$$

### Computing the Hessian

```python
import numpy as np
import matplotlib.pyplot as plt

def compute_hessian_numerical(f, x, h=1e-5):
    """Compute Hessian matrix numerically."""
    n = len(x)
    H = np.zeros((n, n))
    
    for i in range(n):
        for j in range(n):
            # Four-point formula for mixed partial
            x_pp = x.copy(); x_pp[i] += h; x_pp[j] += h
            x_pm = x.copy(); x_pm[i] += h; x_pm[j] -= h
            x_mp = x.copy(); x_mp[i] -= h; x_mp[j] += h
            x_mm = x.copy(); x_mm[i] -= h; x_mm[j] -= h
            
            H[i, j] = (f(x_pp) - f(x_pm) - f(x_mp) + f(x_mm)) / (4 * h * h)
    
    return H

def compute_gradient_numerical(f, x, h=1e-7):
    """Compute gradient numerically."""
    n = len(x)
    grad = np.zeros(n)
    for i in range(n):
        x_plus = x.copy(); x_plus[i] += h
        x_minus = x.copy(); x_minus[i] -= h
        grad[i] = (f(x_plus) - f(x_minus)) / (2 * h)
    return grad

# Example: Rosenbrock function
def rosenbrock(x):
    return (1 - x[0])**2 + 100 * (x[1] - x[0]**2)**2

x = np.array([0.5, 0.5])
H = compute_hessian_numerical(rosenbrock, x)
grad = compute_gradient_numerical(rosenbrock, x)

print("Hessian at [0.5, 0.5]:")
print(H.round(2))
print(f"\nCondition number: {np.linalg.cond(H):.1f}")
print(f"Eigenvalues: {np.linalg.eigvalsh(H).round(2)}")
```

### Hessian Properties Tell Us About the Landscape

```python
def analyze_hessian(H):
    """Analyze what Hessian tells us about the optimization landscape."""
    eigenvalues = np.linalg.eigvalsh(H)
    
    analysis = {
        'eigenvalues': eigenvalues,
        'condition_number': max(eigenvalues) / min(abs(eigenvalues)) if min(abs(eigenvalues)) > 1e-10 else np.inf,
        'is_positive_definite': all(eigenvalues > 0),
        'is_negative_definite': all(eigenvalues < 0),
        'is_indefinite': any(eigenvalues > 0) and any(eigenvalues < 0),
    }
    
    if analysis['is_positive_definite']:
        analysis['point_type'] = 'Local minimum'
    elif analysis['is_negative_definite']:
        analysis['point_type'] = 'Local maximum'
    elif analysis['is_indefinite']:
        analysis['point_type'] = 'Saddle point'
    else:
        analysis['point_type'] = 'Inconclusive'
    
    return analysis

# Test at different points
test_functions = {
    'Bowl (minimum)': lambda x: x[0]**2 + x[1]**2,
    'Hill (maximum)': lambda x: -(x[0]**2 + x[1]**2),
    'Saddle': lambda x: x[0]**2 - x[1]**2,
    'Ill-conditioned': lambda x: 100*x[0]**2 + x[1]**2
}

print("Hessian Analysis at Origin")
print("=" * 50)
for name, f in test_functions.items():
    H = compute_hessian_numerical(f, np.array([0.0, 0.0]))
    analysis = analyze_hessian(H)
    print(f"\n{name}:")
    print(f"  Eigenvalues: {analysis['eigenvalues'].round(2)}")
    print(f"  Condition number: {analysis['condition_number']:.1f}")
    print(f"  Point type: {analysis['point_type']}")
```

---

## Newton's Method: The Pure Second-Order Approach

### The Algorithm

Newton's method uses a **quadratic approximation** of $f$ at each step:

$$f(x + \Delta x) \approx f(x) + \nabla f(x)^T \Delta x + \frac{1}{2} \Delta x^T H \Delta x$$

Setting derivative to zero: $\nabla f + H \Delta x = 0$

**Newton step**: $\Delta x = -H^{-1} \nabla f$

```python
def newtons_method(f, x0, max_iter=50, tol=1e-8, verbose=True):
    """
    Pure Newton's method for optimization.
    
    f: objective function
    x0: starting point
    """
    x = np.array(x0, dtype=float)
    history = {'x': [x.copy()], 'f': [f(x)], 'grad_norm': []}
    
    for i in range(max_iter):
        grad = compute_gradient_numerical(f, x)
        H = compute_hessian_numerical(f, x)
        
        grad_norm = np.linalg.norm(grad)
        history['grad_norm'].append(grad_norm)
        
        if grad_norm < tol:
            if verbose:
                print(f"Converged at iteration {i}, ||grad|| = {grad_norm:.2e}")
            break
        
        # Newton step: solve H @ delta_x = -grad
        try:
            delta_x = np.linalg.solve(H, -grad)
        except np.linalg.LinAlgError:
            print("Hessian singular, using pseudo-inverse")
            delta_x = -np.linalg.pinv(H) @ grad
        
        x = x + delta_x
        history['x'].append(x.copy())
        history['f'].append(f(x))
        
        if verbose and i % 5 == 0:
            print(f"Iter {i}: f = {f(x):.6e}, ||grad|| = {grad_norm:.2e}")
    
    return x, history

# Compare Newton vs Gradient Descent on quadratic
def quadratic(x):
    """Ill-conditioned quadratic: eigenvalues 1 and 100."""
    return 50*x[0]**2 + 0.5*x[1]**2

# Gradient descent
def gradient_descent(f, x0, lr=0.01, max_iter=1000, tol=1e-8):
    x = np.array(x0, dtype=float)
    history = {'x': [x.copy()], 'f': [f(x)]}
    
    for i in range(max_iter):
        grad = compute_gradient_numerical(f, x)
        if np.linalg.norm(grad) < tol:
            break
        x = x - lr * grad
        history['x'].append(x.copy())
        history['f'].append(f(x))
    
    return x, history

x0 = [1.0, 1.0]

print("Comparison: Newton vs Gradient Descent")
print("=" * 50)

# Newton
x_newton, hist_newton = newtons_method(quadratic, x0, verbose=False)
print(f"Newton: {len(hist_newton['f'])} iterations")
print(f"  Final x: {x_newton.round(6)}")

# Gradient Descent
x_gd, hist_gd = gradient_descent(quadratic, x0, lr=0.009)
print(f"\nGradient Descent: {len(hist_gd['f'])} iterations")
print(f"  Final x: {x_gd.round(6)}")
```

### Visualizing Convergence

```python
import matplotlib.pyplot as plt
import numpy as np

def visualize_optimization_paths():
    """Compare Newton and GD convergence visually."""
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 6))
    
    # Contour plot
    x = np.linspace(-1.5, 1.5, 100)
    y = np.linspace(-1.5, 1.5, 100)
    X, Y = np.meshgrid(x, y)
    Z = 50*X**2 + 0.5*Y**2
    
    ax = axes[0]
    contours = ax.contour(X, Y, Z, levels=20, cmap='viridis')
    ax.clabel(contours, inline=True, fontsize=8)
    
    # Plot paths
    x0 = [1.0, 1.0]
    
    _, hist_newton = newtons_method(quadratic, x0, verbose=False)
    _, hist_gd = gradient_descent(quadratic, x0, lr=0.009, max_iter=100)
    
    path_newton = np.array(hist_newton['x'])
    path_gd = np.array(hist_gd['x'])
    
    ax.plot(path_newton[:, 0], path_newton[:, 1], 'r.-', linewidth=2, 
            markersize=10, label=f'Newton ({len(path_newton)-1} steps)')
    ax.plot(path_gd[:, 0], path_gd[:, 1], 'b.-', linewidth=1, 
            markersize=5, label=f'GD ({len(path_gd)-1} steps)')
    ax.plot(x0[0], x0[1], 'go', markersize=12, label='Start')
    ax.plot(0, 0, 'r*', markersize=15, label='Optimum')
    
    ax.set_xlabel('x₁')
    ax.set_ylabel('x₂')
    ax.set_title('Optimization Paths (Ill-conditioned Quadratic)')
    ax.legend()
    ax.set_aspect('equal')
    
    # Convergence plot
    ax = axes[1]
    ax.semilogy(hist_newton['f'], 'r.-', linewidth=2, markersize=8, label='Newton')
    ax.semilogy(hist_gd['f'], 'b-', linewidth=1, label='Gradient Descent')
    ax.set_xlabel('Iteration')
    ax.set_ylabel('f(x) (log scale)')
    ax.set_title('Convergence Comparison')
    ax.legend()
    ax.grid(True)
    
    plt.tight_layout()
    plt.show()

visualize_optimization_paths()
```

### Problems with Pure Newton

| Problem | Description | Solution |
|---------|-------------|----------|
| Hessian computation | $O(n^2)$ storage, $O(n^3)$ inversion | Quasi-Newton approximation |
| Non-positive definite | Newton step may not descend | Damping, trust region |
| Far from optimum | Quadratic approximation poor | Line search, trust region |
| Non-convex | May converge to saddle | Modified Newton |

---

## Damped Newton and Line Search

### The Problem

Pure Newton may take too large steps, causing divergence:

```python
def damped_newton(f, x0, max_iter=100, tol=1e-8, alpha=0.3, beta=0.8):
    """
    Newton's method with backtracking line search.
    
    Backtracking: reduce step size until sufficient decrease
    """
    x = np.array(x0, dtype=float)
    history = {'x': [x.copy()], 'f': [f(x)]}
    
    for i in range(max_iter):
        grad = compute_gradient_numerical(f, x)
        H = compute_hessian_numerical(f, x)
        
        if np.linalg.norm(grad) < tol:
            print(f"Converged at iteration {i}")
            break
        
        # Newton direction
        try:
            delta_x = np.linalg.solve(H, -grad)
        except:
            delta_x = -grad  # Fall back to gradient descent
        
        # Backtracking line search (Armijo condition)
        t = 1.0
        while f(x + t * delta_x) > f(x) + alpha * t * grad @ delta_x:
            t *= beta
            if t < 1e-10:
                break
        
        x = x + t * delta_x
        history['x'].append(x.copy())
        history['f'].append(f(x))
    
    return x, history

# Test on Rosenbrock (hard problem)
x0 = [-1.5, 2.0]
x_opt, history = damped_newton(rosenbrock, x0)
print(f"Optimal point: {x_opt.round(6)}")
print(f"f(x*) = {rosenbrock(x_opt):.2e}")
print(f"True optimum: [1, 1]")
```

---

## Quasi-Newton Methods: Approximating the Hessian

### Why Approximate?

Computing the exact Hessian is expensive ($O(n^2)$ storage, $O(n^3)$ for inversion). Quasi-Newton methods build an approximation $B_k \approx H$ using only gradient information.

### The BFGS Algorithm

**Broyden-Fletcher-Goldfarb-Shanno** is the most popular quasi-Newton method:

```python
def bfgs(f, x0, max_iter=100, tol=1e-8):
    """
    BFGS Quasi-Newton method.
    
    Updates Hessian approximation using gradient differences.
    """
    x = np.array(x0, dtype=float)
    n = len(x)
    
    # Initialize Hessian approximation as identity
    B = np.eye(n)
    
    grad = compute_gradient_numerical(f, x)
    history = {'x': [x.copy()], 'f': [f(x)], 'grad_norm': [np.linalg.norm(grad)]}
    
    for k in range(max_iter):
        if np.linalg.norm(grad) < tol:
            print(f"BFGS converged at iteration {k}")
            break
        
        # Search direction
        p = -np.linalg.solve(B, grad)
        
        # Line search (simple backtracking)
        alpha = 1.0
        c = 1e-4
        while f(x + alpha * p) > f(x) + c * alpha * grad @ p:
            alpha *= 0.5
            if alpha < 1e-10:
                break
        
        # Take step
        s = alpha * p
        x_new = x + s
        
        # Gradient at new point
        grad_new = compute_gradient_numerical(f, x_new)
        y = grad_new - grad
        
        # BFGS update (Sherman-Morrison formula)
        rho = 1.0 / (y @ s + 1e-10)
        
        I = np.eye(n)
        V = I - rho * np.outer(s, y)
        B = V @ B @ V.T + rho * np.outer(s, s)
        
        # Update state
        x = x_new
        grad = grad_new
        
        history['x'].append(x.copy())
        history['f'].append(f(x))
        history['grad_norm'].append(np.linalg.norm(grad))
    
    return x, history

# Compare BFGS with other methods
print("BFGS on Rosenbrock Function")
print("=" * 50)

x0 = [-1.5, 2.0]
x_bfgs, hist_bfgs = bfgs(rosenbrock, x0)
print(f"Solution: {x_bfgs.round(6)}")
print(f"Iterations: {len(hist_bfgs['f'])}")
print(f"Final f(x): {rosenbrock(x_bfgs):.2e}")
```

### BFGS Update Formula

The BFGS update maintains positive definiteness:

$$B_{k+1} = \left(I - \rho_k s_k y_k^T\right) B_k \left(I - \rho_k y_k s_k^T\right) + \rho_k s_k s_k^T$$

Where:
- $s_k = x_{k+1} - x_k$ (step)
- $y_k = \nabla f_{k+1} - \nabla f_k$ (gradient change)
- $\rho_k = 1 / (y_k^T s_k)$

---

## L-BFGS: Limited-Memory BFGS

### The Memory Problem

Standard BFGS stores full $n \times n$ matrix. For deep learning with millions of parameters, this is impossible.

**L-BFGS** stores only the last $m$ pairs $(s_k, y_k)$ and computes $H^{-1} g$ implicitly.

```python
class LBFGS:
    """
    L-BFGS: Limited-memory BFGS optimizer.
    
    Only stores m most recent (s, y) pairs.
    Memory: O(mn) instead of O(n²)
    """
    
    def __init__(self, m=10):
        self.m = m  # History size
        self.s_history = []  # Steps
        self.y_history = []  # Gradient differences
    
    def compute_direction(self, grad):
        """
        Two-loop recursion to compute H⁻¹ @ grad efficiently.
        """
        q = grad.copy()
        n = len(self.s_history)
        
        if n == 0:
            return -grad
        
        alphas = []
        
        # First loop (backward)
        for i in range(n - 1, -1, -1):
            s, y = self.s_history[i], self.y_history[i]
            rho = 1.0 / (y @ s + 1e-10)
            alpha = rho * (s @ q)
            alphas.insert(0, alpha)
            q = q - alpha * y
        
        # Initial Hessian approximation
        s_last, y_last = self.s_history[-1], self.y_history[-1]
        gamma = (s_last @ y_last) / (y_last @ y_last + 1e-10)
        r = gamma * q
        
        # Second loop (forward)
        for i in range(n):
            s, y = self.s_history[i], self.y_history[i]
            rho = 1.0 / (y @ s + 1e-10)
            beta = rho * (y @ r)
            r = r + (alphas[i] - beta) * s
        
        return -r
    
    def update(self, s, y):
        """Add new curvature information."""
        if len(self.s_history) >= self.m:
            self.s_history.pop(0)
            self.y_history.pop(0)
        self.s_history.append(s)
        self.y_history.append(y)


def lbfgs_optimize(f, x0, m=10, max_iter=100, tol=1e-8):
    """L-BFGS optimization."""
    x = np.array(x0, dtype=float)
    optimizer = LBFGS(m=m)
    
    grad = compute_gradient_numerical(f, x)
    history = {'x': [x.copy()], 'f': [f(x)]}
    
    for k in range(max_iter):
        if np.linalg.norm(grad) < tol:
            print(f"L-BFGS converged at iteration {k}")
            break
        
        # Get search direction from L-BFGS
        p = optimizer.compute_direction(grad)
        
        # Line search
        alpha = 1.0
        c = 1e-4
        while f(x + alpha * p) > f(x) + c * alpha * grad @ p:
            alpha *= 0.5
            if alpha < 1e-10:
                break
        
        # Take step
        s = alpha * p
        x_new = x + s
        
        grad_new = compute_gradient_numerical(f, x_new)
        y = grad_new - grad
        
        # Update L-BFGS history
        optimizer.update(s, y)
        
        x = x_new
        grad = grad_new
        
        history['x'].append(x.copy())
        history['f'].append(f(x))
    
    return x, history

# Test L-BFGS
print("\nL-BFGS on Rosenbrock Function")
print("=" * 50)

x_lbfgs, hist_lbfgs = lbfgs_optimize(rosenbrock, [-1.5, 2.0])
print(f"Solution: {x_lbfgs.round(6)}")
print(f"Iterations: {len(hist_lbfgs['f'])}")
```

### L-BFGS Complexity

| Operation | Complexity |
|-----------|------------|
| Storage | $O(mn)$ |
| Direction computation | $O(mn)$ |
| Gradient computation | $O(n)$ |
| Total per iteration | $O(mn)$ |

---

## Comparison: When to Use What?

### Decision Framework

```
                     Problem Size
                   Small (n < 1000)    Large (n > 10000)
                  ┌─────────────────┬─────────────────────┐
   Convex         │  Newton/BFGS    │    L-BFGS / SGD     │
                  │  (fast, exact)  │  (memory-efficient) │
                  ├─────────────────┼─────────────────────┤
   Non-convex     │  Trust Region   │    SGD / Adam       │
   (Deep Learning)│  (more robust)  │  (stochastic wins)  │
                  └─────────────────┴─────────────────────┘
```

### Benchmark Comparison

```python
import time

def benchmark_optimizers():
    """Compare optimizer performance on test functions."""
    
    test_functions = {
        'Quadratic (n=2)': (
            lambda x: 50*x[0]**2 + 0.5*x[1]**2,
            np.array([1.0, 1.0])
        ),
        'Rosenbrock': (
            rosenbrock,
            np.array([-1.5, 2.0])
        ),
    }
    
    optimizers = {
        'Gradient Descent': lambda f, x0: gradient_descent(f, x0, lr=0.01, max_iter=500),
        'Newton': lambda f, x0: damped_newton(f, x0, max_iter=100),
        'BFGS': lambda f, x0: bfgs(f, x0, max_iter=100),
        'L-BFGS (m=5)': lambda f, x0: lbfgs_optimize(f, x0, m=5, max_iter=100),
    }
    
    print("Optimizer Benchmark")
    print("=" * 70)
    
    for func_name, (f, x0) in test_functions.items():
        print(f"\n{func_name}:")
        print("-" * 50)
        
        for opt_name, opt in optimizers.items():
            try:
                start = time.time()
                x_opt, hist = opt(f, x0.copy())
                elapsed = time.time() - start
                
                print(f"  {opt_name:20s}: {len(hist['f']):3d} iters, "
                      f"f={hist['f'][-1]:.2e}, time={elapsed*1000:.1f}ms")
            except Exception as e:
                print(f"  {opt_name:20s}: FAILED - {e}")

benchmark_optimizers()
```

---

## Second-Order Methods in Deep Learning

### Why Not Newton for Deep Learning?

| Challenge | Impact |
|-----------|--------|
| Hessian size | $10^{12}$ elements for 1M parameters |
| Non-convexity | Saddle points, many local minima |
| Stochastic gradients | Noisy curvature estimates |
| Mini-batches | True Hessian unavailable |

### Practical Second-Order Methods for Deep Learning

#### 1. Natural Gradient Descent

Uses Fisher Information Matrix instead of Hessian:

```python
def natural_gradient_update(params, grad, fisher_matrix, lr=0.01):
    """Natural gradient: precondition by Fisher information."""
    # F⁻¹ @ grad
    natural_grad = np.linalg.solve(fisher_matrix + 1e-4 * np.eye(len(params)), grad)
    return params - lr * natural_grad
```

#### 2. K-FAC (Kronecker-Factored Approximate Curvature)

Approximates Fisher as Kronecker product: $F \approx A \otimes B$

```python
# K-FAC approximation (conceptual)
class KFAC:
    """
    K-FAC approximates layer-wise Fisher matrix as:
    F_l ≈ (A ⊗ G) where:
    - A = E[a @ a.T] (input activations)
    - G = E[g @ g.T] (output gradients)
    """
    def __init__(self, layer):
        self.A = None  # Input covariance
        self.G = None  # Gradient covariance
    
    def update_statistics(self, activations, gradients):
        """Update running estimates of A and G."""
        # Exponential moving average
        beta = 0.95
        A_new = activations.T @ activations / len(activations)
        G_new = gradients.T @ gradients / len(gradients)
        
        if self.A is None:
            self.A, self.G = A_new, G_new
        else:
            self.A = beta * self.A + (1 - beta) * A_new
            self.G = beta * self.G + (1 - beta) * G_new
    
    def precondition_gradient(self, grad, damping=1e-3):
        """Apply K-FAC preconditioning."""
        # Solve (A ⊗ G + λI) @ vec(grad) via Kronecker structure
        # Much cheaper than full Fisher
        A_inv = np.linalg.inv(self.A + damping * np.eye(self.A.shape[0]))
        G_inv = np.linalg.inv(self.G + damping * np.eye(self.G.shape[0]))
        return G_inv @ grad @ A_inv
```

#### 3. Shampoo Optimizer

```python
# Shampoo: Full-matrix preconditioning for neural networks
# Used in Google's large-scale training

class ShampooOptimizer:
    """
    Shampoo maintains separate preconditioners for each dimension.
    Better than diagonal (Adam) but cheaper than full second-order.
    """
    def __init__(self, lr=0.01, beta=0.9):
        self.lr = lr
        self.beta = beta
        self.L = {}  # Left preconditioners
        self.R = {}  # Right preconditioners
    
    def update(self, name, param, grad):
        # Initialize preconditioners
        if name not in self.L:
            m, n = grad.shape
            self.L[name] = np.eye(m)
            self.R[name] = np.eye(n)
        
        # Update preconditioners
        self.L[name] = self.beta * self.L[name] + (1 - self.beta) * grad @ grad.T
        self.R[name] = self.beta * self.R[name] + (1 - self.beta) * grad.T @ grad
        
        # Compute preconditioned update
        L_inv_sqrt = np.linalg.inv(np.linalg.cholesky(self.L[name] + 1e-6 * np.eye(self.L[name].shape[0])))
        R_inv_sqrt = np.linalg.inv(np.linalg.cholesky(self.R[name] + 1e-6 * np.eye(self.R[name].shape[0])))
        
        precond_grad = L_inv_sqrt @ grad @ R_inv_sqrt
        return param - self.lr * precond_grad
```

---

## Using SciPy's Optimizers

### Production-Ready Implementation

```python
from scipy.optimize import minimize

def scipy_optimization_demo():
    """Demonstrate scipy's second-order optimizers."""
    
    # Complex test function (Rastrigin)
    def rastrigin(x, A=10):
        n = len(x)
        return A * n + sum(xi**2 - A * np.cos(2 * np.pi * xi) for xi in x)
    
    x0 = np.array([5.0, 5.0])
    
    methods = ['Newton-CG', 'BFGS', 'L-BFGS-B', 'CG', 'Nelder-Mead']
    
    print("SciPy Optimizers on Rosenbrock")
    print("=" * 50)
    
    for method in methods:
        try:
            result = minimize(rosenbrock, x0, method=method, options={'maxiter': 200})
            print(f"{method:15s}: iters={result.nit:3d}, "
                  f"x={result.x.round(4)}, f={result.fun:.2e}, "
                  f"success={result.success}")
        except Exception as e:
            print(f"{method:15s}: FAILED")

scipy_optimization_demo()
```

### PyTorch L-BFGS

```python
# PyTorch's L-BFGS optimizer
import torch

def pytorch_lbfgs_example():
    """Using L-BFGS in PyTorch for neural network training."""
    
    # Simple neural network
    model = torch.nn.Sequential(
        torch.nn.Linear(10, 50),
        torch.nn.ReLU(),
        torch.nn.Linear(50, 1)
    )
    
    # Dummy data
    X = torch.randn(100, 10)
    y = torch.randn(100, 1)
    
    # L-BFGS optimizer
    optimizer = torch.optim.LBFGS(
        model.parameters(),
        lr=1.0,
        max_iter=20,
        history_size=10,
        line_search_fn='strong_wolfe'
    )
    
    criterion = torch.nn.MSELoss()
    
    # L-BFGS requires closure
    def closure():
        optimizer.zero_grad()
        output = model(X)
        loss = criterion(output, y)
        loss.backward()
        return loss
    
    # Training loop
    for epoch in range(10):
        loss = optimizer.step(closure)
        print(f"Epoch {epoch}: loss = {loss.item():.4f}")

# pytorch_lbfgs_example()  # Uncomment if PyTorch available
```

---

## FAQs

### When should I use Newton's method?

Use Newton when:
- Problem is small-medium scale (< 10,000 parameters)
- Function is smooth and convex
- High precision is needed
- Hessian is cheap to compute

### Why is L-BFGS popular for classical ML?

L-BFGS offers:
- Superlinear convergence (faster than GD)
- Low memory ($O(mn)$ instead of $O(n^2)$)
- No hyperparameter tuning (unlike learning rate)
- Works well for convex problems

### Can I use second-order methods for deep learning?

Generally, first-order methods (Adam, SGD) dominate because:
- Stochastic gradients make Hessian estimates noisy
- Memory constraints prohibit full Hessian
- Mini-batch training favors simple updates
- Non-convexity reduces second-order benefits

However, second-order methods are used in:
- Full-batch training on small datasets
- Natural gradient / K-FAC for large-scale training
- Fine-tuning pretrained models

---

## Key Takeaways

1. **Newton's method** uses Hessian for quadratic convergence but costly ($O(n^3)$)
2. **BFGS** approximates Hessian from gradient differences
3. **L-BFGS** uses limited memory—practical for medium-scale problems
4. **Second-order methods** excel for convex, smooth problems
5. **Deep learning** mostly uses first-order due to scale and stochasticity

---

## Next Steps

Continue your optimization mastery:

1. **[Convex Optimization](/docs/ai-ml/foundations/mathematics/calculus/convex-optimization-ml/)** - Theory foundations
2. **[Gradient Descent Optimizers](/docs/ai-ml/foundations/mathematics/calculus/gradient-descent-optimizers/)** - SGD, Adam family
3. **[Learning Rate Scheduling](/docs/ai-ml/foundations/mathematics/calculus/learning-rate-schedules/)** - When to use what

---

## References

1. Nocedal, J., Wright, S. "Numerical Optimization" (2006) - Chapters 6-7
2. Liu, D. C., Nocedal, J. "On the Limited Memory BFGS Method" (1989)
3. Martens, J. "Deep Learning via Hessian-Free Optimization" (2010)
4. Martens, J., Grosse, R. "Optimizing Neural Networks with K-FAC" (2015)

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
