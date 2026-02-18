---
title: "Gradient Descent Optimizers: SGD, Adam, RMSprop Complete Comparison"
description: "Master gradient descent optimizers with practical examples. Compare SGD, Momentum, Adam, RMSprop, and AdaGrad. Learn when to use each optimizer for optimal deep learning results."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 3
toc: true
categories: ["AI/ML", "Deep Learning"]
tags: ["gradient descent", "optimizers", "adam", "sgd", "rmsprop", "deep learning", "neural networks"]
series: ["Mathematics for ML"]
---

# Gradient Descent Optimizers: SGD, Adam, RMSprop Complete Comparison

**"The choice of optimizer can make or break your model's training."** — Sebastian Ruder, Research Scientist at Google

Choosing the right optimizer is one of the most impactful decisions in deep learning. The same model architecture can converge quickly or fail to train entirely based on your optimizer choice.

In this comprehensive guide, you'll understand every major optimizer, when to use each one, and how to tune their hyperparameters for optimal results.

---

## Why Do Optimizers Matter?

### The Optimization Problem

Training a neural network means finding parameters θ that minimize the loss:

$$\theta^* = \arg\min_\theta \mathcal{L}(\theta)$$

The challenge: modern networks have millions of parameters, and the loss landscape is highly non-convex with many local minima, saddle points, and flat regions.

### What Makes a Good Optimizer?

| Property | Importance |
|----------|------------|
| **Convergence speed** | Reach good solution quickly |
| **Stability** | Don't oscillate or diverge |
| **Generalization** | Find solutions that work on test data |
| **Memory efficiency** | Don't require too much RAM |
| **Hyperparameter sensitivity** | Work well without extensive tuning |

---

## How Does Vanilla Gradient Descent Work?

### The Basic Update Rule

$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \mathcal{L}(\theta_t)$$

Where:
- $\theta_t$ = parameters at step t
- $\eta$ = learning rate
- $\nabla_\theta \mathcal{L}$ = gradient of loss

```python
import numpy as np
import matplotlib.pyplot as plt

class VanillaGradientDescent:
    """Basic gradient descent optimizer."""
    
    def __init__(self, lr=0.01):
        self.lr = lr
    
    def update(self, params, grads):
        """Update parameters."""
        for i in range(len(params)):
            params[i] -= self.lr * grads[i]
        return params

# Demonstrate on simple 2D loss function
def rosenbrock(x, y):
    """Rosenbrock function - challenging optimization landscape."""
    return (1 - x)**2 + 100 * (y - x**2)**2

def rosenbrock_gradient(x, y):
    """Gradient of Rosenbrock function."""
    dx = -2 * (1 - x) - 400 * x * (y - x**2)
    dy = 200 * (y - x**2)
    return np.array([dx, dy])

def optimize_and_visualize(optimizer, start, n_steps=1000):
    """Run optimizer and return path."""
    path = [start.copy()]
    params = start.copy()
    
    for _ in range(n_steps):
        grads = rosenbrock_gradient(params[0], params[1])
        params = np.array(optimizer.update([params], [grads])[0])
        path.append(params.copy())
        
        # Early stopping if converged
        if np.linalg.norm(grads) < 1e-8:
            break
    
    return np.array(path)

# Test vanilla GD
start = np.array([-1.5, 2.0])
gd = VanillaGradientDescent(lr=0.001)
path_gd = optimize_and_visualize(gd, start, n_steps=5000)

print(f"Vanilla GD:")
print(f"  Start: {start}")
print(f"  End: {path_gd[-1]}")
print(f"  Steps: {len(path_gd)}")
print(f"  Final loss: {rosenbrock(path_gd[-1][0], path_gd[-1][1]):.6f}")
```

### Problems with Vanilla Gradient Descent

1. **Uniform learning rate**: Same rate for all parameters
2. **Sensitive to LR choice**: Too high = diverge, too low = slow
3. **Stuck in saddle points**: Zero gradient = no progress
4. **Oscillation in ravines**: Bounces between steep walls

---

## What Is Stochastic Gradient Descent (SGD)?

### From Batch to Stochastic

**Batch GD**: Uses all data to compute gradient
$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \mathcal{L}(\theta_t; X, y)$$

