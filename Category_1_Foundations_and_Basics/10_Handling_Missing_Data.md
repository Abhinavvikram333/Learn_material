# Handling Missing Data

## What is Missing Data? 🕳️

**Simple Definition:** When some values in your dataset are empty, unknown, or not recorded.

### Real-Life Example:
```
Survey responses:
Name: John,    Age: 25,  Income: $50k
Name: Mary,    Age: ???, Income: $60k  ← Missing age!
Name: Bob,     Age: 35,  Income: ???   ← Missing income!
Name: Alice,   Age: 28,  Income: $45k
```

**Common representations:**
- Empty cell
- "NA", "N/A", "null", "None"
- "999", "-1" (placeholder values)
- " " (blank space)

---

## Why Does Data Go Missing?

### 1. Missing Completely at Random (MCAR) 🎲

**What it means:** Missing values are totally random, no pattern

**Example:**
```
Survey system crashes randomly
→ Some responses lost completely by chance
→ No relationship to any variables
```

**Impact:** Least problematic!
- Just reduces sample size
- No bias introduced

**Test:** If removing rows with missing data doesn't change your results → MCAR

---

### 2. Missing at Random (MAR) 🎯

**What it means:** Missing values relate to other observed variables, but not the missing value itself

**Example:**
```
Older people less likely to report income
→ Missing income relates to age (which we have!)
→ But not to income level itself
```

**Impact:** Moderate problem
- Can be handled with smart methods
- Use other variables to predict missing values

---

### 3. Missing Not at Random (MNAR) ⚠️

**What it means:** Missing values relate to the value that would have been observed

**Example:**
```
High earners don't report income
→ Missing income relates to income level itself!
→ Systematic pattern in what's missing
```

**Impact:** Most problematic!
- Can introduce serious bias
- Need domain knowledge to handle
- Sometimes can't be fixed with data alone

---

## Visual Example

```
Dataset: Employee Salaries

MCAR: Random computer errors
| Name  | Age | Salary |
|-------|-----|--------|
| John  | 25  | 50k    |
| Mary  | 30  | ???    | ← Random glitch
| Bob   | ???  | 70k    | ← Random glitch

MAR: Young people skip salary question
| Name  | Age | Salary |
|-------|-----|--------|
| John  | 25  | ???    | ← Young → skipped
| Mary  | 30  | 60k    |
| Bob   | 23  | ???    | ← Young → skipped

MNAR: High earners don't report
| Name  | Age | Salary |
|-------|-----|--------|
| John  | 25  | 50k    |
| Mary  | 30  | ???    | ← High earner → hides
| Bob   | 35  | 55k    |
```

---

## Impact of Missing Data

### Problems it causes:

1. **Reduced Sample Size**
   ```
   1000 rows → 300 complete rows
   Lost 70% of data!
   ```

2. **Biased Results**
   ```
   If high values systematically missing
   → Average will be too low
   ```

3. **Algorithm Errors**
   ```
   Most ML algorithms can't handle missing values
   → Will crash or give errors!
   ```

4. **Loss of Information**
   ```
   Throwing away partially complete rows
   → Waste of valuable data
   ```

---

## Methods to Handle Missing Data

```
Main Approaches:
1. Deletion Methods
2. Imputation Methods
3. Prediction Methods
4. Keeping Missing as Information
```

---

## 1. Deletion Methods 🗑️

### A. Listwise Deletion (Complete Case Analysis)

**What it does:** Remove entire row if ANY value is missing

**Example:**
```
Before:
| Age | Income | City    |
|-----|--------|---------|
| 25  | 50k    | NYC     | ✅ Keep
| ??? | 60k    | Boston  | ❌ Delete (age missing)
| 35  | ???    | NYC     | ❌ Delete (income missing)
| 28  | 45k    | Chicago | ✅ Keep

After:
| Age | Income | City    |
|-----|--------|---------|
| 25  | 50k    | NYC     |
| 28  | 45k    | Chicago |
```

