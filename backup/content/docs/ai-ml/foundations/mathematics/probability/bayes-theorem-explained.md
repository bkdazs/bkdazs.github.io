---
title: "Bayes' Theorem Explained: From Basics to Bayesian Machine Learning"
description: "Master Bayes' theorem for machine learning. Learn prior, posterior, likelihood with intuitive examples, Naive Bayes classifier, and Bayesian deep learning."
date: 2024-01-15
lastmod: 2024-01-15
draft: false
weight: 2
toc: true
categories: ["AI/ML", "Mathematics", "Statistics"]
tags: ["bayes theorem", "bayesian machine learning", "prior posterior", "naive bayes", "bayesian inference"]
series: ["Mathematics for ML"]
---

# Bayes' Theorem Explained: From Basics to Bayesian Machine Learning

**"Probability is the language of uncertainty, and Bayes' theorem is its grammar."**

Bayes' theorem is the foundation of probabilistic reasoning and Bayesian machine learning. From spam filters to medical diagnosis to modern deep learning, this single equation powers countless applications. Let's master it.

---

## The Bayes' Theorem Formula

### The Equation That Changed Everything

$$P(A|B) = \frac{P(B|A) \cdot P(A)}{P(B)}$$

**In words**: The probability of A given B equals the probability of B given A, times the probability of A, divided by the probability of B.

### The Terms

| Term | Name | Meaning |
|------|------|---------|
| $P(A\|B)$ | **Posterior** | What we want: probability of A after seeing B |
| $P(B\|A)$ | **Likelihood** | How likely is B if A is true? |
| $P(A)$ | **Prior** | Our belief about A before seeing B |
| $P(B)$ | **Evidence** | How likely is B overall? |

### Visual Intuition

```
BEFORE DATA (Prior)              AFTER DATA (Posterior)
┌─────────────────────┐          ┌─────────────────────┐
│                     │          │       ████          │
│  ███████████████    │    B     │       ████          │
│  ███████████████    │  ──────► │       ████          │
│                     │  (Data)  │                     │
│                     │          │                     │
└─────────────────────┘          └─────────────────────┘
    Uncertain belief               More certain belief
```

---

## Intuitive Examples

### Example 1: Medical Testing

A disease affects 1% of the population. A test has:
- 90% sensitivity (true positive rate): P(positive | disease) = 0.90
- 95% specificity (true negative rate): P(negative | no disease) = 0.95

**Question**: If you test positive, what's the probability you have the disease?

```python
import numpy as np

def medical_test_example():
    """Classic Bayes' theorem medical testing example."""
    
    # Given
    P_disease = 0.01          # Prior: 1% have disease
    P_no_disease = 0.99
    P_pos_given_disease = 0.90    # Sensitivity
    P_neg_given_no_disease = 0.95 # Specificity
    P_pos_given_no_disease = 0.05 # False positive rate
    
    # Calculate P(positive) - total probability
    P_positive = (P_pos_given_disease * P_disease + 
                  P_pos_given_no_disease * P_no_disease)
    
    # Bayes' theorem: P(disease | positive)
    P_disease_given_pos = (P_pos_given_disease * P_disease) / P_positive
    
    print("Medical Testing Example")
    print("=" * 50)
    print(f"Disease prevalence (prior): {P_disease*100:.1f}%")
    print(f"Test sensitivity: {P_pos_given_disease*100:.0f}%")
    print(f"Test specificity: {P_neg_given_no_disease*100:.0f}%")
    print(f"\nP(positive test): {P_positive*100:.2f}%")
    print(f"\n>>> P(disease | positive test): {P_disease_given_pos*100:.1f}%")
    print("\nSurprising? Despite 90% sensitivity, only ~15% chance!")
    print("Base rate (prior) matters enormously.")

medical_test_example()
```

**Output:**
```
Disease prevalence (prior): 1.0%
Test sensitivity: 90%
Test specificity: 95%

P(positive test): 5.85%

>>> P(disease | positive test): 15.4%

Surprising? Despite 90% sensitivity, only ~15% chance!
Base rate (prior) matters enormously.
```

### Example 2: Spam Classification

