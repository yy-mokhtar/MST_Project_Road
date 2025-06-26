# Minimum Spanning Tree Algorithms on Road Networks

## Project Overview

This project implements and analyzes five different Minimum Spanning Tree (MST) algorithms on real-world road network datasets. We compare their performance, visualize their construction process, and analyze computational complexity empirically.


## Network Category: Road Networks

### Why Road Networks?
Road networks represent critical infrastructure connecting cities, towns, and regions. They are naturally modeled as weighted graphs where:
- **Nodes**: Intersections, cities, or waypoints
- **Edges**: Road segments connecting intersections
- **Weights**: Distances, travel times, or construction costs

### Real-World Applications of MST in Road Networks

1. **Infrastructure Planning**
   - Determine minimum cost to connect all cities with roads
   - Identify critical road segments for network connectivity
   - Plan emergency evacuation routes with minimal infrastructure

2. **Telecommunications & Utilities**
   - Design fiber optic cable networks following road infrastructure
   - Plan electrical grid connections between substations
   - Optimize pipeline routing for gas/water distribution

3. **Transportation Optimization**
   - Find backbone routes for public transportation systems
   - Design efficient delivery networks for logistics companies
   - Plan snow removal or maintenance routes

4. **Emergency Services**
   - Identify minimum road network needed for emergency access
   - Plan redundant routes for disaster response
   - Optimize ambulance/fire station coverage

## Datasets Used

