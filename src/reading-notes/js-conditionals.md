---
slug: "/reading-notes/js-conditionals"
date: "2022-03-21"
title: "Reading notes MDN conditionals"
---

[Notes for MDN conditionals guide](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/conditionals).

## Why are conditionals needed?

You need to make decisions based on varying inputs and those decisions decide what actions to take.

## How to write an if-else statement?

```javascript
if (condition) {
	code to run if condition is true
} else {
	run some other code instead
}
```

## What's the difference between == and === ?

`===` is the strict version of `==`. It not only checks whether the values are the same but also if the values' datatypes are the same.

## Does every if need an else?

No.

## How can you add more choices to an if-else statement?

Add `else if`s.

## What are comparison operators for?

To test the condition in a conditional statement.

## Do most values return false or true?

The following return `false`:
- `false`
- `undefined`
- `null`
- 0
- `NaN
- `''``

Anything else returns true.

## What are the logical operators?

- AND (`&&`)
- OR (`||`)
- NOT (`!`)

## What are logical operators for?

To test multiple conditions without writing nested `if-else` statements.

## When to use if-else ?

- not too many choices
- each choice runs quite some code
- conditions are complex

## When to use switch?

When the condition is always the same for each choice.

## How to write a switch statement?

```javascript
switch (condition) {
	case choice1:
		code
		break;
	case choice2:
		code
		break;
	...
	default:
		code
}
```

## How to simplify a simple if-else?

Use ternary operator:

```
(condition) ? run this code : run this code instead
```

## Summary

3 types of conditionals:
- `if-else`
- `switch`
- ternary operator

You can get by with just `if-else`, `switch` and ternary operator are just handy shortcuts.

It's nice to play around with these conditionals in browser examples using the `select` element.