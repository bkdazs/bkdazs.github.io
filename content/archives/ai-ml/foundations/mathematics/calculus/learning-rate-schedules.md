---
title: "Learning Rate Schedules: Warmup, Cosine Annealing, and One-Cycle Policy"
description: "Master learning rate scheduling for deep learning. Implement warmup, step decay, cosine annealing, and one-cycle policy with practical code examples and best practices."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 4
toc: true
categories: ["AI/ML", "Deep Learning"]
tags: ["learning rate", "scheduler", "cosine annealing", "warmup", "one-cycle", "deep learning", "training"]
series: ["Mathematics for ML"]
---

# Learning Rate Schedules: Warmup, Cosine Annealing, and One-Cycle Policy

**"The learning rate is the single most important hyperparameter."** — Yoshua Bengio

A fixed learning rate rarely produces the best results. Modern deep learning uses carefully designed learning rate schedules that adapt throughout training—starting high for fast progress, then decreasing for fine-grained optimization.

In this guide, you'll learn every major learning rate scheduling strategy with implementations and best practices.

---

## Why Do Learning Rate Schedules Matter?

### The Trade-off Problem

- **High LR**: Fast initial progress but can't converge to precise minimum
- **Low LR**: Precise convergence but painfully slow training
- **Solution**: Start high, decrease over time

```
Training Progress:
                    
Loss  ▲             
      │  ╲          High LR: Fast but oscillates
      │   ╲ ────────
      │    ╲        
      │     ╲       
      │      ╲ ─────  Scheduled: Fast AND precise
      │       ╲     
      │        ╲    
      │         ────  Low LR: Slow but precise
      └──────────────► Epochs
```

### Impact on Model Performance

| Approach | Training Time | Final Accuracy | Stability |
|----------|--------------|----------------|-----------|
| Fixed Low LR | Slow | Good | Stable |
| Fixed High LR | Fast | Poor | Unstable |
| Step Decay | Medium | Better | Stable |
| Cosine Annealing | Medium | Best | Stable |
| One-Cycle | Fast | Best | Very Stable |

---

## Step Decay: The Classic Approach

### How It Works

Reduce learning rate by a factor every N epochs:

$$\eta_t = \eta_0 \times \gamma^{\lfloor t / T \rfloor}$$

```python
import numpy as np
import matplotlib.pyplot as plt

class StepDecayScheduler:
    """Classic step decay learning rate scheduler."""
    
    def __init__(self, initial_lr, step_size, gamma=0.1):
        """
        Args:
            initial_lr: Starting learning rate
            step_size: Epochs between LR drops
            gamma: Multiplicative factor (0.1 = 10x reduction)
        """
        self.initial_lr = initial_lr
        self.step_size = step_size
        self.gamma = gamma
    
    def get_lr(self, epoch):
        """Get learning rate for given epoch."""
        return self.initial_lr * (self.gamma ** (epoch // self.step_size))
    
    def get_schedule(self, num_epochs):
        """Get full schedule."""
        return [self.get_lr(e) for e in range(num_epochs)]

# Example: Common ImageNet schedule
scheduler = StepDecayScheduler(initial_lr=0.1, step_size=30, gamma=0.1)
schedule = scheduler.get_schedule(100)

# Visualization
plt.figure(figsize=(10, 4))
plt.plot(schedule)
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Step Decay Schedule (ImageNet typical: 0.1 → 0.01 → 0.001)')
plt.grid(True)
plt.yscale('log')
plt.show()

print("Step Decay Schedule:")
for epoch in [0, 29, 30, 59, 60, 89]:
    print(f"  Epoch {epoch:2d}: LR = {scheduler.get_lr(epoch):.4f}")
```

### Multi-Step Decay

```python
class MultiStepScheduler:
    """Decay at specific milestones."""
    
    def __init__(self, initial_lr, milestones, gamma=0.1):
        self.initial_lr = initial_lr
        self.milestones = sorted(milestones)
        self.gamma = gamma
    
    def get_lr(self, epoch):
        lr = self.initial_lr
        for milestone in self.milestones:
            if epoch >= milestone:
                lr *= self.gamma
        return lr

# ResNet training schedule
scheduler = MultiStepScheduler(initial_lr=0.1, milestones=[30, 60, 80], gamma=0.1)
schedule = scheduler.get_schedule(100)
```

