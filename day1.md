+++
title = "Day 1"
date = "2022-12-01"
+++

# Day 1
**The input** looks like:
```sh
100
200
300 

400

500
600
```
**The problem is** doing a sum of the numbers, grouped by the new line separations (so, here, 100+200+300, 400, 500+600). Then comes the easy part: you have to get the highest number for part one, then the sum of the top three for part two of the puzzle.

First I thought I could do it in `Python`, picking all items per row and appending them to a new list until it reaches an empty line, so that you get something easy to sum. The result to sum would be something like:
```sh
[100,200,300]
[400]
[500,600]
```
But then I thought, what the hell, doing it in a **Bash oneliner** is much more entertaining. I didn't want to write a full script for what felt like a menial task.
My first thought was to divide the output in columns - maybe something like:
```sh
100,400,500
200,,600
300,,
```
This would be as easy as a sum of all the elements of each column in `awk`, but it was a bit of a pain to produce with `paste` or such. Then I realized I just had to transform the new lines into pluses (`+`), replace any consecutive pluses (`++`) with new lines (effectively grouping the input) and feed to a calculator (I have a sweet spot for `bc`). Essentially, I wanted to produce this:
```sh
100+200+300
400
500+600
```
**My solution** is as simple as `tr`+`sed` for some basic character substitutions. From there it's a matter of `sort`ing and extracting the first item (`head`) for the requirements of the first half of the puzzle:
```sh
tr '\n' '+' < input.txt | sed 's/++/\n/g' | bc | sort -rn | head -1
```

To get the sum of the top 3, I just modified `head` and added an `awk` column sum:
```sh
tr '\n' '+' < input.txt | sed 's/++/\n/g' | bc | sort -rn | head -3 | awk '{sum += $1} END {print sum}
```
And that's it!


