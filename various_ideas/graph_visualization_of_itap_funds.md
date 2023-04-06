# Idea Description
I have a system, that can store information about portfolio holdings. I have a case where a portfolio may hold another portfolio as a security. I call portfolios held in positions as securities ITAP portfolio. In this system a portfolio can hold an ITAP portfolios and then that ITAP portfolios can hold yet another ITAP portfolio. I want to visualise these holdings using graph network. I want to represent portfolios as nodes. And when portfolio A holds portfolio B I want to draw a pointed arrow from A to B. Can you help me if can quickly visualize this in python?

# Idea Solution
Yes, you can use the Python library networkx to create a graph network of portfolios and their holdings. Here's an example code snippet:

```python
import networkx as nx
import matplotlib.pyplot as plt

# Define the portfolios and their holdings
portfolios = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': [],
    'D': ['E', 'F'],
    'E': [],
    'F': ['G'],
    'G': ['H'],
    'H': []
}

# Create a directed graph
G = nx.DiGraph()

# Add the portfolios as nodes
G.add_nodes_from(portfolios.keys())

# Add the holdings as edges
for portfolio, holdings in portfolios.items():
    for holding in holdings:
        G.add_edge(portfolio, holding)

# Draw the graph
pos = nx.spring_layout(G)
nx.draw_networkx_nodes(G, pos, node_size=1000)
nx.draw_networkx_edges(G, pos, arrows=True)
nx.draw_networkx_labels(G, pos, font_size=14, font_family='sans-serif')
plt.axis('off')
plt.show()
```

In this example, we first define the portfolios and their holdings as a dictionary, where the keys are the portfolio names and the values are lists of holding portfolio names.

Then we create a directed graph using the ```DiGraph()``` constructor from the ```networkx``` library. We add the portfolios as nodes using the ```add_nodes_from()``` method, and we add the holdings as directed edges using the ```add_edge()``` method.

Finally, we draw the graph using the ```draw_networkx_nodes()```, ```draw_networkx_edges()```, and ```draw_networkx_labels()``` methods, and we use the ```spring_layout()``` function to set the positions of the nodes. We also turn on the arrows for the directed edges using the ```arrows=True``` parameter, and we turn off the axis labels using the ```plt.axis('off')``` method.

You can customize the graph visualization by modifying the parameters of the ```draw_networkx_*``` methods, such as the node size, font size, font family, and edge colors