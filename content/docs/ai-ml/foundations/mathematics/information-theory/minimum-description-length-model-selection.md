---
title: "Minimum Description Length and Model Selection: Complete Guide 2025"
description: "Master Minimum Description Length (MDL) principle for model selection: understand Occam's razor, complexity regularization, and practical applications in machine learning."
date: 2025-01-26
lastmod: 2025-01-26
draft: false
weight: 3
categories: ["AI & ML", "Mathematics"]
tags: ["minimum description length", "model selection", "complexity regularization", "Occam's razor", "MDL principle"]
contributors: ["Quantitative Research Team"]
toc: true
math: true
---

The Minimum Description Length (MDL) principle provides a profound connection between data compression, statistical inference, and machine learning. It formalizes Occam's razor—the preference for simpler explanations—into a rigorous mathematical framework for model selection.

## Why Minimum Description Length Matters in Machine Learning

Consider the fundamental challenge of model selection: given data, how do you choose between a simple model that might underfit and a complex model that might overfit? MDL provides an elegant answer: choose the model that provides the shortest complete description of the data.

> "The best model is the one that compresses the data the most." — Jorma Rissanen, Founder of MDL Theory

### Real-World Impact

Indian tech companies and research institutions apply MDL principles to:
- **Flipkart:** Feature selection for recommendation systems
- **TCS Research:** Anomaly detection in time series
- **IIT Bombay:** Neural architecture search
- **Reliance Jio:** Network traffic pattern analysis

## Section 1: The Minimum Description Length Principle

### What Is MDL and How Does It Work?

The MDL principle states that the best hypothesis for data is the one that minimizes the total description length:

$$L(\text{total}) = L(\text{model}) + L(\text{data} | \text{model})$$

where:
- $L(\text{model})$ = bits needed to describe the model
- $L(\text{data} | \text{model})$ = bits needed to describe data given the model

**The Key Insight:** A model that fits the data perfectly but is very complex (high $L(\text{model})$) is not necessarily better than a simpler model with slightly worse fit.

### How Does MDL Relate to Kolmogorov Complexity?

Kolmogorov complexity $K(x)$ is the length of the shortest program that outputs $x$. While $K(x)$ is uncomputable, MDL provides a practical approximation:

| Concept | Definition | Computability |
|---------|-----------|---------------|
| Kolmogorov Complexity | Shortest program length | Uncomputable |
| MDL | Shortest description within model class | Computable |
| Shannon Entropy | Expected code length | Computable |

```python
import numpy as np
from scipy.stats import norm, poisson
from scipy.special import gammaln

def description_length_gaussian(data, mu, sigma):
    """
    Compute description length for data under Gaussian model.
    
    L(data|model) = -log p(data|mu, sigma)
    
    Args:
        data: Observed data points
        mu: Gaussian mean
        sigma: Gaussian standard deviation
    
    Returns:
        Description length in bits
    """
    n = len(data)
    
    # Negative log-likelihood (in bits)
    nll = 0.5 * n * np.log2(2 * np.pi * sigma**2)
    nll += np.sum((data - mu)**2) / (2 * sigma**2 * np.log(2))
    
    return nll

def description_length_polynomial(data, degree, x):
    """
    Compute description length for polynomial regression.
    
    Includes model complexity penalty.
    
    Args:
        data: (x, y) observed data
        degree: Polynomial degree
        x: Input values
    
    Returns:
        total_dl: Total description length
        model_dl: Model description length
        data_dl: Data description length given model
    """
    y = data
    
    # Fit polynomial
    coeffs = np.polyfit(x, y, degree)
    y_pred = np.polyval(coeffs, x)
    residuals = y - y_pred
    sigma = np.std(residuals) if np.std(residuals) > 0 else 1e-10
    
    # Model description length
    # Number of parameters * bits per parameter
    n_params = degree + 1
    bits_per_param = 32  # Assuming 32-bit float precision
    model_dl = n_params * bits_per_param
    
    # Data description length given model
    n = len(y)
    data_dl = description_length_gaussian(residuals, 0, sigma)
    
    # Add parameter encoding cost
    data_dl += n_params * np.log2(n) / 2  # Approximate MDL penalty
    
    total_dl = model_dl + data_dl
    
    return total_dl, model_dl, data_dl

# Example: Model selection using MDL
print("=== MDL for Polynomial Model Selection ===\n")

np.random.seed(42)

# Generate data from quadratic function
n = 100
x = np.linspace(-3, 3, n)
y_true = 0.5 * x**2 - x + 1  # True function is degree 2
y = y_true + np.random.randn(n) * 0.5  # Add noise

# Compare different polynomial degrees
print(f"{'Degree':<10} {'Total DL':<15} {'Model DL':<15} {'Data DL':<15}")
print("-" * 55)

best_degree = 0
best_dl = float('inf')

for degree in range(1, 10):
    total_dl, model_dl, data_dl = description_length_polynomial(y, degree, x)
    
    if total_dl < best_dl:
        best_dl = total_dl
        best_degree = degree
    
    print(f"{degree:<10} {total_dl:<15.2f} {model_dl:<15.2f} {data_dl:<15.2f}")

print("-" * 55)
print(f"\nBest model: degree {best_degree} (minimum description length)")
print(f"True model: degree 2")
```

