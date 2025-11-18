# Data Preprocessing & Cleaning

## Why is This Important? 🧹

**"Garbage in, garbage out!"**

Even the best ML algorithm will fail with bad data. Data preprocessing is like preparing ingredients before cooking - it's 50-80% of the work but absolutely essential!

### Cooking Analogy 👨‍🍳
Before cooking:
- Wash vegetables (clean data)
- Chop to uniform size (normalize)
- Remove rotten parts (handle outliers)
- Organize ingredients (structure data)

Skip these steps → Bad meal, no matter how good the recipe!

---

## What is Data Preprocessing?

**Simple Definition:** Transforming raw data into a clean, organized format that ML algorithms can understand and learn from effectively.

### Raw Data vs Clean Data

**Raw Data (messy!):**
```
| Name  | Age | Salary | City      |
|-------|-----|--------|-----------|
| john  | 25  | 50000  | new york  |
| MARY  | ??? | 60,000 | Boston    |
| Bob   | -5  | 75k    | NEW YORK  |
| alice | 30  | NULL   | boston    |
```
Problems: Inconsistent capitalization, missing values, errors, different formats!

**Clean Data:**
```
| Name  | Age | Salary | City     |
|-------|-----|--------|----------|
| John  | 25  | 50000  | New York |
| Mary  | 28  | 60000  | Boston   |
| Bob   | 35  | 75000  | New York |
| Alice | 30  | 55000  | Boston   |
```
Better: Consistent format, no missing values, corrected errors!

---

## The Data Preprocessing Pipeline

```
Raw Data
   ↓
1. Data Cleaning
   ↓
2. Data Transformation
   ↓
3. Data Reduction
   ↓
4. Data Integration
   ↓
Clean Data → Ready for ML!
```

---

## Step 1: Data Cleaning 🧼

### What You Do
Fix or remove errors, inconsistencies, and issues in your data.

---

### A. Handling Missing Values

**Problem:** Some data points are empty or missing

**Example:**
```
Age: 25, 30, ???, 28, ???
```

**Solutions:**

**1. Remove rows with missing data**
```
Before: [25, 30, ???, 28, ???]
After:  [25, 30, 28]

When to use: You have lots of data and few missing values
Downside: Lose information
```

**2. Fill with mean/median/mode**
```
Ages: [25, 30, ???, 28, ???]
Mean = (25+30+28)/3 = 27.67

After: [25, 30, 27.67, 28, 27.67]

When to use: Numerical data, random missing values
```

**3. Fill with most frequent value (mode)**
```
Cities: [NYC, Boston, ???, NYC, ???]
Most frequent: NYC

After: [NYC, Boston, NYC, NYC, NYC]

When to use: Categorical data
```

**4. Forward fill / Backward fill**
```
Time series: [10, 12, ???, 15, ???]
Forward fill: [10, 12, 12, 15, 15]

When to use: Time series data
```

**5. Predict missing values**
```
Use other features to predict the missing value
Most sophisticated approach!
```

---

### B. Removing Duplicates

**Problem:** Same data appears multiple times

**Example:**
```
Before:
| ID | Name  | Age |
|----|-------|-----|
| 1  | John  | 25  |
| 2  | Mary  | 30  |
| 3  | John  | 25  | ← Duplicate!
| 4  | Bob   | 35  |

After:
| ID | Name  | Age |
|----|-------|-----|
| 1  | John  | 25  |
| 2  | Mary  | 30  |
| 4  | Bob   | 35  |
```

**Why important:** Duplicates bias your model toward those examples!

---

### C. Fixing Inconsistencies

**Problem:** Same thing represented differently

**Examples:**

**1. Capitalization:**
```
Before: "new york", "New York", "NEW YORK"
After:  "New York", "New York", "New York"
```

**2. Abbreviations:**
```
Before: "USA", "U.S.A.", "United States"
After:  "USA", "USA", "USA"
```

**3. Data types:**
```
Before: "75k", "75000", "$75,000"
After:  75000, 75000, 75000
```

**4. Units:**
```
Before: 5 feet, 60 inches, 1.52 meters
After:  152 cm, 152 cm, 152 cm
```

---

### D. Handling Outliers

**Problem:** Extreme values that don't fit the pattern

**Example:**
```
Ages: [25, 28, 30, 27, 250, 29, 26]
                      ↑
                  Outlier! (Probably error)
```

**Detection Methods:**

**1. Statistical method (Z-score):**
```
If value is more than 3 standard deviations from mean
→ It's an outlier
```

