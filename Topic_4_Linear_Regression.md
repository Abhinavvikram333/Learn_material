# Topic 4: Linear Regression

## What is Linear Regression?

Linear Regression is a **supervised learning algorithm** used to predict a continuous output variable (dependent variable) based on one or more input variables (independent variables). It establishes a linear relationship between input and output.

**Simple Example**: Predicting house prices based on size, or predicting salary based on years of experience.

---

## Types of Linear Regression

### 1. Simple Linear Regression
- Uses **one independent variable** to predict the dependent variable
- Formula: `y = mx + b`
  - `y` = predicted value (dependent variable)
  - `x` = input feature (independent variable)
  - `m` = slope (coefficient)
  - `b` = y-intercept (bias)

**Example**: Predicting salary based on years of experience
```
Salary = m × (Years of Experience) + b
```

### 2. Multiple Linear Regression
- Uses **multiple independent variables** to predict the dependent variable
- Formula: `y = b₀ + b₁x₁ + b₂x₂ + ... + bₙxₙ`
  - `y` = predicted value
  - `x₁, x₂, ..., xₙ` = independent variables
  - `b₀` = intercept
  - `b₁, b₂, ..., bₙ` = coefficients

**Example**: Predicting house price based on size, location, number of rooms, age
```
Price = b₀ + b₁(Size) + b₂(Location) + b₃(Rooms) + b₄(Age)
```

---

## How Does Linear Regression Work?

### Step 1: Plot the Data
- Visualize the relationship between input (x) and output (y)

### Step 2: Find the Best-Fit Line
- The algorithm finds the line that **minimizes the distance** between actual data points and predicted values
- This line represents the relationship between variables

### Step 3: Make Predictions
- Once the best-fit line is found, use it to predict new values

---

## Key Concepts

### 1. Regression Line Equation
```
y = mx + b
```
- The goal is to find the optimal values of `m` (slope) and `b` (intercept)

### 2. Cost Function (Loss Function)
**Mean Squared Error (MSE)** is commonly used:

```
MSE = (1/n) × Σ(yᵢ - ŷᵢ)²
```

Where:
- `n` = number of data points
- `yᵢ` = actual value
- `ŷᵢ` = predicted value

**Goal**: Minimize the MSE to get the best-fit line

### 3. Gradient Descent
- An optimization algorithm used to find the best values of `m` and `b`
- Iteratively adjusts parameters to minimize the cost function

**Process**:
1. Start with random values for `m` and `b`
2. Calculate the cost (error)
3. Adjust `m` and `b` to reduce the cost
4. Repeat until cost is minimized

### 4. R-Squared (R²) Score
- Measures how well the model fits the data
- Range: 0 to 1 (1 = perfect fit)
- Formula: `R² = 1 - (SS_res / SS_tot)`

---

## Assumptions of Linear Regression

1. **Linearity**: Relationship between input and output is linear
2. **Independence**: Observations are independent of each other
3. **Homoscedasticity**: Constant variance of errors
4. **Normality**: Residuals (errors) are normally distributed
5. **No Multicollinearity**: Independent variables are not highly correlated (for multiple regression)

---

## Example: Predicting Salary from Experience

### Dataset
| Years of Experience (x) | Salary (y) |
|------------------------|------------|
| 1                      | 40,000     |
| 2                      | 45,000     |
| 3                      | 50,000     |
| 5                      | 60,000     |
| 7                      | 70,000     |

### Process
1. Plot the data points
2. Find best-fit line: `Salary = 5000 × Experience + 35000`
3. Predict: For 4 years experience → `Salary = 5000 × 4 + 35000 = 55,000`

---

## Advantages

- **Simple and Easy to Understand**: Great for beginners
- **Fast Training**: Computationally efficient
- **Interpretable**: Easy to explain the relationship between variables
- **Works Well**: When there's a linear relationship in the data
- **No Hyperparameter Tuning**: Fewer parameters to adjust

---

## Disadvantages

- **Assumes Linearity**: Doesn't work well for non-linear relationships
- **Sensitive to Outliers**: Extreme values can skew the line
- **Underfitting**: May be too simple for complex patterns
- **Multicollinearity**: Problems when features are highly correlated
- **Limited to Regression**: Can only predict continuous values

---

## When to Use Linear Regression?

### Good Use Cases:
- Predicting house prices
- Sales forecasting
- Risk assessment
- Trend analysis
- Economic forecasting

### Not Suitable For:
- Classification problems (use logistic regression instead)
- Non-linear relationships (use polynomial regression or other algorithms)
- Complex patterns (use decision trees, neural networks, etc.)

---

## Implementation Steps (Conceptual)

### Using Python (scikit-learn)
```python
# Import libraries
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

# 1. Prepare data
X = [[1], [2], [3], [5], [7]]  # Features
y = [40000, 45000, 50000, 60000, 70000]  # Target

# 2. Split data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 3. Create and train model
model = LinearRegression()
model.fit(X_train, y_train)

# 4. Make predictions
predictions = model.predict(X_test)

# 5. Evaluate model
score = model.score(X_test, y_test)  # R² score
```

---

## Key Formulas Summary

| Concept | Formula |
|---------|---------|
| Simple Linear Regression | `y = mx + b` |
| Multiple Linear Regression | `y = b₀ + b₁x₁ + b₂x₂ + ... + bₙxₙ` |
| Mean Squared Error (MSE) | `MSE = (1/n) × Σ(yᵢ - ŷᵢ)²` |
| R² Score | `R² = 1 - (SS_res / SS_tot)` |

---

## Summary

Linear Regression is a fundamental machine learning algorithm that:
- Predicts continuous values based on input features
- Finds the best-fit line that minimizes error
- Works best when there's a linear relationship in data
- Is simple, fast, and interpretable
- Forms the foundation for more advanced algorithms

**Next Steps**: Practice implementing linear regression on real datasets and learn about polynomial regression for non-linear relationships.
