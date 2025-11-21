# Models & Architecture

MzeeChakula employs a sophisticated ensemble of Graph Neural Networks (GNNs) to provide accurate nutrition recommendations.

## The Ensemble System

Our production model combines the strengths of three top-performing architectures:

| Model | Weight | Strength |
| :--- | :--- | :--- |
| **CRGN** (Compositional Reasoning Graph Network) | **40%** | Best overall balance and reasoning capability. |
| **HetGNN** (Heterogeneous Graph Neural Network) | **35%** | Handles diverse node types (Food, Nutrient, Condition) effectively. |
| **GAT** (Graph Attention Network) | **25%** | Efficiently attends to the most relevant neighbors in the graph. |

### Performance
-   **Inference Time**: ~15ms (CPU) / ~3ms (GPU)
-   **Model Size**: ~20.4 MB
-   **Accuracy**: High confidence in link prediction tasks (AUC > 0.90).

## Model Architectures

We evaluated 9 different GNN architectures before selecting the final ensemble.

### 1. CRGN (Compositional Reasoning Graph Network)
Designed to reason about the composition of meals. It learns embeddings that capture how different foods combine to meet nutritional goals.

### 2. HetGNN (Heterogeneous GNN)
Specifically built for heterogeneous graphs where nodes and edges have different types. It uses type-specific aggregation functions to capture the distinct roles of Foods, Nutrients, and Conditions.

### 3. GAT (Graph Attention Network)
Uses attention mechanisms to weigh the importance of neighbors. For example, it can learn that for a diabetic patient, the "Sugar Content" neighbor is more important than "Color".

### Other Evaluated Models
-   **R-GCN**: Relational GCN, good for knowledge graphs but heavier.
-   **Graph-RAG**: Retrieval-Augmented Generation on graphs.
-   **KGNN**: Knowledge Graph Neural Network.
-   **G-GPT**: Generative Pre-trained Transformer for graphs.
-   **GRN**: Graph Recurrent Network (for temporal data).
-   **TCN**: Temporal Convolutional Network.

## Training Process

The models were trained on the **MzeeChakula Knowledge Graph**.

-   **Task**: Link Prediction (predicting `RECOMMENDED_FOR` edges between Food and User/Condition nodes).
-   **Loss Function**: Binary Cross-Entropy.
-   **Optimizer**: AdamW.
-   **Framework**: PyTorch Geometric.

!!! info "Reproducibility"
    Training scripts and notebooks are available in the `models/` repository.
