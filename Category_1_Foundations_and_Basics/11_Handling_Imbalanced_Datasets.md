# Handling Imbalanced Datasets

## What is an Imbalanced Dataset? ⚖️

**Simple Definition:** When one class has way more examples than another class in your data.

### Example:

**Balanced dataset (good):**
```
Email classification:
- Spam: 500 emails (50%)
- Not Spam: 500 emails (50%)
Total: 1,000 emails
Balanced! ✅
```

**Imbalanced dataset (problem):**
```
Fraud detection:
- Normal transactions: 9,900 (99%)
- Fraudulent: 100 (1%)
Total: 10,000 transactions
Very imbalanced! ⚠️
```

---

## Why is This a Problem?

### The Model's Lazy Strategy 😴

**Scenario: Credit card fraud (99% normal, 1% fraud)**

**Naive model strategy:**
```
def predict(transaction):
    return "Normal"  # Always predict Normal!

Accuracy: 99%! 🎉
```

**Wait... seems great?** 🤔

**Reality check:**
```
Caught fraud: 0 out of 100 (0%)
Model is useless! ❌
```

**The problem:** High accuracy, but completely misses what we care about (fraud)!

---

### Real-Life Analogy 🏥

**Medical diagnosis:**
```
Dataset:
- Healthy: 9,900 people (99%)
- Disease: 100 people (1%)

Model always predicts "Healthy"
→ 99% accuracy!
→ But misses ALL disease cases!
→ People die! ❌
```

**We need to catch the rare cases, even if overall accuracy drops!**

---

## How to Detect Imbalance

### Check class distribution:

```python
Class counts:
Class 0 (Normal): 9,500
Class 1 (Fraud): 500

Ratio: 9,500:500 = 19:1

Imbalance ratio: 19 ⚠️
```

### Severity levels:

```
Ratio 1:1 to 1:3    → Mild (usually OK)
Ratio 1:3 to 1:10   → Moderate (needs attention)
Ratio 1:10 to 1:100 → Severe (definitely need fixes)
Ratio > 1:100       → Extreme (very challenging)
```

---

## Strategies to Handle Imbalance

```
Main Approaches:
1. Resampling Techniques
2. Algorithm-Level Approaches
3. Evaluation Metrics Changes
4. Hybrid Methods
```

---

## 1. Resampling Techniques 🔄

### A. Random Undersampling

**What it does:** Remove examples from majority class

**Example:**
```
Before:
- Class 0 (Majority): 1,000 samples
- Class 1 (Minority): 100 samples
Ratio: 10:1

After undersampling:
- Class 0: 100 samples (randomly removed 900)
- Class 1: 100 samples
Ratio: 1:1 ✅
```

**Pros:**
- ✅ Simple and fast
- ✅ Reduces training time
- ✅ Balances dataset

**Cons:**
- ❌ Loses information (discards data!)
- ❌ May remove important examples
- ❌ Only works if you have lots of majority data

**When to use:**
- You have LOTS of majority class data
- Computational resources limited
- Quick experiment

---

### B. Random Oversampling

**What it does:** Duplicate examples from minority class

**Example:**
```
Before:
- Class 0 (Majority): 1,000 samples
- Class 1 (Minority): 100 samples
Ratio: 10:1

After oversampling:
- Class 0: 1,000 samples
- Class 1: 1,000 samples (duplicated original 100 ten times)
Ratio: 1:1 ✅
```

**Pros:**
- ✅ No information loss
- ✅ Simple to implement
- ✅ Keeps all original data

**Cons:**
- ❌ Overfitting risk (exact copies!)
- ❌ Increases training time
- ❌ Model may memorize minority examples

**When to use:**
- Can't afford to lose data
- Have enough compute power
- As a baseline approach

---

### C. SMOTE (Synthetic Minority Over-sampling Technique) ⭐

**What it does:** Create synthetic (new) minority examples, not just copies!

**How it works:**
```
1. Pick a minority sample (A)
2. Find its K nearest minority neighbors
3. Pick one neighbor (B)
4. Create new sample between A and B

New_sample = A + random(0,1) × (B - A)
```

**Visual:**
```
Original minority samples: • •
                          A   B

SMOTE creates new ones between them:
                          • * •
                          A   B
                          ↑
                      New synthetic sample!
```

**Example:**
```
Sample A: Age=25, Income=50k
Sample B: Age=30, Income=60k

New synthetic:
Age = 25 + 0.5 × (30-25) = 27.5
Income = 50k + 0.5 × (60k-50k) = 55k

New sample: Age=27.5, Income=55k ✅
```

**Pros:**
- ✅ Creates diverse new examples
- ✅ Reduces overfitting vs random oversampling
- ✅ Interpolates between real examples

**Cons:**
- ❌ Can create unrealistic samples
- ❌ Doesn't work well with high-dimensional data
- ❌ May create noise

**When to use:**
- Most common choice! ⭐
- Moderate to severe imbalance
- Numerical features

---

### D. ADASYN (Adaptive Synthetic Sampling)

**What it does:** Like SMOTE, but focuses on harder-to-learn examples

