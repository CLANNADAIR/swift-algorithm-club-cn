# Minimum Spanning Tree (Unweighted Graph)
# 最小生成树（未加权图）

A minimum spanning tree describes a path that contains the smallest number of edges that are needed to visit every node in the graph.
最小生成树描述了包含访问图中每个节点所需的最小边数的路径。

Take a look at the following graph:
看一下下图：

![Graph](Images/Graph.png)

If we start from node `a` and want to visit every other node, then what is the most efficient path to do that? We can calculate this with the minimum spanning tree algorithm.
如果我们从节点`a`开始并想要访问每个其他节点，那么最有效的路径是什么？ 我们可以使用最小生成树算法来计算它。

Here is the minimum spanning tree for the graph. It is represented by the bold edges:
这是图表的最小生成树。 它由粗体边表示：

![Minimum spanning tree](Images/MinimumSpanningTree.png)

Drawn as a more conventional tree it looks like this:
绘制为更传统的树，它看起来像这样：

![An actual tree](Images/Tree.png)

To calculate the minimum spanning tree on an unweighted graph, we can use the [breadth-first search](../Breadth-First%20Search/) algorithm. Breadth-first search starts at a source node and traverses the graph by exploring the immediate neighbor nodes first, before moving to the next level neighbors. If we tweak this algorithm by selectively removing edges, then it can convert the graph into the minimum spanning tree.
要计算未加权图上的最小生成树，我们可以使用[广度优先搜索](../Breadth-First%20Search/) 算法。广度优先搜索从源节点开始，并在移动到下一级邻居之前首先通过探索直接邻居节点来遍历图。如果我们通过选择性地删除边缘来调整此算法，那么它可以将图形转换为最小生成树。

Let's step through the example. We start with the source node `a`, add it to a queue and mark it as visited.
让我们逐步完成这个例子。 我们从源节点`a`开始，将其添加到队列中并将其标记为已访问。

```swift
queue.enqueue(a)
a.visited = true
```

The queue is now `[ a ]`. As is usual with breadth-first search, we dequeue the node at the front of the queue, `a`, and enqueue its immediate neighbor nodes `b` and `h`. We mark them as visited too.
队列现在是`[a]`。 与广度优先搜索一样，我们将队列前面的节点`a`出列队列，并将其直接邻居节点`b`和`h`排入队列。 我们也将它们标记为访问过。

```swift
queue.dequeue()   // a
queue.enqueue(b)
b.visited = true
queue.enqueue(h)
h.visited = true
```

The queue is now `[ b, h ]`. Dequeue `b` and enqueue the neighbor node `c`. Mark it as visited. Remove the edge from `b` to `h` because `h` has already been visited.
队列现在是`[b, h]`。 将`b`出列并将邻居节点`c`排队。 将其标记为已访问。 将`b`的边缘移除到`h`，因为已经访问过`h`。

```swift
queue.dequeue()   // b
queue.enqueue(c)
c.visited = true
b.removeEdgeTo(h)
```

The queue is now `[ h, c ]`. Dequeue `h` and enqueue the neighbor nodes `g` and `i`, and mark them as visited.
队列现在是`[h, c]`。 将`h`出列并将邻居节点`g`和`i`排队，并将它们标记为已访问。

```swift
queue.dequeue()   // h
queue.enqueue(g)
g.visited = true
queue.enqueue(i)
i.visited = true
```

The queue is now `[ c, g, i ]`. Dequeue `c` and enqueue the neighbor nodes `d` and `f`, and mark them as visited. Remove the edge between `c` and `i` because `i` has already been visited.
队列现在是`[c, g, i]`。 将`c`出列并将邻居节点`d`和`f`排队，并将它们标记为已访问。 删除`c`和`i`之间的边缘，因为已经访问了`i`。

```swift
queue.dequeue()   // c
queue.enqueue(d)
d.visited = true
queue.enqueue(f)
f.visited = true
c.removeEdgeTo(i)
```

The queue is now `[ g, i, d, f ]`. Dequeue `g`. All of its neighbors have been discovered already, so there is nothing to enqueue. Remove the edges from `g` to `f`, as well as `g` to `i`, because `f` and `i` have already been discovered.
队列现在是`[g, i, d, f]`。 出队`g`。 它的所有邻居都已经被发现了，所以没有什么可以入队的。 删除`g`到`f`的边缘，以及`g`到`i`的边缘，因为已经发现了`f`和`i`。

```swift
queue.dequeue()   // g
g.removeEdgeTo(f)
g.removeEdgeTo(i)
```

The queue is now `[ i, d, f ]`. Dequeue `i`. Nothing else to do for this node.
队列现在是`[i, d, f]`。 出列`i`。 这个节点没有别的办法。

```swift
queue.dequeue()   // i
```

The queue is now `[ d, f ]`. Dequeue `d` and enqueue the neighbor node `e`. Mark it as visited. Remove the edge from `d` to `f` because `f` has already been visited.
队列现在是`[d, f]`。 将`d`出列并将邻居节点`e`排队。 将其标记为已访问。 删除`d`到`f`的边缘，因为已经访问了`f`。