```python
def spam_classification_example():
    """Spam classification with Bayes' theorem."""
    
    # Prior probabilities
    P_spam = 0.4      # 40% of emails are spam
    P_not_spam = 0.6
    
    # Likelihood: P(contains "free" | spam/not spam)
    P_free_given_spam = 0.7    # 70% of spam contains "free"
    P_free_given_not_spam = 0.1  # 10% of legitimate email contains "free"
    
    # P("free") by total probability
    P_free = P_free_given_spam * P_spam + P_free_given_not_spam * P_not_spam
    
    # Bayes' theorem
    P_spam_given_free = (P_free_given_spam * P_spam) / P_free
    
    print("Spam Classification Example")
    print("=" * 50)
    print(f"Prior P(spam): {P_spam*100:.0f}%")
    print(f"P('free' | spam): {P_free_given_spam*100:.0f}%")
    print(f"P('free' | not spam): {P_free_given_not_spam*100:.0f}%")
    print(f"\n>>> P(spam | contains 'free'): {P_spam_given_free*100:.1f}%")
    
    # What if we see multiple words?
    # Naive Bayes assumes independence
    P_money_given_spam = 0.5
    P_money_given_not_spam = 0.05
    
    # P(spam | "free" AND "money")
    # Naive assumption: P(free, money | spam) = P(free|spam) * P(money|spam)
    P_both_given_spam = P_free_given_spam * P_money_given_spam
    P_both_given_not_spam = P_free_given_not_spam * P_money_given_not_spam
    
    P_both = P_both_given_spam * P_spam + P_both_given_not_spam * P_not_spam
    P_spam_given_both = (P_both_given_spam * P_spam) / P_both
    
    print(f"\n>>> P(spam | 'free' AND 'money'): {P_spam_given_both*100:.1f}%")

spam_classification_example()
```

---

## Bayesian Inference Framework

### The ML Version of Bayes

For machine learning, we write:

$$P(\theta | D) = \frac{P(D | \theta) \cdot P(\theta)}{P(D)}$$

Where:
- $\theta$ = model parameters
- $D$ = training data
- $P(\theta)$ = **prior** over parameters
- $P(D|\theta)$ = **likelihood** (how well parameters explain data)
- $P(\theta|D)$ = **posterior** (updated belief after seeing data)

```
┌─────────────────────────────────────────────────────────────────┐
│                     BAYESIAN ML WORKFLOW                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   PRIOR ──────────► LIKELIHOOD ──────────► POSTERIOR            │
│   P(θ)              P(D|θ)                  P(θ|D)              │
│                                                                 │
│   What you          How data               Updated              │
│   believe before    fits model             belief               │
│   seeing data                              after data           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Bayesian vs Frequentist

| Aspect | Frequentist | Bayesian |
|--------|-------------|----------|
| Parameters | Fixed, unknown constants | Random variables with distributions |
| Probability | Long-run frequency | Degree of belief |
| Estimation | Point estimate (MLE) | Full posterior distribution |
| Uncertainty | Confidence intervals | Credible intervals |
| Prior information | Not used | Explicitly incorporated |

```python
import matplotlib.pyplot as plt
from scipy import stats

def bayesian_vs_frequentist():
    """Compare Bayesian and Frequentist approaches."""
    
    # Scenario: Coin flip, observed 7 heads in 10 flips
    n_flips = 10
    n_heads = 7
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Frequentist: MLE
    ax = axes[0]
    p_mle = n_heads / n_flips  # Maximum Likelihood Estimate
    
    # Likelihood function
    p = np.linspace(0.001, 0.999, 1000)
    likelihood = stats.binom.pmf(n_heads, n_flips, p)
    
    ax.plot(p, likelihood / likelihood.max(), 'b-', linewidth=2)
    ax.axvline(p_mle, color='red', linestyle='--', 
               label=f'MLE = {p_mle}')
    
    # 95% confidence interval (Wald interval)
    se = np.sqrt(p_mle * (1-p_mle) / n_flips)
    ci_lower = max(0, p_mle - 1.96 * se)
    ci_upper = min(1, p_mle + 1.96 * se)
    ax.axvspan(ci_lower, ci_upper, alpha=0.2, color='blue',
               label=f'95% CI: [{ci_lower:.2f}, {ci_upper:.2f}]')
    
    ax.set_xlabel('p (probability of heads)')
    ax.set_ylabel('Normalized Likelihood')
    ax.set_title('Frequentist: Maximum Likelihood')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    # Bayesian: Posterior with Beta prior
    ax = axes[1]
    
    # Prior: Beta(1, 1) = Uniform
    alpha_prior, beta_prior = 1, 1
    
    # Posterior: Beta(alpha + heads, beta + tails)
    alpha_post = alpha_prior + n_heads
    beta_post = beta_prior + (n_flips - n_heads)
    
    prior = stats.beta.pdf(p, alpha_prior, beta_prior)
    posterior = stats.beta.pdf(p, alpha_post, beta_post)
    
    ax.plot(p, prior, 'g--', linewidth=2, label='Prior: Beta(1,1)')
    ax.plot(p, posterior, 'r-', linewidth=2, label=f'Posterior: Beta({alpha_post},{beta_post})')
    
    # Posterior mean and credible interval
    post_mean = alpha_post / (alpha_post + beta_post)
    ci_lower = stats.beta.ppf(0.025, alpha_post, beta_post)
    ci_upper = stats.beta.ppf(0.975, alpha_post, beta_post)
    
    ax.axvline(post_mean, color='red', linestyle=':', 
               label=f'Posterior mean = {post_mean:.3f}')
    ax.axvspan(ci_lower, ci_upper, alpha=0.2, color='red',
               label=f'95% Credible: [{ci_lower:.2f}, {ci_upper:.2f}]')
    
    ax.set_xlabel('p (probability of heads)')
    ax.set_ylabel('Density')
    ax.set_title('Bayesian: Posterior Distribution')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("Comparison:")
    print(f"Frequentist MLE: {p_mle:.3f}")
    print(f"Bayesian Posterior Mean: {post_mean:.3f}")
    print(f"Bayesian gives slightly different answer due to prior!")

