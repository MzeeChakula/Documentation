# Project Setup

This guide will help you set up the MzeeChakula project locally for development.

## Prerequisites

-   **Python 3.12+**
-   **Node.js 16+**
-   **Git**

## Cloning the Repository

```bash
git clone https://github.com/MzeeChakula/Model-Testing.git
cd Model-Testing
```

## Backend Setup

The backend is built with **FastAPI**.

1.  Navigate to the backend directory:
    ```bash
    cd backend
    ```

2.  Create a virtual environment:
    ```bash
    python3 -m venv ../.venv
    source ../.venv/bin/activate  # On Windows: ..\.venv\Scripts\activate
    ```

3.  Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```

4.  Run the server:
    ```bash
    uvicorn api.main:app --reload --host 127.0.0.1 --port 8000
    ```

    The API will be available at `http://127.0.0.1:8000`.
    Interactive docs: `http://127.0.0.1:8000/docs`.

## Frontend Setup

The frontend is built with **Vue 3** and **Vite**.

1.  Navigate to the frontend directory:
    ```bash
    cd frontend
    ```

2.  Install dependencies:
    ```bash
    npm install
    ```

3.  Run the development server:
    ```bash
    npm run dev
    ```

    The app will be available at `http://localhost:5173`.

## Environment Variables

Create a `.env` file in the `backend` directory if needed (see `.env.example`).

| Variable | Description | Default |
| :--- | :--- | :--- |
| `MODEL_PATH` | Path to local model files | `./models` |
| `NEO4J_URI` | URI for Neo4j database | `bolt://localhost:7687` |

## Troubleshooting

!!! warning "Model Files"
    If the backend cannot find trained models, it will fall back to a heuristic predictor. To use the full AI capabilities, ensure you have downloaded the model weights from [Hugging Face](https://huggingface.co/Shakiran/MzeeChakulaNutritionEnsembleModel) and placed them in `backend/models/`.