## Section 2: Two-Part vs Prequential MDL

### What Are the Different MDL Approaches?

**Two-Part MDL (Crude MDL):**

$$L_{\text{two-part}}(x) = L(\hat{\theta}) + L(x | \hat{\theta})$$

Encode the model parameters $\hat{\theta}$ separately from the data.

**Prequential (Predictive) MDL:**

$$L_{\text{prequential}}(x_1, ..., x_n) = -\sum_{i=1}^{n} \log p(x_i | x_1, ..., x_{i-1})$$

No explicit model encoding—uses sequential predictions.

**Normalized Maximum Likelihood (NML):**

$$L_{\text{NML}}(x) = -\log p(x | \hat{\theta}(x)) + \log \sum_{y} p(y | \hat{\theta}(y))$$

The theoretically optimal MDL, but often intractable.

```python
def two_part_mdl(data, model_class, param_precision=32):
    """
    Compute two-part MDL code length.
    
    L = L(parameters) + L(data|parameters)
    
    Args:
        data: Observed data
        model_class: Model class with fit() and nll() methods
        param_precision: Bits per parameter
    
    Returns:
        Total description length
    """
    # Fit model to get MLE parameters
    params = model_class.fit(data)
    n_params = len(params)
    
    # Model description length
    model_dl = n_params * param_precision
    
    # Data description length given model
    data_dl = model_class.nll(data, params) / np.log(2)  # Convert nats to bits
    
    return model_dl + data_dl, model_dl, data_dl

def prequential_mdl(data, model_class, initial_data=10):
    """
    Compute prequential (predictive) MDL code length.
    
    L = -sum_{i=1}^{n} log p(x_i | x_1, ..., x_{i-1})
    
    Uses online learning to make predictions.
    
    Args:
        data: Observed data sequence
        model_class: Model with online_update() and predict_prob() methods
        initial_data: Number of initial samples before prediction
    
    Returns:
        Total description length in bits
    """
    n = len(data)
    total_dl = 0
    
    # Initialize with uniform code for initial data
    # (simplified—real implementation would use universal prior)
    total_dl += initial_data * 8  # Assume 8 bits per initial sample
    
    # Prequential coding for remaining data
    model = model_class()
    model.fit(data[:initial_data])
    
    for i in range(initial_data, n):
        # Predict probability of next observation
        prob = model.predict_prob(data[i])
        
        # Code length for this observation
        total_dl += -np.log2(prob + 1e-10)
        
        # Update model with new observation
        model.update(data[i])
    
    return total_dl

class OnlineGaussian:
    """
    Online Gaussian model for prequential MDL.
    """
    
    def __init__(self):
        self.n = 0
        self.mean = 0
        self.M2 = 0  # Sum of squared differences
    
    def fit(self, data):
        """Initialize from data."""
        self.n = len(data)
        self.mean = np.mean(data)
        self.M2 = np.sum((data - self.mean)**2)
    
    def update(self, x):
        """Welford's online algorithm for mean and variance."""
        self.n += 1
        delta = x - self.mean
        self.mean += delta / self.n
        delta2 = x - self.mean
        self.M2 += delta * delta2
    
    def predict_prob(self, x):
        """Predict probability of observation."""
        if self.n < 2:
            return 0.1  # Uninformative prior
        
        var = self.M2 / (self.n - 1)
        std = np.sqrt(var) if var > 0 else 1e-10
        
        # Gaussian probability
        return norm.pdf(x, self.mean, std)

print("=== Prequential MDL Example ===\n")

# Generate Gaussian data
np.random.seed(42)
data = np.random.randn(200) * 2 + 5  # N(5, 4)

# Compute prequential MDL
model = OnlineGaussian()
model.fit(data[:10])

total_dl = 10 * 8  # Initial samples
for i in range(10, len(data)):
    prob = model.predict_prob(data[i])
    total_dl += -np.log2(prob + 1e-10)
    model.update(data[i])

print(f"Prequential MDL: {total_dl:.2f} bits")
print(f"Average bits per sample: {total_dl / len(data):.2f}")
print(f"Theoretical entropy: {0.5 * np.log2(2 * np.pi * np.e * 4):.2f} bits")
```

## Section 3: MDL and Regularization

### How Does MDL Connect to Regularization?

MDL provides a principled justification for regularization in machine learning. The connection is:

$$\text{MDL} = -\log p(\text{data}|\theta) + \lambda \cdot \text{complexity}(\theta)$$

This is equivalent to regularized maximum likelihood!

| MDL Component | ML Equivalent | Effect |
|--------------|---------------|--------|
| $L(\text{data}\|\theta)$ | Negative log-likelihood | Fit data |
| $L(\theta)$ | Regularization term | Control complexity |
| Total MDL | Regularized loss | Balance fit and complexity |