bayesian_vs_frequentist()
```

---

## Conjugate Priors

### What Are Conjugate Priors?

A prior is **conjugate** to a likelihood if the posterior is in the same family as the prior.

| Likelihood | Conjugate Prior | Posterior |
|------------|----------------|-----------|
| Bernoulli/Binomial | Beta | Beta |
| Poisson | Gamma | Gamma |
| Gaussian (known σ) | Gaussian | Gaussian |
| Gaussian (known μ) | Inverse-Gamma | Inverse-Gamma |
| Multinomial | Dirichlet | Dirichlet |

### Why Use Conjugate Priors?

1. **Analytical solution**: No need for numerical methods
2. **Interpretable updates**: Prior parameters have clear meaning
3. **Efficient computation**: Closed-form posterior

```python
def conjugate_prior_demo():
    """Demonstrate Beta-Binomial conjugacy."""
    
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    
    # Different priors
    priors = [
        (1, 1, 'Uniform: No prior knowledge'),
        (2, 2, 'Slightly favor 0.5'),
        (10, 10, 'Strong belief p ≈ 0.5'),
        (1, 5, 'Believe p is small'),
        (5, 1, 'Believe p is large'),
        (0.5, 0.5, 'Jeffreys prior (sparse)')
    ]
    
    # Observed: 7 heads in 10 flips
    n_heads, n_tails = 7, 3
    p = np.linspace(0.001, 0.999, 1000)
    
    for ax, (alpha, beta, title) in zip(axes.flat, priors):
        # Prior
        prior = stats.beta.pdf(p, alpha, beta)
        
        # Posterior
        alpha_post = alpha + n_heads
        beta_post = beta + n_tails
        posterior = stats.beta.pdf(p, alpha_post, beta_post)
        
        ax.plot(p, prior, 'b--', label=f'Prior: Beta({alpha},{beta})')
        ax.plot(p, posterior, 'r-', linewidth=2, 
                label=f'Posterior: Beta({alpha_post},{beta_post})')
        ax.axvline(0.7, color='green', linestyle=':', label='MLE = 0.7')
        
        ax.set_xlabel('p')
        ax.set_ylabel('Density')
        ax.set_title(title)
        ax.legend(fontsize=8)
        ax.set_xlim(0, 1)
    
    plt.suptitle('Effect of Different Priors (Data: 7 heads in 10 flips)', fontsize=14)
    plt.tight_layout()
    plt.show()

