# Datasets & Knowledge Graph

The foundation of MzeeChakula is a comprehensive dataset linking local Ugandan foods to nutritional data and health conditions. This data is structured into a **Heterogeneous Knowledge Graph**.

!!! tip "Explore the Graph"
    For a deep dive into the graph's schema, ontology, and reasoning capabilities, see the [Knowledge Graph](knowledge_graph.md) page.

## Data Sources
-   **Food Composition**: FAO and local Ugandan food composition tables.
-   **Health Conditions**: WHO guidelines and local health ministry data.
-   **Cultural Practices**: Surveys and anthropological studies on Ugandan dietary habits.
-   **Market Data**: Local market surveys for price and seasonality.

## The Knowledge Graph Structure

The graph consists of various node types and edge types, forming a rich network of information.

### Node Types & Features
| Node Type | Count | Feature Dim | Description |
| :--- | :--- | :--- | :--- |
| **Food** | 5,005 | 8 | Features include macronutrients (Protein, Carbs, Fat), energy, and fiber. |
| **Nutrient** | 30 | 30 | Detailed micronutrient profiles (Vitamins, Minerals). |
| **Condition** | 4,852 | 2 | Health conditions encoded with severity and type. |
| **Benefit** | 10,000 | 10,000 | Sparse vector representing specific health benefits. |
| **MealPlan** | 10,000 | 4 | Historical or template meal plans. |
| **Region** | 5 | - | Central, Western, Eastern, Northern, etc. |
| **Season** | 12 | - | Monthly seasonality nodes. |

### Edge Types (Relationships)
-   `CONTAINS`: Food $\rightarrow$ Nutrient (Weighted by amount).
-   `AFFECTS`: Nutrient $\rightarrow$ Condition (Positive/Negative impact).
-   `AVAILABLE_IN`: Food $\rightarrow$ Season.
-   `GROWN_IN`: Food $\rightarrow$ Region.
-   `CULTURALLY_RELEVANT`: Food $\rightarrow$ Culture.
-   `HAS_PRICE`: Food $\rightarrow$ PriceRange.

## Processed Datasets

The `data/processed/` directory contains cleaned CSVs ready for analysis and model training:

| File | Rows | Description |
| :--- | :--- | :--- |
| `food_composition_clean.csv` | 5,006 | Nutritional breakdown of foods. |
| `health_conditions_clean.csv` | 5,029 | Mapping of conditions to dietary needs. |
| `food_prices_clean.csv` | 10,001 | Market prices for cost optimization. |
| `cultural_food_practices_clean.csv` | 10,002 | Cultural relevance data. |
| `food_seasonality_clean.csv` | 5,001 | Availability of foods by month. |

## Graph Tensors

For efficient GNN training, the graph is converted into PyTorch Geometric tensors stored in `data/graph_tensors/`.

```python
import torch
from torch_geometric.data import HeteroData

# Load the precomputed graph
data = torch.load('graph_tensors/graph_pyg.pt')

print(f"Node types: {data.node_types}")
print(f"Edge types: {data.edge_types}")
print(f"Food features shape: {data['Food'].x.shape}")
```

### Tensor Files
-   `{Node}_features.npy`: Numpy arrays containing node feature vectors.
-   `{Source}-{Rel}-{Target}_edge_index.npy`: Adjacency matrices for each relation type.
-   `node_mappings.json`: Maps original IDs (e.g., "Matoke") to graph indices (e.g., `0`).

## Data Processing Pipeline

1.  **Ingestion**: Raw CSVs are loaded from `data/raw/`.
2.  **Cleaning**: Missing values are imputed, and units are standardized.
3.  **Graph Construction**: Nodes and edges are created in Neo4j.
4.  **Enrichment**: Context nodes (Season, Region) are linked.
5.  **Export**: The graph is exported to PyTorch Geometric tensors for training.
