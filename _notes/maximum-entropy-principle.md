---
title: "Maximum Entropy Principle"
subtitle: "Why maximum entropy distributions are everywhere"
date: 2026-10-12
tags:
  - "信息论"
  - "数学"
  - "统计"
---

The maximum entropy principle states that, given certain constraints, the probability distribution that best represents the current state of knowledge is the one with the largest entropy.

## Intuition

When we know very little about a system, we should assume the distribution that makes the fewest assumptions. Maximum entropy distributions are the most "honest" — they don't pretend to know more than we actually know.

## Common Examples

### 1. Uniform Distribution

**Constraint:** Distribution is bounded between a and b, nothing else known.

**Result:** Uniform distribution on [a, b].

This makes sense — if we know nothing except the bounds, all values are equally likely.

### 2. Gaussian Distribution

**Constraints:** Mean μ and variance σ² are known.

**Result:** Normal distribution N(μ, σ²).

This is why Gaussian distributions are so common in nature — they arise whenever we only know the mean and variance.

### 3. Exponential Distribution

**Constraint:** Mean is known, distribution is non-negative.

**Result:** Exponential distribution.

Common in waiting times and survival analysis.

## The Math

Given constraints:
$$E[f_i(X)] = c_i \quad \text{for } i = 1, \ldots, n$$

The maximum entropy distribution has the form:
$$p(x) = \exp\left(\lambda_0 + \sum_{i=1}^n \lambda_i f_i(x)\right)$$

where λ₀, λ₁, ..., λₙ are determined by the constraints.

## Applications

### Physics

- Statistical mechanics: the Boltzmann distribution maximizes entropy given energy constraints
- Thermodynamics: entropy always increases (Second Law)

### Machine Learning

- Maximum entropy classifiers (logistic regression)
- Regularization: adding entropy terms prevents overfitting
- Information bottleneck theory

### Natural Language Processing

- Maximum entropy models for part-of-speech tagging, named entity recognition

## Why It Matters

The maximum entropy principle provides a principled way to choose distributions when information is incomplete. It appears everywhere because it represents the most conservative assumption — the distribution that doesn't assume anything beyond what we know.

## Further Reading

- E.T. Jaynes: "Information Theory and Statistical Mechanics" (1957)
- Cover & Thomas: "Elements of Information Theory"
