+++
title = "Day 8"
date = "2022-12-01"
+++

# Day 8
**The input** looks like this:
```sh
30373
25512
65332
33549
35390
```

**The problem is** (succintely) seeing if numbers up, down, left and right of a given tree are smaller than the tree at a given position, and then marking that position and counting a total of marks.

**My solution** for part one was approaching this as boolean mask, where I just needed to transform the orientation of the input array to adjust to the orientation (from up, from down, from left and from right), then apply a single function that checks for each element if the elements that precede it in the row are smaller. 

The input array and function to check it from a certain orientation looked like this:

```python
import numpy as np
with open("input.txt") as file:
            input = file.readlines()
input = [line.strip('\n') for line in input]

digits_input = [list(x) for x in input]
input_array = np.array(digits_input, dtype=int)

def check_array(input_array):
    mask = []
    for row in input_array:
        row_mask = []
        for i in range(len(row)):
            if i == 0:
                row_mask.append(True)
            else:
                posterior = row[:i]
                if all(row[i] > x for x in posterior):
                    row_mask.append(True)
                else:
                    row_mask.append(False*(len(row)-i-1))
        mask.append(row_mask)
    
    mask = np.array(mask,dtype=bool)
    return mask
```

And the actual transformations for the array to be checked like this:

```python
# set true condition for all elements in edges
edge_mask =  np.zeros_like(input_array, dtype=bool)
edge_mask[0] = True
edge_mask[-1] = True
edge_mask[:,0] = True
edge_mask[:,-1] = True

# left to right
lr_mask = check_array(input_array)

# right to left
rl_array = np.flip(input_array, axis = 1)
rl_mask = check_array(rl_array) 
rl_mask =  np.flip(rl_mask, axis = 1)

# up to down
ud_array = input_array.T
ud_mask = check_array(ud_array)
ud_mask = ud_mask.T

# down to up
du_array = np.flip(input_array.T, axis = 1)
du_mask = check_array(du_array)
du_mask = np.flip(du_mask.T, axis = 0)

# merge arrays:
mask = np.logical_or(lr_mask, edge_mask)
mask = np.logical_or(mask, rl_mask)
mask = np.logical_or(mask, lr_mask)
mask = np.logical_or(mask, ud_mask)
mask = np.logical_or(mask, du_mask)

print("solution 1:", mask.sum())
```

Not too hard, actually - took me an embarrasing amount of time to come up with this solution, as I was making a function for each orientation and it took a bit of rethinking to make it less complicated.

**Part 2** requires that you calculate a **scenic score** by multiplying the number of numbers that are set to `True` in the mask: you have to get the highest score. After some fiddling, it ended up like this:

```python
def check_conseqs(tree, list_input):
    """
    checks how many of the trees preceding, following, up or down 
    are bigger than a given tree
    """
    smaller_than_input = []
    # control for edge
    if len(list_input) == 0:
        return 1
    for adj_tree in list_input:
        if tree > adj_tree:
            smaller_than_input.append(tree)
        else:
            return len(smaller_than_input)+1
    return len(smaller_than_input)

def check_score(arr):
    """
    iterates over all trees and calculates the score
    """
    grid_score = []
    for row in range(len(arr)):
        row_score = []
        for tree in range(len(arr[row])):
            following = arr[row][tree:][1:]
            score = check_conseqs(arr[row][tree], following)
            row_score.append(score)

        grid_score.append(row_score)
    scores = np.array(grid_score)
    return scores

#looking right        
lr_scenery = check_score(input_array)

# looking left
rl_scenery = np.flip(check_score(rl_array), axis = 1)

# looking down
ud_scenery = check_score(ud_array)
ud_scenery = ud_scenery.T

# looking up 
du_scenery = check_score(du_array)
du_scenery = np.flip(du_scenery.T, axis = 0)

# sets edges at 0...
edge_mask =  np.ones(input_array.shape, dtype = np.int8)
edge_mask[0] = 0
edge_mask[-1] = 0
edge_mask[:,0] = 0
edge_mask[:,-1] = 0

final_scenery_score = du_scenery*rl_scenery*ud_scenery*lr_scenery*edge_mask
print("solution 2:", np.max(final_scenery_score))
```
Very similar approach (one function for all calculations, just rotating the input, iterating over rows to create a new array with given scores).
I'm sure there was a more efficient way to do this, but well, I'm ok with the "naive" solution.