```python
def mdl_regularization_equivalence():
    """
    Demonstrate the equivalence between MDL and regularized ML.
    """
    print("=== MDL-Regularization Equivalence ===\n")
    
    # Generate linear regression data
    np.random.seed(42)
    n, d = 100, 10
    
    # True model uses only first 3 features
    X = np.random.randn(n, d)
    true_weights = np.zeros(d)
    true_weights[:3] = [2, -1, 0.5]
    y = X @ true_weights + np.random.randn(n) * 0.5
    
    # Method 1: MDL-based feature selection
    def mdl_feature_selection(X, y):
        """Select features using MDL criterion."""
        n, d = X.shape
        
        best_features = []
        best_mdl = float('inf')
        
        # Greedy forward selection
        remaining = list(range(d))
        selected = []
        
        while remaining:
            best_next = None
            
            for f in remaining:
                test_features = selected + [f]
                X_sub = X[:, test_features]
                
                # Fit model
                coeffs = np.linalg.lstsq(X_sub, y, rcond=None)[0]
                residuals = y - X_sub @ coeffs
                sigma = np.std(residuals)
                
                # MDL: model complexity + data given model
                n_params = len(test_features) + 1  # +1 for variance
                model_dl = n_params * 32  # bits per parameter
                data_dl = n * 0.5 * np.log2(2 * np.pi * np.e * sigma**2)
                total_mdl = model_dl + data_dl
                
                if total_mdl < best_mdl:
                    best_mdl = total_mdl
                    best_next = f
            
            if best_next is not None:
                selected.append(best_next)
                remaining.remove(best_next)
            else:
                break
        
        return selected, best_mdl
    
    # Method 2: L1 regularization (Lasso)
    def lasso_feature_selection(X, y, alpha=0.1):
        """Select features using Lasso."""
        from sklearn.linear_model import Lasso
        
        lasso = Lasso(alpha=alpha)
        lasso.fit(X, y)
        
        selected = np.where(np.abs(lasso.coef_) > 1e-6)[0].tolist()
        return selected, lasso.coef_
    
    # Compare methods
    mdl_features, mdl_score = mdl_feature_selection(X, y)
    lasso_features, lasso_coefs = lasso_feature_selection(X, y)
    
    print("True non-zero features: [0, 1, 2]")
    print(f"MDL selected features: {sorted(mdl_features)}")
    print(f"Lasso selected features: {sorted(lasso_features)}")
    print(f"\nBoth methods identify sparse structure through complexity penalties!")

mdl_regularization_equivalence()
```

### What Is the Relationship Between MDL and BIC/AIC?

MDL is closely related to information criteria used for model selection:

**Bayesian Information Criterion (BIC):**
$$\text{BIC} = -2 \log L(\hat{\theta}) + k \log n$$

**Akaike Information Criterion (AIC):**
$$\text{AIC} = -2 \log L(\hat{\theta}) + 2k$$

**MDL (Stochastic Complexity):**
$$\text{MDL} \approx -\log L(\hat{\theta}) + \frac{k}{2} \log n + O(1)$$

```python
def compare_model_selection_criteria():
    """
    Compare AIC, BIC, and MDL for model selection.
    """
    print("=== Model Selection Criteria Comparison ===\n")
    
    np.random.seed(42)
    
    # Generate polynomial data
    n = 50
    x = np.linspace(-2, 2, n)
    y_true = 0.5 * x**3 - x**2 + 2*x - 1  # Cubic polynomial
    y = y_true + np.random.randn(n) * 0.5
    
    def fit_polynomial(x, y, degree):
        """Fit polynomial and return statistics."""
        coeffs = np.polyfit(x, y, degree)
        y_pred = np.polyval(coeffs, x)
        residuals = y - y_pred
        
        # Residual sum of squares
        rss = np.sum(residuals**2)
        
        # Maximum likelihood variance estimate
        sigma2 = rss / n
        
        # Log-likelihood (Gaussian)
        log_lik = -n/2 * np.log(2*np.pi) - n/2 * np.log(sigma2) - rss/(2*sigma2)
        
        return log_lik, degree + 1  # k = degree + 1 parameters
    
    print(f"{'Degree':<8} {'Log-Lik':<12} {'AIC':<12} {'BIC':<12} {'MDL':<12}")
    print("-" * 56)
    
    best_aic = (float('inf'), 0)
    best_bic = (float('inf'), 0)
    best_mdl = (float('inf'), 0)
    
    for degree in range(1, 10):
        log_lik, k = fit_polynomial(x, y, degree)
        
        # AIC: -2*log_lik + 2*k
        aic = -2 * log_lik + 2 * k
        
        # BIC: -2*log_lik + k*log(n)
        bic = -2 * log_lik + k * np.log(n)
        
        # MDL approximation: -log_lik + (k/2)*log(n)
        mdl = -log_lik + (k/2) * np.log(n)
        
        if aic < best_aic[0]:
            best_aic = (aic, degree)
        if bic < best_bic[0]:
            best_bic = (bic, degree)
        if mdl < best_mdl[0]:
            best_mdl = (mdl, degree)
        
        print(f"{degree:<8} {log_lik:<12.2f} {aic:<12.2f} {bic:<12.2f} {mdl:<12.2f}")
    
    print("-" * 56)
    print(f"\nTrue model: degree 3")
    print(f"AIC selects: degree {best_aic[1]}")
    print(f"BIC selects: degree {best_bic[1]}")
    print(f"MDL selects: degree {best_mdl[1]}")
    
    print(f"\nNote: BIC and MDL have stronger complexity penalty than AIC")
    print(f"      BIC/MDL penalty ~ (k/2)*log(n), AIC penalty ~ k")

compare_model_selection_criteria()
```

