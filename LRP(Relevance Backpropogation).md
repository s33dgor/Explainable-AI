### **Scenario**
We have a simple neural network with:
- **Input Layer**: 2 features (\(x_1, x_2\)).
- **Hidden Layer**: 2 neurons (\(h_1, h_2\)).
- **Output Layer**: 1 neuron (\(y\)).

The neural network's structure and weights are as follows:

1. Input Layer to Hidden Layer:
   \[
   h_1 = w_{11}x_1 + w_{12}x_2 = 0.6x_1 + 0.4x_2
   \]
   \[
   h_2 = w_{21}x_1 + w_{22}x_2 = 0.3x_1 + 0.7x_2
   \]

2. Hidden Layer to Output Layer:
   \[
   y = w_{h1}h_1 + w_{h2}h_2 = 0.5h_1 + 0.5h_2
   \]

The activations of the neurons for a specific input (\(x_1 = 1\), \(x_2 = 2\)) are:
- \(h_1 = 0.6(1) + 0.4(2) = 1.4\)
- \(h_2 = 0.3(1) + 0.7(2) = 1.7\)
- \(y = 0.5(1.4) + 0.5(1.7) = 1.55\)

We want to explain the prediction \(y = 1.55\) by propagating **relevance scores** backward.

---

### **Step-by-Step Relevance Backpropagation**

#### **1. Initialize Relevance at the Output**
At the output layer, we assign the entire output \(y = 1.55\) as the relevance:
\[
R_y = 1.55
\]

#### **2. Backpropagate Relevance to the Hidden Layer**
We use the **LRP-\(\epsilon\) rule**, which distributes relevance proportionally to the contributions of each neuron:
\[
R_{h_i} = \frac{a_i w_{h_i}}{\sum_j a_j w_{h_j} + \epsilon} R_y
\]

For \(h_1\) and \(h_2\):
- Activations: \(a_{h_1} = 1.4\), \(a_{h_2} = 1.7\).
- Weights: \(w_{h1} = 0.5\), \(w_{h2} = 0.5\).
- Stabilization term: \(\epsilon = 0.01\).

**Calculate relevance for \(h_1\):**
\[
R_{h_1} = \frac{1.4 \cdot 0.5}{1.4 \cdot 0.5 + 1.7 \cdot 0.5 + 0.01} \cdot 1.55
\]
\[
R_{h_1} = \frac{0.7}{0.7 + 0.85 + 0.01} \cdot 1.55 = \frac{0.7}{1.56} \cdot 1.55 = 0.695
\]

**Calculate relevance for \(h_2\):**
\[
R_{h_2} = \frac{1.7 \cdot 0.5}{1.4 \cdot 0.5 + 1.7 \cdot 0.5 + 0.01} \cdot 1.55
\]
\[
R_{h_2} = \frac{0.85}{1.56} \cdot 1.55 = 0.855
\]

At the hidden layer:
- \(R_{h_1} = 0.695\)
- \(R_{h_2} = 0.855\)

---

#### **3. Backpropagate Relevance to the Input Layer**
Relevance from each hidden neuron (\(h_1\) and \(h_2\)) is propagated to the input features (\(x_1, x_2\)) based on their weights.

For a neuron \(h_i\):
\[
R_{x_j} = \frac{a_j w_{ij}}{\sum_k a_k w_{ik} + \epsilon} R_{h_i}
\]

**For \(h_1\):**
- Weights: \(w_{11} = 0.6\), \(w_{12} = 0.4\).
- Activations: \(a_{x_1} = 1\), \(a_{x_2} = 2\).
- Relevance: \(R_{h_1} = 0.695\).

**Relevance for \(x_1\):**
\[
R_{x_1}^{(h_1)} = \frac{1 \cdot 0.6}{1 \cdot 0.6 + 2 \cdot 0.4 + 0.01} \cdot 0.695
\]
\[
R_{x_1}^{(h_1)} = \frac{0.6}{0.6 + 0.8 + 0.01} \cdot 0.695 = \frac{0.6}{1.41} \cdot 0.695 = 0.296
\]

**Relevance for \(x_2\):**
\[
R_{x_2}^{(h_1)} = \frac{2 \cdot 0.4}{1 \cdot 0.6 + 2 \cdot 0.4 + 0.01} \cdot 0.695
\]
\[
R_{x_2}^{(h_1)} = \frac{0.8}{1.41} \cdot 0.695 = 0.394
\]

**For \(h_2\):**
- Weights: \(w_{21} = 0.3\), \(w_{22} = 0.7\).
- Activations: \(a_{x_1} = 1\), \(a_{x_2} = 2\).
- Relevance: \(R_{h_2} = 0.855\).

**Relevance for \(x_1\):**
\[
R_{x_1}^{(h_2)} = \frac{1 \cdot 0.3}{1 \cdot 0.3 + 2 \cdot 0.7 + 0.01} \cdot 0.855
\]
\[
R_{x_1}^{(h_2)} = \frac{0.3}{0.3 + 1.4 + 0.01} \cdot 0.855 = \frac{0.3}{1.71} \cdot 0.855 = 0.15
\]

**Relevance for \(x_2\):**
\[
R_{x_2}^{(h_2)} = \frac{2 \cdot 0.7}{1 \cdot 0.3 + 2 \cdot 0.7 + 0.01} \cdot 0.855
\]
\[
R_{x_2}^{(h_2)} = \frac{1.4}{1.71} \cdot 0.855 = 0.7
\]

---

#### **Final Relevance for Input Features**
Combine relevance from both \(h_1\) and \(h_2\) for each input:
- \(R_{x_1} = R_{x_1}^{(h_1)} + R_{x_1}^{(h_2)} = 0.296 + 0.15 = 0.446\)
- \(R_{x_2} = R_{x_2}^{(h_1)} + R_{x_2}^{(h_2)} = 0.394 + 0.7 = 1.094\)

---

### **Interpretation**
- \(R_{x_1} = 0.446\): Input feature \(x_1\) contributes \(0.446\) (out of \(1.55\)) to the prediction.
- \(R_{x_2} = 1.094\): Input feature \(x_2\) contributes \(1.094\) to the prediction.

This shows that \(x_2\) (e.g., "Monthly Usage") is more influential than \(x_1\) (e.g., "Age") in determining the final prediction.

---

### **Key Takeaways**
- LRP distributes the output \(y\) as relevance scores among the input features.
- The **weights** and **activations** at each layer determine how relevance is distributed.
- Stabilization terms (\(\epsilon\)) prevent instability when activations or weights are small.