conjugate_prior_demo()
```

### Sequential Bayesian Updating

```python
def sequential_updating():
    """Show how posterior updates with more data."""
    
    # True probability
    p_true = 0.6
    
    # Start with uniform prior
    alpha, beta = 1, 1
    
    # Generate data sequentially
    np.random.seed(42)
    
    fig, axes = plt.subplots(2, 4, figsize=(16, 8))
    p = np.linspace(0.001, 0.999, 1000)
    
    observations = [0, 5, 10, 20, 50, 100, 200, 500]
    total_heads = 0
    total_flips = 0
    
    for ax, n_total in zip(axes.flat, observations):
        # Generate new flips
        if n_total > total_flips:
            new_flips = n_total - total_flips
            new_heads = np.random.binomial(new_flips, p_true)
            total_heads += new_heads
            total_flips = n_total
        
        # Update posterior
        alpha_post = 1 + total_heads
        beta_post = 1 + (total_flips - total_heads)
        
        posterior = stats.beta.pdf(p, alpha_post, beta_post)
        
        ax.plot(p, posterior, 'b-', linewidth=2)
        ax.axvline(p_true, color='red', linestyle='--', label=f'True p = {p_true}')
        
        if total_flips > 0:
            mle = total_heads / total_flips
            ax.axvline(mle, color='green', linestyle=':', 
                      label=f'MLE = {mle:.2f}')
        
        # 95% credible interval
        if total_flips > 0:
            ci_low = stats.beta.ppf(0.025, alpha_post, beta_post)
            ci_high = stats.beta.ppf(0.975, alpha_post, beta_post)
            ax.axvspan(ci_low, ci_high, alpha=0.2, color='blue')
        
        ax.set_title(f'n = {n_total} flips')
        ax.set_xlabel('p')
        ax.legend(fontsize=8)
        ax.set_xlim(0, 1)
    
    plt.suptitle('Sequential Bayesian Updating: Posterior Converges to True Value', fontsize=14)
    plt.tight_layout()
    plt.show()

sequential_updating()
```

---

## Naive Bayes Classifier

### The Algorithm

Naive Bayes assumes features are **conditionally independent** given the class:

$$P(y | x_1, ..., x_n) \propto P(y) \prod_{i=1}^{n} P(x_i | y)$$

Despite the "naive" assumption, it works surprisingly well!

```python
from sklearn.naive_bayes import GaussianNB, MultinomialNB
from sklearn.datasets import load_iris, fetch_20newsgroups
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report, confusion_matrix
import numpy as np

def naive_bayes_from_scratch():
    """Implement Naive Bayes from scratch."""
    
    class NaiveBayesClassifier:
        """Gaussian Naive Bayes implementation."""
        
        def __init__(self):
            self.classes = None
            self.priors = {}
            self.means = {}
            self.vars = {}
        
        def fit(self, X, y):
            self.classes = np.unique(y)
            
            for c in self.classes:
                X_c = X[y == c]
                self.priors[c] = len(X_c) / len(X)
                self.means[c] = X_c.mean(axis=0)
                self.vars[c] = X_c.var(axis=0) + 1e-9  # Add smoothing
            
            return self
        
        def _gaussian_pdf(self, x, mean, var):
            """Compute Gaussian probability density."""
            return np.exp(-0.5 * (x - mean)**2 / var) / np.sqrt(2 * np.pi * var)
        
        def predict_proba(self, X):
            """Compute class probabilities for each sample."""
            probs = []
            
            for x in X:
                class_probs = []
                for c in self.classes:
                    # Log probability for numerical stability
                    prior = np.log(self.priors[c])
                    likelihood = np.sum(np.log(
                        self._gaussian_pdf(x, self.means[c], self.vars[c])
                    ))
                    class_probs.append(prior + likelihood)
                
                # Softmax to get probabilities
                class_probs = np.array(class_probs)
                class_probs = np.exp(class_probs - class_probs.max())
                class_probs /= class_probs.sum()
                probs.append(class_probs)
            
            return np.array(probs)
        
        def predict(self, X):
            """Predict class labels."""
            probs = self.predict_proba(X)
            return self.classes[np.argmax(probs, axis=1)]
    
    # Test on Iris dataset
    iris = load_iris()
    X_train, X_test, y_train, y_test = train_test_split(
        iris.data, iris.target, test_size=0.3, random_state=42
    )
    
    # Our implementation
    nb = NaiveBayesClassifier()
    nb.fit(X_train, y_train)
    y_pred = nb.predict(X_test)
    
    # Sklearn implementation
    nb_sklearn = GaussianNB()
    nb_sklearn.fit(X_train, y_train)
    y_pred_sklearn = nb_sklearn.predict(X_test)
    
    print("Naive Bayes from Scratch vs Sklearn")
    print("=" * 50)
    print(f"Our accuracy: {(y_pred == y_test).mean():.4f}")
    print(f"Sklearn accuracy: {(y_pred_sklearn == y_test).mean():.4f}")
    
    return nb