---

## Exponential Decay: Smooth Reduction

### Formula

$$\eta_t = \eta_0 \times \gamma^t$$

```python
class ExponentialDecayScheduler:
    """Exponential decay scheduler."""
    
    def __init__(self, initial_lr, gamma=0.95):
        self.initial_lr = initial_lr
        self.gamma = gamma
    
    def get_lr(self, epoch):
        return self.initial_lr * (self.gamma ** epoch)
    
    def get_schedule(self, num_epochs):
        return [self.get_lr(e) for e in range(num_epochs)]

# Fast decay
scheduler_fast = ExponentialDecayScheduler(initial_lr=0.1, gamma=0.9)
# Slow decay
scheduler_slow = ExponentialDecayScheduler(initial_lr=0.1, gamma=0.99)

plt.figure(figsize=(10, 4))
plt.plot(scheduler_fast.get_schedule(100), label='γ=0.9 (fast)')
plt.plot(scheduler_slow.get_schedule(100), label='γ=0.99 (slow)')
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Exponential Decay Schedules')
plt.legend()
plt.grid(True)
plt.show()
```

---

## Learning Rate Warmup: Start Slow, Ramp Up

### Why Warmup?

At the start of training:
- Weights are random (bad gradients)
- Batch normalization statistics are unstable
- Large LR can cause divergence

**Solution**: Start with small LR, gradually increase to target.

```python
class WarmupScheduler:
    """Linear warmup scheduler."""
    
    def __init__(self, target_lr, warmup_epochs):
        self.target_lr = target_lr
        self.warmup_epochs = warmup_epochs
    
    def get_lr(self, epoch):
        if epoch < self.warmup_epochs:
            # Linear warmup
            return self.target_lr * (epoch + 1) / self.warmup_epochs
        return self.target_lr
    
    def get_schedule(self, num_epochs):
        return [self.get_lr(e) for e in range(num_epochs)]

# Warmup then constant
scheduler = WarmupScheduler(target_lr=0.1, warmup_epochs=5)
schedule = scheduler.get_schedule(50)

plt.figure(figsize=(10, 4))
plt.plot(schedule)
plt.axvline(x=5, color='r', linestyle='--', label='Warmup ends')
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Linear Warmup Schedule')
plt.legend()
plt.grid(True)
plt.show()
```

### Warmup + Decay Combined

```python
class WarmupDecayScheduler:
    """Warmup followed by step decay."""
    
    def __init__(self, target_lr, warmup_epochs, decay_epochs, gamma=0.1):
        self.target_lr = target_lr
        self.warmup_epochs = warmup_epochs
        self.decay_epochs = decay_epochs
        self.gamma = gamma
    
    def get_lr(self, epoch):
        if epoch < self.warmup_epochs:
            # Linear warmup
            return self.target_lr * (epoch + 1) / self.warmup_epochs
        else:
            # Step decay after warmup
            decay_epoch = epoch - self.warmup_epochs
            return self.target_lr * (self.gamma ** (decay_epoch // self.decay_epochs))
    
    def get_schedule(self, num_epochs):
        return [self.get_lr(e) for e in range(num_epochs)]

scheduler = WarmupDecayScheduler(
    target_lr=0.1,
    warmup_epochs=5,
    decay_epochs=30,
    gamma=0.1
)
schedule = scheduler.get_schedule(100)

plt.figure(figsize=(10, 4))
plt.plot(schedule)
plt.axvline(x=5, color='r', linestyle='--', alpha=0.5, label='Warmup ends')
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Warmup + Step Decay')
plt.yscale('log')
plt.legend()
plt.grid(True)
plt.show()
```

---

## Cosine Annealing: Smooth Convergence

### How It Works

Learning rate follows cosine curve from initial to minimum:

$$\eta_t = \eta_{min} + \frac{1}{2}(\eta_{max} - \eta_{min})(1 + \cos(\frac{t \pi}{T}))$$

