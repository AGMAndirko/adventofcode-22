+++
title = "Day 9"
date = "2022-12-01"
+++

# Day 9
**The input** looks like a set of instructions to move two elements (`head` and `tail`) around a board:
```sh
R 4
U 4
L 3
D 1
R 4
D 1
L 5
R 2
```

**The problem** is that two elements (head and tail) will be moving, one following instructions and the other tracking it closely. 

**My solution** was to create two classes (one for each element) and just have them move according to the other. I'm refreshed some of my knowledge of classes in Python and feel the final solution is fairly elegant, for once in the last days!

```python
import numpy as np
with open("input.txt") as file:
            input = file.readlines()
input = [line.strip('\n') for line in input]

class head:
    def __init__(self):
        self._v = 0
        self._h = 0

    def coords(self):
        return self._v, self._h

    def move(self, where):
        if where == "R":
            self._h +=1
        if where == "L":
            self._h -=1
        if where == "U":
            self._v +=1
        if where == "D":
            self._v -=1
        return head.coords()

class tail(head):
    def follow_head(self, head_coords, prev_position = None):
        diffv = head_coords[0]-self.coords()[0]
        diffh = head_coords[1]-self.coords()[1]
        
        # in case of diagonal moving needed
        if abs(diffh) == 2 and abs(diffv) == 1 or \
            abs(diffh) == 1 and abs(diffv) == 2:
            # match head previous position
            self._v = prev_position[0]
            self._h = prev_position[1]
            return self.coords()

        # regular moving:
        if diffh > 1:
            self.move("R")
        if diffh < -1:
            self.move("L")
        if diffv > 1:
            self.move("U")
        if diffv < -1:
            self.move("D")

        return self.coords()

head = head()
tail = tail()
record_steps = []

for inst in input:
    inst = inst.split(" ")
    for _ in range(int(inst[1])):
        prev_position = head.coords()
        head.move(inst[0])
        steps = tail.follow_head(head.coords(), prev_position)
        record_steps.append(steps)

# how many unique steps have been taken by tail?
print("Solution 1:",len(set(record_steps))
```

**Part 2** involves adding 10 objects to the grid (**sigh**). I'm glad I approached this as a class problem, but I didn't model diagonal movement properly. So much for elegance. What I did is retouch the follwing formula to include diagonal movement cases and track each previous knot's position (since the cheat of just moving to the last head's position wasn't enough now):

```python
import numpy as np
with open("input.txt") as file:
            input = file.readlines()
input = [line.strip('\n') for line in input]

class head:
    def __init__(self):
        self._v = 0
        self._h = 0

    def coords(self):
        return self._v, self._h

    def move(self, where, how=None):
        if where == "R":
            self._h +=1
        if where == "L":
            self._h -=1
        if where == "U":
            self._v +=1
        if where == "D":
            self._v -=1
        # diagonal:
        if where == "UR":
            self._h +=1
            self._v +=1
        if where == "DR":
            self._h +=1
            self._v -=1
        if where == "UL":
            self._h -=1
            self._v +=1
        if where == "DL":
            self._h -=1
            self._v -=1
        return self.coords()

class tail(head):
    def follow_head(self, headcoords, prev_position = None):
        diffv = headcoords[0]-self.coords()[0]
        diffh = headcoords[1]-self.coords()[1]
        #print(diffv, diffh)
        if diffh == 0 or diffv == 0:
                if diffh > 1:
                    self.move("R")
                if diffh < -1:
                    self.move("L")
                if diffv > 1:
                    self.move("U")
                if diffv < -1:
                    self.move("D")
        else:
            if (diffh == 2 and diffv == 1) or (diffh == 1 and diffv == 2): 
                self.move("UR")
            if (diffh == 2 and diffv == -1) or (diffh == 1 and diffv == -2):
                self.move("DR") 
            if (diffh == -2 and diffv == 1) or (diffh == -1 and diffv == 2): 
                self.move("UL")
            if (diffh == -2 and diffv == -1) or (diffh == -1 and diffv == -2): 
                self.move("DL")
            
            if diffh == 2 and diffv == 2:
                self.move("UR")
            if diffh == -2 and diffv == 2:
                self.move("UL")
            if diffh == 2 and diffv == -2:
                self.move("DR")
            if diffh == -2 and diffv == -2:
                self.move("DL")
        
        return self.coords()

part1_head = head()
part1_tail = tail()
record_steps = []

for inst in input:
    inst = inst.split(" ")
    for _ in range(int(inst[1])):
        prev_position = part1_head.coords()
        part1_head.move(inst[0])
        steps = part1_tail.follow_head(part1_head.coords(), prev_position)
        record_steps.append(steps)

print("Solution 1:",len(set(record_steps)))
print("")

part2_head = head()
tail1 = tail()
tail2 = tail()
tail3 = tail()
tail4 = tail()
tail5 = tail()
tail6 = tail()
tail7 = tail()
tail8 = tail()
tail9 = tail()

record_steps = []
for inst in input:
    inst = inst.split(" ")
    for _ in range(int(inst[1])):
        part2_head.move(inst[0])
        tail1.follow_head(part2_head.coords())
        tail2.follow_head(tail1.coords())
        tail3.follow_head(tail2.coords())
        tail4.follow_head(tail3.coords())
        tail5.follow_head(tail4.coords())
        tail6.follow_head(tail5.coords())
        tail7.follow_head(tail6.coords())
        tail8.follow_head(tail7.coords())
        steps = tail9.follow_head(tail8.coords())
        record_steps.append(steps)

print("Solution 2:",len(set(record_steps)))
```

Aaand that's it! It's getting harder! This one took me a bit more of thinking, and I wish I had the time to rewrite it from scratch knowing the second part - I probably would have approached this differently.