**Pros:**
- ✅ Simple to implement
- ✅ No bias if MCAR

**Cons:**
- ❌ Loses a lot of data
- ❌ Reduces statistical power
- ❌ Biased if not MCAR

**When to use:**
- Very few missing values (< 5%)
- Data is MCAR
- You have lots of data to spare

---

### B. Pairwise Deletion

**What it does:** Use all available data for each calculation

**Example:**
```
Calculating correlations:
- For Age-Income: Use rows with both
- For Age-City: Use rows with both
- Different subset for each pair
```

**Pros:**
- ✅ Uses more data than listwise

**Cons:**
- ❌ Inconsistent sample sizes
- ❌ Can be confusing

---

### C. Column Deletion

**What it does:** Remove entire feature if too many values missing

**Example:**
```
Before:
| Age | Income | Phone    | City    |
|-----|--------|----------|---------|
| 25  | 50k    | ???      | NYC     |
| 30  | 60k    | ???      | Boston  |
| 35  | 70k    | ???      | Chicago |

Phone has 100% missing → Delete column!

After:
| Age | Income | City    |
|-----|--------|---------|
| 25  | 50k    | NYC     |
| 30  | 60k    | Boston  |
| 35  | 70k    | Chicago |
```

**When to use:**
- Feature has > 60-70% missing values
- Feature not critical to analysis

---

## 2. Imputation Methods 🔧

### A. Mean/Median/Mode Imputation

**What it does:** Replace missing values with mean/median/mode

**Example:**

**Mean imputation (for numerical):**
```
Ages: [25, 30, ???, 40, ???]
Mean of non-missing: (25+30+40)/3 = 31.67

After:
Ages: [25, 30, 31.67, 40, 31.67]
```

**Mode imputation (for categorical):**
```
Cities: [NYC, Boston, ???, NYC, ???]
Most frequent: NYC

After:
Cities: [NYC, Boston, NYC, NYC, NYC]
```

**Pros:**
- ✅ Simple and fast
- ✅ Preserves sample size
- ✅ Works reasonably well

**Cons:**
- ❌ Reduces variance (everyone gets same value)
- ❌ Ignores relationships between features
- ❌ Can distort distributions

**When to use:**
- Quick solution needed
- Few missing values
- Preliminary analysis

---

### B. Forward Fill / Backward Fill

**What it does:** Use previous/next value (for time series)

**Forward fill:**
```
Time series: [10, 12, ???, ???, 18]
Forward: [10, 12, 12, 12, 18]
         Copy 12 forward →
```

**Backward fill:**
```
Time series: [10, ???, ???, 18, 20]
Backward: [10, 18, 18, 18, 20]
              ← Copy 18 backward
```

**When to use:**
- Time series data
- Values don't change rapidly
- Missing values are consecutive

---

### C. Interpolation

**What it does:** Estimate value based on surrounding values

**Linear interpolation:**
```
Values: [10, ???, ???, 40]
Time:   [0,  1,   2,   3]

Missing at t=1: 10 + (40-10)/3 * 1 = 20
Missing at t=2: 10 + (40-10)/3 * 2 = 30

After: [10, 20, 30, 40]
```

**When to use:**
- Time series or ordered data
- Smooth trends
- Values change gradually

---

## 3. Advanced Prediction Methods 🎯

### A. K-Nearest Neighbors (KNN) Imputation

**What it does:** Find similar rows and use their values

**Example:**
```
Find person with missing income:
Person A: Age=30, City=NYC, Income=???

Find K=3 most similar people:
Person B: Age=28, City=NYC, Income=55k  (similar!)
Person C: Age=32, City=NYC, Income=60k  (similar!)
Person D: Age=29, City=NYC, Income=58k  (similar!)

Average their incomes: (55k+60k+58k)/3 = 57.67k
Fill Person A's income: 57.67k
```

**Pros:**
- ✅ Uses relationships between features
- ✅ More accurate than mean
- ✅ Adapts to local patterns

