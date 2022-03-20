---
slug: "/codewars/maze-runner"
date: "2022-03-20"
title: "Maze Runner"
---

## Problem description

[Original problem](https://www.codewars.com/kata/58663693b359c4a6560001d6/train/javascript).

```
You will be given a 2D array of the maze and an array of directions.
Your task is to follow the directions given.
If you reach the end point before all your moves have gone, you should return Finish.
If you hit any walls or go outside the maze border, you should return Dead.
If you find yourself still in the maze after using all the moves, you should return Lost.
```

## My solution

Steps:
- find starting point
- go over the directions one by one
    - move the point in the maze based on direction
    - check if point is still within the maze
    - check if wall is hit
    - check if finish is reached

## Other approaches

The most popular community example follows pretty much the same approach I outlined above.

It's a bit of nitpicking but I do find my approach to find the starting point more elegant:

```javascript
const y = maze.map(row => row.includes(2)).indexOf(true)
const x = maze[y].indexOf(2)
```

Beats the 2 `for` loops used by the other example:

```javascript
var path = Array(), curX, curY;
  for (var i = 0; i < maze.length; i++) {
    for (var j = 0; j < maze[i].length; j++) {
      if (maze[i][j] == 2) {
        curX = j;
        curY = i;
      }
    }
  }
```

Another difference is how the check for the point being outside the maze is made.
I've got a pretty lengthy chain of logical operators to make this check:

```javascript
if (point[0] < 0 || point[0] > nrRows - 1 || point[1] < 0 || point[1] > nrColumns - 1) {
            ...
        }
```

This is quite unnecessary since this line of code is also a possibility as shown in the community example.

```javascript
if (!maze[y]) return 'Dead';
```

The reason I didn't find this is because I should pay more attention to data types.
Knowing what data type your output will have can unlock unexpected tricks.

The rest of the differences between my solution and the community one are mainly cosmetic.
I prefer to use a `switch` statement over multiple `if` statements but I guess that's just a matter of preference.
I also prefer to be quite verbose with functions and don't mind having several named helper functions.
This isn't absolutely necessary but does help (at least it does for me) to better structure your code.

## Conclusion

I made the right decision having many helper functions since at some point in solving the puzzle I got stuck with the part of finding the starting point. Luckily because I extracted it as a function it was easy to refactor the function later on when I noticed the bug should be in that part of the code.

On a side note, the [troubleshooting FAQ](https://docs.codewars.com/training/troubleshooting/#test-attempt) for codewars is very useful.
It's especially handy to know the print statements (like `console.log`) you add in your code are printed before assertions in the codewars console.