```python
import numpy as np

class CosineAnnealingScheduler:
    """Cosine annealing learning rate scheduler."""
    
    def __init__(self, initial_lr, min_lr=0, total_epochs=100):
        self.initial_lr = initial_lr
        self.min_lr = min_lr
        self.total_epochs = total_epochs
    
    def get_lr(self, epoch):
        return self.min_lr + 0.5 * (self.initial_lr - self.min_lr) * \
               (1 + np.cos(np.pi * epoch / self.total_epochs))
    
    def get_schedule(self, num_epochs=None):
        if num_epochs is None:
            num_epochs = self.total_epochs
        return [self.get_lr(e) for e in range(num_epochs)]

# Cosine annealing
scheduler = CosineAnnealingScheduler(initial_lr=0.1, min_lr=1e-6, total_epochs=100)
schedule = scheduler.get_schedule()

plt.figure(figsize=(10, 4))
plt.plot(schedule)
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Cosine Annealing Schedule')
plt.grid(True)
plt.show()
```

### Cosine Annealing with Warm Restarts (SGDR)

Reset learning rate periodically to escape local minima:

```python
class CosineAnnealingWarmRestarts:
    """Cosine annealing with warm restarts (SGDR)."""
    
    def __init__(self, initial_lr, T_0, T_mult=1, min_lr=0):
        """
        Args:
            initial_lr: Maximum learning rate
            T_0: Initial cycle length (epochs)
            T_mult: Cycle length multiplier
            min_lr: Minimum learning rate
        """
        self.initial_lr = initial_lr
        self.T_0 = T_0
        self.T_mult = T_mult
        self.min_lr = min_lr
    
    def get_lr(self, epoch):
        # Find which cycle we're in
        if self.T_mult == 1:
            cycle = epoch // self.T_0
            epoch_in_cycle = epoch % self.T_0
            T_cur = self.T_0
        else:
            # Geometric progression of cycle lengths
            cycle = 0
            accumulated = 0
            T_cur = self.T_0
            
            while accumulated + T_cur <= epoch:
                accumulated += T_cur
                T_cur = int(T_cur * self.T_mult)
                cycle += 1
            
            epoch_in_cycle = epoch - accumulated
        
        # Cosine annealing within cycle
        return self.min_lr + 0.5 * (self.initial_lr - self.min_lr) * \
               (1 + np.cos(np.pi * epoch_in_cycle / T_cur))
    
    def get_schedule(self, num_epochs):
        return [self.get_lr(e) for e in range(num_epochs)]

# SGDR with increasing cycle lengths
scheduler = CosineAnnealingWarmRestarts(
    initial_lr=0.1,
    T_0=10,      # First cycle: 10 epochs
    T_mult=2,    # Double each cycle
    min_lr=1e-6
)
schedule = scheduler.get_schedule(100)

plt.figure(figsize=(12, 4))
plt.plot(schedule)
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Cosine Annealing with Warm Restarts (T_0=10, T_mult=2)')
plt.grid(True)
plt.show()
```

---

## One-Cycle Policy: The Secret Weapon

### The Big Idea

Instead of monotonically decreasing, the one-cycle policy:
1. **Warmup**: Increase LR from low to high
2. **Annealing**: Decrease LR from high to very low

This allows finding wider, flatter minima that generalize better.

```python
class OneCycleScheduler:
    """One-cycle learning rate scheduler."""
    
    def __init__(self, max_lr, total_steps, pct_start=0.3, 
                 div_factor=25, final_div_factor=10000):
        """
        Args:
            max_lr: Maximum learning rate
            total_steps: Total training steps
            pct_start: Percentage of cycle spent increasing LR
            div_factor: Initial LR = max_lr / div_factor
            final_div_factor: Final LR = max_lr / final_div_factor
        """
        self.max_lr = max_lr
        self.total_steps = total_steps
        self.pct_start = pct_start
        self.initial_lr = max_lr / div_factor
        self.final_lr = max_lr / final_div_factor
        
        self.step_up = int(total_steps * pct_start)
        self.step_down = total_steps - self.step_up
    
    def get_lr(self, step):
        if step < self.step_up:
            # Linear warmup
            return self.initial_lr + (self.max_lr - self.initial_lr) * step / self.step_up
        else:
            # Cosine annealing
            step_down = step - self.step_up
            return self.final_lr + 0.5 * (self.max_lr - self.final_lr) * \
                   (1 + np.cos(np.pi * step_down / self.step_down))
    
    def get_schedule(self):
        return [self.get_lr(s) for s in range(self.total_steps)]

# One-cycle for 100 epochs
scheduler = OneCycleScheduler(
    max_lr=0.1,
    total_steps=100,
    pct_start=0.3,  # 30% warmup
    div_factor=25,  # Start at max_lr/25
    final_div_factor=10000  # End at max_lr/10000
)
schedule = scheduler.get_schedule()

plt.figure(figsize=(10, 4))
plt.plot(schedule)
plt.axvline(x=30, color='r', linestyle='--', label='Peak LR')
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('One-Cycle Policy')
plt.legend()
plt.grid(True)
plt.show()

print(f"Initial LR: {schedule[0]:.6f}")
print(f"Max LR (at 30%): {max(schedule):.6f}")
print(f"Final LR: {schedule[-1]:.6f}")
```