**Cons:**
- ❌ Computationally expensive
- ❌ Needs scaled features
- ❌ Doesn't work well with many missing values

---

### B. Regression Imputation

**What it does:** Predict missing values using other features

**Example:**
```
Missing: Income
Available: Age, Education, City

1. Train regression: Income = f(Age, Education, City)
   Using rows where Income is not missing

2. Predict missing incomes using the model

Person with Age=30, Masters, NYC
→ Predicted Income = $65k
```

**Pros:**
- ✅ Very accurate
- ✅ Uses all available information
- ✅ Preserves relationships

**Cons:**
- ❌ Complex
- ❌ Assumes relationships are correct
- ❌ Can overfit

---

### C. Multiple Imputation

**What it does:** Create multiple complete datasets with different imputations

**Process:**
```
1. Create 5 different imputed datasets
   - Each fills missing values slightly differently
   - Adds randomness

2. Train model on each dataset separately

3. Combine results (average predictions)
```

**Pros:**
- ✅ Most statistically sound
- ✅ Accounts for uncertainty
- ✅ Gets confidence intervals

**Cons:**
- ❌ Complex
- ❌ Computationally expensive
- ❌ Requires statistical expertise

---

## 4. Treating Missing as Information 💡

### A. Missing Indicator

**What it does:** Create new binary feature indicating if value was missing

**Example:**
```
Original:
| Age | Income |
|-----|--------|
| 25  | 50k    |
| 30  | ???    |

After adding indicator:
| Age | Income | Income_Missing |
|-----|--------|----------------|
| 25  | 50k    | 0              |
| 30  | 55k*   | 1              | ← Filled + flagged

* Filled with mean/median
```

**Why useful?**
```
Sometimes "missing" itself is informative!
Example: People who refuse to answer income question
→ Might be very high or very low earners
→ Missing = meaningful pattern!
```

---

### B. Separate Category for Missing

**What it does:** Treat "missing" as its own category

**Example:**
```
Education: [High School, College, ???, Masters, ???]

After:
Education: [High School, College, Unknown, Masters, Unknown]
                                   ↑ New category
```

**When to use:**
- Categorical variables
- Missing is not random
- Missing has meaning

---

## Decision Framework

### Step-by-Step Guide:

```
1. How much data is missing?

   < 5% → Can safely delete rows (listwise deletion)

   5-20% → Imputation methods

   > 20% in a column → Consider deleting column

   > 40% overall → Need careful investigation

2. What type of data?

   Numerical → Mean/Median/KNN/Regression

   Categorical → Mode/Separate category

   Time Series → Forward fill/Interpolation

3. Why is it missing?

   MCAR → Any method works

   MAR → Advanced methods (KNN, Regression)

   MNAR → Domain knowledge + careful handling

4. How important is accuracy?

   Quick analysis → Mean/Median

   Important decisions → KNN/Regression/Multiple imputation

   Critical applications → Multiple imputation + expert review
```

---

## Complete Example: Customer Dataset

### Original Data:
```
| ID | Age | Income | Purchase | Days_Since_Visit |
|----|-----|--------|----------|------------------|
| 1  | 25  | 50k    | Yes      | 5                |
| 2  | ??? | 60k    | No       | 10               |
| 3  | 35  | ???    | Yes      | ???              |
| 4  | 28  | 45k    | Yes      | 3                |
| 5  | ??? | ???    | No       | 15               |
```

---

### Analysis:

**1. Check missing percentages:**
```
Age: 2/5 = 40% missing ⚠️
Income: 2/5 = 40% missing ⚠️
Days_Since_Visit: 1/5 = 20% missing ⚠️
```

**2. Apply strategies:**

**Age (40% missing, numerical):**
```
Strategy: Mean imputation + missing indicator
Mean age = (25+35+28)/3 = 29.33

| Age   | Age_Missing |
|-------|-------------|
| 25    | 0           |
| 29.33 | 1           |← Filled + flagged
| 35    | 0           |
| 28    | 0           |
| 29.33 | 1           |← Filled + flagged
```

