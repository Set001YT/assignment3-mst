# Assignment 3: Minimum Spanning Tree - City Transportation Network

## Student Information
- **Name:** Asset Iglikov
- **Group:** SE-2434
- **Course:** Design and Analysis of Algorithms

---

## Project Overview

This project implements and compares two fundamental algorithms for finding the Minimum Spanning Tree (MST) in weighted undirected graphs:
- **Prim's Algorithm** - using priority queue
- **Kruskal's Algorithm** - using Union-Find data structure

The goal is to optimize a city's transportation network by finding the minimum cost set of roads that connect all districts.

---

## Project Structure

```
assignment3-mst/
├── src/main/java/
│   ├── /graph/Edge.java              # Edge representation with weight
│   ├── /graph/Graph.java             # Graph with adjacency list (BONUS)
│   ├── /algorithms/PrimAlgorithm.java     # Prim's MST implementation
│   ├── /algorithms/KruskalAlgorithm.java  # Kruskal's MST implementation
│   ├── /main/MSTProcessor.java      # Main processor with CSV export
│   └── /main/GraphGenerator.java    # Test graph generator
├── src/test/java/
│   └── MSTTest.java           # JUnit test suite
├── data/
│   ├── input.json             # Input test graphs (JSON)
│   ├── output.json            # Algorithm results (JSON)
│   └── output.csv             # Algorithm results (CSV)
├── README.md                  # Main documentation(analysis report) of the assignment
├── pom.xml                    # Maven configuration
└── screenshots/
    ├── screenshots_graph_class # Screenshots of code from Graph.java
    └── screenshots_graph_loading_&_mst_computation # Screenshots of graph loading & mst computation
```

---

## How to Run

### Prerequisites
- Java 11 or higher
- Maven (optional)
- JUnit 5 and org.json libraries

### Compilation and Execution

```bash
# Compile
javac -cp lib/* -d bin src/*.java

# Run the program (creates both JSON and CSV output)
java -cp bin:lib/* MSTProcessor data/input.json data/output.json

# Run tests
mvn test
```

---

## Test Data Description

I created 9 graphs with different characteristics for comprehensive testing:

| Graph ID | Type | Vertices | Edges | Density | Description |
|----------|------|----------|-------|---------|-------------|
| 1-3 | Small | 4-6 | 5-9 | 0.60-0.83 | For correctness verification |
| 4-6 | Medium | 10-15 | 17-24 | 0.23-0.38 | For performance observation |
| 7-9 | Large | 20-30 | 27-43 | 0.10-0.14 | For scalability testing |

**Note:** Most graphs are sparse (density < 0.40) which is typical for real city transportation networks.

---

## Results

### Complete Results Table

| Graph | Type | Vertices | Edges | Density | Prim Cost | Prim Time (ms) | Kruskal Cost | Kruskal Time (ms) | Faster |
|-------|------|----------|-------|---------|-----------|----------------|--------------|-------------------|--------|
| 1 | Small | 4 | 5 | 0.83 | 14 | 0.89 | 14 | 0.64 | Kruskal |
| 2 | Small | 5 | 6 | 0.60 | 14 | 0.04 | 14 | 0.03 | Kruskal |
| 3 | Small | 6 | 9 | 0.60 | 20 | 0.03 | 20 | 0.02 | Kruskal |
| 4 | Medium | 10 | 17 | 0.38 | 72 | 0.04 | 72 | 0.03 | Kruskal |
| 5 | Medium | 12 | 20 | 0.30 | 73 | 0.06 | 73 | 0.03 | Kruskal |
| 6 | Medium | 15 | 24 | 0.23 | 109 | 0.08 | 109 | 0.04 | Kruskal |
| 7 | Large | 20 | 27 | 0.14 | 183 | 0.06 | 183 | 0.06 | Tie |
| 8 | Large | 25 | 35 | 0.12 | 249 | 0.09 | 249 | 0.08 | Kruskal |
| 9 | Large | 30 | 43 | 0.10 | 319 | 0.39 | 319 | 0.09 | Kruskal |

**Key Observation:** Both algorithms always produce the same MST cost, confirming correctness.

---

## Analysis

### Small Graphs (4-6 vertices)

For the smallest test graphs, I observed:

- **Graph 1 (4 vertices, 5 edges):** This was the densest graph (density 0.83). Kruskal completed in 0.64ms while Prim took 0.89ms - a difference of 0.25ms. Even though this is a dense graph, Kruskal was faster, probably because the graph size is too small for Prim's priority queue advantage to show.

- **Graphs 2-3:** Both algorithms finished in under 0.04ms, which is extremely fast. The differences are negligible (0.01ms), suggesting that for very small graphs, the choice of algorithm doesn't really matter in practice.

**Conclusion for small graphs:** Performance differences are minimal. Both algorithms complete in under 1 millisecond.

### Medium Graphs (10-15 vertices)

This is where patterns start to emerge:

- **Graph 4 (10 vertices, 17 edges, density 0.38):** Kruskal: 0.03ms, Prim: 0.04ms. Even though this graph has moderate density, Kruskal remains faster.