### One-Cycle with Momentum

The full one-cycle policy also cycles momentum inversely:

```python
class OneCycleFullScheduler:
    """One-cycle scheduler with momentum cycling."""
    
    def __init__(self, max_lr, total_steps, pct_start=0.3,
                 div_factor=25, final_div_factor=10000,
                 max_momentum=0.95, min_momentum=0.85):
        self.max_lr = max_lr
        self.total_steps = total_steps
        self.pct_start = pct_start
        self.initial_lr = max_lr / div_factor
        self.final_lr = max_lr / final_div_factor
        self.max_momentum = max_momentum
        self.min_momentum = min_momentum
        
        self.step_up = int(total_steps * pct_start)
        self.step_down = total_steps - self.step_up
    
    def get_lr(self, step):
        if step < self.step_up:
            return self.initial_lr + (self.max_lr - self.initial_lr) * step / self.step_up
        else:
            step_down = step - self.step_up
            return self.final_lr + 0.5 * (self.max_lr - self.final_lr) * \
                   (1 + np.cos(np.pi * step_down / self.step_down))
    
    def get_momentum(self, step):
        # Momentum cycles inversely to LR
        if step < self.step_up:
            return self.max_momentum - (self.max_momentum - self.min_momentum) * step / self.step_up
        else:
            step_down = step - self.step_up
            return self.min_momentum + 0.5 * (self.max_momentum - self.min_momentum) * \
                   (1 + np.cos(np.pi * step_down / self.step_down + np.pi))
    
    def get_schedules(self):
        lrs = [self.get_lr(s) for s in range(self.total_steps)]
        momentums = [self.get_momentum(s) for s in range(self.total_steps)]
        return lrs, momentums

# Visualize both LR and momentum
scheduler = OneCycleFullScheduler(max_lr=0.1, total_steps=100)
lrs, momentums = scheduler.get_schedules()

fig, axes = plt.subplots(1, 2, figsize=(14, 4))

axes[0].plot(lrs)
axes[0].set_xlabel('Step')
axes[0].set_ylabel('Learning Rate')
axes[0].set_title('One-Cycle: Learning Rate')
axes[0].grid(True)

axes[1].plot(momentums, color='orange')
axes[1].set_xlabel('Step')
axes[1].set_ylabel('Momentum')
axes[1].set_title('One-Cycle: Momentum (Inverse)')
axes[1].grid(True)

plt.tight_layout()
plt.show()
```

---

## Polynomial Decay

### Linear and Polynomial Schedules

```python
class PolynomialDecayScheduler:
    """Polynomial decay scheduler (power decay)."""
    
    def __init__(self, initial_lr, final_lr, total_epochs, power=1.0):
        """
        Args:
            initial_lr: Starting learning rate
            final_lr: Ending learning rate
            total_epochs: Total training epochs
            power: Decay power (1.0 = linear, 2.0 = quadratic)
        """
        self.initial_lr = initial_lr
        self.final_lr = final_lr
        self.total_epochs = total_epochs
        self.power = power
    
    def get_lr(self, epoch):
        decay = (1 - epoch / self.total_epochs) ** self.power
        return (self.initial_lr - self.final_lr) * decay + self.final_lr
    
    def get_schedule(self, num_epochs=None):
        if num_epochs is None:
            num_epochs = self.total_epochs
        return [self.get_lr(e) for e in range(num_epochs)]

# Compare different powers
plt.figure(figsize=(10, 5))
for power in [0.5, 1.0, 2.0, 3.0]:
    scheduler = PolynomialDecayScheduler(
        initial_lr=0.1, final_lr=1e-5, total_epochs=100, power=power
    )
    plt.plot(scheduler.get_schedule(), label=f'power={power}')

plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Polynomial Decay Schedules')
plt.legend()
plt.grid(True)
plt.yscale('log')
plt.show()
```