**Income (40% missing, numerical, might be MNAR):**
```
Strategy: KNN imputation (use Age, Purchase to predict)
Find similar customers and use their income
```

**Days_Since_Visit (20% missing, numerical):**
```
Strategy: Median imputation
Median = 7.5 days
```

---

### Final Clean Data:
```
| ID | Age   | Income | Purchase | Days | Age_Miss | Income_Miss |
|----|-------|--------|----------|------|----------|-------------|
| 1  | 25    | 50k    | Yes      | 5    | 0        | 0           |
| 2  | 29.33 | 60k    | No       | 10   | 1        | 0           |
| 3  | 35    | 52k*   | Yes      | 7.5  | 0        | 1           |
| 4  | 28    | 45k    | Yes      | 3    | 0        | 0           |
| 5  | 29.33 | 51k*   | No       | 15   | 1        | 1           |

* Imputed using KNN
```

---

## Best Practices

### ✅ DO:

1. **Understand WHY data is missing**
   - Random? Systematic? Meaningful?

2. **Visualize missing patterns**
   - See if there are patterns
   - Check correlations

3. **Document your approach**
   - What you did and why
   - How to reproduce

4. **Try multiple methods**
   - Compare results
   - Pick best for your case

5. **Keep original data**
   - Never overwrite
   - Always keep backup

6. **Consider missing indicators**
   - Missing itself can be informative!

---

### ❌ DON'T:

1. **Delete data without thinking**
   - Understand impact first

2. **Always use mean imputation**
   - Lazy approach, often not best

3. **Ignore patterns**
   - MNAR needs special handling

4. **Impute before splitting**
   - Data leakage! Split first!

5. **Forget to scale after imputation**
   - Imputed values might change scale

---

## Common Mistakes

### ❌ Mistake 1: Imputing Before Train-Test Split
```python
# WRONG:
df = impute_missing(df)  # All data!
train, test = split(df)

# CORRECT:
train, test = split(df)
imputer.fit(train)  # Learn from train only
train = imputer.transform(train)
test = imputer.transform(test)
```

---

### ❌ Mistake 2: Using Mean When Data is Skewed
```
Incomes: [30k, 35k, 40k, ???, 1M]
Mean = 276k (way too high because of outlier!)
Median = 37.5k (more representative)
```

---

### ❌ Mistake 3: Ignoring Domain Knowledge
```
Age = -5 (clearly error!)
Filling with mean = bad
Should: Remove or investigate source
```

---

## Quick Reference

| Situation | Best Method |
|-----------|-------------|
| < 5% missing, MCAR | Listwise deletion |
| Numerical, few missing | Mean/Median |
| Categorical, few missing | Mode |
| Time series | Forward fill / Interpolation |
| Many missing, MAR | KNN / Regression |
| Missing has meaning | Missing indicator |
| Critical analysis | Multiple imputation |
| Column > 70% missing | Delete column |

---

## Key Takeaways

1. **Missing data is common** - almost all real datasets have it!

2. **Understand the type:**
   - MCAR (random) → Easy to handle
   - MAR (related to other variables) → Need smart methods
   - MNAR (related to missing value) → Hardest

3. **Many strategies available:**
   - Deletion (simple, loses data)
   - Imputation (mean, KNN, regression)
   - Missing indicators (missing = information)

4. **No one-size-fits-all:**
   - Depends on: amount missing, type of data, why missing

5. **Always:**
   - Investigate patterns
   - Document approach
   - Split before imputing
   - Consider domain knowledge

---

## Next Steps

Now you know how to handle missing data! Next topics:
- Handling imbalanced datasets
- Data augmentation techniques
- Advanced preprocessing methods

---

**Remember:** Missing data is not always a problem - it's often information! Choose your strategy based on understanding WHY data is missing, not just THAT it's missing! 🕵️
