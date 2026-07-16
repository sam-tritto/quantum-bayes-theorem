# Quantum Bayesian Inference Tutorial

This project presents a tutorial demonstrating how classical Bayesian methods can be modeled and computed within a quantum computing framework using **PennyLane**. 

Classical probability updates represent the core of statistical inference, while quantum computing allows us to encode these joint probability distributions natively within the state space of a multi-qubit system. By leveraging controlled gates and quantum measurement post-selection, we can reconstruct the exact joint probability space and compute Bayesian posterior probabilities.

## Repository Contents

* `quantum_bayes_theorem.ipynb`: The main Jupyter notebook tutorial containing mathematical walkthoughs, code implementations, and visual results.
* `pyproject.toml`: Dependency declaration for the project managed via `uv`.

---

## Technical Overview

The tutorial covers two primary quantum-classical mappings:

### 1. Single Variable Quantum Bayesian Update
We model a simple update $P(H \mid D)$ using a two-qubit system:
* **Qubit 0 (Hypothesis)**: Initialized to represent the prior probability $P(H = 1)$ using an $RY$ rotation gate:
  $$\theta_{\text{prior}} = 2 \arcsin\left(\sqrt{P(H=1)}\right)$$
* **Qubit 1 (Data)**: Encodes the likelihoods $P(D \mid H)$ using controlled-rotation ($CRY$) gates, conditioned on Qubit 0 being $|0\rangle$ or $|1\rangle$.
* **Post-Selection**: We observe the joint probability array. By selecting and normalizing only the states where the data qubit is $|1\rangle$ (simulating wave-function collapse), we yield a posterior probability $P(H=1 \mid D=1)$ matching classical Bayes perfectly.

### 2. Quantum Naive Bayes Classifier
We scale the approach to a classifier representing a target class $Y$ and two conditionally independent features $X_1$ and $X_2$ using a three-qubit system:
* **Qubit 0 (Target Class $Y$)**: Loaded with the prior probability $P(Y=1)$.
* **Qubit 1 & 2 (Features $X_1, X_2$)**: Conditioned on the class qubit via controlled rotations.
* **Post-Selection**: By post-selecting the states where both features are observed as true ($X_1=1, X_2=1$), we calculate the posterior class probability:
  $$P(Y=1 \mid X_1=1, X_2=1) = \frac{P(1, 1, 1)}{P(0, 1, 1) + P(1, 1, 1)}$$

### 3. Bayesian Optimization for VQAs / Hybrid Learning
Instead of using quantum to help Bayes, we use classical Bayesian methods to solve a massive quantum problem: tuning continuous parameters (gate angles) in a Variational Quantum Circuit (VQC).
* **How it applies**: We build a Gaussian Process surrogate model of the noisy quantum objective function. We use a Lower Confidence Bound (LCB) acquisition function to decide which parameters to query on the quantum device.
* **Why it matters**: It drastically reduces the number of expensive quantum device evaluations needed to find the ground state compared to gradient-based approaches under noise.

### 4. Quantum Bayesian Networks (QBNs)
We build a full Directed Acyclic Graph (DAG) using a 3-qubit quantum circuit representing Rain ($R$), Sprinkler ($S$), and Wet Grass ($W$).
* **Entanglement & Dependencies**: While $S$ is parented by $R$, the $W$ node depends on both parents. We use multi-controlled rotation gates (`qml.ctrl(qml.RY, ...)`) to construct the joint dependencies.
* **Inference**: By measuring the joint probability state space and post-selecting the states where the grass is observed to be wet ($W=1$), we calculate the posterior probability of Rain: $P(R=1 \mid W=1)$.

### 5. Adaptive Bayesian Metrology (Phase Estimation)
We run an adaptive classical-quantum hybrid loop to estimate an unknown physical phase shift $\phi \in [0, 2\pi]$ using a 1-qubit quantum sensor.
* **Classical-Quantum Feedback**: We feed the peak estimate of our probability distribution back into the quantum sensor as a control phase adjustment $\theta$, shifting our measurement sensitivity dynamically.
* **Bayesian Update**: After each single-shot quantum measurement, we use Bayes' rule classically to update our probability distribution over the phase space, rapidly sharpening a wide prior into a razor-sharp peak centered on the true phase.

---

## Installation & Setup

This repository uses **`uv`** for Python package and environment management.

### Prerequisites
Make sure you have `uv` installed. If you do not have it, you can install it via curl or your platform's package manager:
```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Setup Environment
Clone the repository and initialize the virtual environment with all required dependencies:
```bash
# Install dependencies and create a virtual environment
uv sync
```

The dependencies include:
* `pennylane`: Quantum simulation and circuit definition.
* `scikit-learn`: Gaussian Process regression models for Bayesian Optimization.
* `matplotlib` & `seaborn`: Premium styled plotting libraries.
* `ipykernel` & `nbconvert`: Jupyter notebook execution and kernel integration.

---

## How to Run

To run the notebook interactively:

1. Activate the environment:
   ```bash
   source .venv/bin/activate
   ```
2. Launch Jupyter Notebook or Jupyter Lab:
   ```bash
   uv run jupyter lab
   ```
3. Open `quantum_bayes_theorem.ipynb` and run the cells.
