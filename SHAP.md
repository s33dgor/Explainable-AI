### **Mathematical Formulation and Step-by-Step Explanation of SHAP (SHapley Additive exPlanations)**

SHAP is a powerful framework for explaining individual predictions in machine learning by assigning a **fair value (contribution)** to each feature based on Shapley values from cooperative game theory. It works both globally (aggregating feature importance across the dataset) and locally (explaining specific predictions).

---

### **Key Idea Behind SHAP**
1. **Fair Attribution**: Inspired by Shapley values in game theory, SHAP fairly allocates the "credit" for a model’s prediction among all the input features.
2. **Additive Explanation**: The prediction of the model is decomposed into a sum of contributions from each feature, plus a baseline value.
3. **Global and Local Interpretability**: SHAP explains individual predictions and also provides insights into global trends.

---

### **Mathematical Formulation of SHAP**

The prediction of the model $f(x)$ for a data instance $x$ is decomposed as:

$$
f(x) = \phi_0 + \sum_{i=1}^n \phi_i
$$

Where:
- $\phi_0$: Baseline value (the average model prediction over the entire dataset).
- $\phi_i$: Contribution of the $i$-th feature to the prediction.

#### **Shapley Value Formula**:
The Shapley value $\phi_i$ for feature $i$ is computed as:

$$
\phi_i = \sum_{S \subseteq N \setminus \{i\}} \frac{|S|! \, (|N| - |S| - 1)!}{|N|!} \, \left[ f(S \cup \{i\}) - f(S) \right]
$$

Where:
- $N$: The set of all features.
- $S$: A subset of features excluding $i$.
- $f(S)$: The model’s prediction using only the features in $S$.
- $f(S \cup \{i\}) - f(S)$: The marginal contribution of feature $i$ when added to the subset $S$.

---

### **Steps to Compute SHAP Values**

#### **Step 1: Define the Baseline Prediction**
- Compute $\phi_0$, the average prediction of the model over all data points:

$$
\phi_0 = \frac{1}{m} \sum_{j=1}^m f(x^{(j)})
$$

This is the model's output when no features are included.

#### **Step 2: Perturb Features and Compute Predictions**
- For a given instance $x$, iteratively consider subsets $S$ of features and calculate the model’s output:
  - $f(S)$: Prediction using only the features in $S$.
  - $f(S \cup \{i\})$: Prediction when feature $i$ is added to $S$.

#### **Step 3: Compute Marginal Contributions**
- Compute the difference $f(S \cup \{i\}) - f(S)$, which represents how much feature $i$ adds to the prediction when combined with subset $S$.

#### **Step 4: Aggregate Contributions Across All Subsets**
- Combine the marginal contributions over all possible subsets $S$ using the Shapley formula, weighting each subset based on its size.

#### **Step 5: Summarize the Prediction**
- The SHAP values $\phi_1, \phi_2, \dots, \phi_n$ represent the contribution of each feature to the specific prediction $f(x)$.
- These values add up to the prediction:

$$
f(x) = \phi_0 + \phi_1 + \phi_2 + \dots + \phi_n
$$

---

### **SHAP Strengths**
1. **Fairness**: Based on cooperative game theory, it fairly distributes contributions among features.
2. **Local and Global Insights**:
   - Locally: Explains individual predictions.
   - Globally: Aggregates feature contributions across the dataset to show global feature importance.
3. **Handles Interactions**: Considers how features interact with each other by analyzing all possible subsets.

---

### **Comparison: SHAP vs. LIME**

| **Aspect**               | **SHAP**                                                                                           | **LIME**                                                                                             |
|--------------------------|----------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| **Mathematical Basis**   | Based on **Shapley values** from cooperative game theory.                                         | Fits a **local surrogate model** (e.g., linear regression) around the instance of interest.          |
| **Fairness**             | Ensures **fair distribution** of contributions among features, accounting for interactions.       | Relies on the surrogate model, which might not perfectly capture interactions.                       |
| **Interpretability**     | Both **local and global**: Explains specific predictions and summarizes global feature importance. | Primarily **local**: Focuses on explaining individual predictions.                                   |
| **Feature Interactions** | Explicitly considers all **feature interactions** (e.g., how features influence predictions together). | May not fully account for feature interactions in the surrogate model.                               |
| **Stability**            | **Stable** results due to theoretical foundation.                                                | **Unstable** results can occur due to random sampling of perturbations.                             |
| **Efficiency**           | Computationally expensive for large datasets or complex models.                                  | Typically faster but depends on the complexity of the surrogate model and number of perturbations.  |
| **Additive Nature**      | Decomposes predictions into additive contributions.                                              | Produces coefficients for the surrogate model, which may not decompose predictions perfectly.       |
| **Global Explanations**  | Can aggregate SHAP values to derive global feature importance.                                    | Requires training a separate global surrogate model for global explanations.                         |

---

### **Example**
Let’s explain a loan prediction using both SHAP and LIME.

#### **Scenario**:
- Features: Age, Income, Credit Score.
- Prediction $f(x) = 0.85$ for a customer.

---

#### **SHAP Explanation**:
- $\phi_0 = 0.5$: Baseline (average prediction across all customers).
- $\phi_\text{Age} = 0.1$: Contribution of age to the prediction.
- $\phi_\text{Income} = 0.15$: Contribution of income.
- $\phi_\text{Credit Score} = 0.1$: Contribution of credit score.

**Interpretation**:

$$
f(x) = 0.5 + 0.1 + 0.15 + 0.1 = 0.85
$$

- Age, income, and credit score all positively influence the prediction.

---

#### **LIME Explanation**:
1. Generate perturbed data around the customer (e.g., varying age, income, credit score).
2. Fit a local linear regression as a surrogate:

$$
f(x) = 0.2 \cdot \text{Age} + 0.5 \cdot \text{Income} + 0.1 \cdot \text{Credit Score}
$$

**Interpretation**:
- Income has the strongest positive effect locally ($0.5$).
- Age has a moderate positive effect ($0.2$).

---

### **Which to Use When?**
- Use **SHAP** when:
  - You need a theoretically sound and fair explanation.
  - Feature interactions are important.
  - Both local and global insights are required.
- Use **LIME** when:
  - You need a quick, approximate explanation.
  - Simpler interpretability is acceptable.
  - The model is very large, and SHAP’s computational cost is prohibitive.