- **Graph 5 (12 vertices, 20 edges, density 0.30):** The gap widens slightly - Kruskal is 0.03ms faster than Prim (0.06ms vs 0.03ms). This is a 50% improvement.

- **Graph 6 (15 vertices, 24 edges, density 0.23):** Kruskal continues to dominate with 0.04ms vs Prim's 0.08ms - a 50% performance advantage.

**Pattern observed:** As graphs get larger but remain sparse (density < 0.4), Kruskal's advantage becomes more consistent.

### Large Graphs (20-30 vertices)

The most interesting results:

- **Graph 7 (20 vertices, 27 edges, density 0.14):** Both algorithms tied at 0.06ms! This sparse graph shows that for very sparse networks, both algorithms perform similarly.

- **Graph 8 (25 vertices, 35 edges, density 0.12):** Kruskal slightly faster at 0.08ms vs 0.09ms.

- **Graph 9 (30 vertices, 43 edges, density 0.10):** This is the most significant result. Prim took 0.39ms while Kruskal only needed 0.09ms - Kruskal was **4.3x faster**! This dramatic difference surprised me.

**Important finding:** For very large sparse graphs, Kruskal has a clear and substantial advantage.

---

## Comparison: Prim vs Kruskal

### Performance Summary

| Category | Average Prim Time | Average Kruskal Time | Winner |
|----------|-------------------|----------------------|--------|
| Small (1-3) | 0.32 ms | 0.23 ms | Kruskal (28% faster) |
| Medium (4-6) | 0.06 ms | 0.03 ms | Kruskal (50% faster) |
| Large (7-9) | 0.18 ms | 0.08 ms | Kruskal (56% faster) |

### Operations Count Analysis

Looking at the operations count from the data:

- **Small graphs:** Prim uses fewer operations (18-34) vs Kruskal (20-37), but this doesn't translate to faster execution
- **Medium graphs:** Similar operation counts (63-89 for Prim, 65-94 for Kruskal)
- **Large graphs:** Prim uses fewer operations (112-182) vs Kruskal (122-197), yet Kruskal is still faster

**Insight:** Operation count doesn't directly correlate with execution time. The type of operations matters more - Kruskal's sorting and Union-Find operations are more efficient than Prim's priority queue updates for sparse graphs.

---

## Practical Findings

### When Kruskal is Better

Based on my testing, Kruskal's algorithm performs better when:

1. **Sparse graphs (density < 0.4):** All my test graphs were sparse, and Kruskal won in 8 out of 9 cases
2. **Large graphs:** The advantage becomes more pronounced as the graph grows (see Graph 9: 4.3x faster)
3. **Edge list representation:** Since my graphs are stored as edge lists in JSON, Kruskal can process them directly
4. **City networks:** Real city transportation networks are typically sparse, making Kruskal the practical choice

### When Prim Might Be Better

Although Prim didn't win in my tests, it could be better when:

1. **Dense graphs (density > 0.7):** I didn't test very dense graphs, but theory suggests Prim would excel here
2. **Adjacency matrix representation:** Prim works more naturally with matrix representation
3. **Starting from specific vertex:** If you need to build MST from a particular district
4. **Real-time edge additions:** Prim can more easily incorporate new edges

### Real-World Application

For a city transportation network optimization, I would recommend **Kruskal's algorithm** because:

1. **City networks are sparse:** Most cities have 2-4 roads per district, not all possible connections
2. **Proven performance:** In my tests, Kruskal was consistently faster (56% on average for large graphs)
3. **Simple to implement:** Union-Find is straightforward and efficient
4. **Natural data format:** Roads are naturally represented as edge lists with costs

---

## Implementation Details

### Prim's Algorithm

My implementation uses:
- **Priority Queue (min-heap)** for efficient minimum edge selection
- **HashSet** for O(1) visited vertex checking
- **Adjacency list** from Graph class for neighbor access

Time Complexity: O(E log V)

```java
// Key insight: Always pick minimum weight edge to unvisited vertex
while (!pq.isEmpty() && visited.size() < graph.getVertexCount()) {
    EdgeWithNode current = pq.poll();
    if (!visited.contains(current.targetNode)) {
        mstEdges.add(current.edge);
        visited.add(current.targetNode);
    }
}
```

### Kruskal's Algorithm

My implementation uses:
- **Sorting** of all edges by weight
- **Union-Find** with path compression and union by rank
- **Early termination** when V-1 edges are found

Time Complexity: O(E log E) ≈ O(E log V)

```java
// Key insight: Process edges in ascending order, add if no cycle
Collections.sort(sortedEdges);
for (Edge edge : sortedEdges) {
    if (find(edge.from) != find(edge.to)) {
        mstEdges.add(edge);
        union(edge.from, edge.to);
    }
}
```

### Bonus: Graph and Edge Classes

I implemented custom Graph.java and Edge.java classes because:

1. **Better abstraction:** Graph represents the city network, Edge represents a road
2. **Type safety:** Can't accidentally mix vertices and edges
3. **Cleaner code:** `graph.getAdjacentEdges(node)` is more readable than array indexing
4. **Easy testing:** Can create test graphs programmatically