**Difference from SMOTE:**
```
SMOTE: Creates equal synthetic samples everywhere

ADASYN: Creates MORE synthetic samples in difficult regions
        (where minority samples are surrounded by majority)
```

**When to use:**
- When SMOTE doesn't work well
- Complex decision boundaries

---

## 2. Algorithm-Level Approaches 🎯

### A. Class Weights

**What it does:** Tell the algorithm that minority class mistakes are MORE costly

**Example:**
```
Normal dataset:
- Fraud error: penalty = 1
- Normal error: penalty = 1

With class weights:
- Fraud error: penalty = 10 ⚠️
- Normal error: penalty = 1

Model learns: "Missing fraud is 10× worse!"
```

**How to set weights:**
```
Weight for class = Total samples / (Number of classes × Samples in class)

Example:
Total = 10,000
Class 0 (Normal) = 9,900
Class 1 (Fraud) = 100

Weight_0 = 10,000 / (2 × 9,900) = 0.505
Weight_1 = 10,000 / (2 × 100) = 50

Fraud errors cost 50/0.505 ≈ 100× more!
```

**Pros:**
- ✅ No data modification needed
- ✅ Fast and simple
- ✅ Works with original data

**Cons:**
- ❌ Not all algorithms support it
- ❌ Needs careful tuning

**When to use:**
- First thing to try! ⭐
- Algorithm supports class weights
- Don't want to resample

---

### B. Threshold Moving

**What it does:** Change decision threshold for classification

**Example:**

**Default (threshold = 0.5):**
```
Probability > 0.5 → Predict Fraud
Probability < 0.5 → Predict Normal

Problem: Biased toward majority class!
```

**Adjusted (threshold = 0.3):**
```
Probability > 0.3 → Predict Fraud ✅ (easier to trigger!)
Probability < 0.3 → Predict Normal

Catches more fraud! 🎯
```

**How to choose threshold:**
```
1. Plot precision-recall curve
2. Choose threshold based on business needs
   - Need high precision? → Higher threshold
   - Need high recall? → Lower threshold
```

**When to use:**
- After training model
- Can adjust post-training
- Business priorities clear

---

### C. Ensemble Methods

**What it does:** Combine multiple models trained on balanced subsets

**Example: Balanced Random Forest**
```
1. Create multiple balanced subsets:
   Subset 1: All minority + random majority sample
   Subset 2: All minority + different majority sample
   Subset 3: All minority + different majority sample

2. Train separate model on each

3. Combine predictions (vote/average)
```

**Pros:**
- ✅ Uses all data
- ✅ Robust
- ✅ Often very effective

**Cons:**
- ❌ More complex
- ❌ Slower training

---

## 3. Evaluation Metrics 📊

### Why Accuracy is Misleading

**Example:**
```
Dataset: 100 fraud, 9,900 normal

Model 1: Always predicts "Normal"
Accuracy: 99%
Fraud caught: 0%

Model 2: Catches 80% of fraud, some false alarms
Accuracy: 96%
Fraud caught: 80%

Which is better? Model 2! But lower accuracy!
```

**Accuracy is useless for imbalanced data!** ❌

---

### Better Metrics:

### A. Confusion Matrix

```
                Predicted
                Fraud  Normal
Actual  Fraud   [TP]   [FN]
        Normal  [FP]   [TN]

TP = True Positive (correctly caught fraud)
FP = False Positive (false alarm)
FN = False Negative (missed fraud)
TN = True Negative (correctly identified normal)
```

---

### B. Precision

**Definition:** Of predicted fraud cases, how many were actually fraud?

**Formula:**
```
Precision = TP / (TP + FP)
```

**Example:**
```
Predicted 100 transactions as fraud
80 were actually fraud, 20 were false alarms

Precision = 80 / (80 + 20) = 80%
```

**When important:** When false alarms are costly
- Spam filter (annoying to miss real emails)
- Medical diagnosis (expensive/scary false positives)

---

### C. Recall (Sensitivity)

**Definition:** Of actual fraud cases, how many did we catch?

**Formula:**
```
Recall = TP / (TP + FN)
```

**Example:**
```
Actually 100 fraud cases
Caught 80, missed 20

Recall = 80 / (80 + 20) = 80%
```

**When important:** When missing positives is costly
- Fraud detection (missing fraud = lose money!)
- Disease detection (missing disease = death!)

---

### D. F1-Score

**Definition:** Harmonic mean of Precision and Recall

**Formula:**
```
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

**Why useful:** Balances precision and recall

**Example:**
```
Precision = 80%
Recall = 80%

F1 = 2 × (0.8 × 0.8) / (0.8 + 0.8) = 0.8 (80%)
```

**When to use:** General evaluation for imbalanced data ⭐

---

### E. ROC-AUC (Receiver Operating Characteristic - Area Under Curve)

**What it shows:** Trade-off between true positive rate and false positive rate

**Interpretation:**
```
AUC = 1.0 → Perfect model ✅
AUC = 0.9 → Excellent
AUC = 0.8 → Good
AUC = 0.7 → Fair
AUC = 0.5 → Random guessing (useless!)
```

---

## Complete Example: Credit Card Fraud Detection

### Dataset:
```
Total transactions: 10,000
- Normal: 9,800 (98%)
- Fraud: 200 (2%)

