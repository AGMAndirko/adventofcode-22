+++
title = "Day 2"
date = "2022-12-01"
+++

# Day 2
**The input**: `{A, B, C}` is the opponent's choice and `{X, Y, Z}` is yours in `rock`, `paper` and `scissors` games. It looks like:  
```sh
A X
B Y
C Z
```
**The problem** is calculating a total winning score, where you are given points for the choice (column 2, where `rock=X=1`, `paper=Y=2`, `scissors=Z=3`) and for winning (6 points) or getting a draw (3 points). 

At this point there are several approaches -  `rock, paper & scissors` is a common beginner programmer project. I chose to transform both columns to ordinals, then map the difference between columns to win/lose conditions. I used a bit too much `sed` for my taste, and a 3x3 choice matrix solution would have been quicker, but I really wanted to make this in `bash` again (shame on me!). I also learned a couple dirty tricks of `awk` derivatives.

First, the `ascii values`: Since all are capital letters, an easy way to get alphabet ordinals would be to convert the first column to ASCII values (values 65 onwards for uppercase letters):
```sh
echo $(($(printf '%d' \'A ) -64))
> 1
```
Turns out regular `awk` doesn't have a handy implementation for this... but `gwak` does as `ord()`! so you can do this:
```sh
gawk -l ordchr '{print $1" = "ord($1)-65, 
    $2 " = " ord($2)-88}' example_input.txt
> A = 0 X = 0
> B = 1 Y = 1
> C = 2 Z = 2
```
Now it's a matter of checking the difference, and adjusting the resulting numbers to the win-lose choice matrix.

**My final solution for part 1** of this day ended up like this:
``` sh
choice_points=$(gawk -l ordchr '
    {s+=ord($2)-87} END 
    {print s}'  input.txt)

victory_points=$(gawk -l ordchr '{
    col1=ord($1)-64; 
    col2=ord($2)-88;
    print (col1-col2)}' input.txt |  
    sed 's/0/6/g ; s/3/6/g ; s/-1/0/g ; s/1/3/g ; s/2/0/g' |  
    awk '{sum += $1} END {print sum}')

echo $choice_points+$victory_points | bc
```
The second variable transforms the letters to ordinals (1-3 for the A-C, 0-2 for the X-Z in this case), so that `wins(6 points)=[0|3]`, `draws(3 points)=[1]` and `loses(0 points)=[-1|2]` in the difference between both columns. For example, `(elf) ROCK VS PAPER (you)` would be here `1-1 = 0 = you win`.  Then, it's a matter of summing columns and scores. Note that the order of the substitutions matters.

**The second part** of the puzzle reveals that `{Y,X,Z}` actually stood for the desired victory condition! Effectively, the same problem, but departing from the result rather than the choices of you and your opponent.
Getting the victory points has few shenanigans:
```sh
victory_points=$(gawk -l ordchr '
    {col2=ord($2)-88; score=col2*3}
    {sum+=score} END {print sum}' input.txt)
```
However, guessing the combinations needed is where I'll pay my dirty `sed` pipping above. After some fiddling I ended up dividing between more predictable draws (where you just have to filter the input for draws then pick the numeric value of column 1):
```sh
draw_choice_points=$(gawk -l ordchr '
    {col1=ord($1)-64; col2=ord($2)-87}
    {if (col2==2) print $1, col1}' input.txt |
    sort | uniq -c | 
    awk '{col1=$1*$3; sum+=col1} END {print sum}')
```
And the more lousy win and lose conditions. At this point I gave up with the ASCII approach and I just initialized an array detailing the conditions and applied it to the thing:
```sh
rest_choice_points=$(gawk -l ordchr 'BEGIN {
    arr["A X"] = 3 
    arr["B Z"] = 3
    arr["C X"] = 2
    arr["A Z"] = 2
    arr["B X"] = 1
    arr["C Z"] = 1
}{print arr[$0]}' input.txt | sed '/^[[:blank:]]*$/ d' | sort | uniq -c | awk '{res=$1*$2; sum+=res} END {print sum}')
```

Then the final sum (I told you I like `bc`!):
```sh
echo $victory_points+$draw_choice_points+$rest_choice_points | bc
```
And that's it. By the second part it got a bit out of hand with `awk` (who would have thought that a language released 45 years ago feels awkward?) but it was fun! 

