# Models & Architecture

MzeeChakula employs a sophisticated ensemble of Graph Neural Networks (GNNs) to provide accurate nutrition recommendations. This section details the architecture and theoretical underpinnings of each model used in our system.

## The Ensemble System

Our production model combines the strengths of three top-performing architectures to ensure robustness and accuracy across diverse user scenarios.

| Model | Weight | Strength |
| :--- | :--- | :--- |
| **CRGN** (Compositional Reasoning Graph Network) | **40%** | Best overall balance and reasoning capability. |
| **HetGNN** (Heterogeneous Graph Neural Network) | **35%** | Handles diverse node types (Food, Nutrient, Condition) effectively. |
| **GAT** (Graph Attention Network) | **25%** | Efficiently attends to the most relevant neighbors in the graph. |

### Performance Metrics
-   **Inference Time**: ~15ms (CPU) / ~3ms (GPU) for top-10 recommendations.
-   **Model Size**: ~20.4 MB (optimized for deployment).
-   **Accuracy**: High confidence in link prediction tasks (AUC > 0.90).

---

## Detailed Model Architectures

We evaluated 9 different GNN architectures. Below is a detailed breakdown of each.

### 1. CRGN (Compositional Reasoning Graph Network)
**Architecture**:
CRGN is designed to address the challenge of **compositional generalization**—the ability to understand and generate new combinations of known components.
-   **Graph Parser**: Decomposes the input (user profile and food items) into object-centric subgraphs.
-   **Graph Matcher**: Uses a shared GNN to compute localized node representations, implicitly inducing factor nodes between related concepts.
-   **Reasoning Module**: Learns embeddings that capture how different foods combine to meet specific nutritional goals, effectively modeling the "composition" of a meal plan.

**Why we used it**: Nutrition planning is inherently compositional (e.g., "Rice" + "Beans" + "Spinach"). CRGN excels at understanding these combinations.

### 2. HetGNN (Heterogeneous Graph Neural Network)
**Architecture**:
HetGNN is specifically built for **heterogeneous graphs** where nodes and edges have different types (e.g., Food, Nutrient, Condition).
-   **Random Walk Sampling**: Samples a fixed number of strongly correlated heterogeneous neighbors for each node to handle size variations.
-   **Type-based Aggregation**: Aggregates content embeddings from neighbors of the same type using specific neural network modules (NN-1, NN-2).
-   **Attention Mechanism**: Combines aggregated representations from different neighbor types using attention weights to form the final node embedding.

**Why we used it**: Our knowledge graph is highly heterogeneous. HetGNN allows us to treat "Vitamin A" (Nutrient) differently from "Hypertension" (Condition) during message passing.

### 3. GAT (Graph Attention Network)
**Architecture**:
GAT introduces an **attention mechanism** to standard GNNs.
-   **Attention Coefficients**: Computes the importance of node $j$ to node $i$ ($e_{ij}$) using a shared attention mechanism $a(Wh_i, Wh_j)$.
-   **Multi-Head Attention**: Runs multiple attention mechanisms in parallel to capture different aspects of relationships (e.g., one head focuses on flavor compatibility, another on nutritional value).
-   **Weighted Aggregation**: Updates node features as a weighted sum of neighbors, where weights are the learned attention coefficients.

**Why we used it**: Not all connections are equally important. For a diabetic user, the link between "Soda" and "Sugar" should have a much higher weight than "Soda" and "Water content".

### 4. R-GCN (Relational Graph Convolutional Network)
**Architecture**:
R-GCN extends GCNs to multi-relational graphs by using **relation-specific weight matrices**.
-   **Message Passing**: For each relation type $r$ (e.g., `CONTAINS`, `AVOIDS`), a specific weight matrix $W_r$ is used to transform the neighbor's features.
-   **Regularization**: Uses basis decomposition or block-diagonal decomposition to reduce the number of parameters and prevent overfitting on rare relations.
-   **Self-Loops**: Includes relation-specific self-loops to ensure node identity is preserved.

**Why we used it**: It explicitly models the different types of edges in our graph, ensuring that the `CAUSES` relationship is mathematically distinct from `PREVENTS`.

### 5. Graph-RAG (Retrieval Augmented Generation)
**Architecture**:
Graph-RAG combines graph retrieval with Large Language Models (LLMs).
-   **Subgraph Retrieval**: Instead of fetching flat text chunks, it retrieves relevant subgraphs (e.g., a patient's condition node and all connected recommended foods).
-   **Context Enrichment**: The retrieved subgraph is linearized or embedded and provided as context to an LLM.
-   **Reasoning**: The LLM uses the structural context to generate natural language explanations for recommendations.

**Why we used it**: To provide explainable AI. It allows the system to tell the user *why* a food is recommended (e.g., "Because you have hypertension, and this food is low in sodium").

### 6. KGNN (Knowledge Graph Neural Network)
**Architecture**:
KGNN integrates a GNN encoder with a knowledge-aware decoder.
-   **GNN Encoder**: Aggregates information from the local neighborhood to learn entity embeddings.
-   **Knowledge-Aware Decoder**: Uses a scoring function (like TransE or TransH) to predict the plausibility of a triple $(h, r, t)$.
-   **Receptive Field**: Can be extended to multiple hops to capture high-order structural information.

**Why we used it**: It is highly effective for **link prediction**, which is the core task of recommending a new food (link) to a user.

### 7. G-GPT (Graph GPT)
**Architecture**:
Adapts the Transformer decoder-only architecture (like GPT-3) for graph data.
-   **Graph Linearization**: Converts the graph structure into a sequence of tokens (nodes and edges) that can be processed by a Transformer.
-   **Masked Attention**: Uses masked self-attention to predict the next node or edge in a sequence, effectively "generating" a meal plan step-by-step.
-   **Positional Encodings**: Uses graph-specific positional encodings to retain structural information in the sequence.

**Why we used it**: To explore **generative** capabilities, such as creating entire weekly meal plans from scratch rather than just recommending single items.

### 8. GRN (Graph Recurrent Network)
**Architecture**:
Combines GNNs with Recurrent Neural Networks (RNNs) to handle **dynamic/temporal graphs**.
-   **Spatial-Temporal Processing**: A GNN processes the spatial structure at each time step, while an RNN (like LSTM or GRU) updates the hidden state over time.
-   **Graph Filter**: Replaces standard linear transformations in RNNs with graph convolution operations.

**Why we used it**: To model **seasonality**. Food availability and prices in Uganda change with the seasons (Dry vs. Wet), and GRN captures these temporal dynamics.

### 9. TCN (Temporal Convolutional Network)
**Architecture**:
A specialized neural network for sequence modeling using **1D dilated causal convolutions**.
-   **Causal Convolutions**: Ensures that predictions at time $t$ only depend on history (no future leakage).
-   **Dilated Convolutions**: Allows the network to have a very large receptive field (history) with few layers, capturing long-range temporal dependencies.
-   **Residual Blocks**: Uses residual connections to allow for deeper networks and stable training.

**Why we used it**: An alternative to GRN for modeling time-series data like price fluctuations and seasonal trends, offering parallelizable training.

---

## Training Pipeline

All models were trained using the following standardized pipeline:

1.  **Data Splitting**: 80% Train, 10% Validation, 10% Test (stratified by user/condition).
2.  **Loss Function**: Binary Cross-Entropy (for link prediction) or MSE (for score prediction).
3.  **Optimizer**: AdamW with weight decay for regularization.
4.  **Framework**: PyTorch Geometric (PyG) and Deep Graph Library (DGL).
