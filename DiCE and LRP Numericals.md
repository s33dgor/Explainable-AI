### **Worked Example and Implementation Details: DiCE and Layer-Wise Relevance Propagation (LRP)**

---

## **1. DiCE: Diverse Counterfactual Explanations**

Let’s walk through a worked example for DiCE using a simple binary classification task.

#### **Scenario**
- A machine learning model predicts whether a customer’s loan application is **approved (1)** or **denied (0)**.
- Input features:
  - Age (\(x_1\)): 35 years
  - Income (\(x_2\)): $40,000
  - Credit Score (\(x_3\)): 600
- Prediction for current input: **Denied (0)**.

#### **Objective**
Generate **diverse counterfactual explanations** for how the customer can get the loan approved (\(y_{\text{target}} = 1\)).

---

### **Steps in DiCE**

#### **Step 1: Define Loss Functions**
DiCE uses the following objectives:
1. **Outcome Loss** (\(\mathcal{L}_\text{outcome}\)):
   \[
   \mathcal{L}_\text{outcome} = \| f(x') - y_{\text{target}} \|^2
   \]
   Ensures that counterfactuals lead to the target prediction (\(y_{\text{target}} = 1\)).

2. **Diversity Loss** (\(\mathcal{L}_\text{diversity}\)):
   Encourages counterfactuals to be distinct. For \(K\) counterfactuals \( \{x'_1, x'_2, \dots, x'_K\} \), the diversity loss is:
   \[
   \mathcal{L}_\text{diversity} = - \sum_{i \neq j} \text{distance}(x'_i, x'_j)
   \]
   A common choice for \(\text{distance}()\) is the Euclidean distance.

3. **Proximity Loss** (\(\mathcal{L}_\text{proximity}\)):
   Penalizes large changes to the input:
   \[
   \mathcal{L}_\text{proximity} = \| x - x' \|^2
   \]

4. **Combined Objective**:
   \[
   \mathcal{L} = \mathcal{L}_\text{outcome} + \lambda_1 \mathcal{L}_\text{diversity} + \lambda_2 \mathcal{L}_\text{proximity}
   \]

#### **Step 2: Generate Counterfactuals**
1. Start with random perturbations of the original input \(x\). For instance:
   - Counterfactual 1 (\(x'_1\)): Age = 40, Income = $50,000, Credit Score = 650.
   - Counterfactual 2 (\(x'_2\)): Age = 35, Income = $70,000, Credit Score = 600.

2. Iteratively optimize each counterfactual \(x'_i\) to minimize the total loss \(\mathcal{L}\) using gradient descent or other optimization techniques.

#### **Step 3: Check Feasibility**
Ensure counterfactuals are **valid**:
- Age must remain realistic (e.g., between 18 and 100).
- Income and credit score must be within plausible ranges.

#### **Results**
After optimization, you might generate diverse counterfactuals such as:
1. Increase income to $55,000 and credit score to 700.
2. Keep income at $40,000 but improve credit score to 750 and age to 38.

---

#### **Python Example: Using DiCE**
Here’s a simple Python snippet using the **DiCE** library:

```python
import dice_ml
from dice_ml import Dice
import pandas as pd

# Define the dataset
data = dice_ml.Data(dataframe=pd.read_csv("loan_data.csv"), continuous_features=['Age', 'Income', 'CreditScore'], outcome_name='Approval')

# Define the black-box model
model = dice_ml.Model(model=your_trained_model, backend="sklearn")

# Create a DiCE explainer
dice = Dice(data, model)

# Generate counterfactuals
query_instance = {'Age': 35, 'Income': 40000, 'CreditScore': 600}
cf = dice.generate_counterfactuals(query_instance, total_CFs=3, desired_class=1)

# Visualize the results
cf.visualize_as_dataframe()
```

---

## **2. Layer-Wise Relevance Propagation (LRP)**

Let’s walk through an example using LRP to explain a prediction from a neural network.

---

### **Scenario**
- A neural network predicts whether a customer will churn from a subscription service.
- Input features:
  - \(x_1\): Age = 25
  - \(x_2\): Monthly Usage = 300 minutes
  - \(x_3\): Contract Length = 12 months
- Prediction: **Churn Probability = 0.8**.

---

### **Steps in LRP**

#### **Step 1: Forward Pass**
1. Input passes through the network layers, generating activations at each neuron.
2. Final prediction is computed using the softmax layer.

---

#### **Step 2: Initialize Relevance**
At the output layer, assign the entire prediction score as relevance:
\[
R_{\text{output}} = 0.8
\]

---

#### **Step 3: Backpropagate Relevance**
Relevance is propagated backward through the layers, distributing relevance scores to input features based on their contributions.

1. **LRP-\(\epsilon\) Rule**:
   For neuron \(i\) in layer \(l\) and neuron \(j\) in layer \(l+1\), relevance is computed as:
   \[
   R_i^{(l)} = \sum_j \frac{a_i w_{ij}}{\sum_k a_k w_{kj} + \epsilon} R_j^{(l+1)}
   \]
   Where:
   - \(a_i\): Activation of neuron \(i\).
   - \(w_{ij}\): Weight connecting \(i\) to \(j\).
   - \(\epsilon\): Small term to avoid division by zero.

2. **Distribute Relevance**:
   Relevance scores are distributed proportionally based on the activations and weights.

---

#### **Step 4: Interpret Relevance at the Input**
After backpropagating to the input layer, the relevance scores for features are:
\[
R_{\text{Age}} = 0.2, \quad R_{\text{Monthly Usage}} = 0.5, \quad R_{\text{Contract Length}} = 0.1
\]

**Interpretation**:
- Monthly usage contributes the most to the prediction (0.5 out of 0.8).
- Contract length has the least influence (0.1 out of 0.8).

---

#### **Python Example: LRP**
Here’s a simple Python example using the **Innvestigate** library for LRP:

```python
import innvestigate
import innvestigate.utils as iutils

# Load your trained model
model = your_trained_neural_network

# Create an LRP analyzer
analyzer = innvestigate.create_analyzer("lrp.epsilon", model)

# Input instance to explain
x = preprocess_input([25, 300, 12])  # Age, Monthly Usage, Contract Length

# Perform analysis
relevance_scores = analyzer.analyze(x)

# Display results
print("Relevance Scores:", relevance_scores)
```

---

### **Comparison: DiCE vs. LRP**

| **Aspect**             | **DiCE**                                                            | **LRP**                                                            |
|-------------------------|--------------------------------------------------------------------|--------------------------------------------------------------------|
| **Purpose**             | Generates counterfactuals for actionable explanations.            | Traces feature contributions for attributional explanations.      |
| **Scope**               | Local (instance-specific explanations).                          | Local (specific prediction), but applicable to deep networks.     |
| **Output**              | Diverse counterfactual instances.                                | Relevance scores for each input feature.                          |
| **Flexibility**         | Model-agnostic: Works with any ML model.                         | Model-specific: Requires deep neural networks.                    |
| **Applications**        | Decision-making, recourse, and actionable insights.              | Understanding deep learning models and feature importance.         |
