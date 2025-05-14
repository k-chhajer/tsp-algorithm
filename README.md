# 🚚 Travelling Salesman Problem with Vehicle Routing (ECE297 M4)

This project solves a variation of the Travelling Salesman Problem (TSP) tailored for a courier company with multiple delivery tasks and depots. The goal is to determine an efficient route that starts and ends at a depot, icks up and drops off packages in a valid order and minimizes the total travel time, considering turn pernalties, node connectivity and other real world constraints.

## 📦 Problem Overview
A courier truck must start at one of several depots, make a series of pickups and drop-offs, and return to any depot. Each delivery consists of a pickup and a corresponding drop-off intersection. Deliveries can be completed in any order, but a package must be picked up before it can be dropped off.

This is a computationally hard problem. There is no known polynomial-time algorithm that guarantees an optimal solution. Therefore, the idea to approach the problem is outlined as follows:

### 1. Initial Route Construction

### 2. Local Search (Optimization)
## ✅ Constraints and Assumptions

- Solution must make all deliveries in valid order (package must be picked up before dropped off)
- A single visit to pickup/dropoff intersection is sufficient for multiple packages
- Route must start and end at any depot
- If no valid route exists, return an empty vector
- Solution must be returned within **50 seconds of wall clock time**
  
## ⚙️  Algorithm Pipeline

### **1. Input Preprocessing**
  - Nodes are classified as:
    - ```depotType:``` Starting/ending points
    - ```pickupType``` and ```dropoffType:``` Delivery pairs
  - Nodes are indexed: ```0..depots```, ```depots..depots+pickups```, ```depots+pickups...depots+pickups+dropoffs```
### **2. Distance Matrix Generation**
  - Uses multi-threaded Dijkstra with A* to compute travel times between all nodes
    - Turn penalties included
    - Results stored in a ```dist[i][j]``` matrix for fast lookup
### **3. Initial Path Construction**
  - Applies **regret insertion heuristic**:
    - Greedily insert pickup/dropoff pairs with the highest regret (difference between best and second-best insertion)
    - Chooses best depot pair for start and end based on overall cost
### **4. Route Refinement**
  - Run **2-opt**, **3-opt**, and **4-opt** moves while ensuring pickup-before-dropoff constraints
  - Use **shake2** perturbation to escape local optima by swapping delivery pairs
  - Optimize 3-opt and 4-opt using Multi-threading
  - Keep optimizing till wall clock of 50 seconds is reached (in practice this was kept it till 48 seconds to allow time to preprocess and return final output)
### **5. Final Path Construction**
  - Remove consecutive duplicate intersections
  - Construct valid subpaths using ```findPathBetweenIntersections()```
  - Return result as a list of ```CourierSubPath``` objects

The diagram outlining the pipeline is given below:
![Algorithm Performance](/files/algo_pipeline.png)

## Performance
The timeline of the algorithm's performance on public test cases:

![Algorithm Performance](/files/qor.gif)

Final Place on Leaderboard: 6th (Public + Private Test Cases)

## Future Considerations
The algorithm can be improved significantly by:
1. Further Optimizing 3-opt and 4-opt by improving multi-threading and considering more permutations and combinations of different segments of the route
2. The order in which the optimization algorithms are implemented have a significant impact on the performance and different combinations can be tried to obtain better results
3. Incorporating Simulated Annealing can also significantly improve the algorithm's performance when different optimizations techniques do not yield improved results and optimizations may be applied to slightly worse results to escape the local minima

## Acknowledgement
Shoutout to my ECE297 teammates Luthira Abeykoon (@Luthiraa) and Grace Hao (@gracehao15)
