+++
title = "Day 7"
date = "2022-12-01"
+++

# Day 7
The input looks like this:
```bash
$ cd /
$ ls
dir a
14848514 b.txt
8504156 c.dat
dir d
$ cd a
$ ls
dir e
29116 f
2557 g
62596 h.lst
$ cd e
$ ls
584 i
$ cd ..
$ cd ..
$ cd d
$ ls
4060174 j
8033020 d.log
5626152 d.ext
7214296 k
```
**The problem** is parsing this file structure and doing a sum per directory of file sizes, then outputting... well, actually, the problem is, errr...
**implementing a tree filesystem**. Ugh.

This one was a bit more complicated. My first instinct was that I needed something like this:
```python
[{'/': ['a', 14848514, 8504156, 'd']}
{'a': ['e', 29116, 2557, 62596]}
{'e': [584]}
{'d': [4060174, 8033020, 5626152, 7214296]}]
```
Where I could apply the following process:
- Sum all directories where all elements are integers (here, e and d)
- Substitute the names of the directories elsewhere for the sum
- Repeat untill all directories are a single integer

Not terrible, but what I actually needed what this:
```python
[{'/': ['a', 14848514, 8504156, 'd'],
 'a': ['e', 29116, 2557, 62596]
 'e': [584],
 'd': [4060174, 8033020, 5626152, 7214296]}]
```
Mostly because working with lists withing dictionaries within lists was a road to madness. Working like this is way cleaner.
Also!! The input example doesn't match the actual input! That was a lot of fun. So rather, **since the same name can appear in different places**, you want:
```python
[{'/': ['a', 14848514, 8504156, 'd'],
 '/a': ['e', 29116, 2557, 62596]
 '/a/e': [584],
 '/d': [4060174, 8033020, 5626152, 7214296]}]
```
After quite some effort I ended up doing this for part one (comments directly on the code):

```python
import re
with open("input.txt") as file:
        input = file.readlines()
input = [line.strip('\n') for line in input]

tree = []
pwd = "/"
for line in input:
    if line.startswith("$ cd"):
        ls = []
        match = re.search("cd (.*)$", line)
        cd_dir = match.group(1)
        
        # store first ls
        if cd_dir == "/":
            # strip if dir
            dir = re.search(r"dir (.*)", line)
            file_size = re.search(r"\d+", line)
            if dir:
                ls.append(pwd+dir.group(1))
            if file_size:
                ls.append(int(file_size.group(0)))

            tree.append({"/" : ls})
        
        if cd_dir != "..":
            if cd_dir != "/":
                pwd = pwd+cd_dir+"/"
                tree.append({pwd : ls})
        if cd_dir == "..":
            pwd = pwd.split("/")
            pwd = [x for x in pwd if x != ""] 
            pwd = pwd[:-1]
            pwd = "/"+"/".join(pwd)+"/"
            pwd = pwd.replace("//","/")
            
    if "$" not in line:
        
        # get dir name if dir
        dir = re.search(r"dir (.*)", line)

        if dir:
            ls.append(pwd+dir.group(1)+"/")
        # get file sizes as int
        file_size = re.search(r"\d+", line)
        if file_size:
            ls.append(int(file_size.group(0)))


# transform list of dictionaries into a megadictionary 
# with all paths 
flat_tree = {}
for d in tree:
    flat_tree.update(d)
```
After getting the dictionary of dictionaries, it was a matter of replacing the values:

```python
def sum_values(tree):
    for key, values in tree.items():
    # control for done sums
        if isinstance(values, int):
            continue
    
    # if its a list of digits, sum
        if all(isinstance(x, int) for x in values):
            total = sum(values)
            tree.update({key:total})
    return tree

def replace_values(tree):
    tree = sum_values(tree)
    keys = list(tree.keys())
    
    for key, values in tree.items():
        if isinstance(values, list):
            to_check = [x for x in values if isinstance(x, str)]
            for directory in to_check:
                if isinstance(tree[directory], int):
                    values[values.index(directory)] = tree[directory]
                    tree[key] = values

    # recursive call:
    all_values = list(tree.values())
    for i in all_values:
        if not isinstance(i, int):
            replace_values(tree)
            tree = sum_values(tree)
            return tree
        else: 
            return tree

# Run the above code, and filter
result_size = replace_values(flat_tree)
final = {k: v for k, v in result_size.items() if v < 100000}
print("Solution part 1:", sum(final.values()))
```

**Part two** requires that you calculate left space in the filesystem, get how much space you need for some update and pick the smallest folder that fulfills that criteria:
```python
# part two
left_space = 70000000-result_size.get("/")
to_free = 30000000-left_space
final2 = {k: v for k, v in result_size.items() if v > to_free}
candidates = list(final2.values())
print("Solution part 2:min(candidates))
```
Way easier once you have implemented the filesystem! Pew!
And that's it! 
