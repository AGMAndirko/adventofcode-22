+++
title = "Day 6"
date = "2022-12-01"
+++

# Day 6
**The input** looks like:
```sh
mjqjpqmgbljsphdztnvjfqwrcgsmlb
```

**The problem** is identifying the index of the first group where four unique characters happen (actually, index+4, since `m`, `mj`, `mjq` in the example count towards the number).

My solution is simple: transform each group of four characters to sets (to get unique characters). Control for the length of that set, and move on if it's less than 4 unique characters.
```python
s,e = 0, 4
marker = set(input[0][s:e])

while len(marker) < 4:
    s+=1
    e+=1
    marker = set(input[0][s:e]) 
print(s+4)
```
You could make it more terse, probably.

**The second part** requires that the substring become 14 unique characters. Easy to modify - just change the 4s for 14's:
```python
s,e = 0, 14
marker = set(input[0][s:e])

while len(marker) < 14:
    s+=1
    e+=1
    marker = set(input[0][s:e])
print(s+14)
```
And that's it!