naive_bayes_from_scratch()
```

### Text Classification with Naive Bayes

```python
def text_classification_naive_bayes():
    """Naive Bayes for text classification."""
    
    from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
    
    # Sample data
    texts = [
        "I love this movie, it's amazing!",
        "Great film, wonderful acting",
        "Terrible movie, waste of time",
        "Awful, I hated every minute",
        "Absolutely fantastic, must watch!",
        "Boring and dull, very disappointing",
        "Best movie I've seen this year",
        "Worst film ever made"
    ]
    labels = [1, 1, 0, 0, 1, 0, 1, 0]  # 1 = positive, 0 = negative
    
    # Vectorize text
    vectorizer = CountVectorizer()
    X = vectorizer.fit_transform(texts)
    
    # Train Multinomial Naive Bayes (good for text)
    nb = MultinomialNB()
    nb.fit(X, labels)
    
    # Test on new text
    test_texts = [
        "This movie is great!",
        "I didn't like this film at all",
        "Amazing performance by the actors"
    ]
    X_test = vectorizer.transform(test_texts)
    predictions = nb.predict(X_test)
    probabilities = nb.predict_proba(X_test)
    
    print("Naive Bayes Text Classification")
    print("=" * 50)
    for text, pred, prob in zip(test_texts, predictions, probabilities):
        sentiment = "Positive" if pred == 1 else "Negative"
        print(f"'{text}'")
        print(f"  → {sentiment} (confidence: {prob.max():.2%})")
    
    # Feature importance (most predictive words)
    feature_names = vectorizer.get_feature_names_out()
    log_probs = nb.feature_log_prob_
    
    # Words that indicate positive vs negative
    diff = log_probs[1] - log_probs[0]
    most_positive = feature_names[np.argsort(diff)[-5:]]
    most_negative = feature_names[np.argsort(diff)[:5]]
    
    print(f"\nMost positive words: {list(most_positive)}")
    print(f"Most negative words: {list(most_negative)}")

text_classification_naive_bayes()
```

---

## Bayesian Neural Networks

### Why Bayesian Deep Learning?

Standard neural networks give **point predictions**. Bayesian neural networks give **distributions**, capturing uncertainty.

```
Standard NN:                  Bayesian NN:
   Input → [  θ  ] → Output      Input → [P(θ)] → P(Output)
   (fixed weights)               (distribution over weights)
```

### Uncertainty Types

| Type | Description | Example |
|------|-------------|---------|
| **Aleatoric** | Data uncertainty (irreducible) | Sensor noise |
| **Epistemic** | Model uncertainty (reducible with more data) | Unseen regions |

```python
def bayesian_neural_network_demo():
    """Demonstrate Bayesian uncertainty in predictions."""
    
    import numpy as np
    import matplotlib.pyplot as plt
    
    # Generate data with noise
    np.random.seed(42)
    X_train = np.random.uniform(-3, 3, 20)
    y_train = np.sin(X_train) + np.random.normal(0, 0.2, 20)
    
    # Test points (including regions with no training data)
    X_test = np.linspace(-5, 5, 100)
    
    # Simulate Bayesian NN with Monte Carlo Dropout
    # Multiple forward passes with dropout active
    
    def simple_nn_with_dropout(x, weights, dropout_rate=0.3):
        """Simple NN with dropout for uncertainty estimation."""
        h = np.tanh(x.reshape(-1, 1) @ weights['w1'].T + weights['b1'])
        # Dropout mask
        mask = np.random.binomial(1, 1-dropout_rate, h.shape)
        h = h * mask / (1 - dropout_rate)  # Scale to maintain expected value
        out = h @ weights['w2'].T + weights['b2']
        return out.flatten()
    
    # Initialize weights (pretend we trained the network)
    weights = {
        'w1': np.random.randn(20, 1) * 0.5,
        'b1': np.random.randn(20) * 0.1,
        'w2': np.random.randn(1, 20) * 0.5,
        'b2': np.array([0.0])
    }
    
    # Monte Carlo sampling
    n_samples = 100
    predictions = []
    
    for _ in range(n_samples):
        pred = simple_nn_with_dropout(X_test, weights)
        predictions.append(pred)
    
    predictions = np.array(predictions)
    mean_pred = predictions.mean(axis=0)
    std_pred = predictions.std(axis=0)
    
    # Plot
    fig, ax = plt.subplots(figsize=(12, 6))
    
    # True function
    ax.plot(X_test, np.sin(X_test), 'g-', linewidth=2, label='True function')
    
    # Training data
    ax.scatter(X_train, y_train, c='red', s=50, label='Training data', zorder=5)
    
    # Predictions with uncertainty
    ax.plot(X_test, mean_pred, 'b-', linewidth=2, label='Mean prediction')
    ax.fill_between(X_test, mean_pred - 2*std_pred, mean_pred + 2*std_pred,
                    alpha=0.3, color='blue', label='±2σ uncertainty')
    
    # Highlight regions with high uncertainty (no data)
    ax.axvspan(-5, -3, alpha=0.1, color='red')
    ax.axvspan(3, 5, alpha=0.1, color='red')
    
    ax.set_xlabel('x')
    ax.set_ylabel('y')
    ax.set_title('Bayesian NN: Uncertainty Increases Outside Training Region')
    ax.legend()
    ax.grid(True, alpha=0.3)
    
    plt.tight_layout()
    plt.show()
    
    print("Key insight: Uncertainty (blue band) grows where we have no data!")

