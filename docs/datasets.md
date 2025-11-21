# Datasets & Knowledge Graph

The foundation of MzeeChakula is a comprehensive dataset linking local Ugandan foods to nutritional data and health conditions.

## Data Sources
-   **Food Composition**: FAO and local Ugandan food composition tables.
-   **Health Conditions**: WHO guidelines and local health ministry data.
-   **Cultural Practices**: Surveys and anthropological studies on Ugandan dietary habits.

## The Knowledge Graph

The data is structured as a Heterogeneous Graph with the following schema:

### Nodes
-   **Food**: (e.g., "Matoke", "Millet Porridge") - 5,005 nodes.
-   **Nutrient**: (e.g., "Vitamin A", "Iron") - 30 nodes.
-   **Condition**: (e.g., "Hypertension", "Diabetes") - 4,852 nodes.
-   **Region**: (Central, Western, Eastern, Northern).
-   **Season**: (Dry, Wet).

### Edges (Relationships)
-   `CONTAINS`: Food -> Nutrient (with amount properties).
-   `AFFECTS`: Nutrient -> Condition (positive or negative impact).
-   `AVAILABLE_IN`: Food -> Season/Region.
-   `CULTURALLY_RELEVANT`: Food -> Region.

## Processed Datasets

The `data/processed/` directory contains cleaned CSVs ready for model training:

| File | Rows | Description |
| :--- | :--- | :--- |
| `food_composition_clean.csv` | 5,006 | Nutritional breakdown of foods. |
| `health_conditions_clean.csv` | 5,029 | Mapping of conditions to dietary needs. |
| `food_prices_clean.csv` | 10,001 | Market prices for cost optimization. |
| `cultural_food_practices_clean.csv` | 10,002 | Cultural relevance data. |

## Graph Tensors

For efficient GNN training, the graph is converted into PyTorch Geometric tensors stored in `data/graph_tensors/`.

```python
import torch
data = torch.load('graph_tensors/graph_pyg.pt')
print(data)
# Output: HeteroData(
#   Food={ x=[5005, 8] },
#   Nutrient={ x=[30, 30] },
#   ...
# )
```
