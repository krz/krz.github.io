---
layout: post
title: Causal Discovery with causal-learn
categories: [python,causal inference]
---

Understanding the causal relationships between variables is crucial for making informed decisions and predictions. 
The Python package `causal-learn` offers a powerful toolkit for causal discovery, 
enabling researchers and practitioners to uncover these relationships from observational data. 
In this blog post, we’ll explore how to leverage causal-learn to identify and analyze causal structures, 
providing a step-by-step guide to get you started on your causal discovery journey.

# Data Generation
First, let's generate some data (inspired by this [notebook](https://github.com/PacktPublishing/Causal-Inference-and-Discovery-in-Python)):
```python
import numpy as np
from causallearn.search.FCMBased import lingam
from causallearn.search.FCMBased.lingam.utils import make_dot
import networkx as nx
import seaborn as sns

N = 1000
q = np.random.uniform(0, 2, N)
w = np.random.randn(N)
x = np.random.gumbel(0, 1, N) + w
y = 0.6 * q + 0.8 * w + np.random.uniform(0, 1, N)
z = 0.5 * x + np.random.randn(N)
data = np.stack([x, y, w, z, q]).T
```
This is the DAG of our data:

```python
nodes = ['X', 'Y', 'W', 'Z', 'Q']

edges = [
    ('W', 'X'),
    ('W', 'Y'),
    ('Q', 'Y'),
    ('X', 'Z'),
]

fci_graph = nx.DiGraph()

fci_graph.add_nodes_from(nodes)
fci_graph.add_edges_from(edges)

nx.draw(
    G=fci_graph,
    node_color='#00B0F0',
    nodelist=['X', 'Y', 'W', 'Z', 'Q'],
    with_labels=True,
    pos=nx.circular_layout(fci_graph)
)
```

![image](https://github.com/user-attachments/assets/6fa2dbe8-9a2a-4342-b3b3-6cb8a7aada28)
# Causal Discovery using LiNGAM
Now, let's estimate the causal graph using the LiNGAM method:

```python
model = lingam.DirectLiNGAM(random_state=42)
model.fit(data)
```
`causal-learn` has a built-in function to visualize the estimated DAG:
```python
make_dot(model.adjacency_matrix_, labels=nodes)
```
![image](https://github.com/user-attachments/assets/43f8ef77-8f37-4594-b513-abd1612ad73f)

We can also use `networkx` to visualize the result:
```python
G = nx.DiGraph(model.adjacency_matrix_.T)
nx.draw(G, 
        with_labels=True,
        pos=nx.circular_layout(G))
```
![image](https://github.com/user-attachments/assets/805ffa17-0ae0-4a85-96df-5219e2519cc3)

We see that LiNGAM estimates the true DAG quite accurately!
Another way to visualize the adjacency matrix is a heatmap:

```python
sns.heatmap(model.adjacency_matrix_.T, cmap="rocket_r", cbar=False)
```
![image](https://github.com/user-attachments/assets/1d2ddd67-3171-4831-ac11-ba7a6371833b)

Note that the edge from X to Z has a low probability, if we set the threshold to 0.5 it would actually disappear:
```python
sns.heatmap(model.adjacency_matrix_.T>0.5, cmap="rocket_r", cbar=False)
```
![image](https://github.com/user-attachments/assets/2765169b-4cdf-4492-88cd-250208c42ece)