bayesian_neural_network_demo()
```

### Monte Carlo Dropout

**Key insight**: Dropout at test time approximates Bayesian inference!

```python
def mc_dropout_pytorch():
    """Monte Carlo Dropout for uncertainty estimation (PyTorch code)."""
    
    print("Monte Carlo Dropout (PyTorch)")
    print("=" * 50)
    print("""
    import torch
    import torch.nn as nn
    
    class MCDropoutModel(nn.Module):
        def __init__(self, input_dim, hidden_dim, output_dim, dropout_rate=0.2):
            super().__init__()
            self.fc1 = nn.Linear(input_dim, hidden_dim)
            self.fc2 = nn.Linear(hidden_dim, hidden_dim)
            self.fc3 = nn.Linear(hidden_dim, output_dim)
            self.dropout = nn.Dropout(dropout_rate)
        
        def forward(self, x):
            x = torch.relu(self.fc1(x))
            x = self.dropout(x)  # Keep dropout active!
            x = torch.relu(self.fc2(x))
            x = self.dropout(x)
            return self.fc3(x)
    
    def predict_with_uncertainty(model, x, n_samples=100):
        '''Monte Carlo Dropout prediction.'''
        model.train()  # Keep dropout active!
        
        predictions = []
        for _ in range(n_samples):
            pred = model(x)
            predictions.append(pred.detach())
        
        predictions = torch.stack(predictions)
        mean = predictions.mean(dim=0)
        std = predictions.std(dim=0)
        
        return mean, std
    
    # Usage
    mean, uncertainty = predict_with_uncertainty(model, test_data)
    """)