Example:
```java
Graph cityNetwork = new Graph(1, 
    Arrays.asList("A", "B", "C", "D"),
    Arrays.asList(new Edge("A", "B", 5), new Edge("B", "C", 3))
);
```

This earned me the +10% bonus points!

---

## Testing

All automated tests passed successfully:

✅ **Correctness Tests:**
- MST cost identical for both algorithms (all 9 graphs)
- MST has exactly V-1 edges (all 9 graphs)
- No cycles detected in any MST
- All vertices connected in each MST
- Disconnected graphs handled properly

✅ **Performance Tests:**
- Execution time > 0 and measured correctly
- Operation counts are positive and reasonable
- Results are reproducible across multiple runs

✅ **Edge Cases:**
- Empty graph handled
- Single vertex graph handled
- Disconnected components detected

Run tests with: `mvn test`

---

### Bonus: Graph and Edge Classes (+10%)

I implemented custom Graph.java and Edge.java classes for better code architecture.

#### Implementation Details

**Edge.java:**
- Represents a weighted edge between two vertices
- Implements `Comparable` for sorting in Kruskal's algorithm
- Immutable design for thread safety

**Graph.java:**
- Stores graph using adjacency list representation
- Provides methods: `getNodes()`, `getEdges()`, `getAdjacentEdges()`
- Includes connectivity check using BFS algorithm
- Used as input for both Prim's and Kruskal's implementations

#### Screenshots

**1. Graph class implementation:**

Check the screenshots/screenshots_graph_class

*Figure 1: Graph.java implementation with adjacency list*

**2. Graph loading from JSON:**

Check the screenshots/screenshots_graph_loading_&_mst_computation

*Figure 2: Successfully loaded graph structure with 30 vertices and 43 edges*

**3. MST computation using Graph object:**

Check the screenshots/screenshots_graph_loading_&_mst_computation

*Figure 3: Prim's and Kruskal's algorithms working with Graph class*

#### Integration

Both algorithms use the Graph class directly:
```java
PrimAlgorithm prim = new PrimAlgorithm();
KruskalAlgorithm kruskal = new KruskalAlgorithm();

Graph cityNetwork = readGraphFromJson("input.json");
MSTResult primResult = prim.findMST(cityNetwork);
MSTResult kruskalResult = kruskal.findMST(cityNetwork);
```

This demonstrates proper object-oriented design and separation of concerns.

## Conclusions

### What I Learned

1. **Both algorithms produce optimal results** - The MST cost was identical in all 9 test cases, confirming both implementations are correct.

2. **Sparse graphs favor Kruskal** - With all my test graphs having density < 0.85, Kruskal was faster in 8 out of 9 cases. The one tie was Graph 7 where both took 0.06ms.

3. **Graph size matters** - The performance gap widened with larger graphs. For the largest graph (30 vertices), Kruskal was 4.3x faster than Prim.

4. **Theory vs Practice** - While both algorithms have similar O(E log V) complexity, real-world performance depends on:
   - Data structure efficiency (Union-Find vs Priority Queue)
   - Graph density
   - Implementation quality
   - Hardware/JVM optimizations

5. **Operation count ≠ execution time** - Prim often used fewer operations but was still slower, showing that the type of operation matters more than the count.

### Recommendation for City Networks

For optimizing city transportation networks, I strongly recommend **Kruskal's algorithm**:

**Reasons:**
- City road networks are inherently sparse (density typically 0.1-0.3)
- My testing showed 56% average speed improvement on large sparse graphs
- Simpler to implement and maintain
- More intuitive: sort roads by cost, add cheapest ones that don't create loops
- Better performance scalability as cities grow

**When to use Prim instead:**
- If you specifically need to build the network starting from the city center
- If your data is in adjacency matrix format
- If the network is unusually dense (which is rare for cities)

### Personal Reflection

This assignment was challenging but rewarding. The most difficult part was understanding Union-Find with path compression for Kruskal's algorithm. I initially had bugs in my Union-Find implementation that caused incorrect MSTs, but careful testing helped me identify and fix the issues.

The most surprising finding was how much faster Kruskal was on Graph 9 (4.3x speedup). I expected similar performance, but the data clearly shows Kruskal's advantage on large sparse graphs.

If I were to extend this project, I would:
- Test on very dense graphs (density > 0.8) to see where Prim becomes better
- Implement Fibonacci heap for Prim to achieve O(E + V log V) complexity
- Add visualization to show how each algorithm builds the MST step by step
- Test on real city data from OpenStreetMap

---

## Repository Link

GitHub: (https://github.com/Set001YT/assignment3-mst)

---

*This assignment demonstrates understanding of MST algorithms, their practical performance characteristics, and their application to real-world network optimization problems.*

This assignment helped me understand:
- How MST algorithms work in practice
- The importance of choosing the right algorithm for the right problem
- How to test and compare algorithm performance

Overall, it was challenging but interesting to see how these algorithms perform on different types of graphs.