## Section 4: MDL for Neural Network Model Selection

### How Can MDL Guide Neural Architecture Search?

MDL provides a principled criterion for choosing neural network architectures:

```python
import torch
import torch.nn as nn

def neural_net_mdl(model, data_loader, bits_per_weight=32):
    """
    Compute MDL for a neural network.
    
    MDL = L(weights) + L(data|weights)
    
    Args:
        model: PyTorch model
        data_loader: Data loader
        bits_per_weight: Bits to encode each weight
    
    Returns:
        total_mdl: Total description length
        model_dl: Model (weights) description length
        data_dl: Data description length given model
    """
    # Count parameters
    n_params = sum(p.numel() for p in model.parameters())
    
    # Model description length
    model_dl = n_params * bits_per_weight
    
    # Data description length (negative log-likelihood)
    model.eval()
    total_nll = 0
    n_samples = 0
    
    with torch.no_grad():
        for x, y in data_loader:
            logits = model(x)
            nll = nn.functional.cross_entropy(logits, y, reduction='sum')
            total_nll += nll.item()
            n_samples += x.size(0)
    
    # Convert to bits
    data_dl = total_nll / np.log(2)
    
    total_mdl = model_dl + data_dl
    
    return total_mdl, model_dl, data_dl

class MDLArchitectureSearch:
    """
    Neural Architecture Search using MDL criterion.
    """
    
    def __init__(self, input_dim, output_dim, candidate_architectures):
        """
        Args:
            input_dim: Input feature dimension
            output_dim: Number of output classes
            candidate_architectures: List of hidden layer configurations
        """
        self.input_dim = input_dim
        self.output_dim = output_dim
        self.candidates = candidate_architectures
    
    def build_model(self, hidden_dims):
        """Build MLP with given hidden dimensions."""
        layers = []
        prev_dim = self.input_dim
        
        for h_dim in hidden_dims:
            layers.append(nn.Linear(prev_dim, h_dim))
            layers.append(nn.ReLU())
            prev_dim = h_dim
        
        layers.append(nn.Linear(prev_dim, self.output_dim))
        return nn.Sequential(*layers)
    
    def count_parameters(self, hidden_dims):
        """Count parameters for architecture."""
        model = self.build_model(hidden_dims)
        return sum(p.numel() for p in model.parameters())
    
    def train_and_evaluate(self, hidden_dims, train_loader, val_loader, epochs=10):
        """Train model and compute MDL."""
        model = self.build_model(hidden_dims)
        optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)
        
        # Training
        model.train()
        for epoch in range(epochs):
            for x, y in train_loader:
                optimizer.zero_grad()
                logits = model(x)
                loss = nn.functional.cross_entropy(logits, y)
                loss.backward()
                optimizer.step()
        
        # Compute MDL on validation set
        total_mdl, model_dl, data_dl = neural_net_mdl(model, val_loader)
        
        return total_mdl, model_dl, data_dl, model
    
    def search(self, train_loader, val_loader, epochs=10):
        """
        Search for best architecture using MDL.
        
        Returns:
            best_architecture: Hidden dimensions of best model
            best_model: Trained best model
            results: MDL for all candidates
        """
        results = []
        best_mdl = float('inf')
        best_arch = None
        best_model = None
        
        for hidden_dims in self.candidates:
            n_params = self.count_parameters(hidden_dims)
            total_mdl, model_dl, data_dl, model = self.train_and_evaluate(
                hidden_dims, train_loader, val_loader, epochs
            )
            
            results.append({
                'architecture': hidden_dims,
                'n_params': n_params,
                'total_mdl': total_mdl,
                'model_dl': model_dl,
                'data_dl': data_dl
            })
            
            if total_mdl < best_mdl:
                best_mdl = total_mdl
                best_arch = hidden_dims
                best_model = model
        
        return best_arch, best_model, results

# Example architecture search
print("=== MDL-based Neural Architecture Search ===\n")

# Define candidate architectures
candidates = [
    [32],
    [64],
    [128],
    [64, 32],
    [128, 64],
    [256, 128],
    [128, 64, 32],
    [256, 128, 64],
]

search = MDLArchitectureSearch(
    input_dim=784,
    output_dim=10,
    candidate_architectures=candidates
)

# Show parameter counts
print(f"{'Architecture':<20} {'Parameters':<15}")
print("-" * 35)
for arch in candidates:
    n_params = search.count_parameters(arch)
    print(f"{str(arch):<20} {n_params:<15,}")

print("\nNote: Larger architectures have higher model_dl but potentially lower data_dl")
print("MDL balances this tradeoff to find optimal complexity")
```