---

## Cyclical Learning Rates (CLR)

### Triangle Policy

```python
class CyclicalLRScheduler:
    """Cyclical learning rate scheduler."""
    
    def __init__(self, base_lr, max_lr, step_size, mode='triangular'):
        """
        Args:
            base_lr: Minimum LR
            max_lr: Maximum LR
            step_size: Half cycle length
            mode: 'triangular', 'triangular2', or 'exp_range'
        """
        self.base_lr = base_lr
        self.max_lr = max_lr
        self.step_size = step_size
        self.mode = mode
    
    def get_lr(self, step):
        cycle = np.floor(1 + step / (2 * self.step_size))
        x = np.abs(step / self.step_size - 2 * cycle + 1)
        
        if self.mode == 'triangular':
            scale = 1.0
        elif self.mode == 'triangular2':
            scale = 1 / (2 ** (cycle - 1))
        elif self.mode == 'exp_range':
            scale = 0.99994 ** step
        else:
            scale = 1.0
        
        return self.base_lr + (self.max_lr - self.base_lr) * max(0, (1 - x)) * scale
    
    def get_schedule(self, num_steps):
        return [self.get_lr(s) for s in range(num_steps)]

# Compare CLR modes
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

for ax, mode in zip(axes, ['triangular', 'triangular2', 'exp_range']):
    scheduler = CyclicalLRScheduler(
        base_lr=0.001, max_lr=0.1, step_size=50, mode=mode
    )
    schedule = scheduler.get_schedule(400)
    ax.plot(schedule)
    ax.set_xlabel('Step')
    ax.set_ylabel('Learning Rate')
    ax.set_title(f'CLR: {mode}')
    ax.grid(True)

plt.tight_layout()
plt.show()
```

---

## PyTorch Implementation

### Using Built-in Schedulers

```python
import torch
import torch.optim as optim
from torch.optim.lr_scheduler import (
    StepLR, ExponentialLR, CosineAnnealingLR, 
    CosineAnnealingWarmRestarts, OneCycleLR,
    LinearLR, SequentialLR
)

# Create model and optimizer
model = torch.nn.Linear(10, 2)
optimizer = optim.SGD(model.parameters(), lr=0.1, momentum=0.9)

# Step decay
scheduler_step = StepLR(optimizer, step_size=30, gamma=0.1)

# Exponential decay
scheduler_exp = ExponentialLR(optimizer, gamma=0.95)

# Cosine annealing
scheduler_cos = CosineAnnealingLR(optimizer, T_max=100, eta_min=1e-6)

# Cosine with warm restarts
scheduler_sgdr = CosineAnnealingWarmRestarts(optimizer, T_0=10, T_mult=2)

# One-cycle (specify total steps)
scheduler_onecycle = OneCycleLR(
    optimizer, 
    max_lr=0.1,
    total_steps=1000,  # epochs * batches_per_epoch
    pct_start=0.3,
    anneal_strategy='cos'
)

# Training loop with scheduler
def train_with_scheduler(model, optimizer, scheduler, num_epochs):
    for epoch in range(num_epochs):
        # Training loop here...
        for batch in range(10):  # Example batches
            # Forward, backward, optimize...
            
            # For OneCycleLR, step after each batch
            if isinstance(scheduler, OneCycleLR):
                scheduler.step()
        
        # For most schedulers, step after each epoch
        if not isinstance(scheduler, OneCycleLR):
            scheduler.step()
        
        current_lr = optimizer.param_groups[0]['lr']
        if epoch % 10 == 0:
            print(f"Epoch {epoch}: LR = {current_lr:.6f}")

# Linear warmup + cosine decay (combined)
def get_warmup_cosine_scheduler(optimizer, warmup_epochs, total_epochs, min_lr=1e-6):
    """Create warmup + cosine decay scheduler."""
    warmup = LinearLR(
        optimizer, 
        start_factor=0.01, 
        end_factor=1.0, 
        total_iters=warmup_epochs
    )
    cosine = CosineAnnealingLR(
        optimizer, 
        T_max=total_epochs - warmup_epochs, 
        eta_min=min_lr
    )
    return SequentialLR(
        optimizer, 
        schedulers=[warmup, cosine], 
        milestones=[warmup_epochs]
    )

# Usage
optimizer = optim.SGD(model.parameters(), lr=0.1)
scheduler = get_warmup_cosine_scheduler(optimizer, warmup_epochs=5, total_epochs=100)

# Visualize
lrs = []
for epoch in range(100):
    lrs.append(optimizer.param_groups[0]['lr'])
    scheduler.step()

plt.plot(lrs)
plt.xlabel('Epoch')
plt.ylabel('Learning Rate')
plt.title('Warmup + Cosine Decay')
plt.grid(True)
plt.show()
```

