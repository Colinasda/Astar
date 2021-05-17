- [Astar](#astar)
  - [1 Question](#1-question)
  - [2 Specific Process](#2-specific-process)
    - [Step1. Compute the weights between different nodes](#step1-compute-the-weights-between-different-nodes)
    - [Step2.  Construct nodes information](#step2--construct-nodes-information)
    - [Step3.  Use nodes to build the graph](#step3--use-nodes-to-build-the-graph)
    - [Step4.  A* search](#step4--a-search)
    - [Step5.  Main function](#step5--main-function)

# Astar

## 1 Question
Shortest path problem solved by A* algorithm

## 2 Specific Process

### Step1. Compute the weights between different nodes

First, we need to compute the weights between different nodes based on the problem formula.

![](https://tva1.sinaimg.cn/large/008eGmZEgy1gpmrocwhxej30bq024dfx.jpg)

The relevant operation is shown below. Then we can get the relevant weights.

```python
# Compute the weights between different nodes
length = 7
input_matrix = [[0,1,1],
                [0,2,3],
                [0,3,2],
                [1,4,5],
                [1,6,3],
                [2,4,4],
                [2,5,3],
                [3,5,2],
                [3,6,7],
                [4,7,4],
                [5,7,1],
                [6,7,1]]
for i in range (len(input_matrix)):
    start = input_matrix[i][0]
    end = input_matrix[i][1]
    value = input_matrix[i][2]
    print((start+end)*value)
```



### Step2.  Construct nodes information

First, we need to build nodes information and some built-in function. To be specific, we need to build the following function:

* Check if two nodes are identical.
* Sort nodes based on the total cost.
* Print node results of the best solution.



### Step3.  Use nodes to build the graph

We need to add connection between relevant nodes so that we can construct the graph. Besides, we need a method to display all nodes. The relevant code is shown below.

```python
		# Add conection between adjacent nodes
    def add_connection(self, node_a, node_b, distance=1):
        self.input_dict.setdefault(node_a, {})[node_b] = distance     
        self.input_dict.setdefault(node_b, {})[node_a] = distance

    # get neighbours of the current node
    def get(self, node_a, node_b=None):
        connections = self.input_dict.setdefault(node_a, {})
        if node_b is None:
            return connections
        else:
            return connections.get(node_b)

    # Display all nodes in the graph
    def display_all_nodes(self):
        citys = set([city for city in self.input_dict.keys()])
        city_distances = set([distances for dist in self.input_dict.values() for distances, dist_2 in dist.items()])
        display_all_nodes = citys.union(city_distances)
        return list(display_all_nodes)
```



### Step4.  A* search

The core idea of A* algorithm is f(n)=g(n)+h(n), where

* f(n) is the cost estimate from the initial state through state n to the target state
* g(n) is the actual cost of going from the initial state to state n in the state space
* h(n) is the estimated cost of the optimal path from state n to the target state.

For each exploration, we choose the minimum value of f(n) as the next action. Since our goal is to find the minimum cost value, when we find the first ending path(from V0 to V7), we can use it as a baseline .

In the process of calculating the cost value of other paths, if the cost value exceeds this baseline, we do not need to calculate this path, because it is bound to exceed this baseline. 

Based on the above idea, the implementation of A* algorithm is shown below.

```python
def a_star_search(graph, heuristics, start, end):
    not_expanded = [] # Not Expanded
    expanded = [] # expanded
    start_node = Node(start, None)
    goal_node = Node(end, None)
    # Add the start node to not_expanded list
    not_expanded.append(start_node)
    # Loop until all nodes in not_expanded are expanded
    while len(not_expanded) > 0:
        # Sort The List
        not_expanded.sort()
        # Get the node with the lowest cost
        current_node = not_expanded.pop(0)
        # Add the current node to the expanded list
        expanded.append(current_node)  
        # Check if we have reached the goal if reached start backtrack , else continue
        if current_node == goal_node:
            # List to Store Shrtest Path
            path = []       
            # Backtrack back to the start node 
            while current_node != start_node:
                path.append("V"+current_node.name + ': ' + str(current_node.dist_to_start_node))
                current_node = current_node.parent
            path.append("V"+start_node.name + ': ' + str(start_node.dist_to_start_node))
            return path[::-1]

        # Get neighbours
        neighbors = graph.get(current_node.name)

        # Loop over neighbours
        for key, value in neighbors.items():
            neighbor = Node(key, current_node)
            # Check if the neighbor is in the expanded list
            if(neighbor in expanded):
                continue
            # A* algorithm:f(n)=g(n)+h(n)
            # Calculate full path cost
            neighbor.dist_to_start_node = current_node.dist_to_start_node + graph.get(current_node.name, neighbor.name)
            neighbor.dist_to_goal_node = heuristics.get(neighbor.name)
            neighbor.total_dist = neighbor.dist_to_start_node + neighbor.dist_to_goal_node

            # Check if this Node Path's f(x) A* Value is > or not           
            for node in not_expanded:
                if (neighbor == node and neighbor.total_dist > node.total_dist):
                    continue
            not_expanded.append(neighbor)
    return None
```



### Step5.  Main function

In this step, we can add the connection into the graph based on the adjacency matrix. The process is like:

```python
graph.add_connection('0', '1', 1)
graph.add_connection('0', '2', 6)
graph.add_connection('0', '3', 6)
graph.add_connection('1', '4', 25)
graph.add_connection('1', '6', 21)
graph.add_connection('2', '4', 24)
graph.add_connection('2', '5', 21)
graph.add_connection('3', '5', 16)
graph.add_connection('3', '6', 63)
graph.add_connection('4', '7', 44)
graph.add_connection('5', '7', 12)
graph.add_connection('6', '7', 13)
```

We also need a dictionary to store the middle calculation results. So we can initial the dictionary.

Once we input the source point and destination point, this A* model will show us the shortest path like this:

![](https://tva1.sinaimg.cn/large/008eGmZEgy1gpmsk97kd0j30cy03zt92.jpg)