Imbalance ratio: 49:1 (severe!)
```

---

### Strategy 1: Do Nothing
```
Model: Logistic Regression (default)

Results:
Accuracy: 98%
Precision: 10%
Recall: 5%
F1-Score: 6.67%

Analysis: Useless! Only catches 5% of fraud ❌
```

---

### Strategy 2: Class Weights
```
Model: Logistic Regression with class_weight='balanced'

Results:
Accuracy: 95%
Precision: 30%
Recall: 75%
F1-Score: 43%

Analysis: Much better! Catches 75% of fraud ✅
```

---

### Strategy 3: SMOTE + Class Weights
```
1. Apply SMOTE to balance training data
2. Train with class weights

Results:
Accuracy: 96%
Precision: 40%
Recall: 85%
F1-Score: 54%

Analysis: Best so far! 🎉
```

---

### Strategy 4: Threshold Tuning
```
Same as Strategy 3, but adjust threshold from 0.5 to 0.3

Results:
Accuracy: 94%
Precision: 35%
Recall: 90% ⭐
F1-Score: 50%

Analysis: Catches 90% of fraud! Some false alarms, but acceptable.
```

---

## Decision Framework

```
1. What's your imbalance ratio?

   Mild (1:3) → Try class weights first

   Moderate (1:10) → SMOTE + class weights

   Severe (1:100) → Combination approach

2. What's more important?

   Catching all positives → Optimize for Recall

   Avoiding false alarms → Optimize for Precision

   Balance → Optimize for F1-Score

3. How much data do you have?

   Lots of data → Can try undersampling

   Limited data → Use oversampling (SMOTE)

   Very limited → Class weights + careful evaluation

4. What's your algorithm?

   Supports class weights → Use them! ✅

   Tree-based → Often handles imbalance okay

   Neural networks → Definitely need balancing
```

---

## Best Practices

### ✅ DO:

1. **Always check class distribution first**
   ```
   Know what you're dealing with!
   ```

2. **Use appropriate metrics**
   ```
   Precision, Recall, F1, AUC
   NOT just accuracy!
   ```

3. **Try multiple approaches**
   ```
   Class weights, SMOTE, undersampling
   Compare results
   ```

4. **Consider business context**
   ```
   What's the cost of false positives vs false negatives?
   ```

5. **Validate properly**
   ```
   Use stratified K-fold cross-validation
   Maintains class proportions in each fold
   ```

---

### ❌ DON'T:

1. **Rely on accuracy alone**
   ```
   99% accuracy might mean 0% fraud detection!
   ```

2. **Oversample before splitting**
   ```
   Will cause data leakage!
   Split first, then apply SMOTE to training only
   ```

3. **Ignore the business problem**
   ```
   Understand what errors cost more
   ```

4. **Use same threshold for all problems**
   ```
   Adjust based on precision/recall needs
   ```

---

## Common Mistakes

### ❌ Mistake 1: SMOTE on Test Data
```python
# WRONG:
X_resampled, y_resampled = SMOTE(X_all, y_all)
X_train, X_test = split(X_resampled)

# CORRECT:
X_train, X_test = split(X_all, y_all)
X_train_resampled = SMOTE(X_train)  # Only training!
```

---

### ❌ Mistake 2: Celebrating High Accuracy
```
"My model has 99% accuracy!"
But it predicts everything as majority class...
Check F1-score and confusion matrix!
```

---

### ❌ Mistake 3: Random Oversampling Without Consideration
```
Just duplicating minority class
→ Overfitting risk
→ Use SMOTE instead
```

---

## Quick Reference

| Scenario | Best Approach |
|----------|---------------|
| Mild imbalance (1:3) | Class weights |
| Moderate (1:10) | SMOTE + class weights |
| Severe (1:100) | Ensemble + SMOTE + weights |
| Lots of majority data | Undersampling |
| Limited data | SMOTE |
| Need high recall | Lower threshold |
| Need high precision | Higher threshold |
| Quick fix | Class weights ⭐ |

---

## Key Takeaways

1. **Imbalanced data is common** in real-world ML
   - Fraud detection, disease diagnosis, defect detection

2. **Accuracy is misleading!**
   - Use Precision, Recall, F1-Score, AUC

3. **Multiple strategies available:**
   - Resampling (under, over, SMOTE)
   - Class weights (simplest! ⭐)
   - Threshold tuning
   - Ensemble methods

4. **No one-size-fits-all:**
   - Try multiple approaches
   - Evaluate with proper metrics
   - Consider business context

5. **Always:**
   - Check class distribution
   - Use stratified splits
   - Optimize for the right metric
   - Understand false positive/negative costs

---

## Next Steps

Now you know how to handle imbalanced datasets! Next topic:
- Data augmentation (creating more training examples)
- Advanced resampling techniques
- Cost-sensitive learning

---

**Remember:** When classes are imbalanced, the minority class is usually what you care about most. Don't let the majority class dominate your model! ⚖️
