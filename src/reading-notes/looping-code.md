---
slug: "/reading-notes/looping-code"
date: "2022-03-22"
title: "Reading notes MDN looping code"
---

Notes for [MDN looping code guide](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Looping_code).

## Why use loops?

Doing the same thing again and again.

## Is array the only type of collection you can loop over?

No you also have `Set`s, `Map`s, ...

## What's the most basic loop?
`for-of` loop

```
for (const element of array) {
	code
}
```

## What are some other loops?
`map`: do something to each element in the collection and save the results in a new array

`filter`: test each element in the collection and save the passing elements in a new array

`for`: explained later

## What's the syntax of a for loop?

```
for (initializer; condition; final-expression) {
	code
}
```

initializer: counter variable

condition: when to stop looping

final-expression: code executed at each iteration

Example:

```
for (let i = 1; i < 100; i++)
```

## What's the syntax of a while loop?

```
initializer

while (condition) {
	code
	
	final expression
}
```

## What's the syntax of a do-while loop?

```
initializer

do {
	code
	
	final expression
} while (condition)
```

## How can you exit the loop immediately?

Use `break`.

E.g. example loop over contacts and use `break` when found.

## How can you skip iteration?

Use `continue`.

## What are the 2 differences between a for and while loop?

1) placement initializer
- `for` loop: as argument
- `while` loop: before loop
2) placement final expression
- `for` loop: as argument
- `while` loop: after code has run

## Can you transform a for loop into a while loop?

Yes.

## What's the difference between a while and a do-while loop?

A `do-while` loop is executed at least once.

## What type of loop to use?

1) if you don't care about index: `for-of` loop
2) if you care about the index `for` and `while` loops are interchangeable