**Stochastic GD**: Uses one sample
$$\theta_{t+1} = \theta_t - \eta \nabla_\theta \mathcal{L}(\theta_t; x_i, y_i)$$

**Mini-batch GD**: Uses a batch of samples
$$\theta_{t+1} = \theta_t - \eta \frac{1}{B} \sum_{i=1}^{B} \nabla_\theta \mathcal{L}(\theta_t; x_i, y_i)$$

```python
class SGD:
    """Stochastic Gradient Descent optimizer."""
    
    def __init__(self, lr=0.01):
        self.lr = lr
    
    def update(self, params, grads):
        """
        Update parameters.
        
        params: list of parameter arrays
        grads: list of gradient arrays
        """
        updated_params = []
        for param, grad in zip(params, grads):
            updated_params.append(param - self.lr * grad)
        return updated_params

# Training loop with mini-batch SGD
def train_with_sgd(model, X, y, batch_size=32, epochs=100, lr=0.01):
    """Train model using mini-batch SGD."""
    optimizer = SGD(lr=lr)
    n_samples = len(X)
    losses = []
    
    for epoch in range(epochs):
        # Shuffle data
        indices = np.random.permutation(n_samples)
        X_shuffled = X[indices]
        y_shuffled = y[indices]
        
        epoch_loss = 0
        n_batches = 0
        
        for i in range(0, n_samples, batch_size):
            X_batch = X_shuffled[i:i+batch_size]
            y_batch = y_shuffled[i:i+batch_size]
            
            # Forward pass
            y_pred = model.forward(X_batch)
            loss = model.compute_loss(y_pred, y_batch)
            
            # Backward pass
            grads = model.backward()
            
            # Update
            model.params = optimizer.update(model.params, grads)
            
            epoch_loss += loss
            n_batches += 1
        
        losses.append(epoch_loss / n_batches)
    
    return losses
```

### Advantages of SGD

| Advantage | Explanation |
|-----------|-------------|
| Memory efficient | Only need one batch in memory |
| Faster iterations | Each update is cheap |
| Escapes local minima | Noise helps exploration |
| Online learning | Can learn from streaming data |

---

## How Does Momentum Improve SGD?

### The Physics Analogy

Think of a ball rolling down a hill. With momentum, the ball accumulates velocity and can roll through small bumps and flat regions.

$$v_t = \gamma v_{t-1} + \eta \nabla_\theta \mathcal{L}(\theta_t)$$
$$\theta_{t+1} = \theta_t - v_t$$

Where $\gamma$ (typically 0.9) is the momentum coefficient.

```python
class SGDMomentum:
    """SGD with momentum."""
    
    def __init__(self, lr=0.01, momentum=0.9):
        self.lr = lr
        self.momentum = momentum
        self.velocity = None
    
    def update(self, params, grads):
        """Update with momentum."""
        if self.velocity is None:
            self.velocity = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            self.velocity[i] = self.momentum * self.velocity[i] + self.lr * grad
            updated_params.append(param - self.velocity[i])
        
        return updated_params

# Nesterov Accelerated Gradient (NAG)
class SGDNesterov:
    """SGD with Nesterov momentum - look ahead before stepping."""
    
    def __init__(self, lr=0.01, momentum=0.9):
        self.lr = lr
        self.momentum = momentum
        self.velocity = None
    
    def update(self, params, grads):
        """
        Nesterov update: compute gradient at looked-ahead position.
        
        v_t = γ * v_{t-1} + η * ∇L(θ - γ * v_{t-1})
        θ_{t+1} = θ_t - v_t
        """
        if self.velocity is None:
            self.velocity = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            v_prev = self.velocity[i]
            self.velocity[i] = self.momentum * v_prev + self.lr * grad
            # Nesterov correction
            updated_params.append(param - self.momentum * v_prev - (1 + self.momentum) * self.lr * grad)
        
        return updated_params
```

### Visualizing Momentum Effect

