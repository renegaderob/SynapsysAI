
# Synapsys AI

A cutting-edge AI framework for building intelligent, adaptive, and scalable applications across various industries.

## Features
- Scalable AI models for diverse applications.
- Prebuilt modules for natural language processing (NLP), computer vision, and predictive analytics.
- Easy integration with existing systems and cloud services.
- Comprehensive support for training, deploying, and monitoring models.

Backend Code (Python - FastAPI)  :
backend/main.py  :

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from knowledge_graph.graph import KnowledgeGraph

app = FastAPI()

# Initialize the Knowledge Graph
graph = KnowledgeGraph()

# Data Models
class Node(BaseModel):
    id: str
    data: dict

class Edge(BaseModel):
    source: str
    target: str

class Query(BaseModel):
    question: str

# API Endpoints
@app.post("/node/")
async def add_node(node: Node):
    try:
        graph.add_node(node.id, node.data)
        return {"message": f"Node '{node.id}' added successfully."}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))

@app.post("/edge/")
async def add_edge(edge: Edge):
    try:
        graph.add_edge(edge.source, edge.target)
        return {"message": f"Edge from '{edge.source}' to '{edge.target}' added successfully."}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))

@app.get("/node/{node_id}/")
async def get_node(node_id: str):
    node = graph.get_node(node_id)
    if not node:
        raise HTTPException(status_code=404, detail="Node not found")
    return node

@app.post("/query/")
async def query_graph(query: Query):
    try:
        result = graph.query(query.question)
        return {"result": result}
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))


knowledge_graph/graph.py   :

class KnowledgeGraph:
    def __init__(self):
        self.graph = {}

    def add_node(self, node_id, data):
        if node_id in self.graph:
            raise Exception(f"Node '{node_id}' already exists.")
        self.graph[node_id] = {"data": data, "edges": []}

    def add_edge(self, source, target):
        if source not in self.graph or target not in self.graph:
            raise Exception("Both nodes must exist before adding an edge.")
        self.graph[source]["edges"].append(target)

    def get_node(self, node_id):
        return self.graph.get(node_id, None)

    def query(self, question):
        # Basic query handling
        if question.startswith("GET_NODE:"):
            node_id = question.split(":")[1].strip()
            return self.get_node(node_id)
        return {"error": "Query not understood"}


backend/requirements.txt. :

fastapi
uvicorn


Run the backend:

uvicorn main:app --reload



Frontend Code (React.js)
frontend/src/App.js  :

import React, { useState } from "react";

function App() {
  const [nodeId, setNodeId] = useState("");
  const [nodeData, setNodeData] = useState("");
  const [response, setResponse] = useState("");

  const handleAddNode = async () => {
    const response = await fetch("http://localhost:8000/node/", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ id: nodeId, data: JSON.parse(nodeData) }),
    });
    const data = await response.json();
    setResponse(data.message);
  };

  return (
    <div>
      <h1>SynapSys AI</h1>
      <input
        type="text"
        placeholder="Node ID"
        value={nodeId}
        onChange={(e) => setNodeId(e.target.value)}
      />
      <textarea
        placeholder="Node Data (JSON)"
        value={nodeData}
        onChange={(e) => setNodeData(e.target.value)}
      ></textarea>
      <button onClick={handleAddNode}>Add Node</button>
      <p>Response: {response}</p>
    </div>
  );
}

export default App;

## Installation
### Prerequisites
- Python 3.8+
- [pip](https://pip.pypa.io/en/stable/installation/)
- (Optional) GPU support requires CUDA 11.x and cuDNN.

### Install
Clone the repository and install dependencies:
```bash
git clone https://github.com/yourusername/synapsys-ai.git
cd synapsys-ai
pip install -r requirements.txt