```swift
queue.dequeue()   // d
queue.enqueue(e)
e.visited = true
d.removeEdgeTo(f)
```

The queue is now `[ f, e ]`. Dequeue `f`. Remove the edge between `f` and `e` because `e` has already been visited.
队列现在是`[f, e]`。 出列`f`。 删除`f`和`e`之间的边缘，因为已经访问过`e`。

```swift
queue.dequeue()   // f
f.removeEdgeTo(e)
```

The queue is now `[ e ]`. Dequeue `e`.
队列现在是`[e]`。 出列`e`。

```swift
queue.dequeue()   // e
```

The queue is empty, which means the minimum spanning tree has been computed.
队列为空，这意味着已计算出最小生成树。

Here's the code:

```swift
func breadthFirstSearchMinimumSpanningTree(graph: Graph, source: Node) -> Graph {
  let minimumSpanningTree = graph.duplicate()

  var queue = Queue<Node>()
  let sourceInMinimumSpanningTree = minimumSpanningTree.findNodeWithLabel(source.label)
  queue.enqueue(sourceInMinimumSpanningTree)
  sourceInMinimumSpanningTree.visited = true

  while let current = queue.dequeue() {
    for edge in current.neighbors {
      let neighborNode = edge.neighbor
      if !neighborNode.visited {
        neighborNode.visited = true
        queue.enqueue(neighborNode)
      } else {
        current.remove(edge)
      }
    }
  }

  return minimumSpanningTree
}
```

This function returns a new `Graph` object that describes just the minimum spanning tree. In the figure, that would be the graph containing just the bold edges.
此函数返回一个新的`Graph`对象，该对象仅描述最小生成树。 在图中，这将是仅包含粗体边的图。

Put this code in a playground and test it like so:
将此代码放在playground上并像这样测试：

```swift
let graph = Graph()

let nodeA = graph.addNode("a")
let nodeB = graph.addNode("b")
let nodeC = graph.addNode("c")
let nodeD = graph.addNode("d")
let nodeE = graph.addNode("e")
let nodeF = graph.addNode("f")
let nodeG = graph.addNode("g")
let nodeH = graph.addNode("h")
let nodeI = graph.addNode("i")

graph.addEdge(nodeA, neighbor: nodeB)
graph.addEdge(nodeA, neighbor: nodeH)
graph.addEdge(nodeB, neighbor: nodeA)
graph.addEdge(nodeB, neighbor: nodeC)
graph.addEdge(nodeB, neighbor: nodeH)
graph.addEdge(nodeC, neighbor: nodeB)
graph.addEdge(nodeC, neighbor: nodeD)
graph.addEdge(nodeC, neighbor: nodeF)
graph.addEdge(nodeC, neighbor: nodeI)
graph.addEdge(nodeD, neighbor: nodeC)
graph.addEdge(nodeD, neighbor: nodeE)
graph.addEdge(nodeD, neighbor: nodeF)
graph.addEdge(nodeE, neighbor: nodeD)
graph.addEdge(nodeE, neighbor: nodeF)
graph.addEdge(nodeF, neighbor: nodeC)
graph.addEdge(nodeF, neighbor: nodeD)
graph.addEdge(nodeF, neighbor: nodeE)
graph.addEdge(nodeF, neighbor: nodeG)
graph.addEdge(nodeG, neighbor: nodeF)
graph.addEdge(nodeG, neighbor: nodeH)
graph.addEdge(nodeG, neighbor: nodeI)
graph.addEdge(nodeH, neighbor: nodeA)
graph.addEdge(nodeH, neighbor: nodeB)
graph.addEdge(nodeH, neighbor: nodeG)
graph.addEdge(nodeH, neighbor: nodeI)
graph.addEdge(nodeI, neighbor: nodeC)
graph.addEdge(nodeI, neighbor: nodeG)
graph.addEdge(nodeI, neighbor: nodeH)

let minimumSpanningTree = breadthFirstSearchMinimumSpanningTree(graph, source: nodeA)

print(minimumSpanningTree) // [node: a edges: ["b", "h"]]
                           // [node: b edges: ["c"]]
                           // [node: c edges: ["d", "f"]]
                           // [node: d edges: ["e"]]
                           // [node: h edges: ["g", "i"]]
```

> **Note:** On an unweighed graph, any spanning tree is always a minimal spanning tree. This means you can also use a [depth-first search](../Depth-First%20Search) to find the minimum spanning tree.
> **注意：**在未加权的图上，任何生成树始终是最小的生成树。 这意味着您还可以使用[深度优先搜索](../Depth-First%20Search)来查找最小生成树。

*Written by [Chris Pilcher](https://github.com/chris-pilcher) and Matthijs Hollemans*
*作者：[Chris Pilcher](https://github.com/chris-pilcher)， Matthijs Hollemans*  
*翻译：[Andy Ron](https://github.com/andyRon)*  