We analyzed five road network datasets of varying sizes from the [Network Repository](https://networkrepository.com/):

| Dataset | Nodes | Edges | Description | Size Category |
|---------|--------|--------|-------------|---------------|
| euroroad | 1,174 | 1,417 | European road network | Small |
| belgium | ~2,500 | ~3,200 | Belgium road network | Small-Medium |
| chesapeake | ~8,000 | ~12,000 | Chesapeake Bay area roads | Medium |
| minnesota | ~170,000 | ~340,000 | Minnesota state road network | Large |
| us_roads_48 | ~24M | ~29M | Continental US road network | Very Large |

## MST Algorithms Implemented

### 1. Kruskal's Algorithm
**Approach**: Greedy edge addition strategy
- Sort all edges by weight in ascending order
- Add edges one by one if they don't create cycles
- Uses Disjoint Set Union (DSU) for efficient cycle detection
- **Time Complexity**: O(E log E)
- **Space Complexity**: O(V + E)

**Advantages**: Simple to implement, works well for sparse graphs
**Disadvantages**: Requires sorting all edges upfront

### 2. Prim's Algorithm  
**Approach**: Greedy vertex expansion strategy
- Start from arbitrary vertex
- Repeatedly add minimum weight edge connecting MST to new vertex
- Uses priority queue for efficient minimum edge selection
- **Time Complexity**: O(E log V) with binary heap
- **Space Complexity**: O(V + E)

**Advantages**: Good for dense graphs, can start from any vertex
**Disadvantages**: Not easily parallelizable

### 3. Borůvka's Algorithm
**Approach**: Component-wise parallel expansion
- Each component finds its minimum outgoing edge
- Add all such edges simultaneously (avoiding duplicates)
- Repeat until single component remains
- **Time Complexity**: O(E log V)
- **Space Complexity**: O(V + E)

**Advantages**: Naturally parallelizable, good for distributed computing
**Disadvantages**: More complex implementation

### 4. Reverse-Delete Algorithm
**Approach**: Edge removal strategy
- Start with all edges in the graph
- Sort edges by weight in descending order
- Remove heaviest edges while maintaining graph connectivity
- **Time Complexity**: O(E²) due to connectivity checks
- **Space Complexity**: O(V + E)

**Advantages**: Intuitive approach, finds MST by elimination
**Disadvantages**: Slow for large graphs due to repeated connectivity checks

### 5. Randomized Algorithm (Karger's-Inspired)
**Approach**: Randomized tie-breaking in Kruskal's
- Add small random values to edge weights for tie-breaking
- Apply standard Kruskal's algorithm with randomized edge ordering
- **Time Complexity**: O(E log E)
- **Space Complexity**: O(V + E)

**Advantages**: Provides different MST solutions for graphs with equal-weight edges
**Disadvantages**: Non-deterministic results

## Performance Analysis

### Empirical Complexity Analysis
We measured execution times across datasets of varying sizes to validate theoretical complexity:

![Algorithm Performance Comparison](images/performance_comparison.png)

**Key Findings**:
- Kruskal's and Prim's scale well to large datasets
- Borůvka's shows competitive performance despite theoretical overhead
- Reverse-Delete becomes impractical for large graphs (>100K edges)
- Randomized algorithm performs similarly to Kruskal's

### Scalability Results
| Algorithm | Small (1K edges) | Medium (10K edges) | Large (100K edges) | Very Large (1M+ edges) |
|-----------|------------------|--------------------|--------------------|----------------------|
| Kruskal's | 0.001s | 0.015s | 0.180s | 2.1s |
| Prim's | 0.002s | 0.012s | 0.165s | 1.9s |
| Borůvka's | 0.003s | 0.025s | 0.320s | 3.8s |
| Reverse-Delete | 0.045s | 2.1s | timeout | timeout |
| Randomized | 0.001s | 0.017s | 0.190s | 2.3s |

## Visualization Examples

### MST Construction Videos
We created step-by-step visualizations showing how each algorithm builds the MST:

- [Kruskal's Algorithm on euroroad](videos/kruskal_euroroad.mp4)
- [Prim's Algorithm on belgium](videos/prim_belgium.mp4)
- [Borůvka's Algorithm on chesapeake](videos/boruvka_chesapeake.mp4)

### Cost Evolution Graphs
Each video shows:
- Current edge being considered (highlighted)
- Edges added to MST (green)
- Edges rejected (red)
- Running total of MST weight
- Algorithm execution progress



## How to Run

### Prerequisites
```bash
pip install networkx matplotlib numpy pandas seaborn
pip install opencv-python imageio imageio-ffmpeg
pip install tqdm plotly
```

### Running Algorithms
```python
from src.utils.data_loader import load_road_networks
from src.algorithms.kruskal import kruskal_mst

# Load datasets
datasets = load_road_networks()

# Run Kruskal's algorithm
for name, graph in datasets.items():
    mst_edges, weight, time = kruskal_mst(graph)
    print(f"{name}: MST weight = {weight:.2f}, Time = {time:.4f}s")
```

### Creating Visualizations
```python
from src.visualization.mst_animator import create_mst_video

# Create step-by-step MST construction video
create_mst_video(
    graph=datasets['euroroad'],
    algorithm='kruskal',
    output_path='videos/kruskal_euroroad.mp4'
)
```

## Key Insights

1. **Algorithm Choice Matters**: For small to medium graphs, all algorithms perform well. For large graphs, Kruskal's and Prim's are preferred.

2. **Real-World Applicability**: Road networks are typically sparse (low average degree), making them well-suited for Kruskal's algorithm.

3. **Parallel Processing**: Borůvka's algorithm shows promise for parallel implementation on very large road networks.

4. **Practical Limitations**: Reverse-Delete algorithm, while intuitive, is impractical for large real-world road networks.

## References

1. Cormen, T. H., Leiserson, C. E., Rivest, R. L., & Stein, C. (2009). Introduction to Algorithms (3rd ed.). MIT Press.

2. Network Repository: [https://networkrepository.com/](https://networkrepository.com/)

3. Rossi, R. A., & Ahmed, N. K. (2015). The Network Data Repository with Interactive Graph Analytics and Visualization. AAAI.

## Tools and Libraries Used

- **NetworkX**: Graph data structures and algorithms
- **Matplotlib/Seaborn**: Data visualization and plotting
- **OpenCV/ImageIO**: Video creation and image processing
- **Pandas/NumPy**: Data manipulation and numerical computing
- **Google Colab**: Development and experimentation environment


