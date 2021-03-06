# Binary Search Tree
- Basic Binary Search Tree Implementations in Rust and Python with tests

# API
```
- get(key) -> value
- put(key, value)
- delete(key)
- does_contain(key) -> True/False
- is_empty() -> True/False
- size() -> int
- max() -> key
- min() -> key
- delete_min()
- delete_max()
- floor(key) -> key
  - return largest key less than or equal to given key
- ceiling(key) -> key
  - return smallest ke greaten than or equal to given key
- select(int k) -> key
- rank(key) -> int
  - return number of keys smaller than a given key
```

# Check integrity of BST
```
1. is_size_consistent(node)
- size consistent for all nodes

2. is_rank_consistent(node)
- rank and select makes sense for all nodes

3. is_bst()
- is the tree root at x, a BST with all keys strictly between min and max. 
- if min and max is None, treat as empty constraint 
```

# Miscellaneous Notes

### Computing the floor of key K
- The largest key in the tree less than or equal to key K
- Case1 - the floor is K  (k equals k at root)
- Case2 - the floor is in the leftsubtree (k is less than the key at root)
- Case3 - the floor is in the right subtree or otherwise the key is in the root. (k is greater than the key at root)

### To delete the minimum key
```python
    # recursively go left, until node has no left link, replace this node with its right link
    # if node has a left subtree, you should delete the minimum of that left subtree
    # this function updates the count of each node bubbling-up upon deletion of the minimum
    # and given node, what that node should be upon deletion of the minimum
    # IE, it should be itself, unless it's should be deleted (it's left link is None),
    # then the it's right link should replace it.
    def delete_minimum(current):

        if current.left is None: # current is the minimum, replace it by its right link
            return current.right
        # Recursively delete the minimum of current's left subtree if it exists
        current.left = Node.delete_minimum(current.left)
        # Update count of given subtree
        current.count = 1 + Node.size(current.left) + Node.size(current.right)

        # the current node should be itself, unless it's the minimum, then it should
        # be replaced by its right link.
        return current
```

### Get the keys between two keys 
```python 
    # add the keys between min_key and max_key in the subtree 
    # rooted at `current` to the queue
    # Strategy: 
    # Go left until the left subtree won't satisfy our condition
    # Go right then go left again until the left subtree won't 
    # satisfy our condition.
    # if current key > max_key and we've exhaused its left subtree
    # then all those on its right subtree are larger so we're done
    @staticmethod 
    def inorder_between(current, min_k, max_k, q):
        if current is None: return 
        if current.key > min_k:
            # Repeatedly go left until the node
            # is the minimum or less than mininum 
            # which means it's left subtree no longer has keys we're
            # interested in (everything there is less than our requirement)
            Node.inorder_between(current.left, min_k, max_k, q)
        # Enqueue key that satisfy condition 
        if current.key >= min_k and current.key <= max_k:
            q.put(current.key)
        if current.key < max_k:
            # There might be nodes on the right subtree  
            # That satisfies our condition (check left of right subtree)
            Node.inorder_between(current.right, min_k, max_k, q)
```

### Hibbard deletion

- To delete a node with key k, search for node t containing key k
- Case 0 - node to delete has no children : Delete t by setting parent's link to None
- Case 1 - node to delete has one child: Delete t by replacing parent link to t's child link
- Case 2 - node to delete has two children:
   - find successor node x of node t (x has no left child)
      - successor is x = Node.minimum(t.right)
      - successor node x is go right, then go left, left, left until you encounter the node with no left child
   - get delete the minimum x  in node t's right subtree (but don't garbage collect x)
   - put node x in node t's spot.
```python
    def delete(k, current):

        # If you've found nothing, then there's nothing to delete
        if current is None: return None

        if k < current: # search for key in left
            current.left = Node.delete(k, current.left)
        elif k > current: # search for key in right
            current.right = Node.delete(k, current.right)
        else: # you found the node!

            # Case 1: You have at most one children
            # Remember you are returning back to your parent recursively
            # Returning your only child (if you have one) back to your parent
            # Will make your parent point to your child
            # having nobody pointing at you at this point, you'll be garbage collected
            if current.right is None: return current.left
            if current.left is None: return current.right

            # Case2: You have two children
            node_k = current
            # Get your successor (the minimum value on your right subtree)
            # which will the one to replace you
            current = Node.minimum(node_k.right)
            # Delete your successor from that subtree
            # Make your children your successor's children
            current.right = Node.delete_minimum(node_k.right)
            # Above line removes minimum node from the tree, and returns root of that tree
            current.left = node_k.left
            # From this point you'll be returning your successor pointing at your children
            # back to your parent node, and your parent won't be linked back to you

        current.count = Node.update_size(current) # Update subtree count
        # Either you return yourself, or if you're the node to be deleted
        # your successor/replacement node.
        return current
```

### Another example test client
```
 *  A 8
 *  C 4
 *  E 12
 *  H 5
 *  L 11
 *  M 9
 *  P 10
 *  R 3
 *  S 0
 *  X 7
```

# Miscellaneous References
- https://algs4.cs.princeton.edu/32bst/
- https://algs4.cs.princeton.edu/32bst/BST.java.html
- https://algs4.cs.princeton.edu/32bst/NonrecursiveBST.java.html
