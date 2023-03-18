+++
title = "Day 3"
date = "2022-12-01"
+++

# Day 3
**The input** looks like this:
```sh
vJrwpWtwJgWrhcsFMMfFFhFp
jqHRNqRjqzjGDLGLrsFMfFZSrLrFZsSL
PmmdzqPrVvPwwTWBwg
wMqvLMZHhHMvwLHjbvcjnnSBnvTQFn
ttgJtRGJQctTZtZT
CrZsJsPPZsGzwwsLwLmpwMDw
```

**The problem** is dividing the string evenly, then searching for common characters, then doing a sum of points (1-26 for lowercase letters, 27-52 for uppercase).

This one was easy, I took `awk` and... nah, kidding, I used `python`, like a sane person would. **Here's my solution for part 1**:
```python
# read input, clean
with open("input.txt") as file:
    input = file.readlines()
input = [line.strip('\n') for line in input]

# divide in two sacks (ns1, ns2), find common characters, append to a list
common_chars = []
for i in input:
    ns1= i[0:len(i)//2]
    ns2= i[len(i)//2:len(i)]
    common = list(set(ns1)&set(ns2))
    common_chars.append(common[0])

# some ASCII conversion and some adjusting for uppercase
priorities = [ord(l.swapcase())-64 for l in common_chars]
priorities = [d-6 if d>26 else d for d in priorities]
print(sum(priorities))
``` 

(**PS:** I was playing around with ChatGPT's open beta and fed it the `for` loop above to improve it, and after some gentle prompting it replied with the following working oneliner that doesn't use sets:)

```python
common_chars = [next((char for char in i[0:len(i)//2] if char in i[len(i)//2:len(i)]), "") for i in input]
```

Anyway: **part two of the puzzle** swaps the problem from row based to column-based (in groups of three). So: 
```py
# generates list of lists in chunks of 3
# then finds common characters between each of the 3 lists
grouped = [input[i:i+3] for i in range(0, len(input), 3)]
common_chars = []
for i in grouped:
    common_chars.append(list(set(i[0]) & set(i[1]) & set(i[2]))[0])

# same as before
priorities = [ord(l.swapcase())-64 for l in common_chars]
priorities = [d-6 if d>26 else d for d in priorities]
print(sum(priorities))
```
And that's it!
