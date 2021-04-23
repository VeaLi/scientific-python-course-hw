## return tuples


```python
from collections import deque


class Iterator(object):
    def __init__(self, iterable):
        self.iterable = iterable
        self.L = len(self.iterable) if len(
            self.iterable) % 2 == 0 else len(self.iterable) - 1
        self.iterable = self.iterable[:self.L]
        self.iterable = deque(self.iterable)

    def __iter__(self):
        return self

    def __next__(self):
        if self.iterable:
            a, b = self.iterable.popleft(), self.iterable.popleft()
            return a, b
        else:
            raise StopIteration()
```


```python
x = Iterator(iterable = list(range(11)))
```


```python
for n in x:
    print(n)
```

    (0, 1)
    (2, 3)
    (4, 5)
    (6, 7)
    (8, 9)
    

# HW

You are given a description of class inheritance in JSON format.The description is an array of JSON objects that correspond to the classes.Each JSON object has a field ​name​ that contains the class name, and a ​parent​ field thatcontains a list of object’s direct ancestors names.


```python
IN1 = [{
    "name": "A",
    "parents": []
}, {
    "name": "B",
    "parents": ["A", "C"]
}, {
    "name": "C",
    "parents": ["A"]
}]

IN2 = [{
    "name": "B",
    "parents": ["A", "C"]
}, {
    "name": "C",
    "parents": ["A"]
}, {
    "name": "A",
    "parents": []
}, {
    "name": "D",
    "parents": ["C", "F"]
}, {
    "name": "E",
    "parents": ["D"]
}, {
    "name": "F",
    "parents": []
}]
```


```python
import networkx as nx
from networkx.readwrite import json_graph


def to_graph(IN):
    G = nx.DiGraph()
    for el in IN:
        child = el['name']
        parents = el['parents']

        for p in parents:
            G.add_edge(p, child)

    return G



```


```python
# TEST 1
G = to_graph(IN1)
for node in sorted(G.nodes):
    print(f"{node} : {len(nx.descendants(G, node))+1}")
```

    A : 3
    B : 1
    C : 2
    


```python
# TEST 2
G = to_graph(IN2)
for node in sorted(G.nodes):
    print(f"{node} : {len(nx.descendants(G, node))+1}")
```

    A : 5
    B : 1
    C : 4
    D : 2
    E : 1
    F : 3
    