---

## Finding the Right Learning Rate

### Learning Rate Range Test

```python
def lr_range_test(model, train_loader, criterion, optimizer_class,
                  start_lr=1e-7, end_lr=10, num_steps=100):
    """
    Find good learning rate range.
    
    Steps:
    1. Train with exponentially increasing LR
    2. Track loss at each step
    3. Find LR where loss decreases fastest
    """
    import copy
    
    # Save initial state
    initial_state = copy.deepcopy(model.state_dict())
    
    # Create optimizer with low initial LR
    optimizer = optimizer_class(model.parameters(), lr=start_lr)
    
    # Calculate LR multiplier
    lr_mult = (end_lr / start_lr) ** (1 / num_steps)
    
    lrs = []
    losses = []
    lr = start_lr
    
    model.train()
    data_iter = iter(train_loader)
    
    for step in range(num_steps):
        try:
            batch_x, batch_y = next(data_iter)
        except StopIteration:
            data_iter = iter(train_loader)
            batch_x, batch_y = next(data_iter)
        
        # Forward pass
        optimizer.zero_grad()
        outputs = model(batch_x)
        loss = criterion(outputs, batch_y)
        
        # Record
        lrs.append(lr)
        losses.append(loss.item())
        
        # Backward pass
        loss.backward()
        optimizer.step()
        
        # Update learning rate
        lr *= lr_mult
        for param_group in optimizer.param_groups:
            param_group['lr'] = lr
        
        # Stop if loss explodes
        if loss.item() > 4 * min(losses):
            break
    
    # Restore model
    model.load_state_dict(initial_state)
    
    # Find suggested LR
    # Look for steepest decrease
    smoothed_losses = np.convolve(losses, np.ones(5)/5, mode='valid')
    gradients = np.gradient(smoothed_losses)
    suggested_idx = np.argmin(gradients)
    suggested_lr = lrs[suggested_idx]
    
    # Plot
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    axes[0].plot(lrs, losses)
    axes[0].set_xscale('log')
    axes[0].set_xlabel('Learning Rate')
    axes[0].set_ylabel('Loss')
    axes[0].set_title('LR Range Test')
    axes[0].axvline(x=suggested_lr, color='r', linestyle='--', label=f'Suggested: {suggested_lr:.2e}')
    axes[0].legend()
    axes[0].grid(True)
    
    axes[1].plot(lrs[:len(smoothed_losses)], gradients)
    axes[1].set_xscale('log')
    axes[1].set_xlabel('Learning Rate')
    axes[1].set_ylabel('Loss Gradient')
    axes[1].set_title('Loss Derivative')
    axes[1].axvline(x=suggested_lr, color='r', linestyle='--')
    axes[1].grid(True)
    
    plt.tight_layout()
    plt.show()
    
    print(f"\nSuggested max LR: {suggested_lr:.2e}")
    print(f"For one-cycle, use max_lr={suggested_lr:.2e} to {suggested_lr*2:.2e}")
    
    return lrs, losses, suggested_lr
```

---

## Best Practices and Recommendations

### Schedule Selection Guide

