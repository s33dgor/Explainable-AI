### **1. Why Are Linear Regression (LR) and Decision Trees (DT) Considered Interpretable Models?**

**Interpretable models** are those that allow humans to understand how they make predictions. Their design makes it easy to trace how each feature contributes to the final output. Let’s explore how this works for Linear Regression (LR) and Decision Trees (DT).

---

### **Linear Regression (LR)**

Linear regression is interpretable because:

1. **Simple Mathematical Relationship**: Predictions are made using a straight-line formula:  

   $$
   y = w_1x_1 + w_2x_2 + \dots + w_nx_n + b
   $$

   Where:
   - $$x_1, x_2, \dots, x_n$$ are features.
   - $$w_1, w_2, \dots, w_n$$ are weights (coefficients) showing how much each feature contributes to the prediction.
   - $$b$$ is the bias (intercept), representing the baseline value when all features are zero.

2. **Feature Contribution Analysis**: Each feature’s importance is directly proportional to the magnitude of its weight $$w_i$$. Positive weights increase the prediction, while negative weights decrease it.

3. **Global Interpretability**: The relationship between inputs and outputs is consistent across all predictions.

---

### **Decision Trees (DT)**

Decision trees are interpretable because:

1. **Hierarchical Structure**: The model makes predictions by following a path from the root to a leaf node, making the decision-making process explicit.

2. **Decision Rules**: Each split is based on a simple condition like:

   $$
   x_j \leq \text{threshold}
   $$

   Where:
   - $$x_j$$ is a feature.
   - $$\text{threshold}$$ is a value determined during training.

3. **Traceable Path**: For any prediction, you can follow the path through the tree to see which rules were applied and how they influenced the result.

4. **Feature Importance**: The importance of each feature is derived from how much it reduces impurity (e.g., Gini index or entropy) when used for a split.

---

### **Comparison: Linear Regression vs. Decision Trees**

| **Aspect**               | **Linear Regression (LR)**                                         | **Decision Trees (DT)**                                    |
|--------------------------|-------------------------------------------------------------------|-----------------------------------------------------------|
| **Model Type**           | Linear (additive relationship).                                   | Non-linear, hierarchical.                                 |
| **Interpretability**     | Global: Coefficients $$w_i$$ explain the contribution of features. | Local: Rules and paths explain individual predictions.    |
| **Feature Interactions** | Cannot model feature interactions directly.                      | Handles feature interactions explicitly.                  |
| **Suitability**          | Works well when the relationship is linear.                     | Handles complex, non-linear relationships.                |

---

### **Key Takeaways**

- Linear regression is straightforward, interpretable globally, and best for linear relationships.
- Decision trees are interpretable locally, handle non-linear relationships, and explain predictions with explicit rules.
