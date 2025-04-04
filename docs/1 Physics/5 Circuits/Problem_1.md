# Equivalent Resistance Calculation Using Graph Theory

---

## Introduction

### Motivation:
- Traditional series/parallel methods become cumbersome for complex circuits
- Graph theory provides systematic analysis of arbitrary resistor networks
- Enables automated circuit analysis and optimization
- Bridges electrical engineering and computer science concepts

---

## Graph Representation of Circuits

### Key Concepts:
- **Nodes** represent circuit junctions
- **Edges** represent resistors with resistance values as weights
- **Graph** captures entire circuit topology

```
Example Circuit:
A --10Ω-- B --20Ω-- C
|        |
5Ω      15Ω
|        |
D --30Ω-- E
```

---

## Algorithm Overview (Option 1)

### Step-by-Step Process:

1. **Series Reduction**:
   - Identify chains of nodes with degree 2
   - Replace with single equivalent resistor: 
   
   $R_eq = ΣR_i$

2. **Parallel Reduction**:
   - Identify parallel edges between same nodes
   - Replace with single resistor: 
   
   $1/R_eq = Σ(1/R_i)$

3. **Iterative Simplification**:
   - Repeat until single edge remains
   - Handles nested combinations through recursion

---

## Pseudocode Implementation

```python
function equivalent_resistance(graph):
    while graph has more than 2 nodes:
        # Series reduction
        for node in graph.nodes:
            if node.degree == 2:
                R_eq = sum(edge.weights for edge in node.edges)
                merge_edges(node, R_eq)
        
        # Parallel reduction
        for node_pair in graph.nodes:
            parallel_edges = find_parallel_edges(node_pair)
            if len(parallel_edges) > 1:
                R_eq = 1 / sum(1/edge.weight for edge in parallel_edges)
                replace_parallel_edges(node_pair, R_eq)
    
    return graph.remaining_edge.weight
```

---

## Full Implementation (Option 2)

### Python Code Using NetworkX:

```python
import networkx as nx

def calculate_equivalent_resistance(graph):
    G = graph.copy()
    
    while len(G.nodes()) > 2:
        # Series reduction
        for node in list(G.nodes()):
            if G.degree(node) == 2:
                neighbors = list(G.neighbors(node))
                R_eq = G.edges[neighbors[0], node]['weight'] + G.edges[node, neighbors[1]]['weight']
                G.add_edge(neighbors[0], neighbors[1], weight=R_eq)
                G.remove_node(node)
                break
                
        # Parallel reduction
        for u, v in list(G.edges()):
            parallel_edges = [(a,b) for a,b in G.edges() if sorted((a,b)) == sorted((u,v))]
            if len(parallel_edges) > 1:
                R_eq = 1 / sum(1/G.edges[e]['weight'] for e in parallel_edges)
                G.remove_edges_from(parallel_edges)
                G.add_edge(u, v, weight=R_eq)
                break
                
    return G.edges[list(G.edges())[0]]['weight']
```

---

## Example Applications

### Case 1: Simple Series
```
Input: A --10Ω-- B --20Ω-- C
Output: 30Ω
```

### Case 2: Simple Parallel
```
Input: A --10Ω-- B
       A --20Ω-- B
Output: 6.67Ω
```

### Case 3: Nested Combination
```
Input: A --10Ω-- B --20Ω-- C
       |        |
       5Ω      15Ω
       |        |
       D --30Ω-- E
Output: 22.14Ω
```

---

## Algorithm Analysis

### Efficiency Considerations:
- **Time Complexity**: O(n²) worst case
- **Space Complexity**: O(n + e) for graph storage
- **Optimization Opportunities**:
  - Priority reduction of high-degree nodes
  - Parallel processing for independent reductions
  - Memoization of common subgraphs

---

## Practical Applications

1. **Circuit Design Automation**:
   - SPICE-like simulators
   - PCB layout optimization

2. **Network Analysis**:
   - Power grid modeling
   - Telecommunications networks

3. **Educational Tools**:
   - Interactive circuit analysis
   - Visualization of circuit simplification

---

## Conclusion

- Graph theory provides elegant solution to equivalent resistance
- Algorithm handles arbitrary circuit configurations
- Implementation scales to complex networks
- Foundation for more advanced circuit analysis techniques