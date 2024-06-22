<div align="center">

# A-Star Practice

Another old school java exercise

</div>

# Overview

The A* algorithm is a fundamental programming algorithm that aids in practicing
iteration and fostering programming thinking. It is also a practical algorithm
widely used in real-world applications.

# Task Checklist

### 1. Node and decorator

#### Instruction:

Programming with object-oriented languages can sometimes be challenging.
Developers might initially rely on procedural programming techniques, such as
wrapping an instance inside another. However, these practices can increase
coupling and are not ideal in an object-oriented context. The Decorator Pattern
emerged as a solution, allowing new functionality to be added to an existing
object without altering its structure.

#### Procedural:

1. **Implement node interface**: The `INode` interface defines the getter and
   setter structure, attempt to find the best match of the private field.

2. **Extend accessible decorator**: Create an accessible decorator to describe
   the accessibility of the node in a grid. 

#### Requirement:

A class inherited the `INode` interface and a decorator class extend
`INode.Decorator`.

```java
INode node = new NodeDecoratorImpl(new NodeImpl());
```

### 2. grid graph and builder

#### Instruction:

When designing a program, starting with a simple model is reasonable. For
complex nodes-edges-graph relationships, simplify them into nodes-graph by
assuming equal edges which place in a grid for a minimal prototype. After that
create a builder for graph, allowing quicker initialization of the graph
instance.

#### Procedural:

1. **Implement grid graph by multidimensional array**: Due to the
   standardization of the grid, arrays can be directly used for operations.
   Design a grid 2d graph and a grid 3d graph with two or three-dimensional
   array and implement the `IGraph` interface.

2. **Implement grid graph builder**: Implement `IGraphBuilder` interface and
   finalize the related build function.

   ```java
   IGraph build(final int width, final int height);
   IGraph build(final int width, final int height, final int depth);
   ```

#### Requirement:

A class inherited the `IGraph` interface and a builder inherited the
`IGraphBuilder` interface.

### 3. heuristic and strategy

#### Instruction:

For pluggable code, the strategy pattern is the best solution. For example, the
Manhattan distance has better performance in grid graphs, while the Euclidean
distance can better calculate Euclidean based graphs.

#### Procedural:

1. **Implement Manhattan heuristic**: The Manhattan distance between two points
   $x$ and $y$ is $d(x, y) = |x_1 - x_2| + |y_1 - y_2|$

2. **Implement Euclidean heuristic**: The Euclidean distance between two points
   is $d(x, y) = \sqrt {(x_1 - y_2)^2 + (y_1 - y_2)^2}$ or
   $d(x, y, z) = \sqrt {(x_1 - y_2)^2 + (y_1 - y_2)^2 + (z_1 - z_2)^2}$

#### Requirement:

At least one or more classes inherited the `IHeuristic` interface.

### 4. Pathfinder and observer

#### Instruction:

The A* algorithm description refer to [wiki](https://en.wikipedia.org/wiki/A*_search_algorithm#Description)
which can be summarized to the following pseudocode:

```
function reconstruct_path(path, current)
  total_path = [current]
  while current in path do
    current = path[current]
    total_path.append(current)
  return total_path

function a_star(start_node, goal_node, heuristic)
  open = [start_node]
  closed = []

  g_score[start_node] = 0
  f_score[start_node] = heuristic(start_node, goal_node)

  while open is not empty do
    current_node = node in open with lowest f_score[current_node]

    if current_node == goal_node then
      return reconstruct_path(closed, current_node)

    open.remove(current_node)

    for each neighbor in neighbor_nodes(current_node) do
      if neighbor is not traversable or neighbor in closed then
        continue

      tentative_g_score = g_score[current_node] + heuristic(current_node, neighbor)
      if tentative_g_score < g_score[neighbor] or neighbor is not in open
        closed[neighbor] = current_node
        g_score[neighbor] = tentative_g_score
        f_score[neighbor] = tentative_g_score + heuristic(neighbor, goal_node)
        if neighbor not in open
          open.append(neighbor)

  return failure
```

The algorithm serves as the core and is reusable. This allows for abstraction
and application design patterns, resulting in more structured code. We consider
using the Bridge Pattern. Additionally, we can apply the Observer Pattern,
enabling observers to record and monitor the process by extending the algorithm.

#### Procedural:

1. **Implement pathfinder**: Implement `IPathfinder` interface and consider
   PriorityQueue $O(n^2)$ or TreeSet $O(log_n)$ to achieve filtering and sorting,
   mark it as abstract and build interface for the subclass.

2. **Implement pathfinder subject**: Implement the subclass of pathfinder
   serve as concrete subject for observer.

3. **Check observer can register into pathfinder subject**: Implement
   `IPathfinderObserver` interface and try call register observer in pathfinder.

### 5. Mesh graph and Simple Stupid Funnel

#### Instruction:

After completing the prototype, it's time to develop a more optimized and
efficient version. Instead of using a grid search, which can be computationally
expensive, a navigation mesh (navmesh) is a more effective alternative. We will
use a simple graph structure based on an adjacency list to represent this
navigation mesh. After we find path, we will do simple stupid funnel to optimize
the final result. Here is the pseudocode:

```
function funnel_algorithm(path)
  if length(path) <= 2 then
    return path

  apex_index = 0
  left_index = 1
  right_index = 1

  funnel = [path[apex_index]]

  while right_index < length(path) do
    apex = path[apex_index]
    apex_left = path[left_index]
    apex_right = path[right_index]
    current = path[right_index]

    if current is left of apex then
      if current is right of apex_left then
        left_index = right_index
      else
        funnel.append(path[left_index])
        apex_index = left_index
        left_index = apex_index + 1
        right_index = left_index
    else
      if current is left of apex_right then
        right_index = right_index
      else
        funnel.append(path[right_index])
        apex_index = right_index
        right_index = apex_index + 1
        left_index = right_index
    right_index = right_index + 1

  funnel.append(path[length(path) - 1])
  return funnel
```

#### Procedural:

1. **Implement mesh graph**: Store the index of the mesh as a node, and use
   indices as edges to construct the graph. Consider using an uuid or unique id
   to facilitate queries on node collections.

2. **Implement simple stupid funnel**: Implement the funnel algorithm based on
   pseudocode.
