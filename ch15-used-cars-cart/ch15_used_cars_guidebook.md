# 🗺️ Guidebook: CH15A Predicting used car value with regression trees

**Author:** Bellonda v3.0 | **Focus:** Data Wrangling & Regression Trees (CART)

---

## 🏛️ 1. The Big Picture (Economic Strategy)

**The Business Question:** How do car characteristics (age, condition, features) non-linearly affect their market value, and can we predict prices better than linear models?
**The Hypothesis:** Depreciation is not linear (a 1-year-old car loses value differently than a 10-year-old car), and features interact in complex ways (e.g., "Excellent" condition mainly matters for newer cars). Regression Trees can capture these patterns automatically.
**The Dataset:** `used-cars_2cities_prep.csv` - A transactional dataset of Toyota Camry sales in Chicago and Denver (N=~1000+ inferred), focusing on Gas vehicles.

---

## 🌊 2. Data Flow Architecture

*Visualizing how data moves through this notebook:*

1. **Ingestion:** `used-cars_2cities_prep.csv`
2. **Transformation (Filtering):**
    * `fuel == 'gas'` (Removing Hybrids)
    * `condition not in ['new', 'fair']` (Removing extremes)
    * `transmission != 'manual'` (Focusing on Automatic)
3. **Feature Engineering:**
    * **Dummies:** `LE`, `XLE`, `SE`, `cond_excellent`, `chicago`
    * **Polynomials:** `agesq`, `agecu` (Created but potentially sidelined for Trees)
4. **Split:** Train/Test (70/30) with `random_state=123`.
5. **Analysis:**
    * **Model 1:** Decision Stump (Depth=1, Age only)
    * **Model 2:** Linear Regression (`feols`)
    * **Model 3:** Full Tree (`max_depth=3`)
    * **Model 4:** Pruned Tree (Cost-Complexity Pruning `ccp_alpha`)

---

## 🎬 3. The Walkthrough (Step-by-Step)

### 🔹 Act I: Preparation & Ingestion

* **Goal:** Clean the "raw scrap" of web-scraped car data into a "refined metal" ready for modeling.
* **Key Pattern:** `data.loc[mask]` for rigid filtering.
* **Architect's Note:** The analyst aggressively filters for a homogenous product (Gas, Auto, Camry). This reduces variance but limits the model's external validity (it won't work for Hybrids).

### 🔹 Act II: The Core Transformation

* **The Logic:** "Dummy Encoding" and "Domain Constraints".
  * Missing values in `cylinders` or `condition` are filled or dropped.
  * **Polynomials:** `agesq` and `agecu` are generated.
* **The Trap:** *Redundant Complexity.* While generated, these polynomial features (`age^2`) are strictly necessary for *Linear Regression* to handle curves. **Decision Trees**, however, do not need them; they can approximate curves by splitting `age` at multiple points (step-functions). Including them for trees is harmless but computationally wasteful.
* **Code Highlight:**

    ```python
    # Dropping outliers manually before modeling
    data = data[data.price < 25000]
    data = data[data.odometer < 120000]
    ```

    > This manual "Trimming" removes outliers that could skew the Mean Squared Error (MSE), which Trees try to minimize.

### 🔹 Act III: Analysis & Inference

* **The Model:** Regression Tree (CART).
  * Equation-ish: $\hat{y} = \sum_{m} c_m I(x \in R_m)$ (Prediction is the average price of cars in a specific leaf $R_m$).
* **Library Nuance:** `sklearn.tree.DecisionTreeRegressor`.
  * Uses `random_state=20108` to ensure the tree splits consistently across runs.
* **Pruning the Overfit:**
  * The notebook moves from a simple tree to a huge tree, then **Prunes** it using `ccp_alpha=30000`.
  * **Why?** A massive tree memorizes the training data (Overfitting). Pruning cuts off weak branches that don't improve prediction enough to justify their complexity.
* **Interpretation:** The code uses `permutation_importance` at the very end. unlike regression coefficients ($\beta$), trees don't give direction (+/-). Variable Importance tells us *which* factors (e.g., `age`, `odometer`) were used most often or reduced error the most.

---

## 🛠️ 4. Technical Cheatsheet (For this Notebook)

| Concept | Code Pattern | Lingo |
| :--- | :--- | :--- |
| **Model Init** | `DecisionTreeRegressor(max_depth=3)` | "Hyperparameter" |
| **Pruning** | `DecisionTreeRegressor(ccp_alpha=X)` | "Regularization" |
| **Importance** | `permutation_importance(model, X, y)` | "Feature Attribution" |
| **Splitting** | `train_test_split(..., test_size=0.3)` | "Hold-out validation" |

---

## ⚠️ 5. Critical Warnings
>
> [!CAUTION]
>
> * **Data Loss:** The filter `condition not in ['new', 'fair']` discards the bottom and top of the market. This model will fail if used to price a "Fixer-Upper" or a "Mint Condition" showroom car.
> * **Bias:**
    ***Geographic:** The dataset is limited to Chicago and Denver ("2cities"). Prices in NYC or LA could be drastically different due to rust (road salt) or demand.
    *   **Survivor Bias:** By dropping cars with extremely high odometers (`>120k` inferred from cuts), we model only the "survivors," potentially underestimating depreciation at high mileage.