```python
import numpy as np
import matplotlib.pyplot as plt

def compare_momentum(n_steps=100):
    """Compare SGD with and without momentum."""
    
    # Simple quadratic loss with different curvatures
    def loss_grad(x, y):
        # Loss = x² + 10*y² (steep in y direction)
        return np.array([2*x, 20*y])
    
    # SGD without momentum
    start = np.array([3.0, 3.0])
    path_sgd = [start.copy()]
    pos = start.copy()
    lr = 0.1
    
    for _ in range(n_steps):
        grad = loss_grad(pos[0], pos[1])
        pos = pos - lr * grad
        path_sgd.append(pos.copy())
    
    # SGD with momentum
    path_momentum = [start.copy()]
    pos = start.copy()
    velocity = np.zeros(2)
    momentum = 0.9
    
    for _ in range(n_steps):
        grad = loss_grad(pos[0], pos[1])
        velocity = momentum * velocity + lr * grad
        pos = pos - velocity
        path_momentum.append(pos.copy())
    
    path_sgd = np.array(path_sgd)
    path_momentum = np.array(path_momentum)
    
    # Plot
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Contour plot
    x = np.linspace(-4, 4, 100)
    y = np.linspace(-4, 4, 100)
    X, Y = np.meshgrid(x, y)
    Z = X**2 + 10*Y**2
    
    for ax, path, title in [(axes[0], path_sgd, 'SGD (no momentum)'),
                             (axes[1], path_momentum, 'SGD with Momentum')]:
        ax.contour(X, Y, Z, levels=20, cmap='viridis')
        ax.plot(path[:, 0], path[:, 1], 'r.-', markersize=3, linewidth=0.5)
        ax.plot(path[0, 0], path[0, 1], 'go', markersize=10, label='Start')
        ax.plot(path[-1, 0], path[-1, 1], 'r*', markersize=15, label='End')
        ax.set_xlabel('x')
        ax.set_ylabel('y')
        ax.set_title(f'{title}\n{len(path)} steps, final pos: ({path[-1,0]:.4f}, {path[-1,1]:.4f})')
        ax.legend()
        ax.set_xlim(-4, 4)
        ax.set_ylim(-4, 4)
    
    plt.tight_layout()
    plt.show()
    
    return path_sgd, path_momentum

paths = compare_momentum()
```

---

## What Makes AdaGrad Different?

### Adaptive Learning Rates

**Problem**: Some parameters (e.g., rare words in NLP) need larger updates than frequent ones.

**Solution**: AdaGrad adapts the learning rate for each parameter based on historical gradients.

$$G_t = G_{t-1} + g_t^2$$
$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{G_t + \epsilon}} g_t$$

```python
class AdaGrad:
    """AdaGrad optimizer - adaptive learning rates."""
    
    def __init__(self, lr=0.01, epsilon=1e-8):
        self.lr = lr
        self.epsilon = epsilon
        self.G = None  # Accumulated squared gradients
    
    def update(self, params, grads):
        """Update with adaptive learning rates."""
        if self.G is None:
            self.G = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            # Accumulate squared gradients
            self.G[i] += grad ** 2
            
            # Adaptive learning rate
            adjusted_lr = self.lr / (np.sqrt(self.G[i]) + self.epsilon)
            
            updated_params.append(param - adjusted_lr * grad)
        
        return updated_params
```

### AdaGrad Pros and Cons

| Pros | Cons |
|------|------|
| Good for sparse gradients | Learning rate decays to zero |
| No manual LR tuning per param | Can stop learning prematurely |
| Works well for NLP | Not ideal for non-convex |

---

## How Does RMSprop Fix AdaGrad's Issues?

### Exponential Moving Average

RMSprop uses an exponentially decaying average instead of sum:

$$E[g^2]_t = \rho E[g^2]_{t-1} + (1-\rho) g_t^2$$
$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{E[g^2]_t + \epsilon}} g_t$$

```python
class RMSprop:
    """RMSprop optimizer - fixes AdaGrad's decaying learning rate."""
    
    def __init__(self, lr=0.001, rho=0.9, epsilon=1e-8):
        self.lr = lr
        self.rho = rho  # Decay rate
        self.epsilon = epsilon
        self.E_g2 = None  # Running average of squared gradients
    
    def update(self, params, grads):
        """Update with RMSprop."""
        if self.E_g2 is None:
            self.E_g2 = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            # Update running average
            self.E_g2[i] = self.rho * self.E_g2[i] + (1 - self.rho) * grad ** 2
            
            # Adaptive update
            adjusted_lr = self.lr / (np.sqrt(self.E_g2[i]) + self.epsilon)
            
            updated_params.append(param - adjusted_lr * grad)
        
        return updated_params
```