| Task | Recommended Schedule | Notes |
|------|---------------------|-------|
| **Image Classification** | Cosine or One-Cycle | One-cycle for faster training |
| **Object Detection** | Step Decay | Common for YOLO, Faster R-CNN |
| **NLP/Transformers** | Warmup + Linear Decay | Warmup is critical |
| **Fine-tuning** | Low constant or Cosine | Start with pretrained LR / 10 |
| **GAN Training** | Low constant | Stability more important than speed |
| **Reinforcement Learning** | Linear decay or constant | Task-dependent |

### Common Mistakes to Avoid

```python
# MISTAKE 1: Forgetting warmup for transformers
# Bad
optimizer = torch.optim.Adam(model.parameters(), lr=3e-4)
# Good
scheduler = get_warmup_cosine_scheduler(optimizer, warmup_epochs=5, total_epochs=100)

# MISTAKE 2: Wrong scheduler step location
# Bad: OneCycleLR stepped per epoch
for epoch in range(epochs):
    train_one_epoch(...)
    scheduler.step()  # Wrong for OneCycleLR!

# Good: OneCycleLR stepped per batch
for epoch in range(epochs):
    for batch in dataloader:
        train_step(batch)
        scheduler.step()  # Correct!

# MISTAKE 3: Not matching total_steps
# Bad
scheduler = OneCycleLR(optimizer, max_lr=0.1, total_steps=100)
# Training for 100 epochs * 1000 batches = 100000 steps!

# Good
total_steps = num_epochs * len(dataloader)
scheduler = OneCycleLR(optimizer, max_lr=0.1, total_steps=total_steps)

# MISTAKE 4: Resuming training without scheduler state
# Save
torch.save({
    'model': model.state_dict(),
    'optimizer': optimizer.state_dict(),
    'scheduler': scheduler.state_dict(),  # Don't forget this!
}, 'checkpoint.pt')

# Load
checkpoint = torch.load('checkpoint.pt')
model.load_state_dict(checkpoint['model'])
optimizer.load_state_dict(checkpoint['optimizer'])
scheduler.load_state_dict(checkpoint['scheduler'])
```

---

## FAQs

### How do I choose max_lr for one-cycle?

Use the **LR range test**. Find where loss is decreasing fastest, use that as max_lr. Typically 10x the "normal" learning rate you'd use for constant LR training.

### Should I use warmup for Adam?

**Yes**, especially for:
- Transformers (almost always needs warmup)
- Large batch sizes
- Training from scratch

### How long should warmup be?

- **Transformers**: 5-10% of total steps
- **CNN from scratch**: 3-5 epochs
- **Fine-tuning**: 1-2 epochs or skip

### Can I combine multiple schedulers?

Yes! Use `SequentialLR` or `ChainedScheduler` in PyTorch:

```python
from torch.optim.lr_scheduler import SequentialLR, LinearLR, CosineAnnealingLR

warmup = LinearLR(optimizer, start_factor=0.01, total_iters=5)
decay = CosineAnnealingLR(optimizer, T_max=95)
combined = SequentialLR(optimizer, [warmup, decay], milestones=[5])
```

---

## Key Takeaways

1. **Never use fixed learning rate** for serious training
2. **Warmup is essential** for transformers and large batch sizes
3. **One-cycle policy** provides fastest training with good generalization
4. **Cosine annealing** is a safe default for most tasks
5. **Use LR range test** to find optimal learning rate
6. **Match scheduler to optimizer**: Adam uses smaller LR than SGD

---

## Next Steps

Continue mastering training optimization:

1. **[Gradient Descent Optimizers](/docs/ai-ml/foundations/mathematics/calculus/gradient-descent-optimizers/)** - SGD, Adam, and variants
2. **[Batch Normalization](/docs/ai-ml/deep-learning/batch-normalization/)** - Stabilize training
3. **[Mixed Precision Training](/docs/ai-ml/deep-learning/mixed-precision-training/)** - Train faster with FP16

---

## References

1. Smith, L.N. "Cyclical Learning Rates for Training Neural Networks" (2017)
2. Smith, L.N., Topin, N. "Super-Convergence" (2019)
3. Loshchilov, I., Hutter, F. "SGDR: Stochastic Gradient Descent with Warm Restarts" (2016)
4. Goyal, P., et al. "Accurate, Large Minibatch SGD" (2017) - Facebook warmup paper

---

*Last updated: January 2024. This guide is part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
