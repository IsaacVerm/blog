---
slug: "/codewars/two-sum"
date: "2022-03-18"
title: "Two Sum"
---

This post is about my first attempt at analyzing a [codewars](https://www.codewars.com/dashboard) puzzle I solved.
The problem is called [Two Sum](https://www.codewars.com/kata/52c31f8e6605bcc646000082/javascript) and goes like this:

```
Write a function that takes an array of numbers (integers for the tests) and a target number.
It should find two different items in the array that, when added together, give the target value.
The indices of these items should then be returned in a list.
```

I'm comparing my approach with the approach used by others in order to pick up some best practices from the community.
Writing a blog post about the puzzle is really helpful since I noticed it's way easier to retain new knowledge when writing it down while learning.
It feels like teaching to yourself.

## My solution

This is the code I came up with:

```javascript
function twoSum(numbers, target) {
    let sampleSum = null

    function sampleIndex() {
        return Math.floor(Math.random() * numbers.length)
    }

    while ( sampleSum != target) {
        index1 = sampleIndex()
        index2 = sampleIndex()
        sampleSum = numbers[index1] + numbers[index2]

        if (sampleSum == target) {
            const indices = [index1, index2].sort()
            return indices
        }
    }
}
```

I take a sampling approach since I didn't really know how to make a set of all combinations.
My solution is straightforward: 

1) calculate `sampleSum` which is the sum of 2 random numbers in the input array.
2) if this sum equals the target number I return the indices of these numbers as requested in the problem description.

## Other approaches

The most popular approach according to best practices is this:

```javascript
function twoSum(numbers, target) {
    for (var i = 0; i < numbers.length-1; i++) {
        for (var j = i+1; j < numbers.length; j++) {
            if (numbers[i] + numbers[j] === target) return [i, j];
        }
    }
}
```

Using 2 `for` loops it's very easy to create all the combinations.
I'm a bit ashamed I didn't go for this since it's so elementary.
Guess I tried to overcomplicate things here.

Other approaches make use of the fact we always sum just two numbers:

```javascript
function twoSum(numbers, target) {
  let seen = new Map();
  for (let i = 0; i < numbers.length; i++) {
    let x = numbers[i], y = target - x;
    if (seen.has(y))
      return [seen.get(y), i];
    seen.set(x, i);
  }
}
```

Because we sum just 2 numbers we can calculate the result of subtracting the target from a number in the input array.
This part is covered by the `y = target - x` part.
`x` refers to a number in the input array.

Say we want to find the 2 numbers in `[1,2,3]` which added together make 4.
It's easy for us to see 1 + 3 = 4 so the result the function will return is `[0,2]`.
These are the indices of the first and third number which make 4.

We can write this down in a more algorithmic way by calculating `y = target - x` for the 3 numbers in the input array:

- x = 1: y = 4 - 1 = 3
- x = 2: y = 4 - 2 = 2
- x = 3: y = 4 - 3 = 1 = x

From this example it follows the first element where the y value is the x value of an element we already iterated over is the second number to add with.

This example introduced me to the [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) object.
The MDN documentation contains a nice [table showing the differences between Map and Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map#objects_vs._maps).
The reason to opt for a `Map` over an `Object` here is a `Map` iterates in the order of entry insertion.
If not the `seen.has(y)` part would fail randomly.

## Conclusion

It pays off to think for 2 minutes before starting with a suboptimal approach like the sampling approach I opted for.
Clever mathematics is something good to know but I've got a lot of improvement to make in that department.