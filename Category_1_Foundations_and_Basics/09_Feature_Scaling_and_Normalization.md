# Feature Scaling & Normalization

## What is Feature Scaling? 📏

**Simple Definition:** Transforming all your features to similar ranges so no single feature dominates just because of its scale.

### The Problem: Different Scales

**Example:**
```
Feature 1 - Age: 25 (small number)
Feature 2 - Income: 50,000 (big number)
Feature 3 - Credit Score: 750 (medium number)
```

**Problem:** ML algorithms might think income is MORE important just because it's a bigger number!

### Race Analogy 🏃

Imagine a race where:
- Person A runs in meters: 100m
- Person B runs in kilometers: 0.1km
- They ran the same distance!

But if you just compare numbers (100 vs 0.1), it looks completely different!

**Feature scaling fixes this** → Put everyone on the same measuring system!

---

## Why is it Important?

### Algorithms That NEED Scaling:

**1. Distance-based algorithms:**
```
- K-Nearest Neighbors (KNN)
- K-Means Clustering
- Support Vector Machines (SVM)

Why? They calculate distances between points
Large-scale features dominate the distance calculation!
```

**2. Gradient Descent-based:**
```
- Linear Regression
- Logistic Regression
- Neural Networks

Why? Unscaled features cause slow/unstable training
```

---

### Example: K-Nearest Neighbors

**Without scaling:**
```
Person A: Age=30, Income=40,000
Person B: Age=32, Income=45,000
Person C: Age=35, Income=40,500

Distance A to B:
Age difference: 2
Income difference: 5,000
Total: √(2² + 5000²) = 5,000 (income dominates!)

Age barely matters! ❌
```

**With scaling (0-1 range):**
```
Person A: Age=0.30, Income=0.40
Person B: Age=0.32, Income=0.45
Person C: Age=0.35, Income=0.405

Distance A to B:
Age difference: 0.02
Income difference: 0.05
Total: √(0.02² + 0.05²) = 0.054

Both features matter equally! ✅
```

---

## Types of Feature Scaling

```
Main Types:
1. Normalization (Min-Max Scaling)
2. Standardization (Z-score Scaling)
3. Robust Scaling
4. MaxAbsScaler
5. Log Transformation
```

---

## 1. Normalization (Min-Max Scaling) 📊

### What It Does
Scales data to a fixed range, typically [0, 1]

### Formula:
```
X_scaled = (X - X_min) / (X_max - X_min)
```

### Example:

**Original data:**
```
Ages: [20, 25, 30, 35, 40]

Min = 20
Max = 40
Range = 40 - 20 = 20
```

**Normalized:**
```
20 → (20-20)/20 = 0.00
25 → (25-20)/20 = 0.25
30 → (30-20)/20 = 0.50
35 → (35-20)/20 = 0.75
40 → (40-20)/20 = 1.00

Result: [0, 0.25, 0.5, 0.75, 1.0]
```

**All values now between 0 and 1!** ✅

---

### When to Use:
✅ When you need bounded range (0-1)
✅ When distribution is not Gaussian
✅ For neural networks (bounded activation functions)
✅ For image pixel values

