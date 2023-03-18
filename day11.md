+++
title = "Day 11"
date = "2022-12-01"
+++

# Day 11
**The input** looks like this:
```python
Monkey 0:
  Starting items: 79, 98
  Operation: new = old * 19
  Test: divisible by 23
    If true: throw to monkey 2
    If false: throw to monkey 3

Monkey 1:
  Starting items: 54, 65, 75, 74
  Operation: new = old + 6
  Test: divisible by 19
    If true: throw to monkey 2
    If false: throw to monkey 0

Monkey 2:
  Starting items: 79, 60, 97
  Operation: new = old * old
  Test: divisible by 13
    If true: throw to monkey 1
    If false: throw to monkey 3

Monkey 3:
  Starting items: 74
  Operation: new = old + 3
  Test: divisible by 17
    If true: throw to monkey 0
    If false: throw to monkey 1
```

**The problem** is 1) parsing the notes, 2) creating a monkey class with that info and 3) making the inspect/throw functions.

**My solution** for this one felt way cleaner than the one I took for the similarly `class` problem Day 9:
```python
import re
import math

with open("ex_input.txt") as file:
    input = file.read()

input = input.split("\n\n")

class monkey():
    def __init__(self, id, items, operation, test, passed, failed):
        self.id = id
        self.items = items
        self.operation = operation 
        self.test = int(test)
        self.passed = int(passed)
        self.failed = int(failed)
        self.inspected = 0
    
    def inspect(self):
        for item in self.items:
            self.inspected += 1
            worry = eval(self.operation.replace("old", str(item)))//3
            if worry%self.test == 0:
                monkey_list[self.passed].items.append(worry)
            else:
                monkey_list[self.failed].items.append(worry)  
        self.items = [] 

    def monkey_business(self):
        self.inspect()
    
def parse_notes(note):
    """
    parses monkey notes
    """
    monkey_id = re.search("Monkey (\d*)", note).group(1)
    # parse items
    items = re.search("items: ([\d ,]*)", note).group(1)
    items = items.split(", ")

    operation = re.search("new = (.*)", note).group(1)
    test = re.search("by (\d*)", note).group(1)
    true_cond = re.search("true: throw to monkey (\d*)", note).group(1)
    false_cond = re.search("false: throw to monkey (\d*)", note).group(1)
    return monkey_id, items, operation, test,true_cond, false_cond

# parse notes, asign to a list of monkeys
notes = []
for n_monkeys in range(len(input)):
    notes.append(parse_notes(input[n_monkeys]))
monkey_list = [monkey(*notes[x]) for x in range(len(notes))]

# 20 rounds
for _ in range(20):
    for monkey in monkey_list:
        monkey.inspect()

# get inspection metric
inspected = []
for monkey in monkey_list:
    inspected.append(monkey.inspected)
sorted_inspected = sorted(inspected)
print("Solution 1:", math.prod(sorted_inspected[-2:]))
```

**The second part** involves no longer dividing by 3 the `worry` metric, and increasing the rounds to `10000`, resulting in slow computation times and big integer overflow.

I admit I had to check hints on this one because my maths game is very very weak. Anyway, in the end it turns out it's a matter of computing the least common multiple to keep the validation of tests ok without feeding the operation monstruous integers. Makes sense. The implementation is quite trivial: modify the monkey function `inspect()` to divide by the `LCM`:
```python
    def inspect(self, lcm):

        for item in self.items:
            self.inspected += 1
            worry = eval(self.operation.replace("old", str(item)))%lcm
            if worry%self.test == 0:
                monkey_list[self.passed].items.append(worry)
            else:
                monkey_list[self.failed].items.append(worry)
    self.items = []
```

and instead of the 20 rounds, compute the LCM of the tests of all monkeys, feed it into `inspect`, then increase rounds to `10000`:

```python
lcm = []
for monkey in monkey_list:
    lcm.append(monkey.test)
lcm = math.prod(lcm)

for _ in range(10000):
    for monkey in monkey_list:
        monkey.inspect(lcm)
```
The rest is the same.

I should have realized before, but well... First time I find a similar problem. A signal that I should go back to math principles. 