### How Does Weight Pruning Relate to MDL?

Weight pruning can be viewed through the MDL lens—removing weights reduces $L(\text{model})$ at the cost of potentially increasing $L(\text{data}|\text{model})$:

```python
class MDLPruning:
    """
    Prune neural network weights using MDL criterion.
    """
    
    def __init__(self, model, val_loader, bits_per_weight=32):
        self.model = model
        self.val_loader = val_loader
        self.bits_per_weight = bits_per_weight
        
        # Store original MDL
        self.original_mdl, _, _ = neural_net_mdl(
            model, val_loader, bits_per_weight
        )
    
    def get_weight_importance(self):
        """
        Compute importance of each weight using magnitude.
        
        Returns dictionary mapping parameter name to importance scores.
        """
        importance = {}
        
        for name, param in self.model.named_parameters():
            if 'weight' in name:
                importance[name] = torch.abs(param.data).flatten()
        
        return importance
    
    def prune_by_magnitude(self, sparsity):
        """
        Prune weights by magnitude.
        
        Args:
            sparsity: Fraction of weights to set to zero
        
        Returns:
            Pruned model (modifies in place)
        """
        # Collect all weight magnitudes
        all_magnitudes = []
        for name, param in self.model.named_parameters():
            if 'weight' in name:
                all_magnitudes.append(torch.abs(param.data).flatten())
        
        all_magnitudes = torch.cat(all_magnitudes)
        
        # Find threshold
        threshold = torch.quantile(all_magnitudes, sparsity)
        
        # Prune weights below threshold
        with torch.no_grad():
            for name, param in self.model.named_parameters():
                if 'weight' in name:
                    mask = torch.abs(param.data) > threshold
                    param.data *= mask.float()
        
        return self.model
    
    def find_optimal_sparsity(self, sparsity_levels):
        """
        Find optimal sparsity level using MDL.
        
        Tests different sparsity levels and selects the one
        with minimum MDL.
        
        Args:
            sparsity_levels: List of sparsity values to test
        
        Returns:
            best_sparsity: Optimal sparsity level
            results: MDL for each sparsity level
        """
        results = []
        best_mdl = self.original_mdl
        best_sparsity = 0
        
        # Save original weights
        original_state = {k: v.clone() for k, v in self.model.state_dict().items()}
        
        for sparsity in sparsity_levels:
            # Restore original weights
            self.model.load_state_dict(original_state)
            
            # Prune
            self.prune_by_magnitude(sparsity)
            
            # Compute MDL
            total_mdl, model_dl, data_dl = neural_net_mdl(
                self.model, self.val_loader, self.bits_per_weight
            )
            
            # Adjust model_dl for sparsity
            # Zero weights don't need to be encoded
            n_nonzero = sum(
                (p != 0).sum().item() 
                for p in self.model.parameters()
            )
            adjusted_model_dl = n_nonzero * self.bits_per_weight
            adjusted_mdl = adjusted_model_dl + data_dl
            
            results.append({
                'sparsity': sparsity,
                'n_nonzero': n_nonzero,
                'model_dl': adjusted_model_dl,
                'data_dl': data_dl,
                'total_mdl': adjusted_mdl
            })
            
            if adjusted_mdl < best_mdl:
                best_mdl = adjusted_mdl
                best_sparsity = sparsity
        
        # Restore and apply best sparsity
        self.model.load_state_dict(original_state)
        self.prune_by_magnitude(best_sparsity)
        
        return best_sparsity, results

print("=== MDL-based Weight Pruning ===\n")

# Create example model
model = nn.Sequential(
    nn.Linear(784, 256),
    nn.ReLU(),
    nn.Linear(256, 128),
    nn.ReLU(),
    nn.Linear(128, 10)
)

n_params = sum(p.numel() for p in model.parameters())
print(f"Original model parameters: {n_params:,}")

# Demonstrate pruning concept
sparsity_levels = [0, 0.3, 0.5, 0.7, 0.9]

print(f"\n{'Sparsity':<12} {'Non-zero':<15} {'% Remaining':<15}")
print("-" * 42)

for sparsity in sparsity_levels:
    remaining = int(n_params * (1 - sparsity))
    print(f"{sparsity:<12.1%} {remaining:<15,} {(1-sparsity):<15.1%}")

print("\nMDL-based pruning finds optimal sparsity that minimizes:")
print("  model_dl (proportional to non-zero weights) + data_dl (negative log-likelihood)")
```

## Section 5: MDL for Anomaly Detection

### How Can MDL Detect Anomalies?

Anomalies are data points that require disproportionately long descriptions. MDL provides a natural criterion:

```python
class MDLAnomalyDetector:
    """
    Detect anomalies using Minimum Description Length.
    
    Anomalies are points that require unusually long code lengths.
    """
    
    def __init__(self, model_type='gaussian'):
        """
        Args:
            model_type: 'gaussian' or 'mixture'
        """
        self.model_type = model_type
        self.params = None
    
    def fit(self, X):
        """Fit model to training data."""
        if self.model_type == 'gaussian':
            self.params = {
                'mean': np.mean(X, axis=0),
                'cov': np.cov(X.T) if X.ndim > 1 else np.var(X)
            }
        elif self.model_type == 'mixture':
            # Simple 2-component GMM
            from sklearn.mixture import GaussianMixture
            gmm = GaussianMixture(n_components=2, random_state=42)
            gmm.fit(X)
            self.params = {'gmm': gmm}
    
    def code_length(self, x):
        """
        Compute code length for individual point.
        
        L(x) = -log p(x|model)
        """
        if self.model_type == 'gaussian':
            mean = self.params['mean']
            cov = self.params['cov']
            
            if np.ndim(cov) == 0:
                # 1D case
                diff = x - mean
                log_prob = -0.5 * np.log(2 * np.pi * cov) - 0.5 * diff**2 / cov
            else:
                # Multi-dimensional
                from scipy.stats import multivariate_normal
                log_prob = multivariate_normal.logpdf(x, mean, cov)
            
            return -log_prob / np.log(2)  # Convert to bits
        
        elif self.model_type == 'mixture':
            gmm = self.params['gmm']
            log_prob = gmm.score_samples(x.reshape(1, -1))[0]
            return -log_prob / np.log(2)
    
    def detect(self, X, threshold_percentile=95):
        """
        Detect anomalies based on code length.
        
        Args:
            X: Data to test
            threshold_percentile: Percentile for anomaly threshold
        
        Returns:
            anomaly_scores: Code length for each point
            is_anomaly: Boolean mask of anomalies
        """
        if X.ndim == 1:
            X = X.reshape(-1, 1)
        
        # Compute code lengths
        scores = np.array([self.code_length(x) for x in X])
        
        # Set threshold
        threshold = np.percentile(scores, threshold_percentile)
        
        # Detect anomalies
        is_anomaly = scores > threshold
        
        return scores, is_anomaly, threshold

# Example: Anomaly detection
print("=== MDL Anomaly Detection ===\n")

np.random.seed(42)

# Normal data
normal_data = np.random.randn(100, 2) * 1.5 + np.array([5, 5])

# Anomalies
anomalies = np.array([
    [5, 12],    # Far from cluster
    [-2, 5],    # Far from cluster
    [12, 5],    # Far from cluster
])

# Combine
X_train = normal_data
X_test = np.vstack([normal_data[:20], anomalies])

# Fit detector
detector = MDLAnomalyDetector(model_type='gaussian')
detector.fit(X_train)

# Detect
scores, is_anomaly, threshold = detector.detect(X_test, threshold_percentile=90)

print(f"Threshold code length: {threshold:.2f} bits")
print(f"Detected anomalies: {is_anomaly.sum()}")

# Show scores for known anomalies
print("\nCode lengths for test points:")
print(f"{'Type':<15} {'Code Length':<15} {'Anomaly?':<10}")
print("-" * 40)

for i in range(20, 23):
    print(f"{'Injected':<15} {scores[i]:<15.2f} {'Yes' if is_anomaly[i] else 'No':<10}")
```

## Section 6: MDL for Time Series and Sequences

### How Does MDL Apply to Sequential Data?

For time series, MDL helps select the optimal model order and detect change points:

