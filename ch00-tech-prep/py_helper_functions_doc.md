# Python Helper Functions Documentation

This document provides a student-friendly reference for the helper functions found in `py_helper_functions.py`.
These functions are customized for specific case studies (often involving **Price** prediction, like Used Cars or Hotels), which is reflected in some hardcoded plot labels.

## Global Settings

The script defines global variables and a custom theme:

* **Colors**: A palette `color` (e.g., `#3a5e8cFF`) for consistent visuals.
* **Theme**: `da_theme` sets `matplotlib` defaults (white background, gray spines).

---

## Statistical & Utility Functions

### `seq(start, stop, by, round_n=3)`

Generates a sequence of numbers, primarily used for defining custom **scale breaks** in plots.

* **Usage**: Create a list of tick marks or bin edges.
* **Example**:

    ```python
    # Create breaks for a plot axis from 0 to 1 in steps of 0.2
    scale_breaks = seq(0, 1, 0.2)
    # Result: [0.0, 0.2, 0.4, 0.6, 0.8, 1.0]
    ```

### `skew(l, round_n=3)`

Calculates the skewness of a distribution.

* **Formula**: $\frac{\text{Mean} - \text{Median}}{\text{Standard Deviation}}$
* **Note**: This is a simple measure of skewness (Pearson's second coefficient), distinct from the third moment.
* **Example**:

    ```python
    prices = [100, 120, 150, 600] # Right-skewed data
    print(skew(prices))
    ```

### `tabulate(series, drop_missing=False)`

Generates a frequency table for a categorical variable.

* **Returns**: A DataFrame with counts (`Freq.`), percentages (`Perc.`), and cumulative percentages (`Cum.`).
* **Example**:

    ```python
    # Analyze the distribution of car conditions
    conditions = pd.Series(["Excellent", "Good", "Good", "Fair", "Excellent"])
    freq_table = tabulate(conditions)
    # Output shows how common each condition is
    ```

### `bs_linreg(x, y, size=1, seed=200999)`

Performs **bootstrap** sampling to estimate the uncertainty (variability) of linear regression coefficients (slope and intercept).

* **Context**: Used to see how much the relationship between two variables (e.g., Price vs Age) might vary due to random sampling.
* **Example**:

    ```python
    # Estimate variability in the relationship between Age and Price
    slopes, intercepts = bs_linreg(df['age'], df['price'], size=100)
    print(f"Slope 95% CI: {np.percentile(slopes, [2.5, 97.5])}")
    ```

### `lspline(series, knots)`

Generates linear spline basis functions. This splits a variable into different "slope regions" based on specific cutoff points (**knots**).

* **Context**: Essential for "Piecewise Linear Regression". For example, the depreciation of a car might be faster in the first 2 years than in later years. You would place a knot at 2.
* **Example**:

    ```python
    # Create spline features for 'age' with knots at 2 and 5 years
    # This allows the model to learn different depreciation rates for 0-2, 2-5, and 5+ years
    age_splines = lspline(df['age'], knots=[2, 5])
    ```

### `poly(x, degree=1)`

Generates polynomial features ($x, x^2, x^3...$).

* **Context**: Used for fitting non-linear curves (e.g., quadratic) in a linear regression framework, often for visualization or simple prediction.
* **Example**:

    ```python
    # Create raw polynomial features up to degree 2 (quadratic)
    x_poly = poly(df['age'], degree=2)
    ```

### `ols_crossvalidator(formula, data, n_folds=5, average_rmse=True)`

Runs **K-Fold Cross-Validation** on an OLS linear regression model.

* **Purpose**: To estimate how well a model generalizes to new, unseen data (evaluating overfitting).
* **Returns**: Train/Test RMSE, $R^2$, BIC.
* **Example**:

    ```python
    # Check if a complex model overfits compared to a simple one
    results = ols_crossvalidator("price ~ age + age_sq + brand", data=cars_df, n_folds=4)
    print(f"Test RMSE: {results['Test RMSE']}")
    ```

### `point_predict_with_conf_int(regression, new_datapoint, interval_precision=0.95, round_n=2)`

Predicts the outcome for a specific new observation and provides a **Confidence Interval**.

* **Example**:

    ```python
    # Predict the price of a specific 5-year-old Toyota
    new_car = pd.DataFrame({'age': [5], 'brand': ['Toyota']})
    prediction = point_predict_with_conf_int(fitted_model, new_car)
    # Returns: {'Point prediction': 15000, 'Prediction Interval (95%)': [14000, 16000]}
    ```

### `create_sample_frame(vector, sample_size, n_samples=10000, with_replacement=False, seed=42)`

Draws many samples from a population.

* **Context**: Used for simulations (e.g., investigating the law of large numbers or modifying the Central Limit Theorem) or bootstrapping.
* **Example**:

    ```python
    # Draw 1000 random samples of size 30 from a population of returns
    samples = create_sample_frame(returns_vector, sample_size=30, n_samples=1000, with_replacement=True)
    ```

### `pool_and_categorize_continuous_variable(series, pools, categories, closed="left")`

Bins a continuous variable into custom categories.

* **Example**:

    ```python
    # Group car probability of default into 'Low', 'Medium', 'High' risk
    risk_groups = pool_and_categorize_continuous_variable(
        df['prob_default'], 
        pools=[(0, 0.2), (0.2, 0.5), (0.5, 1.0)], 
        categories=["Low Risk", "Medium Risk", "High Risk"]
    )
    ```

---

## Plotting & Visualization

**Note:** Some of these functions have **hardcoded axis labels** (Usage: Pricing Models).

### `plot_bar(df, x, y, hue, ax, ...)`

Creates a bar plot for interactions.

* **Hardcoded Label**: The Y-axis is labeled "**Mean Price**".
* **Context**: Designed specifically to visualize how categorical features (e.g., Brand, Condition) affect Price.
* **Example**:

    ```python
    # Plot Mean Price by City, grouped by Apartment Type
    plot_bar(df, x="city", y="price", hue="d_apartment", ax=ax)
    ```

### `plot_interactions(df, plot_config)`

Grid of bar plots.

* **Context**: Visualizing multiple price drivers at once.
* **Example**:

    ```python
    plot_config = [
        {"x": "brand", "y": "price", "hue": "fuel_type"},
        {"x": "condition", "y": "price", "hue": "transmission"}
    ]
    plot_interactions(cars_df, plot_config)
    ```

### `plot_model_predictions(model, df, x_col, y_col, ...)`

Overlays model predictions on a scatter plot.

* **Hardcoded Labels**: X-axis "**Age (years)**", Y-axis "**Price (US dollars)**".
* **Context**: Specifically for **Age-Price** depreciation curves. Do not use for other variables unless you ignore the labels.
* **Example**:

    ```python
    # Visualize how a Decision Tree models the depreciation of cars over Age
    plot_model_predictions(tree_model, cars_df, x_col="age", y_col="price")
    ```

### `create_calibration_plot(data, prob_var, actual_var, ...)`

Evaluates probability predictions (Classification).

* **Labels**: X-axis "Predicted event probability", Y-axis "**Actual event probability**".
* **Context**: Checking if a model is "well-calibrated". For example, if a model predicts 70% risk of default, do 70% of such cases actually default?
* **Example**:

    ```python
    create_calibration_plot(loan_data, prob_var="pred_default_prob", actual_var="default_binary")
    ```

### `plot_partial_dependence(model, data, variable, varlabel)`

Effect of one feature on the prediction, marginalizing over others.

* **Hardcoded Label**: Y-axis "**Predicted price**".
* **Context**: Interpreting "Black Box" models (like Random Forest) to see how a feature like 'Horsepower' affects the predicted Price.
* **Example**:

    ```python
    # How does horsepower affect price in our Random Forest?
    plot_partial_dependence(rf_model, cars_df, variable="horsepower", varlabel="Horsepower")
    ```

### `plot_variable_importance(data, x="imp_percentage", y="varname", ...)`

Lollipop chart for Feature Importance.

* **Example**:

    ```python
    # Visualize which features drive the price the most
    plot_variable_importance(importance_df)
    ```

### `plot_shap_interactions(features, shap_values)`

Visualizes complex interactions identified by SHAP values.

* **Context**: Advanced model interpretation.
* **Example**:

    ```python
    # See how Age acts differently depending on Car Condition
    plot_shap_interactions([("age", "Age"), ("condition", "Condition")], shap_values)
    ```
