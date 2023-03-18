+++
title = "Day 10"
date = "2022-12-01"
+++

# Day 10
**The input** looks like this:
```shell
noop
addx 3
addx -5
```
**The problem** is computing cycles according to instructions (1 per line, +1 for each `addx`) and checking a value (`X`) at certain cycles.

**My solution to the first** part was a bit trivial, and made me fear the worst for part 2:
```python
import re
with open("input.txt") as file:
    input = file.readlines()

input = [line.strip('\n') for line in input]

X = 1
cycle = 1

cycles = [20, 60, 100, 140, 180, 220]
list_out = []
for line in input:
    if cycle in cycles:
        list_out.append(cycle*X) 

    if line != "noop":
        # match digit
        match = re.search(" (.*\d*)$", line)
        digit = int(match.group(0))
        cycle += 1
        if cycle in cycles:
            list_out.append(cycle*X)
        X += digit
    cycle += 1

print("Solution 1:", sum(list_out))
```

**Part 2**... well, it's a bit complicated to explain. Basically, it requires you print some characters depending on the value of `X` before.

Here's my solution:
```python
X = 1
cycle = 1
pixel = 0
crt = []
list_X = []

def draw_crt(cycle, X, crt):
    pixel_position = divmod((cycle-1), 40)
    sprite_position = [X-1, X, X+1]
    if pixel_position[1] in sprite_position:
        crt.append("#")
    else:
        crt.append(".")
    if pixel_position[1] == 39:
        crt.append("\n")
    return crt

for line in input:
    crt = draw_crt(cycle, X, crt)

    if line != "noop":
        # match digit
        match = re.search(" (.*\d*)$", line)
        digit = int(match.group(0))
        cycle += 1 
        crt = draw_crt(cycle, X, crt)
        X += digit
    cycle += 1

crt = "".join(crt)
print("Solution 2:")
print(crt)
```
And that's it! 10 out of 25! This one was easier than the latter ones - I think I spent more time trying to get the prompt of part two than actually writing it.