**2. IQR method (Interquartile Range):**
```
Q1 = 25th percentile
Q3 = 75th percentile
IQR = Q3 - Q1

Outliers:
- Below Q1 - 1.5*IQR
- Above Q3 + 1.5*IQR
```

**3. Visual inspection:**
```
Plot the data
See extreme values
Use domain knowledge
```

**Solutions:**

**Option 1: Remove outliers**
```
When: Clearly errors or noise
```

**Option 2: Cap outliers**
```
Set maximum/minimum values
Age > 100? Set to 100
```

**Option 3: Transform data**
```
Use log transformation to reduce impact
log(1000) = 3, much smaller!
```

**Option 4: Keep them**
```
When: Outliers are valid and important
Example: Detecting fraud (fraud is an outlier!)
```

---

## Step 2: Data Transformation 🔄

### What You Do
Convert data into better format for ML algorithms

---

### A. Encoding Categorical Variables

**Problem:** ML algorithms need numbers, not text!

**Example:**
```
Colors: ["Red", "Blue", "Green", "Red"]
Need to convert to numbers!
```

**Solutions:**

**1. Label Encoding** (for ordinal data)
```
Size: ["Small", "Medium", "Large", "Small"]
Encoded: [0, 1, 2, 0]

When to use: Categories have meaningful order
Example: T-shirt sizes, education levels
```

**2. One-Hot Encoding** (for nominal data)
```
Color: ["Red", "Blue", "Red"]

After encoding:
| Red | Blue | Green |
|-----|------|-------|
| 1   | 0    | 0     |
| 0   | 1    | 0     |
| 1   | 0    | 0     |

When to use: No meaningful order
Example: Colors, countries, categories
```

**3. Binary Encoding**
```
For many categories (more efficient than one-hot)
```

---

### B. Feature Scaling (Covered in detail in Topic 9)

**Quick overview:**
```
Before: Age = 25, Income = 50000
After:  Age = 0.25, Income = 0.50

Why: Put all features on similar scale
```

---

### C. Binning/Discretization

**Problem:** Convert continuous values to categories

**Example:**
```
Ages: [15, 22, 35, 45, 67, 80]

Create bins:
- Young: 0-25
- Adult: 26-60
- Senior: 60+

Result: [Young, Young, Adult, Adult, Senior, Senior]

When to use:
- Reduce noise
- Capture non-linear patterns
- Make interpretation easier
```

---

### D. Log Transformation

**Problem:** Skewed data (values bunched on one side)

**Example:**
```
Income: [30k, 35k, 40k, 45k, 1M]
         ↑ Most values here  ↑ Outlier

After log:
log(30k) = 4.48
log(35k) = 4.54
log(40k) = 4.60
log(45k) = 4.65
log(1M) = 6.00

More evenly distributed!
```

**When to use:**
- Income, house prices, population
- Any data that varies by orders of magnitude

---

## Step 3: Data Reduction 📉

### What You Do
Reduce data size while keeping important information

---

### A. Feature Selection

**Goal:** Keep only useful features

**Methods:**

**1. Correlation analysis**
```
Remove features highly correlated with each other
Example: "Square feet" and "Number of rooms" often correlated
```

**2. Feature importance**
```
Train simple model
See which features matter most
Remove unimportant ones
```

**3. Domain knowledge**
```
Use your expertise
"Shoe size doesn't affect salary" → Remove it
```

---

### B. Dimensionality Reduction

**Techniques:**
- PCA (Principal Component Analysis)
- t-SNE
- Autoencoders

**Goal:** Combine features into fewer, more meaningful ones

---

### C. Sampling

**When you have TOO much data:**

**Random sampling:**
```
From 1 million rows → Use random 100,000
```

**Stratified sampling:**
```
Keep same proportions
If 30% are category A, sample should be 30% category A
```

---

## Step 4: Data Integration 🔗

### What You Do
Combine data from multiple sources

**Example:**
```
Source 1: Customer purchases
Source 2: Customer demographics
Source 3: Website clicks

Combined: Complete customer profile
```

**Challenges:**
- Different formats
- Different schemas
- Duplicate entities
- Different scales

**Solution:** Careful merging and standardization

---

## Common Data Quality Issues

### Issue 1: Inconsistent Dates
```
Before:
- "12/03/2023" (US format: Dec 3)
- "12/03/2023" (EU format: Mar 12)

After:
- "2023-03-12" (ISO standard)
```

---

### Issue 2: Different Scales
```
Before:
- Temperature: -10 to 40 (Celsius)
- Humidity: 0 to 100 (Percent)
- Pressure: 900 to 1100 (hPa)

After: All scaled to 0-1 range
```