```python
class MDLTimeSeriesAnalysis:
    """
    MDL-based time series analysis.
    
    Applications:
    - AR model order selection
    - Change point detection
    - Segmentation
    """
    
    @staticmethod
    def ar_model_selection(data, max_order=10):
        """
        Select AR model order using MDL.
        
        Args:
            data: Time series data
            max_order: Maximum AR order to consider
        
        Returns:
            best_order: Optimal AR order
            mdl_scores: MDL for each order
        """
        n = len(data)
        mdl_scores = []
        
        for p in range(1, max_order + 1):
            # Build design matrix
            X = np.column_stack([
                data[i:n-p+i] for i in range(p)
            ])
            y = data[p:]
            
            # Fit AR model (OLS)
            coeffs = np.linalg.lstsq(X, y, rcond=None)[0]
            residuals = y - X @ coeffs
            sigma2 = np.var(residuals)
            
            # MDL components
            # Model: p coefficients + variance
            model_dl = (p + 1) * 32
            
            # Data given model
            n_obs = len(y)
            data_dl = n_obs * 0.5 * np.log2(2 * np.pi * np.e * sigma2)
            data_dl += (p / 2) * np.log2(n_obs)  # MDL penalty
            
            total_mdl = model_dl + data_dl
            mdl_scores.append(total_mdl)
        
        best_order = np.argmin(mdl_scores) + 1
        
        return best_order, mdl_scores
    
    @staticmethod
    def change_point_detection(data, min_segment=10):
        """
        Detect change points using MDL.
        
        A change point is where a different model fits better.
        
        Args:
            data: Time series data
            min_segment: Minimum segment length
        
        Returns:
            change_points: List of detected change points
            segments: Segment boundaries
        """
        n = len(data)
        
        def segment_mdl(segment):
            """MDL for a single segment with Gaussian model."""
            if len(segment) < 2:
                return float('inf')
            
            # Fit Gaussian
            mean = np.mean(segment)
            var = np.var(segment) if np.var(segment) > 0 else 1e-10
            
            # Model: mean + variance
            model_dl = 2 * 32
            
            # Data
            data_dl = len(segment) * 0.5 * np.log2(2 * np.pi * np.e * var)
            
            return model_dl + data_dl
        
        # Greedy change point detection
        change_points = []
        
        def find_best_split(start, end):
            """Find best split point in range."""
            if end - start < 2 * min_segment:
                return None, float('inf')
            
            # MDL without split
            no_split_mdl = segment_mdl(data[start:end])
            
            best_point = None
            best_improvement = 0
            
            for cp in range(start + min_segment, end - min_segment):
                # MDL with split
                split_mdl = segment_mdl(data[start:cp]) + segment_mdl(data[cp:end])
                
                improvement = no_split_mdl - split_mdl
                
                if improvement > best_improvement:
                    best_improvement = improvement
                    best_point = cp
            
            return best_point, best_improvement
        
        # Binary segmentation
        segments = [(0, n)]
        
        while True:
            best_overall_point = None
            best_overall_improvement = 0
            best_segment_idx = None
            
            for idx, (start, end) in enumerate(segments):
                cp, improvement = find_best_split(start, end)
                
                if improvement > best_overall_improvement:
                    best_overall_improvement = improvement
                    best_overall_point = cp
                    best_segment_idx = idx
            
            if best_overall_point is None or best_overall_improvement < 100:  # threshold
                break
            
            # Split segment
            start, end = segments[best_segment_idx]
            segments[best_segment_idx] = (start, best_overall_point)
            segments.insert(best_segment_idx + 1, (best_overall_point, end))
            change_points.append(best_overall_point)
        
        return sorted(change_points), segments

# Example: Time series analysis
print("=== MDL Time Series Analysis ===\n")

np.random.seed(42)

# Generate AR(2) process
n = 200
ar_coeffs = [0.5, -0.3]
noise = np.random.randn(n)
ts = np.zeros(n)
ts[:2] = noise[:2]

for t in range(2, n):
    ts[t] = ar_coeffs[0] * ts[t-1] + ar_coeffs[1] * ts[t-2] + noise[t]

# Select AR order using MDL
best_order, mdl_scores = MDLTimeSeriesAnalysis.ar_model_selection(ts, max_order=8)

print("AR Model Order Selection:")
print(f"True order: 2")
print(f"MDL selected order: {best_order}")
print(f"\n{'Order':<8} {'MDL Score':<15}")
print("-" * 23)
for i, score in enumerate(mdl_scores, 1):
    marker = " *" if i == best_order else ""
    print(f"{i:<8} {score:<15.2f}{marker}")

# Change point detection
print("\n" + "="*50)
print("\nChange Point Detection:")

# Generate data with change points
segment1 = np.random.randn(50) * 1.0 + 0
segment2 = np.random.randn(50) * 1.5 + 3
segment3 = np.random.randn(50) * 0.8 - 2

ts_with_changes = np.concatenate([segment1, segment2, segment3])

change_points, segments = MDLTimeSeriesAnalysis.change_point_detection(
    ts_with_changes, min_segment=15
)

print(f"True change points: [50, 100]")
print(f"Detected change points: {change_points}")
```

## Section 7: Advanced MDL Topics

### What Is Normalized Maximum Likelihood (NML)?

NML provides the theoretically optimal MDL code, but is often intractable:

$$p_{NML}(x^n) = \frac{p(x^n | \hat{\theta}(x^n))}{\sum_{y^n} p(y^n | \hat{\theta}(y^n))}$$

The denominator (stochastic complexity) is the key challenge.