### RMSprop in Practice

```python
# Typical RMSprop hyperparameters
rmsprop_config = {
    'lr': 0.001,      # Learning rate (smaller than SGD)
    'rho': 0.9,       # Decay rate (0.9 is almost always good)
    'epsilon': 1e-8   # Numerical stability
}

# RMSprop works well for:
# - RNNs and LSTMs
# - Non-stationary objectives
# - Online learning
```

---

## What Makes Adam the Most Popular Optimizer?

### Best of Both Worlds

**Adam** (Adaptive Moment Estimation) combines momentum AND adaptive learning rates:

$$m_t = \beta_1 m_{t-1} + (1-\beta_1) g_t$$ (momentum)
$$v_t = \beta_2 v_{t-1} + (1-\beta_2) g_t^2$$ (adaptive)

With bias correction:
$$\hat{m}_t = \frac{m_t}{1 - \beta_1^t}$$
$$\hat{v}_t = \frac{v_t}{1 - \beta_2^t}$$

Update:
$$\theta_{t+1} = \theta_t - \frac{\eta}{\sqrt{\hat{v}_t} + \epsilon} \hat{m}_t$$

```python
class Adam:
    """Adam optimizer - adaptive learning rates with momentum."""
    
    def __init__(self, lr=0.001, beta1=0.9, beta2=0.999, epsilon=1e-8):
        self.lr = lr
        self.beta1 = beta1  # Momentum decay
        self.beta2 = beta2  # RMSprop decay
        self.epsilon = epsilon
        self.m = None  # First moment (momentum)
        self.v = None  # Second moment (RMSprop)
        self.t = 0     # Time step for bias correction
    
    def update(self, params, grads):
        """Update with Adam."""
        self.t += 1
        
        if self.m is None:
            self.m = [np.zeros_like(p) for p in params]
            self.v = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            # Update biased first moment estimate
            self.m[i] = self.beta1 * self.m[i] + (1 - self.beta1) * grad
            
            # Update biased second moment estimate
            self.v[i] = self.beta2 * self.v[i] + (1 - self.beta2) * grad ** 2
            
            # Bias correction
            m_hat = self.m[i] / (1 - self.beta1 ** self.t)
            v_hat = self.v[i] / (1 - self.beta2 ** self.t)
            
            # Update
            updated_params.append(param - self.lr * m_hat / (np.sqrt(v_hat) + self.epsilon))
        
        return updated_params

# Default Adam hyperparameters (rarely need to change)
adam_config = {
    'lr': 0.001,       # Learning rate
    'beta1': 0.9,      # Momentum decay
    'beta2': 0.999,    # RMSprop decay  
    'epsilon': 1e-8    # Numerical stability
}
```

### Why Adam Works So Well

1. **Combines momentum and adaptivity**: Best of SGD+Momentum and RMSprop
2. **Bias correction**: Handles the cold start problem
3. **Works out of the box**: Default hyperparameters work for most cases
4. **Fast convergence**: Often reaches good solutions quickly

### Adam Variants

```python
class AdamW:
    """AdamW - Adam with decoupled weight decay (better regularization)."""
    
    def __init__(self, lr=0.001, beta1=0.9, beta2=0.999, epsilon=1e-8, weight_decay=0.01):
        self.lr = lr
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        self.weight_decay = weight_decay
        self.m = None
        self.v = None
        self.t = 0
    
    def update(self, params, grads):
        """Update with decoupled weight decay."""
        self.t += 1
        
        if self.m is None:
            self.m = [np.zeros_like(p) for p in params]
            self.v = [np.zeros_like(p) for p in params]
        
        updated_params = []
        for i, (param, grad) in enumerate(zip(params, grads)):
            # Update moments
            self.m[i] = self.beta1 * self.m[i] + (1 - self.beta1) * grad
            self.v[i] = self.beta2 * self.v[i] + (1 - self.beta2) * grad ** 2
            
            # Bias correction
            m_hat = self.m[i] / (1 - self.beta1 ** self.t)
            v_hat = self.v[i] / (1 - self.beta2 ** self.t)
            
            # Update with decoupled weight decay
            update = self.lr * m_hat / (np.sqrt(v_hat) + self.epsilon)
            update += self.lr * self.weight_decay * param  # Decoupled!
            
            updated_params.append(param - update)
        
        return updated_params
```