### When NOT to Use:
❌ When you have outliers (they'll squish everything else!)
❌ When you need to preserve the shape of distribution

---

### Example with Outlier:

**Without outlier:**
```
[20, 25, 30, 35, 40] → [0, 0.25, 0.5, 0.75, 1.0] ✅
Nice spread!
```

**With outlier:**
```
[20, 25, 30, 35, 400] → [0, 0.01, 0.03, 0.04, 1.0] ❌
Everything squished to 0!
```

---

## 2. Standardization (Z-score Scaling) 📈

### What It Does
Centers data around mean=0, standard deviation=1

### Formula:
```
X_scaled = (X - mean) / standard_deviation
```

### Example:

**Original data:**
```
Scores: [10, 20, 30, 40, 50]

Mean = 30
Std = 14.14
```

**Standardized:**
```
10 → (10-30)/14.14 = -1.41
20 → (20-30)/14.14 = -0.71
30 → (30-30)/14.14 =  0.00 (the mean)
40 → (40-30)/14.14 =  0.71
50 → (50-30)/14.14 =  1.41

Result: [-1.41, -0.71, 0, 0.71, 1.41]
```

**Mean is now 0, spread is in standard deviations!**

---

### Interpretation:
```
Value = 0 → At the mean (average)
Value = 1 → One standard deviation above mean
Value = -1 → One standard deviation below mean
Value = 2 → Two standard deviations above (unusual!)
```

---

### When to Use:
✅ When data has Gaussian (normal) distribution
✅ When you have outliers (less sensitive than normalization)
✅ For algorithms assuming normally distributed data
✅ Most common choice!

### When NOT to Use:
❌ When you need bounded range
❌ When distribution is heavily skewed

---

## 3. Robust Scaling 💪

### What It Does
Uses median and IQR (InterQuartile Range) instead of mean/std
**Less sensitive to outliers!**

### Formula:
```
X_scaled = (X - median) / IQR

IQR = Q3 - Q1 (75th percentile - 25th percentile)
```

### Example:

**Original data with outliers:**
```
Values: [1, 2, 3, 4, 5, 100]

Q1 (25th percentile) = 2
Median (50th percentile) = 3.5
Q3 (75th percentile) = 5
IQR = 5 - 2 = 3
```

**Robust scaled:**
```
1   → (1-3.5)/3 = -0.83
2   → (2-3.5)/3 = -0.50
3   → (3-3.5)/3 = -0.17
4   → (4-3.5)/3 =  0.17
5   → (5-3.5)/3 =  0.50
100 → (100-3.5)/3 = 32.17 (outlier still stands out)
```

**Outlier doesn't squish other values!** ✅

---

### When to Use:
✅ When you have many outliers
✅ When median/IQR better represent your data
✅ When standard scaling doesn't work well

---

## 4. MaxAbsScaler 🎯

### What It Does
Scales by the maximum absolute value
Result: Values between -1 and 1

### Formula:
```
X_scaled = X / max(|X|)
```

### Example:

**Original data:**
```
Values: [-10, -5, 0, 5, 10]

Max absolute value = 10
```

**Scaled:**
```
-10 → -10/10 = -1.0
-5  → -5/10 = -0.5
0   → 0/10 = 0.0
5   → 5/10 = 0.5
10  → 10/10 = 1.0

Result: [-1.0, -0.5, 0, 0.5, 1.0]
```

---

### When to Use:
✅ Data is already centered around 0
✅ When you want to preserve zero entries
✅ For sparse data (lots of zeros)

---

## 5. Log Transformation 📉

### What It Does
Applies logarithm to reduce skewness

### Formula:
```
X_scaled = log(X)
or
X_scaled = log(X + 1)  (if X can be 0)
```

### Example:

**Original (highly skewed):**
```
Income: [30k, 40k, 50k, 60k, 1M]
         ↑ Most here    ↑ Outlier
```

**After log:**
```
log(30,000) = 4.48
log(40,000) = 4.60
log(50,000) = 4.70
log(60,000) = 4.78
log(1,000,000) = 6.00

More evenly distributed!
```

---

### When to Use:
✅ Data is heavily skewed
✅ Data spans several orders of magnitude
✅ Examples: income, house prices, population

### When NOT to Use:
❌ Data contains zeros or negative numbers
(Use log(X+1) for zeros, can't use for negatives)

---

## Comparison Table

| Method | Range | Outlier Sensitive? | Best For |
|--------|-------|-------------------|----------|
| **Min-Max** | [0, 1] | Very ❌ | Bounded range needed, no outliers |
| **Standardization** | Unbounded | Moderate ⚠️ | Normal distribution, general use |
| **Robust** | Unbounded | Not sensitive ✅ | Many outliers |
| **MaxAbs** | [-1, 1] | Very ❌ | Sparse data, preserve zeros |
| **Log** | Unbounded | Reduces impact ✅ | Skewed data |

---

## Visual Comparison

### Original Data:
```
Feature 1 (Age): 20, 30, 40, 50, 60
Feature 2 (Income): 30k, 50k, 70k, 90k, 110k

Different scales! ❌
```

---

### After Min-Max Normalization:
```
Feature 1: 0.0, 0.25, 0.5, 0.75, 1.0
Feature 2: 0.0, 0.25, 0.5, 0.75, 1.0

Both [0,1]! ✅
```

---

### After Standardization:
```
Feature 1: -1.41, -0.71, 0, 0.71, 1.41
Feature 2: -1.41, -0.71, 0, 0.71, 1.41

Both mean=0, std=1! ✅
```

---

## Complete Example: House Price Prediction

### Original Data:
```
| Size (sqft) | Bedrooms | Price ($) |
|-------------|----------|-----------|
| 1000        | 2        | 200,000   |
| 1500        | 3        | 300,000   |
| 2000        | 4        | 400,000   |
| 2500        | 5        | 500,000   |
```

**Problem:** Different scales!
- Size: 1000-2500
- Bedrooms: 2-5
- Price: 200k-500k

---

### After Min-Max Normalization:

```
| Size | Bedrooms | Price |
|------|----------|-------|
| 0.00 | 0.00     | 0.00  |
| 0.33 | 0.33     | 0.33  |
| 0.67 | 0.67     | 0.67  |
| 1.00 | 1.00     | 1.00  |

All features [0,1]! ✅
```

---

### After Standardization:

```
| Size  | Bedrooms | Price |
|-------|----------|-------|
| -1.34 | -1.34    | -1.34 |
| -0.45 | -0.45    | -0.45 |
| 0.45  | 0.45     | 0.45  |
| 1.34  | 1.34     | 1.34  |

All mean=0, std=1! ✅
```

---

## Important Rules

### Rule 1: Fit on Training, Transform on All ⚠️

**WRONG:**
```python
# DON'T DO THIS!
scale_all_data()  # Using test data info!
split_data()
```

**CORRECT:**
```python
# Split first
train, test = split_data()

# Fit scaler on training ONLY
scaler.fit(train)

# Transform both using training parameters
train_scaled = scaler.transform(train)
test_scaled = scaler.transform(test)  # Use training min/max/mean!
```

**Why?** Using test data statistics = **data leakage**!

---

### Rule 2: Scale Features, Not Target (Usually)

**Typically:**
```python
Scale: X (features) ✅
Don't scale: y (target) ❌
```

**Exception:** For some algorithms (neural networks), scaling target can help!

---

### Rule 3: Scale After Train-Test Split

**Order:**
```
1. Split data
2. Fit scaler on training data
3. Transform training data
4. Transform test data (using training stats)
```

---

### Rule 4: Different Features, Same Scaler (Usually)

**Usually:**
```python
# One scaler for all features
scaler = StandardScaler()
scaler.fit_transform(X)  # All features together
```

**Sometimes:**
```python
# Different scaling for different feature types
age_scaler = MinMaxScaler()  # For age
income_scaler = StandardScaler()  # For income
```

---

## When to Use Which Method?

### Decision Tree:

```
Do you have outliers?
│
├─ YES → Use Robust Scaling or Log Transform
│
└─ NO
    │
    Do you need bounded range (0-1)?
    │
    ├─ YES → Use Min-Max Normalization
    │
    └─ NO
        │
        Is data normally distributed?
        │
        ├─ YES → Use Standardization ✅ (most common)
        │
        └─ NO
            │
            Is data skewed?
            │
            ├─ YES → Use Log Transform
            │
            └─ NO → Use Standardization (default)
```

---

## Algorithms That DON'T Need Scaling

```
✅ Tree-based algorithms:
- Decision Trees
- Random Forests
- Gradient Boosting Trees (XGBoost, LightGBM)

Why? They split on thresholds, scale doesn't matter!
```

**Example:**
```
Unscaled: "If age > 30, go left"
Scaled: "If age_scaled > 0.5, go left"

Same split, just different threshold!
```

---

## Common Mistakes

### ❌ Mistake 1: Scaling Before Splitting
```python
X_scaled = scaler.fit_transform(X_all)  # Used all data!
X_train, X_test = split(X_scaled)
Problem: Test data leaked into training!
```

---

### ❌ Mistake 2: Fitting Scaler on Test Data
```python
train_scaled = scaler.fit_transform(X_train)  # ✅
test_scaled = scaler.fit_transform(X_test)    # ❌ Should be .transform()
Problem: Different scaling parameters!
```

---

### ❌ Mistake 3: Scaling Target Variable Unnecessarily
```python
scaler.fit_transform(y)  # Usually not needed
```

---

### ❌ Mistake 4: Using Wrong Scaler for Data Type
```python
# Heavily skewed data
MinMaxScaler()  # ❌ Outliers will squish everything
Log Transform + StandardScaler()  # ✅ Better!
```

---

## Practical Code Example (Concept)

```python
# Step 1: Split data
X_train, X_test, y_train, y_test = train_test_split(X, y)

# Step 2: Choose scaler
scaler = StandardScaler()  # or MinMaxScaler(), RobustScaler()

# Step 3: Fit on training data ONLY
scaler.fit(X_train)

# Step 4: Transform both sets
X_train_scaled = scaler.transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Step 5: Train model on scaled data
model.fit(X_train_scaled, y_train)

# Step 6: Predict
predictions = model.predict(X_test_scaled)
```

---

## Quick Reference Card

### For New Problem:

**1. Check your algorithm:**
```
Distance-based (KNN, SVM) → MUST scale
Tree-based (Random Forest) → Don't need to scale
Neural Networks → MUST scale
Linear Models → SHOULD scale
```

**2. Check your data:**
```
Outliers present → Robust Scaler or Log Transform
Normal distribution → Standardization
Need 0-1 range → Min-Max
Skewed → Log Transform
Sparse → MaxAbsScaler
```

**3. Default choice:**
```
When in doubt → StandardScaler ✅
Works for most cases!
```

---

## Key Takeaways

1. **Feature scaling = putting features on similar scales**

2. **Why needed:**
   - Distance-based algorithms
   - Gradient descent optimization
   - Prevent large-scale features from dominating

3. **Main methods:**
   - Min-Max: [0,1] range
   - Standardization: mean=0, std=1 (most common)
   - Robust: handles outliers
   - Log: reduces skewness

4. **Critical rules:**
   - Fit on training only
   - Transform both train and test
   - Scale after splitting data

5. **Not always needed:**
   - Tree-based algorithms don't require scaling

---

## Next Steps

Now you know feature scaling! Next topics:
- Handling missing data (advanced techniques)
- Handling imbalanced datasets
- Data augmentation strategies

---

**Remember:** Scaling is like translating languages - different measurements, same information! Make sure your algorithm speaks the same "language" for all features! 🌐📏