---

### Issue 3: Text Data
```
Before: "Great product!!!", "great  product"
After: "great product", "great product"

Steps:
- Lowercase
- Remove punctuation
- Remove extra spaces
- Remove stop words
```

---

## Complete Example: House Prices Dataset

### Raw Data:
```
| Size     | Rooms | Price  | City      | Built |
|----------|-------|--------|-----------|-------|
| 1500 sqft| 3     | $300k  | NEW YORK  | 2010  |
| ???      | 2     | 250000 | boston    | ???   |
| 2000 sq  | 4     | NULL   | New York  | 1985  |
| 1200     | -1    | 200k   | BOSTON    | 2015  |
```

### Step-by-Step Cleaning:

**1. Fix inconsistencies:**
```
- Standardize units: All sizes in sqft
- Standardize format: All prices as numbers
- Standardize text: "New York", "Boston"
```

**2. Handle missing values:**
```
- Size missing: Fill with median (1500)
- Built year missing: Fill with median (2010)
- Price missing: Remove row (can't predict without target!)
```

**3. Fix errors:**
```
- Rooms = -1: Error! Replace with median (3)
```

**4. Encode categories:**
```
City:
| New York | Boston |
|----------|--------|
| 1        | 0      |
| 0        | 1      |
| 1        | 0      |
| 0        | 1      |
```

**5. Scale features:**
```
All numerical values scaled to 0-1 range
```

### Clean Data:
```
| Size | Rooms | Price  | City_NY | City_Boston | Age |
|------|-------|--------|---------|-------------|-----|
| 1500 | 3     | 300000 | 1       | 0           | 14  |
| 1500 | 2     | 250000 | 0       | 1           | 14  |
| 2000 | 4     | 275000 | 1       | 0           | 39  |
| 1200 | 3     | 200000 | 0       | 1           | 9   |
```

Now ready for ML! ✅

---

## Best Practices

### ✅ DO:
1. **Understand your data first**
   - Look at samples
   - Check statistics (mean, min, max)
   - Visualize distributions

2. **Document everything**
   - What you changed
   - Why you changed it
   - How to reproduce

3. **Split data BEFORE preprocessing**
   - Avoid data leakage!
   - Fit preprocessing on training data only

4. **Keep raw data**
   - Never overwrite original
   - Always keep backup

5. **Validate cleaning**
   - Check results make sense
   - Spot-check random samples

---

### ❌ DON'T:
1. **Delete data without thinking**
   - Understand why it's missing first

2. **Use test data for decisions**
   - Data leakage!

3. **Ignore domain knowledge**
   - "Age = 250" is obviously wrong

4. **Over-clean**
   - Some "noise" might be signal!

5. **Forget to save preprocessing steps**
   - Need same steps for new data!

---

## Checklist for Data Preprocessing

```
☐ Load and inspect data
☐ Check for missing values
☐ Check for duplicates
☐ Check data types
☐ Check for outliers
☐ Check for inconsistencies
☐ Handle missing values
☐ Remove duplicates
☐ Fix inconsistencies
☐ Handle outliers
☐ Encode categorical variables
☐ Scale numerical features
☐ Create new features (if needed)
☐ Remove irrelevant features
☐ Split into train/val/test
☐ Validate cleaned data
☐ Document all changes
```

---

## Tools & Libraries (Python)

```python
# Pandas - Data manipulation
import pandas as pd

# NumPy - Numerical operations
import numpy as np

# Scikit-learn - Preprocessing tools
from sklearn.preprocessing import StandardScaler
from sklearn.preprocessing import LabelEncoder
from sklearn.impute import SimpleImputer
```

---

## Key Takeaways

1. **Data preprocessing is 50-80% of ML work**
   - Most important step!

2. **Four main steps:**
   - Cleaning (fix errors)
   - Transformation (convert formats)
   - Reduction (remove redundancy)
   - Integration (combine sources)

3. **Common tasks:**
   - Handle missing values
   - Remove duplicates
   - Fix inconsistencies
   - Handle outliers
   - Encode categories
   - Scale features

4. **Always:**
   - Understand data first
   - Document changes
   - Keep raw data
   - Split before preprocessing
   - Validate results

5. **"Garbage in, garbage out"**
   - Good data → Good models
   - Bad data → Bad models (always!)

---

## Next Steps

Now you know how to clean data! Next topics:
- Feature Engineering (creating new features)
- Feature Scaling (detailed techniques)
- Handling Missing Data (advanced methods)

---

**Remember:** No amount of fancy algorithms can fix bad data. Clean data is the foundation of successful ML! 🧹✨