---

## Complete Optimizer Comparison

### Side-by-Side Comparison

```python
import numpy as np
import matplotlib.pyplot as plt

def compare_all_optimizers():
    """Compare all optimizers on Rosenbrock function."""
    
    def loss_and_grad(pos):
        x, y = pos
        loss = (1 - x)**2 + 100 * (y - x**2)**2
        dx = -2 * (1 - x) - 400 * x * (y - x**2)
        dy = 200 * (y - x**2)
        return loss, np.array([dx, dy])
    
    optimizers = {
        'SGD (lr=0.0001)': SGD(lr=0.0001),
        'SGD+Momentum': SGDMomentum(lr=0.0001, momentum=0.9),
        'AdaGrad': AdaGrad(lr=0.5),
        'RMSprop': RMSprop(lr=0.01),
        'Adam': Adam(lr=0.01)
    }
    
    n_steps = 5000
    start = np.array([-1.5, 2.0])
    
    results = {}
    
    for name, optimizer in optimizers.items():
        pos = start.copy()
        losses = []
        path = [pos.copy()]
        
        for _ in range(n_steps):
            loss, grad = loss_and_grad(pos)
            losses.append(loss)
            
            pos = np.array(optimizer.update([pos], [grad])[0])
            path.append(pos.copy())
            
            # Early stopping
            if loss < 1e-10:
                break
        
        results[name] = {
            'losses': losses,
            'path': np.array(path),
            'final_loss': losses[-1],
            'final_pos': pos
        }
    
    # Plot results
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    
    # Loss curves
    ax = axes[0, 0]
    for name, result in results.items():
        ax.semilogy(result['losses'], label=name)
    ax.set_xlabel('Step')
    ax.set_ylabel('Loss (log scale)')
    ax.set_title('Convergence Comparison')
    ax.legend()
    ax.grid(True)
    
    # Paths
    x = np.linspace(-2, 2, 100)
    y = np.linspace(-1, 3, 100)
    X, Y = np.meshgrid(x, y)
    Z = (1 - X)**2 + 100 * (Y - X**2)**2
    
    for idx, (name, result) in enumerate(results.items()):
        if idx < 5:
            ax = axes[(idx+1)//3, (idx+1)%3]
            ax.contour(X, Y, Z, levels=np.logspace(-1, 3, 20), cmap='viridis')
            path = result['path'][:500]  # First 500 steps
            ax.plot(path[:, 0], path[:, 1], 'r.-', markersize=1, linewidth=0.5)
            ax.plot(path[0, 0], path[0, 1], 'go', markersize=8)
            ax.plot(1, 1, 'r*', markersize=15)  # Optimum
            ax.set_title(f"{name}\nFinal loss: {result['final_loss']:.2e}")
            ax.set_xlim(-2, 2)
            ax.set_ylim(-1, 3)
    
    plt.tight_layout()
    plt.show()
    
    # Summary table
    print("\nOptimizer Comparison Summary")
    print("=" * 60)
    print(f"{'Optimizer':<20} {'Final Loss':<15} {'Final Position'}")
    print("-" * 60)
    for name, result in results.items():
        pos = result['final_pos']
        print(f"{name:<20} {result['final_loss']:<15.2e} ({pos[0]:.4f}, {pos[1]:.4f})")
    
    return results

results = compare_all_optimizers()
```

### Optimizer Selection Guide

| Scenario | Recommended Optimizer | Why |
|----------|----------------------|-----|
| **Default choice** | Adam | Works well in most cases |
| **Computer vision** | SGD + Momentum | Better generalization |
| **NLP / Transformers** | AdamW | Handles weight decay properly |
| **RNNs / LSTMs** | RMSprop | Handles non-stationary well |
| **Sparse gradients** | AdaGrad | Per-parameter learning rates |
| **Final fine-tuning** | SGD (low LR) | Often reaches lower final loss |
| **Limited memory** | SGD | Minimal overhead |

---

## Hyperparameter Tuning Guide

### Learning Rate

The most important hyperparameter across all optimizers:

