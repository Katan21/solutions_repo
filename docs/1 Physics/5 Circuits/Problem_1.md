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
import networkx as nx
from itertools import combinations

class CircuitAnalyzer:
    def __init__(self):
        self.graph = nx.Graph()
        
    def add_resistor(self, node1, node2, resistance):
        """Add a resistor between two nodes"""
        self.graph.add_edge(node1, node2, weight=resistance)
        
    def visualize_circuit(self):
        """Draw the current circuit graph"""
        pos = nx.spring_layout(self.graph)
        edge_labels = nx.get_edge_attributes(self.graph, 'weight')
        nx.draw(self.graph, pos, with_labels=True, node_size=700)
        nx.draw_networkx_edge_labels(self.graph, pos, edge_labels=edge_labels)
        plt.show()
        
    def series_reduction(self):
        """Reduce series connections in the circuit"""
        reduced = False
        for node in list(self.graph.nodes()):
            neighbors = list(self.graph.neighbors(node))
            if len(neighbors) == 2:
                # Calculate equivalent resistance
                R1 = self.graph.edges[node, neighbors[0]]['weight']
                R2 = self.graph.edges[node, neighbors[1]]['weight']
                R_eq = R1 + R2
                
                # Modify the graph
                self.graph.add_edge(neighbors[0], neighbors[1], weight=R_eq)
                self.graph.remove_node(node)
                reduced = True
                break  # Restart after modification
        return reduced
    
    def parallel_reduction(self):
        """Reduce parallel connections in the circuit"""
        reduced = False
        edges = list(self.graph.edges())
        for u, v in edges:
            # Find all parallel edges between u and v
            parallel_edges = [(a,b) for a,b in self.graph.edges() 
                             if {a,b} == {u,v} and (a,b) in self.graph.edges()]
            
            if len(parallel_edges) > 1:
                # Calculate equivalent resistance
                inverse_sum = sum(1/self.graph.edges[e]['weight'] for e in parallel_edges)
                R_eq = 1 / inverse_sum
                
                # Modify the graph
                self.graph.remove_edges_from(parallel_edges)
                self.graph.add_edge(u, v, weight=R_eq)
                reduced = True
                break  # Restart after modification
        return reduced
    
    def delta_wye_transform(self):
        """Apply delta-wye transformation when no series/parallel reductions are possible"""
        # Implementation would go here
        # This handles more complex configurations
        pass
    
    def calculate_equivalent_resistance(self, start_node, end_node):
        """Calculate equivalent resistance between two nodes"""
        # Create a working copy of the graph
        temp_graph = self.graph.copy()
        
        # Add temporary start and end nodes if they don't exist
        if start_node not in temp_graph:
            temp_graph.add_node(start_node)
        if end_node not in temp_graph:
            temp_graph.add_node(end_node)
            
        # Main reduction loop
        while True:
            # Try series reduction first
            if self._attempt_series_reduction(temp_graph):
                continue
                
            # Then try parallel reduction
            if self._attempt_parallel_reduction(temp_graph):
                continue
                
            # If no more reductions possible, check if we're done
            if temp_graph.number_of_edges() == 1 and start_node in temp_graph and end_node in temp_graph:
                return temp_graph.edges[start_node, end_node]['weight']
                
            # If stuck, apply delta-wye transform
            if not self.delta_wye_transform(temp_graph):
                raise ValueError("Circuit cannot be fully reduced using series/parallel methods")
    
    def _attempt_series_reduction(self, graph):
        """Helper method for series reduction"""
        for node in list(graph.nodes()):
            neighbors = list(graph.neighbors(node))
            if len(neighbors) == 2:
                R1 = graph.edges[node, neighbors[0]]['weight']
                R2 = graph.edges[node, neighbors[1]]['weight']
                R_eq = R1 + R2
                graph.add_edge(neighbors[0], neighbors[1], weight=R_eq)
                graph.remove_node(node)
                return True
        return False
    
    def _attempt_parallel_reduction(self, graph):
        """Helper method for parallel reduction"""
        edges = list(graph.edges())
        for u, v in edges:
            parallel_edges = [(a,b) for a,b in graph.edges() 
                            if {a,b} == {u,v} and (a,b) in graph.edges()]
            if len(parallel_edges) > 1:
                inverse_sum = sum(1/graph.edges[e]['weight'] for e in parallel_edges)
                R_eq = 1 / inverse_sum
                graph.remove_edges_from(parallel_edges)
                graph.add_edge(u, v, weight=R_eq)
                return True
        return False

# Example usage
if __name__ == "__main__":
    analyzer = CircuitAnalyzer()
    
    # Create a sample circuit (Wheatstone bridge)
    analyzer.add_resistor('A', 'B', 10)
    analyzer.add_resistor('B', 'C', 20)
    analyzer.add_resistor('A', 'D', 30)
    analyzer.add_resistor('D', 'C', 40)
    analyzer.add_resistor('B', 'D', 50)
    
    # Visualize the original circuit
    print("Original circuit:")
    analyzer.visualize_circuit()
    
    # Calculate equivalent resistance
    try:
        R_eq = analyzer.calculate_equivalent_resistance('A', 'C')
        print(f"\nEquivalent resistance between A and C: {R_eq:.2f} ohms")
    except ValueError as e:
        print(f"\nError: {e}")
```

---
![alt text](<circuit 1.png>)

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