mc_dropout_pytorch()
```

---

## Bayesian Optimization

### Hyperparameter Tuning with Bayes

Bayesian optimization uses Bayes' theorem to efficiently search hyperparameter space.

```python
def bayesian_optimization_example():
    """Bayesian optimization for hyperparameter tuning."""
    
    # Objective function to minimize (black box)
    def objective(x):
        """Example: noisy function with global minimum at x ≈ 2.5"""
        return (x - 2.5)**2 + 0.5 * np.sin(3 * x) + np.random.normal(0, 0.1)
    
    # Simple Gaussian Process surrogate
    class SimpleGP:
        def __init__(self, length_scale=1.0, noise=0.1):
            self.length_scale = length_scale
            self.noise = noise
            self.X_train = None
            self.y_train = None
        
        def kernel(self, X1, X2):
            """RBF kernel."""
            sqdist = np.sum(X1**2, 1).reshape(-1, 1) + np.sum(X2**2, 1) - 2 * X1 @ X2.T
            return np.exp(-0.5 * sqdist / self.length_scale**2)
        
        def fit(self, X, y):
            self.X_train = X.reshape(-1, 1)
            self.y_train = y.reshape(-1, 1)
        
        def predict(self, X):
            X = X.reshape(-1, 1)
            K = self.kernel(self.X_train, self.X_train) + self.noise * np.eye(len(self.X_train))
            K_s = self.kernel(self.X_train, X)
            K_ss = self.kernel(X, X)
            
            K_inv = np.linalg.inv(K)
            mu = K_s.T @ K_inv @ self.y_train
            sigma = K_ss - K_s.T @ K_inv @ K_s
            
            return mu.flatten(), np.sqrt(np.diag(sigma))
    
    # Bayesian optimization loop
    np.random.seed(42)
    
    # Initial random samples
    X_init = np.array([0.0, 5.0])
    y_init = np.array([objective(x) for x in X_init])
    
    X_samples = list(X_init)
    y_samples = list(y_init)
    
    gp = SimpleGP(length_scale=0.5, noise=0.1)
    
    fig, axes = plt.subplots(2, 3, figsize=(15, 10))
    
    for i, ax in enumerate(axes.flat):
        # Fit GP to current data
        gp.fit(np.array(X_samples), np.array(y_samples))
        
        # Predict on grid
        X_grid = np.linspace(0, 5, 100)
        mu, sigma = gp.predict(X_grid)
        
        # Plot true function (unknown in practice)
        true_y = [(x - 2.5)**2 + 0.5 * np.sin(3 * x) for x in X_grid]
        ax.plot(X_grid, true_y, 'g--', alpha=0.5, label='True (unknown)')
        
        # Plot GP prediction
        ax.plot(X_grid, mu, 'b-', linewidth=2, label='GP mean')
        ax.fill_between(X_grid, mu - 2*sigma, mu + 2*sigma, 
                       alpha=0.3, color='blue', label='±2σ')
        
        # Plot samples
        ax.scatter(X_samples, y_samples, c='red', s=80, zorder=5, label='Samples')
        
        # Acquisition function: Expected Improvement
        best_y = min(y_samples)
        z = (best_y - mu) / (sigma + 1e-9)
        ei = sigma * (z * stats.norm.cdf(z) + stats.norm.pdf(z))
        
        # Next sample: maximize acquisition function
        next_x = X_grid[np.argmax(ei)]
        ax.axvline(next_x, color='orange', linestyle=':', label=f'Next: {next_x:.2f}')
        
        ax.set_title(f'Iteration {i+1}: {len(X_samples)} samples')
        ax.set_xlabel('x')
        ax.set_ylabel('y')
        if i == 0:
            ax.legend(fontsize=8)
        
        # Sample next point
        next_y = objective(next_x)
        X_samples.append(next_x)
        y_samples.append(next_y)
    
    plt.suptitle('Bayesian Optimization: Finding Minimum with Few Evaluations', fontsize=14)
    plt.tight_layout()
    plt.show()
    
    print(f"Best found: x = {X_samples[np.argmin(y_samples)]:.3f}")
    print(f"True optimum: x ≈ 2.5")

bayesian_optimization_example()
```

---

## Common Applications

### 1. Medical Diagnosis

```python
def medical_diagnosis():
    """Bayesian medical diagnosis."""
    
    diseases = {
        'Common Cold': {'prior': 0.10, 'fever': 0.3, 'cough': 0.8, 'fatigue': 0.5},
        'Flu': {'prior': 0.03, 'fever': 0.9, 'cough': 0.7, 'fatigue': 0.9},
        'COVID-19': {'prior': 0.01, 'fever': 0.8, 'cough': 0.6, 'fatigue': 0.7},
        'Healthy': {'prior': 0.86, 'fever': 0.02, 'cough': 0.1, 'fatigue': 0.2},
    }
    
    # Patient symptoms
    symptoms = {'fever': True, 'cough': True, 'fatigue': True}
    
    print("Bayesian Medical Diagnosis")
    print("=" * 50)
    print(f"Patient symptoms: {[s for s, present in symptoms.items() if present]}")
    print()
    
    posteriors = {}
    
    for disease, probs in diseases.items():
        prior = probs['prior']
        
        # Likelihood: P(symptoms | disease)
        likelihood = 1.0
        for symptom, present in symptoms.items():
            p_symptom = probs[symptom]
            if present:
                likelihood *= p_symptom
            else:
                likelihood *= (1 - p_symptom)
        
        # Unnormalized posterior
        posteriors[disease] = prior * likelihood
    
    # Normalize
    total = sum(posteriors.values())
    for disease in posteriors:
        posteriors[disease] /= total
    
    # Sort by probability
    sorted_posteriors = sorted(posteriors.items(), key=lambda x: x[1], reverse=True)
    
    for disease, prob in sorted_posteriors:
        print(f"P({disease:12s} | symptoms) = {prob:.2%}")