```python
def learning_rate_finder(model, train_loader, start_lr=1e-7, end_lr=10, steps=100):
    """
    Find good learning rate range using LR finder technique.
    
    1. Train with exponentially increasing LR
    2. Plot loss vs LR
    3. Choose LR where loss is decreasing fastest
    """
    import copy
    
    # Save initial state
    initial_state = copy.deepcopy(model.state_dict())
    
    # Calculate LR multiplier
    lr_mult = (end_lr / start_lr) ** (1 / steps)
    
    lr = start_lr
    losses = []
    lrs = []
    
    optimizer = Adam(lr=lr)
    
    for step in range(steps):
        # Get batch
        batch_x, batch_y = next(iter(train_loader))
        
        # Forward and backward
        loss = train_step(model, batch_x, batch_y, optimizer)
        
        losses.append(loss)
        lrs.append(lr)
        
        # Increase learning rate
        lr *= lr_mult
        for param_group in optimizer.param_groups:
            param_group['lr'] = lr
        
        # Stop if loss explodes
        if loss > 4 * min(losses):
            break
    
    # Restore initial state
    model.load_state_dict(initial_state)
    
    # Plot
    plt.figure(figsize=(10, 5))
    plt.semilogx(lrs, losses)
    plt.xlabel('Learning Rate')
    plt.ylabel('Loss')
    plt.title('Learning Rate Finder')
    plt.grid(True)
    plt.show()
    
    # Suggest LR (where loss is decreasing fastest)
    gradients = np.gradient(losses)
    suggested_idx = np.argmin(gradients)
    suggested_lr = lrs[suggested_idx]
    
    print(f"Suggested learning rate: {suggested_lr:.2e}")
    return lrs, losses
```

### Recommended Hyperparameters by Optimizer

```python
# SGD
sgd_config = {
    'lr': 0.1,           # Start higher, use scheduler
    'momentum': 0.9,     # Almost always 0.9
    'weight_decay': 1e-4 # L2 regularization
}

# Adam
adam_config = {
    'lr': 0.001,         # Default, rarely needs change
    'betas': (0.9, 0.999),  # (β1, β2)
    'epsilon': 1e-8,     # Numerical stability
    'weight_decay': 0    # Use AdamW for regularization
}

# AdamW (preferred for transformers)
adamw_config = {
    'lr': 1e-4,          # Smaller for fine-tuning
    'betas': (0.9, 0.999),
    'epsilon': 1e-8,
    'weight_decay': 0.01 # Decoupled weight decay
}

# RMSprop
rmsprop_config = {
    'lr': 0.001,
    'alpha': 0.99,       # Decay rate (called rho in some implementations)
    'epsilon': 1e-8
}
```

---

## Real-World Training Example

### PyTorch Implementation

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, TensorDataset

# Create a simple classification model
class SimpleClassifier(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.ReLU(),
            nn.BatchNorm1d(hidden_dim),
            nn.Dropout(0.3),
            nn.Linear(hidden_dim, hidden_dim),
            nn.ReLU(),
            nn.BatchNorm1d(hidden_dim),
            nn.Dropout(0.3),
            nn.Linear(hidden_dim, output_dim)
        )
    
    def forward(self, x):
        return self.net(x)

# Training function with different optimizers
def train_and_compare(optimizers_config, model_class, X_train, y_train, X_val, y_val,
                      epochs=100, batch_size=32):
    """
    Train model with different optimizers and compare results.
    """
    # Create data loaders
    train_dataset = TensorDataset(torch.FloatTensor(X_train), torch.LongTensor(y_train))
    val_dataset = TensorDataset(torch.FloatTensor(X_val), torch.LongTensor(y_val))
    
    train_loader = DataLoader(train_dataset, batch_size=batch_size, shuffle=True)
    val_loader = DataLoader(val_dataset, batch_size=batch_size)
    
    results = {}
    
    for opt_name, opt_config in optimizers_config.items():
        print(f"\nTraining with {opt_name}...")
        
        # Create fresh model
        model = model_class(X_train.shape[1], 64, len(np.unique(y_train)))
        
        # Create optimizer
        optimizer = opt_config['class'](model.parameters(), **opt_config['params'])
        
        # Loss function
        criterion = nn.CrossEntropyLoss()
        
        # Training history
        train_losses = []
        val_accs = []
        
        for epoch in range(epochs):
            # Training
            model.train()
            epoch_loss = 0
            for batch_x, batch_y in train_loader:
                optimizer.zero_grad()
                outputs = model(batch_x)
                loss = criterion(outputs, batch_y)
                loss.backward()
                optimizer.step()
                epoch_loss += loss.item()
            
            train_losses.append(epoch_loss / len(train_loader))
            
            # Validation
            model.eval()
            correct = 0
            total = 0
            with torch.no_grad():
                for batch_x, batch_y in val_loader:
                    outputs = model(batch_x)
                    _, predicted = torch.max(outputs.data, 1)
                    total += batch_y.size(0)
                    correct += (predicted == batch_y).sum().item()
            
            val_accs.append(correct / total)
            
            if (epoch + 1) % 20 == 0:
                print(f"  Epoch {epoch+1}: Loss={train_losses[-1]:.4f}, Val Acc={val_accs[-1]:.2%}")
        
        results[opt_name] = {
            'train_losses': train_losses,
            'val_accs': val_accs,
            'final_acc': val_accs[-1]
        }
    
    return results

