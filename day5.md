+++
title = "Day 5"
date = "2022-12-01"
+++


# Day 5
**The input** looks like:
```sh
    [D]    
[N] [C]    
[Z] [M] [P]
 1   2   3 

move 1 from 2 to 1
move 3 from 1 to 3
move 2 from 2 to 1
move 1 from 1 to 2
```

**The problem is** moving the `[X]` crates in column-based stacks according to the instructions below. A problem of **stacks**, basically.

First, we divide the input in two big lines: crates and instructions. Luckily, we can just divide at the empty line:
```sh
with open("ex_input.txt") as file:
    input = file.readlines()
input = [line.strip('\n') for line in input]

# separates by empty line in two lists: crates, and move instructions 
crates_instructions = [input[:input.index('')], input[input.index('')+1:]] 
```

Getting the "columns" right for the crates input required a bit of tinkering, but ultimately it was simple. Basically I wanted this:
```python
[['[]' '[D]' '[]']
 ['[N]' '[C]' '[]']
 ['[Z]' '[M]' '[P]']]
```
so that I could get this kind of input, where I could just use python's `pop()` to follow the instructions:
```python 
[['[Z]', '[N]'], 
 ['[M]', '[C]', '[D]', 
 ['[P]']]
```

In order to do that I used the following code:
```python
# Create empty crates ("[]")
crates = [re.sub("    ", " [] ", row).strip() for row in crates]

# split by spaces, remove the last line with the numbers as I don't really need it
crates = [crate.split() for crate in crates][:-1]
# to array, for convenience:
crate_array = np.array(crates)

# transpose coordinates, reverse items in each row, end with a list of lists of different lengths
crate_array = crate_array.T
crate_array = np.flip(crate_array, axis= 1)
crates = crate_array.tolist()

# remove empty crates 
# (I only needed them to flip the array comfortably)
for list_crates  in crates:
    list_crates[:] = [item for item in list_crates if item != "[]"]
```

Then I just need to follow instructions, remove the number of items required and append it to the required stack:
```python
instructions = crates_instructions[1]
for instruction in instructions:
    # takes instruction lines and picks relevant numbers
    inst_list = instruction.split(" ")
    ncrates = int(inst_list[1])
    origin = int(inst_list[3])-1  # -1 because it'll be treated as an index
    destiny = int(inst_list[5])-1 # idem

    # for each required item to move
    for i in range(1,ncrates+1):
        # it pops it from the required stack 
        moving_crate = crates[origin].pop()
        # and appends it elsewhere:
        crates[destiny].append(moving_crate)

# the final output is just joining all the last items of each list in a string:
print(''.join([item[-1].strip('[]') for item in crates]))
``` 

Verbose, but not really hard once you figure out the input formatting.

**Part two of the puzzle** requires the same output, but keeping the order of the removed stack.
I modified the instructions block slightly to adjust to this, working with list slices rather than `pop()`:
```python
for instruction in instructions:
    #same as above: 
    inst_list = instruction.split(" ")
    ncrates = int(inst_list[1])
    origin = int(inst_list[3])-1
    destiny = int(inst_list[5])-1
    
    moving_crates = crates[origin][-ncrates:]
    crates[origin] = crates[origin][:-ncrates]
    for single_crate in moving_crates:
        crates[destiny].append(single_crate)
``` 
Aaand that's it!