medical_diagnosis()
```

### 2. A/B Testing

```python
def bayesian_ab_testing():
    """Bayesian A/B testing."""
    
    # Experiment results
    # Variant A: 120 conversions out of 1000 visitors
    # Variant B: 145 conversions out of 1000 visitors
    
    conversions_A, visitors_A = 120, 1000
    conversions_B, visitors_B = 145, 1000
    
    # Beta posteriors
    alpha_A = 1 + conversions_A
    beta_A = 1 + visitors_A - conversions_A
    
    alpha_B = 1 + conversions_B
    beta_B = 1 + visitors_B - conversions_B
    
    # Monte Carlo simulation
    n_samples = 100000
    samples_A = np.random.beta(alpha_A, beta_A, n_samples)
    samples_B = np.random.beta(alpha_B, beta_B, n_samples)
    
    # P(B > A)
    prob_B_better = (samples_B > samples_A).mean()
    
    # Expected lift
    lift = ((samples_B - samples_A) / samples_A).mean()
    
    fig, axes = plt.subplots(1, 2, figsize=(14, 5))
    
    # Posterior distributions
    ax = axes[0]
    p = np.linspace(0.08, 0.18, 1000)
    ax.plot(p, stats.beta.pdf(p, alpha_A, beta_A), 'b-', linewidth=2, label='Variant A')
    ax.plot(p, stats.beta.pdf(p, alpha_B, beta_B), 'r-', linewidth=2, label='Variant B')
    ax.axvline(conversions_A/visitors_A, color='blue', linestyle='--', alpha=0.5)
    ax.axvline(conversions_B/visitors_B, color='red', linestyle='--', alpha=0.5)
    ax.set_xlabel('Conversion Rate')
    ax.set_ylabel('Density')
    ax.set_title('Posterior Distributions')
    ax.legend()
    
    # Lift distribution
    ax = axes[1]
    lift_samples = (samples_B - samples_A) / samples_A * 100
    ax.hist(lift_samples, bins=50, density=True, alpha=0.7)
    ax.axvline(0, color='black', linestyle='--', label='No difference')
    ax.axvline(lift_samples.mean(), color='red', linestyle='-', label=f'Mean lift: {lift*100:.1f}%')
    ax.set_xlabel('Relative Lift (%)')
    ax.set_ylabel('Density')
    ax.set_title('Distribution of Lift (B vs A)')
    ax.legend()
    
    plt.tight_layout()
    plt.show()
    
    print("Bayesian A/B Testing Results")
    print("=" * 50)
    print(f"Variant A: {conversions_A}/{visitors_A} = {conversions_A/visitors_A:.2%}")
    print(f"Variant B: {conversions_B}/{visitors_B} = {conversions_B/visitors_B:.2%}")
    print(f"\nP(B > A) = {prob_B_better:.1%}")
    print(f"Expected lift: {lift*100:.1f}%")
    print(f"\nDecision: {'Choose B' if prob_B_better > 0.95 else 'Need more data'}")

bayesian_ab_testing()
```

---

## FAQs

### What's the difference between prior and likelihood?

- **Prior**: Your belief about parameters **before** seeing data
- **Likelihood**: How well the data fits given specific parameter values

### How do I choose a prior?

1. **Uninformative**: Uniform, Jeffreys (minimal assumptions)
2. **Weakly informative**: Regularizing, prevents extreme values
3. **Informative**: Based on domain expertise or previous studies
4. **Conjugate**: For analytical convenience

### When to use Bayesian vs Frequentist?

| Use Bayesian | Use Frequentist |
|-------------|-----------------|
| Uncertainty quantification needed | Point estimates sufficient |
| Small sample sizes | Large datasets |
| Prior knowledge available | Want to be "objective" |
| Sequential updating | One-shot analysis |

---

## Key Takeaways

1. **Bayes' theorem** updates beliefs with evidence
2. **Prior × Likelihood ∝ Posterior**
3. **Conjugate priors** give analytical posteriors
4. **Naive Bayes** is simple but effective
5. **Bayesian NNs** capture uncertainty in predictions
6. **Bayesian optimization** efficiently searches hyperparameter space

---

## Next Steps

Continue your Bayesian journey:

1. **[MLE vs MAP](/docs/ai-ml/foundations/mathematics/probability/mle-vs-map/)** - Parameter estimation
2. **[Probability Distributions](/docs/ai-ml/foundations/mathematics/probability/probability-distributions-machine-learning/)** - Foundation concepts
3. **[Information Theory](/docs/ai-ml/foundations/mathematics/probability/information-theory-entropy/)** - Entropy and KL divergence

---

## References

1. Gelman, A. et al. "Bayesian Data Analysis" (3rd ed.)
2. Murphy, K. P. "Machine Learning: A Probabilistic Perspective"
3. Bishop, C. M. "Pattern Recognition and Machine Learning" - Chapter 3
4. McElreath, R. "Statistical Rethinking"

---

*Last updated: January 2024. Part of our [Mathematics for Machine Learning](/docs/ai-ml/foundations/mathematics/) series.*
