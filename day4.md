+++
title = "Day 4"
date = "2022-12-01"
+++

# Day 4
**The input** looks like this:
```sh
2-4,6-8
2-3,4-5
5-7,7-9
2-8,3-7
6-6,4-6
2-6,4-8
```

**The problem is** finding those ranges that fully contain others.

I really didn't want to keep using `awk`, but in the end this was a problem too easy to solve with it. Took me about three minutes to solve part 1:
```sh
sed 's/-/,/g' example_input.txt      # changes - by , so that it can be treated as a column
    | awk  'BEGIN {FS=","}; 
    {if (($1<=$3 && $2>=$4) ||       
         ($1>=$3 && $2<=$4))
    print $1"-"$2,$3"-"$4}' | wc -l  
# if statement: controls if range 1 is covered by range 2 
# or viceversa
# then some unnecesary pretty printing, and counts output lines

```

**The second part** requires you get all ranges that overlap in any way. I found it quicker to calculate how many don't overlap at all, then rest that number to the total number of lines:
```sh
# same as above, but conditions control ranges don't overlap
non_overlapping=$(sed 's/-/,/g' input.txt | 
    awk  ' BEGIN {FS=","}; 
        {if (($1<$3) && ($2<$3) || 
            ($1>$4) && ($2>$4)) 
        print $1"-"$2,$3"-"$4}' | wc -l)
all=$(wc -l < input.txt)
echo $all-$non_overlapping | bc 
``` 
And that's it!
 