```python
def compute_nml_simple(data, model_class):
    """
    Compute NML code length for simple cases.
    
    This is typically intractable for complex models.
    
    Args:
        data: Observed data
        model_class: Model with MLE fitting
    
    Returns:
        nml_code_length: NML description length
    """
    # MLE for observed data
    theta_hat = model_class.fit(data)
    log_prob_data = model_class.log_prob(data, theta_hat)
    
    # For tractable cases, we can approximate the normalizer
    # Here we show the concept
    
    # NML = -log p(data|theta_hat) + log(COMP)
    # where COMP = sum over all possible data of p(data'|theta_hat(data'))
    
    return -log_prob_data  # Simplified (ignoring normalizer)

class BernoulliNML:
    """
    NML for Bernoulli model (tractable case).
    """
    
    @staticmethod
    def stochastic_complexity(n):
        """
        Compute stochastic complexity for n Bernoulli trials.
        
        COMP = sum_{k=0}^{n} C(n,k) * (k/n)^k * ((n-k)/n)^(n-k)
        """
        from scipy.special import comb
        
        comp = 0
        for k in range(n + 1):
            if k == 0:
                term = 1  # 0^0 = 1
            elif k == n:
                term = 1
            else:
                p_mle = k / n
                term = comb(n, k) * (p_mle ** k) * ((1 - p_mle) ** (n - k))
            comp += term
        
        return comp
    
    @staticmethod
    def nml_code_length(data):
        """
        Compute NML code length for binary data.
        
        Args:
            data: Binary array
        
        Returns:
            NML code length in bits
        """
        n = len(data)
        k = sum(data)  # Number of 1s
        
        # MLE
        p_mle = k / n if n > 0 else 0.5
        
        # Log-likelihood at MLE
        if k == 0:
            log_lik = n * np.log(1 - p_mle + 1e-10)
        elif k == n:
            log_lik = n * np.log(p_mle + 1e-10)
        else:
            log_lik = k * np.log(p_mle) + (n - k) * np.log(1 - p_mle)
        
        # Stochastic complexity
        comp = BernoulliNML.stochastic_complexity(n)
        
        # NML code length
        nml = -log_lik / np.log(2) + np.log2(comp)
        
        return nml

# Example: NML for Bernoulli
print("=== Normalized Maximum Likelihood ===\n")

# Compare NML for different data
test_cases = [
    np.array([1, 1, 1, 1, 1, 0, 0, 0, 0, 0]),  # 50% ones
    np.array([1, 1, 1, 1, 1, 1, 1, 1, 0, 0]),  # 80% ones
    np.array([1] * 10),  # All ones
]

print(f"{'Data':<30} {'k':<5} {'NML (bits)':<15}")
print("-" * 50)

for data in test_cases:
    nml = BernoulliNML.nml_code_length(data)
    k = sum(data)
    print(f"{str(data.tolist()):<30} {k:<5} {nml:<15.2f}")

print(f"\nStochastic complexity for n=10: {BernoulliNML.stochastic_complexity(10):.4f}")
```

## Comparison: MDL vs Other Model Selection Methods

| Method | Principle | Penalty | Asymptotics | Best For |
|--------|-----------|---------|-------------|----------|
| **MDL** | Compression | $\frac{k}{2}\log n$ | Consistent | General |
| **AIC** | Information loss | $2k$ | Efficient | Prediction |
| **BIC** | Bayesian | $k\log n$ | Consistent | Model selection |
| **Cross-validation** | Empirical | Data-dependent | Variable | Finite samples |
| **NML** | Optimal coding | Stochastic complexity | Optimal | Theory |

## Frequently Asked Questions

### How do I choose between MDL and cross-validation?

Use MDL when:
- You want a principled, theory-grounded approach
- Computational resources are limited (no repeated training)
- You need interpretable model complexity measures

Use cross-validation when:
- Sample size is small
- The relationship between complexity and generalization is unclear
- Prediction accuracy is the primary goal

### Can MDL handle deep neural networks?

MDL can be applied to neural networks, but challenges include:
1. **Parameter counting:** Modern networks have millions of parameters
2. **Precision:** What precision to use for weights?
3. **Architecture:** How to account for architecture choices?

Practical approaches use:
- Effective number of parameters (via Fisher information)
- Pruning-based complexity measures
- Variational approximations

### How does MDL relate to Occam's razor?

MDL is a formalization of Occam's razor:
- Simple models have short descriptions
- Complex models need more bits to describe
- MDL prefers the simplest model that explains the data well

The key insight is that "simplicity" is measured in bits, providing an objective criterion.

### What is the relationship between MDL and PAC learning?

Both provide learning guarantees, but differently:
- **PAC:** Bounds generalization error in terms of sample complexity
- **MDL:** Bounds excess risk in terms of code length

The MDL bound is: with high probability,
$$\text{Risk}(\hat{h}) - \text{Risk}(h^*) \leq O\left(\sqrt{\frac{L(\hat{h})}{n}}\right)$$

## Key Takeaways

1. **MDL formalizes Occam's razor** by measuring model complexity in bits

2. **Two-part MDL** separates model and data descriptions; prequential MDL uses sequential prediction

3. **MDL justifies regularization** by showing complexity penalties prevent overfitting

4. **BIC approximates MDL** with penalty $\frac{k}{2}\log n$

5. **NML provides optimal coding** but is often intractable

6. **Applications include** model selection, anomaly detection, change point detection, and neural architecture search

## Next Steps in Your Learning Journey

Now that you understand MDL:

1. **Read Grünwald's "The Minimum Description Length Principle"** for comprehensive theory
2. **Implement MDL model selection** for your own problems
3. **Explore connections to Bayesian inference** and information theory
4. **Study computational tractability** of different MDL variants
5. **Apply MDL to neural network compression** and architecture search

---

*Last updated: January 2025. This article covers MDL principle for model selection as of current research.*
