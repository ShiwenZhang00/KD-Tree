# KD-Tree


## Implement and explain insert() and range()

```python
    def insert(self, p: List[Point]):
        """insert a list of points"""
        self._root = self._insert_helper(p, 0)

    def _insert_helper(self, p: List[Point], depth: int) -> Node:
        # base case
        if len(p) == 0:
            return None

        # calculate the axis
        axis = depth % 2
        # sort the points based on the axis
        p.sort(key=lambda point: point[axis])
        # get the median point
        median_index = len(p) // 2
        median_point = p[median_index]

        # create a new node
        # recursively build the left and right subtree
        node = Node(location=median_point, left=self._insert_helper(p[:median_index], depth + 1), right=self._insert_helper(p[median_index + 1:], depth + 1))
        return node

    def range(self, rectangle: Rectangle) -> List[Point]:
        """range query"""
        result = []
        self._range_helper(self._root, rectangle, 0, result)
        return result

    def _range_helper(self, node: Node, rectangle: Rectangle, depth: int, result: List[Point]):
                if node is None:
                    return

                # calculate the axis
                axis = depth % 2

                # check if the location of the node is inside the rectangle
                if rectangle.is_contains(node.location):
                    result.append(node.location)

                # check if the left subtree should be searched
                if node.location[axis] >= rectangle.lower[axis] and rectangle.lower[axis] <= node.location[axis]:
                    self._range_helper(node.left, rectangle, depth + 1, result)

                # check if the right subtree should be searched
                if node.location[axis] <= rectangle.upper[axis] and rectangle.upper[axis] >= node.location[axis]:
                    self._range_helper(node.right, rectangle, depth + 1, result)
```



## Implement the nearest neighbor query

```python
def nearest_point_query(kd: KDTree, point: Point) -> Point:
    """nearest point query"""
    nearest_node = _nearest_point_query_helper(kd._root, point, 0)
    return nearest_node.location

def _nearest_point_query_helper(node: Node, point: Point, depth: int) -> Node:
    if node is None:
        return None
    
    # calculate the axis
    axis = depth % 2

    # compare the location of the node and the point
    if node.location == point:
        return node
    
    # check if the left subtree should be searched
    if point[axis] < node.location[axis]:
        next_node = _nearest_point_query_helper(node.left, point, depth + 1)
    
    # check if the right subtree should be searched
    else:
        next_node = _nearest_point_query_helper(node.right, point, depth + 1)

    # compare the distance from the node and the point
    # with the distance from the next node and the point
    if next_node is None or (node.location.x - point.x) ** 2 + (node.location.y - point.y) ** 2 < (next_node.location.x - point.x) ** 2 + (next_node.location.y - point.y) ** 2:
        return node
    else:
        return next_node if next_node else node
def nearest_point_query_test():
    points = [Point(7, 2), Point(5, 4), Point(9, 6), Point(4, 7), Point(8, 1), Point(2, 3)]
    kd = KDTree()
    kd.insert(points)
    point = Point(7, 6)
    result = nearest_point_query(kd, point)
    assert Point(9,6) == result
```