# Example usage
optimizers_to_test = {
    'SGD': {
        'class': optim.SGD,
        'params': {'lr': 0.1, 'momentum': 0.9}
    },
    'Adam': {
        'class': optim.Adam,
        'params': {'lr': 0.001}
    },
    'AdamW': {
        'class': optim.AdamW,
        'params': {'lr': 0.001, 'weight_decay': 0.01}
    },
    'RMSprop': {
        'class': optim.RMSprop,
        'params': {'lr': 0.001}
    }
}

# Generate synthetic data
np.random.seed(42)
X_train = np.random.randn(1000, 20)
y_train = (X_train[:, 0] + X_train[:, 1] > 0).astype(int)
X_val = np.random.randn(200, 20)
y_val = (X_val[:, 0] + X_val[:, 1] > 0).astype(int)

# results = train_and_compare(optimizers_to_test, SimpleClassifier, 
#                              X_train, y_train, X_val, y_val)
```

---

## FAQs

### Which optimizer should I use for my project?

**Start with Adam**. It works well out of the box for most problems. If you need better generalization (especially for computer vision), try **SGD with momentum**. For transformers and language models, use **AdamW**.

### Why does Adam sometimes generalize worse than SGD?

Adam can converge to sharper minima that generalize poorly. The adaptive learning rates can be too aggressive. Solutions:
- Use AdamW with proper weight decay
- Switch to SGD for final fine-tuning
- Use learning rate warmup

### How do I know if my learning rate is too high or too low?

- **Too high**: Loss oscillates wildly or increases
- **Too low**: Loss decreases very slowly
- **Just right**: Loss decreases smoothly then plateaus

Use a learning rate finder to identify the optimal range.

### Should I use weight decay with Adam?

Use **AdamW** instead of Adam + L2 regularization. In Adam, weight decay is coupled with the adaptive learning rate, reducing its effectiveness. AdamW decouples weight decay for better regularization.

---

## Key Takeaways

1. **SGD + Momentum** often gives best generalization for vision tasks
2. **Adam** is the best default choice for most problems
3. **AdamW** is preferred for transformers and when using weight decay
4. **RMSprop** works well for RNNs and online learning
5. **Learning rate** is the most important hyperparameter to tune
6. **Combine optimizers**: Use Adam to converge fast, then SGD to fine-tune

---

## Next Steps

Continue learning about training optimization:

1. **[Learning Rate Schedules](/docs/ai-ml/foundations/mathematics/calculus/learning-rate-schedules/)** - Warmup, cosine annealing, one-cycle
2. **[Backpropagation Explained](/docs/ai-ml/foundations/mathematics/calculus/backpropagation-explained/)** - How gradients are computed
3. **[Regularization Techniques](/docs/ai-ml/deep-learning/regularization-techniques/)** - Prevent overfitting

---

## References

1. Kingma, D., Ba, J. "Adam: A Method for Stochastic Optimization" (2014) - ICLR
2. Ruder, S. "An overview of gradient descent optimization algorithms" (2016)
3. Loshchilov, I., Hutter, F. "Decoupled Weight Decay Regularization" (2017) - AdamW
4. Smith, L.N. "Cyclical Learning Rates for Training Neural Networks" (2017)

---

*Last updated: January 2024. This guide is part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
